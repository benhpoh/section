Our webservers are struggling to keep up with the increased volume of traffic since the COVID lockdown and are hoping your caching solution can alleviate some burden Can you please make sure 
- the category HTML pages are cached for 6 hours and the homepage for 24 hours? 
    - "category" - 6 hours
    - "home" - 24 hours
- We also have admin users that access the site with a particular cookie, and do not wish for pages they request to end up in the cache. The cookie they use is called "site_admin=12345". 
    - no cache - cookie="site_admin=12345"
- Other than that, please make sure our images, JS and CSS are cached for 24 hours.
    - cache - [.js, .css, .jpg, ...]


## Setting up parameters
```ts
let cacheResponse: boolean 
let cacheDurationInHours: number

// Create an extendable no cache rule set
noCacheRules = {
    cookies: {
        noCacheList: ["site_admin=12345"]
    }
    // example for if certain pages / file formats are not to be cached
    // html: {}
}

// Create an extendable cache rule set based on criteria (eg file format)
cacheRules = {
    html: {
        "/category": {
            cacheDurationInHours: 6
        },
        "/": {
            cacheDurationInHours: 24
        }
    },
    js: {
        cacheDurationInHours: 24
    },
    css: {
        cacheDurationInHours: 24
    },
    jpg: {
        cacheDurationInHours: 24
    },
    png: {
        cacheDurationInHours: 24
    },
}
```

## Rules in action
When a request comes in, validate it against each rule within `noCacheRules`
```ts
if (`request.cookies` contains a cookie in `noCacheRules.cookies.noCacheList` or /* extend with further rules */)
    cacheResponse = false
else
    cacheResponse = true
end
```


Once it's validated and `cacheResponse` is true, we then determine how long the cache duration should be
```ts
if (`request.filetype` != html)
    // Set cache duration based on what the file type is - such as js, css, or images
    cacheDurationInHours = cacheRules["request.filetype"].cacheDurationInHours
else if (`request.filetype` == html)
    // Set cache duration based on what the html page is - home or category
    cacheDurationInHours = cacheRules.html["request.path"].cacheDurationInHours
else 
    // No matches in cacheRules
    cacheResponse = false
end

if cacheResponse == false
    cacheDurationInHours = 0
end
```


## Apply cache duration
At this point we know how long the response should be cached for. Simply apply it!