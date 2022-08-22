---
title: AEM Extensions
description: AEMは、AEMに対して HTL 仕様の拡張を提供し、開発者としての利便性を考慮しています。
source-git-commit: 6d97bc5d0ab89dffaf56a54c73c94d069bb31ca6
workflow-type: tm+mt
source-wordcount: '308'
ht-degree: 0%

---


# AEM Extensions {#aem-extensions}

次に類似 [HTL 仕様の Apache Sling 拡張機能](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#extensions-of-the-htl-specification-1) AEMには、AEMの概念を HTL スクリプトで直接操作しやすくする、いくつかの追加の式オプションが用意されています。

## i18n {#i18n}

同じ [3 つの追加オプション](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#i18n) Apache Sling のように、Apache Sling はと一緒に使用できます `i18n`:

* `locale`
* `hint`
* `basename`

ただし、AEMでは、 [国際化支援](https://experienceleague.adobe.com/docs/experience-manager-65/developing/components/internationalization/i18n-dev.html) HTL の場合は、API を使用して `com.day.cq.i18n` パッケージ。

## data-sly-include {#data-sly-include}

AEMでは、 `data-sly-include` 追加の `wcmmode` を制御するオプション [WCM モード](https://developer.adobe.com/experience-manager/reference-materials/cloud-service/javadoc/com/day/cq/wcm/api/WCMMode.html) を含むスクリプトに対して使用します。 使用できる値は、使用可能な enum 定数の名前です。

## data-sly-resource {#data-sly-resource}

パスと `Resources`、 `data-sly-resource` ブロック要素は、 [`Maps`](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Map.html) または [`Records`.](https://github.com/apache/sling-org-apache-sling-scripting-sightly-runtime/blob/master/src/main/java/org/apache/sling/scripting/sightly/Record.java) 両方のアプローチで、 `resourceName` 文字列プロパティを指定する必要があります。 この値は、 [合成リソース](https://www.javadoc.io/doc/org.apache.sling/org.apache.sling.api/latest/org/apache/sling/api/resource/SyntheticResource.html) レンダリングコンテキストに含まれる の残りのプロパティ `Record` または `Map` が `data-sly-resource` 通常どおりに使用されます `Resource` プロパティ。 この `sling:resourceType` プロパティがこのマップに見つからない場合、リソースタイプは `resourceType` [式オプション](https://github.com/adobe/htl-spec/blob/1.4/SPECIFICATION.md#229-resource) レンダリングを駆動する現在のリソースのリソースタイプ。

次のマップ/レコードのプロパティがスクリプトスコープで次のように使用できる `map`:

```javascript
{
    resourceName: "myText",
    "sling:resourceType": "core/wcm/components/text/v2/text",
    "text": "Hello World!"
}
```

次のマークアップが指定されます。

```html
<div class="outer" data-sly-resource="${map}"></div>
```

次の出力が期待されます。

```html
<div class="outer">
    <div class="myText">
        <div data-cmp-data-layer="{&quot;text-e58d65c472&quot;:{&quot;@type&quot;:&quot;core/wcm/components/text/v2/text&quot;,&quot;xdm:text&quot;:&quot;<p>Hello world!</p>&quot;}}" id="text-e58d65c472" class="cmp-text">
            <p>Hello world!</p>
        </div>
  </div>
</div>
```
