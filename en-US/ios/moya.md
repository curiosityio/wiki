---
name: Moya
---

# Upload files via Moya

* Configure our `TargetType` in order to define where to upload files.

```
import Moya

enum UserService {
    case changeProfileImage(userId: Int, image: Data)
}

extension UserService: TargetType {
    var baseURL: URL { return URL(string: AppConstants.apiEndpoint)! }

    var path: String {
        switch self {
        case .changeProfileImage(let userId, _):
            return "/v1/users/\(userId)"
        }
    }
    var method: Moya.Method {
        case .changeProfileImage:
            return .patch
        }
    }
    var parameters: [String: Any]? {
        switch self {
        case .changeProfileImage: return nil
        }
    }
    var parameterEncoding: ParameterEncoding {
        switch self {
        case .changeProfileImage:
            return URLEncoding.methodDependent
        }
    }
    var sampleData: Data {
        return Data()
    }
    var task: Task {
        switch self {
        case .changeProfileImage(_, let image):
            return .upload(.multipart([MultipartFormData(provider: .data(image), name: "user[image]", fileName: "profile_image.jpg", mimeType: "image/jpg")]))
        }
    }
}
```

The important parts to keep in mind here are the `task` and `parameters` variables because these can break your method pretty easily. At first, I tried to return my image inside of the `parameters` variable using...

```
var parameters: [String: Any]? {
    switch self {
    case .changeProfileImage(_, let image):
      return [
        "user": [
          image
        ]
      ]
    }
}
```

...yeah, don't do that. The multipart upload definition in `task` is what does the work to upload. Not the parameters. If you need to upload data *besides* the image, the parameters may be used to upload that data but for the image, do not include it in the parameters.

For the `task` variable, this is saying that we need to upload multipart form data (which is usually used for uploading images over http). `MultipartFormData` is an object that takes in info to describe what you want to upload. `.data()` as the `provider` is saying that I want to upload the image using a Swift `Data` object (which you can get from a UIImage by calling: `UIImageJPEGRepresentation(uiimageHere, 1)!`). You can also use `.file()` and `.stream()` for the provider. For images, I find `.data()` works well. `name` parameter specifies the HTTP field name you want to upload the image under. This is important as it's the property that your API is looking for the image from. `fileName` is the file name of the file. `mimeType` is the mime type for the file.
