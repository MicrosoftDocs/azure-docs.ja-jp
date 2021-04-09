---
title: Azure プライベート エンドポイント接続に関する問題のトラブルシューティング
description: プライベート エンドポイント接続を診断するためのステップ バイ ステップのガイダンス
services: private-endpoint
documentationcenter: na
author: rdhillon
manager: narayan
editor: ''
ms.service: private-link
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: rdhillon
ms.openlocfilehash: 90831c0e8d5ab73f65dc801319a357d59799cbc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97807554"
---
# <a name="troubleshoot-azure-private-endpoint-connectivity-problems"></a>Azure プライベート エンドポイント接続に関する問題のトラブルシューティング

この記事では、Azure プライベート エンドポイント接続のセットアップを検証して診断するためのステップ バイ ステップのガイダンスを提供します。

Azure プライベート エンドポイントは、プライベート リンク サービスにプライベートかつ安全に接続するネットワーク インターフェイスです。 このソリューションは、仮想ネットワークから Azure サービス リソースへのプライベート接続を提供することによって、Azure でワークロードをセキュリティで保護するのに役立ちます。 このソリューションにより、それらのサービスが仮想ネットワークに効果的に導入されます。

プライベート エンドポイントで使用できる接続シナリオを次に示します。

- 同じリージョンの仮想ネットワーク
- リージョンでピアリングされた仮想ネットワーク
- グローバルにピアリングされた仮想ネットワーク
- VPN または Azure ExpressRoute 回線を介したオンプレミスのお客様

## <a name="diagnose-connectivity-problems"></a>接続に関する問題を診断する 

プライベート エンドポイント セットアップでの接続の問題を解決するために、以下の手順を確認し、通常の構成がすべて想定どおりであることを確かめてください。

1. リソースを参照して、プライベート エンドポイントの構成を確認します。

    a. **[プライベート リンク センター]** に移動します。

      ![プライベート リンク センター](./media/private-endpoint-tsg/private-link-center.png)

    b. 左側のペインで、 **[プライベート エンドポイント]** を選択します。
    
      ![プライベート エンドポイント](./media/private-endpoint-tsg/private-endpoints.png)

    c. 診断するプライベート エンドポイントをフィルター処理して選択します。

    d. 仮想ネットワークと DNS 情報を確認します。
     - 接続状態が **[承認済み]** になっていることを確認します。
     - プライベート エンドポイントをホストしている仮想ネットワークに VM が接続されていることを確認します。
     - FQDN 情報 (コピー) とプライベート IP アドレスが割り当てられていることを確認します。
    
       ![仮想ネットワークと DNS の構成](./media/private-endpoint-tsg/vnet-dns-configuration.png)
    
1. [Azure Monitor](../azure-monitor/overview.md) を使用して、データが流れているかどうか確認します。

    a. プライベート エンドポイント リソースで **[監視]** を選択します。
     - **[受信データ]** または **[送信データ]** を選択します。 
     - プライベート エンドポイントへの接続を試みたときにデータが流れているかどうか確認します。 約 10 分の遅延が予想されます。
    
       ![プライベート エンドポイント テレメトリの検証](./media/private-endpoint-tsg/private-endpoint-monitor.png)

1.  Azure Network Watcher の **VM の接続のトラブルシューティング** を使用します。

    a. クライアント VM を選択します。

    b. **[接続のトラブルシューティング]** を選択し、 **[送信接続]** タブを選択します。
    
      ![Network Watcher - 送信接続のテスト](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c. **[詳細な接続トレースを行うために Network Watcher を使用する]** を選択します。
    
      ![Network Watcher - 接続のトラブルシューティング](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d. **[Test by FQDN]\(FQDN でテスト\)** を選択します。
     - プライベート エンドポイント リソースから FQDN を貼り付けます。
     - ポートを指定します。 通常、Azure Storage または Azure Cosmos DB の場合は 443、SQL の場合は 1336 を使用します。

    e. **[テスト]** を選択して、テスト結果を検証します。
    
      ![Network Watcher - テスト結果](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
1. テスト結果の DNS 解決には、プライベート エンドポイントに割り当てられているものと同じプライベート IP アドレスが含まれている必要があります。

    a. DNS 設定が正しくない場合は、次の手順を実行します。
     - プライベート ゾーンを使用する場合: 
       - クライアント VM 仮想ネットワークがプライベート ゾーンに関連付けられていることを確認します。
       - プライベート DNS ゾーン レコードが存在することを確認します。 存在しなければ、作成してください。
     - カスタム DNS を使用する場合:
       - カスタム DNS 設定を確認し、DNS 構成が正しいことを検証します。
       ガイダンスについては、[プライベート エンドポイントの概要: DNS の構成](./private-endpoint-overview.md#dns-configuration)に関する記事を参照してください。

    b. ネットワーク セキュリティ グループ (NSG) またはユーザー定義ルートが原因で接続が失敗する場合:
     - NSG アウトバウンド規則を確認し、トラフィックを許可するための適切なアウトバウンド規則を作成します。
    
       ![NSG アウトバウンド規則](./media/private-endpoint-tsg/nsg-outbound-rules.png)

1. ソース仮想マシンでは、NIC の有効なルートで、プライベート エンドポイント IP のネクスト ホップへのルートが InterfaceEndpoints として設定されている必要があります。 

    a. ソース VM のプライベート エンドポイント ルートを確認できない場合は、次の点を調べてください 
     - ソース VM とプライベート エンドポイントが同じ VNET に属している。 「はい」の場合は、サポートに問い合わせる必要があります。 
     - ソース VM とプライベート エンドポイントは異なる VNET に属している。この場合は、VNET 間の IP 接続を調べます。 IP 接続が確立していてもルートを確認できない場合は、サポートにお問い合わせください。 

1. 接続の結果が正しいと確認された場合、接続の問題は、アプリケーション層でのシークレット、トークン、パスワードなどの他の側面に関連している可能性があります。
   - この場合、プライベート エンドポイントに関連付けられているプライベート リンク リソースの構成を確認してください。 詳細については、[Azure Private Link トラブルシューティング ガイド](troubleshoot-private-link-connectivity.md)を参照してください
   
1. 常に、サポート チケットを提出する前に絞り込むことをお勧めします。 

    a. ソースがオンプレミスであり Azure のプライベート エンドポイントに接続し、問題が発生している場合は、次の接続を試してください 
      - オンプレミスから仮想マシンへの接続。オンプレミスから Virtual Network への IP 接続が確立されているかどうかを調べます。 
      - Virtual Network 内の仮想マシンからプライベート エンドポイントへの接続。
      
    b. ソースが Azure であり、プライベート エンドポイントが別の Virtual Network 内にある場合は、次の接続を試してください 
      - 別のソースからプライベート エンドポイントへの接続。 このようにすることで、仮想マシン固有のすべての問題を切り離せます。 
      - プライベート エンドポイントと同じ Virtual Network の一部である任意の仮想マシンへの接続。  

1. 問題が解決されず、接続の問題が依然として存在する場合は、[Azure サポート](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) チームにお問い合わせください。

## <a name="next-steps"></a>次のステップ

 * [更新されたサブネットにプライベート エンドポイントを作成する (Azure portal)](./create-private-endpoint-portal.md)
 * [Azure Private Link トラブルシューティング ガイド](troubleshoot-private-link-connectivity.md)
