title: Getting Started with Go
tags:
  - Go
categories:
  - Personal Projects
date: 2021-02-24 08:00:00
---
Go recently came on to my radar as a very efficient language.  I'm always interested in seeing what else is out there and learning new languages as you never know when you will need a new tool in your toolbelt.  

I've been told Go can behave unexpectedly on Windows, so this blog post will focus on MacOS.

## History of Go
Go, also known as Golang, was designed by 3 programmers at Google in 2007 (for comparison, development was started on Python back in *1989*!).  The 3 designers were allegedly motivated by their hatred of C++ and wanted a new language that had the following characteristics (taken from Wikipedia): 

- static typing and run-time efficiency (like C)
- readability and usability (like Python and JavaScript)
- high-performance networking and multiprocessing

Go is the only language I am aware of that does not support exceptions.  The official docs say this is because the designers believed exception handling makes for convoluted code.  Instead, you can use Go's multi-value returns to easily report errors (but the error handling does seem to be fairly different from other languagues).

The official name of go programmers is gophers.

## Installing Go
Download and install following the instructions [here](https://golang.org/doc/install).  For me, this was as simple as downloading and executing.  I ran `go version` as suggested, which returned `go version go1.16 darwin/amd64`.

## Creating a (very) basic function
The first function I need to create is one that will return the current time as a string.  The syntax for this is:
```go
func getToken() string {
    currentTime := time.Now()
    return fmt.Sprintf("%d%d", currentTime.Hour(), currentTime.Minute())
}
```

Breaking this down, the first line says we are defining a function (func) named getToken that takes no parameters and returns a string.  The next line sets a variable named currentTime to the current time (note, "time" needs to be imported).  Finally, I return the string.  The fmt module has several print statements, including printf, fprintf, and Sprintf (which is reminiscent of Python and Matlab).  Printf will output using a standard output (typically something a monitor can handle), frpintf is used for saving to a file, and Sprintf will output to a char array.

The basic print statement has the string with the formatting followed by variable assignments.  In this case, I have %d two times.  These are considered *verbs*, and there are a lot of them.  %d will output the variables in base 10 format.  Other printing verbs include %v (value in default format), %t (the word 'true' or 'false'), and %b for base 2.  The full list is found [here](https://golang.org/pkg/fmt/).

### The Assignment operator (=)
Use an assignment operator (=) when you are *assigning* or *reassigning* a value to *an already declared variable*.  For instance:
```go
var a int = 1
var b = 2
var c int 
c = 3
```

In each of these cases, the variables are being declared either at or before the time of assignment.  In these instances, you would use the assignment operator.

### The Short Declaration operator (:=)
Use a short declaration operator (:=) when you dont want to specify a type:
```go
d:=4
```

In my function example, I am both declaring and assigning to the currentTime variable, so I use the short declaration operator.

## Adding a github module
This tripped me up for awhile.  You can install go modules from github by using `go get -u github.com/gorilla/mux` (or whatever the module is).  Then, import it using `"github.com/gorilla/mux"` in the imports section.  However, I continued to get an error stating that the module could not be found.  I thought that this was because VSCode has a different GOPATH than wherever it got installed, but that seemed to maybe not be the case (you can check your gopath by using `go env GOPATH`).  I think the thing that finally got this to work for me was: 
```
go mod init main
go get github.com/gorilla/mux
go build main.go
go run main.go
```

## Writing the Login Handler
The login handler will handle bad requests as well as check if the login information is valid.  It first checks to see if the request method is POST, if not, it will send back a status code 400 (bad request).  Then, it will try to read the POST body and returns a status code 500 (internal server error) if it cannot.  It then tries to parse the body into JSON and sends a 400 response if it doesn't match the expected body ({'username':'<username>', 'password':'<password>', 'token':'<token>'}).  

If the request is a POST request with the right JSON body, it then valides the credentials.  If the credentials are good, it sends a 200 status.  if there is some sort of error, it will send 500, and if it's just the wrong credentials it sends 401 (unauthorized).  The code for all of that is below: 

```go
func loginHandler(w http.ResponseWriter, r *http.Request) {
	if r.Method != "POST" {
		w.WriteHeader(400) // bad request
		return
    }	
    // get the body of the POST request
	reqBody, err := ioutil.ReadAll(r.Body)
	if err != nil {
		log.Printf("Error reading post body: %v", err)
		w.WriteHeader(500) // internal server error
		return
    }	
    // try to parse the body
	var login Login
	if err = json.Unmarshal(reqBody, &login); err != nil {
		log.Printf("Error parsing body: %v", err)
		w.WriteHeader(400) // bad request
		return
    }	
    // Do the login comparison
	ok, err := validateCreds(login)	if err != nil {
		log.Printf("Login internal server error: %v", err)
		w.WriteHeader(500) // Internal server error
		return
	}	if !ok {
		log.Printf("Invalid username or password")
		w.WriteHeader(401) // unauthorized
    }	
    
    w.WriteHeader(200)
}
```
### Values and Points in methods
In the example above the `r \*http.Request` makes this a pointer method.  If the method in question needs to modify the parameter (also known as the *receiver*, which in this case is the `r`), then the method must be a pointer method.  If you do not make it a pointer method, the changes to the receiver will be on a copy of the value and therefore will not be available to whatever called the method.


## Routing the Handler
After defining the handler function, I then needed to route it.  I did this within the main function: 
```go 
func main() {
    r := mux.NewRouter()
    r.HandleFunc("/login", loginHandler)
    r.handle("/", r)
    log.Fatal(http.ListenAndServe(":10000", nil))
}
```

After doing this, I started getting CORs errors.  Upon digging in a big, I realized I did not have the correct headers set.  I was previously familiar with CORs and adding headers so this was not completely new.  However, I continued to get CORs errors even after adding the headers.  I have found previously that sometimes you get CORs errors in the front end but the actual error is somewhere else in the API (and not directly a CORs error).  I started debugging by adding some console logs and realized that the request body was empty.  This was strange because I knew I was passing a body!  In the end, it was the OPTIONS preflight request.  This is something that I hadn't seen or handled previously, but makes sense.  Basically, the preflight request asks the server if it is ready and able to send the request.  

The code that ultimately fixed the issue is below:
```go
// example from asanchez.dev/blob/cors-golang-options
func CORS(next http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		// set headers
        (w).Header().Set("Access-Control-Allow-Origin", "*")
		(w).Header().Set("Access-Control-Allow-Headers", "Accept, Content-Type, Content-Length, Accept-Encoding, X-CSRF-Token, Authorization")
        (w).Header().Set("Access-Control-Allow-Methods", "POST, GET, OPTIONS, PUT, DELETE")		
        if r.Method == "OPTIONS" {
			w.WriteHeader(http.StatusOK)
			return
        }		
        fmt.Println("ok")		
        next.ServeHTTP(w, r)
		return
	})
}
```

## Writing a Unit Test
I created a file named `main_test.go` within the root directory of my project.  The general format of this file is:

```go
package mainimport (
	"fmt"
	"testing"
)

func Test_validateCreds(t *testing.T) {
	tables := []struct {
		username string
		password string
		token    string
		isValid  bool
	}{
		{"a@a.com", "123", getToken(), false},
		{"c137@onecause.com", "#th@nH@rm#y#r!$100%D0p#", getToken(), true}, // note that you have to have this ,!
    }	
    for _, table := range tables {
		login := Login{}
		login.Username = table.username
		login.Password = table.password
		login.Token = table.token
		ok, err := validateCreds(login)
		fmt.Printf("%v, %v", ok, err)
		if ok != table.isValid {
			t.Errorf("Validation Login.  Object Recieved: %v, Expected isValid: %v", table, ok)
		}
    }
}
```
This simple project doesnt have much logic in it (and unit test are hard even when you do have testable code!).  The goal of writing this unit test was to just get practice actually writing go unit tests.

I tried to run the unit test by running `go test`, but got an error: `cannot import "main"`.  I got around that by running `go test *.go` instead.  I read a bit more about this [here](https://appliedgo.net/testmain/).  It seems like the best practice is to use your main package for integrating your code or renaming your "main" module to something else.  I definitely have a lot to learn with Go and am excited to see where it goes!

## Next Steps
If this were something I were doing for myself or an actual client, I would definitely integrate hashing and storing (at least) the password.  I would look into ways to pass the password securely and ensure the API is HTTPS only.  I would also want to do more and better unit tests.

In the next post, I'll discuss how I integrated this API inside an Angular UI.