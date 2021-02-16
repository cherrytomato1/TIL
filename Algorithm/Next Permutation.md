## NextPermutation

현 순열에서 사전 순(오름차순)으로 다음 순열을 생성합니다. 즉 배열을 가장 작은 값으로 정렬한 뒤, 한 자리씩 swap하면서 출력합니다. 만약 숫자배열이라면 각각의 자리를 합해서 여러자리 수를 만든다고 했을 때 가장 작은 값부터 가장 큰 값이 될 때까지 순열을 출력합니다.

- ex) 1 2 3(가장 작은 값) → 1 3 2 → 2 1 3 → 2 3 1 → 3 1 2 → 3 2 1(가장 큰 값)

## 알고리즘

먼저 알고리즘 전 해당 배열을 오름차순으로 정렬( 가장 작은 순열을 먼저 만들어 놓음)한 후 다음 큰 순열을 찾는 과정을 반복합니다.

- 아래의 과정마다 하나의 순열을 만들어 내며 가장 큰 내림차순 순열이 만들어질 때까지 반복
    1. 뒤쪽부터 탐색하여 교환위치(i-1) 찾기(i → 가장 높은 값)
        - i 부터 탐색하여 i -1 < i 인 경우 찾기
        - 꼭대기(가장 큰 값) i  가 0이면 마지막 순열까지 탐색 완료
    2. 뒤쪽부터 탐색하여 교환위치(i-1)와 교환할 교환 위치의 값 보다 큰 값 위치(j) 찾기
    3. 두 위치 값(i - 1, j) 교환
    4. 가장 큰 값 인덱스(i) 부터 맨 뒤까지 오름차순 정렬
        - 이미 내림차순 정렬되어 있으므로 0 부터 n-1 자리 수를 각각 교환

### 장점

재귀로 짜여진 순열과 달리 시간 복잡도가 낮습니다. 재귀적인 호출이 없을 뿐더러, 각각의 순열에 대해서 자연스레 백트래킹 과정이 있고, 하나의 배열로 각 인덱스의 값만 교체해주므로 훨씬 빠르다. 또한 순열과 조합을 함께 사용할 수 있습니다.

### 단점

원래의 배열을 재배열하여 순열을 뽑아내므로, nPr 과 같이 특정 개수의 순열을 뽑아낼 수 없다. nPn과 nCr을 같은 알고리즘으로 만들어낼 수 있습니다.

## 구현

c++에는 stl에 next_permutation 함수가 존재하지만, 자바에는 없으므로 직접 구현해야합니다.

### 순열

```java
public static void main(String[] args){
		int[] arr = new int[5];
		//... 배열 채우기 생략
		Arrays.sort(arr);
		do {
		//			do something...
				}while(np(arr));
		}
}
static boolean np(int numbers[]) {
//		1. 교차점 찾기 
		int N = numbers.length;
		int i = N - 1;
//		뒤쪽 부터 탐색하여 꼭대기(i) 찾기 
		while(i > 0 && numbers[i - 1] >= numbers[i]) i--;
		
//		i = 0이면 순열 생성 못함 
		if(i == 0) return false;
		
//		2. 교차할 데이터의 위치 찾기 
		int j = N - 1;
//		뒤쪽 부터 탐색하여 교환할 큰값(j) 찾기 
		while(numbers[i - 1] >= numbers[j])	j--;
		
//		3. i-1 번째 데이터와 j번째 데이터 교환 (swap)
		swap(numbers,i - 1, j);
		
//		4. 다음 순열을 위해서 교환후 꼭지점 이후는 (index)를 내림차순으로 
		int k = N - 1;
		while(i < k) {
				swap(numbers, i++, k--);			
		}
		return true;
}
static void swap(int numbers[], int i, int j) {
		int temp = numbers[i];
		numbers[i] = numbers[j];
		numbers[j] = temp;
}
```

- 소스 배열을 오름차순으로 정렬한 후 do - while로 np(arr)이 참일 때까지 반복. 루프 문 안에서 생성된 순열을 통한 임의의 동작
- i는 맨 뒤부터 탐색하여 i - 1 < i  인 인덱스를 찾고, 해당 i - 1은 뒤에서부터 다시 탐색하여 i - 1보다 큰 값을 찾아 서로 바꿈
- 이후 i 이후의 값들을 다시 오름차순 정렬

### 조합

NextPermutation으로 nCr의 조합을 생성하려면 r개의 원소를 저장하기 위해서 mask 배열을 선언합니다. 이 마스크 배열을 앞서 구현한 nextPermutation 메서드에 넣고 동작시켜서 얻을 수 있는 mask 배열의 플래그들이 선택된 원소들입니다.

이 때 크기의 비교가 가능해야 하므로 boolean 형이 아닌 int 형으로 마스킹을 합니다. mask 배열 역시 오름차순으로 정렬되어 있어야 하므로 하위 인덱스부터 뽑아낼 값의 수만큼 1로 채워줍니다.

```java
static final int N = 5;
static final int R = 3;

public static void main(String[] args){
		int[] arr = new int[N];
		//... 배열 채우기 생략

		int[] mask = new int[N];
		int[] res = new int[R];

		for(int i = R - 1; i >= N - R; i--){
				mask[i] = 1;
		}
		do {
				for(int i = 0, j = 0; ; i < N ; i++){
						if(mask[i] == 1) res[j++] = arr[i];
				}
		//			do something...
		}while(np(mask));		
}
static boolean np(int numbers[]) {
//...
}
```

- 마스킹 배열을 np 메서드에 넣어서 매 np에서 바뀐 mask에 따라 arr의 원소의 사용 여부 확인
