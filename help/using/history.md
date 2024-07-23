---
title: HTL の履歴
description: AEM を長年使用しているユーザー向けに、このドキュメントでは HTL の背景、HTL を JSP に置き換える方法、Sightly からの名前の変更について説明します。
exl-id: 00985b35-2130-4946-959a-0a09a34a0f05
source-git-commit: c6bb6f0954ada866cec574d480b6ea5ac0b51a3f
workflow-type: tm+mt
source-wordcount: '540'
ht-degree: 41%

---


# HTL の履歴 {#history-of-htl}

AEM を長年使用しているユーザー向けに、このドキュメントでは HTL の背景、HTL を JSP に置き換える方法、Sightly からの名前の変更について説明します。

## 以前の名称：Sightly {#sightly}

HTML テンプレート言語（HTL）は、Adobe Experience Manager での HTML 用に、サーバーサイドで優先かつ推奨されるテンプレートシステムです。 以前のバージョンの AEM で使用されていた JSP（JavaServer Pages）の代わりに使用されます。

## JSP と HTL の対比 {#htl-over-jsp}

Adobeでは、新規AEM プロジェクトの場合は、HTMLテンプレート言語を使用することをお勧めします。 これは、JSP と比較して複数の利点があるからです。 ただし、既存のプロジェクトでは、今後数年、既存の JSP を維持するよりも HTL へ移行する方が作業が減ると予想される場合に限り、移行の意味があります。

HTL で記述されたコンポーネントは JSP または ESP で記述されたコンポーネントと互換性があるので、HTL への移行は必ずしもすべてが選択されるわけではありません。 つまり、既存のコンポーネント用の JSP を維持しながら、既存のプロジェクトで新しいコンポーネントに HTL を問題なく使用できます。

同じコンポーネント内でも、HTL ファイルを JSP および ESP と共に使用できます。次の例は、**1 行目** で JSP ファイルから HTL ファイルを含める方法を示し、**2 行目** で HTL ファイルから JSP ファイルを含める方法を示しています。

```xml
<cq:include script="template.html"/>
<sly data-sly-include="template.jsp"/>
```

## よくある質問 {#frequently-asked-questions}

HTL を初めて使用する経験豊富なAEM開発者は、一般的に次のような質問をします。

### HTL には JSP にない制限事項がありますか。 {#limitations}

HTL には JSP と比べた場合に制限事項はありません。つまり、JSP で実行できることは HTL でも実行できます。 ただし、HTL は設計上、いくつかの点で JSP よりも厳密です。 1 つの JSP ファイルで実現できることは、HTL で実現できるように Java クラスまたはJavaScript ファイルに分割する必要がある場合があります。 ただし、ロジックとマークアップの問題点を適切に分離できるように、このアプローチは一般的に望ましいと言えます。

### HTL では JSP タグライブラリはサポートされますか。 {#tag-libraries}

いいえ。ただし、「クライアントライブラリの読み込み [ の節で説明するように ](getting-started.md#loading-client-libraries)、template および call ステートメントが同様のパターンを提供する点は、はじめにのドキュメントも参照してください。

### HTL 機能を AEM プロジェクトで拡張できますか。 {#extended}

いいえ。HTL は、ロジック（[Use-API](#use-api-for-accessing-logic)）とマークアップ（template および call ステートメント）の再利用のために強力な拡張メカニズムを備えています。これを使用して、プロジェクトのコードをモジュール化できます。

### JSP と比べて HTL の最大のメリットは何ですか。 {#benefits}

最大のメリットはセキュリティとプロジェクトの効率性です。これらについては、[ 概要 ](overview.md) で詳しく説明します。

### JavaServer Pages （JSP）は使用されなくなりますか。 {#go-away}

いいえ。JSP を廃止する予定はありません。

## 名前にはどのような意味がありますか？ {#what-is-in-a-name}

AEM 6.0 および 6.1 では、HTL は **Sightly** と呼ばれていました。 Adobeは、仕様の目的を明確にし、Adobeの一般的な命名ガイドラインに合わせるために、名称を **HTMLテンプレート言語** または **HTL** に変更しました。 この名前の変更は 2016年8月（PT）から有効で、AEM バージョン 6.0 以降に適用されます。

>[!NOTE]
>
>この名前の変更はコードや API には影響しないので、互換性には影響しません。 詳しくは、[ このお知らせビデオを参照 ](https://helpx.adobe.com/jp/experience-manager/how-to/announce-htl.html) してください。

HTL について詳しくは、[HTMLテンプレート言語（HTL）の概要 ](overview.md) を参照してください。
