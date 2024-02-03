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
  ### 서론
  - 필요한 모든 라이브러리를 직접 내려받아서 관리하기는 어렵다.
  - 메이븐은 중앙저장소에서 의존성을 다운로드하고 빌드 결과물을 배포하는 등의 작업을 자동화하여 개발자들의 프로젝트의 복잡성을 간소화할 수 있게 한다.(+)
  - 이 프로젝트에서도 메이븐이라는 도구를 사용한다.
  - 이클립스 LUNA버전에는 메이븐이 내장되어있다.
  - JPA 구현체로 하이버네이트를 사용하기 위한 핵심 드라이버가 있다.
    - hibernate-core : 하이버네이트 라이브러리
    - hibernate-entitymanager : 하이버네이트가 JPA 구현체로 동작하도록 JPA 표준을 구현한 라이브러리
    - hibernate-jpa-2.1-api : JPA 2.1 표준 API를 모아둔 라이브러리
   ![ConnectionMaker](./images/st.PNG)
  - 프로젝트 구조는 위와 같다. 
  
  ### 2.3.1 메이븐과 사용 라이브러리 관리
  - 메이븐은 간단히 이야기하면 라이브러리를 관리하는 도구다.
  - pom.xml에 사용할 라이브러리를 적어두면 라이브러리를 자동으로 내려받아서 관리해준다.
  - 최근 자바 애플리케이션은 메이븐 같은 도구를 사용해서 라이브러리를 관리하고 빌드한다.
  - 자바 애플리케이션을 개발하려면 jar 파일로 된 여러 라이브러리가 필요한데 과거에는 이런 라이브러리를 직접 내려받아 사용했다.
  - 메이븐은 사용할 라이브러리 이름과 버전만 명시하면 라이브러리를 자동으로 내려받고 관리해준다.(라이브러리 관리기능)
  - 메이븐은 애플리케이션을 빌드하는 표준화된 방법을 제공한다.(빌드 기능)

  ```xml
  <?xml version="1.0" encoding=nUTF-8M?>
  <project xmlns=?,http: //maven. apache. org/POM/4.0.0” 
  xmlns:xsi=nhttp://www.w3.org/2001/XMLSchema-instanceM
  xsi: schemaLocation=,fhttp: / /maven. apache. org/POM/4.0.0 http: / /maven. 
  apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</m delVers on〉
    <groupId>jpabook</groupId>
    <artifactld>jpa-start</artifactld>
    <version〉l .0-NAPSHOT</version>

    <dependencies>
      <!-- JPA, 하이버네이트 一 〉
      〈dependency〉
      <groupId>org.hibernate</groupId> 
      <artifactld>hibernate-entitymanager</artifactld>
      <version>4.3.10.Final</version>
      </dependency〉
      <!— H2 데이터베이스 — 〉
      〈dependency〉
      <groupId>com.h2database</groupId>
      <artifactld>h2</artifactld>
      <version>l.4.187</version>
      </dependency〉
    </dependencies>
  </project>
  ```
- <dependencies>에 사용할 라이브러리를 지정한다.
- groupId + artifactId + version만 적어주면 라이브러리를 메이븐 공식 저장소에서 내려받아 라이브러리에 추가해준다.
- JPA에 하이버네이트 구현체를 사용하려면 많은 라이브러리가 필요하지만 핵심 라이브러리는 2가지다.
  - 
## 2.4 객체 매핑 시작
## 2.5 persistence.xml 설정
## 2.6 애플리케이션 개발
## 2.7 정리
