---
layout: post
title:  "[nodejs] nodejs로 회원가입 메일인증 시스템 만들기(2)"
date:   2019-12-15
excerpt: "저번 시간에 메일을 보내본 것에 이어, 인증에 관한 부분을 다루어보도록 하겠습니다~"
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
## 인증은 어떻게?
<hr>

[이전 시간](https://plan5886.github.io//nodejs-nodemailer-with-naver/)에는 간단하게 메일을 보내는 방법을 알아보았습니다 ㅎ <br>
이번 시간에는 **인증**에 관한 이야기를 해보겠습니다!! 그럼, 인증은 어떤 방식으로 이루어져야 할까요?
다른 여러 사이트에서 이메일을 통한 인증으로 가입을 해보신 경험 다들 있으실텐데요. frontend의 경우, 아시다시피 그다지 복잡하지 않은 형태로 구현가능합니다.
제 경우에는 
<br> 
 ![alt text](https://plan5886.github.io/assets/img/2019-12-17/img_frontend_verify_input.png "인증 상자")
<br>
 위의 그림과 같이 간단하게 구현했습니다.
 


