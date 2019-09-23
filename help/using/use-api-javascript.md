---
title: HTL JavaScript Use-API
seo-title: HTL JavaScript Use-API
description: HTML テンプレート言語（HTL）JavaScript Use-API を使用すると、HTL ファイルから JavaScript で記述されたヘルパーコードへのアクセスが可能になります。
seo-description: HTML テンプレート言語（HTL）JavaScript Use-API を使用すると、HTL ファイルから JavaScript で記述されたヘルパーコードへのアクセスが可能になります。
uuid: 7ab34b10-30ac-44d6-926b-0234f52e5541
contentOwner: ユーザーは、
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: リファレンス
discoiquuid: 18871af8-e44b-4eec-a483-fcc765dae58f
mwpw-migration-script-version: 2017-10-12T21 46 58.665-0400
translation-type: tm+mt
source-git-commit: bd1962e25d152be4f1608d0a83d8d5b3e728b4aa

---


# HTL JavaScript Use-API {#htl-javascript-use-api}

HTML Template Langugae（HTL）JavaScript Use-API を使用すると、HTL ファイルから JavaScript で記述されたヘルパーコードへのアクセスが可能になります。これにより、複雑なビジネスロジックをすべて JavaScript コードでカプセル化し、HTL コードではマークアップの直接作成処理のみを行うことができます。

## 簡単な例 {#a-simple-example}

以下の場所にあるコンポーネント、`info` を定義します。

**`/apps/my-example/components/info`**

これには次の 2 つのファイルが含まれます。

* **`info.js`**：use クラスを定義する JavaScript ファイル。
* `info.html`：コンポーネント `info` を定義する HTL ファイル。このコードは、use-API 経由で `info.js` の機能を使用します。

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

また、**`info`** コンポーネントを使用するコンテンツノードも 

**`/content/my-example`** に作成し、次のプロパティを定義します。

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

対応するロジックは、テンプレートのすぐ横の *ファイルに配置されている次の&#x200B;**サーバー側**JavaScript を使用して記述できます。*`component.js`

```
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

```
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

親コンポーネントによって `title` が既に指定されていて、**`description`** も追加すると仮定します。

```
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

コンポーネントから独立可能な **`data-sly-template`** ステートメントの場合は、関連付けられている Use-API にパラメーターを渡すと便利な場合があります。

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

対応するロジックは、テンプレートファイルのすぐ横の `template.js` ファイルに配置されている次の&#x200B;***サーバー側*** JavaScript を使用して記述できます。

```
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
