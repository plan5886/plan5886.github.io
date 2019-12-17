---
layout: post
title:  "[nodejs] nodejs로 회원가입 메일인증 시스템 만들기(2)"
date:   2019-12-15
excerpt: "nodejs를 이용하여 회원가입 시, 인증 메일을 보내는 시스템을 구현해 보겠습니다~"
tag: [nodejs, nodemailer, smtp, nodemailer-smtp-transport, nodejs naver, nodejs smtp, nodejs 네이버, nodemailer 네이버 ]
comments: true
---

# nodejs 로 회원가입 메일인증 시스템 만들기(2)

### 읽기전에...
이 글은 nodejs express의 기본 지식을 어느정도 갖추신 분들을 위한 글입니다.<br> nodejs가 처음이시라면,
[egoing님의 nodejs강의](https://opentutorials.org/course/3332/21028)
혹은 [w3schools의 nodejs강의](https://www.w3schools.com/nodejs/default.asp) 를 추천드립니다.<br>
(vuejs와 함께 사용하시는 분들은 [이 강의](https://medium.com/hivelab-dev/vue-express-mysql-part1-98f68408d444)도 추천드립니다.)

### 참고사이트
https://nodemailer.com/about/ (nodemailer 공식사이트) <br>
https://miryang.dev/2019/04/25/nodejs-page-3/ (mac 환경에서 nodejs와 mongoDB를 이용하여 시스템 구축)
=> 저는 mongoDB가 아닌 windows10 환경에서 mysql과 vuejs를 함께 이용해서 구축하였습니다.

<br><br>
## Frontend의 상태
<hr>


