# API Versioning: The good, the bad, the ugly

## Scope

Specific

## Target audience

Web developers, CTOs, Architects, API enthusiasts.

## Abstract for the audience

*The audience only has the titles of the talks and abstracts to decide which track to go to. Therefore, in the thesis, it is necessary to outline the range of problems that you solve in the talk as specifically as possible. If your approach allows you to improve the performance of some software, then mention the size of the improvements, and mention for which particular class of tasks and technology stack this approach is applicable. All the questions stated in the abstracts must receive an answer in the talk. The discrepancy between the abstracts and the content of the talk upsets the audience very much.*

Web API Versioning is a way to allow your developers to move quickly and break things while your clients enjoy the stable API in long cycles. It is best practice for any API-first company to have API Versioning in one way or another. Otherwise, the company will either be unable to improve their API or their clients will have their integrations broken every few months.

The problem is that there is no information on how to implement it. If you try searching for methods of API Versioning, you will see hundreds of articles on whether to put the version into the URL or a header, a few pieces of ASP.NET documentation that discuss their platform-specific versioning, and a single article by Stripe that delves deep into the subject matter. Sadly, even they only describe one approach.

I'll cover all sorts of approaches you can pick to add incompatible features to your API: extremely stable and expensive, easy-looking but horrible in practice, and even completely version-less yet viable. I will provide you with the best practices of how you could find or implement a modern API versioning solution and will discuss the versioning at Stripe and Monite in great detail.

When you leave, you'll have enough information to make your API Versioning user-friendly without overburdening your developers.

## Tags

API Versioning, API, API-First, Stripe, GraphQL, Open-Source, Open Source, Web Development, Backend

## Abstract for the committee

*The audience does not see this field, so you need to write the whole truth here, without fear of spoilers. Here we want to see not only the questions that you cover in the talk but also a short version of the answers to them. What will be the benefit for the audience? If you write "I want to warn viewers about three problems in Lua JIT", this is not enough. Please mention these issues so that we can compare the content of the talks in terms of quality. The Program Committee needs to evaluate not only the relevance of the issues addressed by the talk but also the quality of the answers given by the speaker, so we will still ask you to provide these answers before including the talk in the conference program.*

Please note that I can present this talk in either Russian or English -- whichever one is preferable.

API versioning is hard, very hard; and lack of resources does not help at all. I want this talk to become a beacon to anyone lost in it, to be the first resource that makes a real overview of all major problems and solutions to them. Note that there does not exist the right way to version but there certainly exists the wrong way to version.

At Monite, we provide invoicing and payments APIs to B2Bs all over the world. However, as a startup we are still rapidly changing. As a result, we need to make breaking changes in our API all the time. To make sure that our clients do not have to re-integrate with us every few weeks, we started doing API versioning early. Sadly, we have tried one of the less effective approaches. It proved so painful and expensive that I decided to research everything about API versioning in existence. There are many approaches at different levels of isolation but the most effective ones version strictly the API without touching the database and only minimally touching business logic. One of such effective approaches is applied at Stripe so I contacted the author of their API versioning and developed an open-source framework based on our discussion.

After making an overview of all existing approaches, I will be talking about the methodology and benefits of our approach. Essentially if you already have a framework or can develop one, having many versions becomes easy and backporting features to all prior versions becomes cheap. The same approach allowed Stripe to maintain backward compatibility with every version since Stripe's inception in 2011.

The core of the methodology is to encapsulate the changes between versions into small "compatibility gates" that are independent of your business logic. They mainly describe three things: what has changed, how to convert a request from the prior version to the request of the next version, and how to convert a response from the next version to the response of the prior version. These compatibility gates thus form a chain of migrations: they get automatically applied to every request one after another. This chain effectively turns any request into the request of the latest version and turns the responses from the latest version into responses for any version requested by the client. Thus, you have business logic that handles a single version yet your API can support years worth of versions with minimal maintenance overhead.

This talk will introduce the audience to all necessary concepts and will provide links to all useful resources for anyone who wants to thoroughly research API versioning or build a modern solution themselves.

* (1.5 min) What will you see if you try to research API versioning right now?
  * hundreds of articles on where to put the version
  * Simplistic approaches that are not viable for many versions or for long-term support
  * Platform-specific solutions
  * A single Stripe article describing an advanced versioning technique
* (2 min) A quick description of best practices from "hundreds of articles" (use dates or numbers, put them into a header, leave /v1/ there for the future) because this information is so easily searchable that we should not spend much time on it
* (5 min) A set of issues that don't seem to be covered anywhere.
  * How do you maintain many versions at the same time?
  * What happens if your versions are incompatible in terms of data?
  * How do you deprecate versions if you have many clients?
  * How do you keep your developers sane when versioning?
  * (Note for the committee: The answers to all of these questions depend on the use case and are related to picking the right layer to version, taking the right approach from that layer, and then implementing it correctly. The concrete recommendations will be included in the classification sections.)
* (5 min) Layered classification of API versioning
  * Average API Application consists of three layers: Model, Business Logic, API (similar to MVC but orthogonal to it)
  * When you version, you choose the layer you want to duplicate between versions. If you duplicate a layer, you must duplicate all layers above it
  * We will use this model to understand and classify API versioning approaches
  * The larger piece of logic we duplicate -- the more expensive the approach is to maintain. However, the smaller piece we duplicate -- the more sophisticated our API versioning framework will need to be.
* (10 min) A per-layer classification of the majority of existing API versioning approaches
  * Model
    * Separate deployments with separate databases (expensive but rock-solid stable)
    * Separate deployments with the same database  (expensive and stable but need to be cautious with database changes)
  * Business logic
    * Full business logic and API duplication (feels cheap but instantly turns any project into a mess if there is no advanced infrastructure to support it)
    * Per-route versioning (cheap and simple but quickly turns the project into a mess on many versions)
  * API
    * Per-version serializers (cheap at first but hard to support with many versions)
    * Stripe's Compatibility Gates (expensive to start but cheap to support years worth of versions)
    * GraphQL (expensive for both your developers and your partners' developers but allows for a version-less approach)
    * Yaml API-builder approach used at SuperJob (extremely expensive to start and learn but can be comparable to Stripe's approach in terms of maintainability)
* (10 min) Description of our API versioning approach
  * Latest version and why we want to stay on it
  * Compatibility gates and how they work
  * How compatibility gates can create an easily maintainable chain that makes versioning easy
* Final recommendation: "Avoid versioning at all costs for as long as you can. It is expensive, painful, and introduces so many issues you would never encounter otherwise. But if you can't and need to support a large number of versions and clients: The approach that Stripe and Monite chose is probably your best bet to keep your sanity."

Links for reference:

* <https://github.com/zmievsa/cadwyn>
* <https://stripe.com/blog/api-versioning>
* <https://habr.com/ru/companies/superjob/articles/577650/>
* <https://engineering.linkedin.com/blog/2022/-under-the-hood--how-we-built-api-versioning-for-linkedin-market>
* <https://smartlogic.io/blog/2012-12-12-developing-an-api/>
* <https://thenewstack.io/tricks-api-versioning/>
* <https://github.com/dotnet/aspnet-api-versioning>
* <https://github.com/sjkaliski/pinned>
* <https://github.com/phillbaker/gates>
* <https://github.com/lukepolo/laravel-api-migrations>
* <https://github.com/tomschlick/request-migrations>
* <https://github.com/keygen-sh/request_migrations>

## Video for the program committee

TO BE PROVIDED LATER
