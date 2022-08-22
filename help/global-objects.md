---
title: HTL グローバルオブジェクト
description: 可算オブジェクト、Java ベースのオブジェクト、HTL での JavaScript ベースのオブジェクトについて説明します。
exl-id: ca590b92-f1b3-4e44-a04a-a2c10dff256f
source-git-commit: 5ab1275c984135fe946f36905bbc979cf19edd80
workflow-type: tm+mt
source-wordcount: '200'
ht-degree: 32%

---


# HTL グローバルオブジェクト {#htl-global-objects}

何も指定しなくても、HTL では開発者に役立つ多くのオブジェクトへのアクセスが提供されます。 これらのオブジェクトは、 [Use-API を使用します。](java-use-api.md)

>[!NOTE]
>
>AEMでの JSP の開発に詳しい開発者向けに、をインクルードすると、HTL では、通常 JSP で使用可能なすべてのオブジェクトへのアクセスが提供されます `global.jsp`.

## 可算オブジェクト {#enumerable-objects}

これらのオブジェクトを使用すると、よく使用する情報に便利にアクセスできます。そのコンテンツにはドット表記を使用してアクセスでき、 `data-sly-list` または `data-sly-repeat`.

| 変数名 | 説明 | バックアップ元 |
|--- |--- |--- |
| `properties` | 現在のリソースのプロパティのリスト | [`org.apache.sling.api.resource.ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) |
| `pageProperties` | 現在のページのページプロパティのリスト | [`org.apache.sling.api.resource.ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) |
| `inheritedPageProperties` | 現在のページの継承されたページプロパティのリスト | [`org.apache.sling.api.resource.ValueMap`](https://developer.adobe.com/experience-manager/reference-materials/6-5/javadoc/org/apache/sling/api/resource/ValueMap.html) |

## Java ベースのオブジェクト {#java-backed-objects}

次に示す各オブジェクトは、対応する Java オブジェクトをベースとします。

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

HTL ロジックを JavaScript でバックアップすることもできます。ただし、好ましい、推奨される方法は、[Sling モデル](https://sling.apache.org/documentation/bundles/models.html)を使用することです。
