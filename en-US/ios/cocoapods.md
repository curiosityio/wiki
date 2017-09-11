---
name: Cococapods
---

# How to create cocoapods library

<iframe width="560" height="315" src="https://www.youtube.com/embed/gNMNeqXKnzw" frameborder="0" allowfullscreen></iframe>

# How to install dependencies for your Cocoapods library

After you use `pod lib create NameOfLib` go into your `NameOfLib.podspec` to add your dependencies:

```
#
# Be sure to run `pod lib lint Mac.podspec' to ensure this is a
# valid spec before submitting.
#
# Any lines starting with a # are optional, but their use is encouraged
# To learn more about a Podspec see http://guides.cocoapods.org/syntax/podspec.html
#

Pod::Spec.new do |s|
  s.name             = 'Mac'
  s.version          = '0.1.0'
  s.summary          = 'iOS framework to build offline-first mobile apps.'

# This description is used to generate tags and improve search results.
#   * Think: What does it do? Why did you write it? What is the focus?
#   * Try to keep it short, snappy and to the point.
#   * Write the description between the DESC delimiters below.
#   * Finally, don't worry about the indent, CocoaPods strips it!

  s.description      = <<-DESC
iOS framework to build offline-first mobile apps. Create the models then have Mac handle syncing these models with your API.
                       DESC

  s.homepage         = 'https://github.com/curiosityio/Mac'
  # s.screenshots     = 'www.example.com/screenshots_1', 'www.example.com/screenshots_2'
  s.license          = { :type => 'MIT', :file => 'LICENSE' }
  s.author           = { 'Levi Bostian' => 'levi@curiosityio.com' }
  s.source           = { :git => 'https://github.com/curiosityio/Mac.git', :tag => s.version.to_s }
  # s.social_media_url = 'https://twitter.com/<TWITTER_USERNAME>'

  s.ios.deployment_target = '8.0'

  s.source_files = 'Mac/Classes/**/*'

  # s.resource_bundles = {
  #   'Mac' => ['Mac/Assets/*.png']
  # }

  # s.public_header_files = 'Pod/Classes/**/*.h'
  # s.frameworks = 'UIKit', 'MapKit'

  # Add all of these!!!
  s.dependency 'RxSwift', '~> 3.3.1'
  s.dependency 'RxRealm', '~> 0.5.2'
  s.dependency 'RealmSwift', '~> 2.5.0'
  s.dependency 'ObjectMapper', '~> 2.2.5'
  s.dependency 'Alamofire', '~> 4.4.0'
end
```

Then, go into the directory where your `Podfile` exists. In my case, it is the `Example` directory for your example project. Then, do a `pod install` and it will install all your dependencies needed for your project.

# Install local Cocoapods library in another project

Add this line to your `Podfile` for your project you want to install a library into:

```
pod 'Alamofire', :path => '~/Documents/Alamofire'
```

# Rename cocoapod

In order to rename your cocoapod library, you will need to perform a few actions.

* Rename your XCode project. I don't want to cover every step here on how to do this. But, it requires going into XCode and changing the project settings to renaming a project. This is not CocoaPods specific, but how you rename a project in XCode.

* Publish your new CocoaPod library to CocoaPods. Once you do the rename above, you can now publish your new CocoaPods library to CocoaPods. No special directions here. Do what you always do to release a new CocoaPods library.

* Deprecate your old CocoaPod. This is simple. All via command line.

Make sure you are logged into your CocoaPods library on your machine. The account that owns the pod you want to deprecate. Then, run the command:

```
pod trunk deprecate OldPodName --in-favor-of=NewPodName
```

Do *not* point `OldPodName` to a podspec file or anything. Just the name of the cocoapod itself. Nothing special here. You are specifying in plain English the name of your old pod and telling CocoaPods to redirect to the name of the new one.

# Create subspecs. Break cocoapod into modules for users to install individually.

Let's say you have a CocoaPod library that delivers some core piece of functionality. Then, you want to add functionality to this library for RxSwift support. Not everyone uses RxSwift so adding this dependency to your library (unless it's a core Rx library) would bloat the library for your users using it in their apps. It would be best to add that separate RxSwift functionality into a subspec so that your users can install if *if they choose*.

This is all done via the podspec file. Easy.

* In your .podfile for your project, you will add some text to make it look something like this:

```
Pod::Spec.new do |s|
  s.name             = 'Lucid'
  ...
  s.default_subspec = "Core"

  s.subspec "Core" do |ss|
    ss.source_files  = "Source/Core/**/*.swift"
    ss.dependency "Moya", '~> 8.0.5'
    ss.framework  = "Foundation"
  end

  s.subspec "RxSwift" do |ss|
    ss.source_files = "Source/RxSwift/**/*.swift"
    ss.dependency "Moya/RxSwift", '~> 8.0.5'
    ss.dependency "Lucid/Core"
  end
end
```

Above, we are creating 2 subspecs. 2 modules you could say. Users could install "Lucid/Core" (or simply, "Lucid" for short since we defined the `default_subspec` as "Core") at a minimum or they could install "Lucid/RxSwift" if they want to install the separate RxSwift code.

Let me break each part down:

```
  s.default_subspec = "Core"
```

This means that if someone simply tries to install `pod "Lucid"` into their app, it will install the "Core" subspec. We set a default for them to install. This is a good idea to have.

```
s.subspec "Core" do |ss|
  ss.source_files  = "Source/Core/**/*.swift"
  ss.dependency "Moya", '~> 8.0.5'
  ss.framework  = "Foundation"
end
```

The above code is saying a few things:

* Defining a subspec (aka: module) called "Core".
* Saying the source code for this subspec lives in "/Source/Core/" for the project. Make sure to include `/**/*.swift` if you want to include subdirectories inside of "/Source/Core/" directory!
* The "Core" subspec requires the `Moya` CocoaPods library version `8.0.5`.
* Include the Swift Foundation framework. So we have access to Swift iOS foundation.

```
s.subspec "RxSwift" do |ss|
  ss.source_files = "Source/RxSwift/**/*.swift"
  ss.dependency "Moya/RxSwift", '~> 8.0.5'
  ss.dependency "Lucid/Core"
end
```

The above code is saying a few things:

* Defining a subspec (aka: module) called "RxSwift".
* Saying the source code for this subspec lives in "/Source/RxSwift/" for the project. Make sure to include `/**/*.swift` if you want to include subdirectories inside of "/Source/RxSwift/" directory!
* The "RxSwift" subspec requires the `Moya/RxSwift` CocoaPods library version `8.0.5`.
* The "RxSwift" subspec requires the `Lucid/Core` CocoaPods library. This is important because it allows us to *add* functionality to Lucid. Not create a separate codebase here. If a user installs `Lucid/RxSwift` they will get the `Core` code along with the RxSwift extended functionality.
