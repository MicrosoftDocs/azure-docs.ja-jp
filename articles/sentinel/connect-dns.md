---
title: Azure Sentinel Preview で DNS データを接続する | Microsoft Docs
description: Azure Sentinel で DNS データを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: 1c79aad557efb85a8797584c33c74983ef645d07
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67611314"
---
# <a name="connect-your-domain-name-server"></a>ドメイン ネーム サーバーへの接続

> [!IMPORTANT]
> 現在、Azure Sentinel はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Windows 上で実行されているドメイン ネーム サーバー (DNS) を Azure Sentinel に接続できます。 そのためには、DNS マシンにエージェントをインストールします。 DNS ログを使用すると、分析および監査ログや他の関連データを DNS サーバーから収集し、分析して関連付けることによって、組織の DNS インフラストラクチャに関するセキュリティ、パフォーマンス、および操作に関連する分析情報を得ることができます。

DNS ログ接続を有効にすると、次のことが可能になります。
- 悪意のあるドメイン名を解決しようとしているクライアントを特定する
- 古いリソース レコードを特定する
- クエリ対象になる頻度が高いドメイン名と話好きな DNS クライアントを特定する
- DNS サーバーの要求負荷を確認する
- 動的 DNS 登録エラーを確認する

## <a name="connected-sources"></a>接続先ソース

次の表では、このソリューションでサポートされている接続先ソースについて説明します。

| **接続先ソース** | **サポート** | **説明** |
| --- | --- | --- |
| [Windows エージェント](../azure-monitor/platform/agent-windows.md) | はい | ソリューションでは、Windows エージェントから DNS 情報を収集します。 |
| [Linux エージェント](../azure-monitor/learn/quick-collect-linux-computer.md) | いいえ | ソリューションでは、ダイレクト Linux エージェントから DNS 情報は収集しません。 |
| [System Center Operations Manager 管理グループ](../azure-monitor/platform/om-agents.md) | はい | ソリューションでは、接続された Operations Manager 管理グループ内のエージェントから DNS 情報が収集されます。 Operations Manager エージェントから Azure Monitor への直接接続は必要ありません。 データは管理グループから Log Analytics ワークスペースに転送されます。 |
| [Azure Storage アカウント](../azure-monitor/platform/collect-azure-metrics-logs.md) | いいえ | ソリューションでは、Azure Storage は使用されません。 |

### <a name="data-collection-details"></a>データ収集の詳細

DNS 分析ソリューションでは、Log Analytics エージェントがインストールされている DNS サーバーから DNS インベントリと DNS イベント関連データを収集します。 インベントリ関連データ (DNS サーバーの数、ゾーンの数、リソース レコードの数など) は、DNS PowerShell コマンドレットを実行して収集します。 データは 2 日に 1 回更新されます。 イベント関連データは、Windows Server 2012 R2 の強化された DNS ログと診断機能によって提供される[分析ログと監査ログ](https://technet.microsoft.com/library/dn800669.aspx#enhanc)からほぼリアルタイムで収集されます。


## <a name="connect-your-dns-appliance"></a>DNS アプライアンスの接続

1. Azure Sentinel portal で、 **[Data connectors]\(データ コネクタ\)** を選択し、 **[DNS]** タイルを選択します。
1. DNS マシンが Azure にある場合は、次の手順を実行します。
    1. **[Install agent on Azure Windows virtual machine]\(Azure Windows 仮想マシンにエージェントをインストールする\)** をクリックします。
    1. **[仮想マシン]** 一覧で、Azure Sentinel にストリーミングする DNS マシンを選択します。 これが Windows VM であることを確認します。
    1. 開いているその VM のウィンドウで、 **[接続]** をクリックします。  
    1. **[DNS connector]\(DNS コネクタ\)** ウィンドウで **[有効化]** をクリックします。 

2. DNS マシンが Azure VM ではない場合は、次の手順を実行します。
    1. **[Install agent on non-Azure machines]\(非 Azure マシンにエージェントをインストールする\)** をクリックします。
    1. **[ダイレクト エージェント]** ウィンドウで、 **[Download Windows agent (64 bit)]\(Windows エージェントのダウンロード (64 ビット)\)** または **[Download Windows agent (32 bit)]\(Windows エージェントのダウンロード (32 ビット)\)** を選択します。
    1. DNS マシンにエージェントをインストールします。 **ワークスペース ID**、**主キー**、**2 次キー**をコピーし、インストール中に入力を要求されたらこれらを使用します。

3. Log Analytics で DNS ログに関連するスキーマを使用するために、**DnsEvents** を検索します。

## <a name="validate"></a>検証 

Log Analytics で、スキーマ **DnsEvents** を検索し、イベントが存在することを確認します。

## <a name="next-steps"></a>次の手順
このドキュメントでは、DNS オンプレミス アプライアンスを Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)の概要。
