---
solution: Experience Manager
type: Documentation
product: adobe experience manager
git-repo: https://github.com/AdobeDocs/experience-manager-htl.ja-JP
index: y
recommendations: noDisplay
source-git-commit: 22f62868df0fcfc558e5d62434dde843a9f3ca83
workflow-type: tm+mt
source-wordcount: '81'
ht-degree: 40%

---


# 内部使用メタデータ

GitHub オーサリングシステムでは、メタデータを階層的に定義し、次に示すように前例のレベルを増やしています。

1. metadata.md
1. 目次
1. 記事

metadata.md ファイルで定義されたメタデータはリポジトリ全体に適用されますが、目次と記事のレベルで上書きできます。 メタデータの上書きは、可能な限り低いレベルで行う必要があります。

最低限必要なのは、`experience-manager-core-components.en` リポジトリ内のメタデータです。

metadata.md

* `product`
* `git-repo`
* `index: y`

廃止：

* `solution-title`
* `solution-hub-url`
* `getting-started-title`
* `getting-started-url`
* `tutorials-title`
* `tutorials-url`

目次

* `sub-product`
* `user-guide-title`

記事

* `title`
* `description`
* `index: n`（以前のバージョンのコンポーネントのみ）

