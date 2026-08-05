---
solution: Experience Manager
type: Documentation
git-repo: https://github.com/AdobeDocs/experience-manager-htl.ja-JP
index: true
landing-page-name: experience-manager
landing-page-breadcrumb-title: AEM
recommendations: noDisplay
product_v2:
  - id: fd1f54a9-f50c-467d-8956-cebbaf4f3eb8
usetq: true
source-git-commit: d9500886a302eafb90cb5ece6dec09849acd7ae2
workflow-type: tm+mt
source-wordcount: 86
ht-degree: 2%

---


# 内部使用のためのメタデータ

GitHub オーサリングシステムは、メタデータを階層的に定義し、以下に示すように前例を増やします。

1. metadata.md
1. ToC
1. 記事

metadata.md ファイルで定義されたメタデータは、リポジトリ全体に適用されますが、ToC レベルとアーティクルレベルで上書きできます。 メタデータの上書きは、可能な限り低いレベルで行う必要があります。

`experience-manager-core-components.en` リポジトリ内のメタデータは、必要最小限です。

metadata.md

* `product`
* `git-repo`
* `index: true`

使用されなくなった場合：

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

