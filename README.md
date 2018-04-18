# Test case to show a bug with Crate blob calls

## Description of the problem

What is happening is this:

1. a call to a crate blob that doesn't exist returns a 404
2. the next call, no matter to what returns the same 404 response
3. the next call returns whatever was the response in #2
4. the next call returns whatever was in the response in #3 and so on

From that point on any call that hits the same IP address is one response behind. 

And if you get another 404, the calls become 2 behind as seen in testSeveralCallsAfterMultiple404.

## Clients

The test case in this repo shows the error with HttpClient 4.5.5.  The bug also happens with OkHttp client, see
https://github.com/square/okhttp/issues/3964.  I tested the py-crate client and it did not show the bug.  It appears 
at least Go has this issue too, see https://github.com/herenow/go-crate/issues/25

## Demonstration

The error has been demonstrated with BlobTest.  Simply running

```
mvn clean test
```

in this repo will show 3 failures.

```
Results :

Failed tests:   test2CallsAfter404(com.mjwall.crate.BlobTest): expected:<[C]> but was:<[A]>
  testSeveralCallsAfterMultiple404(com.mjwall.crate.BlobTest): expected:<[D]> but was:<[A]>
  get200After404(com.mjwall.crate.BlobTest): expected:<200> but was:<404>

Tests run: 6, Failures: 3, Errors: 0, Skipped: 0
```

In my research, I came across https://github.com/crate/crate/issues/3128 whose fix may have exposed this bug.
