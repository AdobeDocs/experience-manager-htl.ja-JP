---
title: AEM 拡張機能
description: AEM は、開発者の利便性を考慮して、AEM に対して HTL 仕様の拡張機能を提供します。
exl-id: d78cb84d-f958-45e2-9c6c-df86a68277d5
source-git-commit: 88edbd2fd66de960460df5928a3b42846d32066b
workflow-type: tm+mt
source-wordcount: '308'
ht-degree: 100%

---

# AEM 拡張機能 {#aem-extensions}

[HTL 仕様の Apache Sling 拡張機能](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#extensions-of-the-htl-specification-1)と同様に、AEM には、AEM の概念を HTL スクリプトで直接操作しやすくする、複数の追加の式オプションが用意されています。

## i18n {#i18n}

Apache Sling と同じ [3 つの追加オプション](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html#i18n)を `i18n` と併用できます。

* `locale`
* `hint`
* `basename`

ただし、AEM では、HTLに対する[国際化サポート](https://experienceleague.adobe.com/docs/experience-manager-65/developing/components/internationalization/i18n-dev.html?lang=ja)は、`com.day.cq.i18n` パッケージから API を使用して実装されています。

## data-sly-include {#data-sly-include}

AEM では、`data-sly-include` は追加の `wcmmode` オプションを使用し、含まれたスクリプトに対する [WCM モード](https://developer.adobe.com/experience-manager/reference-materials/cloud-service/javadoc/com/day/cq/wcm/api/WCMMode.html)を制御できます。使用できる値は、使用可能な enum 定数の名前です。

## data-sly-resource {#data-sly-resource}

パスと `Resources` に加えて、`data-sly-resource` ブロック要素は、[`Maps`](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/Map.html) または [`Records` とも動作することができます。](https://github.com/apache/sling-org-apache-sling-scripting-sightly-runtime/blob/master/src/main/java/org/apache/sling/scripting/sightly/Record.java) 両方のアプローチで、`resourceName` 文字列プロパティを指定する必要があります。この値は、レンダリングコンテキストに含まれる[合成リソース](https://www.javadoc.io/doc/org.apache.sling/org.apache.sling.api/latest/org/apache/sling/api/resource/SyntheticResource.html)の作成に使用されます。`data-sly-resource` に渡された`Record` または `Map` の残りのプロパティは、通常どおりに `Resource` プロパティとして使用されます。このマップに `sling:resourceType` プロパティが見つからない場合、リソースタイプは `resourceType` [式オプション](https://github.com/adobe/htl-spec/blob/1.4/SPECIFICATION.md#229-resource)の値またはレンダリングを駆動する現在のリソースのリソースタイプのどちらかと見なされます。

スクリプトスコープに、`map` として次のマップ／レコードのプロパティが使用可能であるとします。

```javascript
{
    resourceName: "myText",
    "sling:resourceType": "core/wcm/components/text/v2/text",
    "text": "Hello World!"
}
```

そして、次のマークアップが指定されます。

```html
<div class="outer" data-sly-resource="${map}"></div>
```

期待される出力は次のようになります。

```html
<div class="outer">
    <div class="myText">
        <div data-cmp-data-layer="{&quot;text-e58d65c472&quot;:{&quot;@type&quot;:&quot;core/wcm/components/text/v2/text&quot;,&quot;xdm:text&quot;:&quot;<p>Hello world!</p>&quot;}}" id="text-e58d65c472" class="cmp-text">
            <p>Hello world!</p>
        </div>
  </div>
</div>
```
