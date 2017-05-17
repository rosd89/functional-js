#ECMAScript 2015

### 1. Primitive(기본형)
* "", 0, false, undefined, null, NaN => 자바스크립트에서 거짓을 표현
	* C스타일을 뛰어넘어 다양한 편버으로 상태를 검사하는 코드가 난무
		* ex 
		
		```javascript
			function(){
				if(arguments.length)
				...
			}
			
			function(v){
				if(v.trim())
				...
			}
		```
	* 가부 판정을 위한 메소의 부재로 숫자나 문자열을 false대용으로 사용
* es6에서는 true, false의 용도를 정확히 하고 자주 쓰는 판정에 대한 boolean 메소드 제공
	* ex)
	
	```javascript
		if(v === parseInt(v, 10)) ...
		if(Number.isInteger(v)) ...
		
		if(v === v + 1 || v === v -1) ...
		if(Number.isFinite(v)) ...
	```
	
	* 다른언어에서 지원하는 방식의 메서드를 제공함으로 인해서 boolean을 사용하게 함
* undefined
	* 애매한 부정값으로 사용되므로 값이 없음을 나타내도록 언어의 여러 요소에서 강제하여 제한된 용도로 사용
	* 값이 없을 때와 똑같다라는 용도로 es6에서 유도
	* ex

	```javascript
		let[a, b=3] = [1, undifined]
		console.log(b) // output => 3
	```
### 2. Implicit System
#### arguments
* 어떠한 코드 상의 힌트도 없고, 단지 언어스펙을 외운 사람만 의미가 파악됨, 또한 언어의 정의된 키워드가 아니므로 요용할 수 있음 -> 이러한 문제를 해결하고자 함
* 코드에 대한 명확성을 높이고자 함

#### Symbol
자동 형변환 등 엔진의 내재된 작동에 사용자가 작성한 객체가 반응하게 하려면 어떠한 코드 상의 힌트도 없고, 단지 언어스펙을 외운 사람만 의미가 파악됨

```
let a = {
	toString : _ => 'a',
	valueOf : _ => 1,
	toJSON : _ => '{a:1}'
}
```
* 일반 문자열이므로 이것이 엔진 작동에 반응하는 트리거 메소드라는 것을 코드로 알 방법이 없고 언어스펙을 아는 사람만 의미 파악이 가능
* 위의 코드에서 언어가 제공하는 메세드인지 아닌지 분명하지 않음
```
let a = {
	[Symbol.toPrimitive] : hint => hint == 'string' ? 'a' : 1
};
```

### 3. Iteration
#### Iterable(Iterator을 호출) & Iterator (반복을 처리하는 객체)
* for, while등 문으로 구성된 반복은 실행될 수 있으나 값으로 존재할 수 없음
	* 인자로 전달하거나 상태를 기억해 둘 수 없음
		1. 사전에 정의가  끝난 정적 값에 대한 반복만 처리가능
		2. 반드시 반복전에 반복할 대상에 대한 사전처리가 완료되어야 함
		3. 반복처리될 대상에 대한 지연처리가 불가
	* 지연설행의 필요성
		1. 수식적이거나 알고리즘을 통해서 계산될 값은 미리 정의해둘 이유가 없음
		2. 메모리에 정의한적이 없어도 반복시점에 자원을 확보하여 반환할 수도 있음
	* 표준적인 값형태의 반복처리기가 필요
	* 문이 제약사항이 많아 최근 언어는 문을 값으로 바꾸려고 노력중 -> es6에서 문을 값으로 사용하기위해 구현
	* 반복이란 매 반복시 마다 실행될 부분과 계속 반복할지 여부로 구성
		
		```
		while(계속 반복할지 여부){
			반복시마다 실행될 부분
		}
		```
		
		* Iterator Protocol
			* 계속 반복할지 여부 -> next() 메서드를 호출
			* 반복시마다 실행될 부분 -> next로 반환된 객체의 done키에 있는 boolean값
		* 문자열은 Iterable=반복객체(Iterator를 반환함)
			
			```
				let [a, b, c] = 'abc'
				let a = [...'abc';
				const test = (...arg) => arg.join('-');
				test(...'abcd'); //'a-b-c-d' 
			```
			* 반복하고자 하는 것을 Iterable화 시키면 됨
			* 커스텀 Iterable
			
				```
					let nw = {
						[Symbol.iterator](){
							var cursor = -1, max = 10;
							return{
								done : false,
								value : 0,
								next(){
									if(cursor++ <= max){
										this.value = cursor * cursor;
									}
									else{
										this.value = undefined;
										this.done = true;
									}
									
									return this;
								}
							}
						}			
					}
				``` 

#### generator
*Iterable을 직접 제작하는 것은 상당한 반복작업을 유발하므로 언어차원에서 간략한 문법을 제공

```
const gene = function*(max){
	let cursor = -1;
	while(cursor++ < max) yield cursor * cursor;
};
((...a) => console.log(a))(...gene(10)); // [0, 1, 2, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

1. 객체 컨텍스트를 변수 컨텍스트로
2. 제어문을 중지시킬 수 있는 yield

* 실행중인 EC에 대해 suspend를 걸 수 있는 기능을 엔진차원에서 추가

### class
* 대체가능성 -> 다형성
* 내적동질성

```
var Dog = function(){};
DOg.prototype.bark = function(){
	return 'woof'
}

var Spitz = function(){};
Spitz.prototype = new DOg();
Spitz.prototype.bark = function(){
	return 'woof woof'
}

var dog = new Spitz();

console.log(dog instanceof Dog);
console.log(dog.bark()); // 내적동질성
```

* 중복키의 문제
	1. 프로토타입은 단일 체이닝 구조로 대상객체가 상위객체의 키를 가리도록 되어있음/
	2. 따라서 하위 클래스는 상위의 이름을 피해서 제작해야만 상위구조와 상요작용할 수 있음
* 생성자에서도 제제릭화된 로직을 짤 수 없고 반드시 하드코딩된 클래스 이름을 알아야만 생정자 체인을 시킬 수 있음

위와 같은 이유로 컨텍스트 객체 도입

1. 기존 this가 현재 주체가 되는 객체의 참조로 동작하는 컨텍스트를 제공했다면.
2. super는 상위클래스에 대한 상대경로를 제공하는 새로운 컨텍스트를 제공

* 단 사용되는 곳에 따라 다름 super

	* 생성자에서 사용되는 경우
	* 메소드에서 사용되는 경우
	* 생성자를 일반 함수로 호출하면 죽음
	* 메소드를 생성자로 사용하려 하는 등의 오용되는 문제에 대해 예외를 발생시켜 방어하도록 설계됨
