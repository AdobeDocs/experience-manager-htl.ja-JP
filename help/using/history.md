---
title: HTL の履歴
description: AEM を長年使用しているユーザー向けに、このドキュメントでは HTL の背景、HTL を JSP に置き換える方法、Sightly からの名前の変更について説明します。
exl-id: 00985b35-2130-4946-959a-0a09a34a0f05
source-git-commit: 88edbd2fd66de960460df5928a3b42846d32066b
workflow-type: ht
source-wordcount: '542'
ht-degree: 100%

---


# HTL の履歴 {#history-of-htl}

AEM を長年使用しているユーザー向けに、このドキュメントでは HTL の背景、HTL を JSP に置き換える方法、Sightly からの名前の変更について説明します。

## 以前の名称：Sightly {#sightly}

HTML テンプレート言語（HTL）は、Adobe Experience Manager での HTML 用に、サーバーサイドで優先かつ推奨されるテンプレートシステムです。 以前のバージョンの AEM で使用されていた JSP（JavaServer Pages）の代わりに使用されます。

## JSP と HTL の対比 {#htl-over-jsp}

HTML テンプレート言語には JSP と比較して多数のメリットがあるので、新しい AEM プロジェクトでは HTML テンプレート言語の使用をお勧めします。ただし、既存のプロジェクトでは、今後数年、既存の JSP を維持するよりも HTL へ移行する方が作業が減ると予想される場合に限り、移行の意味があります。

しかし、必ずしも HTL への移行だけが選択肢ではありません。HTL で記述されたコンポーネントには、JSP や ESP で記述されたコンポーネントと互換性があるからです。つまり、既存のプロジェクトで、新しいコンポーネントに対して問題なく HTL を使用しながら、既存のコンポーネントに対しては JSP を維持することができます。

同じコンポーネント内でも、HTL ファイルを JSP および ESP と共に使用できます。例えば、**行 1** では JSP ファイルで HTL ファイルがインクルードされ、**行 2**&#x200B;では HTL ファイルで JSP ファイルがインクルードされています。

```xml
<cq:include script="template.html"/>
<sly data-sly-include="template.jsp"/>
```

## よくある質問 {#frequently-asked-questions}

これらは、HTL を初めて使用する経験豊富な AEM 開発者からよく寄せられる質問です。

### HTL には JSP にない制限事項がありますか。 {#limitations}

HTL には JSP と比べた場合に制限事項はありません。つまり、JSP で実行できることは HTL でも実行できます。ただし、HTL は設計上、いくつかの点で JSP よりも厳密です。単一の JSP ファイルで実現できることは、HTL で実現できるように Java クラスまたは JavaScript ファイルに分割する必要がある場合があります。これは、ロジックとマークアップの問題点を適切に分離できるように一般的にも必要とされています。

### HTL では JSP タグライブラリはサポートされますか。 {#tag-libraries}

サポートされませんが、[クライアントライブラリの読み込み](getting-started.md#loading-client-libraries)の節で説明するように、template および call ステートメントが同様のパターンを提供します。

### HTL 機能を AEM プロジェクトで拡張できますか。 {#extended}

いいえ、できません。HTL は、ロジック（[Use-API](#use-api-for-accessing-logic)）とマークアップ（template および call ステートメント）の再利用のために強力な拡張メカニズムを備えています。これを使用して、プロジェクトのコードをモジュール化できます。

### JSP と比べて HTL の最大のメリットは何ですか。 {#benefits}

最大のメリットはセキュリティとプロジェクトの効率性です。これらについては、[概要](overview.md)で詳しく説明します。

### JSP はいずれ使用されなくなりますか。 {#go-away}

そうした計画はありません。

## 名前にはどのような意味がありますか？ {#what-is-in-a-name}

AEM 6.0 および 6.1 では、HTL は **Sightly** と呼ばれていました。アドビは、この仕様の目的を明確にし、アドビの一般的な命名ガイドラインに合わせるために、名称を **HTML テンプレート言語**&#x200B;または **HTL** に変更しました。この名前の変更は 2016年8月（PT）から有効で、AEM バージョン 6.0 以降に適用されます。

>[!NOTE]
>
>この名称変更はコードや API に関わるものではないので、互換性については影響はありません。詳しくは、[このお知らせビデオを参照してください。](https://helpx.adobe.com/jp/experience-manager/how-to/announce-htl.html)

HTL についての詳しい情報を確認して実際に使ってみるには、アドビ公式の [HTML テンプレート言語（HTL）使用の手引き](overview.md)を参照してください。
