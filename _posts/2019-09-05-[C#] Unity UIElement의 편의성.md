 
## [C#] UIElement의 사용
   **추신: uxml, uss와 친해지려면 html, css를 잠깐만 접해보는걸 추천합니다.**

추천 유니티 공식문서<br>
https://github.com/Unity-Technologies/UIElementsExamples - 예제 소스<br>
https://blogs.unity3d.com/kr/2019/04/23/whats-new-with-uielements-in-2019-1/ - 소개글 
#### 이 글은 유니티 2019.1.11f1 기준으로 작성되었습니다. 

 
   나는 아직 IMGUI에 익숙하다. 하지만 IMGUI 코드는 짜다보면 
   정갈하게 코드 정리 하기도 쉽지 않을 뿐더러 한번 디자인을 하고 나면 다른 레이아웃으로 변경하는게 보통일이 아니다. 

   UIElement를 처음 접했을때 HTML과 CSS의 느낌을 강하게 받았다. 
   확실히 HTML이나 CSS처럼 GUI가 관리 될 수 있다면 이건 개발자, 디자이너 서로에게 많은 이득을
   가져다 줄것이다. 당연히 프로그래머는 확장에 대한 걱정을 할 필요가 없다. 디자이너도 레이아웃의
   변경에 대해서 프로그래머에게 변경이 어려울 거 같다는 답변을 들을 걱정을 할 필요가 없어진다.

   HTMl,CSS와 비슷하게도 UIElement는 uxml, uss 라는 확장자로 나뉘어 관리된다. uxml은 버튼,인풋필드,레이아웃 등의 형상을 관리하고 uss는 버튼의 색상, 크기, 레이아웃의 사이즈, 정렬기준등을 지정하는 스타일시트 개념이된다. HTML로 따지면 uxml이 HTML이고, uss가 css가된다.


   uxml, uss로 관리할때의 장점은 컴파일이 필요없고 프로그래머가 렌더링에 대한 최적화를 고민할 필요가 없다는 점이다. 

   아직 필자도 UIElement를 제대로 사용할 줄 안다고 생각하진 않는다. 하지만 UIElement에 관한 문서를 찾아보기가 힘드므로 조금 접근을 편하게 할 수 있게 하고자 문서를 작성해본다. 이 문서에서는 UIElement의 기본적인 사용법을 소개하고자한다. 사실 HTML, CSS의 개념이 잡혀있는 웹을 취미로 해본 분이라면 정말 쉽게 접근할 수 있다고 생각한다. 

   ----------- 

## VisualElement란?

    Scripting API에서는 UIElements의 기본이 되는 클래스로 설명하고있다. VisualElement 자체만으로는 보통 레이아웃을 처리할때 사용되며 버튼이나 인풋필드, 프로퍼티 필드등도 렌더링을 위해 VisualElement에서 파생되는 클래스이다. 즉, 렌더링, 이벤트처리, 레이아웃처리 등을 위해 사용되는 클래스이다.
 
 그럼 VisualElement를 이용해서 레이아웃을 먼저 잡아보겠다.

 ![UI_Element](https://raw.githubusercontent.com/shlifedev/shlifedev.github.io/master/assets/images/layout_element.PNG) 

 구현에 대한 방법은 두가지가있다.

   1. UIElement C# API 스크립팅으로만 짜는법 (컴파일 필요)
   2. Uxml, Uss를 작성해서 짜는법. **(컴파일 필요 없음)**
   
일단은 둘다 해볼것이다. 하지만 **1번의 경우** 1번만을 사용하면 
IMGUI API로 코딩하는거랑 별반 다를 게 없다. 
보통은 1+2번을 같이 조합해서 사용해야 한다. 
1번만 쓰면 차라리 숙련도 있는 IMGUI 쓰는게 더 낫고.. 2번의 방식만 쓰면 표현할 데이터의 개수가 유동적일때 대처가 안된다. 

2번 방법만 쓰는것도 마찬가지다. uxml같은 경우 기존 C#코드처럼 반복문이나 조건문을 못달고 C#에 데이터 참조에 한계가 있기에 데이터 변경에 따른 유동적인 처리가 불가능하다. (아이템이 1000개면 1000개를 uxml에 다 넣을순 없으니까.. for문으로 넣어야..)
 
 그래서 uxml, uss는 미리 템플릿을 만드는 용도라고 생각하면된다.
 빨간색 버튼에 대한 템플릿을 만들어놓고 그 빨간색 버튼을 가져와서 재사용 하는 등. 본인이 사용해보면서 익힌 사용법에 불과하지만 본인은 uxml, uss는 단순한 디자인 탬플릿에 그쳐 사용하는게 맘편하다고 생각하고있다.
 
 <br> 

그래서 필자 말대로라면 실제 사용할땐 1+2번을 써야하는데 일단 1번, 2번 따로 쓰는 방법을 보여주려고 한다.
간단하게 1번부터 같이 구현해보자.  


# C#을 이용해서만 레이아웃 작성하는방법 (안좋은 방법) 
 
안좋은 방법이라고 써놨지만 이 섹션을 이해해야 VisualElement의 클래스가 어떻게 동작하는지 알 수 있으므로 한번 코드를 흟어보고 이해하고 넘어가는게 좋다.

그리고 어느정도 레이아웃 추상화 단계적인 부분은 uxml 작성보단 코드로 치는게 더 빠르기 떄문에 
코드로 레이아웃을 잡은다음 uxml, uss로 재작성하는것도 나쁘지 않다.

-----------

아래 코드는 레이아웃을 잡기위한 코드이다. 

```csharp

using UnityEditor;
using UnityEngine;
using UnityEngine.UIElements;
using UnityEditor.UIElements;


public class ElementTest : EditorWindow
{
    static ElementTest wnd;
    [MenuItem("Window/UIElements/ElementTest")] 
    public static void ShowExample()
    {
        wnd = GetWindow<ElementTest>();
        wnd.titleContent = new GUIContent("ElementTest");
        wnd.maxSize = new Vector2(300, 100);
        wnd.minSize = new Vector2(300, 100);
    }

    public void OnEnable()
    {
        VisualElement root = this.rootVisualElement;

        VisualElement TitleElement = new VisualElement();
        VisualElement BodyElement = new VisualElement(); 

        //root에 TitleElement를 자식으로 추가한다.
        root.Add(TitleElement);
        //사이즈 설정 
        TitleElement.style.height = 20;
        TitleElement.style.backgroundColor = Color.blue;

        //root에 BodyElement 자식으로 추가한다.
        root.Add(BodyElement);
        //사이즈 설정  
        BodyElement.style.height = 80;
        BodyElement.style.backgroundColor = new Color(0.1f,0.1f,0.1f); 
    }
}


```

실행해서 윈도우탭을 열면 파란색 타이틀 바, 짙은 회색 바디가 나올것이다. **root** 엘리먼트는 기본적으로 EditorWindow의 최상위 VisualElement이다. 그 밑에 TitleElement, BodyElement를 자식으로 넣어주고 크기를 설정했을 뿐이다.

그럼 아래코드를 입력해서 완성하고자 했던 그림을 완벽하게 구현해보자.
 

```csharp

using UnityEditor;
using UnityEngine;
using UnityEngine.UIElements;
using UnityEditor.UIElements;


public class ElementTest : EditorWindow
{
    static ElementTest wnd;
    [MenuItem("Window/UIElements/ElementTest")] 
    public static void ShowExample()
    {
        wnd = GetWindow<ElementTest>();
        wnd.titleContent = new GUIContent("ElementTest");
        wnd.maxSize = new Vector2(300, 100);
        wnd.minSize = new Vector2(300, 100);
    }

    public void OnEnable()
    {
        VisualElement root = this.rootVisualElement;

        VisualElement TitleElement = new VisualElement();
        VisualElement BodyElement = new VisualElement(); 

        //root에 TitleElement를 자식으로 추가한다.
        root.Add(TitleElement);
        //사이즈 설정 
        TitleElement.style.height = 20;
        TitleElement.style.backgroundColor = Color.blue;

        //root에 BodyElement 자식으로 추가한다.
        root.Add(BodyElement);
        //사이즈 설정  
        BodyElement.style.height = 80;
        BodyElement.style.backgroundColor = new Color(0.1f,0.1f,0.1f);
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
        BodyElement.style.justifyContent = new StyleEnum<Justify>() {value = Justify.Center };


        // 버튼 생성, 스타일지정, 추가.
        Button body_btn_1 = new Button();
        body_btn_1.style.width = 70;
        body_btn_1.style.height = 30;
        body_btn_1.style.unityBackgroundImageTintColor = Color.blue;
        body_btn_1.style.color = Color.white;
        body_btn_1.text = "Send";
        BodyElement.Add(body_btn_1);

        
        Button body_btn_2 = new Button();
        body_btn_2.style.width = 70;
        body_btn_2.style.height = 30;
        body_btn_2.style.unityBackgroundImageTintColor = Color.red;
        body_btn_2.style.color = Color.white;
        body_btn_2.text = "Error";
        BodyElement.Add(body_btn_2); 

    }
}    

```

 **적용된 모습**

 ![UI_Element](https://raw.githubusercontent.com/shlifedev/shlifedev.github.io/master/assets/images/43.PNG)
 

## Uxml, Uss를 이용해서 1번 레이아웃 똑같이 작성하기

 기존 C# 코드를 보면 너무 지저분하다. 물론 내가 정리를 안하고 막코딩 한것도 있지만.. 저렇게 하면 결국 IMGUI 스타일이다. 나는 Uxml,Uss를 이용해서 작성해보려고한다. 

 
 
### Uxml의 작성
아래 코드를 복사해서 Assets/Editor/ElementTest.uxml 경로로 저장.<p> 
HTML태그처럼 열고 닫히고를 반복하며 사용법도 비슷하다.


```xml

<?xml version="1.0" encoding="utf-8"?>
<engine:UXML
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:engine="UnityEngine.UIElements"
    xmlns:editor="UnityEditor.UIElements"

    xsi:noNamespaceSchemaLocation="../../UIElementsSchema/UIElements.xsd"
    xsi:schemaLocation="
                        UnityEngine.UIElements ../../UIElementsSchema/UnityEngine.UIElements.xsd
                        UnityEditor.UIElements ../../UIElementsSchema/UnityEditor.UIElements.xsd
                        UnityEditor.PackageManager.UI ../../UIElementsSchema/UnityEditor.PackageManager.UI.xsd
"
>
<!-- 사실 여기서부터가 코드의 시작이다. 긴 코드가 아님. 겁먹지말자-->
  <engine:VisualElement class ="Title">
    <!-- VisualEmenet Title의 태그 안에 있으므로 자식 오브젝트가 된다.--> 
     
    <engine:Label text ="Uss VisualElement Test">
    </engine:Label>
  </engine:VisualElement>

  <engine:VisualElement class ="BodyElement">
    <engine:Button class ="ButtonOK" text="OK"/>
    <engine:Button class ="ButtonCancel" text="Cancel"/>
  </engine:VisualElement>

</engine:UXML>
<!-- 코드의 끝-->
``` 

아래 코드를 복사해서 Assets/Editor/ElementTest.uss 경로로 저장.
CSS와 별반 차이없는 문법이지만 USS에서만의 **-unity-text-align**같은 문법도 추가되어있다. 그리고 필자는 웹 개발자가 아니라 몰랐는데 CSS표준은 아래처럼 css 내용을 대문자 선언하지않고 소문자 선언한다는데.. 수정할 시간이 없으므로 양해 바람..
``` css
.Title{
	background-color : #0000ff; 
	height : 20;
} 


/*  .Title 클래스 안에 있는 Label에 적용 */
.Title > Label{
	-unity-text-align: middle-center;
	color : #ffffff;
}



.BodyElement{
	background-color : #121212;
	width : auto;
	height : 90;
	justify-content : center;
	align-items : center;
	flex-direction : row;
} 
.BodyElement > .ButtonOK {
	-unity-background-image-tint-color: #0000ff;
	width : 50;
	height : 25;
	color : #ffffff; 
}
 
.BodyElement > .ButtonCancel {
	-unity-background-image-tint-color: #ff0000;
	width : 50;
	height : 25;
	color : #ffffff; 
}
```

 아래 코드를 복사해서 Assets/Editor/ElementTest.cs 로 저장 
```cs
using UnityEditor;
using UnityEngine;
using UnityEngine.UIElements;
using UnityEditor.UIElements;


public class ElementTest : EditorWindow
{
    static ElementTest wnd;
    [MenuItem("Window/UIElements/ElementTest")]
    public static void ShowExample()
    {
        wnd = GetWindow<ElementTest>();
        wnd.titleContent = new GUIContent("ElementTest");
        wnd.maxSize = new Vector2(300, 100);
        wnd.minSize = new Vector2(300, 100);
    }

    /// <summary>
    /// uxml을 불러와서 Root에 넣어주는 함수. 
    /// </summary>
    /// <param name="path"></param>
    public VisualElement LoadUxmlToRoot(string path, string styleSheetPath = null)
    {
        //루트 트리를 가져온다.
        VisualElement root = this.rootVisualElement; 
        var uxml = AssetDatabase.LoadAssetAtPath<VisualTreeAsset>(path);  
        var loadedVisualElement = uxml.CloneTree(); 
        root.Add(loadedVisualElement);  
        if(styleSheetPath != null)
        { 
            LoadUss(styleSheetPath, loadedVisualElement);
        }
        return loadedVisualElement;
    } 

    /// <summary>
    /// Uss를 불러온다음 target 엘리먼트에 스타일시트 적용 
    /// </summary> 
    public void LoadUss(string path, VisualElement target)
    { 
        var uss = AssetDatabase.LoadAssetAtPath<StyleSheet>(path); 
        Debug.Log(uss); 
        target.styleSheets.Add(uss);   
    }

    public void OnEnable()
    {
        VisualElement root = this.rootVisualElement;
        var titleBody = LoadUxmlToRoot("Assets/Editor/ElementTest.uxml", "Assets/Editor/ElementTest.uss");  
    }
}
```

훨씬 더 깔끔하다. UI 레이아웃 변경이 필요한경우 언제든지 uss에서 수정하면 되고 새로운 UI의 (버튼이나.. 인풋필드나.. 기타등등의) 추가가 필요한경우 단순히 uxml에서 수정만 하면 된다. 코드를 건들 필요가 없다.

코드로 칠때랑은 다르게 컴파일 과정이 필요가 없다.

 


 ![UI_Element](https://raw.githubusercontent.com/shlifedev/shlifedev.github.io/master/assets/images/changeUSS.gif)

이제 위와 같이 우리는 USS파일의 내용을 변경하므로서 바로바로 Element의 변화를 확인 할 수 있다.

## 디자인 탬플릿 정의후 사용

 우리는 빨간버튼에 대한 디자인 탬플릿을 정의하고 언제든지 이를 가져다 쓰려고 한다. RedButton.uss와 RedButton.uxml 파일만 만들면 된다.


아래 코드를 RedButton.uxml로 저장.
```xml
<?xml version="1.0" encoding="utf-8"?>
<engine:UXML
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:engine="UnityEngine.UIElements"
    xmlns:editor="UnityEditor.UIElements"

xsi:noNamespaceSchemaLocation="../../UIElementsSchema/UIElements.xsd"
xsi:schemaLocation="
                        UnityEngine.UIElements ../../UIElementsSchema/UnityEngine.UIElements.xsd
                        UnityEditor.UIElements ../../UIElementsSchema/UnityEditor.UIElements.xsd
                        UnityEditor.PackageManager.UI ../../UIElementsSchema/UnityEditor.PackageManager.UI.xsd
"
>
<!-- 사실 여기서부터가 코드의 시작이다. 긴 코드가 아님. 겁먹지말자-->
  <engine:Button class = "RedButton" text=""> 
  </engine:Button>
<!-- 코드의 끝 -->

</engine:UXML>
```

아래코드를 RedButton.uss로 저장
```css
.RedButton {
    width : 50px;
    height : 50px;
    color : #ffffff;
    -unity-background-image-tint-color: #ff5050; 
}
```


그다음 ElementTest.cs를 변경할껀데 한번 어떤 변화가 있는지 확인해 볼것.
```cs
using System.Collections.Generic;
using UnityEditor;
using UnityEngine;
using UnityEngine.UIElements;
using UnityEditor.UIElements;


public class ElementTest : EditorWindow
{
    public List<string> redButtonTexts = new List<string>(3) {"Hello", "I'm", "Legends"};
    static ElementTest wnd;
    [MenuItem("Window/UIElements/ElementTest")]
    public static void ShowExample()
    {
        wnd = GetWindow<ElementTest>();
        wnd.titleContent = new GUIContent("ElementTest");
        wnd.maxSize = new Vector2(300, 100);
        wnd.minSize = new Vector2(300, 100);
    }

    private VisualElement LoadUxmlToRoot(string path, string styleSheetPath = null)
    {
       
        VisualElement root = this.rootVisualElement; 
        var uxml = AssetDatabase.LoadAssetAtPath<VisualTreeAsset>(path);  
        var loadedVisualElement = uxml.CloneTree(); 
        root.Add(loadedVisualElement);  
        if(styleSheetPath != null)
        { 
            LoadUss(styleSheetPath, loadedVisualElement);
        }
        return loadedVisualElement;
    }

    private void LoadUss(string path, VisualElement target)
    { 
        var uss = AssetDatabase.LoadAssetAtPath<StyleSheet>(path); 
        Debug.Log(uss); 
        target.styleSheets.Add(uss);   
    }

    public void OnEnable()
    {
        VisualElement root = this.rootVisualElement; 

        var elementTestWindow = LoadUxmlToRoot("Assets/Editor/ElementTest.uxml", "Assets/Editor/ElementTest.uss");
        
        
        //Query로 elementTestWindow에서 BodyElement 클래스를 검색해서 가져온다.Query는 VisualElement 트리를 순회해서 원하는 데이터를 검색하는데 사용한다..

        var bodyElement = elementTestWindow.Query().Class("BodyElement").Build().First();
        

        foreach (var t in redButtonTexts)
        {
            var redButtonElement = LoadUxmlToRoot("Assets/Editor/RedButton.uxml", "Assets/Editor/RedButton.uss");
            bodyElement.Add(redButtonElement);

            //Query는 우리가 불러온 RedButton.uxml에서 RedButton 이라는 클래스를 찾아온다. Query는 VisualElement 트리를 순회해서 원하는 데이터를 검색하는데 사용한다.
            var redButton = redButtonElement.Query().Class("RedButton").Build().First() as Button;
            
            //레드 버튼을 가져와서 텍스트를 변경한다.
            if (redButton != null) 
                redButton.text = t;
            //클릭하면 디버그 로그 이벤트가 나온다.
            redButton.RegisterCallback(new EventCallback<MouseUpEvent>((target) => { Debug.Log(t); }));
        }
    }
}
```

완성된 결과.

 ![UI_Element](https://raw.githubusercontent.com/shlifedev/shlifedev.github.io/master/assets/images/show.PNG)


## 마치며 

 시간 여유가 너무 없어서 추후 차마 다 못쓴 내용들을 보강할 예정. 하지만 저정도의 개념만을 이해했다면 사실 한글화가 잘된 유니티 공식문서만 봐도 더 많은 정보와 팁을 얻을 수 있음. 깃허브에서 예제소스 다운받아 보는게 이 글 보고 따라하는것보다 백배 빠를지도 모르겠다. 이 글을 쓴 이유는 UIElement에 대해서 아직 모르는 사람이 있기에 작성 해 본 글. 
 
  