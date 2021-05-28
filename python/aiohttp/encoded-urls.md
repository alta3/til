# Making a request with URL parameteters in aiohttp has unexpected escaping

When making a request with a "fully formed" url that includes parameters, aiohttp has an odd quirk that trips me up all the time.
It is [documented](https://docs.aiohttp.org/en/stable/client_quickstart.html#passing-parameters-in-urls) (replicated below) but I always forget.
My memory is probably not consistant on this point because, as [noted by @flyte](https://github.com/aio-libs/aiohttp/issues/1725#issuecomment-365932689):

> It's inconsistent with [python] requests and curl and is a bit astonishing once you finally work out why your requests aren't working properly.

Long story short, aiohttp will (IMO paternilistically) perform url escapes and encoding unless explicitly told not to. 
Here's the documentation and the solution if you find yourself scratching your head why your url parameters aren't making it to the server correctly:

> *aiohttp* internally performs URL canonicalization before sending request.
> 
> Canonicalization encodes *host* part by `IDNA` codec and applies `requoting` to *path* and *query* parts.
> 
> For example ``URL('http://example.com/путь/%30?a=%31')`` is converted to ``URL('http://example.com/%D0%BF%D1%83%D1%82%D1%8C/0?a=1')``.
> 
> Sometimes canonicalization is not desirable if server accepts exact representation and does not requote URL itself.
> 
> To disable canonicalization use ``encoded=True`` parameter for URL construction:
> 
> ``` python
> await session.get(
>     URL('http://example.com/%30', encoded=True))
> ```
