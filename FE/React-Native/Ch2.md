# 2. 리액트 네이티브 기본 다지기

## 1. 리액트 네이티브 프레임워크의 작동 원리

---

> `Hello world`를 화면에 출력해보며 리액트 네이티브 앱의 동작 원리를 알아보자

### 프로젝트 만들기

- 리액트 네이티브 프로젝트 ch02_1 생성
  `npx react-native init ch02_1 --template react-native-template-typescript`
- Trouble Shooting
  ![](images/2-1.png)
  시작부터 오류 발생. template을 찾을 수 없댄다.
  [Couldn't find template.config.js file inside "react-native" template. Please make sure the template is valid · Issue #1637 · react-native-community/cli](https://github.com/react-native-community/cli/issues/1637)
  `npx react-native init ch02_1 --template react-native-template-typescript --npm`
  ⇒ 뒤에 `--npm`을 붙이니 되었다.
  이번엔 cocoapods가 설치되지 않는 오류 발생
  ![](images/2-2.png)

**[ 엄청 오래 걸렸지만 그래도 성공 ]**

![](images/2-3.png)

### 프로젝트 실행하기

- npm start
- npm run android
  - Trouble Shooting
    ![](images/2-4.png)
    왜 너만 이러니!
    **경로 문제라니까 경로를 다시 설정해주자**
    - 터미널에 입력
      → `export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk-11.0.16.jdk/Contents/Home`
- npm run ios

![](images/2-5.png)

### App.tsx 파일

```tsx
import React from 'react';
import { Text } from 'react-native';

export default function App() {
	// console.log('App called');
	const textElement = React.createElement(Text, null, 'Hello world!');
	return textElement;
}
```

![](images/2-6.png)

⇒ Hello World가 뜨는 것을 볼 수 있다!

<aside>
💡 **정적 HTML과 동적 HTML**

</aside>

- 정적 HTML : 웹 서버에서 HTML 문서를 생성하여 웹 브라우저로 전송하는 방식
- 동적 HTML : 웹 브라우저에서 자바스크립트 코드를 실행하여 동적으로 HTML을 생성하는 방식

⇒ React와 같은 프론트엔드 프레임워크는 동적 HTML 방식이다.

### DOM과 렌더링

- DOM 객체
  : DOM 클래스의 인스턴스
- DOM (트리) 구조
  : 무수히 많은 DOM 객체들이 부모/자식 형태의 나무 구조를 이루는 것.
- 렌더링
  : 웹 브라우저가 HTML을 파싱하여 자바스크립트 DOM 구조로 만드는 것

**[ 물리 DOM과 가상 DOM ]**

- 리액트 프레임워크에서 사용하는 개념
- 물리 DOM 구조
  : 웹 브라우저에서 자바스크립트 코드가 생성하는 실제 DOM 구조
- 가상 DOM 구조
  : 리액트 코드가 생성한 자바스크립트 객체 구조

⇒ 리액트는 특정 시점에서 가상 DOM 구조를 물리 DOM 구조로 바꾼다. : `리액트가 렌더링한다.`

→ 이를 수행하는 패키지 : `렌더러`

<aside>
💡 **리액트 프레임워크와 리액트 네이티브 프레임워크의 차이**

</aside>

- 리액트 : 가상 DOM 구조를 react-dom이란 렌더러(DOM 렌더러) 패키지를 사용
- 리액트 네이티브 : react-native 렌더러(네이티브 렌더러) 패키지를 사용

**[ react 패키지의 역할 ]**

- react 패키지 : App.tsx 파일을 가상 DOM 구조로 만드는 역할
- 네이티브 렌더러 : 리액트 요소를 안드로이드 프레임워크, iOS용 UIKit 프레임워크의 화면 UI 객체로 바꿔줌.

**[ 브리지 방식 렌더링 ]**

- 리액트에서는 React.render라는 DOM 렌더러의 동작을 코드로 확인할 수 있지만, 리액트 네이티브는 네이티브 렌더러의 모습을 확인할 수 **없다.**
  - **렌더링이 android와 ios 디렉토리에 있는 자바나 오브젝티브-C로 구현한 네이티브 모듈 쪽에서 되기 때문이다.**
- 네이티브 모듈
  - JavaScriptCore 자바스크립트 엔진이 동작
    ⇒ 안드로이드에서는 JNI 방식, iOS에서는 FFI 방식으로 연결되어 동작한다.
    - JNI : Java Native Interface
    - FFI : Foreign Function Interface
- 리액트 네이티브를 앱에서 실행하면, 네이티브 모듈 + 스레드 2개가 동시에 동작
  - 네이티브(안드, iOS 렌더링)를 담당하는 **UI 스레드**와 App.tsx와 같은 자바스크립트 코드를 실행하는 **자바스크립트 엔진 스레드**
  - 두 스레드는 메시지 큐 방식으로 서로 렌더링과 관련된 데이터를 주고 받는다.
  - 사용자가 화면을 터치 → UI 스레드가 자바스크립트 스레드에 이벤트 형식으로 전달
    ⇒ \*\*\*\*`브리지 방식 프레임워크`
- 네이티브 동작 부분 설치 : `npx react-native link`, `npx pod-install`

<aside>
💡 **엔진과 라이브러리 차이**

</aside>

- 일반적으로 같은 개념이나, 보통 코드 분량이 방대한 라이브러리를 엔진이라고 부른다.

<aside>
💡 **스레드란?**

</aside>

- 자바스크립트 엔진 : 단일 스레드
- 리액트 네이티브 앱 : 다중 스레드

**[ React.createElement API가 하는 일 ]**

document.createElement는 물리 DOM 객체를 생성하나, React.createElement는 가상 DOM 객체 생성

- 리액트 네이티브 렌더러는 네이티브에서 동작하므로 가상 DOM 객체를 네이티브로 넘겨주는 방식이다.
- react-native-cli는 네이티브 모듈에서 동작하는 자바스크립트 엔진이 가상 DOM 객체를 넘겨주는 App의 존재를 알 수 있도록 다음과 같이 index.js 파일을 만든다.

  ```jsx
  /**
   * @format
   */

  import { AppRegistry } from 'react-native';
  import App from './App';
  import { name as appName } from './app.json';

  AppRegistry.registerComponent(appName, () => App);
  ```

- react-native-cli는 네이티브 모듈에서 동작하는 자바스크립트 엔진이 이 파일의 존재를 알 수 있도록 다음과 같은 자바 파일(MainApplication.java) 또한 만든다.
  ```java
  @Override
  protected String getJSMainModuleName() {
    return "index";
  }
  ```
- 리액트 네이티브 앱 실행
  → [MainApplication.java](http://MainApplication.java) 실행
  → 자바스크립트 스레드에서 실행되는 자바스크립트 엔진이 getJSMainModuleName로 index.js 확인
  → index.js를 통해 App 확인
  → App을 호출해 얻은 가상 DOM 객체를 브리지를 통해 네이티브로 전달
  → ‘Hello world’ 출력

### 안드로이드 네이티브 모듈과 npm run adroid 명령과의 관계

`npm run android` : 안드로이드 앱 빌드 명령 실행

- android 디렉토리만을 대상으로 자바 언어로 작성한 네이티브 앱(andoid 폴더) 부분을 빌드하여 에뮬레이터에 앱을 설치한다.

`npm start` : 설치된 앱이 처음 구동될 때 앱이 수신해야 할 ES5 JS 코드로 컴파일된 TS 코드 번들을 제공하는 메트로 서버를 실행하는 명령어

- android/app 디렉토리의 build.gradle 파일 내용을 참조하여 실행 파일을 만든다.(빌드한다.)

### 아이폰 네이티브 모듈과 npx pod-install 명령

- ios 디렉토리의 Podfile : node_modeuls 디렉토리의 native_modules를 설치한다.

`npx pod-install` : ios 디렉토리의 Podfile 실행

### 리액트 네이티브 개발 사용 명령어

| 명령            | 의미                                                                                                                                                       |
| --------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| npm i           | package.json 파일에 명시된 패키지를 node_modules 디렉토리에 설치. 설치한 패키지는 android나 ios 디렉토리의 네이티브 모듈에서 동작하는 파일을 가질 수 있다. |
| npx pod-install | pod 프로그램을 설치하여 원격 저장소에 필요한 패키지를 내려받아 로컬 컴퓨터에 설치. (안드로이드 빌드 도구인 그래들은 내려받는 기능이 탑재되어 필요없다.)    |
| npm start       | 메트로 서버 실행                                                                                                                                           |
| npm run android | 안드로이드 스튜디오의 빌드 명령을 실행하고 빌드한 앱을 에뮬레이터에 설치 및 실행                                                                           |
| npm run iod     | Xcode 빌드 명령을 실행하고 빌드한 앱을 시뮬레이터에 설치 및 실행                                                                                           |

### 프로젝트 초기화 명령어

| 명령 | 의미 |
| ---- | ---- |

| cd android
./gradlew clean | ./gradlew installDebug 명령으로 생성된 임시 파일 삭제.
원격 저장소에서 내려받은 패키지도 함께 삭제 |
| rm -r -force .gradle(윈도우)
rm -r .gradle(맥) | 빌드한 앱 삭제 |
| cd ios
xcodebuild clean | Xcode가 생성한 임시파일 삭제 |
| pod deintegrate | npx pod-install 명령으로 내려받은 패키지 삭제 |

### 코드 디버깅

⇒ `console.log`를 통해 디버깅을 실행한다.
