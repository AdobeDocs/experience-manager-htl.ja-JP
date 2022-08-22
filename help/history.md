---
title: HTL の履歴
description: AEMの長い期間のユーザーに対しては、このドキュメントでは、HTL の背景、JSP の置き換え方法、Sightly からの名前の変更方法を示します。
exl-id: 00985b35-2130-4946-959a-0a09a34a0f05
source-git-commit: 5ab1275c984135fe946f36905bbc979cf19edd80
workflow-type: tm+mt
source-wordcount: '542'
ht-degree: 50%

---


# HTL の履歴 {#history-of-htl}

AEMの長い期間のユーザーに対しては、このドキュメントでは、HTL の背景、JSP の置き換え方法、Sightly からの名前の変更方法を示します。

## 以前の名称：Sightly {#sightly}

HTMLテンプレート言語 (HTL) は、Adobe Experience ManagerでのHTML用に、サーバー側で推奨される推奨テンプレートシステムです。 以前のバージョンの AEM で使用されていた JSP（JavaServer Pages）の代わりに使用されます。

## JSP と HTL の対比 {#htl-over-jsp}

HTML テンプレート言語には JSP と比較して多数のメリットがあるので、新しい AEM プロジェクトでは HTML テンプレート言語の使用をお勧めします。ただし、既存のプロジェクトでは、今後数年、既存の JSP を維持するよりも HTL へ移行する方が作業が減ると予想される場合に限り、移行の意味があります。

しかし、必ずしも HTL への移行だけが選択肢ではありません。HTL で記述されたコンポーネントには、JSP や ESP で記述されたコンポーネントと互換性があるからです。つまり、既存のプロジェクトで、新しいコンポーネントに対して問題なく HTL を使用しながら、既存のコンポーネントに対しては JSP を維持することができます。

同じコンポーネント内でも、HTL ファイルを JSP および ESP と共に使用できます。例えば、**行 1** では JSP ファイルで HTL ファイルがインクルードされ、**行 2**&#x200B;では HTL ファイルで JSP ファイルがインクルードされています。

```xml
<cq:include script="template.html"/>
<sly data-sly-include="template.jsp"/>
```

## よくある質問 {#frequently-asked-questions}

これらは、経験豊富なAEM開発者が HTL を初めて使用する場合によく寄せられる質問です。

### HTL には JSP にない制限事項がありますか。 {#limitations}

HTL には JSP と比べた場合に制限事項はありません。つまり、JSP で実行できることは HTL でも実行できます。ただし、HTL はいくつかの点で JSP よりも厳密に設計されています。1 つの JSP ファイルですべて実行できる処理は、HTL で実行するには、Java クラスまたは JavaScript ファイルに分割する必要がある場合があります。 これは、ロジックとマークアップの問題点を適切に分離できるように一般的にも必要とされています。

### HTL では JSP タグライブラリはサポートされますか。 {#tag-libraries}

いいえ。ただし、 [クライアントライブラリを読み込み中](getting-started.md#loading-client-libraries) 「はじめに」ドキュメントの節では、 template および call ステートメントによって同様のパターンが提供されています。

### HTL 機能を AEM プロジェクトで拡張できますか。 {#extended}

いいえ、できません。HTL は、ロジック ( [Use-API](#use-api-for-accessing-logic)) および（ template および call ステートメント）の両方を使用します。これらは、プロジェクトのコードをモジュール化するために使用できます。

### JSP と比べて HTL の最大のメリットは何ですか。 {#benefits}

主なメリットはセキュリティとプロジェクトの効率性です。これらについては、 [概要。](overview.md)

### JSP はいずれ使用されなくなりますか。 {#go-away}

この線に沿っては予定がありません。

## 名前は？ {#what-is-in-a-name}

AEM 6.0 および 6.1 では、HTL は **Sightly**. Adobe名をに変更しました。 **HTMLテンプレート言語** または **HTL** の仕様を明確にし、Adobeの命名ガイドラインに従って調整する。 この名前の変更は 2016年8月（PT）から有効で、AEM バージョン 6.0 以降に適用されます。

>[!NOTE]
>
>この名称変更はコードや API に関わるものではないので、互換性については影響はありません。詳しくは、 [このお知らせビデオを参照してください。](https://helpx.adobe.com/jp/experience-manager/how-to/announce-htl.html)

HTL の詳細を確認して、最初に重要な場所を見つけるには、アドビの公式 [HTMLテンプレート言語 (HTL) 使用の手引き](overview.md)
