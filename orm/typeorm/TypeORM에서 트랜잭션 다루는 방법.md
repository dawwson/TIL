# TypeORM에서 트랜잭션 다루는 방법

- TypeORM 0.3 이상 버전을 기준으로 트랜잭션을 다루는 방법을 정리한다.

<br>

## Transaction

- 트랜잭션은 데이터베이스에서 한꺼번에 수행되어야 하는 작업 단위를 말한다.

- 트랜잭션의 중요한 특징 중 하나는 트랜잭션이 데이터베이스에 모두 반영되든지, 아니면 전혀 반영되지 않아야 하며 작업이 부분적으로 실행되거나 중단되지 않는 것을 보장하는 것이다.

<br>

## DataSource & EntityManager & QueryRunner

- TypeORM에서는 `DataSource & EntityManager` 또는 `QueryRunner`를 통해 트랜잭션을 생성하고 사용할 수 있다.

- 두 가지 방법을 설명하기에 앞서서, TypeORM의 트랜잭션 수행 과정을 이야기할 때 등장하게 될 `DataSource`, `EntityManager`, `QueryRunner` 이 세 가지 용어를 먼저 정리해보고자 한다.

<br>

### DataSource

- DataSource는 데이터베이스 연결 정보를 저장하고, connection과 connection pool을 설정하고 관리하는 객체이다.

- DataSource 객체를 생성하여 `initialize()`를 실행하면 connection과 connection pool 관리를 위한 설정이 이루어진다.

- 하나의 DBMS에 대하여 전역적으로 하나의 인스턴스로 관리된다.

  ```js
  import { DataSource } from "typeorm";

  const AppDataSource = new DataSource({
    type: "mysql",
    host: "localhost",
    port: 3306,
    username: "test",
    password: "test",
    database: "test",
  });

  AppDataSource.initialize()
    .then(() => {
      console.log("Data Source has been initialized!");
    })
    .catch((err) => {
      console.error("Error during Data Source initialization", err);
    });
  ```

<br>

### Entity Manager

- 모든 엔티티에 대해 쿼리를 수행하는 API를 제공한다.

- 한꺼번에 수행되어야 하는 작업들을 트랜잭션으로 래핑하는 메소드(transaction)를 제공한다.

- 모든 엔티티의 repository를 한 곳에 모은 것과 같은 역할을 한다.

  ```js
  import { DataSource } from "typeorm";
  import { User } from "./entity/User";

  const myDataSource = new DataSource(/*...*/);
  const user = await myDataSource.manager.findOneBy(User, {
    id: 1,
  });
  user.name = "Umed";
  await myDataSource.manager.save(user);
  ```

  - [+] 특정 엔티티에 대해 쿼리를 수행하려면 `myDataSource.getRepository(User).findOneBy({ id: 1 });`로 접근해야 한다.

- EntityManager는 EntityManagerFactory로부터 생성된다.

  - DataSource 인스턴스를 생성하여 초기화할 때 EntityManagerFactory의 create 메서드가 실행되고, 새로운 EntityManager 인스턴스가 DataSource 인스턴스의 manager 프로퍼티에 할당된다.

    <img width="525" alt="1" src="https://github.com/dawwson/TIL/assets/45624238/4e2fd345-445b-4506-be22-da86a6487779">


<br>

### Query Runner

- connection pool을 지원하는 RDBMS의 경우, connection pool에서 하나의 connection을 가져와서 사용하는 인터페이스이다. QueryRunner 객체를 생성할 때마다 connection pool에서 connection을 가져와서 사용한다.

- connection pool을 지원하지 않는 DBMS의 경우 DataSource 전체에서 하나의 connection을 사용하여, QueryRunner 인스턴스가 같은 connection을 공유한다.

- 쿼리를 수행하거나 개발자가 트랜잭션을 직접적으로 제어할 수 있도록 하는 API를 제공한다.

- QueryRunner는 클래스가 아니라 인터페이스이고, DBMS 마다의 구현체 클래스들이 존재한다.

- 주의할 점은 connection pool에서 다시 사용할 수 있도록 쿼리 수행 후에 connection을 반납해야 한다는 것이다.

<br>

## TypeORM Transaction 전략

### 1️⃣ DataSource & Entity Manager Transaction

- 앱을 부트스트랩 할 때 생성한 DataSource 인스턴스의 `transaction` 메소드 또는 `manager` 프로퍼티의 `transaction` 메소드로 트랜잭션을 수행할 수 있다.

  ```js
  await myDataSource.transaction(async (transactionalEntityManager) => {
    await manager.save(users[0]);
    await manager.save(users[1]);
  });

  // or

  await myDataSource.manager.transaction(async (transactionalEntityManager) => {
    await manager.save(users[0]);
    await manager.save(users[1]);
  });
  ```

- TypeORM의 GitHub 코드를 살펴보면, 두 코드는 사실상 같은 코드를 수행한다는 것을 알 수 있다.

  - DataSource 인스턴스를 생성할 때 수행되는 생성자 함수 내부에서 새로운 EntityManager를 생성하여 manager 프로퍼티에 할당한다

    <img width="517" alt="2" src="https://github.com/dawwson/TIL/assets/45624238/4cc9f78f-c636-4f1c-8f61-a03a80dcd5cd">


  - DataSource 클래스의 transaction 메소드에서는, 현재 DataSource 인스턴스의 manager 프로퍼티의 transaction 메소드를 수행한다.

    <img width="491" alt="3" src="https://github.com/dawwson/TIL/assets/45624238/3d50208f-2c8d-4ec8-9a77-26ede16b95c2">


- 주의해야 할 점은 쿼리를 수행하는 코드를 작성할 때 콜백 함수의 매개변수로 주어지는 EntityManager를 사용해야 한다는 것이다.

  - transaction 메소드의 내부를 살펴보면, 새로운 QueryRunner를 생성한다.

    <img width="831" alt="4" src="https://github.com/dawwson/TIL/assets/45624238/c0daf087-bc67-4624-9ad3-a23009539b49">


  - DataSource 클래스의 createQueryRunner 메소드를 따라가보면, 현재 DBMS 드라이버에 맞는 QueryRunner를 생성한다. 생성된 QueryRunner의 manager 프로퍼티에 새로운 EntityManager 객체를 할당한다.

    <img width="459" alt="5" src="https://github.com/dawwson/TIL/assets/45624238/a8bfbb01-9c51-441e-addd-3e18edb89440">

    <img width="657" alt="6" src="https://github.com/dawwson/TIL/assets/45624238/16f59ea4-ee2b-462e-81b2-197a7968de17">


  - 콜백함수로 전달되는 EntityManager는 DataSource에 있는 전역 EntityManager가 아니라, QueryRunner의 EntityManager이다.

- DataSource 또는 DataSource의 EntityManager의 transaction 메소드로 트랜잭션을 수행할 경우, 내부적으로 QueryRunner를 생성하여 commit, rollback, connection 반납까지 해주고 있다.

<br>

### 2️⃣ Query Runner Transaction

- 수동으로 QueryRunner를 생성하여,

  1. 직접 SQL 쿼리를 수행하거나

  2. EntityManager API로 쿼리를 수행하거나

  3. 트랜잭션을 제어할 수 있다.

  ```js
  const queryRunner = dataSource.createQueryRunner();

  await queryRunner.connect();
  // 1. 직접 SQL 쿼리를 수행하거나
  await queryRunner.query("SELECT * FROM users");
  // 2. EntityManager API로 쿼리를 수행하거나
  const users = await queryRunner.manager.find(User);

  // 3. 트랜잭션을 제어할 수 있다.
  await queryRunner.startTransaction();

  try {
    // 트랜잭션 작업들...
    await queryRunner.manager.save(user1);
    await queryRunner.manager.save(user2);
    await queryRunner.manager.save(photos);

    // commit
    await queryRunner.commitTransaction();
  } catch (err) {
    // 에러가 발생하면 rollback
    await queryRunner.rollbackTransaction();
  } finally {
    // 생성한 QueryRunner 해체(connection 반납)
    await queryRunner.release();
  }
  ```

<br>

## 어떤 걸 써야 할까?

- transaction 메소드는 상대적으로 코드가 간결해지는 효과가 있지만, 직접적으로 트랜잭션을 다룰 수 없다.

- QueryRunner는 직접적으로 트랜잭션을 제어할 수 있지만, 중복되는 코드가 많아지고 트랜잭션 관리에 대한 책임이 따른다.

- NestJS에서는 TypeORM의 트랜잭션을 다루는 전략 중 직접적으로 트랜잭션을 조작할 수 있는 QueryRunner를 사용하는 전략을 권장한다. [[참고]](https://docs.nestjs.com/techniques/database#typeorm-transactions)

- 개인적인 생각으로는 QueryRunner로 트랜잭션을 직접 제어하도록 하되, 중복되는 코드를 줄일 수 있는 리팩토링 방안을 찾는 것이 좋을 것 같다.

<br>

## 💡 Connection을 왜 DataSource로 바꿨을까?

- 0.2.x 버전에서 `Connection`은 0.3.0 버전부터 deprecated 되었고, 지금의 `DataSource`로 이름이 바뀌었다. [[참고]](https://typeorm.io/changelog#features-16)

  <img width="813" alt="7" src="https://github.com/dawwson/TIL/assets/45624238/b975222f-1905-4dfd-a168-b21fd07f392c">


- TypeORM 개발자가 올린 issue를 보면, Connection이라는 이름이 적절하지 않으며 DataSource로 변경해야 한다고 이야기한다.

- 이전의 Connection이라는 명칭은 실제로 connection을 의미하지 않으며, 그저 DBMS 연결 설정에 관한 객체일 뿐이다. 실제 connection을 의미하는 것은 QueryRunner이다.

### 왜 DataSource일까?

- Java에서 데이터베이스에 접근할 수 있도록 하는 API인 JDBC(Java Database Connectivity)에 DataSource라는 인터페이스가 있다. DataSource 인터페이스로 데이터베이스 연결과 connection을 얻는 과정을 추상화하는데, DataSource 구현체를 만들어서 사용한다. 대표적으로 Spring에서는 HikariCP connection pool을 기반으로 한 HikariDataSource를 제공한다.

- [+] JPA에도 EntityManager라는 개념이 있다. connection을 얻어서 데이터베이스와 상호작용하고 영속성 컨텍스트를 관리하는 역할이다.

  - TypeORM에도 영속성 컨텍스트, 1차 캐시의 개념이 있는지는 잘 모르겠다. 공식문서 상에서는 영속성 컨텍스트의 역할(1차 캐시에 엔티티 영속화)을 하는 개념은 찾을 수 없었다.

- TypeORM의 여러 개념들은 상대적으로 역사가 더 오래된 Java와 JPA에서 많은 부분 차용해서 만들었다고 생각한다. NodeJS에서 사용할 수 있는 또 다른 ORM 기술 중 하나인 Prisma에서도 DataSource라는 개념이 존재한다.

<br>

## 참고

> 1. TypeOrm 공식 문서 및 GitHub Issue
>    - [Deprecate ConnectionManager and rename Connection to DataSource #8010](https://github.com/typeorm/typeorm/issues/8010)
>    - [Working with DataSource](https://typeorm.io/data-source)
>    - [Working with Entity Manager](https://typeorm.io/working-with-entity-manager)
>    - [Working with Query Runner](https://typeorm.io/query-runner)
>    - [Transactions](https://typeorm.io/transactions)
> 2. 블로그
>    - [[Spring DataSource] Connection Pool, DataSource 란?](https://taler.tistory.com/12)
>    - [DataSource](https://opentutorials.org/module/3569/21223)
>    - [JDBC와 DataSource 이해하기](https://tecoble.techcourse.co.kr/post/2023-06-28-JDBC-DataSource/)
>    - [커넥션 풀이란 (Connection Pool)](https://shuu.tistory.com/130)
>    - [[DB] 커넥션 풀(Connection Pool)과 데이터소스(DataSource)](https://dangdangee.tistory.com/entry/DB-%EC%BB%A4%EB%84%A5%EC%85%98-%ED%92%80Connection-Pool%EA%B3%BC-%EB%8D%B0%EC%9D%B4%ED%84%B0%EC%86%8C%EC%8A%A4DataSource#google_vignette)
