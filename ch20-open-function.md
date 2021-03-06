# 21. `open` function

[open](https://docs.python.org/dev/library/functions.html#open)은 파일을 엽니다. 엄청 간단하죠? 대부분 아래와 같이 사용할 것 입니다.

```python
f = open('photo.jpg', 'r+')
jpgdata = f.read()
f.close()
```

제가 이 글을 쓰는 이유는 대부분 이렇게 파일을 열어서 사용하는 것을 봤기 때문입니다. 위 코드에는 **3가지** 에러가 있습니다. 에러를 모두 찾으셨나요? 그렇지 않다면 계속 읽어주시길. 이 글을 다 읽을 때 쯤 이면 위 코드에서 무슨 문제가 있는지 알게 될 것이고, 더 중요한 것은 이런 실수들을 여러분의 코드에서 피할 수 있을 것입니다. 기본적인 것부터 시작 해보겠습니다.

`open` 함수의 반환값은 파일 핸들이며 운영체제에서 파이썬 애플리케이션으로 전달됩니다. 파일 작업이 끝나면 파일 핸들을 반환하기를 원할 것입니다. 그리고 이렇게 해야 애플리케이션이 한번에 열 수 있는 파일 핸들의 한도에 도달하지 않을 것입니다.

명시 적으로 close를 호출하면 읽기가 성공한 경우에만 파일 핸들이 닫힙니다. 에러가 `f = open(...)`직후에 발생한다면, `f.close()`를 호출하지 않습니다. \(파이썬 인터프리터에 따라 파일 핸들을 반환할 수 있지만, 그건 다른 이야기입니다.\) 예외의 유무에 관계없이 파일을 닫으려면  `with` 를 활용해서 감싸주어야합니다.

```python
with open('photo.jpg', 'r+') as f:
    jpgdata = f.read()
```

`open`의 첫 번째 전달 인자는 파일이름입니다. 두 번째는 어떤 방식\(모드\)으로 파일을 여는지 결정합니다.

* 읽기로만 열고 싶다면, `r`을 사용합니다.
* 읽기와 쓰기로 파일을 불러오고 싶다면, `r+` 을 사용합니다.
* 파일에 덮어쓰기를 하고 싶다면, `w`을 사용합니다.
* 파일에 더해서 쓰고 싶다면, `a`을 사용합니다.

다른 모드로 바꾸는 글자도 몇 개 더 있지만, 사용할 일은 아마 없을 것입니다. 위 모드들은 동작 방식을 바꿀 뿐만 아니라, 권한 오류가 발생할 수도 있기 때문에 중요 합니다. 예를 들어, 쓰기 제한이 걸려있는 디렉토리에서 jpg-파일을 열려고 하면, `open(..., 'r+')`은 실패할 것입니다.  
모드에는 추가 문자가 하나 포함될 수 있습니다. 파일을 바이너리 \(바이트 문자열\) 또는 텍스트 모드 \(문자 스트링\)로  열 수 있습니다.

일반적으로 사람이 작성했다면 텍스트 모드일 가능성이 높습니다. `jpg`이미지 파일은 일반적으로 사람이 직접 작성할 수는 없고 \(사람이 읽을 수도 없습니다\), b를 더해서 바이너리 모드로 열어야합니다.\(위 예제를 따라오고 있다면, 정확하게 말하면 `rb`여야 합니다.\)  
텍스트 모드로 파일을 연다면 \(즉 `r/r+/w/a` 와는 별개로 `t`를 추가한\), 어떤 인코딩을 사용해서 열어야할 지 정확히 알아야합니다. 컴퓨터의 경우 모든 파일은 문자열이 아니라 바이트\(0,1\) 일 뿐입니다.

불행히도, open함수는 파이썬 2.x에서는 인코딩하는 방식을 명시할 수 없습니다. 그렇지만 함수 '[io.open](http://docs.python.org/2/library/io.html#io.open)'은 파이썬 2.x 와 3.x을 지원하고, \(`open`의 다른 이름으로 불리웁니다\) 인코딩 방식을 지정할 수 있습니다. `encoding` 키워드으로 인코딩을 전달할 수 있습니다. 인코딩을 적지않고 통과시키면 시스템 및 파이썬 고유의 기본값이 선택됩니다. 이 기본값을 그대로 사용하고 싶겠지만, 기본값이 잘못된 경우가 많거나 기본 인코딩을 통해서는 파일안의 모든 문자들을 실제로 표현할 수는 없습니다.\(python2.x나 윈도우에서 자주 발생합니다.\) 그래서 인코딩방식을 선택하고 진행하는 게 좋습니다. `utf-8`은 훌륭한 선택입니다. 파일 안에 쓰기를 할 때에는 선호하는 인코딩 방식을 선택하면 됩니다. \(혹은 궁극적으로 파일을 읽는 프로그램이 선호하는 방식을 선택하면 됩니다.\)

어떤 인코딩으로 파일을 썼는지 어떻게 알 수 있을까요? 불행히도 인코딩방식을 찾는 만병통치약은 없습니다. 동일한 바이트라도 의미하는 것이 다를 수도 있고, 동일한 문자라도 다른 인코딩 방식을 사용할 수도 있기 때문입니다. 그래서 인코딩방식을 알기위해서는 꼭 \(HTTP 헤더 같은\) 메타 데이터에 의존해야합니다. 점점 UTF-8로 포맷을 결정짓는 추세입니다.

위 지식들을 체득했으니 파일을 읽는 프로그램을 만들어봅시다.  JPG\(힌트: 파일은 바이트 `FF D8`로 시작합니다.\)인지 확인하고, 입력 파일을 설명하는 글을 텍스트 파일에 작성 해봅시다.

```python
import io

with open('photo.jpg', 'rb') as inf:
    jpgdata = inf.data()

if jpgdata.startwith(b'\xff\xd8'):
    text = u'이 파일은 (%d 바이트 짜리) JPEG 파일입니다. \n'
else:
    text = u'이 파일은 (%d 바이트 짜리) 랜덤 파일입니다. \n'

with io.open('summary.txt', 'w', encoding='UTF-8') as outf:
    outf.write(text % len(jpgdata))
```

이제 `open`을 올바르게 사용하실 수 있을 것 같습니다!

