---
title: HTL Use-API
description: HTL では、Java Use-API と JavaScript Use-API の 2 つの API を使用できます。
translation-type: tm+mt
source-git-commit: f7e46aaac2a4b51d7fa131ef46692ba6be58d878
workflow-type: tm+mt
source-wordcount: '183'
ht-degree: 55%

---


# HTL Use-API {#htl-use-api}

HTLは、ビジネスロジックにマークアップを混ぜないことで、懸念の分離を奨励しています。 ビジネスロジックはUse-APIを通じて実装できます。

次の表に、各APIのメリットとデメリットの概要を示します。

|  | **Java Use-API** | **JavaScript Use-API** |
|--- |--- |--- |
| **メリット** | <ul><li>高速</li><li>デバッガを使用して検査できる</li><li>単体テストが容易</li></ul> | <ul><li>フロントエンド開発者が変更可能</li><li>コンポーネント内に配置され、コンポーネントの表示ロジックを対応するテンプレートに近づけます</li></ul> |
| **デメリット** | <ul><li>フロントエンド開発者は変更できません</li></ul> | <ul><li>低速</li><li>デバッガはありません（まだ）</li><li>単体テストが難しい</li></ul> |

ページコンポーネントについては、すべてのモデルロジックが Java に配置され、ビュー（つまりコンポーネント内）での現象に依存しない明確な API を提供する、混合モデルを使用することをお勧めします。AEM には、ほとんどの場合に対応できる Page API や Resource API などのデフォルトモデルがあります。

コンポーネントに固有のすべてのビューロジックはコンポーネントに属するので、JavaScript としてそのコンポーネント内に配置される必要があります。
