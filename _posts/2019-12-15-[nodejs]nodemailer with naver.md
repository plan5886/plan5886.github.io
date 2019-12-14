---
layout: post
title:  "[nodejs] nodejs로 회원가입 인증메일 시스템 만들기(1)"
date:   2019-12-15
excerpt: "nodejs를 이용하여 회원가입 시, 인증 메일을 보내는 시스템을 구현해 보겠습니다~"
tag: [nodejs, nodemailer, smtp, nodemailer-smtp-transport, nodejs naver ]
comments: true
---

# nodejs 로 회원가입 인증메일 시스템 만들기(1)

### 읽기전에...
이 글은 nodejs express의 기본 지식을 어느정도 갖추신 분들을 위한 글입니다. nodejs가 처음이시라면,<br>
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

![alt text](https://plan5886.github.io/assets/img/2019-12-15/img_form.png "회원가입 폼")

**[vuejs] frontend codes**
```javascript
// some codes

this.$http.post('/api/SignUp', { // 회원가입 버튼 클릭시 호출
  what: this.input_what,         // 핸드폰번호 혹은 이메일주소
  type: typeWhat,                // 핸드폰번호인지, 이메일주소인지 확인하기 위한 변수
  username: this.input_name,
  userid: this.input_id,
  userpw: this.input_pw
});

// some codes
```

제 프론트엔드의 상태가 궁금하신 분들을 위해 준비해보았어요~
<br><br><br>
## Backend 구현
<hr>
모든 기능을 구현하기에 앞서, 메일을 간단하게 보내보겠습니다.
먼저, 이메일을 보내줄 모듈(handle_email)을 구현해 보겠습니다.

* npm install nodemailer
* npm install nodemailer-smtp-transport

**[nodejs] handle_email.js**
```javascript
var nodemailer = require('nodemailer')
var smtpTransporter = require('nodemailer-smtp-transport')

var smtpTransport = nodemailer.createTransport(smtpTransporter({
    service: 'Naver',
    host:'smtp.naver.com',
    auth: {
        user: 'userid@naver.com',     //보내는 분의 메일계정
        pass: 'password'
    }
}));

module.exports =  {
    EmailVerification (email) {

        var mailOption = {
            from: 'userid@naver.com', // 보내는 분의 메일계정
            to: email,                // 받는 분의 메일계정 (여러 개 가능)
            subject: "Hello",         
            text: "Hello world?",
            html: "<b>Hello world?</b>" 
        }

        smtpTransport.sendMail(mailOption, (err, res) => { // 메일을 보내는 코드
            if(err){
                console.log(err)
                throw err
            }
            console.log("mail sent!")
        });
    }
}
```

모듈 구현을 마쳤으니, 간단하게 호출로 이메일을 보내봅시다~

**[nodejs] SignUp.js**
```javascript
var handle_email = require('../my_module/handle_email');
router.post('/', function(req, res, next) {                   
  handle_email.EmailVerification('some Email')
});
```
<br><br>
짜잔~ 이메일이 잘 보내졌나요?

아직 잘 보내지지 않을거 같은데요 ㅎㅎ

그 이유는 **계정 보안**의 문제로, 해당 사이트에서 외부 앱으로부터의 **접근을 제한**하고 있기 때문이에요~

구글의 gmail의 경우, [구글계정설정](https://myaccount.google.com/lesssecureapps) 로 접속하셔서 **사용안함 -> 사용** 으로 꼭!! 바꾸어주셔야 합니다

네이버의 경우, [네이버 계정 smtp설정](https://mail.naver.com/option/imap) 로 접속하셔서 **IMAP/SMTP 사용**에서 사용으로 체크해주시면 됩니다~

계정 설정 후, 다시 실행해보시면, 'mail sent!'라는 기분좋은 문구를 콘솔창에서 보실 수 있어요~
받는 메일 계정에도 꼭 들어가서 확인해보세요 ㅎㅎ

메일 보내는 건 기분좋게 마무리하게 되었네요 ㅎ

다음 포스팅에서는 인증에 관해서 다루어보도록 하겠습니다

쾌딩(유쾌한 코딩)하십셔 ^^7





