## 도커 컴포즈 정의하기
- 도커 컴포즈를 정의하는 부분은 도커에 대한 지식이 없는 사람은 어려울 수 있다.
- 도커의 지식이 없다면 도커 컴포즈로 서버를 실행하는 [도커 컴포즈 사용하기](#도커-컴포즈-사용하기) 부분으로 바로 이동하자.

### 기본적인 도커 컴포즈 파일 정의
```
version: "3"

services:
  web:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: web
    restart: always
    volumes:
      - ./:/app
      - /app/node_modules
      - /app/.next
    ports:
      - 3000:3000
```
- `version: "3"`: 도커 컴포즈의 yml, yaml (야믈, 야무르) 파일의 버전을 의미한다. 도커 컴포즈는 다양한 버전이 있으며 각 버전은 조금씩 다른 키워드와 구문을 사용한다. 구 버전의 도커는 최신 버전의 도커 컴포즈 버전을 지원하지 않을 수 있고 신 버전의 도커는 구 버전의 도커 컴포즈 버전을 지원하지 않을 수 있다.
- `services`: 컨테이너 하나를 어떻게 설정할 것인지 세팅한다. 만약 여러 인스턴스를 정의해야 한다면 각각의 `services:`에 설정 옵션을 지정해 줘야 한다.
- `web` 각 도커 컨테이너의 이름을 의미한다. 서비스가 하나의 도커 컨테이너를 정의하므로 서비스의 이름을 지어주는 것에 해당한다.
- `context`: `Dockerfile`이란 파일이 위치하는 경로를 지정한다.
- `dockerfile`: `Dockerfile`에 해당하는 파일의 이름을 지정한다.
- `container_name: web` 다른 `services`를 정의할 때 `container_name`에 쓰여진 이름을 참조하여 컨테이너 간의 관계를 지어줄 때 사용한다. `services:` 바로 하단의 `web`은 컨테이너의 이름이지만 도커 파일에서 각 서비스의 컨테이너간의 관계를 정의할 때는 `container_name`의 이름을 사용한다.
- `restart: always`를 설정하면 도커 컨테이너가 비정상적으로 종료되었을 때 자동으로 재시작한다. 도커는 컨테이너의 상태를 감시하는 데몬 기능을 실행하고 있고 도커 데몬은 컨테이너 상태를 지속적으로 체크하기 때문에 컨테이너가 종료되면 도커 데몬이 종료된 컨테이너를 다시 실행시키도록 한다. 하지만 정상적인 방법 즉, 의도적인 방법으로 종료가 일어났을 때는 도커 데몬은 재시작 시키지 않고 종료로 간주한다.
- `volumes:` 도커 내부의 폴더 및 파일과 호스트(도커 외부의 직접 코드를 작성하는 부분)의 폴더 및 파일이 공유되도록 설정한다. 이 때 공유되는 파일 및 폴더가 변경되면 어느 쪽을 우선하여 적용할 것인지 도커가 호스트를 덮어 쓸 것인지, 호스트가 도커를 덮어쓸 것인지를 설정할 수 있다.
- `./:/app`는 컨테이너 내부의 `/app` 폴더에 호스트의 작업 폴더인 `./`를 바인딩한다는 의미이다. 바인딩 한다는 것은 호스트의 어떤 경로를 도커 인스턴스의 어느 경로에 매치시킬 것인지를 정하는 것이다. 이 설정만으로는 호스트 폴더의 변경사항이 도커 내부의 파일 및 폴더에 적용되지 않는다.
- `/app/node_modules`와 `/app/.next`는 컨테이너의 `/app/node_modules`와 `/app/.next` 위치의 폴더와 이와 매칭되는 호스트의 `./node_modules` 와 `.next` 폴더를 공유하겠다고 지정하는 것이다. 폴더 공유 대상을 정하면 호스트의 폴더의 변경사항이 도커 내부의 파일 및 폴더에 적용된다.
- `ports:` HTTP 연결을 할 때 포트 번호를 지정하는 것으로 `3000:3000`으로 지정한 것은 왼쪽의 포트번호는 호스트에서 도커의 서버에 접속할 때 사용하는 포트 번호이고, 오른쪽의 포트 번호는 도커 인스턴스의 포트 번호이며 호스트의 3000번 포트는 web 인스턴스의 호스트 3000번에 연결된다는 의미이다. 일반적으로 인스턴스의 포트 번호는 인스턴스 내부에서 사용하는 프로그램들의 기본 포트 번호를 사용하며 호스트의 포트 번호는 특별한 번호로 설정하여 다른 도커 인스턴스와 겹치지 않는 유니크한 포트 번호를 사용하도록 한다.

## 도커 컴포즈 사용하기

#### 도커 이미지 빌드하기
```
docker compose build
```

#### 도커 인스턴스 정지하기
```
docker compose stop
```

#### 이미지로 도커 인스턴스를 기동하기
```
docker compose up
```
- 만약 서버 로그를 보고 싶지 않다면 `-d` 옵션을 주어 실행한다.
```
docker compose up -d
```