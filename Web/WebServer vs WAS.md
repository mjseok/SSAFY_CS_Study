# WebServer vs WAS
> 영상 추천 : [[10분 테코톡] 🎙티거의 Web server vs WAS](https://www.youtube.com/watch?v=F_vBAbjj4Pk&list=PLgXGHBqgT2TvpJ_p9L_yZKPifgdBOzdVH&index=200)

## WebServer란?
### 클라이언트로부터 HTTP 요청을 받아 HTML 문서나 각종 리소스(Resouce)를 반환하는 컴퓨터  

WebServer란 클라이언트가 웹 브라우저에 어떠한 페이지 요청을 하면 WebServer에서 그 요청을 받아 **정적 컨텐츠를 제공하는 서버**이다.  
여기서 정적 컨텐츠란 단순 HTML 문서, CSS, javascript, 이미지, 파일 등 **즉시 응답가능한 컨텐츠**이다.  

그렇다면 WebServer는 정적 컨텐츠만 제공하느냐?  

그것은 아니다. WebServer가 동적 컨텐츠를 요청 받으면 WAS에게 해당 요청을 넘겨주고, WAS에서 처리한 결과를 클라이언트(사용자)에게 전달해주는 역할도 한다.  
이와 같이 WebServer에서 동적인 컨텐츠를 요청 받는 경우 어떻게 처리해야될까 라는 개념에서 WAS라는 개념이 생겼습니다.

ex) Apache, NGNIX  

![image](https://user-images.githubusercontent.com/68904159/196162598-18aadec3-f423-42cf-a646-5eca7ce38849.png)


## WAS(Web Application Server)란?
### 웹 어플리케이션과 서버 환경을 만들어 동작시키는 기능을 제공하는 소프트웨어 프레임워크
 
WAS는 WebServer와 Web Container가 합쳐진 형태로서,  
> Web Server : 정적인 컨텐츠 처리    
> Web Container : 동적인 컨텐츠 처리 (Web Server가 보낸 JSP, PHP 등의 파일을 수행한 결과를 다시 웹 서버로 보내주는 역할을 함)   

> *web Application : 웹 브라우저에서 이용할 수 있는 응용 소프트웨어*  

WAS는 WebServer 단독으로는 처리할 수 없는 비즈니스 로직이나 데이터베이스의 조회 등 다양한 로직 처리가 필요한 **동적 컨텐츠**를 제공한다.  

덕분에 사용자의 다양한 요구에 맞춰 웹 서비스를 제공할 수 있다.  
WAS는 JSP, Servlet 구동환경을 제공해주기 때문에 웹 컨테이너 혹은 서블릿 컨테이너라고도 불린다.  

ex) Tomcat, Jetty, Undertow  
![image](https://user-images.githubusercontent.com/68904159/196162677-ae04aa06-386c-49b1-acb4-52f52a0171f9.png)

## WebServer와 WAS의 차이
- WebServer : 정적인 컨텐츠
- WAS : 정적+동적인 컨텐츠

## WAS는 정적, 동적인 컨텐츠 모두 다룰수 있는데 WebServer를 사용하는 이유는?

WAS는 DB 조회 및 다양한 로직을 처리하는 데 집중해야 한다.   
따라서 단순한 정적 컨텐츠는 웹 서버에게 맡기며 기능을 분리시켜 서버 부하를 방지한다.   
만약 WAS가 정적 컨텐츠 요청까지 처리하면, 부하가 커지고 동적 컨텐츠 처리가 지연되면서 수행 속도가 느려지고 이로 인해 페이지 노출 시간이 늘어나는 문제가 발생하여 효율성이 크게 떨어진다.  
 
### 따라서 대규모 프로젝트에서는 Apache와 Tomcat을 함께 사용해 웹서버 따로, WAS 따로 구현한다.

## WebServer와 WAS를 둘 다 구현 했을 때의 장점
1. 기능을 분리하여 서버 부하를 방지 할 수 있습니다.  
- WAS는 DB조회 등 페이지를 만들기 위한 다양할 로직을 처리하는데, 단순한 정적 콘텐츠를 WAS에서 제공한다면 다른 작업에 사용하는 리소스들로 인해 지연이 생겨날 수 있다.
2. 물리적으로 분리하여 보안을 강화 할 수 있습니다.  
- SSL에 대한 암복호화 처리에 WebServer를 사용한다.
- 공격에 대해 WebServer를 앞단에 두어 중요한 정보가 담긴 DB나 로직까지 (WAS까지)전파되지 못하게 한다.
3. 여러대의 WAS를 연결 가능합니다.  
- LoadBalacing
> LoadBalacing : 부하분산 또는 로드 밸런싱은 컴퓨터 네트워크 기술의 일종으로 중앙처리장치 혹은 저장장치와 같은 컴퓨터 자원들에게 작업을 나누는 것을 의미한다.
- fail over(장애극복),fail back 유연하게 처리 가능
> failover : 실 운용환경(컴퓨터 서버, 시스템, 네트워크 등)에서 이상이 생겼을 때 대체 작동 또는 장애 극복(조치)을 위하여 예비 운용환경으로 자동전환되는 기능이다.  
> failback : 페일오버에 따라 전환된 운용환경을 장애 발생 전 상태로 되돌리는 처리  
- 대용량 웹 어플리케이션의 경우(여러 개의 서버 사용) Web Server와 WAS를 분리하여 무중단 운영을 위한 장애 극복에 쉽게 대응할 수 있다.
4. 다른 종류의 WAS로 서비스 가능합니다.  
- 하나의 서버에서 php Applicaion과 JAVA Application을 함께 사용할 수 있다.

## 왜 Tomcat이 아닌 Apache Tomcat인가?
앞에서 언급한대로 정적 컨텐츠를 처리하는 웹 서버에는 **Apache**가 있고, 동적 컨텐츠를 처리하는 WAS서버는 **Tomcat**이 있는데 Tomcat은 Apache Tomcat이라는 이름으로 많이 사용되어 혼란스러울 것이다. 붙여서 쓰는 이유는 2008년에 릴리즈 된 Tomcat 5.5 버전부터 **정적 컨텐츠를 처리하는 기능이 추가**되었는데, 이 기능이 순수 Apache를 사용하는 것에 비해 **성능적 차이가 전혀 없으며** Tomcat이 Apache의 기능을 포함하고 있기 때문에 Apache Tomcat이라고 부르고 있다.

## 참고 링크
[[Web] 웹 서버와 WAS의 차이를 쉽게 알아보자](https://codechasseur.tistory.com/m/25)
