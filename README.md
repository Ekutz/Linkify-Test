# Linkify

![example](http://cfile2.uf.tistory.com/image/25568B3857AB39A708797F)

* 이미지 출처 : [박상권의 삽질블로그 - Linkify로 TextView의 특정단어 클릭 시 URL 이동시키기](http://gun0912.tistory.com/66)

사진의 하단에 파란색 글씨들 처럼 특정 url로 이동할 수 있는 텍스트가 필요한 경우가 있다. 다수의 TextView를 줄지어 일일히 링크를 다는 방법도 있지만 유동적인 방식으로 링크를 지정할 순 없을까? 하는 궁금증을 해소하기 위해 Linkify에 대한 포스트를 시작한다.

## HyperLink

일종의 '바로가기'라고 생각하면 굉장히 편리하다. 텍스트나 이미지에 바로가기 기능을 넣고 보여주고자 하는 url을 포함시켜 클릭만으로 이동할 수 있도록 하는 방식이다.

## Linkify Method

![linkify method](https://github.com/Ekutz/Linkify/blob/master/linkify%20method.png?raw=true)

Developers의 문서에서 보면 사진과 같이 5개의 파라미터를 요구한다.

* 적용될 TextView
* TextView에서 특정 영역을 찾아낼 패턴
* url이 될 String
* matchFilter
* transformFilter

글로만 봐서는 잘 모르겠으니 실제 코드를 보면서 이해해보자.

## 예제

### 1. 텍스트뷰 생성

![example1](https://github.com/Ekutz/Linkify/blob/master/example%201.png?raw=true)

사진과 같이 레이아웃 정중앙에 텍스트뷰를 위치 시켰다. 1개의 TextView로 만들어 졌으며 '구글'이라는 글자에만 링크를 걸 예정이다.

### 2. 패턴 생성

util.regex의 Pattern을 통해 '구글' 이라는 글자를 찾아낼 패턴을 생성합니다.

```
Pattern pattern = Pattern.compile("구글");

```

### 3. Transform Filter 생성

matcher를 이용하여 커스텀하게 url을 뽑아낼 Transform Filter를 생성합니다.

```
Linkify.TransformFilter transformFilter = new Linkify.TransformFilter() {
    @Override
    public String transformUrl(Matcher match, String url) {
        return "";
    }
};

```

### 4. 실제 적용하기

준비를 마친 후 실제로 코드에 적용해 봅니다.

```
textView = (TextView)findViewById(R.id.textView);

Pattern pattern = Pattern.compile("구글");

Linkify.TransformFilter transformFilter = new Linkify.TransformFilter() {
    @Override
    public String transformUrl(Matcher match, String url) {
        return "";
    }
};

Linkify.addLinks(textView, pattern, "https://www.google.com", null, transformFilter);

```

![example2](https://github.com/Ekutz/Linkify/blob/master/example%202.png?raw=true)

구글이라는 글자를 클릭하게 되면 https://www.google.com 으로 이동하게 됩니다!

### 5. 글자색 바꾸기

보통의 링크들은 파란색을 띄기 마련입니다. 하지만 아무런 세팅을 하지 않은 어플리케이션에서는 링크의 색이 분홍빛을 띕니다. 이는 어플리케이션의 기본 색상 설정과 관련이 있습니다.

**app/res/values/color.xml**

![colors](https://github.com/Ekutz/Linkify/blob/master/colors.png?raw=true)

colorAccent의 색상값을 기존의 분홍색에서 #0000FF(파란색)으로 변경할 경우

![color change](https://github.com/Ekutz/Linkify/blob/master/color%20change.png?raw=true)

링크의 색이 파란색으로 변한다.

### 6. url 대소문자 이슈

Transform Filter의 transformUrl 메소드를 거치면 자동으로 url의 모든 글자가 소문자로 바뀌는 현상이 발생한다. 이는 transformUrl 메소드에 toLowercases() 메소드가 자동으로 불러지기 때문이다. 이를 위해 transformUrl을 커스텀 한다.

```
Linkify.TransformFilter transformFilter = new Linkify.TransformFilter() {
    @Override
    public String transformUrl(Matcher match, String url) {
        return "https://www.google.com";
    }
};

Linkify.addLinks(textView, pattern, "", null, transformFilter);

```

Url String은 빈값을 주고 대소문자 상관없이 그대로 값이 나오도록 세팅하는 방식이다. 이 방법을 통하면 Url에 대문자가 포함되어 있어도 그대로 링크를 타고 들어가는 것을 알 수 있다.

### 7. 포스트를 마치며...

독립적인 TextView일 경우 속성 중 하나인 autolink를 이용하는 편리한 방법도 있지만 게시판과 같이 유동적인 url을 이용해야 할때 꽤나 유용한 기능인것 같다. 텍스트 뷰에서 substring을 이용하여 일부만 추출해서 패턴에 적용한다면 충분히 유동적인 링크를 만들기에 적합하다고 생각한다.

[테스트 소스](https://github.com/Ekutz/Linkify.git)
