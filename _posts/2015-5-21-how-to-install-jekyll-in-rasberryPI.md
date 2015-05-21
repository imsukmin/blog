---
layout: post
title: Jekyll을 라즈베리파이에 설치해 보자
tag : [jekyll, rasberryPI]
category : [blog]
---

이리도 쉬운것을 잘 찾아보지도 않고 안되는 것만 생각한 제가 바보같네요;;

구성은 rasberry pi의 [공식 사이트](https://www.raspberrypi.org/documentation/linux/software/ruby.md)를 기초로 합니다. 

(처음 봤을때는 지킬을 까는 것처럼 보여지더니 알고보니 ruby gem 사용법이군요;)

P.S 역시 stack overflow는 짱입니다. [링크](http://stackoverflow.com/questions/10725767/error-installing-jekyll-native-extension-build)

총 3단계를 거칩니다.

    sudo apt-get install ruby1.9.1
    sudo apt-get install ruby1.9.1-dev
    sudo gem install jekyll

참 쉽죠? 단.. 라즈베리파이 문제인지 ruby가 무거운 것인지 모르겠지만..

jekyll을 설치하는데 5분 이상 기다려야 했던 것 같습니다.

인내심을 가지고 설치하셔야 합니다.