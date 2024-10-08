# 배열
배열을 생성 시 heap 영역에 배열 공간이 생성된다.

**배열의 특징**
- 배열에서 자료를 찾을 때 인덱스를 사용하면 매우 빠르게 자료를 찾을 수 있다.
- 인덱스를 통한 입력, 변경, 조회의 경우 한번의 계산으로 자료의 위치를 찾을 수 있다.

## 배열에 데이터 추가
특정 위치에 데이터 추가(삽입)
- 배열의 중간에 데이터를 추가하려면 추가할 위치 이후의 데이터를 한 칸씩 뒤로 이동시켜야 한다.
- O(n)

**단점**
- 배열의 길이를 동적으로 변경할 수 없다.
- 데이터를 추가하기 불편하다.

# List 자료 구조
순서가 있고, 중복을 허용하는 자료 구조를 리스트라고 한다.

일반적으로 배열과 리스트는 구분해서 이야기 한다.
- 배열: 순서가 있고 중복을 허용하지만 크기가 정적으로 고정된다.
- 리스트: 순서가 있고 중복을 허용하지만 크기가 동적으로 변경된다.

데이터 추가 시 배열의 크기를 초과하면
기존 배열보다 2배 큰 배열을 생성해서 복사. (기존 배열은 참조한느 곳이 없으므로 GC의 대상이 된다.)


데이터 삽입, 삭제에 평균 O(n) 에 시간이 든다. (마지막 원소를 삭제 ,추가하는 경우 O(1))
 
