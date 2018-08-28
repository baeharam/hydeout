---
layout: post
category: c/c++
title: "[STL]Iterator"
---

# 개요

vector, map, set에서 계속 Iterator(반복자)를 사용하다가 정확히는 알지 못하는 것 같아서 그 정의와 관련된 함수들을 정리하고자 한다. Iterator는 쉽게 말해서 STL 컨테이너의 원소들을 포인터 같이 가리키는 클래스라고 할 수 있다. 이런 Iterator 객체는 모든 STL 컨테이너에서 사용할 수 있으며 Iterator 하위에도 여러가지 연산여부의 허용범위에 따라 클래스들이 나뉜다. 하위 클래스로은 InputIterator, OutputIterator, ForwardIterator 등이 있다.

<br>

# 여러가지 함수들

Iterator를 사용하는 가장 기본적인 컨테이너는 벡터이기 때문에 벡터기준으로 설명하도록 하겠다.

* **begin(), end()**

컨테이너의 처음과 끝을 가리키는 Iterator를 리턴한다. 여기서 end()는 정확히 끝이 아니라 끝 바로 다음을 말한다. 아래 그림을 참조하도록 하자.

<img src="http://upload.cppreference.com/mwiki/images/1/1b/range-begin-end.svg">

```c++
#include <iostream>
#include <iterator>
#include <vector>

int main(void)
{
	std::vector<int> ar = { 1,2,3,4,5 };
	std::vector<int>::iterator ptr;

	std::cout << "The vector elements are : ";
	for (ptr = ar.begin(); ptr < ar.end(); ptr++)
		std::cout << *ptr << " ";
	return 0;
}
```

```
1 2 3 4 5
```

`ptr`이라는 벡터 컨테이너에 대한 Iterator 객체를 생성했고 `begin()`과 `end()`를 통해 처음과 끝 Iterator를 받음으로서 컨테이너를 순회하고 있다. 여기서 `*ptr`과 같이 dereference 하는 것은 해당 Iterator 객체가 가리키는 값을 얻어낸다는 말이다. 포인터의 참조와 비슷하다.

<br>

* **advance()**

2번째 인자로 주어지는 값만큼 Iterator 객체의 위치를 이동시킨다.

```c++
// 이어서...
ptr = ar.begin();
std::advance(ptr, 3);
std::cout << *ptr << std::endl;
```

```
4
```

1을 가리키는 위치에서 시작해서 3번 이동해 4를 가리키는 위치로 도달했음을 볼 수 있다.

<br>

* **next(), prev()**

앞으로 이동하던지 뒤로 이동하던지 해서 해당 위치를 가리키는 새로운 Iterator 객체를 리턴한다.

```c++
#include <iostream>
#include <iterator>
#include <vector>

int main(void)
{
	std::vector<int>::iterator ptr = ar.begin();
	std::vector<int>::iterator ftr = ar.end();

	auto it1 = std::next(ptr, 3);
	auto it2 = std::prev(ftr, 3);

	std::cout << "The position of new iterator using next() is : " << *it1 << std::endl;
	std::cout << "The position of new iterator using prev() is : " << *it2 << std::endl;
	return 0;
}
```

```
The position of new iterator using next() is : 4 
The position of new iterator using prev()  is : 3 
```

여기서 헷갈렸던 점은 `end()`가 가리키는 값이 마지막 값 다음이었다는 점이다. 따라서 `ptr`이 가리키는 값이 1 더 큰 것이다.

<br>

* **inserter()**

여러개의 원소들을 특정 위치에 삽입하고 싶을 때 사용하는 함수이다. 첫번째 인자로는 해당 원소들을 가진 컨테이너 객체, 두번째 인자로는 삽입하고 싶은 위치 다음 값을 가리키는 Iterator 객체를 전달해주면 된다.

```c++
#include <iostream>
#include <iterator>
#include <vector>

int main(void)
{
	std::vector<int> ar1 = { 1,2,3,4,5 };
	std::vector<int> ar2 = { 10,20,30 };

	std::vector<int>::iterator ptr = ar1.begin();

	advance(ptr, 3);

    // ptr은 4의 위치를 가리키므로 4 이전에 ar2의 값들을 ar1에 삽입한다는 것!
	copy(ar2.begin(), ar2.end(), std::inserter(ar1, ptr));

	for (auto const &cur : ar1)
		std::cout << cur << " ";
	return 0;
}
```

```
1 2 3 10 20 30 4 5
```

<br>

# 마무리 및 출처

나중에 사용을 하거나 시간이 되면 Iterator의 하위 클래스들에 대해서도 정리를 해보고자 한다. 위에서 정리한 것들은 잊지 말고 써야 될 때 꼭 기억해서 쓰도록 하자.

* [GeeksforGeeks : Iterators in C++ STL](https://www.geeksforgeeks.org/iterators-c-stl/)