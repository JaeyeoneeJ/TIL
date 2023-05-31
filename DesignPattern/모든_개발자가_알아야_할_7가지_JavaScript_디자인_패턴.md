> 이 글은 Amy J. Andrews가 Oct 20, 2020에 작성한 원문인
> **7 JavaScript Design Patterns Every Developer Should Know**을 번역한 글입니다.
>
> 원본 링크: https://javascript.plainenglish.io/7-javascript-design-patterns-every-developer-should-know-df9c40e7debf

부제: 같은 종류의 모든 프로젝트에 적용할 수 있는 아름다운 템플릿처럼 소스 코드를 구성할 수 있다면 어떨까?

- 모든 프로젝트를 위한 템플릿을 잘 작성하고 달성하기 위한 방법 중 하나는 잘 구조화되고 아름답고, 조직화된 코드를 작성하는 데 도움이 되는 디자인 패턴을 사용하는 것임
- 우리는 이 글에서 JavaScript에서 사용되는 일반적인 디자인 패턴 중 일부를 발견할 것임

## 1. Constructor Pattern(생성자 패턴)
- 특정 속성 및 메서드를 사용하여 개체를 초기화하는 함수인 생성자에 대해 우리는 잘 알고 있음
- 생성자 패턴은 해당 정의와 유사함. 이 패턴을 사용하여 동일한 개체의 여러 인스턴스를 만듦
- JavaScript에서 새 객체를 만드는 방법에는 여러 가지가 있음. 아래 예를 살펴보겠음
```javascript
// {}를 사용하여 빈 객체 생성:
let person = {};

// Object()를 사용하여 빈 객체 생성:
let person = new Object();

// 함수 생성자 사용:
function Person(name, age) {
  this.name = name;
  this.age = age;
  this.showName = () => console.log(this.name);
}

let person = new Person('Amy', 28);
person.showName();
```

## 2. Prototype Pattern(프로토타입 패턴)
- 프로토타입 패턴은 객체 기반 생성 디자인 패턴임
- 프로토타입에서 객체를 복제하여 객체의 새 인스턴스를 만듦
- 프로토타입 패턴의 주요 초점은 생성된 각 객체 생성자에 대한 청사진으로 사용되는 객체를 만드는 것임
- 새로운 객체를 직접 생성하는 것이 복잡하고 비효율적이라면 프로토타입 패턴이 큰 도움이 됨
- 아래와 같은 고전적인 방식으로 프로토타입 패턴을 구현할 수 있음
```javascript
function Book (title, price) {
  this.title = title;
  this.price = price;
  this.printTitle = () => console.log(this.title);
}

function BookPrototype(prototype) {
  this.prototype = prototype;

  this.clone = () => {
    let book = new Book();
    book.title = prototype.title;
    book.price = prototype.price;

    return book;
  };
}

let sampleBook = new Book('JavaScript', 15);
let prototype = new BookPrototype(sampleBook);
let book = prototype.clone();
book.printTitle();
```

- JavaScript에는 자체 프로토타입 기능이 내장되어 있으므로 아래와 같이 보다 효과적인 패턴을 사용할 수 있음

```javascript
let book = {
  title: 'JavaScript',
  price: 15
}

let anotherBook = Object.assign({}, book);
```

- 프로토타입으로 사용된 첫 번째 책이 있음. 그런 다음 새 책이 프로토타입 값으로 초기화된 모든 속성 값을 갖도록 해당 프로토타입을 사용하여 새 책 인스턴스를 만듦

## 3. Command Pattern(커맨드 패턴)
- 이 패턴의 주요 목적은 액션이나 오퍼레이션을 객체로 캡슐화하는 것임
- 전자 상거래 상점을 위한 결제 시스템을 구축하기 위한 요구 사항이 있다고 생각해보자. 선택한 결제 방법에 따라 처리할 특정 프로세스가 있음

```javascript
if (selectedPayment === 'creditcard') {
  // handle payment by creditcard
}
```

- 일부 결제 방법에는 한 단계만 처리되지만 다른 결제 방법에는 한 단계 이상이 있을 수 있음. 위의 예제 코드를 사용하면 긴밀한 결합을 유발하는 인터페이스가 아닌 구현을 제공하게 됨
- 명령 패턴은 느슨한 결합을 제공하는 훌륭한 솔루션임. 시스템은 각각의 특정 지불 방법 처리에 대한 많은 정보를 알 수 없어야 함. 이를 달성하기 위해 이 패턴은 실제 구현을 실행하는 코드에서 작업을 요청하는 코드를 분리함
```javascript
function Command(operation) {
  this.operation = operation;
}

Command.prototype.execute = function () {
  this.operation.execute();
}

function ProcessCreditCardPayment() {
  return {
    execute: function() {
      console.log('Credit Card');
    }
  };
}

function ProcessPayPalPayment() {
  return {
    execute: function() {
      console.log('PayPal');
    }
  };
}

function ProcessStripePayment() {
  return {
    execute: function() {
      console.log('Stripe');
    }
  };
}

function CreditCardCommand() {
  return new Command(new ProcessCreditCardPayment());
}

function PayPalCommand() {
  return new Command(new ProcessPayPalPayment());
}

function StripeCommand() {
  return new Command(new ProcessStripePayment());
}

function PaymentSystem() {
  let paymentCommand;

  return {
    setPaymentCommand: function(command) {
      paymentCommand = command;
    },
    executeCommand: function() {
      paymentCommand.execute();
    }
  };
}

function run() {
  let paymentSystem = new PaymentSystem();
  paymentSystem.setPaymentCommand(new CreditCardCommand());
  paymentSystem.executeCommand();
  paymentSystem.setPaymentCommand(new PayPalCommand());
  paymentSystem.executeCommand();
  paymentSystem.setPaymentCommand(new StripeCommand());
  paymentSystem.executeCommand();
}
```

## 4. Observer Pattern(관찰자 패턴)
- 뉴스레터를 구독한 적이 있다면 소유자가 이메일을 보낼 때 마다 알림을 받게 되는 것을 알 수 있음
- 관찰자 패턴은 동일한 메커니즘으로 작동함. 이벤트를 구독하고 해당 이벤트가 발생하면 알림을 받는 구독 모델을 제공함

```javascript
function Newsletter() {
  this.observers = [];
}

NewsLetter.prototype = {
  subscribe: function(observer) {
    this.observers.push(observer);
  },
  unsubscribe: function(observer) {
    this.observers = this.observers.filter(ob => ob !== observer);
  },
  notify: function() {
    this.observers.forEach(observer => console.log('Hello ' + observer.toString()));
  }
}

let subscriber1 = 'Subscriber 1';
let subscriber2 = 'Subscriber 2';
let newsletter = new Newsletter();

newsletter.subscribe(subscriber1);
newsletter.subscribe(subscriber2);

newsletter.notify(); // Hello Subscriber 1 Hello Subscriber 2

newsletter.unsubscribe(subscriber1);

newsletter.notify(); // Hello Subscriber 1
```

## 5. Singleton Pattern(싱글톤 패턴)
- 이 것은 가장 잘 알려진 패턴임. 이 패턴을 사용하여 클래스가 인스턴스를 하나만 가지고 전역적으로 액세스할 수 있도록 제한함
- 전체 애플리케이션의 모든 위치에서 특정 작업을 처리할 무언가가 필요할 때 매우 유용함

```javascript
const utils = (function () {
  let instance;

  function initialize() {
    return {
      sum: function(a, b) {
        return a + b;
      }
    };
  }

  return {
    getInstance: function() {
      if (!instance) {
        instance = initialize();
      }

      return instance;
    }
  };
})();

let sum = utils.getInstance().sum(3, 5); // 8
```

- 편리하긴 하지만 코드를 테스트하기 어렵게 만드는 단점이 있기 때문에 이 패턴을 현명하게 사용해야 함

## 6. Module Pattern(모듈 패턴)
- 이 패턴은 소프트웨어 모듈의 개념을 구현하는 데 사용되는 디자인 패턴임. 모듈 패턴은 강력한 패턴이며 일반적으로 JavaScript에서 사용됨
- JavaScript 라이브러리 내부를 보면 이 패턴이 일반적으로 싱글톤 객체로 사용되는 것을 볼 수 있음. 가장 강력한 애플리케이션을 작성하기 위해 코드를 캡슐화하는 기능을 제공함
- 모듈 내부의 함수, 변수 및 속성을 공개 또는 비공개 멤버로 만들 수 있음

```javascript
const bookModule = (function() {
  // private
  let title = 'JavaScript';
  let price = 15;

  // public
  return {
    printTitle: function() {
      console.log(title);
    }
  }
})();

bookModule.printTitle(); // JavaScript
```

## 7. Factory Pattern(공장 패턴)
- 우리는 패턴을 사용할 때, 그 패턴에 대해 제대로 알지 못한 채 패턴을 사용했을 수 있음
- 공장 패턴은 공장처럼 작동함. JavaScript에서는 객체 생성을 나머지 코드와 분리함. 생성 코드를 래핑한 다음 API를 노출하여 다른 객체를 생성함

```javascript
const Vehicle = function() {};

const Car = function() {
  this.say = function() {
    console.log('I am a car');
  }
};

const Truck = function() {
  this.say = function() {
    console.log('I am a truck');
  }
};

const Bike = funciton() {
  this.say = function() {
    console.log('I am a bike');
  }
};

const VehicleFactory = function() {
  this.createVehicle = (vehicleType) => {
    let vehicle;

    switch (vehicleType) {
      case 'car':
        vehicle = new Car();
        break;
      case 'truck':
        vehicle = new Truck();
        break;
      case 'bike':
        vehicle = new Bike();
        break;
      default:
        vehicle = new Vehicle();
    }

    return vehicle;
  }
};

const vehicleFactory = new VehicleFactory();

let car = vehicleFactory.createVehicle('car')
let truck = vehicleFactory.createVehicle('truck')
let bike = vehicleFactory.createVehicle('bike')

car.say(); // I am a car
truck.say(); // I am a truck
bike.say(); // I am a bike
```

## > 결론
- 프로젝트에 디자인 패턴을 적용하는 것은 어려움. 마스터하기 위해서는 여러 번 시도하고 실패해야 함
- 하지만 노력은 보상을 줌. 디자인 패턴을 완전히 이해하고, 언제 어떤 패턴을 구현해야 하는지 알게 되면 코드를 가장 강력한 수준으로 끌어올릴 수 있음
- 위 내용은 일부 디자인 패턴의 간단한 예일 뿐임. 이 주제에 관심이 있다면 더 깊이 파고들어야 함


<hr>
## ref.
- https://javascript.plainenglish.io/7-javascript-design-patterns-every-developer-should-know-df9c40e7debf