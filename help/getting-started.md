---
title: HTL 使用の手引き
description: AEMでのHTMLに適した、推奨されるサーバー側テンプレートシステムである HTL について説明し、言語の主要概念とその基本構成を理解します。
exl-id: c95eb1b3-3b96-4727-8f4f-d54e7136a8f9
source-git-commit: 5ab1275c984135fe946f36905bbc979cf19edd80
workflow-type: tm+mt
source-wordcount: '2170'
ht-degree: 54%

---


# HTL 使用の手引き {#getting-started-with-htl}

HTMLテンプレート言語 (HTL) は、Adobe Experience ManagerでのHTML用に、サーバー側で推奨される推奨テンプレートシステムです。 すべてのHTMLサーバー側テンプレートシステムと同様に、HTL ファイルは、HTML自体、基本的なプレゼンテーションロジック、実行時に評価される変数を指定することで、ブラウザーに送信される出力を定義します。

このドキュメントでは、HTL の目的の概要と、言語の基本概念および構成の紹介を示します。

>[!TIP]
>
>このドキュメントでは、HTL の目的と、HTL の基本構造および概念の概要を説明します。 特定の構文に関するご質問は、 [HTL の仕様。](specification.md)

## HTL レイヤー {#layers}

AEMで使用される HTL は、複数のレイヤーで定義できます。

1. **[HTL の仕様](specification.md)** - HTL は、オープンソースの、プラットフォームに依存しない仕様で、誰でも自由に実装できます。
1. **[Sling HTL スクリプティングエンジン](specification.md)** - Sling プロジェクトは、AEMで使用される HTL の参照実装を作成しました。
1. **[AEM Extensions](specification.md)** - AEMは Sling HTL スクリプティングエンジン上に構築され、AEM専用の便利な機能を開発者に提供します。

この HTL ドキュメントでは、HTL を使用したAEMソリューションの開発に焦点を当てています。 そのため、3 つのレイヤすべてに接触し、必要に応じて外部リソースをリンクします。

## HTL の基本概念 {#fundamental-concepts-of-htl}

HTML テンプレート言語は式言語を使用して、レンダリングされたマークアップに個々のコンテンツを挿入し、HTML5 データ属性を使用し、マークアップのブロックに対してステートメント（条件や繰り返しなど）を定義します。HTL がコンパイルされて Java サーブレットになると、式と HTL データ属性は両方とも完全にサーバー側で評価され、生成される HTML には何も表示されません。

>[!TIP]
>
>このページで示す例の多くを実行するためには、[Read Eval Print Loop](https://github.com/adobe/aem-htl-repl) というライブ実行環境を使用できます。

### ブロックと式 {#blocks-and-expressions}

次に示す例は、そのまま `template.html` ファイルに含めることができます。

```xml
<h1 data-sly-test="${properties.jcr:title}">
    ${properties.jcr:title}
</h1>
```

2 種類の異なる構文が見られます。

* **ブロックステートメント**  — 条件付きで `<h1>` 要素、 `data-sly-test` HTML5 データ属性が使用されます。 HTL ではこのような属性が複数提供され、これらを使用してHTML要素に動作をアタッチできます。すべての属性にはというプレフィックスが付きます。 `data-sly`.
* **式言語** - HTL 式は `${` および `}` 文字。 実行時にこれらの式が評価され、出力 HTML ストリームに値がインジェクションされます。

詳しくは、 [HTL の仕様](specification.md) を参照してください。

### SLY 要素 {#the-sly-element}

HTL の中心となる概念は、既存のHTML要素を再利用できるようにブロックステートメントを定義することです。これにより、追加の区切り文字を挿入して、ステートメントの開始点と終了点を定義する必要がなくなります。 このような目立たないマークアップのアノテーションによって、静的な HTML が機能的な動的テンプレートに変換されます。これには、HTML コードの妥当性を損なわず、静的ファイルとしても適切に表示できるというメリットがあります。

ただし、ブロックステートメントを挿入する必要がある正確な場所に、既存の要素が存在しない可能性があります。このような場合、特別な `sly` 要素を追加します。要素は出力から自動的に削除されます。添付されたブロックステートメントを実行し、それに応じてコンテンツを表示します。

次の例では、

```xml
<sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</sly>
```

...は次のHTMLのような出力をしますが、両方が存在する場合にのみ `jcr:title` および `jcr:description` プロパティが定義され、どちらも空でない場合は次のようになります。

```xml
<h1>MY TITLE</h1>
<p>MY DESCRIPTION</p>
```

一つ心に留めておくべきことは、 `sly` 要素に注釈を付けることができない場合に、ブロックステートメントで注釈を付けることができます。 これは、 `sly` 要素は、静的要素を動的にする際に静的HTMLを変更しないように、言語が提供する値を遅らせます。

例えば、前の例が `div` 要素を選択し、 `sly` 要素は正しく記述されていません。

```xml
<div>
    <sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
        <h1>${properties.jcr:title}</h1>
        <p>${properties.jcr:description}</p>
    </sly>
</div>
```

そして `div` 要素に条件が注釈付けされている可能性があります。

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

### HTL コメント {#htl-comments}

次の例では、最初の行に HTL コメントを、2 番目の行にHTMLコメントを示しています。

```xml
<!--/* An HTL Comment */-->
<!-- An HTML Comment -->
```

HTL のコメントは、HTML コメントに JavaScript 形式の構文が加えられたものです。HTL コメント全体および HTL コメント内部の文字列は、プロセッサーによって完全に無視され、出力から削除されます。

ただし、標準 HTML コメントの内容はパススルーされ、コメント内部の式は評価されます。

HTML コメントに HTL コメントを含めることも、HTL コメントに HTML コメントを含めることもできません。

### 特別なコンテキスト {#special-contexts}

HTL を最大限に活用するためには、HTL が HTML 構文に基づいているという関係をよく理解することが重要です。

詳しくは、 [「コンテキスト」セクションを表示](https://github.com/adobe/htl-spec/blob/1.4/SPECIFICATION.md#121-display-context) HTL の仕様の詳細を参照してください。

### 要素名と属性名 {#element-and-attribute-names}

式は、HTMLテキストまたは属性値にのみ配置できますが、要素名または属性名の内部には配置できません。そうでない場合は、有効なHTMLではなくなります。 要素名を動的に設定するには、対象の要素について `data-sly-element` ステートメントを使用できます。また、属性名を動的に設定するには、`data-sly-attribute` ステートメントを使用できます。複数の属性を一度に設定することもできます。

```xml
<h1 data-sly-element="${myElementName}" data-sly-attribute="${myAttributeMap}">...</h1>
```

### ブロックステートメントなしのコンテキスト {#contexts-without-block-statements}

HTL はデータ属性を使用してブロックステートメントを定義するので、次のようなコンテキストにはそうしたブロックステートメントを定義できません。使用できるのは式のみです。

* HTML コメント
* script 要素
* style 要素

理由は、これらのコンテキストの内容がテキストであり HTML ではないことです。含まれる HTML 要素は単純な文字データとみなされます。本来の HTML 要素がないと、実行される `data-sly` 属性を指定できません。

これは、大きな制限のように聞こえる場合がありますが、必要な制限です。HTMLテンプレート言語を使用して、HTMLではない出力を生成しないでください。 この後の[ロジックにアクセスするための Use-API](#use-api-for-accessing-logic) では、追加ロジックをテンプレートから呼び出す方法を説明しています。これは、このようなコンテキストで複雑な出力を用意する必要があるときに使用できます。例えば、バックエンドスクリプトからフロントエンドスクリプトにデータを送る簡単な方法は、コンポーネントのロジックで JSON 文字列を生成することです。単純な HTL 式を使用してこれをデータ属性に含めることができます。

次の例に示すのは HTML コメントの動作ですが、script 要素または style 要素でも同じ動作が見られます。

```xml
<!--
    The title is: ${properties.jcr:title}
    <h1 data-sly-test="${properties.jcr:title}">${properties.jcr:title}</h1>
-->
```

これによって次のような HTML が生成されます。

```xml
<!--
    The title is: MY TITLE
    <h1 data-sly-test="MY TITLE">MY TITLE</h1>
-->
```

### 明示的なコンテキストが必要 {#explicit-contexts-required}

この後の[自動コンテキスト認識エスケープ](#automatic-context-aware-escaping)で説明するように、HTL の目的の 1 つは、コンテキスト認識エスケープをすべての式に自動的に適用して、クロスサイトスクリプティング（XSS）によって脆弱性がもたらされるリスクを低減することです。HTL は、HTML マークアップ内部に配置された式のコンテキストを自動的に検出できますが、インライン JavaScript または CSS の構文の分析は行いません。したがって、そのような式に適用する必要がある正確なコンテキストを明示的に指定するのは開発者の責任です。

正しいエスケープを適用しないと XSS の脆弱性が引き起こされるので、HTL は、script と style のコンテキストが宣言されていないとき、それらのコンテキストにあるすべての式の出力を削除します。

次の例では、script と style の内部に指定された式のコンテキストを設定する方法を示します。

```xml
<script> var trackingID = "${myTrackingID @ context='scriptString'}"; </script>
<style> a { font-family: "${myFont @ context='styleString'}"; } </style>
```

エスケープを制御する方法の詳細については、 [式言語の表示コンテキスト](https://github.com/adobe/htl-spec/blob/master/SPECIFICATION.md#121-display-context) の節を参照してください。

## HTL の一般的な機能 {#general-capabilities-of-htl}

ここでは、HTML テンプレート言語の一般的な機能を簡単に説明します。

### ロジックにアクセスするための Use-API {#use-api-for-accessing-logic}

HTML テンプレート言語（HTL）Java Use-API を使用すると、HTL ファイルから `data-sly-use` を介してカスタム Java クラスのヘルパーメソッドへのアクセスが可能になります。これにより、複雑なビジネスロジックをすべて Java コードでカプセル化し、HTL コードではマークアップの直接作成処理のみを行うことができます。

ドキュメントを参照 [HTL Java Use-API](java-use-api.md) を参照してください。

### 自動コンテキスト認識エスケープ {#automatic-context-aware-escaping}

次の例について考えてみます。

```xml
<p data-sly-use.logic="logic.js">
    <a href="${logic.link}" title="${logic.title}">
        ${logic.text}
    </a>
</p>
```

ほとんどのテンプレート言語では、この例によってクロスサイトスクリプティング（XSS）の脆弱性が生じる可能性があります。すべての変数が自動的に HTML エスケープされた場合でも、`href` 属性を特に URL エスケープする必要があるからです。この省略は、忘れやすく、自動的に検出するのが難しいので、最も一般的なエラーの 1 つです。

これに対処するために、HTML テンプレート言語では、指定されたコンテキストに応じて各変数を自動的にエスケープします。これは、HTL がHTMLの構文を理解しているため可能です。

次の `logic.js` ファイルがあるとします。

```javascript
use(function () {
    return {
        link:  "#my link's safe",
        title: "my title's safe",
        text:  "my text's safe"
    };
});
```

最初の例では次の出力が生成されます。

```xml
<p>
    <a href="#my%20link%27s%20safe" title="my title&#39;s safe">
        my text&#39;s safe
    </a>
</p>
```

2 つの属性が別の方法でエスケープされていることに注意してください。これは、HTL では `href` および `src` 属性は、URI コンテキストでエスケープする必要があります。 また、URI の先頭に「`javascript:`」が付いている場合は、コンテキストが明示的に別のものに変更されない限り、その属性全体が削除されます。

エスケープを制御する方法の詳細については、 [式言語の表示コンテキスト](https://github.com/adobe/htl-spec/blob/master/SPECIFICATION.md#121-display-context) の節を参照してください。

### 空の属性の自動削除 {#automatic-removal-of-empty-attributes}

次の例について考えてみます。

```xml
<p class="${properties.class}">some text</p>
```

`class` プロパティの値が空の場合、HTML テンプレート言語は `class` 属性全体を出力から自動的に削除します。

これも可能です。HTL がHTML構文を認識し、動的な値が空でない場合にのみ、条件付きでその値を含む属性を表示できるからです。 これにより、属性の周囲に条件ブロックを追加する必要がなくなり、非常に便利です。条件ブロックを追加すると、マークアップが無効になり読み取れなくなります。

また、式に含まれる変数の型も重要です。

* **String:**
   * **空ではない場合：**&#x200B;文字列を属性値として設定します。
   * **空の場合：**&#x200B;属性全体を削除します。

* **Number：**&#x200B;値を属性値として設定します。

* **Boolean:**
   * **true：**&#x200B;属性を値なしで表示します（ブール値の HTML 属性として）。
   * **false：**&#x200B;属性全体を削除します。

次に、ブール式を使用してブール値のHTML属性を制御する方法の例を示します。

```xml
<input type="checkbox" checked="${properties.isChecked}"/>
```

属性を設定する場合は、`data-sly-attribute` ステートメントも使用できます。

## HTL の一般的な使用方法 {#common-patterns-with-htl}

ここでは、一般的なシナリオをいくつか示し、HTML テンプレート言語を使用して解決する最適な方法を説明します。

### クライアントライブラリの読み込み {#loading-client-libraries}

HTL では、クライアントライブラリは AEM 提供のヘルパーテンプレートを介して読み込まれます。テンプレートには `data-sly-use` を使用してアクセスできます。このファイルには 3 つのテンプレートが含まれ、`data-sly-call` で呼び出すことができます。

* **`css`** - 参照されるクライアントライブラリの CSS ファイルのみを読み込みます。
* **`js`** - 参照されるクライアントライブラリの JavaScript ファイルのみを読み込みます。
* **`all`** - 参照されるクライアントライブラリのすべてのファイル（CSS と JavaScript 両方）を読み込みます。

各ヘルパーテンプレートには、必要なクライアントライブラリを参照するための `categories` オプションを指定できます。このオプションは、文字列値の配列、またはコンマ区切り値のリストを含む文字列です。

以下に 2 つの短い例を示します。

#### 複数のクライアントライブラリをすべて一度に読み込む {#loading-multiple-client-libraries-fully-at-once}

```xml
<sly data-sly-use.clientlib="/libs/granite/sightly/templates/clientlib.html"
     data-sly-call="${clientlib.all @ categories=['myCategory1', 'myCategory2']}"/>
```

#### ページの異なるセクションでクライアントライブラリを参照する {#referencing-a-client-library-in-different-sections-of-a-page}

```xml
<!doctype html>
<html data-sly-use.clientlib="/libs/granite/sightly/templates/clientlib.html">
    <head>
        <!-- HTML meta-data -->
        <sly data-sly-call="${clientlib.css @ categories='myCategory'}"/>
    </head>
    <body>
        <!-- page content -->
        <sly data-sly-call="${clientlib.js @ categories='myCategory'}"/>
    </body>
</html>
```

この例では、HTML `head` および `body` 要素は別のファイルに配置され、 `clientlib.html` テンプレートは、テンプレートが必要な各ファイルに読み込まれる必要があります。

内の template および call ステートメントに関する節 [HTL の仕様](specification.md) では、テンプレートの宣言と呼び出しの仕組みについて詳しく説明しています。

### データをクライアントに渡す {#passing-data-to-the-client}

データをクライアントに渡す最も優れた方法は、を使用することです（HTL を使用する場合もさらに便利です）。 `data` 属性。

次の例は、ロジック（Java でも記述可能）を使用して、クライアントに渡すオブジェクトを JSON にシリアライズする便利な方法です。この方法を簡単に JSON に配置できます。 `data` 属性：

```xml
<!--/* template.html file: */-->
<div data-sly-use.logic="logic.js" data-json="${logic.json}">...</div>
```

```javascript
/* logic.js file: */
use(function () {
    var myData = {
        str: "foo",
        arr: [1, 2, 3]
    };

    return {
        json: JSON.stringify(myData)
    };
});
```

その後、クライアント側の JavaScript がどのようにその属性にアクセスして再び JSON を解析するかは簡単に推測できます。例えば、次に示すように、対応する JavaScript をクライアントライブラリに配置します。

```javascript
var elements = document.querySelectorAll("[data-json]");
for (var i = 0; i < elements.length; i++) {
    var obj = JSON.parse(elements[i].dataset.json);
    //console.log(obj);
}
```

### クライアント側テンプレートの使用 {#working-with-client-side-templates}

[特別なコンテキストの制約解除](#lifting-limitations-of-special-contexts)の節で説明した方法が有効に使用できる特別なケースの 1 つは、`scrip` 要素に配置するクライアント側のテンプレート（例えば Handlebars）を記述する場合です。このケースでこの方法を安全に使用できる理由は、想定とは異なり `script` 要素に JavaScript は含まれず HTML 要素が含まれるからです。その仕組みを次の例で示します。

```xml
<!--/* template.html file: */-->
<script id="entry-section" type="text/template"
    data-sly-include="entry-section.html"></script>

<!--/* entry-section.html file: */-->
<div class="entry-section">
    <h2 data-sly-test="${properties.entrySectionTitle}">
        ${properties.entrySectionTitle}
    </h2>
    {{#each entry}}<h3><a href="{{link}}">{{title}}</a></h3>{{/each}}
</div>
```

上記のように、 `script` 要素には HTL ブロックステートメントを含めることができ、式に明示的なコンテキストを指定する必要はありません。Handlebars テンプレートのコンテンツは独自のファイルで分離されているからです。 また、この例に示すように、サーバー側で実行される HTL（`h2` 要素）を、Handlebars のようなクライアント側で実行されるテンプレート言語（`h3` 要素）と混在させることができます。

ただし、最新の方法では HTML `template` 要素が代わりに使用されます。この方法には、テンプレートの内容を個別のファイルに分離する必要がないというメリットがあります。

### 特別なコンテキストの制約解除 {#lifting-limitations-of-special-contexts}

特殊なケースで、script、style、コメントのコンテキストの制限を回避する必要がある場合には、それらの内容を別の HTL ファイルに分離することができます。 専用のファイルに分離された内容はすべて HTL によって通常の HTML フラグメントと解釈され、それらが含まれていた制約のあるコンテキストは無視されます。

この例は、このページの後半にある[クライアント側テンプレートの使用](#working-with-client-side-templates)を参照してください。

>[!CAUTION]
>
>この方法ではクロスサイトスクリプティング (XSS) の脆弱性が引き起こされる可能性があります。この脆弱性を使用する場合は、セキュリティ面を慎重に検討する必要があります。 通常は、このやり方を使用しなくても同じ処理を実装するさらに適切な方法があります。
