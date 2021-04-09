---
title: Azure Site Recovery を使用した Azure VM のディザスター リカバリーのネットワークについて
description: Azure Site Recovery を使用した Azure VM のレプリケーション用のネットワークの概要について示します。
services: site-recovery
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 3/13/2020
ms.author: harshacs
ms.openlocfilehash: b9fdaf8a0791570ecee402442c5faefe2f70a22b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92370442"
---
# <a name="about-networking-in-azure-vm-disaster-recovery"></a>Azure VM ディザスター リカバリーのネットワークについて



この記事では、[Azure Site Recovery](site-recovery-overview.md) を使用して、リージョン間で Azure VM をレプリケートして復旧するときの、ネットワーク ガイダンスについて説明します。

## <a name="before-you-start"></a>開始する前に

[このシナリオ](azure-to-azure-architecture.md)に対して、Site Recovery でどのようにディザスター リカバリーを提供するかについて説明します。

## <a name="typical-network-infrastructure"></a>一般的なネットワーク インフラストラクチャ

次の図は、Azure VM で実行しているアプリケーションの一般的な Azure 環境を示しています。

![Azure VM で実行されるアプリケーションの一般的な Azure 環境を示す図。](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

オンプレミス ネットワークから Azure への接続に Azure ExpressRoute または VPN 接続を使用している場合、環境は次のようになります。

![customer-environment](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

通常、ネットワークは、ファイアウォールやネットワーク セキュリティ グループ (NSG) を使用して保護されます。 ネットワーク接続を制御するには、サービス タグを使用する必要があります。 NSG では、送信接続を制御するために複数のサービス タグを許可する必要があります。

>[!IMPORTANT]
> 認証済みプロキシを使用してネットワーク接続を制御することは、Site Recoery ではサポートされておらず、レプリケーションを有効にすることはできません。

>[!NOTE]
>- IP アドレス ベースのフィルター処理を実行して、送信接続を制御することはできません。
>- 送信接続を制御するために Azure Site Recovery IP アドレスを Azure ルーティング テーブルに追加しないでください。

## <a name="outbound-connectivity-for-urls"></a>URL に対する送信接続

送信接続を制御するために URL ベースのファイアウォール プロキシを使用している場合、以下の Site Recovery の URL を許可してください。

**URL** | **詳細**
--- | ---
*.blob.core.windows.net | VM からソース リージョンのキャッシュ ストレージ アカウントにデータを書き込むことができるようにするために必要です。 お使いの VM のすべてのキャッシュ ストレージ アカウントを把握している場合、*.blob.core.windows.net の代わりに、特定のストレージ アカウントの URL (例: cache1.blob.core.windows.net および cache2.blob.core.windows.net) へのアクセスを許可することができます。
login.microsoftonline.com | Site Recovery サービス URL に対する承認と認証に必要です。
*.hypervrecoverymanager.windowsazure.com | VM から Site Recovery サービス通信を実行できるようにするために必要です。
*.servicebus.windows.net | VM から Site Recovery の監視および診断データを書き込むことができるようにするために必要です。
*.vault.azure.net | ADE が有効な仮想マシンのレプリケーションをポータルを介して有効にするためのアクセスを許可します
*.automation.ext.azure.com | レプリケートされる項目に対してモビリティ エージェントの自動アップグレードをポータルを介して有効にすることを許可します

## <a name="outbound-connectivity-using-service-tags"></a>サービスタグを使用した送信接続

NSG を使用して送信接続を制御すると同時に、次のサービス タグを許可する必要があります。

- ソース リージョンのストレージ アカウントの場合:
    - ソース リージョンに対して[ストレージ サービス タグ](../virtual-network/network-security-groups-overview.md#service-tags)に基づく NSG ルールを作成します。
    - VM からキャッシュ ストレージ アカウントにデータを書き込むことができるように、これらのアドレスを許可します。
- AAD に対応するすべての IP アドレスへのアクセスを許可するには、[Azure Active Directory (AAD) サービス タグ](../virtual-network/network-security-groups-overview.md#service-tags) ベースの NSG ルールを作成します。
- EventsHub サービス タグ ベースの NSG ルールをターゲット リージョンに対して作成し、Site Recovery 監視へのアクセスを許可します。
- 任意のリージョンでの Site Recovery サービスへのアクセスを許可するために、AzureSiteRecovery サービス タグ ベースの NSG ルールを作成します。
- AzureKeyVault サービス タグ ベースの NSG ルールを作成します。 これは、ADE が有効になっている仮想マシンのレプリケーションを、ポータルを介して有効にする場合にのみ必要です。
- GuestAndHybridManagement サービス タグ ベースの NSG ルールを作成します。 これは、レプリケートされる項目に対してモビリティ エージェントの自動アップグレードをポータルを介して有効にする場合にのみ必要です。
- 必要な NSG ルールをテスト NSG に作成し、問題がないことを確認してから、運用環境の NSG にルールを作成することをお勧めします。

## <a name="example-nsg-configuration"></a>NSG 構成の例

この例は、レプリケートする VM に対して NSG ルールを構成する方法を示しています。

- NSG ルールを使用して送信接続を制御している場合は、必要なすべての IP アドレス範囲のポート 443 に対して "HTTPS 送信を許可" ルールを使用します。
- この例では、VM ソースの場所は "米国東部" で、ターゲットの場所は "米国中部" であると仮定します。

### <a name="nsg-rules---east-us"></a>NSG ルール - 米国東部

1. 次のスクリーンショットで示すように、NSG 上の "Storage.EastUS" に対して送信方向の HTTPS (443) セキュリティ規則を作成します。

      ![Storage.EastUS のネットワーク セキュリティ グループの [送信セキュリティ規則の追加] を示すスクリーンショット。](./media/azure-to-azure-about-networking/storage-tag.png)

2. 次のスクリーンショットで示すように、NSG 上の "AzureActiveDirectory" に対して送信方向の HTTPS (443) セキュリティ規則を作成します。

      ![Azure AD のネットワーク セキュリティ グループの [送信セキュリティ規則の追加] を示すスクリーンショット。](./media/azure-to-azure-about-networking/aad-tag.png)

3. 上記のセキュリティ規則と同様に、ターゲットの場所に対応する NSG 上の "EventHub.CentralUS" に対して送信方向の HTTPS (443) セキュリティ規則を作成します。 これにより、Site Recovery 監視にアクセスできるようになります。

4. NSG 上の "AzureSiteRecovery" に対して送信方向の HTTPS (443) セキュリティ規則を作成します。 これにより、任意のリージョンの Site Recovery Service にアクセスできます。

### <a name="nsg-rules---central-us"></a>NSG ルール - 米国中部

フェールオーバー後にターゲット リージョンからソース リージョンへのレプリケーションが有効になるようにするには、次のルールが必要です。

1. NSG 上の "Storage.CentralUS" に対して、送信方向の HTTPS (443) セキュリティ ルールを作成します。

2. NSG 上の "AzureActiveDirectory" に対して送信方向の HTTPS (443) セキュリティ規則を作成します。

3. 上記のセキュリティ規則と同様に、ソースの場所に対応する NSG 上の "EventHub.EastUS" に対して送信方向の HTTPS (443) セキュリティ規則を作成します。 これにより、Site Recovery 監視にアクセスできるようになります。

4. NSG 上の "AzureSiteRecovery" に対して送信方向の HTTPS (443) セキュリティ規則を作成します。 これにより、任意のリージョンの Site Recovery Service にアクセスできます。

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

0.0.0.0/0 アドレス プレフィックスの Azure の既定のシステム ルートを [カスタム ルート](../virtual-network/virtual-networks-udr-overview.md#custom-routes)でオーバーライドし、VM トラフィックをオンプレミス ネットワーク仮想アプライアンス (NVA) に転送することもできますが、この構成は Site Recovery レプリケーションにはお勧めしません。 カスタム ルートを使用している場合、レプリケーション トラフィックが Azure 境界から外に出ないように、"ストレージ" 用の仮想ネットワーク内に[仮想ネットワーク サービス エンドポイントを作成する](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage)ことをお勧めします。

## <a name="next-steps"></a>次の手順
- [Azure 仮想マシンをレプリケート](./azure-to-azure-quickstart.md)することで、ワークロードの保護を開始します。
- Azure 仮想マシンのフェールオーバーの [IP アドレスの保持](site-recovery-retain-ip-azure-vm-failover.md)について詳しく学習します。
- [ExpressRoute を使用した Azure 仮想マシン](azure-vm-disaster-recovery-with-expressroute.md)のディザスター リカバリーについて詳しく学習します。