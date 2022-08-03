Start date : 7/21
Due date : 7/28

### 요구사항

클라이언트에서 ner 프로젝트를 생성할 수 있어야 한다 (프로젝트 생성 기능이 없음) - API 1
클라이언트에서 다른 프로젝트의 설정 불러오기 기능 (그 클라이언트의 태스크로 제한을 둬야함) - API 2

프로젝트를 만들었으면, 엑셀 파일로 데이터 업로드
엑셀파일을 읽어서 각 줄마다 하나의 work 생성(ner) - API 3

데이터 프로세스는 총 3단계로 진행된다.
1단계. 그냥 단순 ner 작업 (고유명사, 단순명사 …) -> 작업 후 -> 엑셀 파일로 만든다.
1-1. 작업한 파일을 추출 (이미 만들어져있음 데이터 추출 기능 이용)
1-2. 파일을 읽어 Post Processing 후 잘 되면 Saas로 구축 (Saas로 저장하는 방법 알아보기)
1-3. json -> 엑셀로 만든다
1-4. 중요한 점은 파일을 추출하고, post processing을 하고, 엑셀로 만드는 과정이 한번에 이루어져야 한다.
2단계. 1번 엑셀을 업로드 하고 영문 혹은 베트남어로 번역한다 -> 엑셀로 만든다 (1번과 작업 동일)
3단계. Ner 작업한 거 + 번역된 문장까지 보여주고 수정할 수 있게 최종 검수를 한다.. -> 엑셀 x , json 포스트 프로세싱을 한다
3-1. 검수된 엑셀 파일을 읽어서 json 포스트 프로세싱을 한다.

### 특이점

클라이언트가 직접 태스크를 생성할 수 있어야 하고, 설정할 수 있어야 한다. 로컬에서 post processing을 돌리는 것이 아니라 SaaS를 이용한다. 1, 2번 결과 제이슨을 하나의 엑셀 파일로 만들어야 한다.

### API

1. 클라이언트에서 subtask 생성 기능 추가
2. 어떤 API 사용하고 있는지 확인 후 client명에 따라서 보여주는 화면 다르게 설정
3. Work 생성하는 API
4. 1단계 : 엑셀 다운로드(데이터 추출 후 가공하여 엑셀에 저장) : 작업된 파일들을 다운받아(데이터 추출) 파일을 읽고 제이슨을 추출해서 클라이언트가 처음에 서브태스크에 올린 엑셀 파일을 받아 작업자가 작업한 결과 데이터를 옆에 추가로 작성한다.
5. 2단계 : 4번에서 다운받은 엑셀로 또 작업을 한 데이터를 추출해서.. 2번 단계와 동일
6. 마지막으로 2단계에서 다운받은 엑셀을 (검수 과정을 거친 뒤) 포스트 프로세싱을 한다.

### 현재 기능

#### API 1번 : 미니게이트용 서브태스크 생성 API

```ts
Body {
    companyId : 21,
    엑셀 파일,
    단계 : 1 || 2 || 3
}
```

추가 고려할 점 : client에서 서브태스크 생성하는 기능이 아직 없기 때문에, 클라이언트가 생성한 서브태스크 또한 홈페이지에서 보이지 않고 있다. (개발해야 되는지 원래 있는지 파악)

#### API 2번 : Minigate용 설정 만들기

설정 가져오기에서 가져오는 회사의 subtaskId (Request URL: https://api-dev.thedatahunt.com/admin-api/v1/subtasks/408)에 GET 요청을 하여 설정을 가져온다. 이때 json, 라벨 등 설정이 등록되기 때문에 minigate용 태스크를 하나 만들어놓고 그걸 가져오게 해야겠다.개발되어 있는데 단순히 프론트에서 연동을 안시켜놓은듯

#### API 3번

엑셀 파일일 때 work 생성하게 API 개발

1단계 서브태스크 1
2단계 서브태스크 2 (1단계 엑셀 완료파일 불러오기)
3단계 서브태스크 3

1 : 엑셀 업로드(단계로 분기하여 json 파싱) -> work 생성 -> 작업 완료 -> 엑셀 다운로드 (처음에 보낸 엑셀을 같이 전달)
2 : 1단계 엑셀 업로드(단계로 분기) -> work 생성 -> 작업 완료 -> 엑셀 다운로드
3 : 2단계 엑셀 업로드 -> 검수 -> json 파싱

엑셀 업로드(파일 선택) 하는 api에서 단계를 알아볼 수 있는 파라미터를 추가한다.
미니게이트 컴패니에서 생성된 SUBTASK만 파일 선택하는 api 따로 만들기 client

엑셀 파일 업로드하기를 누르면 실행되는 api

POST https://api-dev.thedatahunt.com/admin-api/v1/subtasks/metafile
PUT https://api-dev.thedatahunt.com/admin-api/v1/subtasks/412
GET https://api-dev.thedatahunt.com/admin-api/v1/subtasks/412

후에 데이터 탭을 누르면 실행되는 api
GET https://api-dev.thedatahunt.com/admin-api/v1/subtasks/426
GET https://api-dev.thedatahunt.com/admin-api/v1/works/stats?subtaskId=426
GET https://api-dev.thedatahunt.com/admin-api/v1/works?subtaskId=426&status=created&limit=100&offset=0

데이터 요청을 누르면 실행되는 api
PUT https://api-dev.thedatahunt.com/admin-api/v1/subtasks/414/result
GET https://api-dev.thedatahunt.com/admin-api/v1/subtasks/414/result

완료!! 설정 가져오기
GET https://api-dev.thedatahunt.com/admin-api/v1/subtasks?active=true&companyId=21&offset=0&limit=500

완료!! 설정에서 불러오는 컴패니 목록
GET https://api-dev.thedatahunt.com/admin-api/v1/companies?offset=0&limit=100

-   3단계에서는
    1단계 2단계 작업을 work로 써야하는데,
    태깅까지 되어있는 상태로 보여줘야 한다 거기서 수정작업이 이루어질 수 있도록 해야함 ..

7/25 해야될 일
완료!! 엑셀 값 읽어서 Work 생성
작업 후 json 값 추출 받아서 엑셀 write
작업 파일 다운받는 API -> ner은 처음이라 잘 모르겠음 어ㅏ떻게될지.. 이상없으면 기존 api 이용

현재 지금...
admin-dev서버에서 작업자 할당이 안됨 ㅠ
admin 에서 work NER 생성하는 거
POST https://api.thedatahunt.com/admin-api/v1/works
POST https://api-dev.thedatahunt.com/admin-api/v1/works/files

7/26
<TODO>

1. 1단계 작업 excel 쓰기 (1단계 작업 충분히 저장 -> excel 쓰기)
   1-1. 1단계 작업
   1-2. excel 쓰기 (추가 작업)
   1-3. 2단계에 excel 업로드
   1-4. 2단계 작업
   1-5. 2단계 작업 excel 업로드 (추가 작업)
2. 3단계 Work 만들기 (검수할 수 있게)
3. 3단계

Q. 만약, 전문용어가 없어서 태깅이 안됐을 경우 엑셀에서는 Null || 빈 컬럼 뭘로 보여줘야 하는지?
Q. 보라색으로 색칠된, 특수라벨링 검수 및 수정은 뭔지.. 값을 써줘야 하는가 ?
Q. 데이터헌트와 협의라고 되어있는 객체 키명 마음대로 만들어서 통보(?)해도 되는지 ?
Q. 번역 작업할 때 한 문장씩 라벨을 쳐야하는지..? 아니면 전체문장을 번역하는 건지?

엑셀쓰는 로직
엑셀과 work 리절트를 하나씩 읽는다
