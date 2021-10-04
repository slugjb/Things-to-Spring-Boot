# Spring
***
## RequestMapping

특정 url로 요청하면 controller에서 어떠한 방식으로 처리할지 정의하는데,

이때 들어온 요청을 특정 method와 mapping하기 위해 사용

함과 동시에 다른 origin에서 내 resources에 함수로 접근하지 못하게 하기 위해 사용된다.

* GET
  * 캐시 가능
  * 브라우저 히스토리에 기록 저장
  * 북마크 가능
  * 길이 제한 O
    *  get 요청에 의해 url이 너무 길다고 서버단에서 판단하면 414 오류를 보내는 프로토콜이
       지정되어 있을뿐, 
       
       길이에 대해 명확한 부분은 없다. 서버 기준이 아닌, 브라우저의 기준에 따름
  * 데이터 요청에만 사용
  * 보안 매우 취약
  * http 메시지에 body 없음
  * 멱등(idempotent)이다
  
  get을 통한 요청은 url 주소 끝에 파라미터로 포함되어 전송되며, 이를 query string라고 부른다.
  
  ex) www.ex.com/sample?name=value?name2=value2

* POST
  * 캐시 불가
  * 브라우저 히스토리 저장 안됨
  * 북마크 불가
  * 길이 제한 X
  * http 메시지에 body 있음
  * 멱등(idempotent)이 아니다

  post는 전송할 데이터를 http 메시지 body 부분에 담아서 서버로 보낸다.
  
  body의 타입은 Content-Type 헤더에 따라 결정 된다.

## PathVariables
## @RequestBody 와 @RequestParam
## Spring Boot Application
## Autowired
## RequiredArgsConstructor
## POM -> Gradle
***
# 보충 필요, 매우 중요 (정리 및 공부)
***

## CORS(Cross-Origin Resource Sharing)

http header를 사용해서 appcation이 다른 origin의 resources에 접근 할 수 있도록

함과 동시에 다른 origin에서 내 resources에 함수로 접근하지 못하게 하기 위해 사용된다.

## Log4j / Slf4j

  * log4j
      > 자바 기반 로깅 유틸리티, 디버그용 도구로 주로 사용된다.
      > 애플리케이션에 문제 발생 시, 로깅을 활성화하면 문제의 위치를 정확히 파악할 수 있다.
      > 속도 및 성능에 영향을 미치지 않고 편리하게 사용할 수 있다.
    
    참고 : https://cheershennah.tistory.com/80 , https://cofs.tistory.com/354
 
  * slf4j
      > log4j, logback, commons-logging 같은 로깅 프레임워크를 위한 추상화를 제공해준다.

  ***
