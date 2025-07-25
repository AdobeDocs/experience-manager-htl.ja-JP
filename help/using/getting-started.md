---
title: HTL 使用の手引き
description: AEM の HTML に適した、推奨されるサーバーサイドテンプレートシステムである HTL について学び、言語の主要概念とその基本構成を理解します。
exl-id: c95eb1b3-3b96-4727-8f4f-d54e7136a8f9
source-git-commit: addc69e4b4e56a9b1c5f91ce9af26fa2d326d981
workflow-type: ht
source-wordcount: '2045'
ht-degree: 100%

---


# HTL 使用の手引き {#getting-started-with-htl}

HTML テンプレート言語（HTL）は、Adobe Experience Manager での HTML 用に、サーバーサイドで優先かつ推奨されるテンプレートシステムです。 すべての HTML サーバーサイドテンプレートシステムと同じように、HTL ファイルでも、HTML 本体、基本的なプレゼンテーションロジックおよび実行時に評価される変数を指定することで、ブラウザーに送信される出力を定義します。

このドキュメントでは、HTL の目的の概要と、言語の基本的な概念および構造について説明します。

>[!TIP]
>
>このドキュメントでは、HTL の目的と、HTL の基本的な構造および概念の概要について説明します。特定の構文について疑問がある場合は、[HTL の仕様](specification.md)を参照してください。

## HTL レイヤー {#layers}

AEM では、複数のレイヤーによって HTL が定義されます。

1. **[HTL の仕様](specification.md)** - HTL は、プラットフォームに依存しないオープンソースの仕様で、誰でも自由に実装できます。
1. **[`Sling`HTL スクリプトエンジン](specification.md)** - `Sling` プロジェクトは、AEM で使用される HTL の参照実装を作成しました。
1. **[AEM 拡張機能](specification.md)** - AEM は `Sling` HTL スクリプトエンジン上に構築され、AEM に固有の便利な機能を開発者に提供します。

この HTL ドキュメントでは、HTL を使用した AEM ソリューションの開発に焦点を当てています。そのため、必要に応じて外部リソースをリンクし、3 つのレイヤーすべてに接触します。

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

2 種類の構文は次のように区別できます。

* **ブロックステートメント** - `<h1>` 要素を条件付きで表示する場合は、`data-sly-test` HTML5 データ属性を使用します。HTL ではこのような属性が複数提供され、これらを使用して HTML 要素に動作を関連付けることができます。すべての属性には `data-sly` という接頭辞が付きます。
* **式言語** - `${` と `}` 文字は HTL 式を区切ります。実行時にこれらの式が評価され、出力 HTML ストリームに値が挿入されます。

両方の構文について詳しくは、[HTL の仕様](specification.md)を参照してください。

### SLY 要素 {#the-sly-element}

HTL の核となる概念は、既存の HTML 要素を再利用してブロックステートメントを定義できるようにすることです。再利用することで、ステートメントの開始位置と終了位置を定義する追加の区切り文字を挿入する必要がなくなります。マークアップに注釈を付けると、HTML の有効性を損なうことなく静的 HTML が動的テンプレートに変換され、静的ファイルとしても適切に表示されます。

ただし、ブロックステートメントを挿入する必要がある正確な場所に、既存の要素が存在しないことがあります。このような場合は、特別な `sly` 要素を挿入できます。この要素は、添付されたブロックステートメントを実行し、それに応じてその内容を表示しながら、出力から自動的に削除されます。

以下の例の内容は次のとおりです。

```xml
<sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</sly>
```

次のような HTML が出力されるのは、`jcr:title` プロパティと `jcr:description` プロパティの両方が定義され、いずれも空でない場合のみです。

```xml
<h1>MY TITLE</h1>
<p>MY DESCRIPTION</p>
```

`sly` 要素を使用するのは、既存の要素にブロックステートメントの注釈を付けられなかった場合にのみにしてください。これは、静的 HTML を動的にする際に、その言語で提供される値が `sly` 要素によって抑制され、変更できなくなるためです。

例えば、前の例が既に `div` 要素にラップされている場合、追加された `sly` 要素は不正使用となります。

```xml
<div>
    <sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
        <h1>${properties.jcr:title}</h1>
        <p>${properties.jcr:description}</p>
    </sly>
</div>
```

`div` 要素には、次の条件の注釈が付いている場合があります。

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

### HTL コメント {#htl-comments}

次の例では、1 行目に HTL コメント、2 行目に HTML コメントを表示しています。

```xml
<!--/* An HTL Comment */-->
<!-- An HTML Comment -->
```

HTL のコメントは、HTML コメントに JavaScript 形式の構文が加えられたものです。プロセッサーは、HTL コメント全体とその中の内容を完全に無視し、出力から削除します。

ただし、標準 HTML コメントの内容はパススルーされ、コメント内部の式は評価されます。

HTML コメントに HTL コメントを含めることも、HTL コメントに HTML コメントを含めることもできません。

### 特別なコンテキスト {#special-contexts}

HTL を最大限に活用するためには、HTL が HTML 構文に基づいているという関係をよく理解することが重要です。

詳しくは、HTL の仕様の[コンテキストセクションを表示](https://github.com/adobe/htl-spec/blob/1.4/SPECIFICATION.md#121-display-context)を参照してください。

### 要素名と属性名 {#element-and-attribute-names}

式は HTML テキストまたは属性値でのみ指定できます。要素名や属性名の内部には指定できません。指定した場合、有効な HTML として認識されません。要素名を動的に設定するには、対象の要素について `data-sly-element` ステートメントを使用できます。また、属性名を動的に設定するには、`data-sly-attribute` ステートメントを使用できます。複数の属性を一度に設定することもできます。

```xml
<h1 data-sly-element="${myElementName}" data-sly-attribute="${myAttributeMap}">...</h1>
```

### ブロックステートメントなしのコンテキスト {#contexts-without-block-statements}

HTL はデータ属性を使用してブロックステートメントを定義するので、次のようなコンテキストにはそうしたブロックステートメントを定義できません。使用できるのは式のみです。

* HTML コメント
* script 要素
* style 要素

理由は、これらのコンテキストの内容がテキストであり HTML ではないことです。含まれる HTML 要素は単純な文字データとみなされます。本来の HTML 要素がないと、実行される `data-sly` 属性を指定できません。

このアプローチは、大きな制約と思われるかもしれません。ただし、HTML テンプレート言語では有効な HTML 出力のみを生成する必要があるので、これをお勧めします。この後の[ロジックにアクセスするための Use-API](#use-api-for-accessing-logic) では、追加ロジックをテンプレートから呼び出す方法を説明しています。これは、このようなコンテキストで複雑な出力を用意する必要があるときに使用できます。バックエンドからフロントエンドスクリプトにデータを送信するには、コンポーネントのロジックを含む JSON 文字列を生成し、シンプルな HTL 式を使用してデータ属性に配置します。

次の例に示すのは HTML コメントの動作ですが、script 要素または style 要素でも同じ動作が見られます。

```xml
<!--
    The title is: ${properties.jcr:title}
    <h1 data-sly-test="${properties.jcr:title}">${properties.jcr:title}</h1>
-->
```

出力では、次のような HTML が生成されます。

```xml
<!--
    The title is: MY TITLE
    <h1 data-sly-test="MY TITLE">MY TITLE</h1>
-->
```

### 明示的なコンテキストが必要 {#explicit-contexts-required}

この後の[自動コンテキスト認識エスケープ](#automatic-context-aware-escaping)で説明するように、HTL の目的の 1 つは、コンテキスト認識エスケープをすべての式に自動的に適用して、クロスサイトスクリプティング（XSS）によって脆弱性がもたらされるリスクを低減することです。HTL は HTML マークアップ内の式のコンテキストを検出しますが、インライン JavaScript または CSS の分析は行わないので、開発者はこれらの式の正確なコンテキストを指定する必要があります。

正しいエスケープを適用しないと XSS の脆弱性が引き起こされるので、HTL は、script と style のコンテキストが宣言されていないとき、それらのコンテキストにあるすべての式の出力を削除します。

次の例では、script と style の内部に指定された式のコンテキストを設定する方法を示します。

```xml
<script> var trackingID = "${myTrackingID @ context='scriptString'}"; </script>
<style> a { font-family: "${myFont @ context='styleString'}"; } </style>
```

エスケープの制御方法について詳しくは、HTL の仕様の[式言語の表示コンテキスト](https://github.com/adobe/htl-spec/blob/master/SPECIFICATION.md#121-display-context)の節を参照してください。

## HTL の一般的な機能 {#general-capabilities-of-htl}

ここでは、HTML テンプレート言語の一般的な機能を簡単に説明します。

### ロジックにアクセスするための Use-API {#use-api-for-accessing-logic}

HTML テンプレート言語（HTL）Java Use-API を使用すると、HTL ファイルから `data-sly-use` を介してカスタム Java クラスのヘルパーメソッドへのアクセスが可能になります。このプロセスにより、複雑なビジネスロジックをすべて Java コードでカプセル化し、HTL コードではマークアップの直接作成処理のみを行うことができます。

詳しくは、[HTL Java Use-API](java-use-api.md) を参照してください。

### 自動コンテキスト認識エスケープ {#automatic-context-aware-escaping}

次の例について考えます。

```xml
<p data-sly-use.logic="logic.js">
    <a href="${logic.link}" title="${logic.title}">
        ${logic.text}
    </a>
</p>
```

ほとんどのテンプレート言語では、この例によってクロスサイトスクリプティング（XSS）の脆弱性が生じる可能性があります。すべての変数が自動的に HTML エスケープされた場合でも、`href` 属性を特に URL エスケープする必要があるからです。これは忘れやすく、自動的に検出するのも難しいので、最も一般的なエラーの 1 つです。

対処するために、HTML テンプレート言語では、指定されたコンテキストに応じて各変数を自動的にエスケープします。このアプローチが可能なのは、HTL が HTML の構文を認識しているからです。

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

最初の例では、次の出力が生成されます。

```xml
<p>
    <a href="#my%20link%27s%20safe" title="my title&#39;s safe">
        my text&#39;s safe
    </a>
</p>
```

HTL は `href` 属性と `src` 属性を URI コンテキストでエスケープする必要があることを認識しているため、2 つの属性のエスケープ方法が異なることに注意してください。また、URI の先頭に「`javascript:`」が付いている場合は、コンテキストが明示的に別のものに変更されない限り、その属性全体が削除されます。

エスケープを制御する方法について詳しくは、HTL の仕様の[式言語の表示コンテキスト](https://github.com/adobe/htl-spec/blob/master/SPECIFICATION.md#121-display-context)の節を参照してください。

### 空の属性の自動削除 {#automatic-removal-of-empty-attributes}

次の例について考えます。

```xml
<p class="${properties.class}">some text</p>
```

`class` プロパティの値が空の場合、HTML テンプレート言語は `class` 属性全体を出力から自動的に削除します。

このプロセスが可能なのも、HTL が HTML 構文を認識し、動的な値を含む属性を値が空でない場合のみ条件によって表示できるからです。これは、マークアップを無効にして判読不能にする属性の周囲に条件ブロックを追加せずに済むので、非常に便利です。

また、式に含まれる変数の型も重要です。

* **String:**
   * **空ではない場合：**&#x200B;文字列を属性値として設定します。
   * **空の場合：**&#x200B;属性全体を削除します。

* **Number：**&#x200B;値を属性値として設定します。

* **Boolean:**
   * **true：**&#x200B;属性を値なしで表示します（ブール値の HTML 属性として）。
   * **false：**&#x200B;属性全体を削除します。

次の例は、ブール式を使用してブール値の HTML 属性を制御する方法を示します。

```xml
<input type="checkbox" checked="${properties.isChecked}"/>
```

属性を設定する場合は、`data-sly-attribute` ステートメントも使用できます。

## HTL の一般的な使用方法 {#common-patterns-with-htl}

この節では、一般的なシナリオをいくつか紹介します。ここでは、HTMLテンプレート言語を使用して、これらのシナリオを解決する最適な方法について説明します。

### クライアントライブラリの読み込み {#loading-client-libraries}

HTL では、クライアントライブラリは AEM 提供のヘルパーテンプレートを介して読み込まれます。テンプレートには `data-sly-use` を使用してアクセスできます。このファイルには 3 つのテンプレートが含まれ、`data-sly-call` で呼び出すことができます。

* **`css`** - 参照されるクライアントライブラリの CSS ファイルのみを読み込みます。
* **`js`** - 参照されるクライアントライブラリの JavaScript ファイルのみを読み込みます。
* **`all`** - 参照されるクライアントライブラリのすべてのファイル（CSS と JavaScript 両方）を読み込みます。

各ヘルパーテンプレートには、必要なクライアントライブラリを参照するための `categories` オプションを指定できます。このオプションには、文字列値の配列またはコンマ区切り値のリストを含む文字列を指定できます。

次に 2 つの短い例を示します。

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

この例では、HTML の `head` 要素と `body` 要素が別のファイルにある場合、必要な各ファイルに `clientlib.html` テンプレートを読み込む必要があります。

[HTL の仕様](specification.md)の template および call ステートメントに関する節では、テンプレートの宣言と呼び出しの仕組みについて詳しく記載しています。

### データをクライアントに渡す {#passing-data-to-the-client}

一般的にデータをクライアントに渡すための最適かつ洗練された方法は、HTL では特に、`data` 属性を使用することです。

次の例に、オブジェクトを JSON（Java でも可能）にシリアル化してクライアントに渡す方法を示します。次に、`data` 属性に簡単に配置できます。

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

その後、クライアントサイドの JavaScript がどのようにその属性にアクセスし、再び JSON を解析するかは容易に推測できます。このアプローチは、対応する JavaScript をクライアントライブラリに配置することになります。例えば、次のようになります。

```javascript
var elements = document.querySelectorAll("[data-json]");
for (var i = 0; i < elements.length; i++) {
    var obj = JSON.parse(elements[i].dataset.json);
    //console.log(obj);
}
```

### クライアントサイドテンプレートの使用 {#working-with-client-side-templates}

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

Handlebars テンプレートのコンテンツは独自のファイルに分離されているので、`script` 要素のマークアップには明示的なコンテキストのない HTL ブロックステートメントを含めることができます。また、この例に示すように、サーバー側で実行される HTL（`h2` 要素）を、Handlebars のようなクライアント側で実行されるテンプレート言語（`h3` 要素）と混在させることができます。

ただし、最新の方法では HTML `template` 要素が代わりに使用されます。この方法には、テンプレートの内容を個別のファイルに分離する必要がないというメリットがあります。

### 特別なコンテキストの制約解除 {#lifting-limitations-of-special-contexts}

特殊なケースですが、script、style、コメントのコンテキストの制約を回避する必要がある場合には、それらの内容を別の HTL ファイルに分離することができます。HTL は、独自のファイル内のすべてを標準の HTML フラグメントとして解釈し、インクルード元の制限的なコンテキストを無視します。

この例は、このページの後半にある[クライアントサイドテンプレートの使用](#working-with-client-side-templates)を参照してください。

>[!CAUTION]
>
>この方法ではクロスサイトスクリプティング（XSS）の脆弱性がもたらされることがあります。このアプローチを使用する場合は、セキュリティの面を慎重に検討する必要があります。通常は、このやり方を使用しなくても同じ処理を実装できる、より適切な方法があります。
