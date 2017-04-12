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
