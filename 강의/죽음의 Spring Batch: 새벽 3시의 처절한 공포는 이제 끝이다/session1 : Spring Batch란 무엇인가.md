# Session 1 

## Batch란?
배치처리는 대량의 데이터를 정해진 시간에 자동으로 일괄 처리하여 결과를 출력하는 작업. 시스템이 일을 하게 하는 것

1. 대용량 데이터 처리 : 수천, 수만 개의 데이터를 한 번에 처리
2. 정기적 실행 : 정해진 스케줄에 실행
3. 자동화 : 한 번 만들고 설정해두면 알아서 처리하는 자율 처리 시스템
4. 유한한 양의 데이터 처리 : 처리할 대상이 명확하여 결과를 예측할 수 있다.
5. 예측 가능한 부하 처리 : 시스템이 감당할 수 있는 만큼의 데이터를 처리하도록 설계한다. 


### 활용 사례
- 일일 정산 배치
- 대용량 데이터 마이그레이션
- 리포트 생성
- 데이터 정제
- 백업
- 데이터 통합


### 웹 어플리케이션과 차이

|        기준         |      웹 애플리케이션       |      배치 애플리케이션     |
|------|---|---|
| 실행 방식            | 사용자 요청 기반 (실시간)    | 스케줄 기반 (비대화식)      |
| 응답 속도            | 즉각적 피드백 필수         | 정확성과 완결성 우선        |
| 데이터 처리량         | 단일 요청 단위            | 대량 데이터 (수백,수천만건)  |
| 오류 처리            | 즉시 에러 응답 반환        | 재시도 및 복구 중심         |
| 리소스 사용          | 상시 실행 (대기상태유지)     | 필요시에만 실행 후 종료      |
| 사용자 상호작용       | 실시간 상호작용            | 비대화식 자동 처리          |
| 처리 우선순위         | 응답 시간 최적화           | 처리량 및 신뢰성 최적화       |

## Spring Batch

익숙한 스프링의 기술로 배치 처리의 일반적인 패턴을 손쉽게 구현할 수 있게 해준다. 

#### 유지보수성
스프링의 장점을 계승하여 익숙한 기술들로 유지보수하기 쉬운 배치 처리 시스템을 만들 수 있다.
- 의존성 주입(DI) : 컴포넌트 간 결합도를 낮춘다.
- 관점 지향 프로그래밍(AOP) : 횡단 관심사를 완벽하게 분리
- 추상화 계층 : 특정 기술에 대한 종속성 차단. 기술이 변해도 코드는 살아남는다.

스프링 배치는 스프링 웹 처럼 일반적인 배치 처리의 패턴을 추상화한 컴포넌트들을 제공한다. 그러한 명확한 구조 덕분에 배치 코드를 쉽게 이해하고 유지보수 할 수 있다.



#### 유연한 데이터 처리
ItemReader와 ItemWriter라는 표준화된 인터페이스를 통해 어떤 데이터 소스든 일관된 방식으로 처리할 수 있다. 
스프링 배치가 이미 대부분의 데이터 소스를 위한 ItemReader/Writer 구현체를 제공하여 제공된 구현체의 설젖ㅇ만으로 원하는 데이터 소스를 쉽게 다룰 수 있다.

- 플랫 파일 (CSV, TXT): FlatFileItemReader, FlatFileItemWriter
- XML/JSON: StaxEventItemReader, JsonItemReader, JsonFileItemWriter 등
- RDB : JdbcCursorItemReader, JdbcBatchItemWriter 등으로 DB 접근. JPA와 같은 ORM 프레임워크도 지원
- NoSQL: MongoDB, Redis 등을 위한 전용 Reader/Writer 제공
- 메시징 시스템: KAFKA, AMQP 등 메시지 큐 시스템과의 연동 지원

#### 트랜잭션 관리
- 체크포인트 기능
	- 긴 배치 작업 중간에 안전 지점을 설정
	- 문제 발생시 마지막 체크 포인트부터 재시작 가능
- 유연한 트랜잭션 범위 설정
	- 처리할 데이터 양과 특성에 따라 트랜잭션 범위 조절 가능
	- 작은 단위의 커밋을 메모리 사용량 조절 가능

#### 재시작 기능과 유연한 실행 제어
스프링 배치는 작업 실행을 세밀하게 제어할 수 있는 기능을 제공한다.
- 재시작 기능 : 대용량 처리를 실패해도 처음부터 다시 시작할 필요가 없다. 
	- 오류 발생 시 마지막으로 성공한 step부터 재시작 가능
	- step 내에서도 마지막 처리 항목 이후 작업 재개 가능
- 유연한 실행 제어
	- 특정 step만 선택적으로 실행
	- 파라미터를 통한 동적 작업 제어
	- 이전 step의 결과에 따라 다음 작업 결정 
- 작업 상태 추적
	- 모든 job과 step의 실행 상태를 메타데이터로 관리
	- 작업의 실행 시점과 결과를 정확하게 추적

#### 대용량 데이터 처리와 확장성
다양한 규모의 작업에 유연하게 대응할 수 있도록 기능을 제공한다.
- Chunk 지향 처리
	- 대량의 데이터를 나눠 순차적으로 처리
	- 한 번에 처리하는 데이터양을 제한하여 메모리 최적화
 - 효육적인 데이터 읽기
	- 페이징 방식
	- 커서 방식
- 확장성 기법 (Scalability Techniques)
	- 멀티 스레드 Step: 하나의 step을 여러 스레드로 처리
	- 병렬 Step: 여러 step을 동시에 실행
	- 분산 처리 : 여러 서버에서 배치 처리 분산 실행


#### 데이스 용이성
- 전용 테스트 도구 
- 스프링 테스트 지원
- 단위 테스트 

## 주요 컴포넌트
### Job : 배치 처리의 가장 큰 단위
하나의 완전한 배치 처리를 의미. 
- 일일 매출 집계
- 휴먼 회원 정리
- 정기 결제
- 대용량 데이터 이관 등등..

### Step : Job을 구성하는 실행 단위
Job은 하나 이상의 Step으로 구성된다. 예를 들어 '일일 매출 집계' Job은 다음과 같은 Step으로 이뤄질 수 있다.
1. 매출 집계 step : 전일 주문 데이터를 읽고(read) 결제 완료건을 필터링해(process) 카테고리 별로 집계해서 저장(write)한다.
2. 알람 발송 step : 집계 요약 정보를 관리자에게 전달
3. 캐시 갱신 step : 집계된 데이터로 캐시 정보 업데이트

## Spring Batch 전체 시스템 훑어보기
### Spring Batch가 제공하는 영역
- Job과 Step
- JobLauncher : Job을 실행하고 필요한 파라미터를 전달하는 역할. 배치 작업의 시작점
- JobRepository : 배치 처리의 모든 메터데이터를 관리하는 저장소. 배치 작업 모니터링이나 재실행에 활용된다.
- ExecutionContext : Job과 Step 실행 중의 상태 정보를 key-value 형태로 담는 객체. Job과 Step 간의 데이터 공유나 Job 재시작 시 상태 복원에 사용된다.
- 데이터 처리 컴포넌트 구현체
-   - ItemReader 구현체 : JdbcCursorItemReader, JpaPagingItemReader, MongoCursorItemReader 등 
    - ItemWriter 구현체 : JdbcBatchItemWriter, JpaItemWriter, MongoItemWriter 등
 
### 개발자 제어 영역
#### Job/Step 구성
- @Configuration을 사용해 Job과 Step의 실행 흐름을 정의한다.
- Spring의 DI(의존성 주입)를 활용해 ItemReader, ItemProcessor, ItemWriter 등 배치 작업에 필요한 컴포넌트들을 조합하고 배치 플로우를 완성한다.

#### 데이터 처리 컴포넌트 활용
- Spring Batch에서 제공하는 데이터 처리 컴포넌트의 구현체를 사용하지만 파일 포맷이나 SQL 쿼리 조건 등 세부 로직은 개발자가 직접 지정한다.

#### 단순 작업 처리
- 모든 배치 Job이 데이터를 읽고쓰는 방식로만 구성되지는 않는다. 때로는 파일 복사, 디렉토리 저장, 알림 발송과 같은 단순 작업이 필요하다. 이를 구현하는 것은 개발자의 영역 (tasklet)

#### 커스텀 데이터 처리 컴포넌트
- Spring Batch가 여러 구현체를 제공하지만, 모든 DB나 포멧을 커버하지는 않는다. 이 처럼 Spring Batch가 제공하지 않는 데이터 소스를 다뤄야 하는 경우 ItemReader나 ItemWriter를 개발자가 직접 구현해야한다.
- 데이터를 가공하는 ItemProcessor는 비즈니스 로직의 핵심을 담당하므로, 개발자가 직접 구현해야한다.

## 실행(execute)해보기! 
### 1. 프로젝트 구성하기
`spring-boot-starter-batch` 의존성 추가하기. 
```gradle
plugins {
	id 'java'
	id 'org.springframework.boot' version '3.4.7'
	id 'io.spring.dependency-management' version '1.1.7'
}

group = 'com.system.batch'
version = '0.0.1-SNAPSHOT'

java {
    toolchain {
        languageVersion = JavaLanguageVersion.of(17)
    }
}

repositories {
    mavenCentral()
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-batch'
    runtimeOnly 'com.h2database:h2'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    testImplementation 'org.springframework.batch:spring-batch-test'
    testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
}

tasks.named('test') {
    useJUnitPlatform()
}
```
`spring-boot-starter-batch`는 Spring Batch 개발에 필요한 모든 의존성을 포함하는 스타터 패키지이다. `spring-batch-core`는 물론, Spring Boot 자동 설정 기능을 제공한다.


 Spring Boot 환경에서는 JobLauncherApplicationRunner라는 별도의 컴포넌트가 자동으로 배치 Job 실행을 담당한다.
 ```Java
 @SpringBootApplication
public class KillBatchSystemApplication {
    public static void main(String[] args) {
        System.exit(SpringApplication.exit(SpringApplication.run(KillBatchSystemApplication.class, args)));
    }
}
 ```
배치 애플리케이션에서는 이와 같이 SpringApplication.run()의 결과를 System.exit()로 처리하는 것이 권장된다. 배치 작업의 성공/실패 상태를 exit code로 외부 시스템에 전달할 수 있어 실무에서 배치 모니터링과 제어에 필수적이기 때문이다.

### 2. 코드 구성
- Spring Batch의 핵심 컴포넌트들은 자동으로 구성되지만, 몇 가지 필수 인프라 Bean들은 직접 등록해줘야 한다.
  - `DataSource`, `PlatformTransactionManager` bean 등록 + `DefaultBatchConfiguration` 상속 (JobRepository, JobLauncher 등 Spring Batch의 핵심 컴포넌트들을 자동으로 구성) 
  - Spring Boot를 사용시 Boot가 배치 코어 컴포넌트와 관련된 핵심 인프라 Bean들을 자동으로 구성해주기때문에 별도 BatchConfig를 구성할 필요없다.
- Spring Batch는 Job을 실행할 때 Spring 컨테이너에서 Job 빈을 찾아 실행하기 때문에, Job은 반드시 Bean으로 등록해주도록 한다.

### 3. 예제 실행
Spring Boot 환경에서는 bootRun task를 사용해 배치 Job을 실행할 수 있다.
```
./gradlew bootRun --args='--spring.batch.job.name=systemTerminationSimulationJob'
```
`--spring.batch.job.name` 프로퍼티에 실행할 Job의 이름을 지정하면 **JobLauncherApplicationRunner**가 `spring.batch.job.name`프로퍼티에 지정된 Job을 찾아 실행해준다.
