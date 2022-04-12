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

## docker, docker-compose 설치

> nginx 및 certbot 설치를 손쉽게 하기 위함.
> ubuntu 20.04 에서 설치 한 작업 내역, 아래 링크를 참조바랍니다.

https://docs.docker.com/engine/install/ubuntu/
https://docs.docker.com/compose/install/

## 작업 실행

> 좀 어려울 수도 있으니 여러번 해보시고 잘 안되면 구글링 하심이 정신건강에 이롭긴 합니다 ㅜㅜ
>
> - ./docker-compose.yml 작성 ( `docker-compose.yml.sample` 참조 ) : nginx 와 certbot 을 설치
> - ./init-letsencrypt.sh 작성 ( `init-letsencrypt.sh.sample` 참조 ) : 인증서 발급
> - ./data/nginx/conf.d/app.conf 작성 ( `app.conf.1`, `app.conf.2` 참조 ) : domain 설정정보

1. `$ vi ./data/nginx/conf.d/app.conf` : http 설정
2. `$ docker-compose up -d` : nginx, certbot 기동
3. `$ ./init-letsencrypt.sh` : 인증서 생성 (실행 전 `$ chmod +x ./init-letsencrypt.sh` 로 권한 주는 것 잊지 말고 ... )
4. `$ vi ./data/nginx/conf.d/app.conf` http, https 설정
5. `$ docker-compose restart` 재기동
6. `$ docker stop certbot` 필요 없는 certbot 컨테이너 중지

3개월 지나서 letsencrypt 인증서 재인증 받아야 되는 경우

1. `$ docker-compose down` : 위에서 certbot 컨테이너를 중지 시킨 경우 종료 후 재실행 하기 위함
2. `$ docker-compose up -d` : nginx, certbot 기동
3. `$ ./init-letsencrypt.sh` : 인증서 재생성
4. `$ docker-compose restart` 재기동
5. `$ docker stop certbot` 필요 없는 certbot 컨테이너 중지

## 참조 : docker-compose.yml

> nginx, certbot 버전 - 최초 작업 시점(정상적 동작) 기준으로 함.

## 참조 : temp 파일

> 폴더 구성을 하는 용도로 만듬 (폴더가 없으면 최초 파일 생성에 있어 오류 발생됨)
