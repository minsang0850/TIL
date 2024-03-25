테스트 케이스를 만들고 테스트 메서드를 실행할 수 있어야 한다.

**테스트 프레임워크에 대한 할일 목록**
- 테스트 메서드 호출하기
- 먼저 setUp 호출하기
- 나중에 tearDown 호출하기
- 테스트 메서드가 실패하더라도 tearDown 호출하기
- 여러 개의 테스트 실행하기
- 수집된 결과를 출력하기

부트스트랩 테스트를 위한 전략이 있다. 플래그를 가지고 있는 테스트 케이스를 만들 것이다.
테스트 메서드가 실행되기 전에는 플래그가 false 인 상태여야 한다.
테스트 메서드가 플래그를 설정할 것이고, 테스트 메서드가 실행된 후에는 플래그가 true여야 한다.
메서드가 실행되었는지(was run) 알려주는 테스트 케이스이므로 클래스 이름을 WasRun으로 하자.
플래그 이름도 wasRun으로 하자.

파이썬은 파일에서 읽는 순서대로 명령문을 실행하기 때문에, 테스트 메서드를 수동으로 호출하는 식으로 시작할 수 있다.
```python
test = WasRun("testMethod")
print test.wasRun
test.testMethod()
print test.wasRun
```

이 코드는 우리 예상대로 실행되지 않는다.
WasRun 클래스를 정의하자.
```python
class WasRun:
    def __init__(selfself, name):
        self.wasRun = None
```

```python
dev testMethod(self):
    self.wasRun = 1
```
초록 막대다!

다음으로 필요한 것은 테스트 메서드를 직접 호출하는 대신 진짜 인터페이스인 run() 메서드를 사용하는 것이다.
```python
test = WasRun("testMethod")
print test.wasRun
test.run()
print test.wasRun
```
```python
def run(self): 
    self.testMethod()
```
테스트는 다시 올바른 값을 출력한다.
많은 리팩토링에 이런 느낌이 있다.

두 부분을 서로 분리하여 각각에 따로 작업할 수 있다.
작업이 끝날 때 두 부분이 원래대로 하나로 돌아간다면 그것도 괜찮다.
만약 그렇지 않으면 두 부분을 분리된 상태로 놔둘 수 있다.
이 경우에, 결국에는 TestCase 상위 클래스를 만들 것을 기대하지만, 우선은 예로 제시한 것의 부분들을 차별화해야 한다.

다음 단계는 testMethod()를 동적으로 호출하는 것이다. 파이썬의 가장 멋진 특징 중 하나는 클래스의 이름이나 메서드의 이름을 함수처럼 다룰 수 있다는 점이다.
```python
class WasRun:
    def __init__(self, name):
        self.wasRun = None
        self.name = name
    def run(self):
        method = getattr(self, self.name)
        method()
```
여기 또 다른 리팩토링의 일반적인 패턴이 있다.
하나의 특별한 사례에 대해서만 작동하는 코드를 가져다가 다른 여러 사례에 대해서도 작동할 수 있도록 상수를 변수로 변화시켜 일반화하는 것이다.

이제 우리의 WasRUn 클래스는 독립된 두 가지의 일을 수행한다.
- 메서드가 호출되었는지 그렇지 않은지를 기억하는 일
- 메서드를 동적으로 호출하는 일

TestCase 상위 클래스를 만들고 WasRun이 이를 상속받게 만들자
```python
class TestCase:
    pass
```
```python
class WasRun(TestCase)
```
name 속성을 상위 클래스로 끌어올리자.
```python
# TestCase
def __init__(self, name):
    self.name = name
```
```python
# WasRun
def __init__(self, name):
    self.wasRun = None
    TestCase.__init__(self, name)
```

run() 메서드는 상위 클래스에 있는 속성만을 사용하므로 이것도 상위 클래스에 올라가는게 맞는 것 같다.
```python
#TestCase
def __init__(self, name):
    self.name = name
def run(self):
    method = getattr(self, self.name)
    method()
```

```python
#TestCaseTest
class TestCaseTest(TestCase) :
    def testRunning(selfself):
        test = WasRun("testMethod")
        assert(not test.wasRun)
        test.run()
        assert(test.wasRun)
    TestCaseTest("testRunning").run()
```
테스트 코드의 내용은 단순히 프린트문이 단언(assertion)으로 바뀐 것으로 ,우리가 해낸 것이 메서드 추출의 한 복잡한 형태임을 알 수 있었다.

꼭 이런 작은 단계로 작업해야 하는 것은 아니다.
TDD를 마스터하면 훨씬 큰 기능 단위로 작업할 수 있게 될 것이다.
**검토**
- 자기 과신에 차서 몇 번의 잘못된 출발을 한 후, 아주 자그마한 단계로 시작하는 법을 알아냈다.
- 일단 하드코딩을 한 다음에 상수를 변수로 대체하여 일반성을 이끌어내는 방식으로 기능을 구현했다.
- 플럭거블 셀렉터(Pluggable Selector)를 사용했다. 
- 테스트 프레임워크를 작은 단계로만 부트스트랩했다.
