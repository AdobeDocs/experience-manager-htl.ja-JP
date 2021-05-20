---
title: HTL Use-API
description: HTL では、Java Use-API と JavaScript Use-API の 2 つの API を使用できます。
exl-id: 8f95707e-952c-4efe-a44e-9a1ad501605e
source-git-commit: 8e70ee4921a7ea071ab7e06947824c371f4013d8
workflow-type: tm+mt
source-wordcount: '183'
ht-degree: 55%

---

# HTL Use-API {#htl-use-api}

HTLでは、ビジネスロジックとマークアップの混在を許可しないことで、懸念事項を分離することを推奨しています。 ビジネスロジックはUse-APIを使用して実装できます。

次の表に、各APIのメリットとデメリットの概要を示します。

|  | **Java Use-API** | **JavaScript Use-API** |
|--- |--- |--- |
| **メリット** | <ul><li>高速</li><li>デバッガーで検査可能</li><li>単体テストが容易</li></ul> | <ul><li>フロントエンド開発者が変更可能</li><li>コンポーネント内に配置され、コンポーネントのビューロジックを対応するテンプレートに近づけます。</li></ul> |
| **デメリット** | <ul><li>フロントエンド開発者は変更できない</li></ul> | <ul><li>遅い</li><li>デバッガーなし（まだ）</li><li>単体テストが困難</li></ul> |

ページコンポーネントについては、すべてのモデルロジックが Java に配置され、ビュー（つまりコンポーネント内）での現象に依存しない明確な API を提供する、混合モデルを使用することをお勧めします。AEM には、ほとんどの場合に対応できる Page API や Resource API などのデフォルトモデルがあります。

コンポーネントに固有のすべてのビューロジックはコンポーネントに属するので、JavaScript としてそのコンポーネント内に配置される必要があります。
