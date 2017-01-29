# C++ 문법
## struct (구조체)
### 비트 필드(Bit Fields)
- 구조체의 멤버가 차지하는 메모리 공간을 원하는 대로 조절하는 것
```C++
struct Flags {
  int a: 3; // a는 3비트만 차지
  int b: 4;
  int: 5 // 멤버의 이름이 없다면 사용하지 않는 채로 5비트를 중간에 남겨준다.
  bool c: 1;
}
```

- 이를 활용하여 비트 단위의 논리 연산을 대신할 수 있다
```C++
struct Pixel16 {
  unsigned int blue: 5;
  unsigned int green: 6;
  unsigned int 5;
}

/*
 unsigned short를 Pixel16 구조체 값으로 변환하기 위한 용도의 union.
 만약 이게 없다면, Pixel16.blue = xxx 이런 식으로 값을 대입해야 하기 때문
*/
#include <bitset>

...
union Convert16 {
  Pixel16 pixel;
  unsigned short us;
}

int main() {
  unsigned short color = 0x1234;
  
  Convert16 convert;
  convert.us = color
 ;
 
 cout << std::bitset<16>(convert.pixel.blue)
}
```

## Union (공용체)
- 구조체와 비슷하게 생겼지만 모든 멤버가 같은 메모리 공간에 자리잡는다.
- 한 쪽의 멤버 값이 변경되면, 다른 멤버의 값에도 영향을 준다.
- 각기 다른 크기의 멤버가 있을 경우, 크기가 더 작은 멤버는 맨 앞에서부터 자신의 크기만큼만 공용체와 공유한다.
- 공용체의 모든 멤버는 같은 메모리 공간을 사용하므로 공용체 변수의 크기는 가장 큰 크기를 갖는 멤버의 크기와 같다.
```C++
union MyUnion {
  int i;
  void* p;
}
```

## Enumeration (열거체)와 Enumerator (열거자)
- enum 열거체의 열거자들은 첫 번째 심볼부터 차례대로 0, 1, 2... 의 값으로 초기화되며, 이는 수정할 수 있다.
- 열거자들은 기본적으로 기본적으로 정수 타입이지만 기존의 정수 타입과 많이 다르다. 덧셈 등도 불가.
```C++
enum { JOB_DWARF, JOB_WARRIOR, JOB_SORCERER };
```

## Reference (참조 변수)
- 변수의 별명. &를 사용해서 변수를 정의해주면 Reference 변수를 만들 수 있다.
- 내부적으로 포인터와 유사하게 행동을 하는 것 처럼 보이나, 컴퓨터가 필요에 따라 Reference 변수를 위한 메모리 공간을 없애버릴 수도 있다는 점에서 다르며, 유의해야 한다.
- Reference 변수는 처음 정의할 때 외에는 다른 변수를 참조하게 만들 수 없다. 이 경우 값의 대입만 된다.
- 상수 역시 참조가 가능하다. 단 이때 const를 써야만 한다. 상수는 변경될 수 없으므로.
```C++
int target = 100
int& ref = target;
```

- const 속성을 갖는 Reference 변수는 자신의 값을 변경할 수 없게 된다.
```C++
char c = 'A';
const char& r = c;
r = 'B' // error!
c = 'B' // success!
```

## typedef
- typedef는 타입에 대한 별명을 만드는 데 사용한다.
```C++
typedef short ID_TYPE;

ID_TYPE id1 = (ID_TYPE)0;
ID_TYPE id2 = (ID_TYPE)0;
...
```

## double pointer
- 포인터를 가리키는 포인터도 있다.
```C++
char c = '1';
char* pc = &c;
char** ppc = &pc;
```

## Function (함수)
- 배열을 함수에 넘길때, 사실은 그 배열의 포인터를 넘기는 것이다.
- 함수에서 배열을 받을 때 char hello[] 와 같이 원소 개수는 입력할 필요가 없다. 어차피 포인터를 받는 것이므로.
- 단, 2차원 배열 이상일 경우 int arr[][3][3]과 같이 두 번째 차원부터는 딱 맞는 값을 넣어주어야 한다. 그 이유는 첫 번쨰 차원이 배열에 대한 포인터를 뜻하기 때문이다.


# C++ 코딩 스타일
## using namespace는 나쁘다.
- boost의 예를 들 수 있는데, boost의 상당수는 최신 C++의 표준이 되었다. 그런데 using namespace boost; 라고 해버리면, 이게 boost의 함수인지 C++ 표준 함수인지 헷갈릴 수밖에 없다.

## Pointer vs Reference
- 함수를 통해 값을 변경할때도 포인터나 레퍼런스 둘 다 활용이 가능하다.
- 하지만 둘 중 레퍼런스를 좀 더 추천한다. 그 이유는 사용이 쉽고 안전하기 때문이다.
- 레퍼런스는 포인터처럼 * & 쌍을 각각에 쓰지 않아도 되고, (오직 받는 쪽만 &) 포인터와 달리 NULL 이나 이상한 주소값등이 넘어올 때 문제가 될 여지가 없다.

# C++ 유용한 팁
### 포인터의 + 와 - 연산자는 (피연산자 값 * 포인터 변수의 크기) 만큼 값이 증가 혹은 감소된다.
## 배열의 이름은 그 배열의 첫 번째 원소와 주소 값이 동일하다.
```C++
float f[5];
if ( f == &f[0] ) { // True!
}
```
## 포인터로 구조체의 인자의 접근할 때, 두 가지 방법이 있다.
```C++
Rectangle* p = &rc;

(*p).x = 200; // *p.x 라고 하면 우선순위 때문에 p.x가 먼저 연산된다.
p->y = 250;
```

# C++를 시작하며 실수할 수 있는 부분
## struct 는 참조가 아니라 값이다.
```C++
Student a1 = { 1, 90, 80, 30 };
Student a2 = a1; // 참조가 아닌 값 복사가 발생한다.
```

## 배열의 이름에는 증감 연산자를 사용할 수 없다.
```C++
int nArray[5] = { 0 };
nArray++; // Error!
```

## 포인터와 배열 연산자의 우선순위로 인한 괄호
- [] 연산자가 * 연산자보다 우선 순위가 높으므로 주의해야 한다.
```C++
long *p[20]; // long* 타입의 원소 20개를 갖는 배열
long (*p)[20]; // long 타입의 원소 20개를 받는 배열에 대한 포인터
```

## const 포인터 변수를 주의
- '*을 무시하고 const 다음에 오는 것'을 상수화시킨다고 생각하면 쉽다.
```C++
const int* p // const int 이므로 int 타입의 값이 const이다.
int* const p // p자체가 const이다.
```
