# OAuth2

생활코딩 OAuth2

social service 에서 accessToken 을 획득 후 데이터 접근/생성/삭제 등 동작을 수행

Resource Owner
- user

Resource Server
- Social Service data server

Authorization Server
- Social Service Authorization server

Client
- d


## Register
- Client ID
- Client Secret
- AUthorized redirect URIs : Authorization Server 의 callback url


## Authentication

redirect_server
scope : Client가 ResourceServer가 제공하는 기능 중 허용 범위
redirect_url

- Resource Server
1. check exists ClientId
2. confirm redirect_url
3. confirm resource scope to resource owner
4. save resource owner : client scope
5. authorization code (if 3)
  - Location : https://clinet/callback?code=3
  - client 도 authorization code 를 GET
  - https://resource.server/token?grant_type=authorization_code&code=3&redirect_url=...&client_id=1&client_secret=2
  - ResourceServer는 주어진 정보들을 확인하고 accessToken을 발급
    - accessToken 은 userId-clientId unique


## Refresh Token
- accessToken life cycle 종료 후 get refresh Token
- 액세스토큰 만료시 Client 에서 Authorization Server 에 요청
- 얘는 리소스마다 갱신 정책이 다름
- accessToken 과 refreshToken 을 가져오는 정책은 OAuth2 에서 표준화 된 방법.



## Spring Security OAuth2
### ClientResource
- @NestedConfigurationProperty
- AuthorizationCodeResourceDetails
