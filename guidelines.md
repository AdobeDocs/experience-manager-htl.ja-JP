---
source-git-commit: f09b2268dc4745a8c2e3c53bb20fa3d35f6a7c2b
translation-type: tm+mt

---
# AEM ドキュメントへの貢献に関するガイドライン

## AEM ドキュメントの理念

Adobe Experience Manager ユーザーは、競争の厳しい環境でビジネスを展開しており、競合他社より優位に立つためのデジタルエクスペリエンスを構築しようと懸命に努力しています。したがって、アドビが AEM で高度な新しいツールを提供する際には、お客様が AEM への投資をすぐに活かし ROI を最大化できるような正確かつ明快なドキュメントで、これらのツールが補完されることが不可欠です。

AEM ドキュメントの目標は、ドキュメントをできるだけ早く AEM ユーザーに届けることです。そのため、正確で使いやすいドキュメントを最優先し、ドキュメントの継続的な更新と改善に努めます。

## AEM ドキュメントへの貢献

AEM ドキュメントを継続的に改善するため、AEM ユーザーコミュニティ全体からドキュメントに貢献いただければ幸いです。変更修正依頼（プル要求）であるか問題報告（イシュー）であるかを問わず、修正、明確化、増補、例の追加などがドキュメントの改善になります。

## ドキュメント標準

ドキュメントへの貢献度を歓迎しますが、AEMドキュメントへの貢献度は、プル要求または問題の形で、貢献度およびドキュメントの基準に準拠する必要があります。

これらの標準に合致しない貢献は拒否される可能性があります。

### 標準的な使用例について記載します。

AEM ドキュメントでは標準的な使用例を扱っています。製品の標準的なインストールや使用の範囲を超える使用例は、AEM ドキュメントに含まれていません。

### 一般的に、ドキュメントのバグや対処方法はありません。

AEM ドキュメントでは標準的な使用例を扱っています。この理由から、バグ、バグによる影響、バグの回避策は、一般的にはドキュメントに記載されていません。

このルールの例外は、AEM製品管理で承認された可能性のある解決策と共に既知の問題を記載できるリリースノートに適用されます。

### ドキュメントへの貢献は、技術的な質問に回答するためのものではありません。

AEM ドキュメントの改善に役立つアイデアは、貢献として歓迎します。ただし、コメント、問題報告および変更修正依頼は、*貢献*&#x200B;のみを目的としています。AEMの使用方法、AEMプロジェクトの実装方法、または技術的な問題の解決方法に関する質問に答えるために使用されるものではありません。

Any questions about the usage of AEM or technical errors you may have should be reported through the normal support process via the [Experience Manager Support Portal](https://daycare.day.com/home.html) or discussed in the [Experience Manager community](http://help-forums.adobe.com/content/adobeforums/en/experience-manager-forum/adobe-experience-manager.html).

***AEM ドキュメントへの貢献は、アドビサポートの代わりとなるものではありません。***&#x200B;サポート関連の質問に対する回答を求めるような貢献は、拒否されます。

### 貢献では、影響を受けるドキュメントページを明確に参照する必要があります。

ドキュメントの改善を提案する問題報告を作成する場合は、影響を受けるページへのリンクを含める必要があります。ドキュメントページで「**このページを編集**」リンクを使用して問題報告を作成すると、その問題報告は自動的に該当ページへのリンク付きで作成されます。

プルリクエストは影響を受けるページを参照するので、プルリクエストには適用されません。

## ドキュメントガイドライン

アドビのドキュメントに貢献いただく場合は、特定のスタイルガイドラインに従ってください。

これらのガイドラインに従っていただくと、貢献のレビューが容易になり、貢献がアドビのドキュメントにすばやく統合されるようになります。

### 言葉遣いとスタイル

#### 言葉遣い

* AEM ドキュメントは米国英語で作成および維持管理されています。
* 文章をできるだけ簡単にしてください。
* 言葉遣いを明快かつ簡潔にしてください。

AEM ドキュメントの読者は世界中におり、英語を母国語としていたり流暢に使えるとは限りません。口語的な表現を避け、言葉遣いをできるだけ明快で簡潔なものにしてください。

#### マイクロソフトのスタイルガイドへの準拠

[Microsoft Manual of Styleは、ソフトウェアに関するドキュメントに重点を置いた無料のドキュメントスタイルガイドです。AEMのドキュメントは、可能な限りこのガイドに従っています。](https://docs.microsoft.com/en-us/style-guide/welcome/)

### 書式設定

| 項目 | スタイル |
|---|---|
| UI 要素またはオプション | **太字** |
| ファイル名、パス、ユーザー入力、パラメーター値 | `monospaced` |
| コード、コマンドライン | ```Code Block``` |

### スクリーンショット

スクリーンショットは慎重に、かつ、テキストによる説明が不十分な場合にのみ使用します。

マーカーやその他の注釈（赤枠、矢印、テキストなど）はスクリーンショットに対して使用しないでください。こうすれば、スクリーンショットをドキュメントのローカライズ版で再利用または複製しやすくなります。

### バージョン固有の参照

ドキュメントコンテンツ全体で特定バージョンへの直接参照をできるだけ避けてください。これにより、今後のバージョンについて、ドキュメントの柔軟性と拡張性が高まります。

### 呼称としての Day、AEM、CQ、CRX の使用

ドキュメントでの初出時には必ず、本製品をフルネームである **Adobe Experience Manager** と記載します。その後は **AEM** と記載してもかまいません。

Day、Day Software、CQ、CRX は、クラス名や AEM の歴史に言及する場合など、やむを得ない場合を除き、使用しないでください。