# Docker Compose로 MongoDB 개발 환경 구성하기

- Docker Compose로 MongoDB를 실행하는 방법을 정리하였다.

<br>

## Docker Compose로 개발 환경을 구성하면 좋은 점

- 개발 환경을 하나의 파일에 정의할 수 있다.

- 협업 시 여러 개발자가 동일한 환경에서 작업할 수 있다.

- 여러 서비스 및 네트워크를 정의하고 컨테이너 간 실행 순서나 의존성도 관리할 수 있다.

- 개발 환경에서 데이터베이스 관리가 용이하다. 불필요한 데이터가 쌓여있어 초기화할 때, 컨테이너를 삭제하고 재실행하거나 마운트된 볼륨을 삭제하면 된다.

<br>

## `docker-compose.yml` 파일 작성

- `docker-compose.yml`은 장황한 도커 실행 옵션을 미리 적어둔 문서라고 볼 수 있다.

```yml
# version: "3.8" => 삭제!

services:
  db:
    image: mongo:latest
    container_name: mongodb-container
    ports:
      - ${DB_PORT}:27017
    env_file:
      - .env
    environment:
      MONGO_INITDB_ROOT_USERNAME: admin
      MONGO_INITDB_ROOT_PASSWORD: ${DB_ADMIN_PASSWORD}
      MONGO_INITDB_DATABASE: admin
    volumes:
      - ./mongo-init.js:/docker-entrypoint-initdb.d/mongo-init.js:ro
      - ./data/mongo:/data/db
```

### version

```yml
version: "3.8"
```

- docker-compose.yml 파일의 규격 버전을 적는다.

- 규격에 따라 지원하는 옵션이 달라지는데, 버전3부터는 '3'이라고만 적으면 3으로 시작하는 최신 버전을 사용한다. [[참고]](https://docs.docker.com/compose/compose-file/compose-versioning/)

  - 다만, 버전2에서는 '2' = '2.0'을 의미한다.

  - [+] **version을 명시하면 `version is obsolete` 라는 경고가 발생하는데, 해당 라인을 삭제해줘야 한다.** Docker Compose에서 더이상 version을 사용하지 않는다고 한다.

### services

```yml
services:
  db:
```

- services에는 실행하려는 컨테이너들을 지정한다. 서비스의 이름을 db로 지정하였다.

- Docker Compose에서는 컨테이너 대신 서비스라는 개념을 사용한다.

- [+] 하나의 docker-compose.yml 파일 안에 있는 서비스들은 별도로 지정하지 않으면 하나의 네트워크에 속하여 서비스끼리 통신할 수 있다.

### image

```yml
image: mongo:latest
```

- db 서비스에서 사용할 docker 이미지를 지정한다.

- dockerhub 공식 mongodb 이미지를 사용하였고, 버전은 최신 버전(2024.03 기준 7버전)을 사용하였다. [[참고]](https://hub.docker.com/_/mongo)

### container_name

```yml
container_name: mongodb-container
```

- 서비스의 컨테이너 이름을 지정한다.

### ports

```yml
ports:
  - ${DB_PORT}:27017
```

- <호스트 포트번호>:<서비스 포트번호>로 정의한다.

- 서비스 외부로 노출할 포트를 지정한다.

#### 💡 ports vs expose

- ports는 호스트와 서비스 간의 포트 바인딩을 정의한다. 지정한 호스트 포트번호로 외부에서 서비스와 통신할 수 있다.

- expose는 호스트 내부의 다른 컨테이너에 노출할 포트를 정의한다. 다른 컨테이너에서 접근하려면 동일한 docker 네트워크에 있어야 하며, 호스트 외부에서는 접근할 수 없다.

### env-file

```yml
env_file:
  - .env
```

- 컨테이너에 환경변수를 제공할 파일을 지정한다.

### environment

```yml
environment:
  MONGO_INITDB_ROOT_USERNAME: admin
  MONGO_INITDB_ROOT_PASSWORD: ${DB_ADMIN_PASSWORD}
  MONGO_INITDB_DATABASE: admin
```

- MongoDB DockerHub 공식 이미지에서 제공하는 환경변수를 지정한다. [[참고]](https://hub.docker.com/_/mongo)

  - `MONGO_INITDB_ROOT_USERNAME`, `MONGO_INITDB_ROOT_PASSWORD`

    - MongoDB에서 `root` 역할을 받는 사용자 정보를 지정한다(슈퍼 유저를 의미한다).
    - admin이라는 이름의 인증 정보를 가지고 있는 데이터베이스에 저장된다.

  - `MONGO_INITDB_DATABASE`

    - `/docker-entrypoint-initdb.d/*.js`에서 사용할 데이터베이스 이름을 지정한다.

    - 'admin'으로 지정한 이유는, 초기 데이터 생성 스크립트에서 새로운 사용자를 추가할 때 admin 데이터베이스를 사용하기 위함이다.

- 컨테이너 실행 후 mongosh 명령어로 MongoDB에 접속하여 만들어진 root 사용자를 확인할 수 있다.

  ```shell
  > mongosh -u admin
  > Enter password: ****

  test> show dbs;
  admin> show dbs;
  admin   100.00 KiB
  config   72.00 KiB
  local    72.00 KiB

  test> use admin
  admin> show users
  [
    {
      _id: 'admin.admin',
      userId: UUID('0a509350-9711-4046-ad51-3c69f01ad3b7'),
      user: 'admin',
      db: 'admin',
      roles: [ { role: 'root', db: 'admin' } ],
      mechanisms: [ 'SCRAM-SHA-1', 'SCRAM-SHA-256' ]
    }
  ]
  ```

### volumes

```yml
volumes:
  - ./mongo-init.js:/docker-entrypoint-initdb.d/mongo-init.js:ro
  - ./data/mongo:/data/db
```

- 호스트와 컨테이너 간 데이터 공유 위치를 설정한다. 호스트의 파일 시스템 경로나 다른 컨테이너의 데이터를 컨테이너 내부에 마운트할 수 있다.

- `./mongo-init.js:/docker-entrypoint-initdb.d/mongo-init.js:ro`

  - 호스트의 현재 작업위치(docker-compose.yml 파일이 있는 곳)의 `mongo-init.js` 파일을 찾고, 컨테이너의 `docker-entrypoint-initdb.d` 디렉토리에 마운트한다.

  - `:ro`는 읽기 전용으로 마운트한다는 의미이다.

  - 컨테이너가 처음 시작되면 `docker-entrypoint-initdb.d` 디렉토리에서 `.sh` 또는 `.js` 확장자를 가진 파일을 알파벳 순으로 실행한다.

  - `MONGO_INITDB_DATABASE`에 지정한 데이터베이스를 사용하여 mongosh에서 실행한다.

- `./data/mongo:/data/db`

  - <호스트 디렉토리>:<컨테이너 디렉토리>로 정의한다.

  - 호스트의 현재 작업 위치(docker-compose.yml 파일이 있는 곳)의 특정 디렉토리에 컨테이너 내부의 /data/db 디렉토리를 마운트한다.

  - 컨테이너를 종료하거나 삭제해도 마운트한 디렉토리의 데이터를 삭제하지 않는 한 데이터는 남아있다.

<br>

## `mongo-init.js` 파일 작성

```js
const database = process.env.DB_DATABASE;
const backendUsername = process.env.DB_BACKEND_USERNAME;
const backendPassword = process.env.DB_BACKEND_PASSWORD;

db.createUser({
  user: backendUsername,
  pwd: backendPassword,
  roles: [
    {
      role: "readWrite",
      db: database,
    },
  ],
});
```

- 여기서 db는 `MONGO_INITDB_DATABASE` 환경변수에 지정한 'admin'이고, 백엔드에서 MongoDB에 접속하기 위해 read, write 역할을 가진 사용자를 추가한다.

- MongoDB에서는 역할 기반으로 접근을 제어하기 위해 관리자를 생성한 후에 적절한 권한을 가진 새로운 사용자를 생성할 것을 권장하고 있다. [[참고]](https://www.mongodb.com/docs/manual/administration/security-checklist/#arrow-configure-role-based-access-control)

<br>

## NodeJS에서 MongoDB 접근하기

- `docker-compose up -d` 명령어로 컨테이너를 실행한다.

- MongoDB 서버에 접속해야 한다. mongoose를 사용할 경우 아래와 같이 작성할 수 있다.

  ```js
  import dotenv from "dotenv";
  import mongoose from "mongoose";

  dotenv.config();

  const dbName = process.env.DB_DATABASE;
  const dbPort = process.env.DB_PORT;
  const dbUser = process.env.DB_BACKEND_USERNAME;
  const dbPassword = process.env.DB_BACKEND_PASSWORD;

  const mongoUri = `mongodb://${dbUser}:${dbPassword}@127.0.0.1:${dbPort}/${dbName}?authSource=admin`;

  mongoose.connect(mongoUri);
  ```

  - URI에 `?authSource=admin` 쿼리 파라미터를 붙여주지 않으면 Authentification Failed 에러가 발생한다.

  - authSource는 사용자 인증정보 collection(= users)를 가진 데이터베이스 이름을 지정한다.

  - 백엔드에서 사용하기 위해 read, write 역할을 가진 사용자를 admin 데이터베이스에 추가하였으므로 `?authSource=admin`을 URI에 붙여주어야 한다.

<br>

## 참고

> 1. Docker
>    - [MongoDB Official Image](https://hub.docker.com/_/mongo)
> 2. MongoDB
>    - [Security Checklist](https://www.mongodb.com/docs/manual/administration/security-checklist/#security-checklist)
> 3. 블로그
>    - [도커 컴포즈를 활용하여 완벽한 개발 환경 구성하기](https://www.44bits.io/ko/post/almost-perfect-development-environment-with-docker-and-docker-compose#%EB%8F%84%EC%BB%A4-%EC%BB%B4%ED%8F%AC%EC%A6%88%EB%A1%9C-%EA%B0%9C%EB%B0%9C-%ED%99%98%EA%B2%BD-%EA%B5%AC%EC%84%B1%ED%95%98%EA%B8%B0)
