---
layout: post
title:  "[nodejs] nodejs로 회원가입 메일인증 시스템 만들기(2)"
date:   2019-12-15
excerpt: "저번 시간에 메일을 보내본 것에 이어, 인증에 관한 부분을 다루어보도록 하겠습니다~"
tag: [nodejs, nodemailer, nodemailer-smtp-transport, nodejs naver, nodejs smtp, nodejs 네이버, nodemailer 네이버, nodejs 메일인증, nodejs 인증메일 ]
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


### 유저에게 메일로 인증코드 보내기
먼저 보낼 인증코드를 만들어 보겠습니다.

**[nodejs]SignUp.js**

```javascript
//---추가된 부분--- 
var crypto = require('crypto'); //내장 모듈이라 따로 설치 안해주셔도 됩니다!!
var key_one=crypto.randomBytes(256).toString('hex').substr(100, 5);
var key_two=crypto.randomBytes(256).toString('base64').substr(50, 5);
var key_for_verify=key_one+key_two; //우리가 사용할 인증코드
//------

var handle_email = require('../my_module/handle_email');
router.post('/', function(req, res, next) {                   
  handle_email.EmailVerification('some Email', key_for_verify) // 이전코드에서 수정된부분! 인증코드를 함께 넘김
});
```
 짜잔~ 이렇게하면 저희가 사용할 인증코드 완성! 저도 자세한 코드는 잘 모르는데요... Ctrl+C,V라는 최고의 기술을 통해 만들어 냈습니다 ㅎㅎ
 랜덤으로 5자리씩 2가지의 키를 만들어 합쳐 10자리 1개의 키를 만들고 있습니다.
 코드의 자세한 내용을 알고싶으신 분들은 [이 링크](https://miryang.dev/2019/04/25/nodejs-page-3/)를 통해 공부하시면 되요!
 
 이제 메일과 함께 보내볼 텐데요, 이전 시간에 작성하신 코드를 쪼오끔 수정하시면 금방 보낼 수 있습니다~
 
 **[nodejs]handle_email.js**
 ```javascript
var nodemailer = require('nodemailer')
var smtpTransporter = require('nodemailer-smtp-transport')

var smtpTransport = nodemailer.createTransport(smtpTransporter({
    service: 'Naver',
    host:'smtp.naver.com',
    auth: {
        user: 'userid@naver.com',    
        pass: 'password'
    }
}));

module.exports =  {
    EmailVerification (email, key) { // key 추가

        var mailOption = {
            from: '"name"<userid@naver.com>', 
            to: email,               
            subject: "Hello",         
            // text: "Hello world?",
            html: "<b>verification code: </b>" + key // key 추가
        }

        smtpTransport.sendMail(mailOption, (err, res) => {
            if(err){
                console.log(err)
                throw err
            }
            console.log("mail sent!")
        });
    }
}
```

### 보낸 인증코드 서버에 저장하기
서버의 mysql DB에 저장해보도록 하겠습니다!

**[nodejs]SignUp.js**

```javascript
var crypto = require('crypto'); 
var key_one=crypto.randomBytes(256).toString('hex').substr(100, 5);
var key_two=crypto.randomBytes(256).toString('base64').substr(50, 5);
var key_for_verify=key_one+key_two;

//---추가된 부분---
var mysql = require('mysql'); 
var consql = mysql.createConnection({
    host: 'localhost',
    port: '3306',
    user: 'root',
    password: 'password',
    database: 'DBname'
});
//------

var handle_email = require('../my_module/handle_email');
router.post('/', function(req, res, next) {
  var userinfo = req.body                   
  handle_email.EmailVerification('some Email', key_for_verify)
  userinfo['key_for_verify'] = key_for_verify //userinfo에 key_for_verify 속성 추가
  var sql = 'insert into users set ?'
  consql.query(sql, userinfo, function (err, result) {
      if(err){ //mysql 에러 처리
         console.error(err)
         throw err
      }
      console.log('userinfo insert complete!')
      res.end()
  })
});
```

서버에 저장까지 완료!! 정상적으로 코드가 작동했다면, 콘솔창에 userinfo insert complete라는 문구를 보실 수 있어요!!

### (유저가 입력한 코드) = (서버에 저장된 코드) 인지 확인!
간단한 frontend 입력 폼을 준비해 보았습니다~<br>
![alt text](https://plan5886.github.io/assets/img/2019-12-17/img_front_verify_input.png "회원가입 폼")

이러한 폼에 코드를 입력 후 확인 버튼을 누르면, frontend를 통해 서버에 인증 요청을 합니다.

**[vuejs]frontend.js**
```javascript
this.$http.post('/api/SignUp/verify', {  //   /api/SignUp/verify에 요청함! 
        key_for_verify: this.input_verify_code,
        userid: this.userid
})
```

**[nodejs]SignUp.js**
```javascript

//위의 SignUp.js 코드 아래에 추가하시면 됩니다

router.post('/verify', function(req, res, next) {
    //userid로 해당 인증코드를 찾는다(sql)
    var sql = "select key_for_verify from users where userid = " + mysql.escape(req.body.userid)
    consql.query(sql, (err, result) => {
        if(err){
            console.error(err)
            throw err
        }
        // 서버 DB에 저장된 인증코드와 frontend에서 보내온 코드가 일치하는지 확인!
        if(result[0].key_for_verify == req.body.key_for_verify) {
            //해당 유저에게 로그인 권한을 준다(sql2)
            var sql2 = "update users set permission = 2 where userid = " + mysql.escape(req.body.userid)
            consql.query(sql2, (err, result) => {
                if(err){
                    console.error(err)
                    throw err
                }
                console.log('user verified')
                res.end()
            })
        }
    })
})
```

그다지 많은 내용은 아니었지만, 코드 때문에 포스팅 길이가 많이 길어졌네요 ㅎㅎ

지금까지 긴 글 따라 읽어 주신분들 감사드립니다~

**[nodejs] nodejs로 회원가입 메일인증 시스템 만들기** 마치도록 하겠습니다 ㅎ
 
다음부터는 다른 포스팅으로 찾아뵙도록 하겠습니다~

쾌딩하십셔 ^^7 
