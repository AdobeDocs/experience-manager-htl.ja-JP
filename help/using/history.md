---
title: HTL の履歴
description: AEM を長年使用しているユーザー向けに、このドキュメントでは HTL の背景、HTL を JSP に置き換える方法、Sightly からの名前の変更について説明します。
exl-id: 00985b35-2130-4946-959a-0a09a34a0f05
source-git-commit: addc69e4b4e56a9b1c5f91ce9af26fa2d326d981
workflow-type: tm+mt
source-wordcount: '530'
ht-degree: 96%

---


# HTL の履歴 {#history-of-htl}

AEM を長年使用しているユーザー向けに、このドキュメントでは HTL の背景、HTL を JSP に置き換える方法、Sightly からの名前の変更について説明します。

## 以前の名称：Sightly {#sightly}

HTML テンプレート言語（HTL）は、Adobe Experience Manager での HTML 用に、サーバーサイドで優先かつ推奨されるテンプレートシステムです。 以前のバージョンの AEM で使用されていた JSP（JavaServer Pages）の代わりに使用されます。

## JSP と HTL の対比 {#htl-over-jsp}

アドビでは、AEM の新規プロジェクトには HTML テンプレート言語を使用することをお勧めします。その理由は、JSP と比較して複数の利点があるからです。ただし、既存のプロジェクトでは、今後数年、既存の JSP を維持するよりも HTL へ移行する方が作業が減ると予想される場合に限り、移行の意味があります。

必ずしも HTL への移行だけが選択肢ではありません。HTL で記述されたコンポーネントには、JSP や ESP で記述されたコンポーネントと互換性があるからです。つまり、このアプローチでは、既存のコンポーネント用の JSP を維持しながら、既存のプロジェクトで新しいコンポーネントに HTL を問題なく使用できます。

同じコンポーネント内でも、HTL ファイルを JSP および ESP と共に使用できます。次の例の&#x200B;**行 1** では JSP ファイルで HTL ファイルがインクルードされ、**行 2** では HTL ファイルで JSP ファイルがインクルードされています。

```xml
<cq:include script="template.html"/>
<sly data-sly-include="template.jsp"/>
```

## よくある質問 {#frequently-asked-questions}

HTL を初めて使用する経験豊富な AEM 開発者によくある質問は次のとおりです。

### HTL には JSP にない制限事項がありますか？ {#limitations}

HTL には JSP と比べた場合に制限事項がありません。つまり、JSP で実行できることは HTL でも実行できます。ただし、HTL は設計上、いくつかの点で JSP よりも制限があります。1 つの JSP ファイルで実現できることを HTL で実現するには、Java クラスまたは JavaScript ファイルに分割する必要がある場合があります。ただし、このアプローチはロジックとマークアップの問題点を適切に分離できるように一般的にも必要とされています。

### HTL では JSP タグライブラリはサポートされますか？ {#tag-libraries}

いいえ。サポートされませんが、「はじめに」のドキュメントの[クライアントライブラリの読み込み](getting-started.md#loading-client-libraries)の節で説明するように、template および call ステートメントが同様のパターンを提供します。

### HTL 機能を AEM プロジェクトで拡張できますか？ {#extended}

いいえ。HTL は、ロジック（[Use-API](#use-api-for-accessing-logic)）とマークアップ（template および call ステートメント）の再利用のために強力な拡張メカニズムを備えています。これを使用して、プロジェクトのコードをモジュール化できます。

### JSP と比べて HTL の最大のメリットは何ですか。 {#benefits}

最大のメリットはセキュリティとプロジェクトの効率性です。これらについて詳しくはは、[概要](overview.md)を参照してください。

### JavaServer Pages（JSP）は廃止されますか？ {#go-away}

いいえ。JSP を廃止する予定はありません。

## 名前にはどのような意味がありますか？ {#what-is-in-a-name}

AEM 6.0 および 6.1 では、HTL は **Sightly** と呼ばれていました。アドビは、この仕様の目的を明確にし、アドビの一般的な命名ガイドラインに合わせるために、名称を **HTML テンプレート言語**&#x200B;または **HTL** に変更しました。この名前の変更は 2016年8月（PT）から有効で、AEM バージョン 6.0 以降に適用されます。

>[!NOTE]
>
>この名前の変更はコードや API には影響しないので、互換性には影響しません。

<!-- LINK IS 404
For more information, watch [this announcement video](https://helpx.adobe.com/experience-manager/how-to/announce-htl.html). -->

HTL について詳しくは、[HTML テンプレート言語（HTL）使用の手引き](overview.md)を参照してください。
