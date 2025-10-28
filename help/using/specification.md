---
title: HTL の仕様
description: 詳細な構文情報については、HTL の仕様を参照してください。
exl-id: c0657476-4db6-4fad-ad87-9252b5003237
index: false
source-git-commit: 391c5279f0021dbedaffb0c63e67e037d6c782e1
workflow-type: tm+mt
source-wordcount: '135'
ht-degree: 85%

---


# HTL の仕様 {#htl-specification}

HTML テンプレート言語（HTL）は、HTMLのサーバーサイドテンプレートシステムとして推奨されています。

## HTL レイヤー {#layers}

AEM では、レイヤーの数によって HTL を定義できます。

1. **[HTL の仕様](https://github.com/adobe/htl-spec)** - HTL は、オープンソースの、プラットフォームに依存しない仕様で、誰でも自由に実装できます。仕様は GitHub リポジトリで管理されています。
1. **[Sling HTL スクリプトエンジン](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html)** - `Sling` プロジェクトは、AEM で使用される HTL の参照実装を作成しました。 `Sling` プロジェクトは、ドキュメントを保持しています。
1. **[AEM Extensions](https://experienceleague.adobe.com/ja/docs/experience-manager-htl/content/aem-extensions)** - AEMは `Sling` HTL スクリプティングエンジン上に構築され、AEM固有の便利な機能を開発者に提供します。 これらの拡張機能は、このドキュメントセットの一部としてドキュメントに記載されています。

上記のリンクから、AEM で使用する HTL のすべてのレイヤーに関する専用ドキュメントへ移動します。
