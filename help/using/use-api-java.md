---
title: HTL Java Use-API
description: HTML テンプレート言語（HTL）Java Use-API を使用すると、HTL ファイルからカスタム Java クラスのヘルパーメソッドへのアクセスが可能になります。
exl-id: 9a9a2bf8-d178-4460-a3ec-cbefcfc09959
source-git-commit: 8e70ee4921a7ea071ab7e06947824c371f4013d8
workflow-type: ht
source-wordcount: '2558'
ht-degree: 100%

---

# HTL Java Use-API {#htl-java-use-api}

HTML テンプレート言語（HTL）Java Use-API を使用すると、HTL ファイルから `data-sly-use` を介してカスタム Java クラスのヘルパーメソッドへのアクセスが可能になります。これにより、複雑なビジネスロジックをすべて Java コードでカプセル化し、HTL コードではマークアップの直接作成処理のみを行うことができます。

Java Use-API オブジェクトは、POJO のデフォルトのコンストラクタを通じて、特定の実装でインスタンス化された単純な POJO にすることができます。

Use-API POJO は、次のシグネチャを持つ init と呼ばれるパブリックメソッドも公開できます。

```java
    /**
     * Initializes the Use bean.
     *
     * @param bindings All bindings available to the HTL scripts.
     **/
    public void init(javax.script.Bindings bindings);
```

`bindings` マップには、Use-API オブジェクトが処理に使用できる、現在実行されている HTL スクリプトにコンテキストを提供するオブジェクトを含めることができます。

## 簡単な例 {#a-simple-example}

まず、use クラスを持たない HTL コンポーネントから始めます。これは単一のファイル、`/apps/my-example/components/info.html` で構成されています。

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html}

```xml
<div>
    <h1>${properties.title}</h1>
    <p>${properties.description}</p>
</div>
```

また、このコンポーネントが `/content/my-example/` でレンダリングするコンテンツもいくつか追加します。

### `http://<host>:<port>/content/my-example.json` {#http-localhost-content-my-example-json}

```java
{
    "sling:resourceType": "my-example/component/info",
    "title": "My Example",
    "description": "This Is Some Example Content."
}
```

このコンテンツにアクセスがあると、HTL ファイルが実行されます。HTL コード内で、コンテキストオブジェクト `properties` を使用して、現在のリソースの `title` および `description` にアクセスし、それらを表示します。出力される HTML は以下のとおりです。

### `view-source:http://<host>:<port>/content/my-example.html` {#view-source-http-localhost-content-my-example-html}

```xml
<div>
    <h1>My Example</h1>
    <p>This Is Some Example Content.</p>
</div>
```

### use クラスの追加 {#adding-a-use-class}

**info** コンポーネントは、現状では、use クラスがなくとも、その（非常に単純な）機能を実行できます。ただし、場合によっては、HTL ではできないことを実行する必要があるので、use クラスは必要です。ただし、以下のことに留意してください。

>[!NOTE]
>
>use クラスは、HTL だけでは実行できない場合にのみ使用してください。

例えば、`info` コンポーネントで、リソースの `title` と `description` プロパティを、すべて小文字で表示するとします。HTL には文字列を小文字にするためのメソッドがないので、use クラスが必要になります。これを行うには、以下のように、Java use クラスを追加して `info.html` を変更します。

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-1}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {
    private String lowerCaseTitle;
    private String lowerCaseDescription;

    @Override
    public void activate() throws Exception {
        lowerCaseTitle = getProperties().get("title", "").toLowerCase();
        lowerCaseDescription = getProperties().get("description", "").toLowerCase();
    }

    public String getLowerCaseTitle() {
        return lowerCaseTitle;
    }

    public String getLowerCaseDescription() {
        return lowerCaseDescription;
    }
}
```

以降の節では、コードの様々な部分について順を追って説明します。

### ローカルとバンドルの Java クラス {#local-vs-bundle-java-class}

Java use クラスは、**ローカル**&#x200B;または&#x200B;**バンドル**&#x200B;の 2 つの方法でインストールできます。この例ではローカルインストールを使用します。

ローカルインストールでは、Java ソースファイルは HTL ファイルと並んで、同じリポジトリフォルダーに配置されます。ソースは、オンデマンドで自動でコンパイルされます。コンパイルやパッケージを別途行う必要はありません。

バンドルインストールでは、Java クラスは、標準の AEM バンドルデプロイメントメカニズムを使用してコンパイルし、OSGi バンドル内にデプロイする必要があります（[バンドルされた Java クラス](#bundled-java-class)を参照）。

>[!NOTE]
>
>**ローカル Java use クラス**&#x200B;は、use クラスが対象コンポーネントに特有の場合にお勧めします。
>
>**バンドル Java use クラス**&#x200B;は、複数の HTL コンポーネントからアクセスがあるサービスを Java コードで実装する場合にお勧めします。

### Java パッケージはリポジトリパス {#java-package-is-repository-path}

ローカルインストールを使用するとき、use クラスのパッケージ名は、リポジトリフォルダーの場所と一致する必要があります。ただし、パス内のハイフンは、パッケージ名ではアンダースコアに置き換えられます。

ここでは、`Info.java` は `/apps/my-example/components/info` に配置されているので、パッケージは `apps.my_example.components.info` です。

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-1}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {

   ...

}
```

>[!NOTE]
>
>AEM の開発では、リポジトリ項目の名前にハイフンを使用することが推奨されています。ただし、ハイフンは Java パッケージ名では不正です。この理由から、**リポジトリパス内のハイフンはすべて、パッケージ名ではアンダースコアに変換する必要があります**。

### `WCMUsePojo` の拡張 {#extending-wcmusepojo}

Java クラスを HTL に組み込むには多数の方法がありますが（「`WCMUsePojo` の代替策」を参照）、最も簡単な方法は `WCMUsePojo` クラスを拡張することです。

#### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-2}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo

    ...
}
```

### クラスの初期化 {#initializing-the-class}

`WCMUsePojo` から use クラスが拡張されると、`activate` メソッドを上書きすることによって初期化が実行されます。

### /apps/my-example/component/info/Info.java {#apps-my-example-component-info-info-java-3}

```java
...

public class Info extends WCMUsePojo {
    private String lowerCaseTitle;
    private String lowerCaseDescription;

    @Override
    public void activate() throws Exception {
        lowerCaseTitle = getProperties().get("title", "").toLowerCase();
        lowerCaseDescription = getProperties().get("description", "").toLowerCase();
    }

...

}
```

### コンテキスト {#context}

[アクティベート](https://helpx.adobe.com/jp/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUsePojo.html)メソッドの通常の用途は、現在のコンテキスト（現在の要求やリソースなど）に基づいて HTL コードに必要な値を事前に計算し、（メンバー変数に）格納することです。

`WCMUsePojo` クラスを使用すると、HTL ファイル内で使用可能なものと同じコンテキストオブジェクトにアクセスできます（[グローバルオブジェクト](global-objects.md)を参照）。

`WCMUsePojo` を拡張するクラスでは、名前を基準として、以下のコードを使用してコンテキストオブジェクトにアクセスできます。

[`<T> T get(String name, Class<T> type)`](https://helpx.adobe.com/jp/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUsePojo.html)

また、以下のような適切で&#x200B;**便利なメソッド**&#x200B;によって、よく使用されるコンテキストオブジェクトに直接アクセスできます。

|  |  |
|---|---|
| [PageManager](https://helpx.adobe.com/jp/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/PageManager.html) | [getPageManager()](https://helpx.adobe.com/jp/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageManager()) |
| [Page](https://helpx.adobe.com/jp/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [getCurrentPage()](https://helpx.adobe.com/jp/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentPage()) |
| [Page](https://helpx.adobe.com/jp/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/Page.html) | [getResourcePage()](https://helpx.adobe.com/jp/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourcePage()) |
| [ValueMap](https://helpx.adobe.com/jp/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getPageProperties()](https://helpx.adobe.com/jp/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getPageProperties()) |
| [ValueMap](https://helpx.adobe.com/jp/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getProperties()](https://helpx.adobe.com/jp/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getProperties()) |
| [Designer](https://helpx.adobe.com/jp/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Designer.html) | [getDesigner()](https://helpx.adobe.com/jp/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getDesigner()) |
| [Design](https://helpx.adobe.com/jp/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Design.html) | [getCurrentDesign()](https://helpx.adobe.com/jp/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentDesign()) |
| [Style](https://helpx.adobe.com/jp/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/designer/Style.html) | [getCurrentStyle()](https://helpx.adobe.com/jp/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getCurrentStyle()) |
| [Component](https://helpx.adobe.com/jp/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/day/cq/wcm/api/components/Component.html) | [getComponent()](https://helpx.adobe.com/jp/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getComponent()) |
| [ValueMap](https://helpx.adobe.com/jp/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) | [getInheritedProperties()](https://helpx.adobe.com/jp/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getInheritedProperties) |
| [Resource](https://helpx.adobe.com/jp/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/Resource.html) | [getResource()](https://helpx.adobe.com/jp/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResource()) |
| [ResourceResolver](https://helpx.adobe.com/jp/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ResourceResolver.html) | [getResourceResolver()](https://helpx.adobe.com/jp/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResourceResolver()) |
| [SlingHttpServletRequest](https://helpx.adobe.com/jp/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) | [getRequest()](https://helpx.adobe.com/jp/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getRequest()) |
| [SlingHttpServletResponse](https://helpx.adobe.com/jp/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletResponse.html) | [getResponse()](https://helpx.adobe.com/jp/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getResponse()) |
| [SlingScriptHelper](https://helpx.adobe.com/jp/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/scripting/SlingScriptHelper.html) | [getSlingScriptHelper()](https://helpx.adobe.com/jp/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html#getSlingScriptHelper()) |

### ゲッターメソッド {#getter-methods}

use クラスが初期化されると、HTL ファイルが実行されます。この段階で、HTL は通常、use クラスの様々なメンバー変数の状態を引っ張ってきて、プレゼンテーション用にレンダリングします。

HTL ファイル内からこれらの値へアクセスできるようにするには、以下の命名規則に従って、use クラスでカスタムのゲッターメソッドを定義する必要があります。

* `getXyz` という形式のメソッドを使用すると、HTL ファイル内で `xyz` という名前のオブジェクトプロパティがアクセス可能になります。

例えば、次の例では、`getTitle` メソッドおよび `getDescription` メソッドを使用すると、HTL ファイルのコンテキスト内のオブジェクトプロパティ `title` および `description` がアクセス可能になります。

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-4}

```java
...

public class Info extends WCMUsePojo {

    ...

    public String getLowerCaseTitle() {
        return lowerCaseTitle;
    }

    public String getLowerCaseDescription() {
        return lowerCaseDescription;
    }
}
```

### data-sly-use 属性 {#data-sly-use-attribute}

`data-sly-use` 属性は、HTL コード内で use クラスを初期化する際に使用します。ここに示す例では、`data-sly-use` 属性によって、`Info` クラスを使用することを宣言しています。ここではローカルインストールを使用している（Java ソースファイルを HTL ファイルと同じフォルダーに配置している）ので、クラスのローカル名だけを使用できます。バンドルインストールを使用していた場合は、完全修飾クラス名を指定する必要があります。

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-2}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### ローカル識別子 {#local-identifier}

識別子 `info`（`data-sly-use.info` のドットの後）は、HTL ファイル内で、クラスを識別するために使用します。宣言された識別子は、ファイル内でグローバルスコープとして認識されます。`data-sly-use` ステートメントを含む要素のみに制限されるわけではありません。

### `/apps/my-example/component/info/info.html`{#apps-my-example-component-info-info-html-3}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### プロパティの取得 {#getting-properties}

識別子 `info` はその後、ゲッターメソッド `title` および `description` によってアクセス可能になったオブジェクトプロパティ `Info.getTitle` および `Info.getDescription` へのアクセスに使用されます。

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-4}

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### 出力 {#output}

ここで、`/content/my-example.html` にアクセスすると、以下の HTML が返されます。

### `view-source:http://<host>:<port>/content/my-example.html` {#view-source-http-localhost-content-my-example-html-1}

```xml
<div>
    <h1>my example</h1>
    <p>this is some example content.</p>
</div>
```

## 応用 {#beyond-the-basics}

ここでは、上記の簡単な例よりも高度な機能をいくつか紹介します。

* use クラスにパラメーターを渡す
* バンドルされた Java use クラス
* `WCMUsePojo` の代替策

### パラメーターを渡す {#passing-parameters}

初期化時に、use クラスにパラメーターを渡すことができます。例えば、次のようなことが可能です。

### `/content/my-example/component/info/info.html` {#content-my-example-component-info-info-html}

```xml
<div data-sly-use.info="${'Info' @ text='Some text'}">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
    <p>${info.upperCaseText}</p>
</div>
```

ここでは、`text` というパラメーターを渡しています。その後、use クラスでは、取得した文字列を `info.upperCaseText` で大文字に変換して結果を表示します。適用された use クラスは次のとおりです。

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-5}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {

    ...

    private String reverseText;

    @Override
    public void activate() throws Exception {

        ...

        String text = get("text", String.class);
        reverseText = new StringBuffer(text).reverse().toString();

    }

    public String getReverseText() {
        return reverseText;
    }

    ...
}
```

パラメーターへのアクセスには `WCMUsePojo` メソッドを使用します。[`<T> T get(String paramName, Class<T> type)`](https://helpx.adobe.com/jp/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/com/adobe/cq/sightly/WCMUse.html)

ここでは、次のステートメントです。

`get("text", String.class)`

その後、次のメソッドで文字列が反転され、表示されます。

`getReverseText()`

### data-sly-template からのみパラメーターを渡す {#only-pass-parameters-from-data-sly-template}

上記の例は、技術的には正しいものの、問題の値が HTL コードの実行コンテキストで使用可能な場合（または上記のように値が静的である場合）に HTL から値を渡して use クラスを初期化する意味は、実際にはあまりありません。

理由は、use クラスが HTL コードと同じ実行コンテキストに常にアクセスできるからです。これにより、ベストプラクティスの読み込みポイントが提示されます。

>[!NOTE]
>
>use クラスにパラメーターを渡すのは、その use クラスが `data-sly-template` ファイルで使用されており、かつ渡す必要のあるパラメーターを含む別の HTL ファイルからその data-sly-template ファイルが呼び出されている場合のみにしてください。

例として、既存の例に加えて、別の `data-sly-template` ファイルを作成します。新しいファイルの名前は `extra.html` とします。これには、`data-sly-template` という名前の `extra` ブロックが含まれています。

### `/apps/my-example/component/info/extra.html` {#apps-my-example-component-info-extra-html}

```xml
<template data-sly-template.extra="${@ text}"
          data-sly-use.extraHelper="${'ExtraHelper' @ text=text}">
  <p>${extraHelper.reversedText}</p>
</template>
```

テンプレート `extra` は、単一のパラメーター `text` を取ります。その後、Java use クラス `ExtraHelper` をローカル名 `extraHelper` で初期化し、それに対して、テンプレートパラメーター `text` の値を use クラスパラメーター `text` として渡します。

テンプレートの本文は、プロパティ `extraHelper.reversedText`（内部で実際に `ExtraHelper.getReversedText()` を呼び出すプロパティ）を取得して、その値を表示します。

既存の `info.html` も、この新しいテンプレートを使用するように適応させます。

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-5}

```xml
<div data-sly-use.info="Info"
     data-sly-use.extra="extra.html">

  <h1>${info.lowerCaseTitle}</h1>
  <p>${info.lowerCaseDescription}</p>

  <div data-sly-call="${extra.extra @ text=properties.description}"></div>

</div>
```

`info.html` ファイルには、この時点で `data-sly-use` ステートメントが 2 つ含まれます。元からある、`Info` Java use クラスを読み込むステートメントと、ローカル名 `extra` でテンプレートファイルを読み込む新しいステートメントです。

テンプレートブロックを `info.html` ファイル内に配置すれば、`data-sly-use` が 2 つになるのを防ぐことができますが、テンプレートファイルは別にしたほうが一般的であり、再利用もしやすくなります。

`Info` クラスの採用方法は前と同じで、ゲッターメソッド `getLowerCaseTitle()` および `getLowerCaseDescription()` を、対応する HTL プロパティである `info.lowerCaseTitle` および `info.lowerCaseDescription` を使用して呼び出します。

次に、`data-sly-call` をテンプレート `extra` に対して実行し、`properties.description` の値を `text` パラメーターとして渡します。

Java use クラス `Info.java` は、新しいテキストパラメーターを処理するように変更されます。

### `/apps/my-example/component/info/ExtraHelper.java` {#apps-my-example-component-info-extrahelper-java}

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class ExtraHelper extends WCMUsePojo {
    private String reversedText;
    ...

    @Override
    public void activate() throws Exception {
        String text = get("text", String.class);
        reversedText = new StringBuilder(text).reverse().toString();

        ...
    }

    public String getReversedText() {
        return reversedText;
    }
}
```

`text` パラメーターは `get("text", String.class)` で取得され、値が反転されて、ゲッター `reversedText` を使用することにより、HTL オブジェクト `getReversedText()` として使用可能になります。

### バンドルされた Java クラス {#bundled-java-class}

バンドル use クラスの場合、クラスは、AEM で、標準の OSGi バンドルデプロイメントメカニズムを使用してコンパイル、パッケージ、デプロイを行う必要があります。ローカルインストールとは異なり、use クラスの&#x200B;**パッケージ宣言**&#x200B;には、通常どおりに名前を付けます。

### `/apps/my-example/component/info/Info.java` {#apps-my-example-component-info-info-java-6}

```java
package org.example.app.components;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {
    ...
}
```

また、`data-sly-use` ステートメントは、ローカルクラス名のみではなく、完全修飾クラス名を参照する必要があります。

### `/apps/my-example/component/info/info.html` {#apps-my-example-component-info-info-html-6}

```xml
<div data-sly-use.info="org.example.app.components.info.Info">
  <h1>${info.title}</h1>
  <p>${info.description}</p>
</div>
```

### `WCMUsePojo` の代替策 {#alternatives-to-wcmusepojo}

Java use クラスを作成する最も一般的な方法は、`WCMUsePojo` を拡張することです。ただし、これ以外にもいくつかのオプションがあります。他の方法について理解するには、HTL の `data-sly-use` ステートメントが内部でどのように機能するかを理解することが役立ちます。

次の `data-sly-use` ステートメントがあるとします。

**`<div data-sly-use.`** `localName`**`="`**`UseClass`**`">`**

このステートメントは以下のように処理されます。

（1）

* ローカルファイル `UseClass.java` が、HTL ファイルと同じディレクトリにある場合は、そのクラスをコンパイルして読み込んでみます。成功した場合は（2）に進みます。
* 失敗した場合は、`UseClass` を完全修飾クラス名として解釈し、OSGi 環境から読み込みます。成功した場合は（2）に進みます。
* 失敗した場合は、`UseClass` を HTL ファイルまたは JavaScript ファイルへのパスとして解釈し、そのファイルを読み込みます。成功した場合は（4）に進みます。

(2)

* 現在の `Resource` を `UseClass` に適応させます。成功した場合は（3）に進みます。
* 失敗した場合は、現在の `Request` を `UseClass` に適応させてみます。成功した場合は（3）に進みます。
* 失敗した場合は、引数ゼロのコンストラクターで `UseClass` をインスタンス化してみます。成功した場合は（3）に進みます。

（3）

* HTL 内で、新しく適応または作成したオブジェクトを、名前 `localName` にバインドします。
* `UseClass` が [`io.sightly.java.api.Use`](https://helpx.adobe.com/jp/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html) を実装する場合は、`init` メソッドを呼び出して、現在の実行コンテキスト（`javax.scripting.Bindings` オブジェクトの形式）を渡します。

(4)

* `UseClass` が、`data-sly-template` を含む HTL ファイルへのパスである場合は、テンプレートを用意します。
* そうでない場合、`UseClass` が JavaScript use クラスへのパスである場合は、use クラスを用意します（[JavaScript Use-API](use-api-javascript.md) を参照）。

上記の説明に関する重要な点を以下に示します。

* `Resource` から適応可能なクラス、`Request` から適応可能なクラス、引数ゼロのコンストラクターを持つクラスはどれも use クラスになることができます。`WCMUsePojo` を拡張することも、`Use` を実装することも、必須ではありません。
* ただし、use クラスが&#x200B;*実際に* `Use` を実装する場合は、その `init` メソッドが現在のコンテキストで自動的に呼び出され、そのコンテキストに依存する初期化コードを配置できます。
* `WCMUsePojo` を拡張する use クラスは、`Use` 実装の特殊ケースです。これは便利なコンテキストメソッドを提供し、`activate` メソッドは `Use.init` から自動的に呼び出されます。

### Use インターフェイスの直接実装 {#directly-implement-interface-use}

use クラスの最も一般的な作成方法は `WCMUsePojo` を拡張することですが、[`io.sightly.java.api.Use`](https://helpx.adobe.com/jp/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use.html) インターフェイス自体を直接実装することもできます。

`Use` インターフェイスで定義されるのは次の 1 つのメソッドのみです。

[`public void init(javax.script.Bindings bindings)`](https://helpx.adobe.com/jp/experience-manager/6-2/sites/developing/using/reference-materials/javadoc/io/sightly/java/api/Use#init(javax.script.Bindings))

`init` メソッドは、クラスの初期化時に、すべてのコンテキストオブジェクトおよび use クラスに渡されるすべてのパラメーターを保持する `Bindings` オブジェクトで呼び出されます。

すべての追加機能（`WCMUsePojo.getProperties()` と同等の機能など）を、[`javax.script.Bindings`](http://docs.oracle.com/javase/7/docs/api/javax/script/Bindings.html) オブジェクトを使用して明示的に実装する必要があります。次に例を示します。

### `Info.java` {#info-java}

```java
import io.sightly.java.api.Use;

public class MyComponent implements Use {
   ...
    @Override
    public void init(Bindings bindings) {

        // All standard objects/binding are available
        Resource resource = (Resource)bindings.get("resource");
        ValueMap properties = (ValueMap)bindings.get("properties");
        ...

        // Parameters passed to the use-class are also available
        String param1 = (String) bindings.get("param1");
    }
    ...
}
```

`Use` を拡張するのではなく `WCMUsePojo` インターフェイスを自分で実装する主なケースは、既に存在するクラスのサブクラスを use クラスとして使用する場合です。

### リソースから適応可能 {#adaptable-from-resource}

もう 1 つのオプションは、`org.apache.sling.api.resource.Resource` から適応可能なヘルパークラスを使用することです。

DAM アセットの MIME タイプを表示する HTL スクリプトを作成する必要があるとします。この場合、HTL スクリプトが呼び出されるのは、JCR `Node` をノードタイプ  `dam:Asset` でラップする `Resource` のコンテキスト内です。

`dam:Asset` ノードは次のような構造になっています。

### リポジトリ構造 {#repository-structure}

```java
{
  "content": {
    "dam": {
      "geometrixx": {
        "portraits": {
          "jane_doe.jpg": {
            ...
          "jcr:content": {
            ...
            "metadata": {
              ...
            },
            "renditions": {
              ...
              "original": {
                ...
                "jcr:content": {
                  "jcr:primaryType": "nt:resource",
                  "jcr:lastModifiedBy": "admin",
                  "jcr:mimeType": "image/jpeg",
                  "jcr:lastModified": "Fri Jun 13 2014 15:27:39 GMT+0200",
                  "jcr:data": ...,
                  "jcr:uuid": "22e3c598-4fa8-4c5d-8b47-8aecfb5de399"
                }
              },
              "cq5dam.thumbnail.319.319.png": {
                  ...
              },
              "cq5dam.thumbnail.48.48.png": {
                  ...
              },
              "cq5dam.thumbnail.140.100.png": {
                  ...
              }
            }
          }  
        }
      }
    }
  }
}
```

ここでは、サンプルプロジェクト geometrixx の一部として AEM のデフォルトインストールに含まれているアセット（JPEG 画像）を表示します。このアセットの名前は `jane_doe.jpg`、MIME タイプは `image/jpeg` です。

HTL 内からアセットにアクセスするには、`data-sly-use` ステートメント内で [`com.day.cq.dam.api.Asset`](https://helpx.adobe.com/jp/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/com/adobe/granite/asset/api/Asset.html) アセットをクラスとして宣言し、`Asset` の GET メソッドを使用して必要な情報を取得します。次に例を示します。

### `mimetype.html` {#mimetype-html}

```xml
<div data-sly-use.asset="com.day.cq.dam.api.Asset">
  <p>${asset.mimeType}</p>
</div>
```

`data-sly-use` ステートメントは、現在の `Resource` を `Asset` に適応し、`asset` というローカル名を付けるように、HTL に指示します。次に、HTL ゲッターの短縮形である `asset.mimeType` を使用して、`Asset` の  `getMimeType` メソッドを呼び出します。

### リクエストから適応可能 {#adaptable-from-request}

[`org.apache.sling.api.SlingHttpServletRequest`](https://helpx.adobe.com/jp/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) から適応可能な任意のクラスを use クラスとして採用することもできます

上記の `Resource` から適応可能な use クラスの場合と同様に、[`SlingHttpServletRequest`](https://helpx.adobe.com/jp/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) から適応可能な use クラスも `data-sly-use` ステートメントで指定できます。実行時に、現在のリクエストが与えられたクラスに適応され、結果のオブジェクトが HTL 内で使用可能になります。
