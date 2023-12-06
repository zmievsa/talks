# API Versioning in Microservices

API versioning and microservices are both incredibly impressive when done correctly such as [API Versioning at Stripe](https://stripe.com/blog/api-versioning) and [microservices at Netflix](https://youtu.be/CZ3wIuvmHeM). However, both are also extremely expensive to make and even more expensive when implemented incorrectly. But do we ever talk about combining API Versioning and microservices? Let's discuss a few issues at play here and how to work with them.

## How to achieve consistency for the end user

*How to combine versions from multiple services, why dates are used, how version waterfalling works.*

## How would services interact?

*API versioning can also be useful for interactions in-between services. EXAMPLE*

### Services should not leak versions

Let's say that `Payments` service makes HTTP calls to `Invoices` service. This is already a problem: we now have synchronous calls between services. This introduces coupling and we should now consider whether these services should be separate at all. Alright, let's assume that they should be. Which version would `Payments` use? In correct API versioning, `Payments` would simply choose a single version and stick with it -- just like a client would. However, in incorrect API versioning `Payments` could decide to use multiple versions of `Invoices` API based on API version in `Payments`. This is what Brandur Leach calls a `side effect` -- `Payments` service uses a different version of another service, i.e. a different business logic based on its own API Version. If you have a single business logic for all versions, then each side effect like this will be another `if` statement within it. Afters some nubmer of versions, your code will become so complicated that the maintenance burden of API versioning will be simply too large to sustain. This is why I recommend you to avoid side effects.

In API versioning, your microservices should view other services as no more than external clients. Do you expect a single service of your end user to use multiple API versions of your microservice? Probably not immediately. Returning back to `Payments` example: you should use a single version of `Invoicing` service in all your versions if possible. There are a few scenarios that you should be vary of:

#### Invoicing service has its own side effects

If `Invoicing` service has its own side effects and your clients expect that these side effects will proliferate to `Payments`, then `Payments` microservice becomes a leaky abstraction -- its logic is not just coupled, its entirely dependent on another service. This means that you are not building microservices, you are building a distributed monolith. Microservices must be independent, especially in their logic and versions. Otherwise it means that whenever `Invoicing` service makes a mistake or breaks its contract in any way -- it will immediately proliferate to your service. Yet for API versioning it means that any side effect that `Invoicing` introduces will **have to** proliferate to your service. So this makes your versions coupled -- if `Invoicing` released a new version that affects your functionality, then you must also release a new version **at the same time** that supports the new logic to be consistent for your end users. This is a classic deployment coupling that should never be introduced in microservices.

Instead, `Payments` should try to stay independent from invoicing and should only publish asynchronous events that `Invoicing` is going to be able to read. This will significantly decouple and simplify the two services which in turn leads to faster and simpler releases.

#### Payments uses Invoicing schema structure in its requests or responses

Let's say that `Payments` response schema has a nested field `invoice` that is based on the schema from `Invoicing` service. Now you have a big problem: if invoicing service alters its schemas in a new version, `Payments` will also have to introduce a new version. The scarier thing is that you cannot introduce breaking changes into pre-existing API versions so once the new version of `Invoicing` releases to the public, `Payments` service will also have to release a new version **at the same time**.  Otherwise you will confuse your user by using the same schema yet not following it. This is, again, a deployment-level coupling which means that you are building a distributed monolith.

Instead, you should denormalize your data and have your own schema for `invoice` field that is independent from `Invoicing` service. If your `invoice` must absolutely be consistent with `Invoicing` service then you have two options:

* you are most likely building a distributed monolith and these services should become one
* you should only store the `ID` of the invoice so that your users can `GET` the invoice themselves. This will eliminate the coupling between your services but logically they will sadly still be a distributed monolith
