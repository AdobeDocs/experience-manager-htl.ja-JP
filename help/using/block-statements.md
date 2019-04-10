---
title: HTL ブロックステートメント
seo-title: HTL ブロックステートメント
description: HTML テンプレート言語（HTL）ブロックステートメントは、既存の HTML に直接追加されるカスタム data 属性です。
seo-description: HTML テンプレート言語（HTL）ブロックステートメントは、既存の HTML に直接追加されるカスタム data 属性です。
uuid: 0624fb6e-6989-431b-aabc-1138325393f1
contentOwner: ユーザーは、
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: リファレンス
discoiquuid: 58aa6ea8-1d45-4f6f-a77e-4819f593a19d
mwpw-migration-script-version: 2017-10-12T21 46 58.665-0400
translation-type: ht
source-git-commit: 7a94b0b010461b29d2b74c9c717e3b218d0ca5a8

---


# HTL ブロックステートメント {#htl-block-statements}

HTML テンプレート言語（HTL）ブロックステートメントは、既存の HTML に直接追加されるカスタム `data` 属性です。これを使用すると、プロトタイプの静的 HTML ページに目立たないアノテーションを簡単に付け、HTML コードの妥当性を損なうことなく、機能的な動的テンプレートに変換できます。

## sly 要素 {#sly-element}

**&lt;sly&gt; 要素**は、生成される HTML には表示されませんが、data-sly-unwrap の代わりに使用できます。&lt;sly&gt; 要素の目的は、その要素が出力されていないことをより明確にすることです。必要に応じて、data-sly-unwrap を引き続き使用することもできます。

```xml
<sly data-sly-test.varone="${properties.yourProp}"/>
```

data-sly-unwrap と同様、この要素の使用は最小限に抑えるようにしてください。

## use {#use}

**`data-sly-use`**：ヘルパーオブジェクト（JavaScript または Java で定義）を初期化し、変数を使用して公開します。

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

パラメーターは、オプションを使用して、初期化に渡すことができます。**通常、この機能は、それ自体が `data-sly-template` ブロック内にある HTL コードでのみ使用する必要があります。

```xml
<div data-sly-use.nav="${'navigation.js' @parentPage=currentPage}">${nav.foo}</div>
```

別の HTL テンプレートを初期化します。これは、**`data-sly-call`** を使用して呼び出すことができます。

```xml
<div data-sly-use.nav="navTemplate.html" data-sly-call="${nav.foo}"></div>
```

>[!NOTE]
>
>Use-API について詳しくは、以下を参照してください。
>
>* [Java Use-API](use-api-java.md)
>* [JavaScript Use-API](use-api-javascript.md)
>



## unwrap {#unwrap}

**`data-sly-unwrap`**：生成されるマークアップから、コンテンツを保持したまま、ホスト要素を削除します。これを使用すると、HTL プレゼンテーションロジックの一環として必要ではあるものの実際の出力では好ましくない要素を除外できます。

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

## text {#text}

**`data-sly-text`**：ホスト要素のコンテンツを指定したテキストに置き換えます。

例えば、

```xml
<p>${properties.jcr:description}</p>
```

は、以下と同等です。

```xml
<p data-sly-text="${properties.jcr:description}">Lorem ipsum</p>
```

いずれの場合も、**`jcr:description`** の値が段落テキストとして表示されます。2 つ目のメソッドのメリットは、HTML に目立たないアノテーションを付ける一方で、元の設計時の静的プレースホルダーコンテンツを保持できることです。

## attribute {#attribute}

**data-sly-attribute**：ホスト要素に属性を追加します。

例えば、

```xml
<div title="${properties.jcr:title}"></div>
```

は、以下と同等です。

```xml
<div title="Lorem Ipsum" data-sly-attribute.title="${properties.jcr:title}"></div>
```

いずれの場合も、`title` 属性が **`jcr:title`** の値に設定されます。2 つ目のメソッドのメリットは、HTML に目立たないアノテーションを付ける一方で、元の設計時の静的プレースホルダーコンテンツを保持できることです。

属性は左から右へ解釈されます。属性の右端のインスタンス（リテラル、または **`data-sly-attribute`** で定義）が、同じ属性の、左側に定義されているインスタンス（リテラル、または **`data-sly-attribute`** で定義）より優先されます。

値の評価結果が空の文字列になる属性（**`literal`**、または **`data-sly-attribute`** で設定）は最終的なマークアップでは削除されます。**このルールの 1 つの例外は、リテラルの空文字列に設定されているリテラル属性は保持されるということです。****以下に例を挙げます。  

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

## element {#element}

**`data-sly-element`**：ホスト要素の要素名を置き換えます。

以下に例を挙げます。  

```xml
<h1 data-sly-element="${titleLevel}">text</h1>
```

この場合、**`h1`** が **`titleLevel`** の値に置き換えられます。

セキュリティ上の理由により、`data-sly-element` では以下の要素名のみを使用できます。

```xml
a abbr address article aside b bdi bdo blockquote br caption cite code col colgroup
data dd del dfn div dl dt em figcaption figure footer h1 h2 h3 h4 h5 h6 header i ins
kbd li main mark nav ol p pre q rp rt ruby s samp section small span strong sub 
sup table tbody td tfoot th thead time tr u var wbr
```

他の要素を設定するには、XSS セキュリティをオフにする必要があります（`@context='unsafe'`）。

## test {#test}

**`data-sly-test`：**ホスト要素とそのコンテンツを条件付きで削除します。値を `false` とすると要素が削除され、`true` とすると要素が保持されます。

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

## repeat {#repeat}

data-sly-repeat を使用すると、指定されているリストに基づいて、要素が複数回繰り返されます。**

```xml
<div data-sly-repeat="${currentPage.listChildren}">${item.name}</div>
```

これは data-sly-list と同様に機能しますが、コンテナ要素は不要です。

次の例では、属性の *item* も参照できることを示しています。

```xml
<div data-sly-repeat="${currentPage.listChildren}" data-sly-attribute.class="${item.name}">${item.name}</div>
```

## list {#list}

**`data-sly-list`**：指定されているオブジェクト内の可算プロパティごとに、ホスト要素のコンテンツを繰り返します。

簡単なループは次のとおりです。

```xml
<dl data-sly-list="${currentPage.listChildren}">
    <dt>index: ${itemList.index}</dt>
    <dd>value: ${item.title}</dd>
</dl>
```

リストの範囲内で、以下のデフォルトの変数が使用可能です。

**`item`**：現在の反復項目。

**`itemList`**：以下のプロパティを含むオブジェクト。

**`index`**：0 から始まるカウンター（`0..length-1`）。

**`count`**：1 から始まるカウンター（`1..length`）。

`first`：現在の項目が最初の項目である場合は `true`。

**`middle`**：現在の項目が最初の項目でも最後の項目でもない場合は `true`。

**`last`**：現在の項目が最後の項目である場合は `true`。

**`odd`**：`index` が奇数の場合は `true`。

**`even`**：`index` が偶数の場合は `true`。

`data-sly-list` ステートメントで識別子を定義すると、**`itemList`** 変数と `item` 変数の名前を変更できます。**`item`** は *** `<variable>`*** になり、**`itemList`** は **`*<variable>*List`** になります。

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

## resource {#resource}

**`data-sly-resource`**：Sling の解決およびレンダリングプロセスによって示されたリソースのレンダリング結果を含めます。

簡単なリソースの内容は次のとおりです。

```xml
<article data-sly-resource="path/to/resource"></article>
```

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
>AEM は、含まれている要素をラップする装飾タグを制御する明確でシンプルなロジックを提供します。詳しくは、コンポーネント開発に関するドキュメントの[装飾タグ](https://helpx.adobe.com/jp/experience-manager/6-4/sites/developing/using/decoration-tag.html)を参照してください。

## include {#include}

**`data-sly-include`**：指示された HTML テンプレートファイル（HTL、JSP、ESP など）が対応するテンプレートエンジンによって処理されるときに、ホスト要素のコンテンツを、テンプレートファイルで生成されたマークアップに置き換えます。含まれる側のファイル**のレンダリングコンテキストには、現在の HTL コンテキスト（含む側のファイル**のコンテキスト）は含まれません。その結果、HTL ファイルを含めるには、現在の **`data-sly-use`** を、含まれる側のファイルで繰り返す必要があります（このような場合、通常は、**`data-sly-template`** と `data-sly-call` を使用するほうが便利です）。

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

## template と call {#template-call}

`data-sly-template`：テンプレートを定義します。ホスト要素とそのコンテンツは、HTL では出力されません。

`data-sly-call`：data-sly-template で定義したテンプレートを呼び出します。呼び出される側のテンプレートのコンテンツ（オプションでパラメーター化）によって、呼び出しのホスト要素のコンテンツが置き換えられます。

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

別のファイルに配置されているテンプレートは、`data-sly-use` を使用して初期化できます。この場合、`data-sly-use` と `data-sly-call` も、同じ要素上に配置できます。

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

## i18n オブジェクトと locale オブジェクト {#i-n-and-locale-objects}

i18n および HTL を使用していると、カスタムロケールオブジェクトも渡すことができるようになります。

```xml
${'Hello World' @ i18n, locale=request.locale}
```

## URL 操作 {#url-manipulation}

一連の URL 操作を使用できます。

その使用方法については、以下の例を参照してください。

html 拡張子をパスに追加します。

```xml
<a href="${item.path @ extension = 'html'}">${item.name}</a>
```

html 拡張子とセレクターをパスに追加します。

```xml
<a href="${item.path @ extension = 'html', selectors='products'}">${item.name}</a>
```

html 拡張子とフラグメント（#value）をパスに追加します。

```xml
<a href="${item.path @ extension = 'html', fragment=item.name}">${item.name}</a>
```

## AEM 6.3 でサポートされている HTL 機能 {#htl-features-supported-in-aem}

次の新しい HTL 機能は、Adobe Experience Manager（AEM）6.3 でサポートされています。

### 数値／日付の書式設定 {#number-date-formatting}

AEM 6.3 では、カスタムコードを記述せずに、数字と日付のネイティブな形式をサポートしています。また、タイムゾーンとロケールもサポートしています。

書式を最初に指定してから、書式設定が必要な値を指定する例を次に示します。

```xml
<h2>${ 'dd-MMMM-yyyy hh:mm:ss' @
           format=currentPage.lastModified,
           timezone='PST',
           locale='fr'}</h2>

<h2>${ '#.00' @ format=300}</h2>
```

>[!NOTE]
>
>使用できる形式について詳しくは、[HTL の仕様](https://github.com/Adobe-Marketing-Cloud/htl-spec/blob/master/SPECIFICATION.md)を参照してください。

### data-sly-use とリソース {#data-sly-use-with-resources}

これを使用すると、data-sly-use を使用して HTL 内で直接リソースを取得することができ、リソースを取得するためのコードを記述する必要がありません。

次に例を示します。

```xml
<div data-sly-use.product=“/etc/commerce/product/12345”>
  ${ product.title }
</div>
```

### requestAttributes {#request-attributes}

*data-sly-include* と *data-sly-resource* には、受信側の HTL スクリプトで使用するために *requestAttributes* を渡すことができるようになりました。

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

例えば、Sling モデルを通じて、指定された requestAttributes の値を使用できます。

この例では、*layout* が use クラスから Map を使用して挿入されます。

```xml
@Model(adaptables=SlingHttpServletRequest.class)
public class ProductSettings {
  @Inject @Optional @Default(values="empty")
  public String layout;

}
```

### コード「@extension」の修正 {#fix-for-extension}

コード「@extension」は、AEM 6.3 のすべてのシナリオで動作します。その後、*www.adobe.com.html* のような結果が得られたら、拡張子を追加するかどうかも確認します。

```xml
${ link @ extension = 'html' }
```
