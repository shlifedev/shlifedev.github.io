 
## [C#] UIElement의 사용

   나는 아직 IMGUI에 익숙하다. 하지만 IMGUI 코드는 짜다보면 
   정갈하게 코드 정리 하기도 쉽지 않을 뿐더러 한번 디자인을 하고 나면 다른 레이아웃으로 변경하는게 보통일이 아니다. 

   UIElement를 처음 접했을때 HTML과 CSS의 느낌을 강하게 받았다. 
   확실히 HTML이나 CSS처럼 GUI가 관리 될 수 있다면 이건 개발자, 디자이너 서로에게 엄청난 생산성 증가를 가져온다. 프로그래머는 확장에 대한 걱정을 할 필요가 없다. 

   UIElement는 uxml, uss 라는 확장자로 나뉘어 관리된다. uxml은 버튼,인풋필드,레이아웃 등의 형상을 관리하고 uss는 버튼의 색상, 크기, 레이아웃의 사이즈, 정렬기준등을 지정하는 스타일시트 개념이된다. HTML로 따지면 uxml이 HTML이고, uss가 css가된다.

   이 문서에서는 UIElement의 대단함(?), 편리함(?)을 써내려 가볼려고한다. 사실 HTML, CSS의 개념이 잡혀있는 웹을 취미로 해본 분이라면 정말 쉽게 접근할 수 있다.

## VisualElement란?

    Scripting API에서는 UIElements의 기본이 되는 클래스로 설명하고있다. 레이아웃을 처리할때 사용되며 버튼이나 인풋필드, 프로퍼티 필드등도 렌더링을 위해 VisualElement에서 파생된다.

 조금더 쉽게 설명 하자면 그냥 레이아웃을 짜기위한 객체라고 볼 수 있다. 레이아웃 처리와 더불어 이벤트 처리하는 것들도 있지만 기본적으론 레이아웃을 처리하는데에 많이 쓰게 될 녀석이다.  
 
 자, 다같이 따라서 아래와 같은 레이아웃을 한번 구현해보자!!

 ![UI_Element](https://raw.githubusercontent.com/shlifedev/shlifedev.github.io/master/assets/images/layout_element.PNG)

 우리가 위와같은 레이아웃을 짜는 방법은 두가지가 있다.

   1. UIElement를 사용한 C# 스크립팅으로 짜는법 (컴파일 필요)
   2. Uxml, Uss를 작성해서 짜는법. (컴파일 필요 없음)
   
일단은 둘다 해볼것이다. 간단하게 1번부터 같이 구현해보자.
첫번째로 아래 코드를 그대로 입력하고 먼저 실행을 해보면, 간단한 네모 레이아웃만 잡혀서 나올것이다.

**(VisualElement)x.style** 부분이 가장 중요한데 이곳에서 스타일을 지정한다. 우리는 2번에서는 이 스타일을 uss라는 파일에서 따로 관리할것이다. 그렇게되면 코드를 수정하는게 아니기때문에 컴파일이 필요없어진다. 하지만 우리는 일단 스크립트에서 작성해보자.

```csharp
using UnityEditor;
using UnityEngine;
using UnityEngine.UIElements;
using UnityEditor.UIElements;


public class ElementTest : EditorWindow
{
    [MenuItem("Window/UIElements/ElementTest")]
    public static void ShowExample()
    {
        ElementTest wnd = GetWindow<ElementTest>();
        wnd.titleContent = new GUIContent("ElementTest");
    }

    public void OnEnable()
    { 
        //기본 루트 
        VisualElement root = this.rootVisualElement;
        
        //내가 만들 레이아웃 Elements.
        VisualElement TitleElement = new VisualElement();
        VisualElement BodyElement = new VisualElement();
        VisualElement FootElement = new VisualElement();
        VisualElement LogElement = new VisualElement();


        //root에 TitleElement를 자식으로 추가한다.
        root.Add(TitleElement);
        //사이즈 설정 
        TitleElement.style.height = 20;
        TitleElement.style.backgroundColor = Color.blue;

        //root에 BodyElement 자식으로 추가한다.
        root.Add(BodyElement);
        //사이즈 설정 
        BodyElement.style.height = 150;
        BodyElement.style.backgroundColor = Color.cyan;

        //root에 FootElement 자식으로 추가한다.
        root.Add(FootElement);
        //사이즈 설정 
        FootElement.style.height = 40;
        FootElement.style.backgroundColor = Color.green;

        //root에 LogElement 자식으로 추가한다.
        root.Add(LogElement);
        //사이즈 설정 
        LogElement.style.height = 100;
        LogElement.style.backgroundColor = Color.black;
    }
}

```

실행하면 레이아웃 4개가 Blue, Cyan, Green, Black 순서대로 나뉘어져서 보여질것이다.
 HTML로 따지면 DIV태그 4개를 쓴거랑 똑같다고 보면된다. 그러면 저곳에 타이틀, 버튼1,2 ,확인취소, 로그를 달아보자. 좀 많이 길어지고 지저분하니 주의.. 

(코드 이해를 돕기 위해 함수처리를 따로 하진 않겠음)

```csharp
using UnityEditor;
using UnityEngine;
using UnityEngine.UIElements;
using UnityEditor.UIElements;


public class ElementTest : EditorWindow
{
    [MenuItem("Window/UIElements/ElementTest")]
    public static void ShowExample()
    {
        ElementTest wnd = GetWindow<ElementTest>();
        wnd.titleContent = new GUIContent("ElementTest");
    }

    public void OnEnable()
    {
        VisualElement root = this.rootVisualElement;

        VisualElement TitleElement = new VisualElement();
        VisualElement BodyElement = new VisualElement();
        VisualElement FootElement = new VisualElement();
        VisualElement LogElement = new VisualElement();


        //root에 TitleElement를 자식으로 추가한다.
        root.Add(TitleElement);
        //사이즈 설정 
        TitleElement.style.height = 20;
        TitleElement.style.backgroundColor = Color.blue;

        //root에 BodyElement 자식으로 추가한다.
        root.Add(BodyElement);
        //사이즈 설정 
        BodyElement.style.height = 150;
        BodyElement.style.backgroundColor = Color.cyan;

        //root에 FootElement 자식으로 추가한다.
        root.Add(FootElement);
        //사이즈 설정 
        FootElement.style.height = 40;
        FootElement.style.backgroundColor = Color.green;

        //root에 LogElement 자식으로 추가한다.
        root.Add(LogElement);
        //사이즈 설정 
        LogElement.style.height = 100;
        LogElement.style.backgroundColor = Color.black;

        //라벨 생성, 스타일지정, 추가
        Label titleLabel = new Label();
        titleLabel.style.color = Color.white;
        titleLabel.text = "Visual Element 테스트";
        titleLabel.style.unityTextAlign = new StyleEnum<TextAnchor>() { value = TextAnchor.MiddleCenter };
        TitleElement.Add(titleLabel);


        //추가되는 자식들을 '열' 기준으로 정렬한다. (이 옵션을 주지 않으면 버튼1,2가 Element안에 세로로 나열되어 들어감.)
        //한마디로 이 옵션은 기본 GUI에 BeginHorizontal(); 과 같다.
        BodyElement.style.flexDirection = new StyleEnum<FlexDirection>() { value = FlexDirection.Row };
        BodyElement.style.alignItems = new StyleEnum<Align>() { value = Align.Center };
        Button body_btn_1 = new Button();
        body_btn_1.style.width = 70;
        body_btn_1.style.height = 70;
        body_btn_1.text = "버튼1";
        BodyElement.Add(body_btn_1);

        Button body_btn_2 = new Button();
        body_btn_2.style.width = 70;
        body_btn_2.style.height = 70;
        body_btn_2.text = "버튼2";
        BodyElement.Add(body_btn_2);


        //추가되는 자식들을 '열' 기준으로 정렬한다. (이 옵션을 주지 않으면 버튼1,2가 Element안에 세로로 나열되어 들어감.)
        //한마디로 이 옵션은 기본 GUI에 BeginHorizontal(); 과 같다.
        FootElement.style.flexDirection = new StyleEnum<FlexDirection>() { value = FlexDirection.Row };
        FootElement.style.alignItems = new StyleEnum<Align>() { value = Align.Center };
        Button foot_btn_ok = new Button();
        foot_btn_ok.style.width = 70;
        foot_btn_ok.style.height = 32;
        foot_btn_ok.style.unityBackgroundImageTintColor = Color.black;
        foot_btn_ok.style.color = Color.white;
        foot_btn_ok.text = "OK"; 
        FootElement.Add(foot_btn_ok);

        Button foot_btn_cancel = new Button();
        foot_btn_cancel.style.width = 70;
        foot_btn_cancel.style.height = 32;
        foot_btn_cancel.style.unityBackgroundImageTintColor = Color.red;
        foot_btn_cancel.style.color = Color.white; 
        foot_btn_cancel.text = "Cancel";
        FootElement.Add(foot_btn_cancel);


    }
}
```




