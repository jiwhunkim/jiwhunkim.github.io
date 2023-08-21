---
layout: post
title: 2023 우아한 스터디 - 우아한 대규모 시스템 설계 - 채팅 서비스 만들고 확장하기
tags: ["study"]
date: 2023-07-15T00:00:00+09:00
key: 2023-07-15-woowa-system-design-study-chat-service
---
# 가상 면접 사례로 배우는 대규모 시스템 설계 기초

## 채팅 서비스 만들고 확장하기
- 처음에는 작은 채팅 서비스
- 사용자가 많아지면서 인접한 국가에서 사용하기 시작함
- 특정날(설날, 크리스마스 등)에 메시지 전송량이 매우 많음
- 참고자료
  - https://www.slideshare.net/awskorea/aws-summit-seoul-2023-aws-native

## 질문
- 데이터베이스
  - 데이터베이스가 필요한가
  - 데티어베이스를 어떤걸 쓸 것인가
  - 채팅의 내용에 보관주기는 어느정도로 설정한 것인가
  - 오래된 채팅 내용은 어디에 저장할 것인가
- 모놀리틱, 모노레포 
  - 서버 언어, 프레임워크 
    - 자바, 스프링 
  - 웹소켓 + JS 
  - 팀 구성원의 숙련도에 따라서 선택함 
- 어플리케이션 배포는 어떻게 할지? 
  - 어플리케이션은 스케일업 또는 스케일아웃이 가능해야함 
- 한국 인접한 나라가 사용하는데 사용자가 느리다고 말하는 상황 
  - 처음에는 한국 100% -> 70% 
  - 옆나라 30% 
  - 옆나라랑 한국이랑 채팅방은 20%, 각나라 채팅은 80%
- 디비를 인접한 국가로 다중화를 해놓았는데 장애가 난 상황 
  - 인접한 국가까지 쓸 정도로 서비스가 커졌을 때 소스 코드 관리를 어떻게 하면 좋을지? 
  - 도메인 별로 분리할지? 
  - 흐름/업무 프로세스별로 분리할지? 
  - 이벤트 기반으로 간다. 
- 특정날에 메시지 전송량이 무척 많을 때의 처리를 어떻게 할 것인가? 
  - 이모지를 어떻게 구현할까? 
## 답변
- 데이터베이스
  - 회원은 RDB?
  - 채팅의 경우에는 친구 조회나 친구 추가 join 도 빈번하게 일어남
  - 채팅은 별도 DB? -> NoSql
  - 확장성에 명확함
- 모노레포로 갔으면 좋겠다
  - 자바, 스프링
- 어플리케이션 배포
  - 회사 정책에 따름
  - 클라우드 서비스를 이용한다.
  - 컨테이너를 배포한다.
  - ec2 에 올린다.
  - 젠킨스 + 코드 디플로이를 이용한다.
- 사용자가 많아지면서 인접한 국가에서 사용하는 상황
  - 채팅을 관리하는 NoSql 을 리전간에 걸쳐서 확장한다.
  - Region 간 DB 를 복제 싱크하고, 옆나라에 어플리케이션을 올린다. 소스는 한벌로 간다.
- 장애가 난 상황은 route 처리를 바꾼다.
- MSA 구조로 가거나 어떻게 쪼개면 좋을지
- 병목구간은 무엇일까?
  - 회원 DB -> 회원 DB에 대한 정보를 캐싱
  - 채팅방이 느릴 때 -> 채팅서버가 처리를 느리니까 워커로드에 컨테이너를 더 올린다.
  - 카프카의 토픽을 늘린다.
  - 통신 방식의 프로토콜을 바꾼다([겁나 빠른 황소 프로젝트](https://yunknows.tistory.com/31))
- 특정날에 메시지 전송량이 무척 많을 때의 처리를 어떻게 할 것인가?
  - 인스턴스를 미리 많이 올려둔다.
  - 컨테이너를 미리 많이 올려둔다.
  - [LINE 오픈챗 서버가 100배 급증하는 트래픽을 다루는 방법](https://engineering.linecorp.com/ko/blog/how-line-openchat-server-handles-extreme-traffic-spikes)