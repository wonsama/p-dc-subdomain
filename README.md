# p-dc-subdomain

## 개요

- 1개 또는 1개 이상 pc(공인 IP) 에서 여러개의 서브도메인을 지정하고자 하는 경우
- 손쉽게 설정 하고자 하는 경우 ( docker-compose 활용 )
- ssl 설정 ( https ), 무료 인증서를 설정 ( letsencrypt )

## domain / sub-domain 설정

> 다른 도메인 호스팅 업체도 동일(유사) 하게 작업을 해주면 됨

예시)

| 호스트명                 | 유형 | TTL   | 데이터 |
| ------------------------ | ---- | ----- | ------ |
| free-account.wonsama.dev | A    | 1시간 | IP주소 |

이런식으로 호스트명 (서브 도메인명 ) 과 IP 주소를 적어준다.

## docker 설치

> nginx 및 certbot 설치를 손쉽게 하기 위함.
> ubuntu 에서 설치 한 작업 내역, 아래 링크를 참조바랍니다.
> 과거에는 `docker` 와 `docker-compose` 가 별도 였으나 `docker` 커맨드에 포함됨 `docker compose` 이런식으로 호출하게 됨

- [Install Docker Engine on Ubuntu](https://docs.docker.com/engine/install/ubuntu/)

## 작업 실행

> 좀 어려울 수도 있으니 여러번 해보시고 잘 안되면 구글링 하심이 정신건강에 이롭긴 합니다 ㅜㅜ
>
> - ./docker-compose.yml 작성 ( `docker-compose.yml.sample` 참조 ) : nginx 와 certbot 을 설치
> - ./init-letsencrypt.sh 작성 ( `init-letsencrypt.sh.sample` 참조 ) : 인증서 발급
> - ./data/nginx/conf.d/app.conf 작성 ( `app.conf.1` 참조 ) : domain 설정정보

## 2개 이상의 도메인 등록 작업하는 방법

### 0. 들어가기 앞서

> ./init-letsencrypt.sh

파일에서 반드시 테스트를 수행하여 정상적으로 인증서가 설치 된지 여부를 확인하고 적용하기 바랍니다.

- `staging=1` : staging mode => 테스트 모드로 인증서 발행
- `staging=0` : production mode => 실제 운영 모드로 인증서 발행

왜냐면 인증서 발행 횟수 제한이 걸려 있기 때문 (과도한 인증서 발행을 제한하기 위함이라 함.)

### 1. 1st domain

> docker compose up -d 수행

- nginx 를 기동시켜 acme 인증을 받을 수 있도록 함.

> ./data/nginx/conf.d/subdomain1.conf

- subdomain1 정보를 설정
- 80 / 443 포트 모두 설정

> ./init-letsencrypt.sh

- subdomain1 정보만 기입하여 인증서 발행 (domains 정보 참조)

> docker compose down -d 수행

### 2. 2nd domain

> ./data/nginx/conf.d/subdomain1.conf

- 이전에 설정했던 subdomain1.conf 를 subdomain1.conf.1 이런식으로 rename

> docker compose up -d 수행

- nginx 를 기동시켜 acme 인증을 받을 수 있도록 함.

> ./data/nginx/conf.d/subdomain2.conf

- subdomain2 정보를 설정
- 80 / 443 포트 모두 설정

> ./init-letsencrypt.sh

- subdomain2 정보만 기입하여 인증서 발행 (domains 정보 참조)

> docker compose down -d 수행

### 3. 위 절차를 따라 가면서 Xth domain 까지 설정 이후

> ./data/nginx/conf.d/

- 위 폴더에서 rename 했던 conf 파일을 `xxx.conf.1 => xxx.conf 형태로 모두 바꿔준다.

> docker compose up -d 수행

- nginx 만 기동 시켜주도록 한다
- docker stop [certbot pid]

### 4. 나중에 다시 등록 하는 방법

> ./data/certbot/conf

- 아래 폴더 내 모든 정보를 삭제한다
- 폴더에 들어간 이후 `rm -rf *` 커맨드 수행 (실행 전 올바른 폴더에 위치하였는지를 확인 필요)
- accounts, archive, live, renewal

그리고 위 과정을 다시 반복 수행

## 참조 : temp 파일

> 폴더 구성을 하는 용도로 만듬 (폴더가 없으면 최초 파일 생성에 있어 오류 발생됨)
