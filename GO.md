# GoLang Resources

## Configuration amd Metrics
* [Parsing cli params](https://golang.org/pkg/flag/)
* [Golang library for managing configuration data from environment variables](https://github.com/kelseyhightower/envconfig)
* [Viper is a complete configuration solution for Go applications including 12-Factor apps. It is designed to work within an application, and can handle all types of configuration needs and formats](https://github.com/spf13/viper)
* [Cobra is both a library for creating powerful modern CLI applications as well as a program to generate applications and command files. Integrated with viper.](https://github.com/spf13/cobra)
* [Metrics library](https://golang.org/pkg/expvar/)

## Compilation and Build
* [Conditional compilation](https://dave.cheney.net/2013/10/12/how-to-use-conditional-compilation-with-the-go-build-tool)
* [Embed Static Assets](https://tech.townsourced.com/post/embedding-static-files-in-go/)

## Optimization for Performance
Profile before optimizing. Bottlenecks will surprise you. Don’t guess where the code spends it’s time, use a profiler and see.

### Performance Mantras *By Craig Hanson and Pat Crain*

1. **Don’t do it.** Can we avoid doing the calculation at all? For example: Do we need to parse the input or just pass it as-is?
2. **Do it, but don’t do it again.** Can we use memoization/caching? Parse objects once at the “edges” and use the parsed objects internally.
3. **Do it less.** Do we need to run this every millisecond? Can every second work? Can we use only a subset of the data?
4. **Do it later.** Can we make this API call async?
5. **Do it when they’re not looking.** Can we run the calculation in the background while doing another task?
6. **Do it concurrently.** Will concurrency help here? Consider Amdhal’s law.
7. **Do it cheaper.** Can we use a map here instead of a slice? Research available algorithms and data structures and know their complexity. Test them on your data

### Go Specific Performance Mantras
1. Memory Allocation: Avoid allocations as possible (see the design of io.Reader). Pre-allocate if you already know the size. Be careful of slices keep large amounts of memory e.g. ```s := make([]int, 1000000)[:3]```
2. Defer might slow you Down. However consider the advantages.
3. Strings are immutable. Use bytes.Buffer or strings.Builder
4. Know when a goroutine is going to stop. Avoid goroutine leaks. Use context for cancellation/timeouts.
5. Cgo calls are expensive. Group them together in one cgo call.
6. Channel can be slower than sync.Mutex. However they are much easier to work with.
7. Interface calls are more expensive the struct calls. You can extract the value from the interface first. However it’s less generic code.
8. Use ```go run -gcflags=-m -l```. You’ll see what escapes to the heap.

## Making Go Programs High Performing
* https://dave.cheney.net/high-performance-go-workshop/dotgo-paris.html
* https://www.slideshare.net/TylerTreat/so-you-wanna-go-fast-80300458
* [pprof - library for profiling Go programs](https://golang.org/pkg/net/http/pprof/), [how to use pprof](https://blog.golang.org/profiling-go-programs)
* [The benchcmp command displays performance changes between benchmarks.](https://godoc.org/golang.org/x/tools/cmd/benchcmp)
* [Debugging performance issues in Go programs](https://github.com/golang/go/wiki/Performance)


## Testing
* [Go junit](https://godoc.org/github.com/stretchr/testify)
* [Black box testing https](https://golang.org/pkg/testing/quick/)
* [Subtests and sub-benchmarks/cucumber-like testing](https://blog.golang.org/subtests)
* [Making godoc examples part of tests](https://blog.golang.org/examples)

## Modules
[Go modules](https://github.com/golang/go/wiki/Modules) are the latest and official way of managing dependencies. 

1. Once per project, create go.mod (assume our project name is dbX): ```go mod init oracle.com/go/dbX```. If you’re under GOPATH (run go env GOPATH to check), you’ll need to set GO111MODULE environment variable:```export GO111MODULE=on```
2. To install a package run: ```go get github.com/pkg/errors```. To install a specific version run: 
```go get github.com/pkg/errors@v0.8.0```.
3. To have *go mod* scan your code and update go.mod run: ```go mod tidy```.
4. *go get* uses git to fetch packages. This means you can have an internal company git server and install packages from it. You can use Athens, go-import-redirect, Go Registry and others to have custom URLs. If you don’t have a git server, you can use the replace directive in go.mod to install packages from a local directory.
```module dbX require (github.com/pkg/errors v0.8.1)``` (Replace github.com/pkg/errors => /path/to/errors).
5. [You can also choose to vendor your dependencies.](https://arslan.io/2018/08/26/using-go-modules-with-vendor-support-on-travis-ci/) Run ```go mod vendor``` and then a vendor directory will be created with all your dependencies. This mean you’re not relying on internet connection to build. To use the vendor directory, pass ```-mod=vendor``` to all go commands (e.g. ```go build -mod=vendor```) or set it in GOFLAGS environment variable (e.g. ```export GOFLAGS="-mod=vendor"```)


Additional resources on modules
* https://blog.golang.org/using-go-modules
* https://www.youtube.com/watch?v=ms5l0zxC-uM

## HTTP
HTTP packages:
1. https://golang.org/pkg/net/http/
2. http://www.gorillatoolkit.org/pkg/mux
3. https://github.com/go-chi/chi
4. https://godoc.org/github.com/valyala/fasthttp
5. https://godoc.org/github.com/hashicorp/go-cleanhttp

[HTTP middleware](https://www.alexedwards.net/blog/making-and-using-middleware)

## Managing Dependencies
[Managing dependencies](https://research.swtch.com/deps)

## Go Code Quality
* https://github.com/golang/go/wiki/CodeReviewComments
* https://go-proverbs.github.io/
* https://www.youtube.com/channel/UC_BzFbxG2za3bp5NRRRXJSw

## Error Handling
* https://blog.golang.org/error-handling-and-go
* https://medium.com/statuscode/pipeline-patterns-in-go-a37bb3a7e61d
* https://blog.golang.org/defer-panic-and-recover

## Concurrency
* https://talks.golang.org/2012/waza.slide#1
* https://www.ardanlabs.com/blog/2017/10/the-behavior-of-channels.html
* https://medium.com/justforfunc/why-are-there-nil-channels-in-go-9877cc0b2308
* https://speakerdeck.com/arnecls/concurrency-patterns-in-go
* https://dave.cheney.net/2013/04/30/curious-channels
* https://rodaine.com/2018/08/x-files-sync-golang/
* https://www.oreilly.com/library/view/concurrency-in-go/9781491941294/

## Tips and Tricks
* http://www.golangbootcamp.com/book/tricks_and_tips
* [Godoc tricks](https://godoc.org/github.com/fluhus/godoc-tricks)

