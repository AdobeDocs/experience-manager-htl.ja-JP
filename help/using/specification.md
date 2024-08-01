---
title: HTL の仕様
description: 詳細な構文情報については、HTL の仕様を参照してください。
exl-id: c0657476-4db6-4fad-ad87-9252b5003237
source-git-commit: c6bb6f0954ada866cec574d480b6ea5ac0b51a3f
workflow-type: ht
source-wordcount: '138'
ht-degree: 100%

---


# HTL の仕様 {#htl-specification}

HTML テンプレート言語（HTL）は、HTMLのサーバーサイドテンプレートシステムとして推奨されています。

## HTL レイヤー {#layers}

AEM では、レイヤーの数によって HTL を定義できます。

1. **[HTL の仕様](https://github.com/adobe/htl-spec)** - HTL は、オープンソースの、プラットフォームに依存しない仕様で、誰でも自由に実装できます。仕様は GitHub リポジトリで管理されています。
1. **[Sling HTL スクリプティングエンジン](https://sling.apache.org/documentation/bundles/scripting/scripting-htl.html)** - Sling プロジェクトは、AEMが使用する HTL の参照実装を作成しました。Sling プロジェクトは、ドキュメントを保持しています。
1. **[AEM 拡張機能](aem-extensions.md)** - AEM は Sling HTL スクリプティングエンジン上に構築され、AEM に固有の便利な機能を開発者に提供します。これらの拡張機能は、このドキュメントセットの一部としてドキュメントに記載されています。

上記のリンクから、AEM で使用する HTL のすべてのレイヤーに関する専用ドキュメントへ移動します。
