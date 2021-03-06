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
