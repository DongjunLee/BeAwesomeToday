# AWS 5월 월간 웨비나 - AWS Lambda와 API Gateway를 통한 Serverless Architecture 특집 (윤석찬)

## 배포의 변화
1. Amazon EC2  
2. 컨테이너를 활용하여 빠르게 배포 ( Containers Services -  Docker )  
3. Serverless - AWS Lambda - Node.js, Python 2.7 제공
	코드만 업로드하고, 그것으로 애플리케이션이 작동 
	Millisec 단위로 배포. 과금도
	
이전에는 Auto Scailing을 통해 EC2 인스턴스관리
컨테니어로 각각의 자원을 효율적으로 관기

**S3** - 무제한 용량의 내구성 높은 객체 스토리지, 단순하게 파일 업로드/다운로드 등등..
속도 빠르고 비용 쌈, Static 파일에 유용.
이미 잘 사용하고 있었음.

개발 블록이 존재했음.

**Dynamic DB** - read/write 의 capacity를 조절.
NoSQL, 

FUNTIONS - **AWS LAMBDA**  
서버없는, 이벤트 처리 방식의 컴퓨팅 서비스  
Lambda = 클라우드 함수 기반 마이크로 서비스

- Node.js, Java, Python
- 라이브러리 업로드 해서 사용가능
- 메모리설정에 따라 CPU 자원 할당 됨.

**API Gateway**

Trigger 방식.

Demo.

마이크로서비스.  
Lambda를 API로 통신  
작은 서비스간 인터랙션 결합 제거, API로 통신  

그래서 API Gateway를 통해서 REST 서비스를 관리  
CDN 네트워크를 통해..  

Swagger 로 API 표준 관리.  

API Gateway + AWS Lambda + Dynamic DB  
API를 통해서 통신을 해서 백엔드 구축.  

Severless Framework  
Claudia.js  

일단 기본은 Node.js 로 되어 있음.  

저 프레임워크를 공부하고 실행해 보아도 좋을 듯.  
AWS를 사용하는 방법은 기본적으로 잘 알아주자.  
Serverless Architecture 를 배워서 해보자!  
