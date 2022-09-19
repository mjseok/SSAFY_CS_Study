# Hash table collision 발생 시 해결방법

**Q. Hash table에서 collision이 발생하면 어떻게 되나요? 해결방법에는 무엇이 있을까요?**

**A.** collision이 발생할 경우 대표적으로 2가지 방법으로 해결합니다.

첫 번째, open addressing 방식은 collision이 발생하면 미리 정한 규칙에 따라 hash table이 비어있는 slot을 찾습니다. 빈 slot을 찾는 방법에 따라 크게 Linear Probing, Quadratic Probing, Double Hashing으로 나뉩니다.

두 번째, seperate chaining 방식은 linked list를 이용합니다. 만약에 collision이 발생하면 linked list에 노드(slot)를 추가하여 데이터를 저장합니다. 

[면접팁] 

  hash table에서 collision이 발생했을 때, **seperate chaining과 open addressing** 두 가지 방식으로 해결한다. 두 가지 방법이 어떤 메커니즘으로 작동하는지, 어떤 차이점이 있는지 잘 학습하고 가야한다.

  또한 seperate chaining의 경우 **worst case의 시간복잡도에 대해서 설명할 줄 알아야 한다.** 삽입과 검색, 삭제의 시간복잡도는 O(1)이지만, worst case의 경우에 O(n)가 될 수도 있다. 왜 O(n)이 되는지를 설명할 수 있도록 collision에 대해서 알아본다.

### Open addressing

 open addressing 방식은 collision이 발생하면 미리 정한 규칙에 따라 hash table의 비어있는 slot을 찾는다. 추가적인 메모리를 사용하지 않으므로 linked list 또는 tree 자료구조를 통해 추가로 메모리 할당을 하는 seperate chaining 방식에 비해 메모리를 적게 사용한다.

 open addressing은 빈 slot을 찾는 방법에 따라 크게 Linear Probing, Quadratic Probing, Double Hasing으로 나뉜다.

- **Linear Probing(선형 조사법) & Quadratic Probing(이차 조사법)**
    - 선형 조사법은 충돌이 발생한 해시값으로부터 일정한 값만큼($+1, +2, +3, ...$) 건너 뛰어, 비어 있는 slot에 데이터를 저장한다.
    - 이차 조사법은 제곱수($+1^2, +2^2, +3^2, ...$)로 건너 뛰어, 비어 있는 slot을 찾는다.
    - 충돌이 여러번 발생하면 여러번 건너 뛰어 빈 slot을 찾는다. 선형 조사법과 이차 조사법의 경우 충돌 횟수가 많아지면 특정 영역에 데이터가 집중적으로 몰리는 클러스터링(clustering) 현상이 발생하는 단점이 있다.(해시 값이 같으면 충돌 발생시 빈 슬록을 찾기 위해 접근하는 위치가 동일하기 때문이다.) 클러스터링 현상이 발생하면, 평균 탐색 시간이 증가하게 된다.
    - 
        ![1](https://user-images.githubusercontent.com/48662662/191041237-bbde7c3d-9eab-4820-b2fe-128d5f9ddb20.PNG)

        ![2](https://user-images.githubusercontent.com/48662662/191041276-e93834bf-3381-4ed1-9a82-87464c9c7b44.PNG)

        ![3](https://user-images.githubusercontent.com/48662662/191041316-fee5be1f-869f-49f7-9653-a567fd8c4436.PNG)
        
        ![4](https://user-images.githubusercontent.com/48662662/191041339-4df01c44-c825-4325-999b-8a7c60131605.PNG)
        
    
- 이러한 클러스터링 현상을 방지하기 위한 방법으로는 Double Hashing(이중해시, 중복해시)가 있다.
- **Double Hashing(이중해시, 중복해시)**
    - 이중 해싱은 open addressing 방식을 통해 collision을 해결할 때, probing 하는 방식중에 하나이다. linear probing이나 quadratic probing을 통해 탐사할 때는 탐사 이동 폭이 같기 때문에 클러스터링 문제가 발생할 수 있다. 클러스터링 문제가 발생하지 않도록 2개의 해시함수를 사용하는 방식을 이중 해싱이라고 한다. 하나는 최초의 해시값을 얻을 때 사용하고 또 다른 하나는 해시 충돌이 발생할 때 탐사 이동폭을 얻기 위해 사용한다.
    - 이중 해싱은 키를 참조하여 더해 step = C - (k mod C)으로 다음 조사할 위치의 간격을 정할 수 있다. 충돌이 발생했을 때 h(k) ⇒ h(k)+step ⇒  h(k)+2*step ⇒ …  순으로 진행되며 빈 버킷을 찾는다.
    - eg. 크기가 7인 해시테이블에서 (8,1,9,6,13)을 입력하는 예시는 다음과 같다.
    
![5](https://user-images.githubusercontent.com/48662662/191041422-c322fe91-27c6-4c8f-8523-128dbb846035.PNG)


**Q. 이중해싱(double hashing)이 무엇인지 설명해 주세요.**

**A.** 이중 해싱은 open addressing 방식을 통해 collision을 해결할 때, probing하는 방식 중에 하나입니다. linear probing이나 quadratic probing을 통해 탐사할 때는 탐사이동폭이 같기 때문에 클러스터링 문제가 발생할 수 있습니다. 클러스터링 문제가 발생하지 않도록 2개의 해시함수를 사용하는 방식을 이중 해싱이라고 합니다. 하나는 최초의 해시값을 얻을 때 사용하고 또 다른 하나는 해시 충돌이 발생할 때 탐사 이동폭을 얻기 위해 사용합니다.

### Separate chaining

separate chaining 방식은 linked list(또는 Tree)를 이용하여 collision을 해결한다. 충돌이 발생하면 linked list에 노드(slot)를 추가하여 데이터를 저장한다.

- 삽입: 서로 다른 두 key가 같은 해시값을 갖게 되면 linked list에 node를 추가하여 (key, value) 데이터 쌍을 저장한다. 삽입의 시간복잡도는 O(1)이다.
- 검색: 기본적으로 O(1)의 시간복잡도 이지만 **최악의 경우 O(n)**의 시간복잡도를 갖는다.
- 삭제: 삭제를 하기 위해선 검색을 먼저 해야하므로 검색의 시간복잡도와 동일하다. 기본적으로 O(1)이지만 최악의 경우 O(n)의 시간복잡도를 갖는다.

 worst case의 경우 n개의 모든 key가 동일한 해시값을 갖게 되면 길이 n의 linked list가 생성되게 됩니다. 이 때, 검색의 복잡도가 O(n)이 됩니다.

**Q. worst case에 시간복잡도는 O(n)이라고 했는데 어떤 상황인가요?**

**A.** n개이 모든 key가 동일한 해시값을 갖게 되면 길이 n의 linked list가 생성되게 됩니다. 이 때, 특정 key를 찾기 위해서는 길이 n의 linked list를 검색하는 O(n)의 시간복잡도와 동일하게 됩니다.

![캡처](https://user-images.githubusercontent.com/48662662/191041479-11634704-05e3-4681-b907-517986c2fdc1.PNG)


참고: seperate chaining은 기본적으로 linked list를 이용하여 데이터를 저장하지만, collision이 많이 발생하여 linked list의 길이가 길어지면 Binary Search Tree(BST) 자료구조를 이용하여 데이터를 저장하기도 한다. BST를 사용함으로써 검색의 worst case 시간복잡도를 O(n)에서 O($logn$)으로 낮출 수 있다. 

참고

인프런 - 기출로 대비하는 개발자 전공면접
