---
title: Azure Automation で使用される Azure データセンターの DNS レコード | Microsoft Docs
description: この記事では、その Automation アカウントをホストしている特定の Azure リージョンへの通信を制限するときに Azure Automation 機能に必要とされる DNS レコードについて説明します。
services: automation
ms.subservice: process-automation
ms.date: 11/25/2020
ms.topic: conceptual
ms.openlocfilehash: 8630afa7410aad81a7a3c61540fc74702fc6481c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100576002"
---
# <a name="dns-records-for-azure-regions-used-by-azure-automation"></a>Azure Automation によって使用される Azure リージョンの DNS レコード

[Azure Automation](../automation-intro.md) サービスでは、機能がサービスに接続するために多数の DNS レコードが使用されます。 特定のリージョンに対して定義された Automation アカウントがある場合は、そのリージョン データセンターへの通信を制限できます。 ファイアウォールの背後にホストされた次の Automation の機能が動作するには、それらのレコードを把握しておく必要があります。

* Hybrid Runbook Worker
* 状態の構成
* Webhooks

>[!NOTE]
>新しいレコードでは、バージョン 1.6.10.2 以上でない限り、Linux Hybrid Runbook Worker の登録が失敗します。 マシンが更新されたバージョンの worker ロールを受信し、それらの新しいレコードを使用するには、より新しいバージョンの [Linux 用の Log Analytics エージェント](../../azure-monitor/agents/agent-linux.md)にアップグレードする必要があります。 既存のマシンは、引き続き問題なく動作します。  

## <a name="dns-records-per-region"></a>リージョンあたりの DNS レコード数

次の表は、リージョンごとの DNS レコードを示しています。

>[!NOTE]
>ここで提供されている Automation DNS レコードの一覧は廃止されていますが、「[Private Link のサポート](#support-for-private-link)」の下にリストされている新しいレコードに移行し、自動化プロセスのエラーを回避するための時間的猶予を与えるために、引き続き機能します。

| **リージョン** | **DNS レコード** |
| --- | --- |
| オーストラリア中部 |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| オーストラリア東部 |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| オーストラリア東南部 |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| カナダ中部 |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| インド中部 |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| 米国東部 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| 東日本 |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| 北ヨーロッパ |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| 米国中南部 |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| 東南アジア |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| 英国南部 | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov バージニア州 | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |
| 米国中西部 | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| 西ヨーロッパ |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| 米国西部 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |

### <a name="support-for-private-link"></a>Private Link のサポート

Azure Automation で [Private Link](../../private-link/private-link-overview.md) をサポートするために、サポートされているすべてのデータセンターの DNS レコードが更新されました。 リージョン固有の URL の代わりに、次の URL は Automation アカウントに固有です。

| **リージョン** | **DNS レコード** |
| --- | --- |
| 米国中西部 |`https://<accountId>.webhook.wcus.azure-automation.net`<br>`https://<accountId>.agentsvc.wcus.azure-automation.net`<br>`https://<accountId>.jrds.wcus.azure-automation.net` |
| 米国西部 |`https://<accountId>.webhook.wus.azure-automation.net`<br>`https://<accountId>.agentsvc.wus.azure-automation.net`<br>`https://<accountId>.jrds.wus.azure-automation.net` |
| 米国西部 2 |`https://<accountId>.webhook.wus2.azure-automation.net`<br>`https://<accountId>.agentsvc.wus2.azure-automation.net`<br>`https://<accountId>.jrds.wus2.azure-automation.net` |
| 米国中部 |`https://<accountId>.webhook.cus.azure-automation.net`<br>`https://<accountId>.agentsvc.cus.azure-automation.net`<br>`https://<accountId>.jrds.cus.azure-automation.net` |
| 米国中南部 |`https://<accountId>.webhook.scus.azure-automation.net`<br>`https://<accountId>.agentsvc.scus.azure-automation.net`<br>`https://<accountId>.jrds.scus.azure-automation.net` |
| 米国中北部 |`https://<accountId>.webhook.ncus.azure-automation.net`<br>`https://<accountId>.agentsvc.ncus.azure-automation.net`<br>`https://<accountId>.jrds.ncus.azure-automation.net` |
| 米国東部 |`https://<accountId>.webhook.eus.azure-automation.net`<br>`https://<accountId>.agentsvc.eus.azure-automation.net`<br>`https://<accountId>.jrds.eus.azure-automation.net` |
| 米国東部 2 |`https://<accountId>.webhook.eus2.azure-automation.net`<br>`https://<accountId>.agentsvc.eus2.azure-automation.net`<br>`https://<accountId>.jrds.eus2.azure-automation.net` |
| カナダ中部 |`https://<accountId>.webhook.cc.azure-automation.net`<br>`https://<accountId>.agentsvc.cc.azure-automation.net`<br>`https://<accountId>.jrds.cc.azure-automation.net` |
| 西ヨーロッパ |`https://<accountId>.webhook.we.azure-automation.net`<br>`https://<accountId>.agentsvc.we.azure-automation.net`<br>`https://<accountId>.jrds.we.azure-automation.net` |
| 北ヨーロッパ |`https://<accountId>.webhook.ne.azure-automation.net`<br>`https://<accountId>.agentsvc.ne.azure-automation.net`<br>`https://<accountId>.jrds.ne.azure-automation.net` |
| 東南アジア |`https://<accountId>.webhook.sea.azure-automation.net`<br>`https://<accountId>.agentsvc.sea.azure-automation.net`<br>`https://<accountId>.jrds.sea.azure-automation.net` |
| 東アジア |`https://<accountId>.webhook.ea.azure-automation.net`<br>`https://<accountId>.agentsvc.ea.azure-automation.net`<br>`https://<accountId>.jrds.ea.azure-automation.net` |
| インド中部 |`https://<accountId>.webhook.cid.azure-automation.net`<br>`https://<accountId>.agentsvc.cid.azure-automation.net`<br>`https://<accountId>.jrds.cid.azure-automation.net` |
| 東日本 |`https://<accountId>.webhook.jpe.azure-automation.net`<br>`https://<accountId>.agentsvc.jpe.azure-automation.net`<br>`https://<accountId>.jrds.jpe.azure-automation.net` |
| 韓国中部 |`https://<accountId>.webhook.kc.azure-automation.net`<br>`https://<accountId>.agentsvc.kc.azure-automation.net`<br>`https://<accountId>.jrds.kc.azure-automation.net` |
| オーストラリア東南部 |`https://<accountId>.webhook.ase.azure-automation.net`<br>`https://<accountId>.agentsvc.ase.azure-automation.net`<br>`https://<accountId>.jrds.ase.azure-automation.net` |
| オーストラリア東部 |`https://<accountId>.webhook.ae.azure-automation.net`<br>`https://<accountId>.agentsvc.ae.azure-automation.net`<br>`https://<accountId>.jrds.ae.azure-automation.net` |
| オーストラリア中部 |`https://<accountId>.webhook.ac.azure-automation.net`<br>`https://<accountId>.agentsvc.ac.azure-automation.net`<br>`https://<accountId>.jrds.ac.azure-automation.net` |
| 英国南部 |`https://<accountId>.webhook.uks.azure-automation.net`<br>`https://<accountId>.agentsvc.uks.azure-automation.net`<br>`https://<accountId>.jrds.uks.azure-automation.net` |
| フランス中部 |`https://<accountId>.webhook.fc.azure-automation.net`<br>`https://<accountId>.agentsvc.fc.azure-automation.net`<br>`https://<accountId>.jrds.fc.azure-automation.net` |
| 南アフリカ北部 |`https://<accountId>.webhook.san.azure-automation.net`<br>`https://<accountId>.agentsvc.san.azure-automation.net`<br>`https://<accountId>.jrds.san.azure-automation.net` |
| ブラジル南部 |`https://<accountId>.webhook.brs.azure-automation.net`<br>`https://<accountId>.agentsvc.brs.azure-automation.net`<br>`https://<accountId>.jrds.brs.azure-automation.net` |
| 中国北部 |`https://<accountId>.webhook.bjb.azure-automation.cn`<br>`https://<accountId>.agentsvc.bjb.azure-automation.cn`<br>`https://<accountId>.jrds.bjb.azure-automation.cn` |
| 中国北部 2 |`https://<accountId>.webhook.bjs2.azure-automation.cn`<br>`https://<accountId>.agentsvc.bjs2.azure-automation.cn`<br>`https://<accountId>.jrds.bjs2.azure-automation.cn` |
| 中国東部 2 |`https://<accountId>.webhook.sha2.azure-automation.cn`<br>`https://<accountId>.agentsvc.sha2.azure-automation.cn`<br>`https://<accountId>.jrds.sha2.azure-automation.cn` |
| US Gov バージニア州 |`https://<accountId>.webhook.usge.azure-automation.us`<br>`https://<accountId>.agentsvc.usge.azure-automation.us`<br>`https://<accountId>.jrds.usge.azure-automation.us` |
| US Gov テキサス |`https://<accountId>.webhook.ussc.azure-automation.us`<br>`https://<accountId>.agentsvc.ussc.azure-automation.us`<br>`https://<accountId>.jrds.ussc.azure-automation.us` |
| US Gov アリゾナ |`https://<accountId>.webhook.phx.azure-automation.us`<br>`https://<accountId>.agentsvc.phx.azure-automation.us`<br>`https://<accountId>.jrds.phx.azure-automation.us` |

DNS レコードの `<accountId>` は、**URL** の値の Automation アカウント ID を表す GUID に置き換えます。 必要な ID は、Azure portal の **[アカウント設定]** の下にある **[キー]** から取得できます。

![Automation アカウントの主キーのページ](./media/automation-region-dns-records/automation-account-keys.png)

**[URL]** フィールド - `https://<GUID>.agentsvc.<region>.azure-automation.net/accounts/<GUID>` から、*accounts/* の後の値をコピーします。

> [!NOTE]
> Webhook および agentservice DNS のすべてのレコードは、Private Link をサポートするために新しいスタイルの DNS レコードに更新されています。 JRDS DNS レコードの場合、古いスタイルと新しいスタイル両方の DNS レコードがサポートされます。 Private Link を使用していない場合は、古いスタイルの DNS レコードが表示されますが、Private Link を使用している場合は、新しいスタイルの DNS レコードが表示されます。

[例外](../automation-runbook-execution.md#exceptions)を定義するときは、一覧に示されているアドレスを使用することをお勧めします。 リージョン名の代わりにリージョン IP アドレスの一覧を入手するには、Microsoft ダウンロード センターから次のクラウド環境の JSON ファイルをダウンロードします。

* [Azure IP 範囲とサービス タグ - Azure パブリック](https://www.microsoft.com/download/details.aspx?id=56519)
* [Azure IP 範囲とサービス タグ - Azure Government](https://www.microsoft.com/download/details.aspx?id=57063)
* [Azure IP 範囲とサービス タグ - Azure Germany](https://www.microsoft.com/download/details.aspx?id=57064)
* [Azure IP 範囲とサービス タグ - Azure China Vianet 21](https://www.microsoft.com/download/details.aspx?id=57062)

IP アドレス ファイルには、Microsoft Azure データセンターで使用されている IP アドレス範囲が一覧表示されています。 計算、SQL、およびストレージの範囲が含まれ、現在デプロイされている範囲と今後変更される IP 範囲が反映されます。 このファイルに現れる新しい範囲は、少なくとも 1 週間はデータセンターで使用されません。

新しい IP アドレス ファイルを毎週ダウンロードすることをお勧めします。 その後、Azure で実行されているサービスを正しく識別するようにサイトを更新します。

> [!NOTE]
> Azure ExpressRoute を使用している場合は、毎月第 1 週に Azure 領域の Border Gateway Protocol (BGP) アドバタイズを更新するために、IP アドレス ファイルが使用されることを覚えておきます。

## <a name="next-steps"></a>次のステップ

* Hybrid Runbook Worker のトラブルシューティングを行う方法については、「[Hybrid Runbook Worker のトラブルシューティング](../troubleshoot/hybrid-runbook-worker.md#general)」を参照してください。

* State Configuration に関する問題をトラブルシューティングする方法については、「[State Configuration の問題のトラブルシューティング](../troubleshoot/desired-state-configuration.md)」を参照してください。