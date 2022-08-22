---
title: HTL の仕様
description: 詳細な構文情報については、 HTL の仕様を参照してください。
exl-id: c0657476-4db6-4fad-ad87-9252b5003237
source-git-commit: 942c5249cc38e49a7d617ba4cf65a878f2e229ab
workflow-type: tm+mt
source-wordcount: '152'
ht-degree: 6%

---


# HTL の仕様 {#htl-specification}

HTMLテンプレート言語 (HTL) は、HTML用のサーバーサイトテンプレートシステムとして推奨される、推奨される方式です。

## HTL レイヤー {#layers}

AEMで使用される HTL は、複数のレイヤーで定義できます。

1. **[HTL の仕様](https://github.com/adobe/htl-spec)** - HTL は、オープンソースの、プラットフォームに依存しない仕様で、誰でも自由に実装できます。 仕様は GitHub リポジトリで管理されています。
1. **[Sling HTL スクリプティングエンジン](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html)** - Sling プロジェクトは、AEMで使用される HTL の参照実装を作成しました。 ドキュメントは Sling プロジェクトによって管理されています。
1. **[AEM Extensions](aem-extensions.md)** - AEMは Sling HTL スクリプティングエンジン上に構築され、AEM専用の便利な機能を開発者に提供します。 これらの拡張機能は、このドキュメントセットの一部としてドキュメントに記載されています。

上記のリンクから、AEMで使用する HTL のすべてのレイヤーに関する専用ドキュメントへ移動します。
