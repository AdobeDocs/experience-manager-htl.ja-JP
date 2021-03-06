---
title: HTL グローバルオブジェクト
description: 何も指定しなくても、global.jsp をインクルードすると、通常 JSP で使用可能なすべてのオブジェクトへのアクセスが HTL で提供されます。
exl-id: ca590b92-f1b3-4e44-a04a-a2c10dff256f
source-git-commit: 8e70ee4921a7ea071ab7e06947824c371f4013d8
workflow-type: ht
source-wordcount: '220'
ht-degree: 100%

---

# HTL グローバルオブジェクト {#htl-global-objects}

何も指定しなくても、`global.jsp` をインクルードすると、通常 JSP で使用可能なすべてのオブジェクトへのアクセスが HTL で提供されます。これらのオブジェクトは、[Use-API](use-api.md) を利用した際に一緒に提供されます。

## 可算オブジェクト {#enumerable-objects}

これらのオブジェクトを使用すると、よく使用する情報に便利にアクセスできます。それらのオブジェクトのコンテンツには、ドット表記を使用してアクセスできます。また、`data-sly-list` または `data-sly-repeat` を使用すると、反復処理が可能です。

| 変数名 | 説明 |
|--- |--- |
| `properties` | 現在の Resource のプロパティのリスト。[org.apache.sling.api.resource.ValueMap](https://helpx.adobe.com/jp/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) によるバックアップ |
| `pageProperties` | 現在の Page のページプロパティのリスト。[org.apache.sling.api.resource.ValueMap](https://helpx.adobe.com/jp/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) によるバックアップ |
| `inheritedPageProperties` | 現在の Page の継承されたページプロパティのリスト。[org.apache.sling.api.resource.ValueMap](https://helpx.adobe.com/jp/experience-manager/6-5/sites/developing/using/reference-materials/javadoc/org/apache/sling/api/resource/ValueMap.html) によるバックアップ |

## Java ベースのオブジェクト {#java-backed-objects}

次に示す各オブジェクトは、対応する Java オブジェクトをベースとします。

以下の表では、特に便利な変数を太字で強調表示しています。

| 変数名 | 説明 |
|---|---|
| **`component`** | `com.day.cq.wcm.api.components.Component` |
| `componentContext` | `com.day.cq.wcm.api.components.ComponentContext` |
| **`currentDesign`** | `com.day.cq.wcm.api.designer.Design` |
| `currentNode` | `javax.jcr.Node` |
| **`currentPage`** | `com.day.cq.wcm.api.Page` |
| `currentSession` | `javax.servlet.http.HttpSession` |
| `currentStyle` | `com.day.cq.wcm.api.designer.Style` |
| `designer` | `com.day.cq.wcm.api.designer.Designer` |
| `editContext` | `com.day.cq.wcm.api.components.EditContext` |
| `log` | `org.slf4j.Logger` |
| `out` | `java.io.PrintWriter` |
| `pageManager` | `com.day.cq.wcm.api.PageManager` |
| `reader` | `java.io.BufferedReader` |
| **`request`** | `org.apache.sling.api.SlingHttpServletRequest` |
| `resolver` | `org.apache.sling.api.resource.ResourceResolver` |
| **`resource`** | `org.apache.sling.api.resource.Resource` |
| **`resourceDesign`** | `com.day.cq.wcm.api.designer.Design` |
| **`resourcePage`** | `com.day.cq.wcm.api.Page` |
| `response` | `org.apache.sling.api.SlingHttpServletResponse` |
| `sling` | `org.apache.sling.api.scripting.SlingScriptHelper` |
| `slyWcmHelper` | `com.adobe.cq.sightly.WCMScriptHelper` |
| **`wcmmode`** | `com.adobe.cq.sightly.SightlyWCMMode` |
| `xssAPI` | `com.adobe.granite.xss.XSSAPI` |

## JavaScript ベースのオブジェクト {#javascript-backed-objects}

HTL ロジックを JavaScript でバックアップすることもできます。ただし、好ましい、推奨される方法は、[Sling モデル](https://sling.apache.org/documentation/bundles/models.html)を使用することです。

<!-- 

Comment Type: draft

<p> </p> 
<p>JS-specific context variables: These supply access to asynchronous implementations of all the Java objects listed below). To write HTL code that is portable to granite.js, you must use the variables provided by aem and sly, not the native Java variables.</p> 
<ul> 
 <li>wcm
  <ul> 
   <li>currentPage</li> 
   <li>nativePage: [com.day.cq.wcm.apiPage]</li> 
   <li>properties: {<i>enumerable</i>}</li> 
  </ul> </li> 
 <li>granite
  <ul> 
   <li>request
    <ul> 
     <li>parameters: {<i>enumerable</i>}</li> 
     <li>nativeRequest: [org.apache.sling.scripting.core.impl.helper.OnDemandReaderRequest]</li> 
     <li>pathInfo
      <ul> 
       <li>nativePathInfo: [SlingRequestPathInfo: path='/content/geometrixx/en/jcr:content/par/text', selectorString='null', extension='html', suffix='null']</li> 
      </ul> </li> 
    </ul> </li> 
   <li>resource
    <ul> 
     <li>nativeResource: [Paragraph, path=/content/geometrixx/en/jcr:content/par/text, type=wcm/foundation/components/text, cssClass=default, column=0/0, diffInfo=[null], resource=[JcrNodeResource, type=wcm/foundation/components/text, superType=null, path=/content/geometrixx/en/jcr:content/par/text]]</li> 
     <li>path: "/content/geometrixx/en/jcr:content/par/text"</li> 
     <li>properties: {sling:resourceType,jcr:created,jcr:lastModified,jcr:createdBy, textIsRich,jcr:lastModifiedBy,jcr:primaryType}</li> 
    </ul> </li> 
   <li>properties: {sling:resourceType,jcr:created,jcr:lastModified,jcr:createdBy, textIsRich,jcr:lastModifiedBy,jcr:primaryType}</li> 
  </ul> </li> 
</ul> 
<p>JS specific non-HTL related variables. Present due to JS-implementation. Generally not used in templating:</p> 
<ul> 
 <li>console: JS Object</li> 
 <li>exports: JS Object</li> 
 <li>module: JS Object</li> 
 <li>setImmediate: JS Function</li> 
 <li>setTimeout: JS Function</li> 
 <li>use: JS Function</li> 
</ul>
-->
