# NestJS

## 개요

> 전체 내용은 공식 문서를 바탕으로 추가적으로 학습한 내용을 기술함 <br>
> Nest에 대한 기본 개념 정의

### 정의
NestJS 공식 문서 링크: <a href="https://docs.nestjs.com/">바로가기</a>

NestJS는 효율적이고 안정적이며 확장가능한 서버 애플리케이션을 구축하기 위한 Node.js 프레임워크 중 하나이다.

### 탄생 배경

나를 포함한 Node.js를 사용해본 많은 사람들이 아키텍처 구조에 대한 고민을 했을 것이다. 아키텍처에 정답이 없다보니 토이 프로젝트나 프로덕션 개발할 때마다 구조가 바뀌는 경우가 많았다..
그리고 여러 오픈 프로젝트나 boilerplate를 항상 참고하고 공부 하면서 어떤게 좋은지 고민을 해왔다.

Nest 역시 Node.js의 주요 문제를 아키텍처라고 언급하고 있다. 이러한 문제를 해결하고자 노력했으며 Angular 프레임워크로 부터 영감을 받아 NestJS를 만들었다고 한다.

## NestJS 이점

1. 모듈식 아키텍처
	- 모듈, 미들웨어, 컨트롤러, 서비스 등 정형화된 아키텍처로 개발 촉진
2. Typescript 지원
	- 타입 검사나 코드 자동 완성 등 가능 (Node.js도 가능)
3. 강력한 의존성 주입 시스템 제공
	- 모듈 간의 유연한 결합
	- 코드 유연성과 테스트 용이
4. 데코레이터 사용
	- 클래스, 메서드, 속성에 데코레이터 지원
5. 객체 지향 프로그래밍(OOP), 함수형 프로그래밍(FP), FRP(함수형 반응형 프로그래밍)의 개념 결합 사용
6. 엔터프라이즈급 애플리케이션 구축에 적합

이러한 이점으로 Node.js 대비 유지보수와 생산성이 좋아서 많은 기업에서도 사용 중

 
## 기본 구조

**모듈, 컨트롤러, 서비스** 이 세가지로 구분됨
일반적으로 많이 사용하는 구조다보니 플로우도 아래와 같음

>클라이언트 요청은 컨트롤러에서 처리하여 요청 사항을 서비스로 전달
서비스에서는 요청사항을 처리하여 응답

NestJs가 가진 차이점은 컨트롤러와 서비스가 동작하기 위해서는 **모듈에 등록**을 해야한다는 것과 
**공급자(providers)**라는 개념이 있다.

간단하게 각 요소에 대한 개요 설명하고 디테일적인 부분은 추후 게시
### 1. 컨트롤러 (Controller)

![](https://docs.nestjs.com/assets/Controllers_1.png)

- 애플리케이션에 대한 특정 HTTP 요청을 처리하고 응답을 반환하는 역할
- 특정 URI 엔드포인트와 HTTP 요청 메서드를 처리하는 메서드를 정의
- 컨트롤러를 만들려면 **클래스**와 **데코레이터**를 사용

#### Routing

Controller를 정의하려면 ```@Controller()```라는 데코레이터를 사용해야 한다.
모든 표준 HTTP 메서드 데코레이터를 제공함 

```javascript
@Controller('cats')
export class CatsController {
	@Get()
	findAll(): string {
		return 'This action returns all cats';
	}
	@Post()
	create(): string {
		return 'create'
	}
	@Put()
	update(): string {
		return 'update'
	}
	@Delete()
	remove(): string {
		return 'remove'
	}
}
```


#### 매개변수와 쿼리스트링

##### @Param
- 경로 상의 동적 매개 변수
- 매개변수가 있는 경로는 정적 경로 뒤에 선언

```javascript
// /test/cats/1
@Get(':id')
get(@Param() params: any): string {
  console.log(params.id);
  return `This action returns a #${params.id} cat`;
}

// /hangyeol?country=korea
@Get(':name')
get(
	@Param('name') name: string,
	@Query('country') country: string
) {
	return `my name is ${name} from ${country}`
}
```

##### @Query
- 경로에서 쿼리 매개변수를 처리할 때 사용
- 여러 개의 매개변수가 있는 경우, 순서대로 정의

```javascript
// /cats?age=2&breed=Persian
@Get()
async findAll(@Query('age') age: number, @Query('breed') breed: string) {
  return `This action returns all cats filtered by age: ${age} and breed: ${breed}`;
}
```

#### 기타

@Request (또는 @Req) 데코레이터도 존재해서 Request 객체 접근도 가능하며 자주 사용하는 요소는 간단하게 @Body처럼 되어 있다.

이 외에도 다양한 데코레이터는 추후 게시

### 2. 서비스 (Service)

- 컨트롤러에서 사용할 일반적인 비즈니스 로직 담당
- 컨트롤러가 클라이언트 요청을 처리하는데 필요한 작업 처리
- 데이터베이스의 데이터를 가져오거나 외부 API 호출 등 데이터 처리
- 컨트롤러와 같은 **클래스**이며, ```@injectable``` 데코레이터를 사용하여 주입가능한 상태로 변환

```javascript
@Injectable()
export class CatsService {
  private readonly cats: Cat[] = [];

  findAll(): Cat[] {
    return this.cats;
  }
}
```

해당 클래스는 IoC Container (NestJS 런타임 시스템)에서 관리하며 다른 곳에서 사용가능하도록 주입가능한 상태로 변환 필요

서비스를 생성하고 컨트롤러에 주입하는 것이 끝이 아니라 모듈 즉, Nest IoC 컨테이너에 등록하는 것도 필요한데 모듈과 의존성 주입에서 설명하겠다.

### 3. 모듈 (Module)과 providers

![Nest가 모듈과 공급자 간의 관계와 종속성을 해결하는 데 사용하는 내부 구조](https://docs.nestjs.com/assets/Modules_1.png)

#### (1) 정의

모듈은 ```@Module()``` 데코레이터를 사용하는 클래스이다.

- 모듈을 사용하여 애플리케이션을 구성
- 모든 애플리케이션에는 최소 하나의 모듈 (root module)이 있음
- 애플리케이션에 필요한 모든 컨트롤러, 서비스, 프로바이더 및 미들웨어 등을 모듈에 등록되어야 사용 가능
- 간단하게 특정 기능 또는 비즈니스 로직을 담당하는 컴포넌트 집합라고 할 수 있음 

#### (2) 구성요소

모듈을 구성, 설명하는 속성을 갖는 객체는 4가지
- providers
- controllers
- imports
- exports

##### 1) 공급자 (providers)

Nest.js의 기본 개념이며, 서비스, 리포지토리, 팩토리, 헬퍼 등 프로바이더로 취급
이 프로바이더를 사용하여 의존성 주입할 수 있으며, 인스턴스 간 서로 다양한 관계를 만들 수 있다.
(인스턴스를 연결하는 기능은 Nest 런타임에 위임)

- 모듈이 생성하고 IoC 컨테이너에 추가할 클래스 인스턴스 또는 값의 배열
- 주로 서비스와 리포지토리 등을 포함하는데, **서비스**는 애플리케이션의 논리를 관리하는 역할을 하고 있기 때문에 <u>공급자로 정의하기에 이상적임</u>
##### 2) controllers

모듈이 정의하는 컨트롤러의 배열
##### 3) imports

해당 모듈이 의존하는 다른 모듈의 배열
가져온 모듈들을 현재 모듈의 providers와 controllers가 사용할 수 있도록 제공
##### 4) exports

해당 모듈에서 제공되며 다른 모듈에서 import하여 사용할 수 있는 providers의 배열 즉, 공급자의 하위 집합을 의미함

#### (3) 기능 모듈 (Feature Modules)

```javascript
import { Module } from '@nestjs/common';
import { UsersController } from './users.controllers';
import { UsersService } from './users.service';

@Module({
	controllers: [UsersController],
	providers: [UsersService]
})
export class UsersModule {}
```


- 애플리케이션의 특정 기능을 캡슐화하는 것을 뜻함
- 특정 기능(요소)과 관련된 코드를 구성하여 명확한 경계와 더 나은 구성을 유지하는데 도움을 줌

이는 애플리케이션이나 팀이 성장함에 따라 특히 중요하며 이는 SOLID 원칙과 일치한다.
ex) 사용자관리, 상품관리, 주문처리 등 특정 기능에 대해 컨트롤러, 서비스, 리포지토리 등 그룹화

```javascript

import { Module } from '@nestjs/common';
import { UsersModule } from './users/users.module';

@Module({
  imports: [UsersModule],
})
export class AppModule {}

```

정의한 모듈을 루트 모듈 (AppModule)로 가져와서 import하면 끝

#### (4) 공유 모듈 (Shared Moduels)

![](https://docs.nestjs.com/assets/Shared_Module_1.png)

애플리케이션 전반에 공유되는 기능을 제공
예) 데이터베이스 접속, 로깅 인증 등 공통적인 작업을 수행하는 기능들을 shared 모듈로 구성

Nest에서 모듈은 기본적으로 **싱글톤** 이므로 여러 모듈에서 모든 공급자의 동일한 인스턴스를 손쉽게 공유할 수 있음

특정 서비스의 인스턴스를 다른 모듈 간에 공유하고 싶다면 <u>모듈의 exports 배열</u>에 추가해야 함

```javascript

import { Module } from '@nestjs/common';
import { UsersController } from './users.controller';
import { UsersService } from './users.service';

@Module({
  controllers: [UsersController],
  providers: [UsersService],
  exports: [UsersService]
})
export class UsersModule {}
```

UsersModule을 가져오는 모든 모듈은 UsersService에 액세스할 수 있으며, UsersModule을 가져오는 다른 모든 모듈과 동일한 인스턴스를 공유한다.

물론 UsersService가 필요한 모듈마다 직접 등록해도 되지만 이렇게 되면 모듈마다 UsersService의 인스턴스가 생성되기 때문에 메모리 사용량 증가나 상태 불일치와 같은 이상 동작을 초래할 수 있다.

공유 모듈을 통해 특정 서비스를 캡슐화하고 내보냄으로써 해당 모듈을 가져오는 모든 모듈에서 동일한 서비스 인스턴스가 재사용 되도록 보장한다.

동일한 인스턴스를 공유하므로 공유 상태나 리소스를 더 쉽게 관리할 수 있도록 하며 예측 가능한 동작으로 이어진다. 이를 통해 애플리케이션 전체에서 서비스를 효율적으로 공유할 수 있게 한다.

```javascript
import { Module } from '@nestsjs/commmon';
import { CatsController } from './users.controller';
import { CatsService } from './users.service';
import { UsersModule } from '../users/users.module';

@Module({
	imports: [UsersModule],
	controllers: [UsersController],
	providers: [UsersService]
})
export class UsersModule {}
```

공유 모듈을 생성 했다면 일반 기능 모듈에서 ```imports```로 공유 모듈을 사용할 수 있음


기능 모듈에서 imports로 공유 모듈을 사용

#### (5) 글로벌 모듈 (Global Module)

- 데이터베이스 연결 등 애플리케이션 전역적으로 사용되는 모듈은 ```@Global```데코레이터를 명시하여 사용
- 해당 데코레이터가 명시되어 있는 모듈은 imports 없이 사용가능
- 애플리케이션의 루트나 코어 부분에 구현

```javascript
import { Module, Global } from '@nestjs/common';
import { CatsController } from './cats.controller';
import { CatsService } from './cats.service';

@Global()
@Module({
  controllers: [CatsController],
  providers: [CatsService],
  exports: [CatsService],
})
export class CatsModule {}
```

일반적으로 ```imports``` 배열을 사용하여 모듈을 다른 모듈에서 제어되고 명확한 방식으로 사용할 수 있도록 하는 것이 좋다. 이러한 방식은 모듈의 필요한 부분만 다른 모듈과 공유하여 애플리케이션간 불필요한 결합을 피할 수 있다.

## 의존성 주입 (DI)

SW 엔지니어링 디자인 패턴 중 하나로 모듈 간 높은 결합도를 줄이고 유연성과 재사용성을 높이고자 나온 패턴임
특정 클래스가 의존하고 있는 다른 클래스나 컴포넌트를 직접 만들지 않고 외부(매개체) 에서 주입받아 사용

- 클래스는 필요한 의존성을 명시적으로 정의 (생성자, 매개변수를 통함)
-  DI 컨테이너 또는 IoC(Inversion of Control) 컨테이너는 의존성을 관리. 이 컨테이너는 필요한 의존성을 찾아 인스턴스를 생성하고 요청한 클래스에 주입
- 클래스는 직접적으로 의존성을 생성하거나 관리할 필요 없이 해당 의존성을 사용할 수 있음

```javascript
import { Controller, Get, Post, Body } from '@nestjs/common';
import { CatsService } from './cats.service';
import { Cat } from './interfaces/cat.interface';

@Controller('cats')
export class CatsController {
  constructor(private catsService: CatsService) {}
  @Post()
  async create(@Body() cat: Cat) {
    this.catsService.create(cat);
  }
  
  @Get()
  async findAll(): Promise<Cat[]> {
    return this.catsService.findAll();
  }
}
```

이후에 Controller 클래스에 CatsService 공급자 의존성을 주입

CatService의 인스턴스는 NestJS 프레임워크에서 생성하여 private 형태로 CatsController 주입된다.
이렇게 하면 실제 서비스 로직 없이 컨트롤러 테스트도 가능하다.

**단, 모듈에서 providers에 명시되지 않은 Service는 의존성 주입을 할 수 없음**