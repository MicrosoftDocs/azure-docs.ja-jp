---
title: Azure Private Link 接続に関する問題のトラブルシューティングを行う
description: プライベート リンク接続を診断するためのステップ バイ ステップのガイダンス
services: private-link
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
ms.openlocfilehash: 45a7a146dd929408b50a0045fe2598726ee05505
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95544311"
---
# <a name="troubleshoot-azure-private-link-connectivity-problems"></a>Azure Private Link 接続に関する問題のトラブルシューティングを行う

この記事では、Azure Private Link のセットアップに使用される接続を検証して診断するためのステップ バイ ステップのガイダンスを提供します。

Azure Private Link を使用すると、Azure の PaaS (サービスとしてのプラットフォーム) サービス (Azure Storage、Azure Cosmos DB、Azure SQL Database など) や、Azure でホストされている顧客サービスまたはパートナー サービスに、仮想ネットワーク内のプライベート エンドポイントからアクセスできます。 仮想ネットワークとサービスの間のトラフィックは、Microsoft のバックボーン ネットワークを経由するため、パブリック インターネットに露出することはありません。 また、独自のプライベート リンク サービスを仮想ネットワークに作成し、そのサービスを顧客に非公開で配信することもできます。

Standard レベルの Azure Load Balancer の内側で稼動するサービスは、Private Link で提供することができます。 サービスのコンシューマーは、仮想ネットワーク内にプライベート エンドポイントを作成し、それをこのサービスにマップして非公開でアクセスできます。

Private Link で使用できる接続シナリオを次に示します。

- 同じリージョンの仮想ネットワーク
- リージョンでピアリングされた仮想ネットワーク
- グローバルにピアリングされた仮想ネットワーク
- VPN または Azure ExpressRoute 回線を介したオンプレミスのお客様

## <a name="deployment-troubleshooting"></a>デプロイのトラブルシューティング

プライベート リンク サービス用に選択したサブネットからソース IP アドレスを選択できない場合のトラブルシューティングについては、[プライベート リンク サービスのネットワーク ポリシーの無効化](./disable-private-link-service-network-policy.md)に関する情報を参照してください。

ソース IP アドレスの選択元のサブネットに対して **privateLinkServiceNetworkPolicies** 設定が無効になっていることを確認してください。

## <a name="diagnose-connectivity-problems"></a>接続に関する問題を診断する

プライベート リンクのセットアップで接続の問題が生じた場合は、以下の手順を確認し、通常の構成がすべて想定どおりであることを確かめてください。

1. リソースを参照して Private Link の構成を確認します。

    a. **[プライベート リンク センター]** に移動します。

      ![プライベート リンク センター](./media/private-link-tsg/private-link-center.png)

    b. 左ペインで **[プライベート リンク サービス]** を選択します。

      ![プライベート リンク サービス](./media/private-link-tsg/private-link-service.png)

    c. 診断するプライベート リンク サービスをフィルター処理して選択します。

    d. プライベート エンドポイント接続を確認します。
     - 接続元とするプライベート エンドポイントが、 **[Approved]\(承認済み\)** の接続状態でリストされていることを確認します。
     - 状態が **[Pending]\(保留中\)** の場合は、それを選択して承認します。

       ![プライベート エンドポイント接続](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - 接続元のプライベート エンドポイントの名前を選択して移動します。 接続状態に **[Approved]\(承認済み\)** と表示されていることを確認します。

       ![プライベート エンドポイント接続の概要](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - 両方の側が承認されたら、接続を再試行します。

    e. **[概要]** タブで **[別名]** を確認し、 **[プロパティ]** タブで **[リソース ID]** を確認します。
     - その **別名** と **リソース ID** の情報が、このサービスへのプライベート エンドポイントを作成するために使用している **別名** と **リソース ID** と一致することを確認します。

       ![別名の情報を確認する](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![リソース ID の情報を確認する](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f. **[概要]** タブで **[Visibility]\(可視性\)** の情報を確認します。
     - サブスクリプションが **[Visibility]\(可視性\)** スコープの下にあることを確認します。

       ![可視性の情報を確認する](./media/private-link-tsg/pls-overview-pane-visibility.png)

    g. **[概要]** タブで **[ロード バランサー]** の情報を確認します。
     - ロード バランサーには、そのリンクを選択することで移動できます。

       ![ロード バランサーの情報を確認する](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - ロード バランサーの設定が期待どおりに構成されていることを確認します。
       - **フロントエンド IP 構成** を確認します。
       - **バックエンド プール** を確認します。
       - **負荷分散規則** を確認します。

       ![ロード バランサーのプロパティを確認する](./media/private-link-tsg/pls-ilb-properties.png)

     - ロード バランサーが前出の設定に従って動作していることを確認します。
       - サブネット (ロード バランサーのバックエンド プールがあるサブネットを除く) 内の VM を選択します。
       - 前出の VM からロード バランサーのフロントエンドにアクセスしてみます。
       - 負荷分散規則に従ってバックエンド プールへの接続が行われる場合、ロード バランサーは動作しています。
       - また、Azure Monitor を使用してロード バランサーのメトリックを確認し、データがロード バランサーを介して流れているかどうかを確認することもできます。

1. [Azure Monitor](../azure-monitor/overview.md) を使用して、データが流れているかどうか確認します。

    a. プライベート リンク サービス リソースで、 **[Metrics]\(メトリック\)** を選択します。
     - **[入力バイト数]** または **[出力バイト数]** を選択します。
     - プライベート リンク サービスへの接続を試みたときにデータが流れているかどうかを確認します。 約 10 分の遅延が予想されます。

       ![プライベート リンク サービスのメトリックを確認する](./media/private-link-tsg/pls-metrics.png)

1. 問題が解決されず、接続の問題が依然として存在する場合は、[Azure サポート](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) チームにお問い合わせください。

## <a name="next-steps"></a>次のステップ

 * [Private Link サービスを作成する (CLI)](./create-private-link-service-cli.md)
 * [Azure プライベート エンドポイントのトラブルシューティング ガイド](troubleshoot-private-endpoint-connectivity.md)