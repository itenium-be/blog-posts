---
layout: post
author: Wouter Van Schandevijl
title: "Newtonsoft.Json vs System.Text.Json"
subTitle: "Just what we needed, choices."
date: 2025-04-16
desc: >
  Newtonsoft.Json was the .NET go-to JSON library for a
  very long time. Yes there were some versioning issues,
  but now, is it even more confusing?
bigimg:
  url: dotnet-json-big.png
  prompt: "Two futuristic glass vials labeled Newtonsoft.Json and System.Text.Json, each glowing with a different color (e.g., blue vs orange), on a high-tech lab table. Data particles or code symbols float inside."
  origin: Midjourney
img:
  url: dotnet-json.png
  origin: ChatGPT
  prompt: "A sleek, high-tech digital scale balancing two glowing cubes labeled 'Newtonsoft.Json' and 'System.Text.Json', surrounded by abstract data streams and JSON code fragments, futuristic interface design, vibrant lighting, cyberpunk color palette, minimal shadows, modern UI aesthetics"
categories: dotnet
tags: [tutorial]
extras:
  - githubproject: https://github.com/itenium-be/DotNet-Json
    githubtext: "The code as xUnit tests"
interesting:
  - url: https://learn.microsoft.com/en-us/dotnet/standard/serialization/system-text-json/migrate-from-newtonsoft
    desc: "Pretty in-depth migration article with a nice comparison table"
  - url: https://github.com/dotnet/runtime/tree/main/src/libraries/System.Text.Json/tests/System.Text.Json.Tests/Serialization
    desc: "The official STJ test suite"
toc:
  title: .NET Json
  icon: dot-circle-o
package-versions:
  - package: Newtonsoft.Json
    version: 13.0.3
  - package: System.Text.Json
    version: 9.0.4
socials:
  linkedin: "https://www.linkedin.com/posts/itenium_itenium-keeponlearning-dotnet-activity-7338143179109552129-gndY"
  facebook: "https://www.facebook.com/share/p/1Fw4WtFiNL/"
  twitter: "https://x.com/itenium_be/status/1932377417504424443"
  instagram: "https://www.instagram.com/p/DKt1V0OBejS/"
---

{% include github-stars.html url="JamesNK/Newtonsoft.Json" desc="" %}

System.Text.Json (STJ) is about twice as fast as Newtonsoft.Json while only consuming 50% as much memory.
A legacy project might still be using Newtonsoft but chances are serialization
isn't a bottleneck so it's probably not worth making the switch.

For new applications you'll want to stick with STJ however. Only add the extra nuget
if you really need one of the advanced Newtonsoft features (ex: Linq to Json, polymorphic
serialization, circular reference handling).

<!--more-->

System.Text.Json is included in the runtime for .NET Core 3.1 and later.
Otherwise use the [nuget package](https://www.nuget.org/packages/System.Text.Json).

# TL&DR

- **System.Text.Json**: The default, fast, memory efficient, but basic features
- **Newtonsoft.Json**: Easy, tolerant, lots of features, but much slower


# Basic Usage

(De)Serialization with optional settings/options:

```csharp
// System.Text.Json
var options = new JsonSerializerOptions();
string json = JsonSerializer.Serialize(obj[, options]);
MyClass? obj = JsonSerializer.Deserialize<MyClass>(json[, options]);

// Newtonsoft.Json
var settings = new JsonSerializerSettings();
string json = JsonConvert.SerializeObject(obj[, settings]);
MyClass? obj = JsonConvert.DeserializeObject<MyClass>(json[, settings]);
```

# Configuration

|              | System.Text.Json                       | Newtonsoft.Json                 | Remarks |
|--------------|----------------------------------------|---------------------------------|---------|
| Type         | JsonSerializerOptions                  | JsonSerializerSettings
|              | DefaultIgnoreCondition                 | _separate properties_           |
| Default      | JsonIgnoreCondition.WhenWritingDefault | DefaultValueHandling            | Skip default(T)
| Null         | JsonIgnoreCondition.WhenWritingNull    | NullValueHandling               | Skip null only
| Pretty Print | WriteIndented, IndentCharacter, IndentSize, NewLine | Formatting
| Numbers      | NumberHandling                         | FloatFormatHandling             | See Floats
| Dates        | _custom converters_                    | DateFormatString, DateFormatHandling, DateTimeZoneHandling, DateParseHandling
| Circular Refs| ReferenceHandler                       | ReferenceLoopHandling
| Unexisting properties | UnmappedMemberHandling        | MissingMemberHandling           |
| Constructor  | RespectRequiredConstructorParameters   | ConstructorHandling
{: .table-code}

## Floats

Serialize float/double NaN, Infinity and -Infinity as strings.

```csharp
// System.Text.Json
var opts = new JsonSerializerOptions()
{
  NumberHandling = JsonNumberHandling.AllowNamedFloatingPointLiterals,
  // JsonNumberHandling.Strict (default): Throw ArgumentException for NaN etc
};
var obj = new Floats() { Float = 3.1415f, Float2 = float.NaN };
string json = JsonSerializer.Serialize(obj, opts);
Assert.Equal("{\"Float\":3.1415,\"Float2\":\"NaN\"}", json);

// Serialize all values as strings
opts = new JsonSerializerOptions()
{
  NumberHandling = JsonNumberHandling.WriteAsString | JsonNumberHandling.AllowReadingFromString
};
obj = new Floats() { Float = 3.1415f, Float2 = float.PositiveInfinity};
json = JsonSerializer.Serialize(obj, opts);
Assert.Equal("{\"Float\":\"3.1415\",\"Float2\":\"Infinity\"}", json);


// Newtonsoft.Json
var sets = new JsonSerializerSettings()
{
  // "String" is the default behavior:
  FloatFormatHandling = FloatFormatHandling.String,
  // FloatFormatHandling.DefaultValue: Serialize NaN etc as 0.0
  // FloatFormatHandling.Symbol: Serialize NaN etc WITHOUT quotes (=not valid json)
};
var obj = new Floats() { Float = 3.1415f, Float2 = float.NegativeInfinity };
string json = JsonConvert.SerializeObject(obj, sets);
Assert.Equal("{\"Float\":3.1415,\"Float2\":\"-Infinity\"}", json);

// Newtonsoft can also serialize ALL floats as a string with a custom converter
var settings = new JsonSerializerSettings
{
  Converters = new List<JsonConverter> { new StringFloatConverter() },
};

public class StringFloatConverter : JsonConverter
{
  public override bool CanConvert(Type objectType) => objectType == typeof(float);

  public override object ReadJson(JsonReader reader, Type objType, object value, JsonSerializer serializer)
  {
    if (reader.TokenType == JsonToken.String)
      return float.Parse((string)reader.Value);

    return Convert.ToDouble(reader.Value);
  }

  public override void WriteJson(JsonWriter writer, object value, JsonSerializer serializer)
  {
    writer.WriteValue(((float)value).ToString(CultureInfo.InvariantCulture));
  }
}
```

## Default Options

Only for Newtonsoft:

```csharp
JsonConvert.DefaultSettings = () => new JsonSerializerSettings {};

// Read-only for STJ:
var defaultOpts = JsonSerializerOptions.Default;
```


# Performance

## Case Sensitivity

One of the reasons System.Text.Json is so much more performant is because
Newtonsoft json key names are completely case insensitive, while property
name cases have to match exactly in STJ.

```csharp
public record Person(string Name);

// System.Text.Json
string json = "{\"name\":\"Bert\"}";
var obj = JsonSerializer.Deserialize<Person>(json);
Assert.Null(obj.Name);

// Newtonsoft.Json
string json = "{\"name\":\"Bert\"}";
var obj = JsonConvert.DeserializeObject<Person>(json);
Assert.Equal("Bert", obj.Name);
```

## Postel's law

Be conservative in what you send, be liberal in what you accept.
{: .notice--info}

Newtonsoft.Json is **very** liberal in what it will deserialize
for you without complaint.

```csharp
string json = """
{
  // Newtonsoft parses this
  Name: 'Bert',
}
""";
```

While System.Text.Json throws a `JsonException` on:
- The comment
- The missing quotes around Name
- The use of single quotes around Bert
- The extra comma after 'Bert'

Some of these can still be parsed by STJ with some extra configuration:

```csharp
var opts = new JsonSerializerOptions()
{
  PropertyNameCaseInsensitive = true,
  ReadCommentHandling = JsonCommentHandling.Skip,
  AllowTrailingCommas = true,
};
```


## Span(Of T)

The main reason for the performance difference however is the use of `Span<T>`
in System.Text.Json. Which is not being adopted by Newtonsoft because of
.NET Framework support.

Performance is about 2x but this increases to about 3x for serialization when
using the STJ [source generation](https://devblogs.microsoft.com/dotnet/introducing-c-source-generators/)
feature to eliminate the use of Reflection.



# WebApi

System.Text.Json is the default from .NET Core 3.1 and up.

I've added the same "sensible" configuration for both:
- Use camelCase for property names -- which is typically used in JavaScript
- During development, pretty print the json
- Do not include `null` and primitive default values (ex: `false`, `0`) in the output so the bytes sent over the network are somewhat reduced
- Serialize enums as strings. While this increases the bytes sent, it does make them more human-readable.


```csharp
var builder = WebApplication.CreateBuilder(args);

// System.Text.Json
builder.Services.AddControllers().AddJsonOptions(options =>
{
  options.JsonSerializerOptions.PropertyNamingPolicy = JsonNamingPolicy.CamelCase;

  if (builder.Environment.IsDevelopment())
    options.JsonSerializerOptions.WriteIndented = true;

  options.JsonSerializerOptions.DefaultIgnoreCondition = JsonIgnoreCondition.WhenWritingDefault;
  options.JsonSerializerOptions.Converters.Add(new JsonStringEnumConverter());
});

// System.Text.Json for Minimal APIs (.NET Core 7)
builder.Services.ConfigureHttpJsonOptions(options => { ... });
// If not specified, defaults to:
var opts = new JsonSerializerOptions(JsonSerializerDefaults.Web);
Assert.True(opts.PropertyNameCaseInsensitive);
Assert.Equal(JsonNamingPolicy.CamelCase, opts.PropertyNamingPolicy);
Assert.Equal(JsonNumberHandling.AllowReadingFromString, opts.NumberHandling);


// Newtonsoft.Json
Install-Package Microsoft.AspNetCore.Mvc.NewtonsoftJson

using Newtonsoft.Json.Serialization;
builder.Services.AddControllers().AddNewtonsoftJson(options =>
{
  options.SerializerSettings.ContractResolver = new CamelCasePropertyNamesContractResolver();

  if (builder.Environment.IsDevelopment())
    options.SerializerSettings.Formatting = Formatting.Indented;

  options.SerializerSettings.NullValueHandling = NullValueHandling.Ignore;
  options.SerializerSettings.DefaultValueHandling = DefaultValueHandling.Ignore;
  options.SerializerSettings.Converters.Add(new Newtonsoft.Json.Converters.StringEnumConverter());
});
```

# Custom Converters

When the serialization options/settings can't handle your case, maybe because
you cannot control how the json communication happens or because some values
should be converted to a domain specific type, you can add your own converters.

Imagine that our frontend sends and/or accepts Money in a specific culture, like
`15.000,99` (nl-BE formatting).

## STJ CulturalMoney

For illustration purposes only; you'll probably want to handle all sorts of edge cases 😉

```csharp
new JsonSerializerOptions().Converters.Add(new CulturalMoneyConverter(new CultureInfo("nl-BE")));

public class CulturalMoneyConverter(CultureInfo culture) : JsonConverter<decimal>
{
  public override decimal Read(ref Utf8JsonReader reader, Type toConvert, JsonSerializerOptions opts)
  {
    return reader.TokenType == JsonTokenType.String
      ? decimal.Parse(reader.GetString(), NumberStyles.Any, culture)
      : reader.GetDecimal();
  }

  public override void Write(Utf8JsonWriter writer, decimal value, JsonSerializerOptions options)
  {
    writer.WriteStringValue(value.ToString("#,###.##", culture));
  }
}
```

## Newtonsoft CulturalMoney

```csharp
new JsonSerializerSettings().Converters.Add(new CulturalMoneyConverter(new CultureInfo("nl-BE")));

public class CulturalMoneyConverter(CultureInfo culture) : Newtonsoft.Json.JsonConverter
{
  public override bool CanConvert(Type objectType) => objectType == typeof(decimal);

  public override object ReadJson(JsonReader reader, Type objType, object? value, JsonSerializer js)
  {
    return reader.TokenType == JsonToken.String
      ? decimal.Parse((string)reader.Value, NumberStyles.Any, culture)
      : Convert.ToDecimal(reader.Value);
  }

  public override void WriteJson(JsonWriter writer, object? value, JsonSerializer serializer)
  {
    writer.WriteValue(((decimal)value).ToString("#,###.##", culture));
  }
}
```


## Contract Resolvers

If converters aren't doing it for you, because your frontend team is sending some
really weird stuff, it's still possible to further alter the behavior of serialization.

In STJ this is done by setting the `JsonSerializerOptions.TypeInfoResolver`, where you
could use `new DefaultJsonTypeInfoResolver()` and set the `Modifiers` property or provide
custom implementation(s) with `JsonTypeInfoResolver.Combine(...)`.

For Newtonsoft, set the `JsonSerializerSettings.ContractResolver` and inherit from
the `DefaultContractResolver`.


# Adoption

[Adoption for both libraries is going up](https://nugettrends.com/packages?ids=System.Text.Json&ids=Newtonsoft.Json&months=120), with no decline in sight for Newtonsoft.Json
but I'm guessing this is comparing apples and oranges because STJ is included by default
and is thus under-represented in the graph.

![Newtonsoft.Json vs System.Text.Json nuget downloads graph for the last 10 years](/assets/blog-images/dotnet-json-trend.png "Nuget downloads comparison"){: .img-responsive}


# More!!

## Polymorphism

Works pretty much out of the box for Newtonsoft.
For STJ it is possible to achieve starting from .NET Core 7
with the `JsonDerivedTypeAttribute` on the base class.

## Attributes

Both have attributes that can be applied to fields and/or
properties to change serialization behavior without reverting
to custom code.

### STJ Attributes

```csharp
public class Person
{
  [JsonPropertyName("person_name")]
  [JsonPropertyOrder(10)]
  public string Name { get; }

  [JsonIgnore(Condition = JsonIgnoreCondition.WhenWritingNull)]
  public int? Age { get; }

  [JsonIgnore]
  public string Password { get; set; }

  // We cannot pass a format to the Converter (like Newtonsoft allows)
  [JsonConverter(typeof(BirthDateFormatConverter))]
  public DateTime BirthDate { get; }

  [JsonConstructor]
  public PersonWithAttributes(string name, int? age = null)
  {
    Name = name; // "person_name" does NOT work
    Age = age;
  }
}
```

### Newtonsoft Attributes

```csharp
[JsonObject(MemberSerialization.OptOut)]
public class PersonWithAttributes
{
  [JsonProperty("person_name", Order = 10)]
  public string Name { get; }

  [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
  public int? Age { get; }

  [JsonIgnore]
  public string Password { get; set; }

  // Pass the format to the Converter
  [JsonConverter(typeof(DateFormatConverter), "yyyy-MM-dd")]
  public DateTime BirthDate { get; }

  [JsonConstructor]
  public PersonWithAttributes(string name, int? age)
  {
      Name = name; // This parameter could also be called person_name
      Age = age;
  }
}
```


## Callbacks

Execute code before/after (de)serialization:
- STJ: implement interfaces (IJsonOnSerializing, IJsonOnSerialized, IJsonOnDeserializing, IJsonOnDeserialized)
- Newtonsoft: attributes on methods (OnSerializing, OnSerialized, OnDeserializing, OnDeserialized)
