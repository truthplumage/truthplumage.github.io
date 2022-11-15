---
layout: post
title: 로그인을 위한 jwt 사용법 with kotlin
permalink: /kotlin/jwt-kotlin-start
---

안녕하세요

우선 로그인을 위한 jwt 를 사용하기 위해 안내 블로그를 쓰기 시작한 박진우 입니다.ㅎㅎ

우선 로그인 방식은 예전부터 꽤 많은 수정이 있었는데 <br>
저는 요근래에 사용중인 jwt 방식에 대해서 포스팅 해보려고 합니다.

jwt(Json Web Token)란 기본 정보들을 base 64 인코딩으로 먼저 코드 값으로 바꾸고

header, payload, verify signature의 형태로 보여주는 구조를 의미 합니다.

[https://jwt.io](https://jwt.io)<br>
위 사이트에 접속해보시면 좀 더 이해가 편하실 겁니다.

header에는 알고리즘과 타입에 대한 정의가 기본적으로 되어 있고<br>
payload에는 사용자가 사용하려는 데이터를 기반으로 한 정보들이 들어있습니다.<br>
(간단하게 Base64 디코딩만 하면 얼마든지 내부 내용을 볼 수 있습니다.)

마지막으로 verify signature 정보가 있는데 이 부분은 header에 들어있는 알고리즘으로 만든 사인 정보인데

간단하게 데이터에 대한 인증 hash 정도로 생각하시면 될 것 같습니다.

```kotlin
Jwts.builder()
//헤더의 타입(type)을 지정할 수 있습니다. jwt를 사용하기 때문에 Header.JWT_TYPE로 사용해줍니다.
.setHeaderParam(Header.TYPE, Header.JWT_TYPE)
//헤더의 알고리즘(alg)을 지정할 수 있습니다. 알고리즘에 대한 정보는 아래와 같은 정보로 사용했습니다.
//알고리즘은 RS256(Sha256 with RSA)
.setHeaderParam(Header.COMPRESSION_ALGORITHM, SignatureAlgorithm.RS256.value)
//등록된 클레임 중, 토큰 발급자(iss)를 설정할 수 있습니다.
.setIssuer("")
//등록된 클레임 중, 발급 시간(iat)를 설정할 수 있습니다. Date 타입만 추가가 가능합니다.
.setIssuedAt(now)
//등록된 클레임 중, 만료 시간(exp)을 설정할 수 있습니다. 마찬가지로 Date 타입만 추가가 가능합니다.(min은 분단위로 된 int 입니다.)
.setExpiration(
    Date(
        now.time + Duration.ofMinutes(min.toLong())
            .toMillis()
    )
)
//비공개 클레임을 설정할 수 있습니다. (key-value)
.claim("user_id", userInfo.id).claim("user_name", userInfo.userName)
//해싱 알고리즘과 시크릿 키를 설정할 수 있습니다.
.signWith(privateKey, SignatureAlgorithm.RS256)
.compact()

```
