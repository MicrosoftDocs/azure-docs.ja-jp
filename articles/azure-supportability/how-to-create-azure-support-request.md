---
title: "Azure サポート要求を作成する方法 | Microsoft Docs"
description: "Azure サポート要求を作成する方法"
services: Azure Supportability
documentationcenter: 
author: ganganarayanan
manager: scotthit
editor: 
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.service: azure-supportability
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2016
ms.author: gangan
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 2a5da389d4e8120b604cb7a435ab30373bec2376


---
# <a name="how-to-create-an-azure-support-request"></a>Azure サポート要求を作成する方法
## <a name="summary"></a>概要
Azure ユーザーは、Azure Portal ([https://portal.azure.com](https://portal.azure.com)) でサポート要求を作成し、管理できます。

> [!NOTE]
> ドイツの Azure Portal は [https://portal.microsoftazure.de](https://portal.microsoftazure.de)、米国政府機関向け Azure Portal は [https://portal.azure.us](https://portal.azure.us) です。
> 
> 

お客様からいただいたご意見とご要望に基づき、サポート要求のシステムを改善しました。大きな目標として次の 3 つに取り組んでいます。

* **合理化**: クリックとブレードを減らし、サポート要求の送信プロセスを簡素化しました。
* **統合**: Azure リソースの問題を解決するとき、コンテキストを切り替えなくてもそのリソースのサポート要求を簡単に送信できるようにします。
* **効率的**: 問題を効率的に解決するためにサポート エンジニアが必要とする重要な情報を集めします。

## <a name="getting-started"></a>使用の開始
サポート要求は、上部にあるナビゲーション メニューから、あるいはリソース ブレードから直接、作成できます。

**上部のナビゲーション バーから**

![新しいサポート要求](./media/how-to-create-azure-support-request/NewSupportRequest.png)

**リソース ブレードから**

![コンテキストで](./media/how-to-create-azure-support-request/Incontext.png)

## <a name="basics"></a>基本
サポート要求プロセスの最初の手順は、ユーザーが抱える問題とそのサポート プランに必要な基本情報を集めることです。

例を見てみましょう。仮想マシンに技術的な問題が発生しました。ネットワークの接続に問題があるように思われます。
ウィザードの最初の手順でサービス ("Windows を実行している仮想マシン") とリソース (仮想マシンの名前) を選択すると、この問題の支援プロセスが開始します。

![[基本] ブレード](./media/how-to-create-azure-support-request/Basics.png)

> [!NOTE]
> Azure は、サブスクリプション管理 (請求、割り当て調整、アカウント振り替えなど) に無制限のサポートを提供します。 技術的なサポートについては、サポート プランが必要になります。 [サポート プランについて詳しく見る](https://azure.microsoft.com/support/plans)。
> 
> 

## <a name="problem"></a>問題点
ウィザードの 2 番目の手順で、問題に関する追加情報を集めます。 この手順で正確な情報を提供することで、最も適したサポート エンジニアに問題を送り、可能な限り速やかに診断を開始できます。

![[問題] ブレード](./media/how-to-create-azure-support-request/Problem.png)

上記の仮想マシンの接続の例を続けると、このフォームに入力し、ネットワークの接続問題であることを指定します。問題が発生したおおよその時間など、細かな情報も入力できます。

## <a name="related-help"></a>関連ヘルプ
一部の問題については、問題を解決するための関連ヘルプ リンクを提供します。 推奨された文書で問題を解決できない場合、このプロセスを続け、サポート要求を作成できます。
![関連ヘルプ](./media/how-to-create-azure-support-request/RelatedHelp.png)

## <a name="contact-information"></a>連絡先情報
ウィザードの最後の手順で、マイクロソフトがお客様に連絡するための連絡先情報を確認します。
![連絡先情報](./media/how-to-create-azure-support-request/ContactInformation.png)

問題の重大度によっては、マイクロソフトからお客様への連絡は営業時間内にするか、24x7 の応答を希望されるか伺うことがあります。24x7 の応答の場合、営業時間内に限らずご連絡いたします。
![連絡先情報 24x7](./media/how-to-create-azure-support-request/ContactInformation-2.png)

## <a name="manage-support-requests"></a>サポート要求を管理する
サポート要求を作成したら、 **[サポート要求の管理]** ページから詳細を確認できます。

**上部のナビゲーション バーから**

![[サポート要求を管理する] リンク](./media/how-to-create-azure-support-request/ManageSupportRequest-link.png)

**[サポート要求の管理]** ページでは、サポート要求とその状態をすべて確認できます。
![サポート要求を管理する](./media/how-to-create-azure-support-request/ManageSupportRequest.png)

サポート要求を選択すると、重大度と、サポート エンジニアの応答に必要な予想時間を含む、詳細が表示されます。
![VID](./media/how-to-create-azure-support-request/VID.png)

要求の重大度を変更する場合、 **[事業影響度]** タイルをクリックします。 上記の例では、要求が現在、重大度 C に設定されています。

タイルをクリックすると、重大度の一覧が表示されます。登録したサポート要求に割り当てることができます。

> [!NOTE]
> 最高重大度はサポート プランごとに異なります。 [サポート プランについて詳しく見る](https://azure.microsoft.com/support/plans)。
> 
> 

![VID-2](./media/how-to-create-azure-support-request/VID-2.png)

## <a name="feedback"></a>フィードバック
ご意見とご提案をお待ちしております。 あなたの [提案](https://feedback.azure.com/forums/266794-support-feedback)をお寄せください。 また、[Twitter](https://twitter.com/azuresupport) や [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure)でマイクロソフトの最新情報をご覧いただけます。

## <a name="learn-more"></a>詳細情報
[Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq)




<!--HONumber=Nov16_HO3-->


