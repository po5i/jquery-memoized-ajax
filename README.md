# jquery Memoized Ajax

Memoization is a technique for caching the results of expensive function calls so that subsequent calls (given the same inputs) are very fast. This property is also useful for expensive AJAX calls. This plugin adds a method called `$.memoizedAjax` that behaves exactly like `$.ajax`, but caches the result in localStorage. The next time `memoizedAjax` is called with the same `data` and `url` arguments, it will return the result immediately, in the form of a resolved `$.Deferred` (if you have no idea what a deferred is, that's ok. Just treat this exactly like `$.ajax`...with a few caveats. See below).

## Example usage:

Use it exactly how you would use `$.ajax`.

```javascript
$.memoizedAjax({
  url: '/reallyFreakinSlowLookup',
  type: 'GET',
  data: { name: 'Bobby Tables' },
  success: function(person) { console.log(person.age); }
});

// goes and does the ajax call and logs the result after some time

$.memoizedAjax({
  url: '/reallyFreakinSlowLookup',
  type: 'GET',
  data: { name: 'Bobby Tables' },
  success: function(person) { console.log(person.age); }
});

// resolves immediately, with no ajax call, and logs the result
```

## Caveats

Some things to watch out for when using `memoizedAjax` instead of just `ajax`. When `memoizedAjax` returns a cached result, it will actually return a resolved jQuery deferred object, not a jqXHR. Most of the time, this distinction is no big deal. There are times, however, where you'd like to `abort` a jqXHR object that you've stored in a variable. In those cases, you should check for the `abort` method before calling it, as a jQuery deferred object doesn't have an `abort` method, and your program will throw an error if the result happens to be cached.

```javascript
var ajaxCall = $.memoizedAjax({
  url: '/reallyFreakinSlowLookup',
  type: 'GET',
  data: { name: 'Bobby Tables' }
});

// DON'T DO THIS
ajaxCall.abort(); // will throw an error if memoizedAjax returns a cached result

// DO THIS INSTEAD
ajaxCall.abort && ajaxCall.abort();
```

One thing to keep in mind is that **accessing localStorage is a blocking process**. Thus, it's not a good idea to do 10 `memoizedAjax` calls in a row, as this can really hamper your page performance.
