---
title: HTL Java Use-API
description: HTL Java Use-API を使用すると、HTL ファイルからカスタム Java クラスのヘルパーメソッドへのアクセスが可能になります。
exl-id: 9a9a2bf8-d178-4460-a3ec-cbefcfc09959
source-git-commit: da2375a2390f0254dba9745d9f4970e88788e5d5
workflow-type: ht
source-wordcount: '1518'
ht-degree: 100%

---


# HTL Java Use-API {#htl-java-use-api}

HTL Java Use-API を使用すると、HTL ファイルからカスタム Java クラスのヘルパーメソッドへのアクセスが可能になります。

## ユースケース {#use-case}

HTL Java Use-API を使用すると、HTL ファイルは `data-sly-use` を通してカスタム Java クラスのヘルパーメソッドへのアクセスが可能になります。これにより、複雑なビジネスロジックをすべて Java コードでカプセル化し、HTL コードではマークアップの直接作成処理のみを行うことができます。

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

この例では、Use-API の使用方法を説明します。

>[!NOTE]
>
>この例は、その使用方法を簡単に説明するために簡略化しています。実稼動環境では、[Sling モデル](https://sling.apache.org/documentation/bundles/models.html)の使用を推奨します。

まず、use クラスを持たない `info` と呼ばれる HTL コンポーネントから始めます。これは単一のファイル、`/apps/my-example/components/info.html` で構成されています。

```xml
<div>
    <h1>${properties.title}</h1>
    <p>${properties.description}</p>
</div>
```

また、このコンポーネントが `/content/my-example/` でレンダリングするコンテンツもいくつか追加します。

```xml
{
    "sling:resourceType": "my-example/component/info",
    "title": "My Example",
    "description": "This Is Some Example Content."
}
```

このコンテンツにアクセスがあると、HTL ファイルが実行されます。HTL コード内で、コンテキストオブジェクト `properties` を使用して、現在のリソースの `title` および `description` にアクセスし、それらを表示します。出力されるファイル `/content/my-example.html` は以下のとおりです。

```html
<div>
    <h1>My Example</h1>
    <p>This Is Some Example Content.</p>
</div>
```

### use クラスの追加 {#adding-a-use-class}

現状の `info` コンポーネントは、その単純な機能を実行するために use クラスを必要としません。ただし、場合によっては、HTL ではできないことを実行する必要があるので、use クラスは必要です。ただし、以下のことに留意してください。

>[!NOTE]
>
>use クラスは、HTL だけでは実行できない場合にのみ使用してください。

例えば、`info` コンポーネントで、リソースの `title` と `description` プロパティを、すべて小文字で表示するとします。HTL には文字列を小文字にするためのメソッドがないので、use クラスが必要になります。これを行うには、以下のように、Java use クラスを追加して `/apps/my-example/component/info/info.html` を変更します。

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

さらに、`/apps/my-example/component/info/Info.java` を作成します。

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

詳しくは、[`com.adobe.cq.sightly.WCMUsePojo` の Javadocs](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html) を参照してください。

次に、コードの様々な部分について説明します。

### ローカルとバンドルの Java クラス {#local-vs-bundle-java-class}

Java use クラスは 2 つの方法でインストールできます。

* **ローカル** - ローカルインストールでは、Java ソースファイルは HTL ファイルと並んで、同じリポジトリーフォルダーに配置されます。ソースは、オンデマンドで自動でコンパイルされます。コンパイルやパッケージを別途行う必要はありません。
* **バンドル** - バンドルインストールでは、Java クラスは標準の AEM バンドルデプロイメントメカニズムを使用してコンパイルし、OSGi バンドル内にデプロイする必要があります（[バンドルされた Java クラス](#bundled-java-class)の節を参照）。

どの方法をいつ使用するかを把握するには、次の 2 つの点に注意してください。

* **ローカル Java use クラス**&#x200B;は、use クラスが対象コンポーネントに特有の場合にお勧めします。
* **バンドル Java use クラス**&#x200B;は、複数の HTL コンポーネントからアクセスがあるサービスを Java コードで実装する場合にお勧めします。

この例ではローカルインストールを使用します。

### Java パッケージはリポジトリーパス {#java-package-is-repository-path}

ローカルインストールを使用するとき、use クラスのパッケージ名は、リポジトリフォルダーの場所と一致する必要があります。ただし、パス内のハイフンは、パッケージ名ではアンダースコアに置き換えられます。

ここでは、`Info.java` は `/apps/my-example/components/info` に配置されているので、パッケージは `apps.my_example.components.info` です。

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

Java クラスを HTL に組み込む方法はいくつかありますが（[`WCMUsePojo`](#alternatives-to-wcmusepojo) の代替方法の節を参照）、最も簡単な方法は `WCMUsePojo` クラスを拡張することです。この例では `/apps/my-example/component/info/Info.java` です。

```java
package apps.my_example.components.info;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo

    ...
}
```

### クラスの初期化 {#initializing-the-class}

`WCMUsePojo` から use クラスが拡張されると、`activate` メソッド（この場合は `/apps/my-example/component/info/Info.java` 内）を上書きすることによって初期化が実行されます。

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

[アクティベート](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html)メソッドの通常の用途は、現在のコンテキスト（現在の要求やリソースなど）に基づいて HTL コードに必要な値を事前に計算し、（メンバー変数に）格納することです。

`WCMUsePojo` クラスを使用すると、HTL ファイル内で使用可能なものと同じコンテキストオブジェクトにアクセスできます（ドキュメント[グローバルオブジェクト](global-objects.md)を参照）。

`WCMUsePojo` を拡張するクラスでは、名前を基準として、以下のコードを使用してコンテキストオブジェクトにアクセスできます。

[`<T> T get(String name, Class<T> type)`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html)

また、一般的に使用されるコンテキストオブジェクトには、このテーブルに示す適切で便利なメソッドによって直接アクセスできます。

| オブジェクト | 便利なメソッド |
|---|---|
| [`PageManager`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/PageManager.html) | [`getPageManager()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getPageManager--) |
| [`Page`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/Page.html) | [`getCurrentPage()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getCurrentPage--) |
| [`Page`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/Page.html) | [`getResourcePage()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getResourcePage--) |
| [`ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) | [`getPageProperties()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getPageProperties--) |
| [`ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) | [`getProperties()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getProperties--) |
| [`Designer`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/designer/Designer.html) | [`getDesigner()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getDesigner--) |
| [`Design`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/designer/Design.html) | [`getCurrentDesign()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getCurrentDesign--) |
| [`Style`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/designer/Style.html) | [`getCurrentStyle()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getCurrentStyle--) |
| [`Component`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/day/cq/wcm/api/components/Component.html) | [`getComponent()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getComponent--) |
| [`ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) | [`getInheritedProperties()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getInheritedPageProperties--) |
| [`Resource`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/Resource.html) | [`getResource()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getResource--) |
| [`ResourceResolver`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ResourceResolver.html) | [`getResourceResolver()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getResourceResolver--) |
| [`SlingHttpServletRequest`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/SlingHttpServletRequest.html) | [`getRequest()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getRequest--) |
| [`SlingHttpServletResponse`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/SlingHttpServletResponse.html) | [`getResponse()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getResponse--) |
| [`SlingScriptHelper`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/scripting/SlingScriptHelper.html) | [`getSlingScriptHelper()`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/com/adobe/cq/sightly/WCMUsePojo.html#getSlingScriptHelper--) |

### ゲッターメソッド {#getter-methods}

use クラスが初期化されると、HTL ファイルが実行されます。この段階で、HTL は通常、use クラスの様々なメンバー変数の状態を引っ張ってきて、プレゼンテーション用にレンダリングします。

HTL ファイル内からこれらの値へアクセスできるようにするには、以下の命名規則に従って、use クラスでカスタムのゲッターメソッドを定義する必要があります。

* `getXyz` という形式のメソッドを使用すると、HTL ファイル内で `xyz` という名前のオブジェクトプロパティがアクセス可能になります。

次のサンプルファイル `/apps/my-example/component/info/Info.java` では、メソッド `getTitle` および `getDescription` により、オブジェクトプロパティ `title` および `description` が HTL ファイルのコンテキスト内でアクセス可能になります。

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

この `/apps/my-example/component/info/info.html` の例での使い方に注意してください。

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### ローカル識別子 {#local-identifier}

識別子 `info`（`data-sly-use.info` のドットの後）は、HTL ファイル内で、クラスを識別するために使用します。宣言された識別子は、ファイル内でグローバルスコープとして認識されます。`data-sly-use` ステートメントを含む要素のみに制限されるわけではありません。

この `/apps/my-example/component/info/info.html` の例での使い方に注意してください。

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### プロパティの取得 {#getting-properties}

識別子 `info` はその後、ゲッターメソッド `title` および `description` によってアクセス可能になったオブジェクトプロパティ `Info.getTitle` および `Info.getDescription` へのアクセスに使用されます。

この `/apps/my-example/component/info/info.html` の例での使い方に注意してください。

```xml
<div data-sly-use.info="Info">
    <h1>${info.lowerCaseTitle}</h1>
    <p>${info.lowerCaseDescription}</p>
</div>
```

### 出力 {#output}

ここで、`/content/my-example.html` にアクセスすると、以下の `/content/my-example.html` ファイルが返されます。

```xml
<div>
    <h1>my example</h1>
    <p>this is some example content.</p>
</div>
```

>[!NOTE]
>
>この例は、その使い方を簡単に説明するために簡略化されています。実稼動環境では、[Sling モデル](https://sling.apache.org/documentation/bundles/models.html)の使用を推奨します。

## 応用 {#beyond-the-basics}

この節では、上記の簡単な例よりも高度な機能をいくつか紹介します。

* use クラスにパラメーターを渡す
* バンドルされた Java use クラス

### パラメーターを渡す {#passing-parameters}

初期化時に、use クラスにパラメーターを渡すことができます。例えば、次のようなことが可能です。

詳しくは、Sling [HTL スクリプティングエンジンのドキュメント](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#passing-parameters-to-java-use-objects)を参照してください。

### バンドルされた Java クラス {#bundled-java-class}

バンドル use クラスの場合、クラスは、AEM で標準の OSGi バンドルデプロイメントメカニズムを使用してコンパイル、パッケージおよびデプロイされる必要があります。ローカルインストールとは異なり、use クラスのパッケージ宣言には、`/apps/my-example/component/info/Info.java` の例のように通常どおりに名前を付ける必要があります。

```java
package org.example.app.components;

import com.adobe.cq.sightly.WCMUsePojo;

public class Info extends WCMUsePojo {
    ...
}
```

また、`data-sly-use` ステートメントは、この `/apps/my-example/component/info/info.html` の例のように、ローカルクラス名のみではなく、完全修飾クラス名を参照する必要があります。

```xml
<div data-sly-use.info="org.example.app.components.info.Info">
  <h1>${info.title}</h1>
  <p>${info.description}</p>
</div>
```
