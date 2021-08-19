# ReactJS-22 mongoose를 이용한 MongoDB 연동



## 🔥1. MongoDB

- 서버를 개발할 때 데이터베이스를 사용하면 웹 서비스에서 사용되는 데이터를 저장하고, 효율적으로 조회하거나 수정할 수 있다.
- 기존에는 MySQL, OracleDB, PostgreSQL 같은 RDBMS(관계형 데이터베이스)를 자주 사용했다.
- 그러나 관계형 데이터베이스의 한계가 존재한다.
  1. 데이터 스키마가 고정적이다.
     - 스키마: 데이터베이스에 어떤 형식의 데이터를 넣을지에 대한 정보를 가리킨다.
     - 새로 등록하는 데이터 형식이 기존에 있던 데이터들과 다르면, 기존 데이터를 모두 수정해야 새 데이터를 등록할 수 있다.
     - 데이터 양이 많을 때는 데이터베이스의 스키마를 변경하는 작업이 매우 번거롭다.
  2. 확장성
     - RDMS는 저장하고 처리해야 할 데이터양이 늘어나면 여러 컴퓨터에 분산시키는 것이 아니라, 해당 데이터베이스 서버의 성능을 업그레이드하는 방식으로 확장해 주어야 한다.
- MongoDB는 이러한 한계를 극복한 문서 지향적 NoSQL 데이터베이스이다.
  - 데이터베이스에 등록하는 데이터들은 유동적인 스키마를 지닐 수 있다.
  - 여러 컴퓨터로 분산하여 처리할 수 있도록 확장하기 쉽게 설계되어 있다.
- 상황별로 적합한 데이터베이스를 선택하는 것이 중요하다.
  - 데이터의 구조가 자주 바뀐다면 MongoDB가 유리하다.
  - 까다로운 조건으로 데이터를 필터링해야 하거나, ACID 특성을 지켜야 한다면 RDBMS가 유리하다.
  - (ACID 특성은 원자성(Atomicity), 일관성(Consistency), 고립성(Isolation), 지속성(Durability)의 앞 글자를 떠서 만든 용어이고, 데이터베이스 트랜잭션이 안전하게 처리되는 것을 보장하기 위한 성질을 의미한다.)

### 1-1) 문서

- 여기서 말하는 '문서(document)'는 RDBMS의 레코드(record)와 개념이 비슷하다.

- 문서의 데이터 구조는 한 개 이상의 키-값 쌍으로 되어 있다.

  - ```json
    // 문서 예시
    {
      "_id": ObejctId("5023951200ab23400fe034"),
      "username": "chichi",
      "name": { first: "C.H.", last: "Choi" }
    }
    ```

- 문서는 BSON(바이너리 형태의 JSON) 형태로 저장된다. 그렇기 때문에 나중에 JSON 형태의 객체를 데이터베이스에 저장할 때, 큰 공수를 들이지 않고 데이터베이스에 등록할 수 있다.

- 새로운 문서를 만들면 _id라는 고유값을 자동으로 생성하는데, 이 값은 시간, 머신 아이디, 프로세스 아이디, 순차 번호로 되어 있어 값의 고유함을 보장한다.

- 여러 문서가 들어있는 곳을 컬렉션 이라고 한다.

  - 기존 RDBMS에서는 테이블 개념을 사용하므로 각 테이블마다 같은 스키마를 가지고 있어야 한다.

  - 새로 등록해야 할 데이터가 다른 스키마를 가지고 있다면, 기존 데이터들의 스키마도 모두 바꾸어 주어야 한다.

  - 반면 MongoDB는 다른 스키마를 가지고 있는 문서들이 한 컬렉션에서 공존할 수 있다.

    - ```json
      {
        "_id": ObejctId("5023951200ab23400fe034"),
        "username": "chichi",
        "name": { first: "C.H.", last: "Choi" }
      },
      {
        "_id": ObejctId("5023951200ab23400fe035"),
        "username": "chichi2",
        "phone": "010-1234-1234"
      }
      ```



### 1-2) MongoDB 구조

- 서버 하나에 데이터베이스를 여러 개 가지고 있을 수 있다.
- 각 데이터베이스에는 여러 개의 컬렉션이 있다.
- 컬렉션 내부에는 문서들이 있다.

![reactjs-22-01](md-images/reactjs-22-01.png)



### 1-3) 스키마 디자인

- MongoDB에서 스키마를 디자인하는 방식은 기존 RDBMS에서 스키마를 디자인하는 방식과 완전히 다르다.
- 기존의 RDBMS로 디자인한다면 다음과 같다.

![reactjs-22-02](md-images/reactjs-22-02.png)

- NoSQL에서는 그냥 모든 것을 문서 하나에 넣는다.

```json
{
  id: ObjectId,
  title: String,
	body: String,
  username: String,
  createdDate: Date,
  comments: [
    {
      _id: ObejctId,
      text: String,
      createdDate: Date
    }
  ]
}
```



## 🔥2. MongoDB 서버 준비

### 2-1) 설치

```bash
$ brew tap mongodb/brew
$ brew install mongodb-community@4.2
$ brew services start mongodb-community@4.2
```

### 2-2) MongoDB 작동 확인

```bash
$ mongo
MongoDB shell version v4.2.15
connecting to: mongodb://127.0.0.1:27017/?compressors=disabled&gssapiServiceName=mongodb
Implicit session: session { "id" : UUID("beaccfbd-b0b9-496a-b3a6-e9d0705adb33") }
MongoDB server version: 4.2.15
...
```

- `Mongo Not Found` 가 표시될 경우 mongo가 설치된 path를 참조할 수 있게 설정해준다.([참고](https://stackoverflow.com/questions/22862808/mongod-command-not-found-os-x))

```bash
> version()
4.2.15
```



## 🔥3. mongoose의 설치 및 적용

- mongoose는 Node.js 환경에서 사용하는 MongoDB 기반 ODM(Object Data Modelling) 라이브러이다.
- 데이터베이스 문서들을 자바스크립트 객체처럼 사용할 수 있게 해준다.
- 백엔프 프로젝트에 이어서 진행함으로 프로젝트 디렉터리에서 다음 명령어를 입력한다.

```bash
$ yarn add mongoose dotenv
```

- dotenv는 환경변수들을 파일에 넣고 사용할 수 있게 하는 개발도구이다.

- mongoose를 사용하여 MongoDB에 접속할 때, 서버에 주소나 계정 및 비밀번호가 필요하다. 이렇게 민감하고 환경별로 달라질 수 있는 값은 코드 안에 작성하지 않고, 환경변수로 설정하는 것이 좋다.

  

### 3-1) .env 환경변수 파일 생성

- 프로젝트 루트 경로에 .env 파일을 만든다.

```text
# .env
PORT=4000
MONGO_URI=mongodb://localhost:27017/blog
```

- 여기서 blog는 우리가 사용할 데이터베이스 이름이다. 지정한 데이터베이스가 서버에 없다면 자동으로 만들어준다.

```javascript
// src/index.js
require('dotenv').config();
(...)

// 비구조화 할당을 통해 process.env 내부 값에 대한 레퍼런스 만들기
const { PORT } = process.env;

(...)

// PORT가 지정되어 있지 않다면 4000을 사용
const port = PORT || 4000;
app.listen(port, () => {
  console.log('Listening to port %d', port);
});
```

- .env 파일에서 PORT를 4001로 변경한 뒤 서버를 재시작하면 바뀐 포트로 실행된 것을 볼 수 있다.

![reactjs-22-03](md-images/reactjs-22-03.png)



### 3-2) mongoose로 서버와 데이터베이스 연결

```javascript
(...)
const mongoose = require('mongoose');

// 비구조화 할당을 통해 process.env 내부 값에 대한 레퍼런스 만들기
const { PORT, MONGO_URI } = process.env;

mongoose
  .connect(MONGO_URI, { useNewUrlParser: true, useFindAndModify: false })
  .then(() => {
    console.log('Connected to MongoDB');
  })
  .catch((e) => {
    console.error(e);
  });

(...)
```



## 4. esm으로 ES 모듈 import/export 문법 사용

- 리액트 프로젝트에서 사용해 오던 ES 모듈 import/export 문법은 Node.js에서 아직 정식 지원되지 않는다.
- 깔끔한 코드를 위해 사용할 수 있도록 설정한다.

```bash
$ yarn add esm
```

- 기존 src/index.js 파일의 이름을 main.js로 변경하고, index.js 파일을 새로 생성해서 다음과 같이 작성한다.

```javascript
// src/index.js
// 이 파일에서만 no-global-assign ESLint 옵션을 비활성화한다.
/* eslint-disable no-global-assign */

require = require('esm')(module /*, options*/);
module.exports = require('./main.js');
```

- package.json 파일에서 스크립트도 수정해준다.

```json
// package.json
{
  ...,
  "scripts": {
    "start": "node -r esm src",
    "start:dev": "nodemon --watch src/ -r esm src/index.js"
  }
}
```

- ESLint에서 import/export 구문을 사용해도 오류로 간주하지 않도록 다음과 같이 .eslintrc.json 을 수정한다.

```json
// .eslintrc.json
{
  ...,
  "parserOptions": {
    "ecmaVersion": 12,
    "sourceType": "module"
  },
  ...,
}
```

- 그리고 현재까지 작성한 코드 파일들을 import/export 구문으로 수정한다.

