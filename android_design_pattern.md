# 디자인 패턴

효율적인 안드로이드 개발을 위해 디자인 패턴인 MVC, MVP, MVVM에 대해 알아본다.

참고사이트
https://thdev.tech/androiddev/2016/10/23/Android-MVC-Architecture/
https://fomaios.tistory.com/entry/Design-Pattern-MVP-%ED%8C%A8%ED%84%B4%EC%9D%B4%EB%9E%80
https://beomseok95.tistory.com/131

# MVC

로직과 시각적 화면을 구분하는 패턴이다. 

모델 - 뷰 - 컨트롤러로 구분되어 있다. 세 기능으로 분리하기 때문에 코드의 확장성, 유연성이 증가하며 유지보수하기 용이하다.

주로 웹에서 사용되며 가장 널리 사용되는 구조 중 하나이다.

모든 입력은 컨트롤러에서 발생한다.

## 모델 - 뷰 - 컨트롤러

<구조>

1. 모델: 데이터와 로직을 관리

2. 뷰: 레이아웃과 화면을 관리, 모델로부터 데이터를 받음

3. 컨트롤러: 사용자 입력에 따른 응답으로 모델, 뷰를 업데이트

<과정>

- 컨트롤러 - 입력발생

- 컨트롤러 - 데이터 갱신이 필요한지 모델에 확인 요청

- 모델 - 데이터 갱신 필요 접수

- 뷰 - 모델 혹은 컨트롤러에서 데이터 갱신 요청 확인

- 뷰 - 모델에서 필요한 데이터를 받아와 뷰 갱신


## 안드로이드의 MVC

안드로이드는 다음과 같은 코드처럼 Activity/Fragment 의 View 들이 뷰와 컨트롤러를 모두 담당한다.

하나의 화면 안에서 컨트롤러인 클릭 이벤트가 발생하고 이를 뷰에서 모두 처리한다. 

웹의 MVC 패턴은 뷰와 컨트롤러가 모두 분리된 상태를 말하지만 안드로이드는 그렇지 못하다.

따라서 class 와 함수 분리를 잘 해야 한다.

<장점>
- Activity에서 모든 걸 동작하게 처리한 해주면 되기에 개발이 쉬울 수 있고 별도의 패턴을 구분하지 않아 모르는 사람이 보기에 읽는 것도 쉬울 수 있다.

<단점>
- 하나의 class 코드 라인 수가 길어질 수 있다.
- 복사 붙여넣기가 많아져 스파게티 코드가 될 수 있다.
- 유지보수가 어렵다.
- 대부분 뷰에서 모델을 직접 호출하기 때문에 뷰와 모델 간의 결합도가 높다.
- 결합도가 높으면 테스트 코드 작성이 어렵다

```java
public class MainActivity extends AppCompactActivity {
  @Override
  protected void onCreate(Bundle savedInstanceState) {
    // ...

    FloatingActionButton fab = (FloatingActionButton) findViewById(R.id.fab);
    fab.setOnClickListener(new View.OnClickListener() {
        @Override
        public void onClick(View view) {
            // 데이터 갱신 요청
            // Model에 접근해서 최신 데이터를 요청
              // ex) ArrayList<String> getItems()
            // 전달받은 ArrayList를 통해 View를 갱신
        }
    });
  }
}
```

# MVP
모델 - 뷰 - 프리젠터 로 분리된 디자인 패턴이다. 

MVC의 컨트롤러가 하는 역할을 프리젠터가 맡게 되며 MVC 모델과 뷰의 의존성을 줄일 수 있다.

오직 프리젠터를 통해서만 뷰와 모델에 상태 변화를 알려줄 수 있다.

뷰와 모델을 분리시키기 때문에 MVC보다 테스트가 용이해졌지만 뷰와 프리젠터의 의존성이 강해져 C대신에 P가 복잡해지는 문제는 여전히 남아있다.

## 모델 - 뷰 - 프리젠터

<구조>

1. 모델: 네트워크, 로컬 데이터 등 전반적인 데이터 처리를 담당

2. 뷰: 이벤트가 발생하는 지점, 이를 프리젠터로 전달, 계산하거나 데이터를 가져오는 것 모두 뷰에서 하지 않음

3. 프리젠터: 뷰에서 전달받은 이벤트를 처리(데이터 가공 포함)하고 다시 뷰에 전달

<과정>

- 뷰: 이벤트 발생, 프리젠터에 전달

- 프리젠터: 이벤트 형태에 따라 캐시 데이터를 가져오거나 모델에 요청

- 모델: 데이터 요청 확인 후 로컬, 서버에서 데이터를 가져와 프리젠터로 전달

- 프리젠터: 전달받은 데이터를 가공 후 뷰에 전달

- 뷰: 전달 받은 데이터로 뷰를 갱신

# MVVM

MVC에서 C가 VM으로 바뀐 패턴이다.

모델 - 뷰 - 뷰모델로 구성되어 있으며 뷰모델은 UI단에 위치한다.

각각의 구성요소는 서로 의존성을 갖지 않으며 뷰->뷰모델->모델 형태의 단방향적인 독립성을 갖는다.

소프트웨어를 최대한 기능적으로 작은 단위로 나눈다.

뷰 로직과 비즈니스 로직을 분리하기 때문에 UI가 나오지 않아도 개발할 수 있다.

뷰모델과 뷰는 일대다 관계이기 때문에 여러개의 뷰(액티비티, 프래그먼트)가 하나의 뷰모델을 가질 수 있다. 

따라서 부작용으로 뷰모델이 거대해질 수 있으며 설계가 복잡할 수 있다.

## 모델 - 뷰 - 뷰모델

<구조>
1. 모델: 비즈니스 로직과 데이터를 다룸. 전반적인 네트워크, 데이터 처리를 담당.

2. 뷰: 사용자에게 화면으로 보여지는 모든 구조, 레이아웃을 포함. 애니메이션 같은 UI로직 담당.

3. 뷰모델: 뷰가 사용할 메서드와 필드를 구현. 뷰에게 상태 변화 전달(옵저빙). 뷰와 모델 사이의 매개체 역할. 모델에서 제공받은 데이터를 필요한 정보로 가공한 뒤 뷰가 가져갈 수 있게 이벤트로 제공. 뷰모델은 뷰에 대한 정보(context)가 전혀 없어야 함. 

<과정>
- 뷰: 이벤트 발생 후 뷰모델로 요청을 전달 

- 뷰모델: 모델에 데이터 요청

- 모델: 데이터를 받아서 뷰모델로 전달

- 뷰모델: 데이터를 가공해 뷰로 전달

- 뷰: 데이터 바인딩을 통해 자동 갱신
