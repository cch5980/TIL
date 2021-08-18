# ReactJS-18-2 Redux-Saga



## 🔥1. redux-saga

- redux-saga는 좀 더 까다로운 상황에서 유용하다.
  - 기존 요청을 취소 처리해야 할 때(불필요한 중복 요청 방지)
  - 특정 액션이 발생했을 때 다른 액션을 발생시키거나, API 요청 등 리덕스와 관계없는 코드를 실행할 때
  - 웹소켓을 사용할 때
  - API 요청 실패 시 재요청해야 할 때



### 1-1) 제너레이터 함수

- ES6 문법
- 함수를 작성할 때 함수를 특정 구간에 멈춰 놓을 수 있고, 원할 때 다시 돌아가게 할 수 있다.
- 하나의 함수에서 값을 여러개 반환하는 것은 불가능하므로 아래와 같은 코드는 제대로 작동하지 않는다. 맨 위에 있는 값이 1이 반환된다.

```react
function weirdFunction() {
  return 1;
  return 2;
  return 3;
  return 4;
  return 5;
}
```

- 제너레이터 함수를 사용하면 함수에서 값을 순차적으로 반환할 수 있다.
- 제너레이터 함수를 만들 때는 `function*` 키워드를 사용한다.

```react
function* generatorFunction() {
  console.log('안녕하세요');
  yield 1;
  console.log('제너레이터 함수');
  yield 2;
  console.log('function*');
  yield 3;
  return 4;
}
```

- 제너레이터 함수를 호출했을 때 반환되는 객체를 **제너레이터** 라고 한다.

```react
const generator = generatorFunction();
```

![reactjs-18(2)-01](md-images/reactjs-18(2)-01.png)



- redux-saga는 제너레이터 함수 문법을 기반으로 비동기 작업을 관리해준다. 우리가 디스패치하는 액션을 모니터링해서 그에 따라 필요한 작업을 따로 수행한다.

```react
function* watchGenerator() {
  console.log('모니터링 중...');
  let prevAction = null;
  while(true) {
    const action = yield;
    console.log('이전 액션: ', prevAction);
    prevAction = action;
    if (action.type === 'HELLO') {
      console.log('안녕하세요');
    }
  }
}

const watch = watchGenerator();
```

![reactjs-18(2)-02](md-images/reactjs-18(2)-02.png)



## 🔥2. 비동기 카운터 만들기

```bash
$ yarn add redux-saga
```

- 카운터 리덕스 모듈

```react
// src/modules/counter.js
import { createAction, handleActions } from "redux-actions";
import { delay, put, takeEvery, takeLatest } from 'redux-saga/effects';

// 액션 타입 정의
const INCREASE = 'counter/INCREASE';
const DECREASE = 'counter/DECREASE';

const INCREASE_ASYNC = 'counter/INCREASE_ASYNC';
const DECREASE_ASYNC = 'counter/DECREASE_ASYNC';

// 액션 생성 함수
export const increase = createAction(INCREASE);
export const decrease = createAction(DECREASE);

// 마우스 클릭 이벤트가 payload 안에 들어가지 않도록 () => undefined를 두번째 파라미터로 넣어준다.
export const increaseAsync = createAction(INCREASE_ASYNC, () => undefined);
export const decreaseAsync = createAction(DECREASE_ASYNC, () => undefined);

function* increaseSaga() {
    yield delay(1000);  // 1초 기다린다.
    yield put(increase());  // 특정 액션을 디스패치한다.
}

function* decreaseSaga() {
    yield delay(1000);  // 1초 기다린다.
    yield put(decrease());  // 특정 액션을 디스패치한다.
}

export function* counterSaga() {
    // takeEvery는 들어오는 모든 액션에 대해 특정 작업을 처리해준다.
    yield takeEvery(INCREASE_ASYNC, increaseSaga);
    // takeLatest는 기존에 진행 중이던 작업이 있ㄷ면 취소 처리하고
    // 가장 마지막으로 실행된 작업만 실행한다.
    yield takeLatest(DECREASE_ASYNC, decreaseSaga);
}


// 초기값
const initialState = 0;

const counter = handleActions(
    {
        [INCREASE]: state => state + 1,
        [DECREASE]: state => state - 1,
    },
    initialState
);

export default counter;
```

- 루트 리듀서처럼 루트 사가를 만들어야 한다.

```react
// src/modules/index.js
import { combineReducers } from 'redux';
import { all } from 'redux-saga/effects';
import counter, { counterSaga } from './counter';

const rootReducer = combineReducers({
    counter
});

export function* rootSaga() {
    // all 함수는 여러 사가를 합쳐 주는 역할을 한다.
    yield all([counterSaga()]);
}

export default rootReducer;
```

- 스토어에 redux-saga 미들웨어 적용

- 리덕스 개발자 도구 설치

  - ```bash
    $ yarn add redux-devtools-extension
    ```

```react
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import { createStore, applyMiddleware } from 'redux';
import { Provider } from 'react-redux';
import createSagaMiddleware from 'redux-saga';
import rootReducer, { rootSaga } from './modules';
import { composeWithDevTools } from 'redux-devtools-extension';

const sagaMiddleware = createSagaMiddleware();
const store = createStore(
  rootReducer,
  composeWithDevTools(applyMiddleware(sagaMiddleware))
);
sagaMiddleware.run(rootSaga);

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
);
```

- 컴포넌트 코드와 컨테이너 코드는 따로 [git](https://github.com/cch5980/Learn-ReactJS/tree/main/redux-saga)에 첨부

![reactjs-18(2)-03](md-images/reactjs-18(2)-03.png)

- +1 버튼을 빠르게 두번 누르면 INCREASE_ASYNC 액션이 두 번 디스패치되고, 이에 따라 INCREASE 액션도 두 번 디스패치된다.
  - `takeEvery`를 사용하여 increaseSaga를 등록했으므로 디스패치되는 모든 INCREASE_ASYNC 액션에 대해 1초 후 INCREASE 액션을 발생시켜 준다.
- -1 버튼을 빠르게 두번 누르면 DECREASE_ASYNC 액션이 두 번 디스패치되었음에도 DECREASE 액션은 단 한번 디스패치되었습니다.
  - `takeLatest`를 사용했기 때문에 여러 액션이 중첩되어 디스패치되었을 때 기존의 것들은 무시하고 가장 마지막 액션만 처리한다.



## 🔥3. API 요청 상태 관리하기

- API를 호출해야 하는 상황에는 사가 내부에서 직접 호출하지 않고 `call` 함수를 사용한다.
  - 첫번째 인수는 호출하고 싶은 함수이고, 그 뒤에 오는 인수들은 해당 함수에 넣어 주고 싶은 인수이다.

```react
// src/modules/sample.js
import { createAction, handleActions } from 'redux-actions';
import * as api from '../lib/api';
import { call, put, takeLatest } from 'redux-saga/effects';
import { startLoading, finishLoading } from './loading';

// 액션 타입을 선언
const GET_POST = 'sample/GET_POST';
const GET_POST_SUCCESS = 'sample/GET_POST_SUCCESS';
const GET_POST_FAILURE = 'sample/GET_POST_FAILURE';

const GET_USERS = 'sample/GET_USERS';
const GET_USERS_SUCCESS = 'sample/GET_USERS_SUCCESS';
const GET_USERS_FAILURE = 'sample/GET_USERS_FAILURE';

export const getPost = createAction(GET_POST, id => id);
export const getUsers = createAction(GET_USERS);

function* getPostSaga(action) {
    yield put(startLoading(GET_POST));  // 로딩 시작
    // 파라미터로 action을 받아오면 액션의 정보를 조회할 수 있다.
    try {
        // call을 사용하면 Promise를 반환하는 함수를 호출하고, 기다릴 수 있다.
        // 첫 번째 파라미터는 함수, 나머지 파라미터는 해당 함수에 넣을 인수이다.
        const post = yield call(api.getPost, action.payload);   // api.getPost(action.pay-load)를 의미
        yield put({
            type: GET_POST_SUCCESS,
            payload: post.data
        });
    } catch (e) {
        // try/catch 문을 사용하여 에러를 잡는다.
        yield put({
            type: GET_POST_FAILURE,
            payload: e,
            error: true
        });
    }
    yield put(finishLoading(GET_POST)); // 로딩 완료
}

function* getUsersSaga(action) {
    yield put(startLoading(GET_USERS));  // 로딩 시작
    try {
        const users = yield call(api.getUsers);
        yield put({
            type: GET_USERS_SUCCESS,
            payload: users.data
        });
    } catch (e) {
        yield put({
            type: GET_USERS_FAILURE,
            payload: e,
            error: true
        });
    }
    yield put(finishLoading(GET_USERS)); // 로딩 완료
}

export function* sampleSaga() {
    yield takeLatest(GET_POST, getPostSaga);
    yield takeLatest(GET_USERS, getUsersSaga);
}

// 초기 상태 선언
// 요청의 로딩 중 상태는 loading 이라는 객체에서 관리
const initialState = {
    post: null,
    users: null
}

const sample = handleActions({
    [GET_POST_SUCCESS]: (state, action) => ({
        ...state,
        post: action.payload
    }),
    [GET_USERS_SUCCESS]: (state, action) => ({
        ...state,
        users: action.payload
    }),
}, initialState);

export default sample;
```

- 루트 사가에 등록한다

```react
// src/modules/index.js
import { combineReducers } from 'redux';
import { all } from 'redux-saga/effects';
import counter, { counterSaga } from './counter';
import sample, { sampleSaga } from './sample'; 
import loading from './loading';

const rootReducer = combineReducers({
    counter,
    sample,
    loading,
});

export function* rootSaga() {
    // all 함수는 여러 사가를 합쳐 주는 역할을 한다.
    yield all([counterSaga(), sampleSaga()]);
}

export default rootReducer;
```

![reactjs-18(2)-04](md-images/reactjs-18(2)-04.png)



## 🔥4. 리팩토링

```react
// src/lib/createRequestSaga.js
import { call, put } from 'redux-saga/effects';
import { startLoading, finishLoading } from '../modules/loading';

export default function createRequestSaga(type, request) {
    const SUCCESS = `${type}_SUCCESS`;
    const FAILURE = `${type}_FAILURE`;

    return function* (action) {
        yield put(startLoading(type));  // 로딩 시작
        try {
            const response = yield call(request, action.payload);
            yield put({
                type: SUCCESS,
                payload: response.data
            });
        } catch (e) {
            yield put({
                type: FAILURE,
                payload: e,
                error: true
            });
        }
        yield put(finishLoading(type)); // 로딩 끝
    }
}
```

```react

// src/modules/sample.js
import createRequestSaga from '../lib/createRequestSaga';
(...)

export function* sampleSaga() {
    yield takeLatest(GET_POST, getPostSaga);
    yield takeLatest(GET_USERS, getUsersSaga);
}

(...)
export default sample;
```



## 🔥5. 추가적인 유용한 기능

- `select`: 사가 내부에서 현재 상태를 참조한다.

```react
// src/modules/counter.js
import { select } from 'redux-saga/effects';

(...)
function* increaseSaga() {
    yield delay(1000);  // 1초 기다린다.
    yield put(increase());  // 특정 액션을 디스패치한다.
    const number = yield select(state => state.counter);   // state는 스토어 상태를 의미
    console.log(`현재 값은 ${number}입니다.`)
}

(...)
```

![reactjs-18(2)-05](md-images/reactjs-18(2)-05.png)



- `throttle`: 사가가 실행되는 주기를 제한하는 방법
  - 사가가 n초에 단 한번만 호출되도록 설정할 수 있다.

```react
import { throttle } from 'redux-saga/effects';

export function* counterSaga() {
    yield throttle(3000, INCREASE_ASYNC, increaseSaga);
    ...
}
```

