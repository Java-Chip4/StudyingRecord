# 모든 개발자를 위한 HTTP 웹 기본 지식
## 5. HTTP 메서드 활용
### 5.1 클라이언트에서 서버로 데이터 전송
- 데이터 전달 방식은 크게 두 가지
  - 쿼리 파라미터를 통한 데이터 전송
    - GET
    - 주로 정렬 필터(검색어)
  - 메시지 바디를 통한 데이터 전송
    - POST, PUT, PATCH
    - 회원 가입, 상품 주문, 리소스 등록, 리소스 변경


- 데이터 전송의 4가지 상황
  - **정적 데이터 조회**
    - 이미지, 정적 텍스트 문서
    - 조회는 GET 사용
    - 쿼리 파라미터 미사용. 리소스 경로로 단순하게 조회 가능
  - **동적 데이터 조회**
    - 주로 검색, 게시판 목록에서 정렬 필터
    - 조회는 GET 사용
    - 쿼리 파라미터 사용
    - 조회 조건을 줄여주는 필터, 조회 결과를 정렬하는 정렬 조건에 주로 사용
  - **HTML Form을 통한 데이터 전송**
    - 회원가입, 상품 주문, 데이터 변경
    - POST 전송 - 저장
      - Content-Type: application/x-www-form-urlencoded 사용
        - form의 내용을 메시지 바디를 통해서 전송(key=value, 쿼리 파리미터 형식)
        - 전송 데이터를 url encoding 처리
      - Content-Type: multipart/form-data
        - 파일 같은 binary 데이터를 전송할 때 주로 사용
        - 다른 종류의 여러 파일과 폼의 내용을 함께 전송할 수 있어서 multipart
      - HTML Form 전송은 POST, GET 지원
  - **HTTP API를 통한 데이터 전송**
    - 회원가입, 상품 주문, 데이터 변경
    - 서버 to 서버
      - 백엔드 시스템 통신
      - 앱 클라이언트
        - 아이폰, 안드로이드
    - 웹 클라이언트
      - HTML에서 Form 전송 대신 js를 통한 통신에 사용(ajax)
        - react, vueJs 같은 웹 클라이언트와 API 통신
      - POST, PUT, PATCH : 메시지 바디를 통해 데이터 전송
      - GET : 조회, 쿼리 파라미터로 데이터 전달
      - Content-Type: application/json을 주로 사용 (de facto)

### 5.2 HTTP API 설계 예시
- HTTP API - 컬렉션
  - POST 기반 등록
    - Ex) 회원 관리 API 제공

- HTTP API - 스토어
    - PUT 기반 등록
        - Ex) 정적 컨텐츠 관리, 원격 파일 관리

- HTML FORM 사용
  - 웹 페이지 회원 관리
  - GET, POST만 지원


---

### 회원 관리 시스템 - API 설계
#### POST 기반 등록 **\* 주로 사용**
- 회원 목록 : /members -> **GET**
- 회원 등록 : /members -> **POST**
- 회원 조회 : /members/{id} -> **GET**
- 회원 수정 : /members/{id} -> **PATCH, PUT, POST**
- 회원 삭제 : /members/{id} -> **DELETE**
  

- POST 기반 등록 특징
  - 클라이언트는 등록될 리소스의 URI를 모른다
    - 회원 등록 /members -> POST
    - POST /members
  - 서버가 새로 등록될 리소스 URI를 생성해준다
    ```
    HTTP/1.1 201 Created
    Location: /members/100
    ```
  - 컬렉션 (Collection)
    - 서버가 관리하는 리소스 디렉토리
    - 서버가 리소스의 URI를 생성하고 관리
    - 여기서 컬렉션은 /members


#### PUT 기반 등록
- 파일 목록 : /files -> **GET**
- 파일 조회 : /files/{filename} -> **GET**
- 파일 등록 : /files/{filename} -> **PUT**
- 파일 삭제 : /files/{filename} -> **DELETE**
- 파일 대량 등록 : /files -> **POST**

- PUT 기반 등록 특징
  - 클라이언트가 리소스 URI를 알고 있어야 한다
    - 파일 등록 /files/{filename} -> PUT
    - PUT /files/star.jpg
  - 클라이언트가 직접 리소스의 URI를 지정한다
  - 스토어 (Store)
    - 클라이언트가 관리하는 리소스 저장소
    - 클라이언트가 리소스의 URI를 알고 관리
    - 여기서 스토어는 /files


#### HTML Form 사용
- 회원 목록 : /members -> **GET**
- 회원 등록 폼 : /members/new -> **GET**
- 회원 등록 : /members/new, /members -> **POST**
- 회원 조회 : /members/{id} -> **GET**
- 회원 수정 폼 : /members/{id}/edit -> **GET**
- 회원 수정 : /members/{id}/edit, /members/{id} -> **POST**
- 회원 삭제 : /members/{id}/delete -> **POST**

- HTML Form 사용 특징
  - 컨트롤 URI의 사용이 불가피
    - GET, POST만 지원하므로 제약이 있음
    - 이를 해결하기 위해 동사로 된 리소스 경로 사용
    - POST의 /new, /edit, /delete가 컨트롤 URI
    - HTTP 메서드로 해결하기 애매한 경우 사용(HTTP API 포함)


---


#### 정리 : 참고하면 좋은 URI 설계 개념
- 문서 Document
  - 단일 개념
  - - ex) /members/100, files/star.jpg
- 컬렉션 Collection
  - 서버가 관리하는 리소스 디렉토리
  - 서버가 리소스의 URI를 생성하고 관리
  - ex) /members
- 스토어 Store
  - 클라이언트가 관리하는 자원 저장소
  - 클라이언트가 리소스 URI를 알고 관리
  - ex) /files
- 컨트롤러 Controller, 컨트롤 URI
  - 문서, 컬렉션, 스토어로 해결하기 어려운 추가 프로세스 실행
  - 동사를 직접 사용
  - ex) /members/{id}/delete
- REST Resource Naming Guide
  - https://restfulapi.net/resource-naming
- REST API에 관하여
  - https://www.youtube.com/watch?v=RP_f5dMoHFc

---

참고 : [김영한님 - 모든 개발자를 위한 HTTP 웹 기본 지식](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/)
