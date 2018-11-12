---
layout: post
title: "[MAC] AdoptOpenJdk 설치 및 Brew 활용법"
date:   2018-06-26 09:00:00 +0900
categories: MAC 
---

## Brew
~~~
brew update
brew upgrade
brew install <program>

brew tab <Another Repository>
brew cask install google-chrome // "To Install, drag thie icon.." No more
brew cask info google-chrome
~~~

## AdoptOpenJdk 설치 (BREW)
~~~
brew tap AdoptOpenJDK/openjdk
brew cask install <version>
brew cask info adoptopnejdk9 // Local에 설치된 Location 정보를 획득한다.

> Versions
OpenJDK8 - adoptopenjdk8
OpenJDK9 - adoptopenjdk9
~~~

## AdoptOpenJdk 설치 (tar.gz 파일)
- [홈페이지에서 파일 다운로드](https://adoptopenjdk.net/installation.html?variant=openjdk11&jvmVariant=hotspot#x64_mac-jdk)
- Extract the .tar.gz. You can use the following command:
    - tar -xf OpenJDK11U-jdk_x64_mac_hotspot_11.0.1_13.tar.gz
- Add this version of Java to your PATH:
    - export PATH=$PWD/jdk-11.0.1+13/Contents/Home/bin:$PATH
- Check that Java has installed correctly:
    - java -version

### Reference
 - [brew를 통한 AdoptOpenJdk 설치](https://github.com/AdoptOpenJDK/homebrew-openjdk)
 