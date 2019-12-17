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
서버에서 유저에게 메일로 보낸 인증코드와 서버가 가지고 있는 인증코드가 서로 같은지 확인한다면, 해당 유저가 맞는지 아닌지 판별 할 수 있을거 같아요~<br>
세분화해서 생각해 본다면,
1. 유저에게 메일로 인증코드를 보낸다.
2. 보낸 메일코드를 서버는 저장한다.
3. 유저가 메일을 확인 한 후, 입력 폼에 코드를 입력해 제출<br>
-> (유저가 입력한 코드) = (서버에 저장된코드) 인지 확인!
4. 맞다면 인증 성공!(로그인 가능), 틀리다면 인증 실패(로그인 불가, 인증코드 다시 보내기)

이런식으로 생각해 볼 수 있겠네요 ㅎㅎ


<br><br>
## Backend 구현
<hr>


#### 유저에게 메일로 인증코드 보내기
 


