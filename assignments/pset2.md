# Problem Set 2: Composing Concepts

## Concept Questions

1. The contexts are used in the NonceGeneration concept to ensure the strings are unique within the context/environment we're using. A context will end up being a domain in the URLShortening app; for example, tinyURL would be a context.
2. The NonceGeneration must store sets of used strings to adhere to its purpose -- make sure a new string doesn't conflict with an old string in the provided context. Each string in the set of used strings in the specification maps a counter number in the implementation; the counter is the size of the used string set.
3. One advantage of this scheme is that the short URL suffix is easier to type and less prone to typos. However, there are more privacy concerns because anyone can access it. For example, if the short link links to an editable Google Doc and someone else brute forces the short link, they will have full capabiltiy to do anything. I would modify the NonceGeneration concept to reference the database of common dictionary words when generating a nonce.

## Synchronization Questions

1. The generate sync only includes the shortUrlBase argument because it only needs the base as the context to generate a unique nonce. Generating a nonce isn't dependent on the targetURL. On the other hand, to register a shortened URL, it needs the targetURL to associate the generated nonce with.
2. This convention isn't used in every case because some argument names are more general with the variable name being more specific, which allows for the concept to be reused.
3. The setExpiry sync only depends on register to fully complete (which implicitly depends on request and generate to fully complete). It also only needs the information provided from register, and not request (the targetUrl and shortUrlBase) aren't needed to set an expiration for the shortUrl.
4. I would merge the generate and register synchronization. The shortUrlBase doesn't need to be a variable anymore because it's fixed.
   **sync** register <br>
   **when** <br>
   &nbsp;Request.shortenUrl (targetUrl) <br>
   &nbsp;NonceGeneration.generate(): (nonce) <br>
   **then** UrlShortening.register (shortUrlSuffix: nonce, targetUrl)
5. **sync** handleExpiry <br>
   **when** <br>
   &nbsp;ExpiringResource.expireResource (): (resource: shortUrl) <br>
   **then** UrlShortening.delete(shortUrl) <br>

## Extending the design

1.  **concept**AnalyticsTracking [User, Resource] <br>
    **purpose**view how many times a resource created by a user has been used <br>
    **principle**a user registers their resource and can get a report of how many times it was accessed <br>
    **state** <br>
    a set of Profiles with <br>
    &nbsp; a user User <br>
    &nbsp; a set of ResourceCounts <br>
    a set of ResourceCounts with <br>
    &nbsp; a resource Resource <br>
    &nbsp; an accessCount NUmber <br>
    **actions** <br>
    registerResource(user: User, resource: Resource) <br>
    &nbsp;&nbsp;**requires** this resource doesn't already exist with any user <br>
    &nbsp;&nbsp;**effect** adds this resource with an accessCount initialized to 0 to the ResourceCounts and adds this ResourceCount to the Profile with this user <br>
    addCount(resource: Resource) <br>
    &nbsp;&nbsp;**requires** this resource is associated with a ResourceCount and Profile <br>
    &nbsp;&nbsp;**effects** increments this resource's accessCount by 1 <br>
    getResourceCount(user: User, resource: Resource): (count: Number) <br>
    &nbsp;&nbsp;**requires**this resource is in this user's Profile <br>
    &nbsp;&nbsp;**effects**returns this resource's accessCount <br>
    removeResource(resource: Resource) <br>
    &nbsp;&nbsp;**requires** this resource exists in a Profile
    &nbsp;&nbsp;**effects** removes this resource from the set of ResourceCounts
2.  **sync** registerURLtoUser <br>
    **when** URLShortening.register() : (shortUrl) <br>
    **then** AnalyticsTracking.registerResource(user: User, shortURL: Resource)
    <br>
    **sync** incrementAccess <br>
    **when** URLShortening.lookup(shortUrl)<br>
    **then** AnalyticsTracking.addCount(shortUrl: Resource) <br>
    <br>
    **sync** report <br>
    **when** Request.getResourceCount(user, shortUrl: Resource)<br>
    **then** AnalyticsTracking.getResourceCount(user, shortUrl: Resource): (count: Number) <br>
3.

- For users to choose their own URL, we can modify the Request action to take in an additional optional shortURLSuffix and add a sync: <br>
  **sync** registerCustomSuffix <br>
  **when** Request.shortenUrl (targetUrl, shortUrlBase, shortUrlSuffix) <br>
  **then** URLShortening.register(shortUrlSuffix, shortUrlBase, targetUrl) <br>
- To apply the word as nonce strategy, only the implementation of NonceGeneration would change so that it generates strings (that are now limited to valid words) that is not already used by the context.
- Including the target URL in the analytics would be undesirable because multiple users could have different short URLs to the same targetURL and the purpose of the analytics is to limit who can see the analytics of a shortURL to the user that generated it. This breaks user privacy.
- To generate short URLs that are not easily guessed, the implementation of NonceGeneration would be modified (similar to the "word as nonce" strategy). For example, we can require the nonce to be at least 6 characters long with each character being randomly generated.
- Supporting reporting of analytics to creators who have not registered as a user is undesirable because it hurts the purpose that analytics should not be public. Not everyone should be able to view the analytics and without having a user account, the concept wouldn't know how to tell if someone that wants to access the analytics is the original creator or someone else.
