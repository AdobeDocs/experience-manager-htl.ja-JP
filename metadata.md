---
solution: Experience Manager
type: Documentation
product: adobe experience manager
git-repo: https://github.com/AdobeDocs/experience-manager-htl.ja-JP
index: y
source-git-commit: 89b9e89254f341e74f1a5a7b99735d2e69c8a91e
workflow-type: ht
source-wordcount: '107'
ht-degree: 100%

---


# 内部使用メタデータ

GitHub オーサリングシステムのメタデータは階層的で、次の増加する先例レベルが定義されます。

1. metadata.md
1. 目次
1. 記事

metadata.md ファイルで定義されたメタデータはリポジトリ全体に適用されますが、目次と記事のレベルで上書きできます。メタデータの上書きは、可能な限り低いレベルで行う必要があります。

最低限必要なのは、experience-manager-core-components.en リポジトリ内のメタデータです。

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

メタデータに関する追加情報は、[内部オーサリングガイド](https://experienceleague.adobe.com/docs/authoring-guide-exl/using/authoring/features/metadata.html?lang=ja#solution)を参照してください。
