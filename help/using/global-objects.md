---
title: HTL グローバルオブジェクト
description: HTL での列挙可能オブジェクトと Java ベースのオブジェクトについて説明します。
exl-id: ca590b92-f1b3-4e44-a04a-a2c10dff256f
index: false
TQID: https://experienceleague.adobe.com/zhQ4NTPQXgqIUNL9TeYk7sHqVQ5x-xWjps3OylhPJL4
product_v2: id: fd1f54a9-f50c-467d-8956-cebbaf4f3eb8
role_v2: id: ff6a42d2-313e-452e-93a6-792e4fad9ff8
source-git-commit: 944fa924e7ccba0a195b2c92584ab75df86b1f83
workflow-type: ht
source-wordcount: 277
ht-degree: 100%

---

# HTL グローバルオブジェクト {#htl-global-objects}

何も指定しなくても、HTL では開発者に役立つ多くのオブジェクトにアクセスできます。 これらのオブジェクトは、[Use-API](java-use-api.md) を利用した際に一緒に提供されます。

>[!NOTE]
>
>AEM での JSP 開発に詳しい開発者は、`global.jsp` を含めると、 HTL を使用して JSP で一般的に使用できるすべてのオブジェクトにアクセスできます。

## 列挙可能オブジェクト {#enumerable-objects}

これらのオブジェクトを使用すると、よく使用する情報に便利にアクセスできます。 コンテンツにはドット表記でアクセスでき、`data-sly-list` または `data-sly-repeat` を使用して反復処理できます。

| 変数名 | 説明 | ベース |
|--- |--- |--- |
| `properties` | 現在のリソースのプロパティリスト | [`org.apache.sling.api.resource.ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) |
| `pageProperties` | 現在のページのページプロパティリスト | [`org.apache.sling.api.resource.ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) |
| `inheritedPageProperties` | 現在のページの継承されたページプロパティリスト | [`org.apache.sling.api.resource.ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) |

## Java ベースのオブジェクト {#java-backed-objects}

対応する Java オブジェクトは、次の各オブジェクトをサポートします。

| 変数名 | 説明 |
|---|---|
| `component` | `com.day.cq.wcm.api.components.Component` |
| `componentContext` | `com.day.cq.wcm.api.components.ComponentContext` |
| `currentContentPolicy` | `com.day.cq.wcm.api.policies.ContentPolicy` |
| `currentContentPolicyProperties` | `com.day.cq.wcm.api.policies.ContentPolicy` |
| `currentDesign` | `com.day.cq.wcm.api.designer.Design` |
| `currentNode` | `javax.jcr.Node` |
| `currentPage` | `com.day.cq.wcm.api.Page` |
| `currentSession` | `javax.servlet.http.HttpSession` |
| `currentStyle` | `com.day.cq.wcm.api.designer.Style` |
| `designer` | `com.day.cq.wcm.api.designer.Designer` |
| `editContext` | `com.day.cq.wcm.api.components.EditContext` |
| `log` | `org.slf4j.Logger` |
| `out` | `java.io.PrintWriter` |
| `pageManager` | `com.day.cq.wcm.api.PageManager` |
| `reader` | `java.io.BufferedReader` |
| `request` | `org.apache.sling.api.SlingHttpServletRequest` |
| `resolver` | `org.apache.sling.api.resource.ResourceResolver` |
| `resource` | `org.apache.sling.api.resource.Resource` |
| `resourceDesign` | `com.day.cq.wcm.api.designer.Design` |
| `resourcePage` | `com.day.cq.wcm.api.Page` |
| `response` | `org.apache.sling.api.SlingHttpServletResponse` |
| `sling` | `org.apache.sling.api.scripting.SlingScriptHelper` |
| `slyWcmHelper` | `com.adobe.cq.sightly.WCMScriptHelper` |
| `wcmmode` | `com.adobe.cq.sightly.SightlyWCMMode` |
| `xssAPI` | `com.adobe.granite.xss.XSSAPI` |

## JavaScript ベースのオブジェクト {#javascript-backed-objects}

HTL ロジックを JavaScript でバックアップすることもできます。 ただし、好ましい、推奨される方法は、[Sling モデル](https://sling.apache.org/documentation/bundles/models.html)を使用することです。

>[!NOTE]
>
>[JavaScript Use API](https://github.com/adobe/htl-spec/blob/master/SPECIFICATION.md#42-javascript-use-api) は、AEM as a Cloud Service での使用が廃止されました。 代わりに、[Java Use API](java-use-api.md) を使用してください。
>
>非推奨（廃止予定）の機能および削除された機能について詳しくは、[AEM as a Cloud Service リリースノート](https://experienceleague.adobe.com/ja/docs/experience-manager-cloud-service/content/release-notes/deprecated-removed-features)を参照してください。
