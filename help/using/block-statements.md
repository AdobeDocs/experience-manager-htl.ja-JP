---
title: HTL ブロックステートメント
description: HTML テンプレート言語（HTL）ブロックステートメントは、既存の HTML に直接追加されるカスタム data 属性です。
translation-type: tm+mt
source-git-commit: 2336ce8860056f5bc6470b23ee4cda135f7107d8
workflow-type: tm+mt
source-wordcount: '1555'
ht-degree: 58%

---


# HTL ブロックステートメント {#htl-block-statements}

HTML テンプレート言語（HTL）ブロックステートメントは、既存の HTML に直接追加されるカスタム `data` 属性です。これを使用すると、プロトタイプの静的 HTML ページに目立たないアノテーションを簡単に付け、HTML コードの妥当性を損なうことなく、機能的な動的テンプレートに変換できます。

## ブロックの概要 {#overview}

HTLブロックプラグインは、HTML要素に設定された `data-sly-*` 属性によって定義されます。 要素には終了タグを含めることも、自己終了タグを含めることもできます。 属性には値(静的な文字列または式)を指定することも、単にブール値の属性（値なし）を指定することもできます。

```xml
<tag data-sly-BLOCK></tag>                                 <!--/* A block is simply consists in a data-sly attribute set on an element. */-->
<tag data-sly-BLOCK/>                                      <!--/* Empty elements (without a closing tag) should have the trailing slash. */-->
<tag data-sly-BLOCK="string value"/>                       <!--/* A block statement usually has a value passed, but not necessarily. */-->
<tag data-sly-BLOCK="${expression}"/>                      <!--/* The passed value can be an expression as well. */-->
<tag data-sly-BLOCK="${@ myArg='foo'}"/>                   <!--/* Or a parametric expression with arguments. */-->
<tag data-sly-BLOCKONE="value" data-sly-BLOCKTWO="value"/> <!--/* Several block statements can be set on a same element. */-->
```

評価されたすべての `data-sly-*` 属性が、生成されたマークアップから削除されます。

### 識別子 {#identifiers}

ブロック文の後に、次の識別子を付けることもできます。

```xml
<tag data-sly-BLOCK.IDENTIFIER="value"></tag>
```

この識別子は、様々な方法でブロック文で使用できます。次に例を示します。

```xml
<!--/* Example of statements that use the identifier to set a variable with their result: */-->
<div data-sly-use.navigation="MyNavigation">${navigation.title}</div>
<div data-sly-test.isEditMode="${wcmmode.edit}">${isEditMode}</div>
<div data-sly-list.child="${currentPage.listChildren}">${child.properties.jcr:title}</div>
<div data-sly-template.nav>Hello World</div>

<!--/* The attribute statement uses the identifier to know to which attribute it should apply it's value: */-->
<div data-sly-attribute.title="${properties.jcr:title}"></div> <!--/* This will create a title attribute */-->
```

最上位レベルの識別子では、大文字と小文字が区別されません（大文字と小文字が区別されないHTML属性を使用して設定できるため）が、すべてのプロパティで大文字と小文字が区別されます。

## 使用可能なブロックステートメント {#available-block-statements}

使用可能なブロック文は多数あります。 同じ要素で使用する場合、次の優先度リストはブロック文の評価方法を定義します。

1. `data-sly-template`
1. `data-sly-set`, `data-sly-test`, `data-sly-use`
1. `data-sly-call`
1. `data-sly-text`
1. `data-sly-element`, `data-sly-include`, `data-sly-resource`
1. `data-sly-unwrap`
1. `data-sly-list`、`data-sly-repeat`
1. `data-sly-attribute`

2つのブロック文の優先順位が同じ場合、評価順序は左から右です。

### use {#use}

`data-sly-use` ヘルパーオブジェクト（JavaScriptまたはJavaで定義）を初期化し、変数を介して公開します。

JavaScript オブジェクトを初期化します。ここで、ソースファイルはテンプレートと同じディレクトリに配置されます。ファイル名を使用する必要があります。

```xml
<div data-sly-use.nav="navigation.js">${nav.foo}</div>
```

Java クラスを初期化します。ここで、ソースファイルはテンプレートと同じディレクトリに配置されます。ファイル名ではなく、クラス名を使用する必要があります。

```xml
<div data-sly-use.nav="Navigation">${nav.foo}</div>
```

Java クラスを初期化します。ここで、このクラスは OSGi バンドルの一部としてインストールされます。完全修飾クラス名を使用する必要があります。

```xml
<div data-sly-use.nav="org.example.Navigation">${nav.foo}</div>
```

パラメーターは、オプションを使用して、初期化に渡すことができます。通常、この機能は、それ自体が `data-sly-template` ブロック内にある HTL コードでのみ使用する必要があります。

```xml
<div data-sly-use.nav="${'navigation.js' @parentPage=currentPage}">${nav.foo}</div>
```

別の HTL テンプレートを初期化します。これは、`data-sly-call` を使用して呼び出すことができます。

```xml
<div data-sly-use.nav="navTemplate.html" data-sly-call="${nav.foo}"></div>
```

>[!NOTE]
>
>Use-API について詳しくは、以下を参照してください。
>
>* [Java Use-API](use-api-java.md)
>* [JavaScript Use-API](use-api-javascript.md)


#### data-sly-use とリソース {#data-sly-use-with-resources}

これにより、でHTL内で直接リソースを取得でき `data-sly-use` るので、取得するためにコードを記述する必要はありません。

次に例を示します。

```xml
<div data-sly-use.product=“/etc/commerce/product/12345”>
  ${ product.title }
</div>
```

>[!TIP]
>
>「 [パスが必ずしも必須ではありません」も参照してください。](#path-not-required)

### unwrap {#unwrap}

`data-sly-unwrap` 生成されたマークアップからホスト要素を削除し、その内容を保持します。 これを使用すると、HTL プレゼンテーションロジックの一環として必要ではあるものの実際の出力では好ましくない要素を除外できます。

ただし、このステートメントは慎重に使用する必要があります。通常、HTL マークアップは、意図している出力マークアップに可能な限り近い形に保持することをお勧めします。つまり、HTL ブロックステートメントを追加するときは、新しい要素を導入せずに、既存の HTML に注釈を付けるだけにするよう努めてください。

例えば、

```xml
<p data-sly-use.nav="navigation.js">Hello World</p>
```

では、以下が生成されます。

```xml
<p>Hello World</p>
```

一方で、

```xml
<p data-sly-use.nav="navigation.js" data-sly-unwrap>Hello World</p>
```

では、以下が生成されます。

```xml
Hello World
```

要素を条件付きでアンラップすることも可能です。

```xml
<div class="popup" data-sly-unwrap="${isPopup}">content</div>
```

### set {#set}

`data-sly-set` は、事前定義済みの値を持つ新しい識別子を定義します。

```xml
<span data-sly-set.profile="${user.profile}">Hello, ${profile.firstName} ${profile.lastName}!</span>
<a class="profile-link" href="${profile.url}">Edit your profile</a>
```

### text {#text}

`data-sly-text` ホスト要素のコンテンツを指定したテキストに置き換えます。

例えば、

```xml
<p>${properties.jcr:description}</p>
```

は、以下と同等です。

```xml
<p data-sly-text="${properties.jcr:description}">Lorem ipsum</p>
```

いずれの場合も、`jcr:description` の値が段落テキストとして表示されます。2 つ目のメソッドのメリットは、HTML に目立たないアノテーションを付ける一方で、元の設計時の静的プレースホルダーコンテンツを保持できることです。

### attribute {#attribute}

`data-sly-attribute` 属性をホスト要素に追加します。

例えば、

```xml
<div title="${properties.jcr:title}"></div>
```

は、以下と同等です。

```xml
<div title="Lorem Ipsum" data-sly-attribute.title="${properties.jcr:title}"></div>
```

いずれの場合も、`title` 属性が `jcr:title` の値に設定されます。2 つ目のメソッドのメリットは、HTML に目立たないアノテーションを付ける一方で、元の設計時の静的プレースホルダーコンテンツを保持できることです。

属性は左から右へ解釈されます。属性の右端のインスタンス（リテラル、または `data-sly-attribute` で定義）が、同じ属性の、左側に定義されているインスタンス（リテラル、または `data-sly-attribute` で定義）より優先されます。

値の評価結果が空の文字列になる属性（`literal`、または `data-sly-attribute` で設定）は最終的なマークアップでは削除されます。このルールの 1 つの例外は、リテラルの空文字列に設定されているリテラル属性は保持されるということです。以下に例を挙げます。  

```xml
<div class="${''}" data-sly-attribute.id="${''}"></div>
```

によって以下が生成されます。

```xml
<div></div>
```

ただし、

```xml
<div class="" data-sly-attribute.id=""></div>
```

によって以下が生成されます。

```xml
<div class=""></div>
```

複数の属性を設定するには、マップオブジェクトのホールドに、属性とそれらの値に対応するキーと値のペアを渡します。例えば、以下のように仮定します。

```xml
attrMap = {
    title: "myTitle",
    class: "myClass",
    id: "myId"
}
```

すると、

```xml
<div data-sly-attribute="${attrMap}"></div>
```

によって以下が生成されます。

```xml
<div title="myTitle" class="myClass" id="myId"></div>
```

### element {#element}

`data-sly-element` ホスト要素の要素名を置き換えます。

例：

```xml
<h1 data-sly-element="${titleLevel}">text</h1>
```

この場合、`h1` が `titleLevel` の値に置き換えられます。

セキュリティ上の理由により、`data-sly-element` では以下の要素名のみを使用できます。

```xml
a abbr address article aside b bdi bdo blockquote br caption cite code col colgroup
data dd del dfn div dl dt em figcaption figure footer h1 h2 h3 h4 h5 h6 header i ins
kbd li main mark nav ol p pre q rp rt ruby s samp section small span strong sub
sup table tbody td tfoot th thead time tr u var wbr
```

他の要素を設定するには、XSS セキュリティをオフにする必要があります（`@context='unsafe'`）。

### test {#test}

`data-sly-test` 条件に応じて、ホスト要素とそのコンテンツを削除します。 値を `false` とすると要素が削除され、`true` とすると要素が保持されます。

例えば、`p` 要素とそのコンテンツは、`isShown` が `true` の場合にのみレンダリングされます。

```xml
<p data-sly-test="${isShown}">text</p>
```

テストの結果を任意の変数に割り当てて、それを後から使用することができます。これは、通常、「if else」ロジックの構築に使用します。明示的な else 要素がないからです。

```xml
<p data-sly-test.abc="${a || b || c}">is true</p>
<p data-sly-test="${!abc}">or not</p>
```

変数は、いったん設定すると、HTL ファイル内でグローバルスコープを持ちます。

次に、値の比較の例を示します。

```xml
<div data-sly-test="${properties.jcr:title == 'test'}">TEST</div>
<div data-sly-test="${properties.jcr:title != 'test'}">NOT TEST</div>

<div data-sly-test="${properties['jcr:title'].length > 3}">Title is longer than 3</div>
<div data-sly-test="${properties['jcr:title'].length >= 0}">Title is longer or equal to zero </div>

<div data-sly-test="${properties['jcr:title'].length > aemComponent.MAX_LENGTH}">
    Title is longer than the limit of ${aemComponent.MAX_LENGTH}
</div>
```

### repeat {#repeat}

With `data-sly-repeat` you can repeat an element multiple times based on the list that is specified.

```xml
<div data-sly-repeat="${currentPage.listChildren}">${item.name}</div>
```

This works the same way as `data-sly-list`, except that you do not need a container element.

次の例では、属性の *item* も参照できることを示しています。

```xml
<div data-sly-repeat="${currentPage.listChildren}" data-sly-attribute.class="${item.name}">${item.name}</div>
```

### list {#list}

`data-sly-list` 指定したオブジェクトの列挙可能な各プロパティに対してホスト要素の内容を繰り返します。

簡単なループは次のとおりです。

```xml
<dl data-sly-list="${currentPage.listChildren}">
    <dt>index: ${itemList.index}</dt>
    <dd>value: ${item.title}</dd>
</dl>
```

リストの範囲内で、以下のデフォルトの変数が使用可能です。

* `item`：現在の反復項目。
* `itemList`：以下のプロパティを含むオブジェクト。
* `index`：0 から始まるカウンター（`0..length-1`）。
* `count`：1 から始まるカウンター（`1..length`）。
* `first`：現在の項目が最初の項目である場合は `true`。
* `middle`: `true` 現在の項目が最初と最後の項目のどちらでもない場合。
* `last`: `true` 現在の品目が最後の品目の場合。
* `odd`: `true` が奇数 `index` の場合は、
* `even`: `true` が偶数 `index` の場合。

`data-sly-list` ステートメントで識別子を定義すると、`itemList` 変数と `item` 変数の名前を変更できます。`item` とな `<variable>` り、と `itemList` なる `<variable>List`。

```xml
<dl data-sly-list.child="${currentPage.listChildren}">
    <dt>index: ${childList.index}</dt>
    <dd>value: ${child.title}</dd>
</dl>
```

プロパティに動的にアクセスすることもできます。

```xml
<dl data-sly-list.child="${myObj}">
    <dt>key: ${child}</dt>
    <dd>value: ${myObj[child]}</dd>
</dl>
```

### resource {#resource}

`data-sly-resource` sling解像度とレンダリングプロセスを通して、指定されたリソースをレンダリングした結果が含まれます。

簡単なリソースの内容は次のとおりです。

```xml
<article data-sly-resource="path/to/resource"></article>
```

#### Path Not Required Walys Required {#path-not-required}

既にリソースを持っている場合、でのパスの使用 `data-sly-resource` は不要です。 既にリソースを持っている場合は、それを直接使用できます。

例えば、次のように指定できます。

```xml
<sly data-sly-resource="${resource.path @ decorationTagName='div'}"></sly>
```

しかし、以下も全く受け入れられません。

```xml
<sly data-sly-resource="${resource @ decorationTagName='div'}"></sly>
```

次の理由により、可能な場合は、リソースを直接使用することをお勧めします。

* 既にリソースを持っている場合は、パスを使用した再解決は不要な追加作業です。
* 既にリソースを持っている場合にパスを使用すると、Slingリソースがラップされるか、任意のパスで提供されずに合成される可能性があるので、予期しない結果が生じる可能性があります。

#### Options {#resource-options}

オプションを使用すると、多数のバリアントを追加できます。

リソースのパスを操作：

```xml
<article data-sly-resource="${ @ path='path/to/resource'}"></article>
<article data-sly-resource="${'resource' @ prependPath='my/path'}"></article>
<article data-sly-resource="${'my/path' @ appendPath='resource'}"></article>
```

セレクターを追加（または置換）：

```xml
<article data-sly-resource="${'path/to/resource' @ selectors='selector'}"></article>
```

複数のセレクターを追加、置換または削除：

```xml
<article data-sly-resource="${'path/to/resource' @ selectors=['s1', 's2']}"></article>
```

セレクターを既存に追加：

```xml
<article data-sly-resource="${'path/to/resource' @ addSelectors='selector'}"></article>
```

既存のセレクターから一部を削除：

```xml
<article data-sly-resource="${'path/to/resource' @ removeSelectors='selector1'}"></article>
```

すべてのセレクターを削除：

```xml
<article data-sly-resource="${'path/to/resource' @ removeSelectors}"></article>
```

リソースのリソースタイプを上書き：

```xml
<article data-sly-resource="${'path/to/resource' @ resourceType='my/resource/type'}"></article>
```

WCM モードを変更：

```xml
<article data-sly-resource="${'path/to/resource' @ wcmmode='disabled'}"></article>
```

デフォルトでは、AEM 装飾タグは無効です。decorationTagName オプションを使用すると復活させることができ、cssClassName を使用するとその要素にクラスを追加できます。

```xml
<article data-sly-resource="${'path/to/resource' @ decorationTagName='span',
cssClassName='className'}"></article>
```

>[!NOTE]
>
>AEM は、含まれている要素をラップする装飾タグを制御する明確でシンプルなロジックを提供します。For details see [Decoration Tag](https://docs.adobe.com/content/help/en/experience-manager-65/developing/components/decoration-tag.html) in the developing components documentation.

### include {#include}

`data-sly-include` 対応するテンプレートエンジンで処理されるときに、ホスト要素のコンテンツを、示されたHTMLテンプレートファイル（HTL、JSP、ESPなど）で生成されたマークアップで置き換えます。 含まれる側のファイルのレンダリングコンテキストには、現在の HTL コンテキスト（含む側のファイルのコンテキスト）は含まれません。その結果、HTL ファイルを含めるには、現在の `data-sly-use` を、含まれる側のファイルで繰り返す必要があります（このような場合、通常は、`data-sly-template` と `data-sly-call` を使用するほうが便利です）。

簡単な include は次のとおりです。

```xml
<section data-sly-include="path/to/template.html"></section>
```

JSP も同様の方法で含めることができます。

```xml
<section data-sly-include="path/to/template.jsp"></section>
```

オプションを使用すると、ファイルのパスを操作できます。

```xml
<section data-sly-include="${ @ file='path/to/template.html'}"></section>
<section data-sly-include="${'template.html' @ prependPath='my/path'}"></section>
<section data-sly-include="${'my/path' @ appendPath='template.html'}"></section>
```

WCM モードを変更することもできます。

```xml
<section data-sly-include="${'template.html' @ wcmmode='disabled'}"></section>
```

### requestAttributes {#request-attributes}

In the `data-sly-include` and `data-sly-resource` you can pass `requestAttributes` in order to use them in the receiving HTL-script.

これで、スクリプトまたはコンポーネントにパラメーターを適切に渡すことができます。

```xml
<sly data-sly-use.settings="com.adobe.examples.htl.core.hashmap.Settings"
        data-sly-include="${ 'productdetails.html' @ requestAttributes=settings.settings}" />
```

Settings クラスの Java コードでは、requestAttributes を渡すために Map が使用されています。

```xml
public class Settings extends WCMUsePojo {

  // used to pass is requestAttributes to data-sly-resource
  public Map<String, Object> settings = new HashMap<String, Object>();

  @Override
  public void activate() throws Exception {
    settings.put("layout", "flex");
  }
}
```

For example, via a Sling-Model, you can consume the value of the specified `requestAttributes`.

この例では、layout が use クラスから Map を使用して挿入されます。

```xml
@Model(adaptables=SlingHttpServletRequest.class)
public class ProductSettings {
  @Inject @Optional @Default(values="empty")
  public String layout;

}
```

### template と call {#template-call}

テンプレートブロックは、関数の呼び出しと同様に使用できます。 宣言の中でパラメータを取得し、呼び出す際に渡すことができます。 再帰も可能です。

`data-sly-template` テンプレートを定義します。 ホスト要素とそのコンテンツは、HTL では出力されません。

`data-sly-call` データスルキンテンプレートで定義されたテンプレートを呼び出します。 呼び出される側のテンプレートのコンテンツ（オプションでパラメーター化）によって、呼び出しのホスト要素のコンテンツが置き換えられます。

静的テンプレートを定義して、呼び出します。

```xml
<template data-sly-template.one>blah</template>
<div data-sly-call="${one}"></div>
```

動的テンプレートを定義して、パラメーターを指定して呼び出します。

```xml
<template data-sly-template.two="${ @ title}"><h1>${title}</h1></template>
<div data-sly-call="${two @ title=properties.jcr:title}"></div>
```

Templates located in a different file, can be initialized with `data-sly-use`. この場合、`data-sly-use` と `data-sly-call` も、同じ要素上に配置できます。

```xml
<div data-sly-use.lib="templateLib.html">
    <div data-sly-call="${lib.one}"></div>
    <div data-sly-call="${lib.two @ title=properties.jcr:title}"></div>
</div>
```

テンプレートの反復がサポートされています。

```xml
<template data-sly-template.nav="${ @ page}">
    <ul data-sly-list="${page.listChildren}">
        <li>
            <div class="title">${item.title}</div>
            <div data-sly-call="${nav @ page=item}" data-sly-unwrap></div>
        </li>
    </ul>
</template>
<div data-sly-call="${nav @ page=currentPage}" data-sly-unwrap></div>
```

## sly Element {#sly-element}

現在の要素を削除するために `<sly>` HTMLタグを使用でき、その子のみを表示できます。 この機能は、 `data-sly-unwrap` ブロック要素に似ています。

```xml
<!--/* This will display only the output of the 'header' resource, without the wrapping <sly> tag */-->
<sly data-sly-resource="./header"></sly>
```

有効なHTML 5タグではありませんが、 `<sly>` タグは、次を使用して最終出力に表示でき `data-sly-unwrap`ます。

```xml
<sly data-sly-unwrap="${false}"></sly> <!--/* outputs: <sly></sly> */-->
```

The goal of the `<sly>` element is to make it more obvious that the element is not output. If you want you can still use `data-sly-unwrap`.

As with `data-sly-unwrap`, try to minimize the use of this.
