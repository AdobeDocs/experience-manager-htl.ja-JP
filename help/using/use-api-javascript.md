---
title: HTL JavaScript Use-API
description: HTMLテンプレート言語(HTL)JavaScript Use-APIを使用すると、HTLファイルからJavaScriptで記述されたヘルパーコードへのアクセスが可能になります。
exl-id: e98bfbd5-fa64-48c7-bd14-477d4c5e1788
source-git-commit: 8e70ee4921a7ea071ab7e06947824c371f4013d8
workflow-type: tm+mt
source-wordcount: '324'
ht-degree: 87%

---

# HTL JavaScript Use-API {#htl-javascript-use-api}

HTMLテンプレート言語(HTL)JavaScript Use-APIを使用すると、HTLファイルからJavaScriptで記述されたヘルパーコードへのアクセスが可能になります。 これにより、複雑なビジネスロジックをすべて JavaScript コードでカプセル化し、HTL コードではマークアップの直接作成処理のみを行うことができます。

次の規則が使用されます。

```javascript
/**
 * In the following example '/libs/dep1.js' and 'dep2.js' are optional
 * dependencies needed for this script's execution. Dependencies can
 * be specified using an absolute path or a relative path to this
 * script's own path.
 *
 * If no dependencies are needed the dependencies array can be omitted.
 */
use(['dep1.js', 'dep2.js'], function (Dep1, Dep2) {
    // implement processing
  
    // define this Use object's behavior
    return {
        propertyName: propertyValue
        functionName: function () {}
    }
});
```

## 簡単な例 {#a-simple-example}

以下の場所にあるコンポーネント、`info` を定義します。

`/apps/my-example/components/info`

これには次の 2 つのファイルが含まれます。

* **`info.js`**：use クラスを定義する JavaScript ファイル。
* **`info.html`**：コンポーネント `info` を定義する HTL ファイル。このコードは、use-API 経由で `info.js` の機能を使用します。

### /apps/my-example/component/info/info.js {#apps-my-example-component-info-info-js}

```java
"use strict";
use(function () {
    var info = {};
    info.title = resource.properties["title"];
    info.description = resource.properties["description"];
    return info;
});
```

### /apps/my-example/component/info/info.html {#apps-my-example-component-info-info-html}

```xml
<div data-sly-use.info="info.js">
    <h1>${info.title}</h1>
    <p>${info.description}</p>
</div>
```

また、`info` コンポーネントを使用するコンテンツノードも 

`/content/my-example` に作成し、次のプロパティを定義します。

* `sling:resourceType = "my-example/component/info"`
* `title = "My Example"`
* `description = "This is some example content."`

結果のリポジトリ構造は次のとおりです。

### リポジトリ構造 {#repository-structure}

```java
{
  "apps": {
    "my-example": {
      "components": {
        "info": {
          "info.html": {
            ...
          },
          "info.js": {
            ...
          }
        }
      }
    }
 },
 "content": {
    "my-example": {
      "sling:resourceType": "my-example/component/info",
      "title": "My Example",
      "description": "This is some example content."
    }
  }
}
```

次のコンポーネントテンプレートについて考えます。

```xml
<section class="component-name" data-sly-use.component="component.js">
    <h1>${component.title}</h1>
    <p>${component.description}</p>
</section>
```

対応するロジックは、テンプレートのすぐ横の  ファイルに配置されている次のサーバー側 JavaScript を使用して記述できます。`component.js`

```javascript
use(function () {
    var Constants = {
        DESCRIPTION_PROP: "jcr:description",
        DESCRIPTION_LENGTH: 50
    };

    var title = currentPage.getNavigationTitle() || currentPage.getTitle() || currentPage.getName();
    var description = properties.get(Constants.DESCRIPTION_PROP, "").substr(0, Constants.DESCRIPTION_LENGTH);

    return {
        title: title,
        description: description
    };
});
```

このコードは、別のソースから `title` の取得を行い、記述を 50 文字に切り詰めます。

## 依存関係 {#dependencies}

ナビゲーションタイトルのデフォルトロジックや文字列を一定の長さにうまく切り詰めるなどのスマート機能が既に備えられているユーティリティクラスがあると仮定します。

```javascript
use(['../utils/MyUtils.js'], function (utils) {
    var Constants = {
        DESCRIPTION_PROP: "jcr:description",
        DESCRIPTION_LENGTH: 50
    };

    var title = utils.getNavigationTitle(currentPage);
    var description = properties.get(Constants.DESCRIPTION_PROP, "").substr(0, Constants.DESCRIPTION_LENGTH);

    return {
        title: title,
        description: description
    };
});
```

## 拡張 {#extending}

依存関係のパターンは、別のコンポーネント（通常は現在のコンポーネントの `sling:resourceSuperType`）のロジックの拡張にも使用できます。

親コンポーネントによって `title` が既に指定されていて、`description` も追加すると仮定します。

```javascript
use(['../parent-component/parent-component.js'], function (component) {
    var Constants = {
        DESCRIPTION_PROP: "jcr:description",
        DESCRIPTION_LENGTH: 50
    };

    component.description = utils.shortenString(properties.get(Constants.DESCRIPTION_PROP, ""), Constants.DESCRIPTION_LENGTH);

    return component;
});
```

## テンプレートにパラメーターを渡す {#passing-parameters-to-a-template}

コンポーネントから独立可能な `data-sly-template` ステートメントの場合は、関連付けられている Use-API にパラメーターを渡すと便利な場合があります。

このコンポーネントでは、別のファイルに配置されているテンプレートを呼び出してみます。

```xml
<section class="component-name" data-sly-use.tmpl="template.html" data-sly-call="${tmpl.templateName @ page=currentPage}"></section>
```

以下のテンプレートが `template.html` に配置されています。

```xml
<template data-sly-template.templateName="${@ page}" data-sly-use.tmpl="${'template.js' @ page=page, descriptionLength=50}">
    <h1>${tmpl.title}</h1>
    <p>${tmpl.description}</p>
</template>
```

対応するロジックは、テンプレートファイルのすぐ横の `template.js` ファイルに配置されている次のサーバー側 JavaScript を使用して記述できます。

```javascript
use(function () {
    var Constants = {
        DESCRIPTION_PROP: "jcr:description"
    };

    var title = this.page.getNavigationTitle() || this.page.getTitle() || this.page.getName();
    var description = this.page.getProperties().get(Constants.DESCRIPTION_PROP, "").substr(0, this.descriptionLength);

    return {
        title: title,
        description: description
    };
});
```

渡されたパラメーターは、`this` キーワードで設定されます。
