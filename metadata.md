---
solution: Experience Manager
type: Documentation
product: adobe experience manager
git-repo: https://github.com/AdobeDocs/experience-manager-htl.ja-JP
index: true
landing-page-name: experience-manager
landing-page-breadcrumb-title: AEM
recommendations: noDisplay
source-git-commit: 944fa924e7ccba0a195b2c92584ab75df86b1f83
workflow-type: tm+mt
source-wordcount: '86'
ht-degree: 2%

---


# 内部使用のメタデータ

GitHub オーサリングシステムでは、メタデータを階層的に定義し、次に示すように前例のレベルを増やしています。

1. metadata.md
1. から C
1. 記事

metadata.md ファイルで定義されたメタデータはリポジトリ全体に適用されますが、目次と記事のレベルで上書きできます。 メタデータの上書きは、できるだけ低いレベルで行う必要があります。

最低限必要なのは、`experience-manager-core-components.en` リポジトリ内のメタデータです。

metadata.md

* `product`
* `git-repo`
* `index: true`

廃止：

* `solution-title`
* `solution-hub-url`
* `getting-started-title`
* `getting-started-url`
* `tutorials-title`
* `tutorials-url`

ToCs

* `sub-product`
* `user-guide-title`

記事

* `title`
* `description`
* `index: false` （以前のバージョンのコンポーネントのみ）

