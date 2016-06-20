---
name: Alamofire
---

# Get JSON string from HTTP response

```
import Alamofire
import ObjectMapper

...

let params: [String: AnyObject] = [
    "email": email,
    "password": password
]

Alamofire.request(Router.LoginUser(params))
    .responseJSON(completionHandler: { (response: Response<AnyObject, NSError>) in
        let responseData = Mapper<DATA>().map(response.result.value)
    })
```

`response.result.value` is the code we are looking for here. `.responseJSON` parses the response into JSON and then to access the parsed JSON: `response.result.value`. This allows us to use ObjectMapper library to map the JSON string to actual data.

# Create a Router object to house API HTTP interaction rules

It is handy for Alamofire to house all of the endpoints, parameters, encoding rules in one place to access when we want to use `Alamofire.request()`. This is how:

The example below has 2 great examples. 1 of them injects a string into the endpoint: `/users/usernamehere/repos` while the other is a GET call with query params (works the same way with PUT, POST, anything with a body).

```
import Alamofire

...

class API {

    enum Router: URLRequestConvertible {
        static let baseURL = "https://api.github.com"

        case GetUserRepos(String) // use `String` when you need to include a string in the /end/point/
        case GainLoginAccess([String: AnyObject]) // use `[String: AnyObject]` when you have query parameters or a body.

        var method: Alamofire.Method {
            switch self {
            case .GetUserRepos:
                return .GET
            case .FooBar:
                return .GET
            }
        }

        var path: String {
            switch self {
            case .GetUserRepos(let githubUsername):
                return "/users/\(githubUsername)/repos"
            case .FooBar:
                return "/users/login/i/made/this/endpoint/up"
            }
        }

        var URLRequest: NSMutableURLRequest {
            let URL = NSURL(string: Router.baseURL)!
            let mutableURLRequest = NSMutableURLRequest(URL: URL.URLByAppendingPathComponent(path))
            mutableURLRequest.HTTPMethod = method.rawValue

            // If using OAuth2 with tokens, use this to set the token on all requests automatically.
            if let token = ClientManager.getAccessToken() {
                mutableURLRequest.setValue("Bearer \(token)", forHTTPHeaderField: "Authorization")
            }

            switch self {
            case .GainLoginAccess(let parameters):
                return ParameterEncoding.URL.encode(mutableURLRequest, parameters: parameters).0
            default: // no parameters or body so don't have anything to encode
                return mutableURLRequest
            }
        }
    }

}
...

// Now you can call the router functions to make a request
Alamofire.request(Router.GetUserRepos("curiosityio"))
    .responseJSON(completionHandler: { (response: Response<AnyObject, NSError>) in
        // do something with response
    })

// or for the other call with parameters/body

let params: [String: AnyObject] = [
    "email": email,
    "password": password
]

Alamofire.request(Router.FooBar(params))
    .responseJSON(completionHandler: { (response: Response<AnyObject, NSError>) in
        // do something with response
    })    
```

# Set OAuth2 token in header

I use the Alamofire router object to do this for me automatically on every request once token is set after login:

```
... more router data explained above on section about creating a Router object to house API routes  
var URLRequest: NSMutableURLRequest {
    let URL = NSURL(string: Router.baseURL)!
    let mutableURLRequest = NSMutableURLRequest(URL: URL.URLByAppendingPathComponent(path))
    mutableURLRequest.HTTPMethod = method.rawValue

    // If using OAuth2 with tokens, use this to set the token on all requests automatically.
    if let token = ClientManager.getAccessToken() { // If a token exists, set it.
        mutableURLRequest.setValue("Bearer \(token)", forHTTPHeaderField: "Authorization")
    }

    switch self {
    case .GainLoginAccess(let parameters):
        return ParameterEncoding.URL.encode(mutableURLRequest, parameters: parameters).0
    default: // no parameters or body so don't have anything to encode
        return mutableURLRequest
    }
}
```
