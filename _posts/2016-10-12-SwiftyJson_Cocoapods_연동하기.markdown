---
layout: post
title: "xcode8 + swift3 + cocoapods"
date:   2016-10-12 10:00:00 +0900
categories: swift  
---

### cocoapods install And Project setting
#### Anywhere
~~~
sudo gem install cocoapods
pod setup
~~~

#### Project Location
~~~
cd project_location
pod init (created PodFile ...)
~~~

#### edit PodFile
~~~
vim Podfile
~~~

#### add library (sample)
~~~
 Uncomment this line to define a global platform for your project
# platform :ios, '9.0'

target 'FizzBuzz' do
  # Comment this line if you're not using Swift and don't want to use dynamic frameworks
  use_frameworks!

  # Pods for FizzBuzz
  pod 'SwiftyJSON', :git => 'https://github.com/SwiftyJSON/SwiftyJSON.git'

  target 'FizzBuzzTests' do
    inherit! :search_paths
    # Pods for testing
  end

  target 'FizzBuzzUITests' do
    inherit! :search_paths
    # Pods for testing
  end

  post_install do |installer|
  	installer.pods_project.targets.each do |target|
    	target.build_configurations.each do |config|
      		config.build_settings['SWIFT_VERSION'] = '3.0'
    	end
  	end
  end
end
~~~


