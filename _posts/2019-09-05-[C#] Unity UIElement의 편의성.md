 
## [C#] UIElement의 사용 

추천 유니티 공식문서<br>
https://github.com/Unity-Technologies/UIElementsExamples - 예제 소스<br>
https://blogs.unity3d.com/kr/2019/04/23/whats-new-with-uielements-in-2019-1/ - 소개글 
#### 이 글은 유니티 2019.1.11f1 기준으로 작성되었습니다. 
 
   IMGUI로 에디터의 UI를 만드려고 하다보면 사실 번거롭게 느껴지는게 많습니다.
   코드를 순차적으로 써 내려가며 레이아웃을 작성해야 하기에 코드는 쉽게 지저분해지기 마련이고
   IMGUI는 제가 느끼기엔 유지보수 가능하게끔 코드를 잘 짜려면 코드가 길어지고, 분할되고, 그만큼 시간이 걸립니다.

   UIElement를 처음 접했을때 HTML과 CSS의 느낌을 강하게 받았습니다. 
   wpf나 html,css로 레이아웃 디자인을 해보신분들은 알겠지만 UIElement는 레이아웃 설계하는게 정말 쉽다는게 강점입니다.
   
   레이아웃을 쉽게 작성하고, 해당 레이아웃에 있어야할 기능의 코드가 쉽게 관리 될 수 있다면 
   복잡하지않고 빠르게 에디터를 작성할 수 있을것입니다.
 
   uxml, uss로 관리할때의 장점은 레이아웃,디자인 변경에 있어 (동적으로 생성하는 UI가 아닌경우) 유니티 컴파일이 필요없다는겁니다.
   익숙해지기만 한다면 IMGUI보다 개발 속도는 빨라질거고요.

   물론 단순한 에디터를 만드는데는 아직 IMGUI가 더 빠르고 쉽습니다. 그냥 OnGUI코드를 만들고 코딩하면 되니까요.
   하지만 레이아웃 설계가 확실해야하는 에디터는 UIElement를 사용해보시길 추천드립니다. 
   ----------- 

## VisualElement로 레이아웃을 만들기

-  **Scripting API에서는 UIElements의 기본이 되는 클래스로 설명하고 있습니다.
   VisualElement 클래스는 보통 레이아웃을 처리할때 사용됩니다. 
   버튼이나 인풋필드, 프로퍼티 필드등은 VisualElement를 상속받아 추가구현됩니다.
   UIElement에서 사용하는 모든 렌더링된 객체들은 VisualElement를 상속받는다고 보시면 됩니다.

 
 그럼 VisualElement를 이용해서 레이아웃을 먼저 잡아봅시다. 
 ![UI_Element](https://raw.githubusercontent.com/shlifedev/shlifedev.github.io/master/assets/images/43.PNG)
 우리는 이런 형태의 UI를 만들것이고, 정말 쉽게 가능합니다. 
 만드는 방법은 아래와 같습니다.

   1. UIElement C# API 스크립팅으로만 짜는법 (컴파일 필요, IMGUI처럼 코드에 때려박는 형식)
   2. Uxml, Uss를 작성해서 짜는법. **(인터프리터로 작동하여 컴파일 시간이 따로 필요 없음)**
   
일단은 둘다 해볼것입니다.
1번의 경우 IMGUI처럼 간단하게 OnGUI에 떄려박듯이 구현한다고 생각하시면 됩니다. 
굳이 복잡하게 레이아웃을 잡을 필요 없는경우엔 편리하겠죠. <br>
보통은 1+2번을 같이 조합해서 사용하는 경우가 많을것입니다. <br>
(for문을 돌려서 리스트의 요소를 여러개 생성하려면 컴파일이 필요하니깐.) <br> 


# C#을 이용해서만 레이아웃 작성하는방법 
 
이 섹션을 이해해야 VisualElement의 클래스가 어떻게 동작하는지 알 수 있으므로<br> 
한번 코드를 흟어보고 이해하고 넘어가는게 좋습니다. 

-----------

아래 코드는 레이아웃을 잡기위한 코드입니다.

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

실행해서 윈도우탭을 열면 파란색 타이틀 바, 짙은 회색 바디가 나올것입니다.<br>
**root** 엘리먼트는 기본적으로 EditorWindow의 최상위 VisualElement입니다.<br>
그 밑에 TitleElement, BodyElement를 자식으로 넣어주고 크기를 설정했을 뿐입니다.

그럼 아래코드를 입력해서 완성하고자 했던 그림을 완벽하게 구현해봅시다.<br> 

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

 기존 C# 코드를 보면 너무 지저분합니다.<br>
 물론 제가 정리를 안하고 막코딩 한것도 있지만.. 저렇게 하면 결국 IMGUI 스타일입니다. <br>
 Uxml,Uss를 이용해서 작성해보려고 합니다.

 
 
### Uxml의 작성
아래 코드를 복사해서 Assets/Editor/ElementTest.uxml 경로로 저장.<p> 
HTML태그처럼 열고 닫히고를 반복하며 사용법도 비슷합니다.


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
CSS라는 언어와 90%정도 유사한거 같으니 나중에 css도 참고해보시면
**width**, **height**, **background-color**...등의 여러 필드들을 배우실 수 있습니다.

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

훨씬 더 깔끔합니다.<br>
UI 레이아웃 변경이 필요한경우 언제든지 uss에서 수정하면 되고 새로운 UI의 (버튼이나.. 인풋필드나.. 기타등등의) 추가가 필요한경우 단순히 uxml에서 수정만 하면 됩니다. 즉, cs코드를 건들 필요가 없습니다.  

코드로 칠때랑은 다르게 컴파일 과정이 필요가 없습니다.<br> 

 ![UI_Element](https://raw.githubusercontent.com/shlifedev/shlifedev.github.io/master/assets/images/changeUSS.gif)

이제 위와 같이 USS파일의 내용을 변경하므로서 바로바로 Element의 변화를 확인 할 수 있습니다.

## 디자인 탬플릿 정의후 사용

 빨간버튼에 대한 디자인 탬플릿을 정의하고 언제든지 이를 가져다 쓰려고 합니다. RedButton.uss와 RedButton.uxml 파일만 만들면 됩니다.


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
 
 UIElement라는게 있길래 한번 써 봤습니다.  
 저 또한 사용법을 완벽 숙지한것은 아니지만 확실한건 복잡한 레이아웃을 쉽게 설계 할 수 있습니다.  
 GUIStyle같은거 때문에 골머리 썩을필요없이 uss에서 처리할 수도 있구요!  
 관심 있으신분들은 사용해보시길..