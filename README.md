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
           
 ```
 @RequestMapping("/user/view/{id}")
public String view(@PathVariable("id") int id) {
    ...
}
```
 > URL의 중괄호({}) 에는 패스 변수를 넣는다. 이 이름을 @PathVariable 애노테이션의 값으로 넣어서 메소드
 > 파라미터에 부여해 주면 된다. /user/view/10 이라는 URL 이라면, id 파라미터에 10이라는 값이 들어온다.
 > 파라미터의 타입은 URL 의 내용이 적절히 변환될 수 있는 것을 사용해야 한다. int 타입을 썻을 경우에는
 > 반드시 해당 패스 변수 자리에 숫자 값이 들어 있어야 한다. 타입이 일치하지 않는 값이 들어 올 경우
 > 별 다른 예외처리를 해주지 않는 다면 HTTP 400 - Bad Request 응답코드가 전달된다.


## @RequestParam
  > 1개의 요청 파라미터를 받기 위해서 사용한다. @RequestParam은 필수 여부가 true이기 때문에, 기본적으로
  > 반드시 해당 파라미터가 전달되어야 한다. 만약 전송되지 않으면 400 Error을 유발하게 된다. 
  > 그렇기 때문에 반드시 필요한 변수가 아니라면 required의 값을 false로 설정가능하며, 해당 Parameter를
  > 사용하지 않고 요청을 보낼 경우에 default로 받을 값을 defaultValue 옵션을 통해 설정할 수도 있다.

  * 가져올 요청 파라미터의 이름을 @RequestParam 어노테애션의 기본 값으로 지정해주면 된다.
  * 요청 파라미터의 값은 메소드 파라미터의 타입에 따라 적절히 변환 된다.

```
public String method1(@RequestParam("id") int id) { ... }
 
public String method2(@RequestParam("id") int id,
        @RequestParam("name") String name,
        @RequestParam("file") MultipartFile file) { ... }
 
public String method3(@RequestParam Map<String, String> params) { ... }
 
public String method4(
        @RequestParam(value="id", required=false, defaultValue="-1") int id) { ... }
 
public String method5(@RequestParam int id) { ... }

```
 * method1() 의 선언은 id 요청 파라미터를 int 타입으로 변환해서 메소드의 id 파라미터에 넣어준다.

 * @RequestParam 은 method2() 와 같이 하나 이상의 파라미터에 적용할 수 있다.
 * 
    스프링의 내장 변환기가 다룰 수 있는 모든 타입을 지원한다.
    
 * method3() 과 같이 @RequestParam 에 파라미터 이름을 지정하지 않고 Map<String, String> 타입으로
 
    선언하면 모든 요청 파라미터를 담은 맵으로 받을 수 있다. 파라미터 이름은 맵의 키에, 값은 맵의 값에 담겨 전달된다.
    
 * @RequestParam 을 사용했다면 해당 파라미터가 반드시 있어야 한다. 없다면 HTTP 400 - Bad Request 가 발생한다.
  
     파라미터가 필수가 아니라 선택적으로 제공하게 하려면, required 엘리먼트를 false 로 설정해 주면 된다.
   
     요청 파라미터가 존재하지 않을 때 사용할 디폴트 값도 지정할 수 있다.
  
     method4() 는 required 와 defaultValue 엘리먼트를 설정한 예시이다.

 * method5() 는 메소드 파라미터의 이름과 요청 파라미터의 이름이 일치하기 때문에 @RequestParam 의 이름 엘리먼트를 생략한 예시이다.

 * String, int 와 같은 단순 타입인 경우는 @RequestParam 을 아예 생략할 수도 있다.
  
    이때는 메소드 파라미터와 같은 이름의 요청 파라미터 값을 받는다. 하지만 파라미터의 개수가 많고
  
    종류가 다양해지면 코드를 이해하는 데 불편할 수도 있다. 단순한 메소드가 아니라면 명시적으로
  
    @RequestParam 을 부여해 주는 것을 권장한다.

***
* URL 

RequestParam
http://192.168.0.1:8080?aaa=bbb&ccc=ddd
PathVariable
http://192.168.0.1:8080/bbb/ddd

## ModelAttribute
   > 클라이언트가 전송하는 multipart/form-data 형태의 HTTP Body 내용과 HTTP 파라미터들을 Setter를 통해
   > 1대1로 객체에 바인딩하기 위해 사용된다. @ModelAttribute에는 매핑시키는 파라미터의 타입이 객체의 타입과
   > 일치하는지를 포함한 다양한 검증(Validiation) 작업이 추가적으로 진행된다. 예를 들어 게시물의 번호를
   > 저장하는 int형 index 변수에 "1번" 이라는 String형을 넣으려고 한다면, BindException이 발생하게 된다. 
   > 즉, Json이나 XML과 같은 형태의 데이터를 MessageConverter를 통해 변환시키는 @RequestBody와 달리,
   > @ModelAttribute는 multipart/form-data 형태의 HTTP Body와 HTTP 파라미터들을 매핑시킨다는 차이가 있다.

   * 변환이 아닌 바인딩을 하므로, 변수들의 Setter함수가 없으면 변수들이 저장되지 않는다.

  > @ModelAttribute 어노테이션을 활용해서 특정 Parameter만을 받아올 수도 있다.
  > {writer:'slug', contents: 'hi i'm slug'}의 형태로 데이터를 전송했다고 하면, 컨트롤러에서는
  > @ModelAttribute('writer') String writer의 형태를 활용하여 writer 변수의 'slug' 만을 바인딩시켜 받아올 수 있다

   

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
   > 클라이언트가 전송하는 Json(application/json)형태의 HTTP Body 내용을 Java Object로 변환시켜주는 역할을 한다.
   > 그렇기 때문에 Body가 존재하지 않는 Get 메소드에 @RequestBody를 활용하려고 한다면 에러가 발생하게 된다.
   > @RequesetBody로 받는 데이터는 Spring에서 관리하는 MessageConverter들 중 하나인
   > MappingJackson2HttpMessageonverter를 통해 Java객체로 변환된다.
   > Spring은 메세지를 변환하는 과정에서 객체의 기본 생성자를 통해 객체를 생성하고, Reflection을 사용해
   > 값을 할당하기 때문에, @RequestBody에는 Setter가 필요가 없다.

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

  ## 정리
  > 클라이언트에서 서버로 필요한 데이터를 요청하기 위해 JSON 데이터를 요청 본문에 담아서 서버로 보내면,
  > 서버에서는 @RequestBody 어노테이션을 사용하여 HTTP 요청 본문에 담긴 값들을 자바객체로 변환시켜, 객체에 저장한다.

  > 서버에서 클라이언트로 응답 데이터를 전송하기 위해 @ResponseBody 어노테이션을 사용하여 자바 객체롤 HTTP
  > 응답 본문의 객체로 변환하여 클라이언트로 전송한다.
 
     

***
보충 필요
>@RequestBody를 통해서 자바객체로 conversion을 하는데, 이때 HttpMessageConverter를 사용한다.
>@ResponseBody 가 붙은 파라미터에는 HTTP 요청의 분문 body 부분이 그대로 전달된다.
>RequestMappingHandlerAdpter 에는 HttpMessageConverter 타입의 메세지 변환기가 여러개 등록되어 있다.

## Autowired `보충필요`
  필요한 의존 객체의 타입에 해당하는 빈을 찾아 주입한다.
  
   * 생성자
   * setter
   * 필드
  
  Autowired는 기본값이 ture이기 때문에 의존성 주입을 해야할 대상을 찾이 못한다면 애플리케이션 구동에 실패한다.
  
 ### Constructor Dependency Injection
   생성자 주입은 생성자에 의존성 주입을 받고자 하는 field를 나열하는 방법으로,
   권고되는 방법 중 하나이다.
   
    * 장점
      * 필수적으로 사용해야 하는 레퍼런스 없이는 인스턴스를 만들지 못하도록 강제한다.
      * Spring 4.3 이상부터는 생성자가 하나인 경우 @Autowired를 사용하지 않아도 된다.
      * 
   

## Spring Boot Application

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
