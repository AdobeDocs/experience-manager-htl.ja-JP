---
title: HTL の仕様
description: 詳細な構文情報については、HTL の仕様を参照してください。
exl-id: c0657476-4db6-4fad-ad87-9252b5003237
source-git-commit: addc69e4b4e56a9b1c5f91ce9af26fa2d326d981
workflow-type: tm+mt
source-wordcount: '134'
ht-degree: 62%

---


# HTL の仕様 {#htl-specification}

HTML テンプレート言語（HTL）は、HTMLのサーバーサイドテンプレートシステムとして推奨されています。

## HTL レイヤー {#layers}

AEM では、レイヤーの数によって HTL を定義できます。

1. **[HTL の仕様](https://github.com/adobe/htl-spec)** - HTL は、オープンソースの、プラットフォームに依存しない仕様で、誰でも自由に実装できます。仕様は GitHub リポジトリで管理されています。
1. **[Sling HTL スクリプティングエンジン ](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html)** - `Sling` プロジェクトは、AEMが使用する HTL の参照実装を作成しました。 `Sling` プロジェクトは、そのドキュメントを維持します。
1. **[AEM Extensions](aem-extensions.md)** - AEMは `Sling` HTL スクリプティングエンジン上に構築され、AEM固有の便利な機能を開発者に提供します。 これらの拡張機能は、このドキュメントセットの一部としてドキュメントに記載されています。

上記のリンクから、AEM で使用する HTL のすべてのレイヤーに関する専用ドキュメントへ移動します。
