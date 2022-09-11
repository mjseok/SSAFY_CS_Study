# Q. Hash table는 어떤 자료구조인가요?

**[핵심 답변]**

hash table은 효율적인 탐색(빠른 탐색)을 위한 자료구조로써 key-value 쌍의 데이터를 입력받습니다. hsah function h에 key 값을 입력으로 넣어 얻은 해시값 h(k)를 위치로 지정하여 key-value 데이터 쌍을 저장합니다. 저장, 삭제, 검색의 시간복잡도는 모두 O(1)입니다.

**[면접 TIP]**

cs 면접에서 가장 자주 나오는 질문중에 하나입니다. hashtable이 면접 단골질문인 이유는 다음과 같습니다.

1. 실무에서도 활용도가 높은 자료구조입니다.
2. Linked list, Array 더 나아가면 Tree까지 질문할 수 있습니다.
3. 시간복잡도에 대해서 물어보기 좋습니다.

한편, 좋은 hash function의 조건을 물어보는 경우도 있습니다. 좋은 hash function의 핵심적인 조건은 해시값이 고르게 분포되게 하는 것입니다.

**Q. 좋은 hash function의 조건은 뭘까요?**

[핵심 답변] 각 상황마다 good hash function은 달라질 수 있으나 대략적인 기준은 연산 속도가 빨라야 하고, 해시값이 최대한 겹치지 않아야 합니다.

### Direct-address Table

Direct-address Table(직접 주소화 테이블)이란, key 값으로 k를 갖는 원소는 index k에 저장하는 방식입니다.

```java
key: 출석번호, value: 이름
(3, 노정호)
(5, 배준석)
(6, 정재헌)
(7, 남영욱)
```

![1](https://user-images.githubusercontent.com/48662662/189540914-79229cc1-afa9-4559-9f5d-0178e325de87.PNG)

- table은 array로 구현할 수 있습니다.
- 직접주소화 테이블이라고도 부릅니다.

직접 주소화 방법을 통해 key-value 쌍의 데이터를 저장하고자 하면 많은 문제가 발생합니다.

- 불필요한 공간 낭비

```java
key: 학번, value: 이름
(2022390, 노정호)
(2022392, 배준석)
(2022393, 정재헌)
(2022401, 남영욱)
```

![2](https://user-images.githubusercontent.com/48662662/189540931-2cacaf33-dabe-4c67-a61f-7460e9cad77b.PNG)

- key가 다양한 자료형을 담을 수 없게 됨

```java
key: ID, value: 이름
(nossi8128, 노정호)
(js9876, 배준석)
(zebra001, 정재헌)
(nam1234, 남영욱)
```

![3](https://user-images.githubusercontent.com/48662662/189540948-3e1a813b-58f3-413f-b975-f18884d73cfc.PNG)

### Hash table

(key, value) 데이터 쌍을 저장하기 위한 방법으로 직접 주소화 방법이 잘 맞지 않습니다. hash table은 hash function h를 이용해서 (key, value)를 index: h(k)에 저장합니다. 이 때, “키 k값을 갖는 원소가 위치 h(k)에 hash된다.” 또는 “h(k)는 키 k의 해시값이다.”라고 표현합니다. key는 무조건 존재해야 하며, 중복되는 key가 있어서는 안됩니다. 

  한편, hash table을 구성하고 있는, (key, value) 데이터를 저장할 수 있는 각각의 공간을 slot 또는 bucket이라고 합니다.

![4](https://user-images.githubusercontent.com/48662662/189540956-0524e524-c2b9-41fd-a7f8-2e167ae1c9a7.PNG)

![5](https://user-images.githubusercontent.com/48662662/189540962-bcc2ff74-d910-44a3-8832-16b420444a58.PNG)

### Collision

collision이란 서로 다른 key의 해시값이 똑같을 때를 말합니다. 즉, 중복되는 key는 없지만 해시값은 중복될 수 잇는데 이 때 collision이 발생했다고 합니다. 따라서 collision이 최대한 적게 나오도록 hash function을 잘 설계해야하고, 어쩔 수 없이 collision이 발생하는 경우 seperate chaining 또는 open addressing 등의 방법을 사용하여 해결합니다.

![6](https://user-images.githubusercontent.com/48662662/189540967-3c9a9194-fe82-43fd-8e4c-5e4e09d763ac.PNG)

### 시간복잡도와 공간효율

**시간복잡도**는 저장, 삭제, 검색 모두 기본적으로 O(1)이지만, collision으로 인하여 최악의 경우 O(n)이 될 수 있습니다. 

**공간효율성**은 떨어집니다. 데이터가 저장되기 전에 미리 저장공간(slot, bucket)을 확보해야 하기 때문입니다. 따라서 저장공간이 부족하거나 채워지지 않은 부분이 많은 경우가 생길 수 있습니다.

### Reference

인프런 - 개발자전공면접cs완전정복
