# AWS Lambda Tips

## 1. Memory

- Lambda 과금 = 실행 시간 X 사용한 메모리
- 인스턴스의 성능은 메모리 설정에 비례해서 CPU 성능이 결정.
- 예시) 같은 Task에 관해서 
	- 1024 MB => 52.54 ms
	- 128 MB => 462.23 ms

## 2. Warm Start

- Lambda를 실행하면 해당 코드를 EC2 컨테이너에서 수행한다.
- 최초 수행시 cold start(해당 자원을 할당받는 시간)으로 시간이 좀 걸린다.
- 이미 작업이 돌아가고 있는 것에 비슷한 Input이 들어오면 이어서 작업이 진행된다.
- [CloudWatch Schedule Event](http://docs.aws.amazon.com/ko_kr/AmazonCloudWatch/latest/events/ScheduledEvents.html)를 통해서 반복을 호출을 통해 Lambda를 항상 컨테이너 위에 떠있게 할 수 있지만, 호출에 따라서 새로운 컨테이너에 올라가서 cold start에 걸린다

## 3. Re-use Lambda

- 전역 변수를 선언한 경우, warm start(이미 실행한 컨테이너에서 재 실행) 에서 그 값을 계속 가지고 있음.
- 이를 잘 활용하면 수행시간을 짧게 할 수 있으나, 사이드 이펙트가 있을 수 있음

## 4. Lambda Limit

- 각 Region 별로 Lambda는 100개 동시 수행가능 (기본설정)
	- 이를 넘는 경우, SQS를 이용. (SQS에서 동시 실행하는 Lambda의 수를 고려해서 호출)
	- [관련수식](http://docs.aws.amazon.com/ko_kr/lambda/latest/dg/concurrent-executions.html)
	- Concurrent Invocations = events (or requests) per second * function duration (in secs)
- Lambda로 넘길 수 있는 Request Body의 최대 크기는 6MB.
	- 이를 넘는 경우, SNS를 이용. (필터링을 통해서 크기를 줄인 뒤 호출)
- 그 외 API Gateway 초당 1000번, EC2의 Network Interface 350

## 5. Devide Batch Task for Lambda

- Lambda로 Batch 작업을 나눠서 하는 경우
	- 연산이 많아서 CPU 사용량이 많은 경우 => Lambda를 잘게 나눠라.
	- Lambda 자체 연산보다는 외부 I/O 작업이 많은 경우 => Lambda 하나의 수행시간을 길게 가져라. 
 
## 6. Logging in Lambda

- CloudWatch 에 자동 저장되게 하는 방법
	- Node.JS => console.log, console.info
	- .NET core => Consol.eWriteline
	- Java => log4j
	- Etc..
- CloudeWatch 는 유료이기 때문에.. 특정 기간 이후에 것을 S3에 보관하도록 하자.

## 7. Encryption

- Java의 경우, KMSEncryption을 사용한다면 JCE 오류 발생 (JVM 설정을 통해 암호화제한을 풀어줘야 함)
	- [aws-encryption-sdk-java](https://github.com/awslabs/aws-encryption-sdk-java)를 사용하면 문제가 해결 된다.
	- AmazonS3EncryptionClient 를 사용.

## Reference

- [AWS Lambda 사용에 관련된 Tip](http://seokjoonyun.blogspot.kr/2017/03/aws-lambda-tip.html)