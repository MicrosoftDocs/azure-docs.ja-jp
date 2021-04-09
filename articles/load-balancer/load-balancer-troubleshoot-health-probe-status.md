---
title: Azure Load Balancer の正常性プローブの状態に関するトラブルシューティング
description: Azure Load Balancer の正常性プローブの状態に関する既知の問題のトラブルシューティングを行う方法について説明します。
services: load-balancer
documentationcenter: na
author: asudbring
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/02/2020
ms.author: allensu
ms.openlocfilehash: 28823c997cd974d5061829df88680ed52075caa0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98029154"
---
# <a name="troubleshoot-azure-load-balancer-health-probe-status"></a>Azure Load Balancer の正常性プローブの状態に関するトラブルシューティング

このページには、Azure Load Balancer の正常性プローブについてよく寄せられる質問のトラブルシューティング情報が記載されています。

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>症状:Load Balancer の背後にある VM が正常性プローブに応答しない
バックエンド サーバーがロード バランサ― セットに参加するには、プローブ チェックを渡す必要があります。 正常性プローブの詳細については、[Load Balancer のプローブ](load-balancer-custom-probe-overview.md)に関するページをご覧ください。 

Load Balancer バックエンド プール VM は、次のいずれかの理由によりプローブに応答しない可能性があります。 
- Load Balancer バックエンド プール VM が正常でない 
- Load Balancer バックエンド プール VM がプローブ ポートでリッスンしていない 
- ファイアウォール、またはネットワーク セキュリティ グループが Load Balancer バックエンド プール VM 上のポートをブロックしている 
- Load Balancer の他の構成ミス

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>原因 1:Load Balancer バックエンド プール VM が正常でない

**検証と解決策**

この問題を解決するには、参加している VM にログインし、VM の状態が正常かどうか、また、その VM が、プール内の他の VM からの **PsPing** または **TCPing** に応答できるかどうかを確認します。 VM が正常でない場合、またはプローブに応答できない場合、その VM を負荷分散に参加させるには、問題を解決してから、VM を正常な状態に戻す必要があります。

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>原因 2:Load Balancer バックエンド プール VM がプローブ ポートでリッスンしていない
VM の状態が正常であるにもかかわらず、プローブに応答していない場合、参加している VM のプローブ ポートが開いていないか、そのポート上で VM がリッスンしていないことが原因の 1 つとして考えられます。

**検証と解決策**

1. バックエンド VM にログインします。
2. コマンド プロンプトを開き、コマンド netstat -an を実行して、プローブ ポートでリッスンしているアプリケーションが存在かどうかを検証します。
3. ポートの状態が "**リッスン中**" になっていない場合は、適切なポートを構成します。 
4. または、"**リッスン中**" として表示されている別のポートを選択し、ロード バランサ―の構成を適宜更新します。

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>原因 3:ファイアウォール、またはネットワーク セキュリティ グループが Load Balancer バックエンド プール VM 上のポートをブロックしている
VM 上のファイアウォールがプローブ ポートをブロックしているか、サブネットまたは VM 上に構成された 1 つ以上のネットワーク セキュリティ グループが、ポートへのプローブのアクセスを許可していない場合、VM は正常性プローブに応答できません。

**検証と解決策**

1. ファイアウォールが有効になっている場合は、プローブ ポートを許可するように構成されているかどうかを確認します。 許可する構成になっていない場合は、プローブ ポートのトラフィックを許可するように構成し、再度テストします。
2. ネットワーク セキュリティ グループの一覧で、プローブ ポートの受信または送信トラフィックで干渉が発生していないかどうかを確認します。
3. また、サブネットまたは VM の NIC で、**すべて拒否** ネットワーク セキュリティ グループ ルールの優先順位が、LB プローブとトラフィックを許可する既定のルールよりも高くなっていないかどうかを確認します (ネットワーク セキュリティ グループでは、Load Balancer IP アドレス 168.63.129.16 が許可されている必要があります)。
4. ルールのいずれかがプローブのトラフィックをブロックしている場合は、そのルールを削除し、プローブ トラフィックを許可するように再構成します。  
5. VM が正常性プローブに応答するようになったかどうかをテストします。

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>原因 4:Load Balancer の他の構成ミス
上記の原因すべてを検証し、正しく解決したにもかかわらず、バックエンド VM が正常性プローブに応答しない場合は、手動で接続をテストし、トレースをいくつか収集して、接続状況を把握します。

**検証と解決策**

1. VNet 内にある他の VM から **Psping** でプローブ ポートの応答をテストし (.\psping.exe-t 10.0.0.4:3389 など)、結果を記録します。 
2. VNet 内にある他の VM から **TCPing** でプローブ ポートの応答をテストし (.\tcping.exe 10.0.0.4 3389 など)、結果を記録します。 
3. この ping テストで応答がない場合は、次の操作を行います
    - 同じ VNet のターゲット バックエンド プール VM と他のテスト VM で同時 Netsh トレースを実行します。 ここで、しばらくの間 PsPing テストを実行し、ネットワーク トレースをいくつか収集して、テストを停止します。 
    - ネットワーク キャプチャを分析し、ping クエリに関連する受信パケットと送信パケットの両方があるかどうかを確認します。 
        - バックエンド プール VM に受信パケットがない場合は、トラフィックをブロックしているネットワーク セキュリティ グループまたは UDR の構成ミスが存在する可能性があります。 
        - バックエンド プール VM に送信パケットがない場合は、関連のない問題 (アプリケーションがプローブ ポートをブロックしている、など) が VM にないかどうかを確認する必要があります。 
    - プローブが、ロード バランサーに到達する前に、他の場所に (おそらく UDR 設定によって) 強制的に転送されていないかどうかを確認します。 この場合、トラフィックは、バックエンド VM に到達しません。 
4. プローブの種類を (たとえば、HTTP から TCP に) 変更し、ネットワーク セキュリティ グループ ACL とファイアウォールの対応するポートを、プローブ応答の構成に問題があるかどうかを検証するように構成します。 正常性プローブ構成の詳細については、[正常性プローブ構成のエンドポイント負荷分散](/archive/blogs/mast/endpoint-load-balancing-heath-probe-configuration-details)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

前の手順で問題を解決できない場合は、 [サポート チケット](https://azure.microsoft.com/support/options/)を開きます。