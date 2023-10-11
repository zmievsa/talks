# API Versioning: The good, the bad, the ugly

## Scope

Specific

## Target audience

Web developers, CTOs, Architects, API enthusiasts.

Please note that I can present this talk in either Russian or English -- whichever one is preferable.

## Abstract for the audience

*The audience only has the titles of the talks and abstracts to decide which track to go to. Therefore, in the theses, it is necessary to outline the range of problems that you solve in the talk as specifically as possible. If your approach allows you to improve the performance of some software, then mention the size of the improvements, mention for which particular class of tasks and technology stack this approach is applicable. It is important that all the questions stated in the abstracts actually receive an answer in the talk. The discrepancy between the abstracts and the content of the talk upsets the audience very much.*

Web API Versioning is a way to allow your developers to move quickly and break things while your clients will enjoy the stable API in long cycles. It is best practice for any API-first company to have API Versioning in one way or another. Otherwise the company will either be unable to improve their API or their clients will have their integrations broken every few months.
vers
The problem is: there is no information on how to implement it. If you try searching for methods of API Versioning, you will see thousands of articles on whether to put the version into the URL or into a header, a few pieces of ASP.NET documentation that discuss their platform-specific versioning, and a single article by Stripe that actually delves deep into the subject matter. Sadly, even they do not provide all details.

I'll cover all sorts of approaches you can pick to add incompatible features into your API: extremely stable and expensive, easy-looking but horrible in practice, and even completely versionless yet viable. I will provide you with the best practices of how you could implement a modern API versioning solution and will discuss the one we chose at Monite in great detail.

When you leave, you'll have enough information to make your API Versioning user-friendly without overburdening your developers.

## Tags

API Versioning, API, API-First, Stripe, GraphQL, Open-Source, Open Source, Web Development, Backend

## Abstract for the commitee

*The audience does not see this field, so you need to write the whole truth here, without fear of spoilers. Here we want to see not only the questions that you cover in the talk, but also a short version of the answers to them. What will be the benefit for the audience? If you write "I want to warn viewers about three problems in Lua JIT", this is not enough. Please mention these issues so that we can compare the content of the talks in terms of quality. It is important for the Program Committee to evaluate not only the relevance of the issues addressed by the talk, but also the quality of the answers given by the speaker, so we will still ask you to provide these answers before including the talk in the conference program.*

If you google API versioning -- you will be met with simple platform-specific solutions such as ASP.NET controller versioning, countless articles discussing where to put the version -- url, header, accept header, media type header, or a query param -- and then you will see a single Stripe article with a unique approach. Sadly, there is not much detail beyond that. This talk is an attempt to create a review of all API versioning approaches and propose a few recommended options.

First, let's get rid of the most cliché information: if you wish to follow best practices, you will probably use dates or simple numbers as versions and version using a header, and it doesn't really matter which one; but leave a /v1/ in your URL for a rainy day. What I described right now was the conclusion of the 99% of searcheable information on API Versioning.

Then I would like to describe versioning in differing levels of isolation:

1. an extremely expensive and stable versioning by separate deployments or separate kubernetes namespaces deployed from separate branches which you would choose if you have a small number of concurrent versions and need the old ones to be rock-solid stable
2. an easy "add a v2 endpoint" approach which works for very simple version changes but can turn any product into a hot mess if there are multiple versions or if the changes between versions are complex
3. an easy-looking "copy routes and business logic into a separate directory and route to it" approach that turns any developer's life into hell within only 2-4 versions and which developers [at SuperJob](<https://habr.com/ru/companies/superjob/articles/577650/>) have called "Versioning by suffering". We at Monite have also picked this approach first and sufferred enormously
4. Per-version response/request converters which allow you to completely separate the versioning interface layer but are hard to scale to many versions
5. Configuration-based automatic request/response combination layer like they do at SuperJob. It is so complex and company-specific that developing the framework for it is probably an overkill for most companies but it's a valid approach for having a lot of versions nonetheless
6. Stripe's and Linkedin's migration gate-based approach which allows you to support hundreds of versions at the same time while having minimal duplication in business logic yet requires a nuanced framework to handle all migrations
7. Versionless GraphQL which solves the majority of problems of API versioning by design. It can be complex to set up and introduce to clients but is one of the cheapest approaches to versioning

At Monite, we have chosen Stripe's approach. I have contacted the author of their approach and developed an open-source framework based on our discussion. I will be discussing its methodology and benefits for about a third of my talk. Essentially if you already have a framework or can develop one, having many versions becomes easy and backporting features to all prior versions becomes cheap. This approach allowed Stripe to maintain backwards compatibility with every version since Stripe's inception in 2011.

The core of the methodology is to encapsulate the changes between versions into small "version change modules" which are independent from your business logic. These modules mainly describe three things: what has changed, how to convert a request from prior version to the request of the next version, and how to convert a response from next version to the request of the prior version. The version change modules (or version gates, as Stripe calls them) thus form a chain of migrations: they get automatically applied to every request one after another. This chain effectively turns any request into the request of the latest version and turns the responses from the latest version into responses for any version requested by the user. Thus you have business logic that handles a single version yet your API can support years worth of versions without issue.

Obviously, there are many more things to consider when versioning. I will provide links to all major resources for anyone who wants to delve deeper into it and then I will provide my final recommendation: versioning is hard, unforgiving, and extremely expensive so it is best to mirror the first law of distributed systems here -- if you can afford not to version, then don't.

Links for reference:

<https://github.com/Ovsyanka83/cadwyn>
<https://stripe.com/blog/api-versioning>
<https://habr.com/ru/companies/superjob/articles/577650/>
<https://engineering.linkedin.com/blog/2022/-under-the-hood--how-we-built-api-versioning-for-linkedin-market>
<https://smartlogic.io/blog/2012-12-12-developing-an-api/>
<https://thenewstack.io/tricks-api-versioning/>
<https://github.com/dotnet/aspnet-api-versioning>
<https://github.com/sjkaliski/pinned>
<https://github.com/phillbaker/gates>
<https://github.com/lukepolo/laravel-api-migrations>
<https://github.com/tomschlick/request-migrations>
<https://github.com/keygen-sh/request_migrations>

## Video for the program commitee

TO BE PROVIDED LATER
