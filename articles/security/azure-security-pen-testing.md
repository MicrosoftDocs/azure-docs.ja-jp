---
title: "侵入テスト | Microsoft Docs"
description: "この記事では、侵入テスト プロセスの概要と、Azure インフラストラクチャで実行されているアプリに対して侵入テストを行う方法について提供します。"
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 5d58d9dd6c92c81328f89a703b3bc8529aff8dc0


---
# <a name="pen-testing"></a>侵入テスト
アプリケーションのテストとデプロイに Microsoft Azure を使用することの優れた点の 1 つは、アプリケーションを開発、テストおよびデプロイするために、オンプレミスのインフラストラクチャをまとめる必要がないことです。 すべてのインフラストラクチャが、Microsoft Azure Platform サービスで処理されます。 オンプレミスのハードウェアの要求、取得、および “ラックとスタック” に関して心配する必要はありません。

これは非常に良いことですが、通常のセキュリティで適切な注意を払う必要はあります。 実行する必要のある操作の 1 つは、Azure にデプロイするアプリケーションに対する侵入テストです。

Microsoft が [Azure 環境の侵入テスト](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)を実行していることは、既にご存知かもしれません。 これは、セキュリティ コントロールの向上、新しいセキュリティ コントロールの導入、セキュリティ プロセスの向上という観点から、プラットフォームを向上し、アクションをガイドするために役立ちます。

お客様のためにアプリケーションの侵入テストを行うことはありませんが、お客様のアプリケーションに侵入テストを実行する必要があることは理解しています。 お客様のアプリケーションのセキュリティを強化するときに、Azure エコシステム全体のセキュリティ保護を高めることができるため、これは良いことです。

お客様がアプリケーションの侵入テストを行うと、Microsoft に対して攻撃しているように見える可能性があります。 攻撃パターンを [継続的に監視し](http://blogs.msdn.com/b/azuresecurity/archive/2015/07/05/best-practices-to-protect-your-azure-deployment-against-cloud-drive-by-attacks.aspx) 、必要に応じて、インシデントへの対応プロセスを開始することになります。 お客様が適切な注意を払っている侵入テストが原因で、インシデントへの対応プロセスをトリガーした場合、お客様の役に立つことはなく、Microsoft の役にも立ちません。

どうすればよいでしょうか。

Azure でホストされるアプリケーションの侵入テストを行う準備ができたら、Microsoft に連絡する必要があります。 お客様が特定のテストを実行する予定であることを把握すると、テストが Azure 侵入テストの契約条件に適合している限り、不注意でシャットダウンすることはありません (テストを実行している IP アドレスをブロックするなど)。
実行できる標準テストは、次のとおりです。

*  [Open Web Application Security Project (OWASP) の上位 10 の脆弱性](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [ファジー テスト](https://blogs.microsoft.com/cybertrust/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) 
* [ポートのスキャン](https://en.wikipedia.org/wiki/Port_scanner) 

実行できないテストの種類の 1 つは、 [サービス拒否 (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) 攻撃です。 これには、Dos 攻撃自体を開始したり、DoS 攻撃の種類の判断、デモンストレーション、またはシミュレートを実行する可能性がある関連テストを実行したりすることも含まれます。

Microsoft Azure でホストされているアプリケーションの侵入テストを行う準備はできていますか? その場合は、「 [侵入テストの概要](https://security-forms.azure.com/penetration-testing/terms) 」ページに進みます (また、ページの下部にある [テスト要求を作成] ボタンをクリックします)。 また、侵入テストの契約条件の詳細と、Azure やその他の Microsoft サービスに関連するセキュリティの不備を報告する方法に関する役立つリンクを見つけることもできます。



<!--HONumber=Nov16_HO3-->


