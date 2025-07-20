## Make Request With [[Golang]]

To Do Operations with URL we use `net/url` module
### GET : 

```Go
package main

import (
	"fmt"
	"io"
	"net/http"
)

const url = "https://lco.dev"

func main() {
	response, err :=http.Get(url)
	if err!=nil {
		panic(err)
	}
	
	defer response.Body.Close() // Responsible of the Programmer to Close the connection, otherwise it will be requesting on indefinitely
	
	data, err:=io.ReadAll(response.Body) // Data is in bytes format
	if err!=nil {
		panic(err)
	}
	
	content := string(data) // We have to convert bytes data to string
	
	fmt.Println(content)
}

func CheckNilErr(err error) {
	if err!=nil {
		panic(err)
	}
}
```

### POST : 

```Go
package main

import (
	"fmt"
	"io"
	"net/http"
	"strings"
)

const url = "http://localhost:4080/ddis/user"

func main() {
	firstname := "Test"
	lastname := "go"
	username := "testgo1@cdac.in"
	secret := "Cdac@123"
	
	// Using string interpolation to include the values of variables
	jsonData := strings.NewReader(fmt.Sprintf(`
		{
			"firstname": "%s",
			"lastname": "%s",
			"username": "%s",
			"secret": "%s"
		}
	`, firstname, lastname, username, secret))
	
	response, err:=http.Post(url,"application/json", jsonData)
	CheckNilErr(err)
	defer response.Body.Close()

	data, err := io.ReadAll(response.Body)
	CheckNilErr(err)
	
	content := string(data)
	
	fmt.Println(content)
}

func CheckNilErr(err error) {
	if err!=nil {
		panic(err)
	}
}
```

### PATCH : 

```Go
package main

import (
	"fmt"
	"io"
	"strings"
	"net/http"
)

const url = "http://localhost:4080/ddis/user"

func main() {
	jsonData := strings.NewReader(`
		{
			"_id" : "659f7b7498cfaac6a8579e4a",
			"isEnabled": true
		}
	`)
	request, err:= http.NewRequest("PATCH",patchUrl,jsonData)
	CheckNilErr(err)
	
	request.Header.Set("Content-Type", "application/json")
	client := &http.Client{}
	response, err := client.Do(request)
	CheckNilErr(err)
	
	defer response.Body.Close()
	
	data, err := io.ReadAll(response.Body)
	CheckNilErr(err)
	
	content := string(data)
	
	fmt.Println(content)
}

func CheckNilErr(err error) {
	if err!=nil {
		panic(err)
	}
}
```

## Handling JSON Data

```Go
package main

import (
	"fmt"
	"strings"
	"encoding/json"
)

type user struct {
	Firstname string `json:"firstname"`
	Lastname string `json:"lastname"`
	Username string `json:"username"`
	Secret string `json:"-"`
	Tags []string `json:"tags,omitempty"`
}

func main() {
	users := []user{
	{Firstname: "Nick",Lastname: "Maxx",Username: "nickmaxx@cdac.in",Secret: "abc",Tags: []string{"user","admin"}},
	{"Junpei", "nil" , "junpei@cdac.in", "bac", nil},
	}

	content, err := json.MarshalIndent(users,"","\t")
	CheckNilErr(err)
	  
	jsonContent := string(content
	fmt.Println(jsonContent)
	  
	var decodedJson []user
	
	checkValid := json.Valid([]byte(jsonContent))

	if checkValid {
		error := json.Unmarshal([]byte(jsonContent),&decodedJson)
		CheckNilErr(error)
		fmt.Printf("Decoded User: %#v", decodedJson)
	}
}

func CheckNilErr(err error) {
	if err!=nil {
		panic(err)
	}
}
// Expected Output:
//[
//       {
//               "firstname": "Nick",
//               "lastname": "Maxx",
//               "username": "nickmaxx@cdac.in",
//               "tags": [
//                       "user",
//                       "admin"
//               ]
//       },
//       {
//               "firstname": "Junpei",
//               "lastname": "nil",
//               "username": "junpei@cdac.in"
//       }
//]
//Decoded User [{Nick Maxx nickmaxx@cdac.in  [user admin]} {Junpei nil //junpei@cdac.in  []}]
```

==Using Map: We do not want to create struct predefined always as we will not know what will come to us as JSON data all the time==

```Go
package main

import (
	"fmt"
	"strings"
	"encoding/json"
)

type user struct {
	Firstname string `json:"firstname"`
	Lastname string `json:"lastname"`
	Username string `json:"username"`
	Secret string `json:"-"`
	Tags []string `json:"tags,omitempty"`
}

func main() {
	users := user{Firstname: "Nick", Lastname: "Maxx", Username: "nickmaxx@cdac.in", Secret: "abc", Tags: []string{"user","admin"}}

	content, err := json.MarshalIndent(users,"","\t")
	CheckNilErr(err)  
	
	jsonContent := string(content)
		  
	var decodedJsonMap map[string]interface{} // We donot know the type of value always and it will not be always similar when it comes to us
	error := json.Unmarshal([]byte(jsonContent), &decodedJsonMap)
	CheckNilErr(error)  
	
	fmt.Printf("Decoded User: %v \n", decodedJsonMap) 
	
	for k, v := range decodedJsonMap {
		fmt.Printf("Key is %v, Value is %v, and type is %T \n", k, v,v)
	}
}

func CheckNilErr(err error) {
	if err!=nil {
		panic(err)
	}
}

```

## Go Modules

### Initializing a Go Module

```bash
# Navigate to your project directory
cd your_project_directory

# Initialize a new Go module
go mod init module_name
```

- Use `go mod init` to initialize a new Go module.
- Replace `module_name` with the desired name for your module. usually like this: `github.com/{username}/{module_name}`

### Adding Dependencies

```Go
# Add a dependency to your project
go get package_name@version

```

- Use `go get` to add a dependency to your project.
- Specify the desired version using `@version`.

### Listing Dependencies

```bash
# List all dependencies of the project 
go list -m all
```

- Use `go list -m all` to list all dependencies of the project.

### Updating Dependencies

```bash
# Update all dependencies to their latest versions 
go get -u ./...
```

- Use `go get -u ./...` to update all dependencies to their latest versions.

### Removing Unused Dependencies

```bash
# Remove unused dependencies 
go mod tidy
```

- Use `go mod tidy` to remove unused dependencies from the `go.mod` file.

### Vendoring Dependencies

```bash
# Create a vendor directory with dependencies
go mod vendor
```

- Use `go mod vendor` to create a `vendor` directory with project dependencies.
- `vendor` is like `node_modules` in `npm`

### Run Through Vendor

```Go
go run -mod=vendor main.go
```

- Use `-mod=vendor` to run through vendor libraries
 
### Verifying Dependencies

```bash
# Verify dependencies have expected content 
go mod verify
```

- Use `go mod verify` to verify that dependencies have the expected content.

### Cleaning the Module Cache

```bash
# Clean the module cache 
go clean -modcache
```

- Use `go clean -modcache` to clean the module cache.

