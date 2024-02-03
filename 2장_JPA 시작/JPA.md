# 2장 JPA 시작

## 2.1 이클립스 설치와 프로젝트 불러오기

### 서론
  -  JPA를 사용하기 위한 기본 준비한다.
  -  이클립스 IDE 설치, H2 데이터베이스 설치, 라이브러리 설치한다.

### 이클립스 설치와 프로젝트 불러오기
  -  메이븐이 내장된 LUNA 버전의 이클립스 설치한다.
  -  Eclipse IDE for Java EE Developers 패키지 다운로드 한다. (JPA로 개발할 때 편리한 도구들 지원)
  >  메이븐(Maven)은 자바 기반 프로젝트의 라이프사이클 관리와 빌드 자동화를 지원하는 도구
 
  #### 예제 프로젝트 불러오기
  ![ConnectionMaker](./images/2.2.PNG)
  
  -  File > import.. 

  ![ConnectionMaker](./images/2.3.PNG)
  
  - Maven > Existing Maven Projects를 선택하고 Next 버튼을 누르면 메이븐 프로젝트를 불러올 수 있는 위 화면이 뜬다.
  - Browse..버튼을 눌러서 예제가 있는 경로 선택한다.

  ![ConnectionMaker](./images/2.4.PNG)
  
  -  경로를 선택하면 모든 프로젝트에 대한 /pom.xml이 나타난다.
  -  Finish 버튼을 선택해서 모든 예제 프로젝트을 불러온다.

  ![ConnectionMaker](./images/2.5.PNG)

  - 예제 프로젝트를 처음 불러오면 메이븐 저장소에 라이브러리를 내려받기 때문에 1~10분 정도 기다려야 한다.


  #### 메이븐 오류 해결
  ![ConnectionMaker](./images/2.6.PNG)
  
  - 메이븐 관련 오류가 발생하면 Maven > Update Project.. 선택한다.
  - 메이블 프로젝트의 설정 정보를 다시 업데이트할 수 있는 화면이 나온다.

  ![ConnectionMaker](./images/2.7.PNG)
  
  - 문제가 발생한 프로젝트 선택한 다음 Ok버튼을 누르면 메이븐 프로젝트를 초기화하고 다시 환경을 구성한다.
  - 문제가 해결되지 않으면 이 과정을 2~3번 정도 반복한다.
  - 만약 그래도 문제가 해결되지 않으면 Force Update of Snapshots/Release를 추가로 체크하고 Ok버튼을 누른다.

  
## 2.2 H2 데이터 베이스 설치

  ### 서론
  - MySQL이나 오라클 데이터베이스를 사용해도 되지만 설치하는 부담이 크다.
  - 설치가 필요없고 용량도 가벼눙 H2 데이터베이스를 사용한다.
  - H2 데이터베이스는 자바가 설치되어 있어야 동작한다.

    #### H2 데이터베이스 설치 방법
    - http://www.h2database.com에 들어가서 All Platforms 또는 Platform-Independent Zip을 내려받아서 압축을 푼다.
    - 압축을 푼 곳에서 bin/h2.sh를 실행하면 H2 데이터베이스를 서버 모드로 실행한다.
    - H2 데이터베이스는 JVM 메모리 안에서 실행되는 임베디드 모드와 실제 데이터베이스처럼 별도의 서버를 띄워서 동작하는 서버모드가 있다.
    ![ConnectionMaker](./images/2.8.PNG)
    - H2 데이터베이스를 서버 모드로 실행한 후에 웹 브라우저에서 http ://localhost:8082를 입력하면 H2 데이터베이스에 접속할 수 있는 화면이 뜬다.

    ■ 드라이버 클래스: org.h2.Driver
    ■ JDBC URL: jdbc：h2：tcp：//localhost/~/test
    ■ 사용자명 : sa
    ■  비밀번호: 입력하지 않는다

    ![ConnectionMaker](./images/2.9.PNG)

    - test라는 이름의 데이터베이스에 서버 모드로 접근할 수 있다.

    #### 예제 테이블 생성
    ```sql
    CREATE TABLE MEMBER (
      ID VARCHAR(255) NOT NULL, -- 아이디 (기본키) 
      NAME VARCHAR(255) , -- 이름
      AGE INTEGER NOT NULL, -- 나이
      PRIMARY KEY (ID)
    )
    ```
    
## 2.3 라이브러리와 프로젝트 구조
## 2.4 객체 매핑 시작
## 2.5 persistence.xml 설정
## 2.6 애플리케이션 개발
## 2.7 정리
