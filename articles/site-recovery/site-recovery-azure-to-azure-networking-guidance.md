---
title: "Azure 間で仮想マシンをレプリケートするための Azure Site Recovery のネットワーク ガイダンス | Microsoft Docs"
description: "Azure 仮想マシンのレプリケートに関するネットワーク ガイダンス"
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/31/2017
ms.author: sujayt
ms.openlocfilehash: 99c08a1efbc610959fb4ba824dcb0601efac5877
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="networking-guidance-for-replicating-azure-virtual-machines"></a>Azure 仮想マシンのレプリケートに関するネットワーク ガイダンス

>[!NOTE]
> Azure 仮想マシンの Site Recovery レプリケーションは現在プレビューの段階です。

この記事では、リージョン間で Azure 仮想マシンをレプリケートして復旧するときの、Azure Site Recovery のネットワーク ガイダンスについて詳しく説明します。 Azure Site Recovery の要件の詳細については、[前提条件](site-recovery-prereq.md)に関する記事をご覧ください。

## <a name="site-recovery-architecture"></a>Site Recovery アーキテクチャ

Site Recovery では、Azure 仮想マシンで実行されているアプリケーションを、プライマリ リージョンで中断が発生した場合に復旧できるように、別の Azure リージョンに簡単にレプリケートできます。 詳細については、[このシナリオと Site Recovery アーキテクチャ](site-recovery-azure-to-azure-architecture.md)に関するページをご覧ください。

## <a name="your-network-infrastructure"></a>ご使用のネットワーク インフラストラクチャ

次の図は、アプリケーションを Azure 仮想マシンで実行するための一般的な Azure 環境を示しています。

![customer-environment](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

オンプレミス ネットワークから Azure への接続に Azure ExpressRoute または VPN 接続を使用している場合、環境は次のようになります。

![customer-environment](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

通常、顧客は、ファイアウォールやネットワーク セキュリティ グループ (NSG) を使用してネットワークを保護します。 ファイアウォールでは、URL ベースまたは IP ベースのホワイトリストを使用することでネットワーク接続を制御できます。 NSG では、IP 範囲の使用に関するルールによって、ネットワーク接続を制御できます。

>[!IMPORTANT]
> 認証済みプロキシを使用してネットワーク接続を制御している場合、これはサポートされておらず、Site Recovery レプリケーションを有効にすることはできません。

以降のセクションでは、Site Recovery レプリケーションを動作させるために Azure 仮想マシンから要求される、ネットワーク送信接続の変更について説明します。

## <a name="outbound-connectivity-for-azure-site-recovery-urls"></a>Azure Site Recovery URL の送信接続

URL ベースのファイアウォール プロキシを使用して送信接続を制御している場合は、次の必須の Azure Site Recovery サービス URL を必ずホワイトリストに登録してください。


**URL** | **目的**  
--- | ---
*.blob.core.windows.net | VM からソース リージョンのキャッシュ ストレージ アカウントにデータを書き込むことができるようにするために必要です。
login.microsoftonline.com | Site Recovery サービス URL に対する承認と認証に必要です。
*.hypervrecoverymanager.windowsazure.com | VM から Site Recovery サービス通信を実行できるようにするために必要です。
*.servicebus.windows.net | VM から Site Recovery の監視および診断データを書き込むことができるようにするために必要です。

## <a name="outbound-connectivity-for-azure-site-recovery-ip-ranges"></a>Azure Site Recovery IP 範囲の送信接続

>[!NOTE]
> 必要な NSG ルールをネットワーク セキュリティ グループに自動的に作成するには、[こちらのスクリプトをダウンロードして使用](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702)します。

>[!IMPORTANT]
> * 必要な NSG ルールをテスト用のネットワーク セキュリティ グループに作成し、問題がないことを確認してから、運用ネットワーク セキュリティ グループにルールを作成することをお勧めします。
> * 必要な数の NSG ルールを作成するには、サブスクリプションがホワイトリストに登録されていることを確認します。 サブスクリプションで NSG ルールの制限値を増やす必要があるときは、サポートにお問い合わせください。

IP ベースのファイアウォール プロキシまたは NSG ルールを使用して送信接続を制御する場合は、仮想マシンのソースとターゲットの場所に応じて、次の IP 範囲をホワイトリストに登録する必要があります。

- ソースの場所に対応するすべての IP 範囲  ([IP 範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)をダウンロードできます)。VM からキャッシュ ストレージ アカウントにデータを書き込むことができるようにするためには、ホワイトリストの登録が必要です。

- Office 365 [認証と ID IP V4 エンドポイント](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)に対応するすべての IP 範囲。

    >[!NOTE]
    > 今後、新しい IP が Office 365 の IP 範囲に追加された場合は、新しい NSG ルールを作成する必要があります。

- Site Recovery サービス エンドポイント IP ([XML ファイルで入手可能](https://aka.ms/site-recovery-public-ips))。これはターゲットの場所によって異なります。

   **ターゲットの場所** | **Site Recovery サービス IP** |  **Site Recovery 監視 IP**
   --- | --- | ---
   東アジア | 52.175.17.132 | 13.94.47.61
   東南アジア | 52.187.58.193 | 13.76.179.223
   インド中部 | 52.172.187.37 | 104.211.98.185
   インド南部 | 52.172.46.220 | 104.211.224.190
   米国中北部 | 23.96.195.247 | 168.62.249.226
   北ヨーロッパ | 40.69.212.238 | 52.169.18.8
   西ヨーロッパ | 52.166.13.64 | 40.68.93.145
   米国東部 | 13.82.88.226 | 104.45.147.24
   米国西部 | 40.83.179.48 | 104.40.26.199
   米国中南部 | 13.84.148.14 | 104.210.146.250
   米国中央部 | 40.69.144.231 | 52.165.34.144
   米国東部 2 | 52.184.158.163 | 40.79.44.59
   東日本 | 52.185.150.140 | 138.91.1.105
   西日本 | 52.175.146.69 | 138.91.17.38
   ブラジル南部 | 191.234.185.172 | 23.97.97.36
   オーストラリア東部 | 104.210.113.114 | 191.239.64.144
   オーストラリア南東部 | 13.70.159.158 | 191.239.160.45
   カナダ中部 | 52.228.36.192 | 40.85.226.62
   カナダ東部 | 52.229.125.98 | 40.86.225.142
   米国中西部 | 52.161.20.168 | 13.78.149.209
   米国西部 2 | 52.183.45.166 | 13.66.228.204
   英国西部 | 51.141.3.203 | 51.141.14.113
   英国南部 | 51.140.43.158 | 51.140.189.52
   英国南部 2 | 13.87.37.4| 13.87.34.139
   英国北部 | 51.142.209.167 | 13.87.102.68
   韓国中部 | 52.231.28.253 | 52.231.32.85
   韓国南部 | 52.231.298.185 | 52.231.200.144

## <a name="sample-nsg-configuration"></a>NSG 構成の例
このセクションでは、Site Recovery レプリケーションが仮想マシンで動作できるようにするために、NSG ルールを構成する手順について説明します。 NSG ルールを使用して送信接続を制御する場合は、必要なすべての IP 範囲に対して "HTTPS 送信を許可" ルールを使用します。

>[!Note]
> 必要な NSG ルールをネットワーク セキュリティ グループに自動的に作成するには、[こちらのスクリプトをダウンロードして使用](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702)します。

たとえば、VM のソースの場所が "米国東部" で、レプリケーション ターゲットの場所が "米国中部" の場合は、次の 2 つのセクションの手順に従います。

>[!IMPORTANT]
> * 必要な NSG ルールをテスト用のネットワーク セキュリティ グループに作成し、問題がないことを確認してから、運用ネットワーク セキュリティ グループにルールを作成することをお勧めします。
> * 必要な数の NSG ルールを作成するには、サブスクリプションがホワイトリストに登録されていることを確認します。 サブスクリプションで NSG ルールの制限値を増やす必要があるときは、サポートにお問い合わせください。

### <a name="nsg-rules-on-the-east-us-network-security-group"></a>米国東部ネットワーク セキュリティ グループの NSG ルール

* [米国東部 IP 範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)に対応するルールを作成します。 これは、VM からキャッシュ ストレージ アカウントにデータを書き込むことができるようにするために必要です。

* Office 365 [認証と ID IP V4 エンドポイント](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)に対応するすべての IP 範囲に対して、ルールを作成します。

* ターゲットの場所に対応するルールを作成します。

   **場所** | **Site Recovery サービス IP** |  **Site Recovery 監視 IP**
    --- | --- | ---
   米国中央部 | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules-on-the-central-us-network-security-group"></a>米国中部ネットワーク セキュリティ グループの NSG ルール

フェールオーバー後にターゲット リージョンからソース リージョンへのレプリケーションが有効になるようにするには、次のルールが必要です。

* [米国中部 IP 範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)に対応するルール。 これらは、VM からキャッシュ ストレージ アカウントにデータを書き込むことができるようにするために必要です。

* Office 365 [認証と ID IP V4 エンドポイント](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)に対応するすべての IP 範囲に対するルール。

* ソースの場所に対応するルール:

   **場所** | **Site Recovery サービス IP** |  **Site Recovery 監視 IP**
    --- | --- | ---
   米国東部 | 13.82.88.226 | 104.45.147.24


## <a name="guidelines-for-existing-azure-to-on-premises-expressroutevpn-configuration"></a>Azure とオンプレミス間の既存の ExpressRoute/VPN 構成に関するガイドライン

オンプレミスと Azure のソースの場所が ExpressRoute または VPN で接続されている場合は、このセクションのガイドラインに従ってください。

### <a name="forced-tunneling-configuration"></a>強制トンネリング構成

顧客の一般的な構成では、送信インターネット トラフィックを、オンプレミスの場所に強制的にフローさせる既定のルート (0.0.0.0/0) が定義されています。 これはお勧めしません。 レプリケーション トラフィックと Site Recovery サービスの通信は、Azure の境界を離れないようにします。 解決するには、レプリケーション トラフィックがオンプレミスに移動しないように、[こちらの IP 範囲](#outbound-connectivity-for-azure-site-recovery-ip-ranges)のユーザー定義ルート (UDR) を追加します。

### <a name="connectivity-between-the-target-and-on-premises-location"></a>ターゲットとオンプレミスの場所の間の接続性

ターゲットの場所とオンプレミスの場所の間の接続に関する、次のガイドラインに従ってください。
- アプリケーションがオンプレミスのマシンに接続する必要がある場合、または VPN/ExpressRoute を介してオンプレミスからアプリケーションに接続するクライアントがある場合は、ターゲットの Azure リージョンとオンプレミスのデータセンターの間に少なくとも 1 つの[サイト間接続](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)があることを確認します。

- ターゲットの Azure リージョンとオンプレミスのデータセンターの間のトラフィック フローが多くなることが予想される場合は、ターゲットの Azure リージョンとオンプレミスのデータセンターの間に別の [ExpressRoute 接続](../expressroute/expressroute-introduction.md)を作成します。

- フェールオーバー後に仮想マシンの IP を保持する必要がある場合は、ターゲット リージョンのサイト間/ExpressRoute 接続を切断状態のままにしてください。 これにより、ソース リージョンの IP 範囲とターゲット リージョンの IP 範囲の間で競合がなくなります。

### <a name="best-practices-for-expressroute-configuration"></a>ExpressRoute 構成のベスト プラクティス
ExpressRoute 構成については、次のベスト プラクティスに従ってください。

- ソース リージョンとターゲット リージョンの両方で、ExpressRoute 回線を作成する必要があります。 その後、次の接続を作成します。
  - ソース仮想ネットワークと ExpressRoute 回線の間。
  - ターゲット仮想ネットワークと ExpressRoute 回線の間。

- ExpressRoute Standard の一部として、同じ地理的リージョンに回線を作成できます。 さまざまな地理的リージョンに ExpressRoute 回線を作成するには、Azure ExpressRoute Premium が必要ですが、これには増分コストが発生します  (既に ExpressRoute Premium を使用している場合、追加コストはありません)。詳細については、[ExpressRoute の場所に関するドキュメント](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region)と「[ExpressRoute の価格](https://azure.microsoft.com/pricing/details/expressroute/)」を参照してください。

- ソース リージョンとターゲット リージョンでは、異なる IP 範囲を使用することをお勧めします。 ExpressRoute 回線は、同じ IP アドレス範囲の 2 つの Azure 仮想ネットワークに同時に接続することはできません。

- 両方のリージョンに同じ IP 範囲で仮想ネットワークを作成してから、両方のリージョンに ExpressRoute 回線を作成できます。 フェールオーバー イベントの場合は、ソース仮想ネットワークから回線を切断し、ターゲット仮想ネットワークで回線を接続します。

 >[!IMPORTANT]
 > プライマリ リージョンが完全にダウンしている場合は、切断操作が失敗することがあります。 この場合、ターゲット仮想ネットワークは、ExpressRoute 接続を取得できません。

## <a name="next-steps"></a>次のステップ
[Azure 仮想マシンをレプリケート](site-recovery-azure-to-azure.md)することで、ワークロードの保護を開始します。
