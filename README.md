# p-dc-subdomain

## 개요

- 1개 또는 1개 이상 pc(공인 IP) 에서 여러개의 서브도메인을 지정하고자 하는 경우
- 손쉽게 설정 하고자 하는 경우 ( docker-compose 활용 )
- ssl 설정 ( https ), 무료 인증서를 설정 ( letsencrypt )

## GoogleDomains

> 다른 도메인 호스팅 업체도 동일(유사) 하게 작업을 해주면 됨

| 호스트명                 | 유형 | TTL   | 데이터 |
| ------------------------ | ---- | ----- | ------ |
| free-account.wonsama.dev | A    | 1시간 | IP주소 |

이런식으로 호스트명 (서브 도메인명 ) 과 IP 주소를 적어준다.

로컬에서 특정 포트로 호스팅 하는 경우에도 ( ex) port 3000 ) 서브 도메인을 활용하여 proxy 할 수 있다.

## docker, docker-compose 설치

> nginx 및 certbot 설치

> ubuntu 20.04 에서 설치 한 작업 내역임, 아래 링크를 참조하면 된다.

어려운 것은 없고? 뭐 따라하면 금방임.

https://docs.docker.com/engine/install/ubuntu/
https://docs.docker.com/compose/install/

## 중요 파일

- ./docker-compose.yml

> nginx 와 certbot 을 설치

- ./init-letsencrypt.sh

> 인증서 발급 정보
> 한번에 여러개 설정 가능

- ./data/nginx/conf.d/app.conf

> subdomain & ssl 설정 정보
> 한번에 여러개 설정 가능

## 작업 실행

> 좀 어려울 수도 있으니 여러번 해보시고 잘 안되면 구글링 하심이 정신건강에 이롭긴 합니다 ㅜㅜ

### 1. 폴더 생성 및 파일 작성

폴더 생성

```txt
L data
__ L nginx
__ __ L conf.d
__ L certbot
__ __ L www
__ __ L conf
```

- ./docker-compose.yml 작성 ( `docker-compose.yml.sample` 참조 )
- ./init-letsencrypt.sh 작성 ( `init-letsencrypt.sh.sample` 참조 )
- ./data/nginx/conf.d/app.conf 작성 ( `app.conf.1`, `app.conf.2` 참조 )

### 2. 인증서 생성

`app.conf` 에서 ssl 설정(443포트 포함) 해당 부분을 제거
`docker-compose.yml` 파일이 있는 경로에서 `docker-compose up` 수행
`chmod +x ./init-letsencrypt.sh` 이후 `./init-letsencrypt.sh` 을 실행하여 인증서 생성

### 3. 인증서 반영

`app.conf` 에서 ssl 설정(443포트 포함) 해당 부분을 추가
`docker-compose.yml` 파일이 있는 경로에서 `docker-compose up -d` 수행 (백그라운드 기동)

추후 인증서 기간이 만료 되면 2, 3 항목을 순서대로 실행한다
