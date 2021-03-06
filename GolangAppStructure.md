# HOW TO STRUCTURE GO APPLICATION
https://medium.com/statuscode/how-i-write-go-http-services-after-seven-years-37c208122831

All components have a single server structure:
```
type server struct {
     db *someDatabase
     router *someRouter
     email EmailSender
}
```

Inside every component called routes.go where all the routing can live:
```
package app
func (s *server) routes() {
    s.router.HandleFunc("/api/", s.handleAPI())
    s.router.HandleFunc("/about", s.handleAbout())
    s.router.HandleFunc("/", s.handleIndex())
}
```
HTTP handlers hang off the server; handler functions don’t actually handle the requests, they return a function that does. If a particular handler has a dependency, take it as an argument.
```
func (s *server) handleSomething() http.HandlerFunc {
    thing := prepareThing()
    return func(w http.ResponseWriter, r *http.Request) {
        // use thing        
    }
}
func (s *server) handleGreeting(format string) http.HandlerFunc {
    return func(w http.ResponseWriter, r *http.Request) {
        fmt.Fprintf(w, format, "World")
    }
}
```

# MIDDLEWARE

Middleware functions take an http.HandlerFunc and return a new one that can run code before and/or after calling the original handler — or it can decide not to call the original handler at all.
```
func (s *server) adminOnly(h http.HandlerFunc) http.HandlerFunc {
    return func(w http.ResponseWriter, r *http.Request) {
        if !currentUser(r).IsAdmin {
            http.NotFound(w, r)
            return
        }
        h(w, r)
    }
}
```

Usually have middleware listed in the routes.go file:
```
package appfunc (s *server) routes() {
    s.router.HandleFunc("/api/", s.handleAPI())
    s.router.HandleFunc("/about", s.handleAbout())
    s.router.HandleFunc("/", s.handleIndex())
    s.router.HandleFunc("/admin", s.adminOnly(s.handleAdminIndex()))
}
```
If an endpoint has its own request and response types, usually they’re only useful for that particular handler.

If that’s the case, you can define them inside the function. If your request/response types are hidden inside the handler, you can just declare new types in your test code.
```
func (s *server) handleSomething() http.HandlerFunc {
    type request struct {
        Name string
    }
    type response struct {
        Greeting string `json:"greeting"`
    }
    return func(w http.ResponseWriter, r *http.Request) {
        ...
    }
}
```
# SETUP DEPENDENCIES USING sync.Once

If you have to do anything expensive when preparing the handler, I defer it until when that handler is first called. This improves application startup time.
```
func (s *server) handleTemplate(files string...) http.HandlerFunc {
    var (
        init sync.Once
        tpl  *template.Template
        err  error
    )
    return func(w http.ResponseWriter, r *http.Request) {
        //expensive work, the code is only executed one time, and other calls (other people making the same request) will block           until it’s finished.
        init.Do(func(){
            tpl, err = template.ParseFiles(files...)
        })
        if err != nil {
            http.Error(w, err.Error(), http.StatusInternalServerError)
            return
        }
        // use tpl
    }
}
```

# TESTABILITY

Such a server is easily testable. 
Create a server instance inside each test — if expensive things lazy load, this won’t take much time at all, even for big components
By calling ServeHTTP on the server, we are testing the entire stack including routing and middleware, etc. You can of course call the handler methods directly if you want to avoid this
Use httptest.NewRecorder to record what the handlers are doing
```
func TestHandleAbout(t *testing.T) {
    is := is.New(t)
    srv := server{
        db:    mockDatabase,
        email: mockEmailSender,
    }
    srv.routes()
    req, err := http.NewRequest("GET", "/about", nil)
    is.NoErr(err)
    w := httptest.NewRecorder()
    srv.ServeHTTP(w, req)
    is.Equal(w.StatusCode, http.StatusOK)
}
```
