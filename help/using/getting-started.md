---
title: HTL 使用の手引き
seo-title: HTL 使用の手引き
description: AEM でサポートされる HTL は、JSP の代わりに、AEM における HTML 用のサーバー側テンプレートシステムとして優先かつ推奨されています。
seo-description: Adobe Experience Manager でサポートされる HTML テンプレート言語（HTL）は、JSP の代わりに、AEM における HTML 用のサーバー側テンプレートシステムとして優先かつ推奨されています。
uuid: 4a7d6748-8cdf-4280-a85d-6c5319abf487
content-type: リファレンス
topic-tags: 概要
discoiquuid: 3bf2ca75-0d68-489d-bd1c-1d4fd730c61a
mwpw-migration-script-version: 2017-10-12T21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 1e3df6159b48da27460f3ad95c22c13d025b1a72

---


# HTL 使用の手引き {#getting-started-with-htl}

Adobe Experience Manager（AEM）でサポートされる HTML テンプレート言語（HTL）は、JSP（JavaServer Pages）の代わりに、AEM における HTML 用のサーバー側テンプレートシステムとして優先かつ推奨されています。

>[!NOTE]
>
>このページで示す例の多くを実行するためには、[Read Eval Print Loop](https://github.com/Adobe-Marketing-Cloud/aem-htl-repl) というライブ実行環境を使用できます。
>
>AEM コミュニティは、HTL の使用に関連した一連の[記事、ビデオ、ウェビナー](related-community-articles.md)を作成しています。

## JSP と HTL の対比 {#htl-over-jsp}

HTML テンプレート言語には JSP と比較して多数のメリットがあるので、新しい AEM プロジェクトでは HTML テンプレート言語の使用をお勧めします。ただし、既存のプロジェクトでは、今後数年、既存の JSP を維持するよりも HTL へ移行する方が作業が減ると予想される場合に限り、移行の意味があります。

しかし、必ずしも HTL への移行だけが選択肢ではありません。HTL で記述されたコンポーネントには、JSP や ESP で記述されたコンポーネントと互換性があるからです。つまり、既存のプロジェクトで、新しいコンポーネントに対して問題なく HTL を使用しながら、既存のコンポーネントに対しては JSP を維持することができます。

同じコンポーネント内でも、HTL ファイルを JSP および ESP と共に使用できます。例えば、**行 1** では JSP ファイルで HTL ファイルがインクルードされ、**行 2**&#x200B;では HTL ファイルで JSP ファイルがインクルードされています。

```xml
<cq:include script="template.html"/>
<sly data-sly-include="template.jsp"/>
```

### よくある質問{#frequently-asked-questions}

HTML テンプレート言語について説明する前に、JSP と HTL の比較に関連する重要な質問に回答します。

**HTL には JSP にない制限事項がありますか。** HTL には JSP と比べた場合に制限事項はありません。つまり、JSP で実行できることは HTL でも実行できます。ただし、HTL はいくつかの面で JSP に比べて厳密に設計されています。つまり、場合によっては、1 つの JSP ファイルですべて実行できる処理を、HTL で実行するためには Java クラスまたは JavaScript ファイルに分割する必要があります。これは、ロジックとマークアップの問題点を適切に分離できるように一般的にも必要とされています。

**HTL では JSP タグライブラリはサポートされますか。**
サポートされませんが、[クライアントライブラリの読み込み](getting-started.md#loading-client-libraries)で説明するように、[template および call](block-statements.md#template-call) ステートメントによって同様のパターンが提供されます。

**HTL 機能を AEM プロジェクトで拡張できますか。**
**サポートされませんが、[クライアントライブラリの読み込み](getting-started.md#loading-client-libraries)で説明するように、[template および call](block-statements.md#template-call) ステートメントによって同様のパターンが提供されます。いいえ、できません。HTL は、ロジック（[Use-API](getting-started.md#use-api-for-accessing-logic)）とマークアップ（[template および call](block-statements.md#template-call) ステートメント）の再利用のために強力な拡張メカニズムを備えています。これを使用して、プロジェクトのコードをモジュール化することができます。

**JSP と比べて HTL の最大のメリットは何ですか。**
最大のメリットはセキュリティとプロジェクトの効率性です。これらについては、[概要](overview.md)で詳しく説明します。

**JSP はいずれ使用されなくなりますか。**&#x200B;現時点ではそうした予定はありません。

## HTL の基本概念 {#fundamental-concepts-of-htl}

HTML テンプレート言語は式言語を使用して、レンダリングされたマークアップに個々のコンテンツを挿入し、HTML5 データ属性を使用し、マークアップのブロックに対してステートメント（条件や繰り返しなど）を定義します。HTL がコンパイルされて Java サーブレットになると、式と HTL データ属性は両方とも完全にサーバー側で評価され、生成される HTML には何も表示されません。

### ブロックと式 {#blocks-and-expressions}

次に示す例は、そのまま **`template.html`** ファイルに含めることができます。

```xml
<h1 data-sly-test="${properties.jcr:title}">
    ${properties.jcr:title}
</h1>
```

2 種類の異なった構文が見られます。

* **[ブロック文](block-statements.md)**&lt;h1&gt;要素を条件付きで表示す **るには** 、 `[data-sly-test](block-statements.md#test)` HTML5データ属性が使用されます。 HTL ではこのような属性が複数提供され、これらを使用して HTML 要素に動作を関連付けることができます。すべての属性には `data-sly` というプレフィックスがついています。

* **[式言語](expression-language.md)**
HTL の式は、`${` と `}` で区切られます。実行時にこれらの式が評価され、出力 HTML ストリームに値がインジェクションされます。

上記の 2 つのリンクページでは、構文で使用できる機能について詳しく説明しています。

### SLY 要素 {#the-sly-element}

>[!NOTE]
>
>SLY 要素は AEM 6.1 すなわち HTL 1.1 で導入されました。
>
>それ以前には、代わりに `[data-sly-unwrap](block-statements.md)` 属性を使用する必要がありました。

HTL の中心となる概念は、既存の HTML 要素を再利用できるようにブロックステートメントを定義することです。これにより、追加の区切り文字を挿入して、ステートメントの開始位置と終了位置を定義する必要がなくなります。このような目立たないマークアップのアノテーションによって、静的な HTML が機能的な動的テンプレートに変換されます。これには、HTML コードの妥当性を損なわず、静的ファイルとしても適切に表示できるというメリットがあります。

ただし、ブロックステートメントを挿入する必要がある正確な場所に、既存の要素が存在しない可能性があります。そのような場合には、SLY 要素を挿入できます。これは、出力から自動的に削除されますが、関連付けられたブロックステートメントを実行し、それに応じてコンテンツを表示します。

次に例を示します。

```xml
<sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</sly>
```

次のような HTML が出力されるのは、**`jcr:title`** プロパティと **`jcr:decription`** プロパティの両方が定義され、いずれも空でない場合のみです。

```xml
<h1>MY TITLE</h1>
<p>MY DESCRIPTION</p>
```

注意する必要があるのは、既存の要素がブロックステートメントで注釈付けできなかったときだけ SLY 要素を使用するということです。なぜなら、言語によって提供される値が、静的な HTML を動的にする際にその HTML を変更しないように、SLY 要素が防ごうとするためです。

例えば、前の例が既に DIV 要素にラップされている場合、SLY 要素の追加は正しくありません。

```xml
<div>
    <sly data-sly-test="${properties.jcr:title && properties.jcr:description}">
        <h1>${properties.jcr:title}</h1>
        <p>${properties.jcr:description}</p>
    </sly>
</div>
```

DIV 要素に条件が注釈付けされている可能性もあります。

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

### HTL コメント {#htl-comments}

次の例の&#x200B;**行 1** は HTL コメント、**行 2** は HTML コメントです。

```xml
<!--/* An HTL Comment */-->
<!-- An HTML Comment -->
```

HTL のコメントは、HTML コメントに JavaScript 形式の構文が加えられたものです。HTL コメント全体および HTL コメント内部の文字列は、プロセッサーによって完全に無視され、出力から削除されます。

ただし、標準 HTML コメントの内容はパススルーされ、コメント内部の式は評価されます。

HTML コメントに HTL コメントを含めることも、HTL コメントに HTML コメントを含めることもできません。

### 特別なコンテキスト {#special-contexts}

HTL を最大限に活用するためには、HTL が HTML 構文に基づいているという関係をよく理解することが重要です。

### 要素名と属性名 {#element-and-attribute-names}

式は HTML テキストまたは属性値としてのみ指定できます。要素名や属性名の内部には指定できません。そのようにしないと有効な HTML として認識されません。要素名を動的に設定するには、対象の要素について [`data-sly-element`](block-statements.md#element) ステートメントを使用できます。また、属性名を動的に設定するには、[`data-sly-attribute`](block-statements.md#attribute) ステートメントを使用できます。複数の属性を一度に設定することもできます。

```xml
<h1 data-sly-element="${myElementName}" data-sly-attribute="${myAttributeMap}">...</h1>
```

### ブロックステートメントなしのコンテキスト {#contexts-without-block-statements}

HTL はデータ属性を使用してブロックステートメントを定義するので、次のようなコンテキストにはそうしたブロックステートメントを定義できません。使用できるのは式のみです。

* HTML コメント
* script 要素
* style 要素

理由は、これらのコンテキストの内容がテキストであり HTML ではないことです。含まれる HTML 要素は単純な文字データとみなされます。本来の HTML 要素がないと、実行される **`data-sly`** 属性を指定できません。

これは大きな制約と思われるかもしれませんが、必要な制約です。HTML テンプレート言語を使用して、HTML ではない出力を生成すべきではないからです。この後の[ロジックにアクセスするための Use-API](getting-started.md#use-api-for-accessing-logic) では、追加ロジックをテンプレートから呼び出す方法を説明しています。これは、このようなコンテキストで複雑な出力を用意する必要があるときに使用できます。例えば、バックエンドスクリプトからフロントエンドスクリプトにデータを送る簡単な方法は、コンポーネントのロジックで JSON 文字列を生成することです。単純な HTL 式を使用してこれをデータ属性に含めることができます。

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

### 明示的なコンテキストの必要性 {#explicit-contexts-required}

この後の[自動コンテキスト認識エスケープ](getting-started.md#automatic-context-aware-escaping)で説明するように、HTL の目的の 1 つは、コンテキスト認識エスケープをすべての式に自動的に適用して、クロスサイトスクリプティング（XSS）によって脆弱性がもたらされるリスクを低減することです。HTL は、HTML マークアップ内部に配置された式のコンテキストを自動的に検出できますが、インライン JavaScript または CSS の構文の分析は行いません。したがって、そのような式に適用する必要がある正確なコンテキストを明示的に指定するのは開発者の責任です。

正しいエスケープを適用しないと XSS の脆弱性が引き起こされるので、HTL は、script と style のコンテキストが宣言されていないとき、それらのコンテキストにあるすべての式の出力を削除します。

次の例では、script と style の内部に指定された式のコンテキストを設定する方法を示します。

```xml
<script> var trackingID = "${myTrackingID @ context='scriptString'}"; </script>
<style> a { font-family: "${myFont @ context='styleString'}"; } </style>
```

エスケープを制御する方法の詳細は、[式言語の表示コンテキスト](expression-language.md#display-context)を参照してください。

### 特別なコンテキストの制約解除 {#lifting-limitations-of-special-contexts}

特殊なケースですが、script、style、コメントのコンテキストの制約を回避する必要がある場合には、それらの内容を別の HTL ファイルに分離することができます。専用のファイルに分離された内容はすべて HTL によって通常の HTML フラグメントと解釈され、それらが含まれていた制約のあるコンテキストは無視されます。

この例は、このページの後半にある[クライアント側テンプレートの使用](getting-started.md#working-with-client-side-templates)を参照してください。

>[!CAUTION]
>
>この方法ではクロスサイトスクリプティング（XSS）の脆弱性がもたらされることがあります。これを使用する場合にはセキュリティの面をよく調べる必要があります。通常は、このやり方を使用しなくても同じ処理を実装するさらに適切な方法があります。

## HTL の一般的な機能 {#general-capabilities-of-htl}

ここでは、HTML テンプレート言語の一般的な機能を簡単に説明します。

### ロジックにアクセスするための Use-API {#use-api-for-accessing-logic}

次の例について考えてみます。

```xml
<p data-sly-use.logic="logic.js">${logic.title}</p>
```

`logic.js` に続けて、サーバー側で実行される JavaScript ファイルが指定されています。

```
use(function () {
    return {
        title: currentPage.getTitle().substring(0, 10) + "..."
    };
});
```

HTML テンプレート言語では、マークアップ内でコードを混在できないので、その代わりにテンプレートからコードを簡単に実行する Use-API 拡張メカニズムを提供しています。

上記の例は、サーバー側で実行される JavaScript を使用してタイトルを 10 文字に短縮します。Java コードを使用しても、完全修飾 Java クラス名を提供することで同じ処理を行うことができます。通常、ビジネスロジックは Java で構築する方が望ましいのですが、コンポーネントで、Java API で提供される内容からビュー固有の変更が必要な場合は、そのためにサーバー側で実行される JavaScript を使用すると便利です。

次の節ではこれについてさらに詳しく説明します。

* [data-sly-use ステートメント](block-statements.md#use)に関する節では、このステートメントで実行できるすべての処理について説明します。
* [Use-API のページ](use-api.md)では、ロジックを記述するために Java と JavaScript のどちらを選ぶかということについて参考となる情報を説明します。
* ロジックの記述方法の詳細については、[JavaScript Use-API](use-api-javascript.md) と [Java Use-API](use-api-java.md) のぺージが参考になります。

### 自動コンテキスト認識エスケープ {#automatic-context-aware-escaping}

次の例について考えてみます。

```xml
<p data-sly-use.logic="logic.js">
    <a href="${logic.link}" title="${logic.title}">
        ${logic.text}
    </a>
</p>
```

ほとんどのテンプレート言語では、この例によってクロスサイトスクリプティング（XSS）の脆弱性が生じる可能性があります。すべての変数が自動的に HTML エスケープされた場合でも、`href` 属性を特に URL エスケープする必要があるからです。これは非常に忘れやすく、自動的に検出するのも難しいので、最も一般的なエラーの 1 つになっています。

これに対処するために、HTML テンプレート言語では、指定されたコンテキストに応じて各変数を自動的にエスケープします。この方法が可能なのは、HTL が HTML の構文を認識しているためです。

次の `logic.js` ファイルがあるとします。

```
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

2 つの属性が別の方法でエスケープされていることに注意してください。HTL では、`href` 属性と `src` 属性を URI コンテキストに応じてエスケープする必要があることが認識されています。また、URI の先頭に「**`javascript:`**」が付いている場合は、コンテキストが明示的に別のものに変更されない限り、その属性全体が削除されます。

エスケープを制御する方法の詳細は、[式言語の表示コンテキスト](expression-language.md#display-context)を参照してください。

### 空の属性の自動削除 {#automatic-removal-of-empty-attributes}

次の例について考えてみます。

```xml
<p class="${properties.class}">some text</p>
```

`class` プロパティの値が空の場合、HTML テンプレート言語は `class` 属性全体を出力から自動的に削除します。

このようにできるのも、HTL が HTML 構文を認識し、動的な値が空でない場合のみ、条件によってその値を含む属性を表示できるためです。これにより、属性の周囲に条件ブロックを追加せずにすみ、非常に便利です。条件ブロックを追加すると、マークアップが無効になったり読み取れなくなったりすることがあります。

また、式に含まれる変数の型も重要です。

* **String:**
   * **空ではない場合：**&#x200B;文字列を属性値として設定します。
   * **空の場合：**&#x200B;属性全体を削除します。

* **Number：**&#x200B;値を属性値として設定します。

* **Boolean:**
   * **true：**&#x200B;属性を値なしで表示します（ブール値の HTML 属性として）。
   * **false：**&#x200B;属性全体を削除します。

次の例は、ブール式を使用して ブール値の HTML 属性を制御する方法を示します。

```xml
<input type="checkbox" checked="${properties.isChecked}"/>
```

属性を設定する場合は、[`data-sly-attribute`](block-statements.md#attribute) ステートメントも使用できます。

## HTL の一般的な使用方法 {#common-patterns-with-htl}

ここでは、一般的なシナリオをいくつか示し、HTML テンプレート言語を使用して解決する最適な方法を説明します。

### クライアントライブラリの読み込み {#loading-client-libraries}

HTL では、クライアントライブラリは AEM 提供のヘルパーテンプレートを介して読み込まれます。テンプレートには [`data-sly-use`](block-statements.md#use) を使用してアクセスできます。このファイルには 3 つのテンプレートが含まれ、[`data-sly-call`](block-statements.md#template-call) で呼び出すことができます。

* **`css`** - 参照されるクライアントライブラリの CSS ファイルのみを読み込みます。
* **`js`** - 参照されるクライアントライブラリの JavaScript ファイルのみを読み込みます。
* **`all`** - 参照されるクライアントライブラリのすべてのファイル（CSS と JavaScript 両方）を読み込みます。

各ヘルパーテンプレートには、必要なクライアントライブラリを参照するための **`categories`** オプションを指定できます。このオプションには、文字列値の配列またはコンマ区切り値のリストを含む文字列を指定できます。

以下に、2 つの短い例を示します。

### 複数のクライアントライブラリをすべて一度に読み込む {#loading-multiple-client-libraries-fully-at-once}

```xml
<sly data-sly-use.clientlib="/libs/granite/sightly/templates/clientlib.html"
     data-sly-call="${clientlib.all @ categories=['myCategory1', 'myCategory2']}"/>
```

### ページの異なるセクションでクライアントライブラリを参照する {#referencing-a-client-library-in-different-sections-of-a-page}

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

上記の 2 つ目の例で、HTML **`head`** 要素と **`body`** 要素が別のファイルに指定された場合、**`clientlib.html`** テンプレートは、テンプレートが必要な各ファイルに読み込まれる必要があります。

[template および call](block-statements.md#template-call) ステートメントに関する節では、テンプレートの宣言と呼び出しの仕組みについて詳しく記載しています。

### データをクライアントに渡す {#passing-data-to-the-client}

データをクライアントに渡すための最適かつ洗練された方法は、HTL をさらに活用して、データ属性を使用することです。

次の例は、ロジック（Java でも記述可能）を使用して、クライアントに渡すオブジェクトを JSON にシリアライズする非常に便利な方法です(この後でオブジェクトを簡単にデータ属性に配置できます)。

```xml
<!--/* template.html file: */-->
<div data-sly-use.logic="logic.js" data-json="${logic.json}">...</div>
```

```
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

その後、クライアント側の JavaScript がどのようにその属性にアクセスして再び JSON を解析するかは簡単に推測できます。例えば、対応する JavaScript を次に示します。これがクライアントライブラリへの配置を行います。

```
var elements = document.querySelectorAll("[data-json]");
for (var i = 0; i < elements.length; i++) {
    var obj = JSON.parse(elements[i].dataset.json);
    //console.log(obj);
}
```

### クライアント側テンプレートの使用 {#working-with-client-side-templates}

[特別なコンテキストの制約解除](getting-started.md#lifting-limitations-of-special-contexts)の節で説明した方法が有効に使用できる特別なケースの 1 つは、**script** 要素に配置するクライアント側のテンプレート（例えば Handlebars）を記述する場合です。このケースでこの方法を安全に使用できる理由は、想定とは異なり **script** 要素に JavaScript は含まれず HTML 要素が含まれるからです。その仕組みを次の例で示します。

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

この例では、**`script`** 要素に組み込まれるマークアップが HTL ブロックステートメントを含むことができます。式は明示的なコンテキストを示す必要がありません。Handlebars テンプレートの内容はそれ自体のファイルに分離されているからです。また、この例に示すように、サーバー側で実行される HTL（**`h2`** 要素）を、Handlebars のようなクライアント側で実行されるテンプレート言語（**`h3`** 要素）と混在させることができます。

ただし、最新の方法では HTML **`template`** 要素が代わりに使用されます。この方法には、テンプレートの内容を個別のファイルに分離する必要がないというメリットがあります。

**関連項目：**

* [式言語](expression-language.md) – HTL 式で実行できる処理を詳しく説明します。
* [ブロックステートメント](block-statements.md) – HTL で使用できるすべてのブロックステートメントとその使用方法を説明します。
