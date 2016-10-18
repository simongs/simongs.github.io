---
layout: post
title: "Alamofire With Xcode8 + Swift 3 + SwiftyJSON"
date:   2016-10-01 17:45:49 +0900
categories: swift 
---

## BASIC

### What is Alamofire
~~~
iOS와 OS X에서 사용하기 위한 Swift 기반의 네트워킹 라이브러리입니다.
~~~

### Base on
~~~
NSURLSession 이나 NSURLConnection 에 대한 이해가 있으면 좋다.
해당 기본 라이브러리를 Wrapping한 상위 라이브러리이다.

CocoaPods는 필수적으로 설치가 되어 있어야 한다.
~~~

### CocoaPods
 - [CocoaPods link](https://cocoapods.org)
 
~~~
CocoaPods is a dependency manager for Swift and Objective-C Cocoa projects. 
It has over 23 thousand libraries and is used in over 1.2 million apps.
CocoaPods can help you scale your projects elegantly.
~~~

## Using on XCode

### Alamofire 설치 방법

#### 1. Git Submodule
참고 : [세련된 HTTP 프레임워크 Alamofire](https://outofbedlam.github.io/swift/2016/02/04/Alamofire/)

~~~
$ cd AlamofireApp/
$ git submodule add https://github.com/Alamofire/Alamofire.git
Cloning into 'Alamofire'...
remote: Counting objects: 3449, done.
remote: Total 3449 (delta 0), reused 0 (delta 0), pack-reused 3449
Receiving objects: 100% (3449/3449), 1.30 MiB | 31.00 KiB/s, done.
Resolving deltas: 100% (2124/2124), done.
Checking connectivity... done.
~~~

#### 2. CocoaPods
~~~
sudo gem install cocoaPods
pod setup

pod init
생성된 Pod File 수정
pod install
~~~

#### 2.1. (CocoaPods) swift 3.0 을 사용하기 위한 방법

 - Build Option 을 3.0으로 세팅
 
~~~
  post_install do |installer|
  	installer.pods_project.targets.each do |target|
    	target.build_configurations.each do |config|
      		config.build_settings['SWIFT_VERSION'] = '3.0'
    	end
  	end
  end
end
~~~
 
 - 위의 설정 없는 경우는 Xcode의 옵션을 조정

~~~
 	pod install
 	workspace open
 	LATER, LATER 선택 (swift Version 2.3 -> 3.0 호환 관련)
 	(in Alamofire Build Option) Use Legacy Swift Laguage Version -> NO 선택
~~~
 
 <figure class="video_container">
  <iframe width="560" height="315" src="https://www.youtube.com/embed/FjsxG07haJI" frameborder="0" allowfullscreen></iframe>
</figure>

## JSON Parser

### SwiftyJSON

~~~
  # Pods for FizzBuzz
  pod 'SwiftyJSON', :git => 'https://github.com/SwiftyJSON/SwiftyJSON.git'
~~~

## Problem Solve

#### 1. 999 Error
~~~
[Error Message]
Error Domain=NSURLErrorDomain Code=-999 "cancelled" 
UserInfo=0x78fc41e0 {
	NSErrorFailingURLKey=myURL, 
	NSErrorFailingURLStringKey=myURL, 
	NSLocalizedDescription=cancelled
}
~~~


응답이 오기 전에 호출한 sessionManager객체의 instanc가 사라지는 현상. 별도의 HttpClient를 클래스를 생성한다.
(약식 HttpClient입니다.)

~~~ swift
import Foundation
import Alamofire


class HttpClient {
    static let sharedInstance = HttpClient()
    
    private var manager : SessionManager?
    
    func getInstance()->SessionManager{
        if let m = self.manager{
            return m
        }else{
            var defaultHeaders = SessionManager.defaultHTTPHeaders
            defaultHeaders["X-Naver-Client-Id"] = "value"
            defaultHeaders["X-Naver-Client-Secret"] = "value"
            
            let configuration = URLSessionConfiguration.default
            configuration.httpAdditionalHeaders = defaultHeaders
            configuration.timeoutIntervalForRequest = 40configuration.timeoutIntervalForResource = 40

            let tempmanager = Alamofire.SessionManager(configuration: configuration)
            
            self.manager = tempmanager
            return self.manager!
        }
    }
}
~~~


#### 2. Testcase 만들기

 - waitExpectations 를 활용하여 응답을 받을 수 있도록 한다.
 - 테스트케이스의 method명은 반드시 "test"로 시작해야 한다.

~~~ swift
func testExample() {
        
        let ex = expectation(description: "Expecting a JSON data not nil")
        
        Alamofire.request("https://httpbin.org/get").responseJSON { response in
            print(response.request)  // original URL request
            print(response.response) // HTTP URL response
            debugPrint(response.data)     // server data
            debugPrint(response.result)   // result of response serialization
            
            ex.fulfill()
            
            if let JSON = response.result.value {
                debugPrint("JSON: \(JSON)")
            }
        }
        
        waitForExpectations(timeout: 10) { (error) in
            if let error = error {
                XCTFail("error: \(error)")
            }
        }
    }

~~~

## Reference
 - [Alamofire Github](https://github.com/Alamofire/Alamofire)
 - [Raywenderlich - iOS/Swift Alamofire 사용하기](http://rhammer.tistory.com/115)
 - [Alamofire 라이브러리 연결](https://outofbedlam.github.io/swift/2016/02/04/Alamofire/)
 - [Alamofire 테스트 케이스](http://stackoverflow.com/questions/39894064/how-to-write-unit-test-for-alamofire-request-function)
 - [swift 에서 URLRequest, JSON 파싱 (REST API 클라이언트 개발)](http://mtsparrow.blogspot.kr/2016/04/swift-urlrequest-json-rest-api.html)
- [cocoapods 이용하기](http://mtsparrow.blogspot.kr/2016/03/cocoapod.html)
- [Alamofire JSON 응답결과 -> Object 로 변환](https://github.com/tristanhimmelman/AlamofireObjectMapper)
- [JSON library comparison in Swift](
http://yannickloriot.com/2016/02/json-library-comparison-in-swift/)
