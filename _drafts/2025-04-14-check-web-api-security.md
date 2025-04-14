---
layout: post
author: Wouter Van Schandevijl
title: "Checking Security on your Controllers"
subTitle: "Make sure your controller action methods are properly secured"
date: 2025-04-14
desc: >
  Ensure that each Controller or all its Action Methods are properly
  secured with AllowAnonymous or Authorize Attributes with an
  automated integration test.
bigimg:
  url: -big.png
  prompt: ""
  origin: Midjourney
img:
  url: .png
  origin: Midjourney
  prompt: ""
categories: dotnet
tags: [testing,war-story]
extras:
  - githubproject: https://github.com/itenium-be/DotNet-Controller-Security-Check
    githubtext: "Source code, variations and tests for test"
toc:
  title: Controller Security
  icon: dot-circle-o
socials:
  linkedin: ""
  instagram: ""
  twitter: ""
---

Adding the `[AllowAnonymous]` or `[Authorize("policy")]` whenever a new Controller
Action Method is added, it's something that is easily forgotten.

... As I noticed when I was looking at some of our controllers 😵

Of course I have written this test many times before. These days it can be quickly
re-created with AI but to avoid having to debug its code, here is the copy pasta
version, with some possible variations.

<!--more-->

# TL&DR

```c#
Type[] controllerTypes = typeof(YourController).Assembly.GetTypes()
  .Where(t => t.GetCustomAttributes(typeof(ApiControllerAttribute), true).Any())
  .ToArray();

var missingAuthActions = new List<string>();
foreach (Type controllerType in controllerTypes)
{
  // If the entire Controller has Authorize/AllowsAnonymous attribute, just move on
  bool controllerHasAuthorize = controllerType.GetCustomAttributes(typeof(AuthorizeAttribute), true).Any();
  if (controllerHasAuthorize)
    continue;

  bool controllerHasAllowAnonymous = controllerType.GetCustomAttributes(typeof(AllowAnonymousAttribute), true).Any();
  if (controllerHasAllowAnonymous)
    continue;

  // NonAction methods do not represent an endpoint, and thus need no security check
  var methods = controllerType
    .GetMethods(BindingFlags.Instance | BindingFlags.Public | BindingFlags.DeclaredOnly)
    .Where(m => !m.GetCustomAttributes(typeof(NonActionAttribute), true).Any());

  foreach (MethodInfo method in methods)
  {
    bool hasAuthorize = method.GetCustomAttributes(typeof(AuthorizeAttribute), true).Any();
    bool hasAllowAnonymous = method.GetCustomAttributes(typeof(AllowAnonymousAttribute), true).Any();
    if (!hasAllowAnonymous && !hasAuthorize)
    {
      missingAuthActions.Add($"{controllerType.Name}.{method.Name}");
    }
  }
}

if (missingAuthActions.Any())
{
  var sortedMethods = missingAuthActions.OrderBy(x => x);
  Assert.Fail($"The following actions are missing [Authorize] attribute:\n{string.Join("\n", sortedMethods)}");
}
```

# Variations

Maybe you want to remove the code allowing `[AllowAnonymous]` or something...


## Finding the Controllers

The code above checks for the presence of `[ApiController]` but your mileage may vary:

```c#
// Controllers have [ApiController] attribute
.Where(t => t.GetCustomAttributes(typeof(ApiControllerAttribute), true).Any())

// Controllers inherit from ControllerBase
.Where(t => t.IsSubclassOf(typeof(ControllerBase)))

// Check for "Controller" in the name (or use EndsWith)
.Where(t => t.Name.Contains("Controller"))
```

## NotImplementedException

In a blatant violation of the [Interface Segregation Principle (ISP)](https://en.wikipedia.org/wiki/Interface_segregation_principle)
some of our controllers implemented an interface but only implemented a few of the methods, with all others just doing a:

```c#
throw new NotImplementedException();
```

And of course this broke the test 🙈

More AI to the rescue! Thank god for AI too, because I'm pretty sure
I would've spent a lot of time on this otherwise 🔥

This doesn't catch all variations (ex: `var ex = new NotImplementedException(); throw ex;`)
but was good enough for our code-base.

```c#
public static bool MethodThrowsNotImplementedException(MethodInfo method)
{
  byte[]? methodBody = method.GetMethodBody()?.GetILAsByteArray();
  if (methodBody == null)
    return false;

  for (int i = 0; i < methodBody.Length - 5; i++)
  {
    // Look for NEWOBJ (0x73) followed later by THROW (0x7A)
    if (methodBody[i] == 0x73 && methodBody[i + 5] == 0x7A)
    {
      var ctorToken = BitConverter.ToInt32(methodBody, i + 1);
      var ctor = method.Module.ResolveMethod(ctorToken) as ConstructorInfo;
      if (ctor?.DeclaringType == typeof(NotImplementedException))
        return true;
    }
  }
  return false;
}
```

# Minimal APIs

We're not using minimal APIs but for the sake of completeness, here is the code
to check the same thing:

## Program.cs

Here we need to check if either `RequireAuthorization` or `AllowAnonymous` fluent
methods were called for each endpoint.

Also note the `public partial class Program` addition so that we can reference Program
from the test. This is probably not necessary if you have nicely split your startup code
instead of just dumping everything in Program.cs 😉

```c#
app.MapGet("/unsecured", () => "Stormy");
app.MapGet("/secured", () => "Sunny").RequireAuthorization();
app.MapGet("/anonymous", () => "Cloudy").AllowAnonymous();

app.Run();

// Enable access to Program from the test
#if DEBUG
public partial class Program { }
#endif
```

## The Test

```c#
var factory = new WebApplicationFactory<Program>().WithWebHostBuilder(builder =>
{
  builder.ConfigureTestServices(services => { /* Mock services as needed */ });
});

var endpointDataSource = factory.Services.GetRequiredService<EndpointDataSource>();
var unsecured = new List<string>();
foreach (var endpoint in endpointDataSource.Endpoints)
{
  bool isAuthorized = endpoint.Metadata.GetMetadata<IAuthorizeData>() != null;
  bool isAnonymousAllowed = endpoint.Metadata.GetMetadata<IAllowAnonymous>() != null;
  if (!isAuthorized && !isAnonymousAllowed)
  {
    unsecured.Add(endpoint.DisplayName ?? "??");
  }
}

if (unsecured.Any())
{
  Assert.Fail($"Add .RequireAuthorization() or .AllowAnonymous() to the following endpoints: \n{string.Join(", ", unsecured)}");
}
```
