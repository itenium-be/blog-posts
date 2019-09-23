---
layout: post
author: Wouter Van Schandevijl
title: "CQRS and CRUD"
subTitle: "A Loving Relationship"
date: 2019-09-19
desc: >
  Detailing the mess you burden yourself
  with when trying to combine these two
  arch enemies. Is there a way out of this
  dire nightmare?
bigimg:
  url: CQRS-Ramble-Big.png
  desc: "Photo by NeONBRAND"
  origin: https://unsplash.com/photos/Gk578U8c2W8
img:
  url: CQRS-Ramble.png
  desc: "If all you have is a hammer..."
  origin: https://unsplash.com/photos/9cRDDvhpBRw
  title: "A Loving Relationship"
categories: design
tags: [war-story]
interesting:
  - url: https://steve-yegge.blogspot.com/2008/02/portrait-of-n00b.html
    desc: "Portrait of a n00b (by Steve Yegge)"
  - url: http://www.informit.com/store/enterprise-integration-patterns-designing-building-9780321200686
    desc: "Book: Enterprise Integration Patterns: Designing, Building, and Deploying Messaging Solutions"
  - url: https://chriskiehl.com/article/event-sourcing-is-hard
    desc: "Don't Let the Internet Dupe You, Event Sourcing is Hard"
  - url: https://www.infoq.com/news/2016/04/event-sourcing-anti-pattern
    desc: "A Whole System Based on Event Sourcing is an Anti-Pattern"
  - url: https://www.youtube.com/watch?v=LDW0QWie21s&feature=youtu.be&t=2391
    desc: "Video: Greg Young — A Decade of DDD, CQRS, Event Sourcing - Process Managers"
  - url: https://www.youtube.com/watch?v=LDW0QWie21s&feature=youtu.be&t=1926
    desc: "Video: Greg Young — A Decade of DDD, CQRS, Event Sourcing - Frameworks :("
toc:
  title: CQRS + CRUD = 💩
---

# A ramble against CQRS
{: .hide-from-excerpt}

A lot of interesting articles have been published about **Command Query Responsibility Segregation**.

This is a mere rant. Not necessarily against CQRS per se, but against **when**
it is being applied in spite of Martin Fowlers cautionary words:

> For some situations, this separation can be valuable, but beware
> that **for most systems CQRS adds risky complexity**.  
> — _Martin Fowler ([CQRS Article](https://martinfowler.com/bliki/CQRS.html))_


<!--more-->

I've always loved [CQS](https://en.wikipedia.org/wiki/Command%E2%80%93query_separation).
How could one not, it makes perfect sense that `getUser(5)`
would return user "5" and not do much else, perhaps there is some caching in place
but one would hope it doesn't actually create a User record or something.

Then Greg Young introduced CQRS: An architectural pattern that allows us
to add nice things, like Event Sourcing or a reporting database pretty easily.
Adding such significant changes might be much easier compared to the impact on a more classical
architecture, even when doing so late in the development life cycle.

> Despite these benefits, **you should be very cautious about using CQRS**  
> — _Martin Fowler_

In his entire article on CQRS, this is the only sentence that Martin has put in bold.

Unfortunately developers like the latest and greatest very much and CQRS has, for many organizations
and teams been put forward as the defacto standard architecture.

There is of course that one project where it makes a lot of sense but let's be realistic,
most of us are working on enterprise applications which are all pretty much CRUD.

So let's explore what usually happens when you use CQRS for a CRUD application...


# The CQRS Mayhem

An often overlooked aspect of design patterns are the `Consequences`:

> How does the pattern support its objectives? What are the trade-offs
> and results of using the pattern? What aspect of the system structure does it
> let you vary independently?  
> — _Design Patterns: Elements of Reusable Object-Oriented Software_


If there is a whole point to this blog post it's probably:  

The aspect of a CQRS system that benefits most from using the pattern
is adding new ways to display, update and create existing entities.
**This is the one thing that typically doesn't happen in a CRUD system.**
{: .notice}



When applying CQRS to something that is 80% CRUD to all 100% of the application,
you have not properly assessed the patterns' `Applicability` nor its `Intent`
and are left only with its `Consequences`:


![There will be consequences meme](/assets/blog-images/CQRS-Ramble-Consequences.jpg "There will be consequences"){: .img-responsive}


## An Explosion of Classes

A create and an update probably save to the same place in the same structure.

So you need to do a mapping from 2 models to the "database" structure. I'm sure you have a mapper
for that, so you now have 2 mappers. Oh and of course also 2 controllers, perhaps 2 services
and also 2 validators. And 2 repositories and 2 dtos and 2 handlers and uhm do you not need
a separate interface type for all these? We want to talk to an interface, not an implementation
after all!

Instead of a delete/getOne just passing along an `int id` we will now also introduce
good old `DeleteModelRequest` and `GetModelByIdRequest`: Because who doesn't like a hundred classes with one `int Id` field in them.
Even if you don't want them, you'll probably end up with a package that requires a specific type per command for its wiring.

So yeah, the amount of types in your application will grow. Dramatically.


## An Explosion of Duplication

Well since it's CRUD you are bound to be doing the exact same thing multiple times. Right.. not exactly the same thing of course,
there is that one field... in which entity was it again?

![Duplication!? Duplication everywhere!](/assets/blog-images/CQRS-Ramble-Duplication-Everywhere.jpg)


- The CreateHandler for entity 1 will look very much like the one for entity 2.
- The CreateRequest and UpdateRequest for an entity will also very much look alike.
- All DeleteRequests and GetByIdRequests will look exactly the same.
- The mapping from the CreateModel and the UpdateModel to the persistance model.



## Confusion on the frontend

An update screen is usually populated with existing values. You have different models for
the Read. So you need to map the Read to the Update model before you can populate the controls...
Or you need to also create a Get for the Update model. Either way, yes, you'll need to write more code.


(perhaps I can't pin the next one entirely on CQRS but there is no stopping now)

For simple entities we obviously need to only fetch what we really need on the frontend. So
we create a `ListModel` and an `FullModel`. When we need to show just a few fields, we also
create a `HeaderModel` and a `ModalModel`. Since you're not using GraphQL, each of these
also needs a backend endpoint and suddenly we also need to write some more code:
A `ListModelRequest`, `FullModelRequest` etc and their corresponding `ListModelRequestHandler` and `FullModelRequestHandler`.

But things get even better! We now have to keep all representations of the same entity
in sync. Alternatively we fetch when needed and discard from memory as soon as possible, for
example when the entity is no longer visible to the user. The amazing part is that now, we are
probably fetching more data compared to the single entity approach - which was probably the main
reason for having multiple representations in the first place.

Additionally it becomes harder to reuse components. Yeah, I would really like to use
the cool `ModalModelPopupComponent` but what I have is a `ListModel` and the `ModalModel` doesn't have
that one property. The horror!

_Disclaimer_: as always, "it depends": for entities with many children it might be too heavy
to work with just one representation. <small>(each heading should probably have a disclaimer)</small>




# The Search For Answers

The search for answers **to the wrong question**:  

> How can I keep this CRUD application easy to understand and modify while also applying CQRS?  
> — _Every developer working on a CQRS + CRUD app_


Because you want to keep [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself), one searches for solutions.

![Keeping Oneself DRY](/assets/blog-images/CQRS-Ramble-Keeping-DRY.jpeg)




## Domain Inheritance

The inevitable happens. In order to somewhat mitigate the maintainability issues
that arise from applying CQRS when it really shouldn't, inheritance is used so that each
time a property changes, is removed or added, it doesn't result in doing the same change
x times.


```c#
class CreateUserRequest {
    string Name;
    DateTime BirthDate;
    Address Address;
}

class UpdateUserRequest : CreateUserRequest {
    int Id;
}

class FullUserViewModel : UpdateUserRequest {
    DateTime CreatedOn;
    string CreatedBy;
    // perhaps this one is a stretch 😅
}
```

The most striking, but perhaps also a non-issue, is that this is a Liskov Substitution Principle violation.
Your `UpdateUserRequestHandler` is going to accept the `CreateUserRequest` but it will probably not do what you'd
hope from a create request. A non issue because the execution chain is probably auto-wired.

Then there is reduced readability: in order to see what an entity contains you need to drill down the inheritance
chain and puzzle it together mentally.

Okay, so we are back to copy/paste? Now it's reduced maintainability: all property changes need to be done x times.
And surprise 🎉, most maintenance includes exactly that: add this one field, rename that field, delete that field.
Even class and property documentation needs to be duplicated.

There just isn't a good solution to avoiding duplicating properties when your Read, Update and Create models are only
marginally different from each other.


## Generic Base Classes

The next source of repetition needs to go and so a well intended developer creates generic base classes to avoid
request handler code duplication.

We end up with the same amount of "Handlers" but now they do not contain any code
they only inherit a base, provide some options with generic parameters and a have constructor passing
a bunch of dependencies.

```typescript
class CreateModelHandlerBase<TReadModel, TCreateModel, TUpdateModel, TReturnModel> {
    constructor(
        IRepository<TReadModel, TCreateModel, TUpdateModel>,
        IUnitOfWork<TCreateModel>,
        ITranslator,
        IErrorHandler,
        ILogger<TCreateModel>,
        IAuditor,
        IReferenceDataProvider,
        IUserProvider,
        ILanguageProvider,
        IHopeIMNotMissingOne
    ) {}

    DoIt(model): TReturnModel {
        // lots of crazy code here
        // it probably handles all
        // the creates for all the
        // entities.This one might
        // inherit from the Update
    }
}
```

Yeah you don't really need the Read/Update models for a CreateHandlerBase but
unfortunately the IRepository needed it. The TReadModel is first because the
same order of generic parameters is used in the Read, Update and Delete handlers
due to some nasty copy paste errors in the past.

#### How to recognize such class

- Lot's of generic parameters
- Zero documentation
- Already contains `if (model is Model5Request)` branches.
- No unit tests
- Seems to work but only its creator knows how


Okay, perhaps I can't be too mad about this one, everyone probably ends up
with something similar for a CRUD persistence layer using either architecture.


# Why would more code even be bad

> The ratio of time spent reading versus writing is well over 10 to 1.  
> — _Robert C. Martin_

So if you have a code base that is x times the size of what it could be
you'll now spend much more time on what you're already doing most?


<br>

> You would naturally expect a project that's twice as large as another to have
> twice as many errors. But the density of defects — the number of defects per 1000
> lines of code — increases.  
> — _Steve McConnell (Code Complete, "How Program Size Affects Construction")_

More code === More bugs 😃


## Accidental Complexity vs Essential Complexity

[Essential Complexity](https://en.wikipedia.org/wiki/No_Silver_Bullet) is driven by
business needs and cannot be avoided.  
Accidental Complexity is what we are creating here: More types to create and
maintain. Arguably, a more difficult to follow flow of execution. More code to
read/scan through during bug hunting.

Not everyone in the team will have worked with CQRS. There is a learning curve. During an initial adjustment period
less value will be delivered.

Adding Accidental Complexity may be a good thing, but what are you buying yourself here?
What did applying CQRS bring to table that starts really paying off months/years later?



# Freedom or Enslavement

For those of you who have not yet noticed, what bothers me most about CQRS is that for everything
you do, it always means more code, usually in the form of new classes.

And isn't that exactly what the Open-Closed Principle advocates? Well yes... true... I rest my case
all hail CQRS.  
If the most common types of feature requests can be handled in an Open-Closed way, then 👍 for the team!
Unfortunately for CQRS this is only the case when creating new Entities or new ways to Create/Update existing entities.
Even long before you've gotten to 🎺 🎉 v1 🎉 🎺, no more new entities will be added and since it's still very much a CRUD
application you most often end up with "Please add field x to entity y" tickets which is something
CQRS isn't very good at.

So what is one to do?

## KISS

If the only difference between create and update is the availability of the `id` property,
instead of literally writing/copying hundreds of lines of code, you can solve the issue
with no more than about 2 lines of code:

```typescript
if (isCreate && entity.id)
    throw Error;
```

If there is more to it:  
- Add the second if!
- How about `if (isUpdate) doThatOtherStuff();`?
- Add metadata to properties signaling "allow edit on create only" and other things happening in your domain.
- Introduce a seam
- Be creative! Injectors, Interceptors, Filters!



## A CQRS Rule of Thumb

An easy to follow checklist to determine if you need CQRS for this upcoming "MetaDataAdmin" project:

If, for a single entity, your UX designer

OR: created 1 wireframe for Create, Read and Update  
OR: made one wireframe for each but they look pretty much alike  
**THEN**: Choose CQRS at your own peril.  

<br>

If he made 7 radically different wireframes: 2 Creates, 3 Updates and 2 Reads - for the same entity.  
**THEN**: I suggest you consider CQRS, it might be a good fit.  


## Mitigation

Create code snippets, perhaps even a little generator to mitigate the pain. (you mean, write even more code? 😀)

Good documentation would help a lot but well, you know how it goes 😃

A simple diagram explaining the flow for a typical CRUD operation:
where in the project folder structure to create which class goes a long way
getting new devs and juniors onboard.

If you end up using the the domain inheritance thing, (generated?) printouts
for the important entity types so you can piece things together more easily are a good
workaround for properties being "all over the place".

UnitTest the heck out of those pretty "BaseHandlers" and you'll be fine!




# Conclusions

<!-- !Safely in comment! -->
<!--   CQRS === The 😈   -->

[THINK!](https://itenium.be/blog/productivity/pragmatic-tip-2-think-about-your-work/) Does it make sense to use CQRS or not!?

THINK! If so, will it be helpful to do for all parts of the application and for all entities?


## The Cure Is Worse Than The Disease

If you are using CQRS for a CRUDdy app, then you are basically avoiding issues like:

- For a create, you cannot set the `id`
- For create and update, you cannot set audit fields (Create & Update meta info)
- Sometimes, for some entities there are some fields that can only be set during Create or Update


And replacing them with issues like:

- (Junior) Developers that have to learn this whole CQRS thing
- An explosion of classes and duplication (mappers, entities, dtos, requests, ...)
- More expensive maintenance for CRUD-related feature requests
- You are making your application Open-Closed in all the wrong ways
- Developers that do not like duplication search for solutions
    - Domain classes that inherit from each other
    - Empty classes that just inherit from another class
