### Why?

현재 작업 단가를 테이블 안 json 컬럼에서 다루고 있기 때문에, subtask와 테이블에 unit price 컬럼을 생성한다. 또 단가 변경 추적의 용이를 위해서 price history 테이블을 하나 만들고 subtask와 관계를 맺어준다.

1. unit price 컬럼 추가
2. 테이블 생성 및 관계 설정
3. 배포

### [Migration](https://orkhan.gitbook.io/typeorm/docs/migrations#how-migrations-work)

model에 변화가 생기면 모델 변경 사항을 데이터베이스에 동기화해야 하는데, `synchronize: true`를 사용하기엔 안전하지 않다. 따라서 마이그레이션을 이용한다. 마이그레이션은 데이터베이스 스키마를 업데이트하고 기존 데이터베이스에 새로운 변경 사항을 적용하기 위한 SQL 쿼리가 포함된 단일 파일이다. Entity의 변경없이 데이터베이스를 사용하다가 중간에 컬럼명을 바꿔야 된다고 해보자.

SQL query를 사용해 새로운 마이그레이션을 만들 수 있다.
`ALTER TABLE "post" ALTER COLUMN "title" RENAME TO "name";`

이 SQL 쿼리를 실행하면 데이터베이스 스키마가 새 코드베이스와 함께 작동할 준비가 된다. TypeORM은 이러한 SQL 쿼리를 작성하고 필요할 때 실행할 수 있는 장소를 제공하는데, 바로 마이그레이션이다.

### 마이그레이션 만들기

create a new migration using CLI:
`typeorm migration:create -n PostRefactoring`

`PostRefactoring`은 마이그레이션의 이름이며 원하는대로 정할 수 있다. CLI를 실행하면 마이그레이션 폴더 안에 새 파일 `{TIMESTAMP}-PostRefactoring.ts`이 생성된다. 이제 파일을 열어 마이그레이션 SQL query를 실행할 수 있다.

```ts
import { MigrationInterface, QueryRunner } from "typeorm";

export class PostRefactoringTIMESTAMP implements MigrationInterface {
    async up(queryRunner: QueryRunner): Promise<void> {}

    async down(queryRunner: QueryRunner): Promise<void> {}
}
```

`up`은 마이그레이션을 수행하길 원하는 코드를 포함하고, `down`은 `up`의 변경 사항을 되돌릴 수 있다. \n
`up`, `down` 둘 다 내부에 QueryRunner 개체가 있다. 모든 데이터베이스 작업은 이 개체를 사용하여 실행된다.
