---
title: Azure Load Balancer のトラブルシューティング
titlesuffix: Azure Load Balancer
description: Azure Load Balancer に関する既知の問題のトラブルシューティング
services: load-balancer
documentationcenter: na
author: chadmath
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/09/2018
ms.author: genli
ms.openlocfilehash: 4e0e3cf6067467947bcb799a915a93d1bb342ea1
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71154925"
---
# <a name="troubleshoot-azure-load-balancer"></a>Azure Load Balancer のトラブルシューティング

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

このページには、Azure Load Balancer についてよく寄せられる質問のトラブルシューティング情報が示されています。 Load Balancer の接続が利用できない場合の最も一般的な症状を次に示します。 
- Load Balancer の背後にある VM が正常性プローブに応答しない 
- Load Balancer の背後にある VM が構成済みポートのトラフィックに応答しない

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
2. コマンド プロンプトを開き、次のコマンドを実行して、プローブ ポートでリッスンしているアプリケーションが存在しているかどうかを検証します。   
            netstat -an
3. ポートの状態が "**リッスン中**" になっていない場合は、適切なポートを構成します。 
4. または、"**リッスン中**" として表示されている別のポートを選択し、ロード バランサ―の構成を適宜更新します。              

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>原因 3:ファイアウォール、またはネットワーク セキュリティ グループが Load Balancer バックエンド プール VM 上のポートをブロックしている  
VM 上のファイアウォールがプローブ ポートをブロックしているか、サブネットまたは VM 上に構成された 1 つ以上のネットワーク セキュリティ グループが、ポートへのプローブのアクセスを許可していない場合、VM は正常性プローブに応答できません。          

**検証と解決策**

* ファイアウォールが有効になっている場合は、プローブ ポートを許可するように構成されているかどうかを確認します。 許可する構成になっていない場合は、プローブ ポートのトラフィックを許可するように構成し、再度テストします。 
* ネットワーク セキュリティ グループの一覧で、プローブ ポートの受信または送信トラフィックで干渉が発生していないかどうかを確認します。 
* また、サブネットまたは VM の NIC で、**すべて拒否**ネットワーク セキュリティ グループ ルールの優先順位が、LB プローブとトラフィックを許可する既定のルールよりも高くなっていないかどうかを確認します (ネットワーク セキュリティ グループでは、Load Balancer IP アドレス 168.63.129.16 が許可されている必要があります)。 
* ルールのいずれかがプローブのトラフィックをブロックしている場合は、そのルールを削除し、プローブ トラフィックを許可するように再構成します。  
* VM が正常性プローブに応答するようになったかどうかをテストします。 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>原因 4:Load Balancer の他の構成ミス
上記の原因すべてを検証し、正しく解決したにもかかわらず、バックエンド VM が正常性プローブに応答しない場合は、手動で接続をテストし、トレースをいくつか収集して、接続状況を把握します。

**検証と解決策**

* VNet 内にある他の VM から **Psping** でプローブ ポートの応答をテストし (.\psping.exe-t 10.0.0.4:3389 など)、結果を記録します。 
* VNet 内にある他の VM から **TCPing** でプローブ ポートの応答をテストし (.\tcping.exe 10.0.0.4 3389 など)、結果を記録します。 
* この ping テストで応答がない場合は、次の操作を行います
    - 同じ VNet のターゲット バックエンド プール VM と他のテスト VM で同時 Netsh トレースを実行します。 ここで、しばらくの間 PsPing テストを実行し、ネットワーク トレースをいくつか収集して、テストを停止します。 
    - ネットワーク キャプチャを分析し、ping クエリに関連する受信パケットと送信パケットの両方があるかどうかを確認します。 
        - バックエンド プール VM に受信パケットがない場合は、トラフィックをブロックしているネットワーク セキュリティ グループまたは UDR の構成ミスが存在する可能性があります。 
        - バックエンド プール VM に送信パケットがない場合は、関連のない問題 (アプリケーションがプローブ ポートをブロックしている、など) が VM にないかどうかを確認する必要があります。 
    - プローブが、ロード バランサーに到達する前に、他の場所に (おそらく UDR 設定によって) 強制的に転送されていないかどうかを確認します。 この場合、トラフィックは、バックエンド VM に到達しません。 
* プローブの種類を (たとえば、HTTP から TCP に) 変更し、ネットワーク セキュリティ グループ ACL とファイアウォールの対応するポートを、プローブ応答の構成に問題があるかどうかを検証するように構成します。 正常性プローブ構成の詳細については、[正常性プローブ構成のエンドポイント負荷分散](https://blogs.msdn.microsoft.com/mast/2016/01/26/endpoint-load-balancing-heath-probe-configuration-details/)に関するページをご覧ください。

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>症状:Load Balancer の背後にある VM が構成済みデータ ポートのトラフィックに応答しない

バックエンド プール VM が正常として表示され、正常性プローブに応答するにもかかわらず、負荷分散に参加していない、またはデータ トラフィックに応答していない場合は、次のいずれかが原因である可能性があります。 
* Load Balancer バックエンド プール VM がデータ ポートでリッスンしていない 
* ネットワーク セキュリティ グループが Load Balancer バックエンド プール VM 上のポートをブロックしている  
* 同じ VM と NIC から Load Balancer にアクセスしている 
* 参加している Load Balancer バックエンド プール VM からインターネット Load Balancer フロントエンドにアクセスしている 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>原因 1:Load Balancer バックエンド プール VM がデータ ポートでリッスンしていない 
VM がデータ トラフィックに応答しない場合、その原因としては、参加している VM でターゲット ポートが開いていない、または VM がそのポートでリッスンしていないことが考えられます。 

**検証と解決策**

1. バックエンド VM にログインします。 
2. コマンド プロンプトを開き、次のコマンドを実行して、データ ポートでリッスンしているアプリケーションが存在しているかどうかを検証します。  netstat -an 
3. そのポートに "リッスン中" 状態が表示されていない場合は、適切なリスナー ポートを構成します 
4. ポートが "リッスン中" としてマークされている場合は、そのポートのターゲット アプリケーションに問題がないかどうかを確認します。 

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>原因 2:ネットワーク セキュリティ グループが Load Balancer バックエンド プール VM 上のポートをブロックしている  

サブネットまたは VM で構成されている 1 つ以上のネットワーク セキュリティ グループが、発信元 IP またはポートをブロックしている場合、VM は応答できません。

* バックエンド VM で構成されているネットワーク セキュリティ グループを一覧表示します。 詳細については、「[Manage network security groups](../virtual-network/manage-network-security-group.md)」(ネットワーク セキュリティ グループの管理) をご覧ください。
* ネットワーク セキュリティ グループの一覧で、次を確認します。
    - データ ポートの受信または送信トラフィックで干渉が発生していないかどうか。 
    - サブネットまたは VM の NIC で、**すべて拒否**ネットワーク セキュリティ グループ ルールの優先順位が、Load Balancer プローブとトラフィックを許可する既定のルールよりも高くなっていないかどうか (ネットワーク セキュリティ グループでは、プローブ ポートである Load Balancer IP アドレス 168.63.129.16 が許可されている必要があります) 
* ルールのいずれかがトラフィックをブロックしている場合は、そのルールを削除し、データ トラフィックを許可するように再構成します。  
* VM が正常性プローブに応答するようになったかどうかをテストします。

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>原因 3:同じ VM とネットワーク インターフェイスから Load Balancer にアクセスしている 

Load Balancer のバックエンド VM でホストされているアプリケーションが、同じネットワーク インターフェイスを介して同じバックエンド VM でホストされている別のアプリケーションにアクセスしようとすると、失敗します。このシナリオはサポートされていません。 

**解決策**: この問題を解決するには、次のいずれかの方法を使用します。
* アプリケーションごとに個別のバックエンド プール VM を構成する。 
* 各アプリケーションが独自のネットワーク インターフェイスと IP アドレスを使用していたように、デュアル NIC VM でアプリケーションを構成する。 

### <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>原因 4:参加している Load Balancer バックエンド プール VM から内部 Load Balancer フロントエンドにアクセスしている

内部 Load Blancer が VNet 内で構成され、参加しているバックエンド VM の 1 つが内部 Load Balancer フロントエンドにアクセスしようとしている場合は、エラーが発生し、アクセス元の VM にフローがマップされます。 このシナリオはサポートされていません。 詳しくは、「[制限事項](load-balancer-overview.md#limitations)」をご覧ください。

**解決策**: このシナリオをブロック解除するには、プロキシの使用などいくつかの方法があります。 Application Gateway または他のサードパーティ製プロキシ (nginx や haproxy など) を評価してください。 Application Gateway の詳細については、「[Application Gateway の概要](../application-gateway/application-gateway-introduction.md)」を参照してください

## <a name="additional-network-captures"></a>その他のネットワーク キャプチャ
サポート ケースを開く場合は、迅速に解決できるように次の情報を収集します。 1 つのバックエンド VM を選択して、次のテストを実行してください。
- VNet 内にあるバックエンド VM から Psping でプローブ ポートの応答をテストし (psping 10.0.0.4:3389 など)、結果を記録します。 
- この ping テストで応答がなかった場合は、PsPing を実行しながら、バックエンド VM と VNet テスト VM で同時 Netsh トレースを実行し、その後、Netsh トレースを停止します。 
  
## <a name="next-steps"></a>次の手順

前の手順で問題を解決できない場合は、 [サポート チケット](https://azure.microsoft.com/support/options/)を開きます。

