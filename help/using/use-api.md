---
title: HTL Use-API
seo-title: アドビ HTL Use-API
description: HTL では、Java Use-API と JavaScript Use-API の 2 つの API を使用できます。
seo-description: アドビ HTL では、Java Use-API と JavaScript Use-API の 2 つの API を使用できます。
uuid: ab44aa5c-ce7e-40b9-97fb-e86c6a28405c
contentOwner: ユーザーは、
products: SG_EXPERIENCEMANAGER/HTL
topic-tags: html-template-language
content-type: リファレンス
discoiquuid: 89004426-eb59-4b63-913f-51bf98662773
mwpw-migration-script-version: 2017-10-12T21 46 58.665-0400
translation-type: tm+mt
source-git-commit: 271c355ae56e16e309853b02b8ef09f2ff971a2e

---


# HTL Use-API {#htl-use-api}

次の表は、それぞれの API の長所と短所を示しています。

|  | **Java Use-API** | **JavaScript Use-API** |
|--- |--- |--- |
| **長所** | <ul><li>速い</li><li>デバッガーで調査可能</li><li>単体テストが容易</li></ul> | <ul><li>フロントエンド開発者による変更が可能</li><li>コンポーネント内に配置されているので、コンポーネントのビューロジックを、該当するテンプレートの近くに保持できる</li></ul> |
| **短所** | <ul><li>フロントエンド開発者による変更が不可能</li></ul> | <ul><li>遅い</li><li>デバッガー未対応</li><li>単体テストが困難</li></ul> |


ページコンポーネントについては、すべてのモデルロジックが Java に配置され、ビュー（つまりコンポーネント内）での現象に依存しない明確な API を提供する、混合モデルを使用することをお勧めします。AEM には、ほとんどの場合に対応できる Page API や Resource API などのデフォルトモデルがあります。

コンポーネントに固有のすべてのビューロジックはコンポーネントに属するので、JavaScript としてそのコンポーネント内に配置される必要があります。
