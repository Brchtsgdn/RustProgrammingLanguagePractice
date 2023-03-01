# Chapter 03
이 장에서는 다른 언어들과 러스트가 어떻게 다른지를 다룬다.


## 1장
- vscode의 변수

## 2장
- vscode의 data Type

### 숫자
- 데이터 타입상 특이사항은 없음
- 오버플로우가 발생 할 여지를 알려줌
- --release flag로 오버플로우 체크를 안함 대신 실제로 오버플로우 발생
- 오버플로우가 발생 할 여지가 있으면 처리를 해줘야함

### 불
- 특이사항 없음

### 문자
- char -> ''
- string -> ""

### Compound Type

기본적으로 고정 길이를 가짐

1. 튜플

```
// 타입 무관
fn main() {
    let tup: (i32, f64, u8) = (500, 6.4, 1);
}
// 구조분해 할당 k원
fn main() {
    let tup = (500, 6.4, 1);

    let (x, y, z) = tup;

    println!("The value of y is: {y}");
}

// . + index로 접근 가능
fn main() {
    let x: (i32, f64, u8) = (500, 6.4, 1);

    let five_hundred = x.0;

    let six_point_four = x.1;

    let one = x.2;
}
```


2. 배열
- 스택이 아닌 힙에 위치 시키고 싶을 때 유용하다고 함 (원문이 이해안가지만 다시 다룬다고 한다, 콜스택-메모리힙 말하는 거겠지)
- 가변 배열 c++처럼 vector 있음
- 그 외에는 배열과 똑같다
- index out of bounds error를 설명하면서 러스트는 런타임에러로 부적절한 메모리 참조를 막아준다고 함

## 함수
- 러스트를 '표현식' 기반 언어라고 소개하고 있다
- 자바스크립트와 비슷하게 동작하고 있음, 문과 식의 구분이 더 엄격함

### 표현식과 문

```
fn main() {
    let y = {
        let x = 3;
        x + 1
    };

    println!("The value of y is: {y}");
}

```

심지어 이런것도 가능하다..
코드블럭을 만들고 별도의 리턴을 하거나 하지 않았음, 마지막 표현식에서 값이 4로 평가되었을 뿐
자세히 보면 x + 1에는 세미콜론이 없는데, 그렇기에 표현식이다.
만약 x + 1;로 끝나는 블록이라면 문 두개가 있을 뿐이라서(할당문 + 표현식인 문)
값으로 평가할 수 있는 표현식이 아니기에 에러가 날 것
어쨌든 표현식이라 할당'문'에 사용이 가능하다.

그리고 가장 중요한 포인트 : 함수 호출은 표현식이다

### 함수의 리턴값

- 함수 선언에서 리턴값을 정의해둬야함
- 놀라운 사실 : 코드블럭 내의 마지막 표현식이 리턴값이됨 🫢
- 물론 return 키워드를 사용해서 특정하거나 미리 리턴 할 수 도 있음


```
fn main() {
    let x = plus_one(5);

    println!("The value of x is: {x}");
}

fn plus_one(x: i32) -> i32 {
    x + 1;
}

```
당연히 이건 안되겠지 함수에 문밖에 없으니까 

근데 놀라운건

```
$ cargo run
   Compiling functions v0.1.0 (file:///projects/functions)
error[E0308]: mismatched types
 --> src/main.rs:7:24
  |
7 | fn plus_one(x: i32) -> i32 {
  |    --------            ^^^ expected `i32`, found `()`
  |    |
  |    implicitly returns `()` as its body has no tail or `return` expression
8 |     x + 1;
  |          - help: remove this semicolon

For more information about this error, try `rustc --explain E0308`.
error: could not compile `functions` due to previous error

```
컴파일러가 help: remove this semicolon 이런것도 추천해줌...

## 주석
특이사항 없음

## Control Flow (제어문?? -> 맞음)

### 조건문
저자는 만약 하나 이상의 else if 문이 필요하다면 리팩토링을 권하고 있음
그리고 당연히 match 키워드에 대한 소개도 해뒀음

if 문이 표현식이기 때문에 아래와 같은 할당문도 가능하다고 함
```
let number = if condition { 5 } else { 6 };
```

### 반복문
기본적으로 loop 키워드로 사용
break, continue 키워드 사용도 가능하다
break, continue 키워드 뒤에 반환값을 적어둘 수 도 j음

```
fn main() {
    let mut counter = 0;

    let result = loop {
        counter += 1;

        if counter == 10 {
            break counter * 2;
        }
    };

    println!("The result is {result}");
}

```

break, continue 키워드는 루프가 중첩인경우 가장 안쪽부터 동작하기는 하지만, 루프 이름을 특정해서 사용도 가능하다.

```
fn main() {
    let mut count = 0;
    'counting_up: loop {
        println!("count = {count}");
        let mut remaining = 10;

        loop {
            println!("remaining = {remaining}");
            if remaining == 9 {
                break;
            }
            if count == 2 {
                break 'counting_up;
            }
            remaining -= 1;
        }

        count += 1;
    }
    println!("End count = {count}");
}

```

while, for 루프도 각각 있음 

```
fn main() {
    let mut number = 3;

    while number != 0 {
        println!("{number}!");

        number -= 1;
    }

    println!("LIFTOFF!!!");
}

```

```
fn main() {
    let a = [10, 20, 30, 40, 50];
    let mut index = 0;

    while index < 5 {
        println!("the value is: {}", a[index]);

        index += 1;
    }
}

```

```
fn main() {
    let a = [10, 20, 30, 40, 50];

    for element in a {
        println!("the value is: {element}");
    }
}

```

```
fn main() {
    for number in (1..4).rev() {
        println!("{number}!");
    }
    println!("LIFTOFF!!!");
}

```
이런 식으로 많이 쓴다고 함