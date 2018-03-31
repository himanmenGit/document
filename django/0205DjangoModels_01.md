# Django Models
모델은 데이터어에 대한 단일 정보 소스다. 일반적으로 각 모델은 단일 데이터베이스 테이블에 매핑된다. 여기에는 저장중인 데이터의 필수 필드와 동작이 포함 되어 있다.

* 모델의 각 속성은 데이터베이스의 필드를 나타낸다.

* 장고는 자동으로 생성된 데이터베이스 엑세스 API(ORM)를 제공한다.
* 각 필드는 클래스 속성으로 지정되며 각 속성은 데이터베이스 열에 매핑된다.
* 테이블 이름은 일부 모델 메타 데이터에서 자동으로 파생되지만 재정의 될 수 있다. (myapp의 person 테이블은 myapp_person이라는 이름의 테이블로 자동으로 만들어 진다.)
* ID 필드는 자동으로 추가되지만 동작은 무시 될 수 있다.

### 모델 사용하기
* 모델을 정의 한 후에는 장고에세 해당 모델을 사용 할 것이라고 알려야 한다. `config/settings.py`에 `INSTALLED_APPS`설정에 해당 패키지 이름을 추가 해야 한다.
* `INSTALLED_APPS`에 새 앱을 추가 할 때는 `manage.py migrate`를 실행하고 선택적으로 `manage.py makemigraions`를 사용하여 마이그레이션을 수행 해야 한다. 왜 선택적인 마이그레이션을 해야 하는 지는 우리가 만든 앱이 아닌 받아온 서드파티 라이브러리에서도 모델이 존재 하거나 장고에서 기본적으로 사용하는 모델 마이그레이션이 존재 할 수 있기 때문이다.

### 필드
필드는 모델의 가장 중요한 부분이자 필수 부분이다. 데이터 베이스의 필드 목록이고 클래스 속성에 의해 지정된다. 모델 API와 충돌하는 필드 이름을 사용하지 말고 필드 이름을 `clean`, `save`, `delete`를 사용하지 않도록 주의 해야 한다.


### 필드 타입
모델의 각필드는 해당 필드 클래스의 인스턴스여야 한다. 장고는 필드 클래스 유형을 사용하여 몇가지를 결정한다.
* 데이트베이스에 저장할 데이터 종류를 나타내는 열 유형 이다.(CharField 문자열)
* 여기서 생성 된 필드가 HTML 폼 위젯 생성시 어떤걸 사용할지 정해 진다.
* 모델에 필드가 정의 되어 있으면 장고에서 폼을 자동으로 만들어 준다.
* 필드를 만들때 사용하는 최소 요구 사항을 나타낼 수 있다. 그러면 HTML 프론트 단에서 에러를 발생 시키게 할 수 있다. (max_length=10) 
* 장고에는 많은 내장 필드 타입이 존재 하는데 그중 원하는 것이 없다면 사용자 정의 모델 필드를 만들 수 있다. (전화번호 필드, 주소 필드 등)


### 필드 옵션
* 각 필드는 필드 특정 인수 세트를 사용한다. 예를들어 `CharField`에는 데이터베를 저장하는데 사용되는 `VARCHAR` 데이터베이스 필드의 크기를 지정하는 `max_length`인수가 필요 하다.
* 각 필드마다 지정해주어야 하는 필수 필드 옵션들이 있다.

모든 필드 유형에서 사용 할 수 있는 일반적인 인수가 있는데 모두 선택 사항이다. 자주 사용되는 것들에 대해 간단한 요약이 있다.
**null**
* 데이터 베이스에 빈값을 null로 하고 싶으면 `null=True`를 꼭 넣어야 한다. 이것은 없는 것
**blank**
* 데이터베이스에 빈값을 넣을 수 있다. 이것은 빈 '값'(빈 문자열)
> `null`과 `blank`는 다른데 `null`은 순전히 데이터베이스와 관련되어 있고 `blank`는 유효성 검사와 관련이 있다. 만약 `blank=True`면 폼 유효성 검사에서 빈 값을 넣을 수 있다.
> 문자열 필드는 `null=True`를 사용하게 되면 데이터가 없는 것에 대한 유효성 검사가 두가지의 조건을 만족하게 되므로 그런 경우를 피하기 위해서 장고에서도 `blank=True`만 사용하는것을 추천한다.
* 하지만 `blank`옵션은 제한적으로 사용이 가능하다. `DateField`같은 경우는 데이터베이스 차원에서 빈값을 허용하지 않는다. 없거나 있거나 둘중 하나여야 한다.그래서 `blank=True`만 사용이 불가능 하고 `null=True`를 사용 하여야 한다. 만약 `null=True`를 늦게 넣었다면 `makemigrations`와 `miagrate`를 다시 해주어야 한다.

**choices**
* 튜플 형태의 반복 가능한 선택 항목 으로 이것이 주어지면 폼은 기본 텍스트 필드 대신 선택 상자로 바뀌고 주어진 선택 사항으로 항목을 제한함.
```
SHIRT_SIZE = (
    ('S', 'Small'),
    ('M', 'Medium'),
    ('L', 'Large'),
)

shirt_size = models.CharField(max_length=1, choices=SHIRT_SIZE)
```
```
def __str__(self):
    return f'{self.get_shirt_size_display()}'
```
앞의 짧은 단어를 데이터 베이스에 저장하고 렌더링 되는 부분에서는 뒤의 문자열을 사용하는 것.
그리고 이렇게 만들어 놓으면 자동 생성 폼에서 쉽게 사용 가능하다.
그래서 데이터 베이스의 용량절약이 가능하다.

**default**
객체를 생성할때 값을 주지 않으면 기본 으로 들어 가는 값.

**help_text**
폼 위젯과 함께 표시되는 추가 도움말 텍스트. 문서화에 유용하다.

**primary_key**
`primary_key=True`면 필드는 모델의 기본키 이다. 장고는 기본으로 사용하는 키를 자동으로 생성하는데 그것이 pk, id라고 불리는 것이다. 해당 필드옵션을 주지 않게 되면 장고가 자동으로 `IntegerField`필드로 `primay_key`를 생성한다. 반드시 해당 필드를 생성할 필요는 없지만 만약에 만들 경우 해당 필드는 자동으로 생성되지 않는다.
그리고 이 키는 읽기 전용이며 기존 객체의 기존 키 값을 변경 후 저장 하게 되면 새로운 객체가 만들어 진다.

**unique**
`True`면 해당 필드에서 값이 고유하여야 한다.

### 자동 기본 키 필드 (Automatic prmary key fields)
만약 사용자 정의 기본 키 필드를 지정 하려면 필드 중 하나에 `primary_key = True` 옵션을 지정 하면된다. 그러면 장고가 `Field.primay_key` 를 명시적으로 설정 했다고 판단하여 자동으로 `id`를 추가 하지 않는다.

그래서 반드시 `primary_key=True` 갖기 위해 하나의 필드가 필요 하다.

### 자세한 필드 이름 ( Verbose field names)
`ForeignKey, ManyToManyField`, `OneToOneField`를 제외한 각 필드 유형은 선택적인 첫 번째 위치 인수에 `Verbose name`을 사용한다. `Verbose name`이 주어지지 않으면 장고는 필드의 속성 이름을 사용하여 밑줄을 공백으로 변환 자동으로 생성 한다.
```
name = models.CharField('이름', ...)
```
그리고 관계형 필드의 경우 반드시 `verbose_name`이라는 키워드 인자를 사용하여야 한다. 관계형 필드는 첫 번째 인자가 어디와 관계 할 것인지 가르키고 있기 때문이다.

### PrimaryKey(기본 키) 란?
관계형 데이터베이스에서 레코드의 식별자로 이용하기에 가장 적합한 것으로 설계자에 의해 선택, 정의된 후보 키를 말한다. 테이블에 수많은 데이터가 존재 할 때 하나의 데이터를 가르키기에 좋다. 인덱싱이 되어 있기 때문 정렬이 되 이을 경우 빠르게 데이터를 가져 올 수 있다

### API 란?
API(Application Programming interface, 응용 프로그래밍 인터페이스)
응용 프로그램에서 사용할 수 있도록, 운영 체제나 프로그래밍 언어가 제공하는 기능을 제어할 수 있게 만든 인터페이스를 뜻 한다.

어떤 서비스가 가지고 있는 내부 서비스를 사용하기 쉽게 추상화 하여 사용자에게 제공 하는 것.

예를 들면 응용프로그램 사이에서도 인터페이스가 일어 나는데 데이터 베이스의 가장 아랫 단에서 SQL문을 쓰기가 복잡하고 어려울때 파이썬을 이용해서 데이터베이스와 연결하여 사용 하는 경우.
```
DB - API
Database - python-sqlite3 - python - ORM - Django
```

ORM을 사용하여 데이터베이스에 접근 할 수 있다.