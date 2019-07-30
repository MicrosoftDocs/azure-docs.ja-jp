---
title: Azure Site Recovery を使用した Azure から Azure へのディザスター リカバリーのネットワークについて | Microsoft Docs
description: Azure Site Recovery を使用した Azure VM のレプリケーション用のネットワークの概要について示します。
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 3/29/2019
ms.author: sutalasi
ms.openlocfilehash: 844563e03529e472624b35d2b545c3e432e4ea17
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876293"
---
# <a name="about-networking-in-azure-to-azure-replication"></a>Azure から Azure へのレプリケーションのネットワークについて



この記事では、[Azure Site Recovery](site-recovery-overview.md) を使用して、リージョン間で Azure VM をレプリケートして復旧するときの、ネットワーク ガイダンスについて説明します。

## <a name="before-you-start"></a>開始する前に

[このシナリオ](azure-to-azure-architecture.md)に対して、Site Recovery でどのようにディザスター リカバリーを提供するかについて説明します。

## <a name="typical-network-infrastructure"></a>一般的なネットワーク インフラストラクチャ

次の図は、Azure VM で実行しているアプリケーションの一般的な Azure 環境を示しています。

![customer-environment](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

オンプレミス ネットワークから Azure への接続に Azure ExpressRoute または VPN 接続を使用している場合、環境は次のようになります。

![customer-environment](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

通常、ネットワークは、ファイアウォールやネットワーク セキュリティ グループ (NSG) を使用して保護されます。 ファイアウォールでは、URL や IP ベースのホワイトリスト登録を使用して、ネットワーク接続を制御します。 NSG では、IP アドレスの範囲を使用するルールを指定して、ネットワーク接続を制御します。

>[!IMPORTANT]
> 認証済みプロキシを使用してネットワーク接続を制御することは、Site Recoery ではサポートされておらず、レプリケーションを有効にすることはできません。


## <a name="outbound-connectivity-for-urls"></a>URL に対する送信接続

送信接続を制御するために URL ベースのファイアウォール プロキシを使用している場合、以下の Site Recovery の URL を許可してください。


**URL** | **詳細**  
--- | ---
*.blob.core.windows.net | VM からソース リージョンのキャッシュ ストレージ アカウントにデータを書き込むことができるようにするために必要です。 お使いの VM のすべてのキャッシュ ストレージ アカウントを把握している場合、*.blob.core.windows.net の代わりに、特定のストレージ アカウントの URL (例: cache1.blob.core.windows.net および cache2.blob.core.windows.net) をホワイトリストに登録できます。
login.microsoftonline.com | Site Recovery サービス URL に対する承認と認証に必要です。
*.hypervrecoverymanager.windowsazure.com | VM から Site Recovery サービス通信を実行できるようにするために必要です。 ファイアウォール プロキシによって IP がサポートされている場合は、該当する 'Site Recovery IP' を使用できます。
*.servicebus.windows.net | VM から Site Recovery の監視および診断データを書き込むことができるようにするために必要です。 ファイアウォール プロキシによって IP がサポートされている場合は、該当する 'Site Recovery Monitoring IP' を使用できます。

## <a name="outbound-connectivity-for-ip-address-ranges"></a>IP アドレス範囲に対する送信接続

IP ベースのファイアウォール プロキシ、または NSG ルールを使用して送信接続を制御している場合は、次の IP 範囲を許可する必要があります。

- ソース リージョンのストレージ アカウントに対応するすべての IP アドレス範囲
    - ソース リージョンに対して[ストレージ サービス タグ](../virtual-network/security-overview.md#service-tags)に基づく NSG ルールを作成します。
    - VM からキャッシュ ストレージ アカウントにデータを書き込むことができるように、これらのアドレスを許可します。
- AAD に対応するすべての IP アドレスへのアクセスを許可するには、[Azure Active Directory (AAD) サービス タグ](../virtual-network/security-overview.md#service-tags) ベースの NSG ルールを作成します。
    - 今後、新しいアドレスが Azure Active Directory (AAD) に追加された場合は、新しい NSG ルールを作成する必要があります。
- Site Recovery サービス エンドポイント IP アドレス - [XML ファイル](https://aka.ms/site-recovery-public-ips)で入手できます。ターゲットの場所によって異なります。
- 必要な NSG ルールをテスト NSG に作成し、問題がないことを確認してから、運用環境の NSG にルールを作成することをお勧めします。


Site Recovery IP アドレスの範囲は次のとおりです。

   **ターゲット** | **Site Recovery IP** |  **Site Recovery 監視 IP**
   --- | --- | ---
   東アジア | 52.175.17.132 | 13.94.47.61
   東南アジア | 52.187.58.193 | 13.76.179.223
   インド中部 | 52.172.187.37 | 104.211.98.185
   インド南部 | 52.172.46.220 | 104.211.224.190
   米国中北部 | 23.96.195.247 | 168.62.249.226
   北ヨーロッパ | 40.69.212.238 | 52.169.18.8
   西ヨーロッパ | 52.166.13.64 | 40.68.93.145
   East US | 13.82.88.226 | 104.45.147.24
   米国西部 | 40.83.179.48 | 104.40.26.199
   米国中南部 | 13.84.148.14 | 104.210.146.250
   米国中部 | 40.69.144.231 | 52.165.34.144
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
   韓国南部 | 52.231.198.185 | 52.231.200.144
   フランス中部 | 52.143.138.106 | 52.143.136.55
   フランス南部 | 52.136.139.227 |52.136.136.62
   オーストラリア中部| 20.36.34.70 | 20.36.46.142
   オーストラリア中部 2| 20.36.69.62 | 20.36.74.130
   南アフリカ西部 | 102.133.72.51 | 102.133.26.128
   南アフリカ北部 | 102.133.160.44 | 102.133.154.128
   米国政府バージニア州 | 52.227.178.114 | 23.97.0.197
   US Gov アイオワ | 13.72.184.23 | 23.97.16.186
   米国政府アリゾナ | 52.244.205.45 | 52.244.48.85
   米国政府テキサス | 52.238.119.218 | 52.238.116.60
   US DoD East | 52.181.164.103 | 52.181.162.129
   US DoD Central | 52.182.95.237 | 52.182.90.133
## <a name="example-nsg-configuration"></a>NSG 構成の例

この例は、レプリケートする VM に対して NSG ルールを構成する方法を示しています。

- NSG ルールを使用して送信接続を制御している場合は、必要なすべての IP アドレス範囲のポート 443 に対して "HTTPS 送信を許可" ルールを使用します。
- この例では、VM ソースの場所は "米国東部" で、ターゲットの場所は "米国中央部" であると仮定します。

### <a name="nsg-rules---east-us"></a>NSG ルール - 米国東部

1. 次のスクリーンショットで示すように、NSG 上の "Storage.EastUS" に対して送信方向の HTTPS (443) セキュリティ規則を作成します。

      ![storage-tag](./media/azure-to-azure-about-networking/storage-tag.png)

2. 次のスクリーンショットで示すように、NSG 上の "AzureActiveDirectory" に対して送信方向の HTTPS (443) セキュリティ規則を作成します。

      ![aad-tag](./media/azure-to-azure-about-networking/aad-tag.png)

3. ターゲットの場所に対応する Site Recovery IP に対して、送信方向の HTTPS (443) ルールを作成します。

   **Location** | **Site Recovery IP アドレス** |  **Site Recovery 監視 IP アドレス**
    --- | --- | ---
   米国中部 | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>NSG ルール - 米国中央部

フェールオーバー後にターゲット リージョンからソース リージョンへのレプリケーションが有効になるようにするには、次のルールが必要です。

1. NSG 上の "Storage.CentralUS" に対して、送信方向の HTTPS (443) セキュリティ ルールを作成します。

2. NSG 上の "AzureActiveDirectory" に対して送信方向の HTTPS (443) セキュリティ規則を作成します。

3. ソースの場所に対応する Site Recovery IP に対して、送信方向の HTTPS (443) ルールを作成します。

   **Location** | **Site Recovery IP アドレス** |  **Site Recovery 監視 IP アドレス**
    --- | --- | ---
   米国中部 | 13.82.88.226 | 104.45.147.24

## <a name="network-virtual-appliance-configuration"></a>ネットワーク仮想アプライアンスの構成

ネットワーク仮想アプライアンス (NVA) を使って VM からの送信方向のネットワーク トラフィックを制御する場合、すべてのレプリケーション トラフィックが NVA を通過すると、アプライアンスが調整される可能性があります。 レプリケーション トラフィックが NVA に送られないように、"ストレージ" 用の仮想ネットワーク内にネットワーク サービス エンドポイントを作成することをお勧めします。

### <a name="create-network-service-endpoint-for-storage"></a>ストレージ用のネットワーク サービス エンドポイントを作成する
レプリケーション トラフィックが Azure 境界から外に出ないように、"ストレージ" 用の仮想ネットワーク内にネットワーク サービス エンドポイントを作成することができます。

- お使いの Azure 仮想ネットワークを選び、[サービス エンドポイント] をクリックします

    ![storage-endpoint](./media/azure-to-azure-about-networking/storage-service-endpoint.png)

- [追加] をクリックして、[サービス エンドポイントの追加] タブを開きます
- [サービス] で "Microsoft.Storage" を選び、[サブネット] フィールドで必要なサブネットを選んで、[追加] をクリックします

>[!NOTE]
>仮想ネットワークへのアクセスを、ASR に使用されていストレージ アカウントに制限しないでください。 "すべてのネットワーク" からのアクセスを許可する必要があります

### <a name="forced-tunneling"></a>強制トンネリング

0\.0.0.0/0 アドレス プレフィックスの Azure の既定のシステム ルートを [カスタム ルート](../virtual-network/virtual-networks-udr-overview.md#custom-routes)でオーバーライドし、VM トラフィックをオンプレミス ネットワーク仮想アプライアンス (NVA) に転送することもできますが、この構成は Site Recovery レプリケーションにはお勧めしません。 カスタム ルートを使用している場合、レプリケーション トラフィックが Azure 境界から外に出ないように、"ストレージ" 用の仮想ネットワーク内に[仮想ネットワーク サービス エンドポイントを作成する](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage)ことをお勧めします。

## <a name="next-steps"></a>次の手順
- [Azure 仮想マシンをレプリケート](site-recovery-azure-to-azure.md)することで、ワークロードの保護を開始します。
- Azure 仮想マシンのフェールオーバーの [IP アドレスの保持](site-recovery-retain-ip-azure-vm-failover.md)について詳しく学習します。
- [ExpressRoute を使用した Azure 仮想マシン](azure-vm-disaster-recovery-with-expressroute.md)のディザスター リカバリーについて詳しく学習します。
