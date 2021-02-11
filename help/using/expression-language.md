---
title: HTL 式言語
description: HTML テンプレート言語は、式言語を使用して HTML 出力の動的要素を提供するデータ構造にアクセスします。
translation-type: tm+mt
source-git-commit: f7e46aaac2a4b51d7fa131ef46692ba6be58d878
workflow-type: tm+mt
source-wordcount: '1854'
ht-degree: 81%

---


# HTL 式言語 {#htl-expression-language}

HTML テンプレート言語は、式言語を使用して HTML 出力の動的要素を提供するデータ構造にアクセスします。この式言語は `${` と `}` で区切られます。HTML の形式を正しく保つために、式は、属性値、要素コンテンツ、コメントのいずれかのみで使用できます。

```xml
<!-- ${component.path} -->
<h1 class="${component.name}">
    ${properties.jcr:title}
</h1>
```

式は、`\`文字の前に付加されている場合にエスケープできます。例えば、`\${test}`は`${test}`をレンダリングします。

>[!NOTE]
>
>このページで示す例を試してみるには、[Read Eval Print Loop](https://github.com/Adobe-Marketing-Cloud/aem-sightly-repl) というライブ実行環境を使用できます。

式の構文には、[変数](#variables)、[リテラル](#literals)、[演算子](#operators)および[オプション](#options)が含まれます。

## 変数 {#variables}

変数は、データの値またはオブジェクトを格納するコンテナです。変数の名前は、識別子と呼ばれます。

何も指定しなくても、`global.jsp` をインクルードすると、通常 JSP で使用可能なすべてのオブジェクトへのアクセスが HTL で提供されます。[グローバルオブジェクト](global-objects.md)のページに、HTL によってアクセスが提供されるすべてのオブジェクトの一覧を示します。

### プロパティのアクセス {#property-access}

変数のプロパティにアクセスするには、ドット表記と括弧表記という 2 つの方法があります。

```xml
${currentPage.title}  
${currentPage['title']} or ${currentPage["title"]}
```

ほとんどのケースでは単純なドット表記をお勧めします。括弧表記は、識別子に無効な文字が含まれるプロパティへのアクセスやプロパティへの動的なアクセスに使用します。この後の 2 つの節でこれら 2 つのケースについて詳しく説明します。

アクセス対象のプロパティは関数の場合もありますが、引数を渡すことはできません。したがって、ゲッターのように引数を取らない関数にのみアクセスできます。これは、式に埋め込まれるロジックの量を減らすことを目的とした必要な制約です。必要であれば、[`data-sly-use`](block-statements.md#use) ステートメントを使用してパラメーターをロジックに渡すことができます。

また、前の例に示すように、Java のゲッター関数（`getTitle()` など）は、前に `get` を付けずに、後続の文字を小文字にすることでアクセスできます。

### 有効な識別子文字{#valid-identifier-characters}

変数の名前すなわち識別子には規則があります。開始には文字（`A`-`Z`と`a`-`z`）、またはアンダースコア(`_`)を使用する必要があり、それ以降の文字には数字(`0`-`9`)またはコロン(`:`)を使用できます。 Unicode 文字（`å` や `ü` など）は識別子では使用できません。

コロン(`:`)文字はAEMのプロパティ名で共通であるため、有効な識別子文字であることを強調する必要があります。

`${properties.jcr:title}`

括弧表記は、識別子に無効な文字（次の例の空白文字など）が含まれるプロパティにアクセスするために使用できます。

`${properties['my property']}`

### メンバーへの動的アクセス {#accessing-members-dynamically}

```xml
${properties[myVar]}
```

### Null 値の許容処理 {#permissive-handling-of-null-values}

```xml
${currentPage.lastModified.time.toString}
```

## リテラル {#literals}

リテラルは固定値を示す表記です。

### Boolean {#boolean}

ブール値は論理エンティティを表し、`true` と `false` の 2 つの値を持つことができます。

`${true} ${false}`

### 数値 {#numbers}

数値の型は正の整数のみです。浮動小数点など他の形式の数値は変数ではサポートされますが、リテラルとして表すことはできません。

`${42}`

### 文字列 {#strings}

文字列は、テキストデータを表し、単一または重複で引用します。

`${'foo'} ${"bar"}`

通常の文字に加えて次の特殊文字を使用できます。

* `\\` バックスラッシュ
* `\'` 一重引用符（アポストロフィ）
* `\"` 二重引用符
* `\t` タブ
* `\n` 改行
* `\r` キャリッジリターン
* `\f` フォームフィード
* `\b` Backspace
* `\uXXXX` 4 個の 16 進数（XXXX）で指定される Unicode 文字。\
   次の Unicode エスケープシーケンスは便利です。

   * `\u0022` の代わりに `"` を使用
   * `\u0027` の代わりに `'` を使用

上記以外の文字の場合は、バックスラッシュ文字の前にエラーが表示されます。

文字列のエスケープを使用する方法の例を次に示します。

```xml
<p>${'it\'s great, she said "yes!"'}</p>
<p title="${'it\'s great, she said \u0022yes!\u0022'}">...</p>
```

HTL ではコンテキスト固有のエスケープが適用されるので、上記の例では次の出力が生成されます。

```xml
<p>it&#39;s great, she said &#34;yes!&#34;</p>
<p title="it&#39;s great, she said &#34;yes!&#34;">...</p>
```

### 配列 {#arrays}

配列には、名前またはインデックスで参照できる複数の値が並んでいます。要素の型は混在することができます。

```xml
${[1,2,3,4]}
${myArray[2]}
```

配列は、テンプレートの値リストを指定する場合に役立ちます。

```xml
<ul data-sly-list="${[1,2,3,4]}">
  <li>${item}</li>
</ul>
```

## 演算子 {#operators}

### 論理演算子 {#logical-operators}

これらの演算子は通常は Boolean 値と一緒に使用されます。ただし、JavaScript と同様に、指定されたオペランドの 1 つの値が実際に返されます。つまり、非ブール値と一緒に使用すると、非ブール値が返されることがあります。

ある値が `true` に変換できる場合、その値は真です。ある値が `false` に変換できる場合、その値は偽です。`false`に変換できる値は、未定義の変数、null値、数値の0、および空の文字列です。

#### 論理否定（NOT） {#logical-not}

`${!myVar}` は、その1つ `false` のオペランドを次の値に変換できる場合に返し `true`ます。それ以外の場合は、を返し `true`ます。

例えば、子ページがない場合のみ要素を表示するなど、テスト条件を逆にするために使用できます。

```xml
<p data-sly-test="${!currentPage.hasChild}">current page has no children</p>
```

#### 論理積（AND） {#logical-and}

`${varOne && varTwo}` は、偽 `varOne` の場合に返します。それ以外の場合は、を返し `varTwo`ます。

この演算子を使用すると、2 つのプロパティの存在を確認するなど、2 つの条件を一度にテストできます。

```xml
<div data-sly-test="${properties.jcr:title && properties.jcr:description}">
    <h1>${properties.jcr:title}</h1>
    <p>${properties.jcr:description}</p>
</div>
```

論理積（AND）演算子は、HTML 属性を条件付きで表示する場合にも使用できます。動的に設定された値が false または空文字列に評価されると、そのような値を含む属性が HTL によって削除されるためです。つまり、次の例では `class` 属性が表示されるのは、`logic.showClass` が真で、さらに `logic.className` が存在し空でない場合のみです。

```xml
<div class="${logic.showClass && logic.className}">...</div>
```

#### 論理和（OR） {#logical-or}

`${varOne || varTwo}` 真 `varOne` の場合は返します。それ以外の場合は、を返し `varTwo`ます。

この演算子を使用すると、少なくとも 1 つのプロパティの存在を確認するなど、2 つの条件のいずれか 1 つが該当するかどうかをテストできます。

```xml
<div data-sly-test="${properties.jcr:title || properties.jcr:description}">...</div>
```

論理和（OR）演算子は、真である最初の変数を返します。また、フォールバック値を設定するために使用すると非常に便利です。

HTLは、falseまたは空の文字列に評価される式ーによって設定された値を持つ属性を削除するので、HTML属性を条件付きで表示する場合にも使用できます。 次の例では、**`properties.jcr:`**&#x200B;タイトルが存在し、空でない場合はタイトルが表示され、存在し、空でない場合は&#x200B;**`properties.jcr:description`**&#x200B;が表示されます。そうでない場合は、「タイトルまたは説明が指定されていません」というメッセージが表示されます。

```xml
<p>${properties.jcr:title || properties.jcr:description || "no title or description provided"}</p>
```

### 条件（三項）演算子 {#conditional-ternary-operator}

`${varCondition ? varOne : varTwo}` は `varOne` が真の場合に `varCondition` を返し、それ以外の場合に `varTwo` を返します。

通常、この演算子は、ページのステータスに応じて別のメッセージを表示するなど、式内の条件を定義するために使用できます。

```xml
<p>${currentPage.isLocked ? "page is locked" : "page can be edited"}</p>
```

>[!TIP]
>
>コロン文字は識別子でも使用できるので、パーサに明確にするために、三項演算子を空白で区切ることをお勧めします。

```xml
<p>${properties.showDescription ? properties.jcr:description : properties.jcr:title}</p>
```

### 比較演算子 {#comparison-operators}

等式および不等式の演算子で使用できるのは、同一の型のオペランドのみです。型が一致しない場合にはエラーが表示されます。

* 文字列は、文字が同じ順序で並んでいるときに等しいとみなされます。
* 数値は、同じ値のときに等しいとみなされます。
* ブール値は、両方が `true` または両方が `false` のときに等しいとみなされます。
* Null または未定義変数は、それぞれおよび互いに等しいとみなされます。

`${varOne == varTwo}` とが等し `true` い `varOne` 場合 `varTwo` に返します。

`${varOne != varTwo}` とが等し `true` くない `varOne` 場合 `varTwo` に返します。

関係演算子では数値であるオペランドしか使用できません。他のすべての型を使用するとエラーが表示されます。

`${varOne > varTwo}` が次の値より大き `true` い `varOne` 場合に返し `varTwo`ます。

`${varOne < varTwo}` より小さ `true` い `varOne` 場合は、を返し `varTwo`ます。

`${varOne >= varTwo}` が `true` を返すのは、`varOne` が `varTwo` よりも大きい場合か等しい場合です。

`${varOne <= varTwo}` が `true` を返すのは、`varOne` が `varTwo` よりも小さい場合か等しい場合です。

### グループ化の括弧 {#grouping-parentheses}

グループ化演算子 `()`  は、式における評価の順序を制御します。

`${varOne && (varTwo || varThree)}`

## Options {#options}

式のオプションは、式に作用したり式を変更したりできるほか、ブロックステートメントと共に使用するとパラメーターとしても機能します。

「`@`」より後はすべてオプションです。

```xml
${myVar @ optOne}
```

オプションには、変数またはリテラルの値を指定できます。

```xml
${myVar @ optOne=someVar}
${myVar @ optOne='bar'}
${myVar @ optOne=10}
${myVar @ optOne=true}
```

複数のオプションを使用する場合はコンマで区切ります。

```xml
${myVar @ optOne, optTwo=bar}
```

オプションのみを含むパラメーター式も可能です。

```xml
${@ optOne, optTwo=bar}
```

### 文字列の書式設定 {#string-formatting}

列挙されているプレースホルダー {*n*} を対応する値に置き換えるオプションです。

```xml
${'Page {0} of {1}' @ format=[current, total]}
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

`@extension`は、すべてのシナリオで機能し、拡張子を追加するかどうかをチェックします。

```xml
${ link @ extension = 'html' }
```

### 数値／日付の書式設定 {#number-date-formatting}

HTLを使用すると、カスタムコードを記述することなく、数値や日付をネイティブに書式設定できます。 また、タイムゾーンとロケールもサポートしています。

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
>使用できる形式の詳細については、[HTL仕様](https://github.com/Adobe-Marketing-Cloud/htl-spec/blob/master/SPECIFICATION.md)を参照してください。

### インターナショナライゼーション {#internationalization}

**&#x200B;現在の[辞書](https://docs.adobe.com/content/help/en/experience-manager-65/developing/components/internationalization/i18n-translator.html)を使用して、現在のソースの言語に文字列を翻訳します（以下を参照）。翻訳が見つからない場合は、元の文字列を使用します。

```xml
${'Page' @ i18n}
```

hint オプションを使用すると、トランスレーター用にコメントを提供し、テキストが使用されるコンテキストを指定できます。

```xml
${'Page' @ i18n, hint='Translation Hint'}
```

言語のデフォルトのソースは`resource`です。つまり、テキストはコンテンツと同じ言語に翻訳されます。 これは`user`に変更できます。つまり、言語はブラウザーのロケールまたはログインユーザーのロケールから取得されます。

```xml
${'Page' @ i18n, source='user'}
```

明示的なロケールを指定すると、ソース設定が上書きされます。

```xml
${'Page' @ i18n, locale='en-US'}
```

翻訳済みの文字列に変数を埋め込むには、format オプションを使用できます。

```xml
${'Page {0} of {1}' @ i18n, format=[current, total]}
```

### 配列の結合 {#array-join}

デフォルトで、配列をテキストとして表示する場合、HTL ではコンマ区切り値が（スペースなしで）表示されます。

別の区切り記号を指定するには、join オプションを使用します。

```xml
${['one', 'two'] @ join='; '}
```

### 表示コンテキスト {#display-context}

HTL 式の表示コンテキストは、HTML ページの構造内の場所を表します。例えば、レンダリング時にテキストノードを生成する場所に式が表示される場合、この式は `text` コンテキストにあると言えます。属性の値内にある場合は `attribute` コンテキストにあると言えます。その他も同様です。

スクリプト（JS）コンテキストとスタイル（CSS）コンテキストを除き、HTL は式のコンテキストを自動的に検出し、適宜エスケープして、XSS セキュリティの問題を防ぎます。スクリプトおよび CSS の場合は、必要なコンテキスト動作を明示的に設定する必要があります。また、コンテキスト動作は、自動動作を上書きすることが望ましいその他のケースでも明示的に設定できます。

ここでは、次のように、3 つの異なるコンテキストの 3 つの変数があるとします。

* `properties.link` ( `uri` コンテキスト)
* `properties.title` (`attribute` コンテキスト)
* `properties.text` (`text` コンテキスト)

HTL は、それぞれのコンテキストのセキュリティ要件に従って、それぞれを別々の方法でエスケープします。次のような通常のケースでは、明示的なコンテキスト設定は必要ありません。

```xml
<a href="${properties.link}" title="${properties.title}">${properties.text}</a>
```

マークアップを安全に出力する（つまり、式自体の評価結果が になる）場合は、`html`html コンテキストが使用されます。

```xml
<div>${properties.richText @ context='html'}</div>
```

スタイルコンテキストには明示的なコンテキストを設定する必要があります。

```xml
<span style="color: ${properties.color @ context='styleToken'};">...</span>
```

スクリプトコンテキストには明示的なコンテキストを設定する必要があります。

```xml
<span onclick="${properties.function @ context='scriptToken'}();">...</span>
```

エスケープと XSS 保護はオフにすることもできます。

```xml
<div>${myScript @ context='unsafe'}</div>
```

### コンテキストの設定 {#context-settings}

| コンテキスト | 用途 | 動作 |
|--- |--- |--- |
| `text` | 要素内のコンテンツのデフォルト | すべての HTML 特殊文字をエンコードします。 |
| `html` | マークアップを安全に出力 | AntiSamy ポリシールールを満たすように HTML をフィルタリングし、ルールに一致しないものを削除します。 |
| `attribute` | 属性値のデフォルト | すべての HTML 特殊文字をエンコードします。 |
| `uri` | リンクおよびパスを表示  href および src 属性値のデフォルト | href または src 属性値として記述するための URI を検証し、検証が失敗する場合は何も出力しません。 |
| `number` | 数値を表示 | 整数を含めるための URI を検証し、検証が失敗する場合はゼロを出力します。 |
| `attributeName` | 属性名を設定する際の data-sly-attribute のデフォルト | 属性名を検証し、検証が失敗する場合は何も出力しません。 |
| `elementName` | data-sly-element のデフォルト | 要素名を検証し、検証が失敗する場合は何も出力しません。 |
| `scriptToken` | JS 識別子、リテラル数またはリテラル文字列 | JavaScript トークンを検証し、検証が失敗する場合は何も出力しません。 |
| `scriptString` | JS 文字列内 | 文字列から抜き出される文字をエンコードします。 |
| `scriptComment` | JS コメント内 | JavaScript コメントを検証し、検証が失敗する場合は何も出力しません。 |
| `styleToken` | CSS 識別子、数値、サイズ、文字列、16 進数の色または機能 | CSS トークンを検証し、検証が失敗する場合は何も出力しません。 |
| `styleString` | CSS 文字列内 | 文字列から抜き出される文字をエンコードします。 |
| `styleComment` | CSS コメント内 | CSS コメントを検証し、検証が失敗する場合は何も出力しません。 |
| `unsafe` | 上記のどれでもジョブが実行されない場合のみ | エスケープと XSS 保護を完全に無効にします。 |
