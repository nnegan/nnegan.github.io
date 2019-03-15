---
layout: post
title:  "2019-03-12-express+sequelize"
date:   2019-03-12 14:43:45
author: negan k
categories: Nodejs
tags: nodjs dev
---

## 시퀄라이저의 특징

1.DB Entity정의를 시퀄라이저의 model(js파일)로 정의할수 있다.

2.DB의 Entity의 관계역시 시퀄라이저에서 정의할수 있다.(ORM)

3.Express에서 시퀄라이저의 Model을 이용하여 DB의 entity에 접근 하여 CRUD등을 할 수 있다.

4.Express 서버에서 시퀄라이저에 선언된 Entity정의와 그 관계정의(테이블간 fk등…)을 이용하여 서버에 테이블과 그 관계를 생성할수 있다.
--------------

**1. Express 프로젝트 생성**

본인은 이클립스에서 express 프로젝트 생성 후 StartExploer에서 쉘로 실행함.


npm을 통해 Express generator를 먼저 설치합니다. (global로 추가하기 위해 -g 옵션을 사용합니다.)
~~~
npm install express-generator -g
~~~

Express generator를 사용해서 프로젝트를 생성합니다. 
~~~
express TestSeq
~~~

폴더를 이동하여 모듈 설치
~~~
cd TestSeq
npm install
~~~

생성된 프로젝트 구조
~~~
.
├── app.js
├── bin
│   └── www
├── package.json
├── public
│   ├── images
│   ├── javascripts
│   └── stylesheets
│       └── style.css
├── routes
│   ├── index.js
│   └── users.js
└── views
    ├── error.jade
    ├── index.jade
    └── layout.jade
~~~

**2. sequelize install**

경로 이동 후 npm 추가 설치
~~~
npm i sequelize mysql2
npm i -g sequelize-cli
sequelize init
~~~

자동으로 필요한 파일들이 생성된다.

**3. DB 환경 설정**

config/config.json 파일은 시퀄라이저가 DB에 접근하는 정보를 저장한다
~~~json
{
  "development": {
    "username": "hanumoka",
    "password": "password",
    "database": "band_of_coder",
    "host": "127.0.0.1",
    "dialect": "mysql",
    "operatorsAliases": false
  },
  "test": {
    "username": "root",
    "password": null,
    "database": "database_test",
    "host": "127.0.0.1",
    "dialect": "mysql"
  },
  "production": {
    "username": "root",
    "password": null,
    "database": "database_production",
    "host": "127.0.0.1",
    "dialect": "mysql"
  }
}
~~~

**4. DB 테이블을 자동으로 models폴더에 js파일로 생성**

1) 테이블 단위로 js 생성
~~~
npm install -g sequelize-auto
npm install -g mysql
~~~

~~~
sequelize-auto -o "./models" -d db명 -h localhost -u 유져명 -p 3306 -x 비밀번호 -e mysql
~~~

2) js파일 수정

생성된 파일 제일 아래에 timestamps false 선언 (자동으로 생성일 수정일의 타임스탬프가 따라다녀서 express에서 생성단 테이블이 아니면 에러가 발생한다.)

~~~js
...
  }, {
    tableName: 'de_tb_emp',
    timestamps : false
  });
...
~~~  

**5. 만들어 보기**

1) 라우팅 경로 추가

app.js
~~~js
.
.;
app.use('/commcode', commcodeRouter);
.
.;
var commcodeRouter = require('./routes/commcode');
~~~

2) 라우터 경로 밑에 실제 사용

commcode.js
~~~js
var express = require('express');
var url = require('url'); //고전적인 url 방식을 쓰기 위해 선언
var router = express.Router();
var codeModel = require('../models').de_tb_code;//  db_tb_code 테이블을 선언

//==> http://127.0.0.1:3000/commoncode 
router.get('/', function(req, res, next) {
  try{
    const results = codeModel.findAll();
    res.json(results);
  } catch(error) {
    console.error(error);
    next(error);
  }
});

//==> http://127.0.0.1:3000/commoncode/코드
router.get('/:mstCode', function(req, res, next) {	
	
	const mstCode = req.params.mstCode;	
	console.log(`mst_code => "${mstCode}"`);	
	codeModel.findAll({
		  where: {
		    mst_code: `${mstCode}`
		  }
		}).then(results => {
	    res.json(results);
	}).catch(err => {
    console.error(err);
  });
});

//==> http://127.0.0.1:3000/commoncode/get?mstCode=코드
// 위에 url을 선언했으므로 사용 가능함.
router.get('/get', function(req, res, next) {
	var parseObj = url.parse(req.url, true);
	var mstCode = parseObj.query.mst_code;
	codeModel.findAll({
				  where: {
				    mst_code: `${mstCode}`
				  }
				}).then(results => {
	    res.json(results);
	}).catch(err => {
     console.error(err);
  });
});

module.exports = router;
~~~

**6. 나아가기**

app.js에 라우터 경로 추가 후 작성

manager.js
~~~js
var express = require('express');
var url = require('url');
var router = express.Router();
var empModel = require('../models').de_tb_emp;
var loginModel = require('../models').de_tb_login;
var codeModel = require('../models').de_tb_code;

/* Join */
// Select 쿼리 ORM기능을 이용하여 쿼리문 작성없이 조인 쿼리 생성
router.get('/', function(req, res, next) {

	empModel.hasMany(loginModel, {foreignKey: 'emp_no', as: 'loginModel' }); 
	
	empModel.find({
	    where: {emp_no: '123'}
	,   include: {model: loginModel, as: 'loginModel', attributes: ['emp_no', 'emp_nm']}
	}).then(function(result) {
	    res.json(result);
	}).catch(function(err){
	     //TODO: error handling
		console.error(err);
	});
});

/* 아래로는 ORM을 배제한 쿼리 */

// Select 쿼리
router.get('/query', function(req, res, next) {
	
	codeModel.sequelize.query('SELECT mst_code, mst_code_nm from de_tb_code limit 1').then(function(result) {
	    res.json(result);
	}).catch(function(err){
	     //TODO: error handling
		console.error(err);
	});
});

// 변수 설정하여 쿼리
// replacements 
router.get('/dquery', function(req, res, next) {
	
	codeModel.sequelize.query('SELECT mst_code, mst_code_nm from de_tb_code where mst_code=:mst_code limit 1', {replacements :{mst_code:'M0400'}} )
		.then(function(result) {
	    res.json(result);
	}).catch(function(err){
	     //TODO: error handling
		console.error(err);
	});
});

// 조인문 쿼리
router.get('/join_query', function(req, res, next) {
	
	empModel.sequelize.query(' SELECT emp.emp_no, emp.emp_nm, login.emp_login_ip FROM de_tb_emp AS emp LEFT JOIN de_tb_login AS login ON emp.emp_no = login.emp_no limit 1')
		.then(function(result) { 
	    res.json(result);
	}).catch(function(err){
	     //TODO: error handling
		console.error(err);
	});
});

module.exports = router;
~~~
> 참고사이트
1) http://webframeworks.kr/tutorials/expressjs/expressjs_orm_three/ 
2) https://jongmin92.github.io/2017/05/17/Emily/3-make-express-project/
3) https://m.blog.naver.com/hyoun1202/220670652183

> 예제 소스

1. https://github.com/jhkim98/nodejs_seqtest