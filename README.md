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
  RUL 경로에 변수를 넣어주는 것!!
  
  * @RequestMapping의 URL 정의 부분과 Method내의 Parameter 부분에 정의를 하여 사용이 가능하다.
  * @RequestMapping 어노테이션 갑으로 {템플릿 변수}를 사용한다.
  * @PathVariable 어노테이션을 이용해서 {템플릿 변수}와 동일한 이름을 갖는 파라미터를 추가한다.
`@RequestMapping(value = "/user/email/{email}", method=RequestMethod.GET)
이러한 형식일때 아래와 같이 바꿔주면 값이 제대로 들어온다.
```
@RequestMapping(value = "user/email/{email:.+}", method = RequestMethod.GET)
public ModelAndView getUserByEmail(@PathVariable("email") String email) {}
```
  ※ 주의 : null이나 공백값이 들어가는 parameter라면 적용하지 말것!
           Spring에서 @PathVariable를 사용하여 값을 넘겨받을 때에 . 이 포함되어 있으면,
           .을 포함하여 뒤가 잘려서 들어온다.

## @RequestParam


RequestParam
http://192.168.0.1:8080?aaa=bbb&ccc=ddd
PathVariable
http://192.168.0.1:8080/bbb/ddd

## @RequestBody / @ResponseBody
     ![image](https://user-images.githubusercontent.com/64000158/135939785-4cb3b016-5073-403d-90eb-7841a5a6f2a5.png)  

 클라이언트 -> 서버 = request 메시지  / 서버 -> 클라이언트 = response 메시지
 
 >웹에서 화면 전환(refresh) 없이 이루어지는 동작들은 대부분 비동기 통신으로 이루어진다.
 >비동기 통신을 하기 위해서는 클라이언트에서 서버로 혹은 서버에서 클라이언트로 요청,응답 메세지를 
 >보낼 때 본문에 데이터를 담아서 보내야하는데, 이 본문이 바로 `body`이다.
 >본문에 담기는 데이터 형식은 여러가지가 있지만, 가장 대표적으로 사용되는것이 `JSON`이다
 >즉, 비동기식 클라-서버 통신을 위해 JSON 형식의 데이터를 주고받는 것이다.

 * @RequestBody
   * Http 요청의 본분(body)이 그대로 전달된다.
   * 일반적인 GET/POST의 요청 파라미터라면 @RequestBody를 사용할 일이 없다.
   * xml이나 json기반의 메세지를 사용하는 요청의 경우에는 이 방법이 매우 유용하다
   * HTTP 요청의 바디내용을 통째로 자바 객체로 변환해서 매핑된 메소드 파라미터로 전달해준다.

 * @ResponseBody   `보충필요`
   * 자바 객체를 HTTP요청의 바디내용으로 매핑하여 클라이언트로 전송한다.
   * @ResponseBody가 붙은 파라미터가 있으면, HTTP요청의 미디어 타입과 파라미터의 타입을 확인한다.
   * 메세지 변환기 중에서 해당 미디어 타입과 파라미터 타입을 처리할 수 있다면, HTTP요청의 본문 부분을

     통째로 변환해서 지정된 메소드 파라미터로 전달해준다.
   * @ResponseBody 어노테이션을 사용하면 HTTP요청 body를 자바 객체로 전달받을 수 있다.

  * @RestController `보충필요`
   > @Controller와는 다르게 @RestController는 리턴값에 자동으로 @ResponseBody가 붙게 되어 별도의
   > 어노테이션을 명시해주지 않아도, HTTP 응답데이터(body)에 자바 객체로 매핑되어 전달된다.
   > @Controller인 경우에는 body를 자바 객체로 받기 위해서는 @ResponseBody 어노테이션을 반드시 명시해주어야한다.

  ** 정리
  > 클라이언트에서 서버로 필요한 데이터를 요청하기 위해 JSON 데이터를 요청 본문에 담아서 서버로 보내면,
  > 서버에서는 @RequestBody 어노테이션을 사용하여 HTTP 요청 본문에 담긴 값들을 자바객체로 변환시켜, 객체에 저장한다.

  > 서버에서 클라이언트로 응답 데이터를 전송하기 위해 @ResponseBody 어노테이션을 사용하여 자바 객체롤 HTTP
  > 응답 본문의 객체로 변환하여 클라이언트로 전송한다.
 
     

***
보충 필요
>@RequestBody를 통해서 자바객체로 conversion을 하는데, 이때 HttpMessageConverter를 사용한다.
>@ResponseBody 가 붙은 파라미터에는 HTTP 요청의 분문 body 부분이 그대로 전달된다.
>RequestMappingHandlerAdpter 에는 HttpMessageConverter 타입의 메세지 변환기가 여러개 등록되어 있다.



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
