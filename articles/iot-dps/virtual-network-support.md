---
title: 仮想ネットワーク向けの Azure IoT Device Provisioning Service (DPS) サポート
description: Azure IoT Device Provisioning Service (DPS) で仮想ネットワーク接続パターンを使用する方法
services: iot-hub
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: wesmc
ms.openlocfilehash: f1409a931195d236b2729e629e4603c606137593
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94959783"
---
# <a name="azure-iot-hub-device-provisioning-service-dps-support-for-virtual-networks"></a>仮想ネットワーク向けの Azure IoT Hub Device Provisioning Service (DPS) サポート

この記事では、DPS を使用した IoT ハブでの IoT デバイス プロビジョニングの仮想ネットワーク (VNET) 接続パターンについて説明します。 このパターンは、顧客が所有する Azure VNET 内のデバイス、DPS、IoT ハブ間のプライベート接続を提供します。 

DPS が VNET で構成されているほとんどのシナリオでは、IoT Hub も同じ VNET で構成されます。 IoT Hub の VNET のサポートと構成の詳細については、[IoT Hub 仮想ネットワークのサポート](../iot-hub/virtual-network-support.md)に関するページを参照してください。



## <a name="introduction"></a>はじめに

既定では、DPS ホスト名は、インターネット経由でパブリックにルーティング可能な IP アドレスを持つパブリック エンドポイントにマップされます。 このパブリック エンドポイントは、すべての顧客に表示されます。 パブリック エンドポイントへのアクセスは、ワイドエリア ネットワークやオンプレミス ネットワークを経由して IoT デバイスによって実行できます。

いくつかの理由から、お客様は DPS などの Azure リソースへの接続を制限することをお勧めします。 これらの理由には以下のものが含まれます。

* パブリック インターネット経由での接続の露出を防止します。 IoT ハブと DPS リソースのネットワーク レベルの分離によって追加のセキュリティ レイヤーを導入することで、露出を減らすことができます。

* オンプレミスのネットワーク資産からのプライベート接続エクスペリエンスを有効にし、データとトラフィックが Azure バックボーン ネットワークに直接送信されるようにする。

* 機微なオンプレミス ネットワークからの抜き取り攻撃を防ぐ。 

* [プライベート エンドポイント](../private-link/private-endpoint-overview.md)を使用して確立された Azure 全体の接続パターンに従う。

接続を制限するための一般的な方法としては、[DPS IP フィルター規則](./iot-dps-ip-filtering.md)と[プライベート エンドポイント](../private-link/private-endpoint-overview.md)を使用した仮想ネットワーク (VNET) があります。 この記事の目的は、プライベート エンドポイントを使用した DPS の VNET アプローチについて説明することです。 

オンプレミス ネットワークで動作するデバイスは、[仮想プライベートネットワーク (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) または [ExpressRoute](https://azure.microsoft.com/services/expressroute/) プライベート ピアリングを使用して Azure の VNET に接続し、プライベート エンドポイントを介して DPS リソースにアクセスできます。 

プライベート エンドポイントは、Azure リソースにアクセス可能な、顧客所有の VNET 内に割り当てられたプライベート IP アドレスです。 DPS リソース向けのプライベート エンドポイントがあると、VNET 内で動作するデバイスが、パブリック エンドポイントへのトラフィックを許可することなく、DPS リソースによるプロビジョニングを要求できるようになります。


## <a name="prerequisites"></a>前提条件

続行する前に、確実に次の前提条件が満たされているようにしてください。

* DPS リソースは既に作成されており、IoT ハブにリンクされています。 新しい DPS リソースのセットアップに関するガイダンスについては、[Azure portal による IoT Hub Device Provisioning Service の設定](./quick-setup-auto-provision.md)に関するページを参照してください

* プライベート エンドポイントが作成されるサブネットを持つ Azure VNET をプロビジョニング済みである。 詳細については、[Azure CLI を使用した仮想ネットワークの作成](../virtual-network/quick-create-cli.md)に関するページを参照してください。

* オンプレミス ネットワークの内部で動作するデバイスの場合は、[仮想プライベート ネットワーク (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md)、または Azure VNET への [ExpressRoute](https://azure.microsoft.com/services/expressroute/) プライベート ピアリングを設定します。

## <a name="private-endpoint-limitations"></a>プライベート エンドポイントの制限事項

プライベート エンドポイントを使用する場合、DPS に関する次の制限事項に注意してください。

* DPS リソースとリンクされたハブが異なるクラウドにある場合、プライベート エンドポイントは DPS で動作しません。 たとえば、[Azure Government とグローバル Azure](../azure-government/documentation-government-welcome.md) です。

* 現時点では、DPS 向けの [Azure Functions のカスタム割り当てポリシー](how-to-use-custom-allocation-policies.md)は、Azure Functions が VNET とプライベート エンドポイントにロックダウンされている場合には機能しません。 

* 現在の DPS VNET のサポートは、DPS へのデータ イングレスのみが対象です。 データ エグレス (DPS から IoT Hub へのトラフィック) は、専用の VNET ではなく、内部のサービス間のメカニズムを使用します。 DPS と IoT Hub 間での VNET ベースの完全なエグレス ロックダウンのサポートは、現在使用できません。

* 最短待機時間割り当てポリシーを使用して、待機時間が最も短いデバイスを IoT ハブに割り当てます。 この割り当てポリシーは、仮想ネットワーク環境では信頼できません。 

## <a name="set-up-a-private-endpoint"></a>プライベート エンドポイントを設定する

プライベート エンドポイントを設定するには、これらの手順に従います。

1. [Azure portal](https://portal.azure.com/)で、DPS リソースを開き、 **[ネットワーク]** タブをクリックします。 **[プライベート エンドポイント接続]** と **[+ プライベート エンドポイント]** をクリックします。

    ![DPS 向けに新しいプライベート エンドポイントを作成する](./media/virtual-network-support/networking-tab-add-private-endpoint.png)

2. _プライベート エンドポイントの作成_ の基本ページで、次の表に記載されている情報を入力します。

    ![プライベート エンドポイントの作成の基本](./media/virtual-network-support/create-private-endpoint-basics.png)

    | フィールド | 値 |
    | :---- | :-----|
    | **サブスクリプション** | プライベート エンドポイントを含める Azure サブスクリプションを選択します。  |
    | **リソース グループ** | サービスが含まれるプライベート エンドポイントを選択または作成します |
    | **名前**       | プライベート エンドポイントの名前を入力します |
    | **リージョン**     | 選択したリージョンは、VNET を含むリージョンと同じである必要がありますが、DPS リソースと同じである必要はありません。 |

    ページの下部の **[次へ:リソース]** をクリックし、プライベート エンドポイントがポイントするリソースを構成します。

3. _プライベート エンドポイント リソースの作成_ ページで、次の表に記載されている情報を入力します。

    ![プライベート エンドポイントの作成のリソース](./media/virtual-network-support/create-private-endpoint-resource.png)

    | フィールド | 値 |
    | :---- | :-----|
    | **サブスクリプション**        | プライベート エンドポイントがポイントする DPS リソースを含む Azure サブスクリプションを選択します。  |
    | **リソースの種類**       | **Microsoft.Devices/ProvisioningServices** を選択します。 |
    | **リソース**            | プライベート エンドポイントがマップされる DPS リソースを選択します。 |
    | **ターゲット サブリソース** | **iotDps** を選択します。 |

    > [!TIP]
    > **リソース ID またはエイリアス設定による Azure リソースへの接続** についての情報は、この記事の [プライベート エンドポイントを要求する](#request-a-private-endpoint)セクションで説明しています。


    ページの下部の **[次へ:構成]** をクリックして、プライベート エンドポイントの VNET を構成します。

4. _プライベート エンドポイントの構成の作成_ ページで、プライベート エンドポイントの作成先となる仮想ネットワークとサブネットを指定します。
 
    ページの下部の **[次へ:タグ]** をクリックし、必要に応じて、リソースのタグを指定します。

    ![プライベート エンドポイントの構成](./media/virtual-network-support/create-private-endpoint-configuration.png)

6. **[確認および作成]** 、引き続き **[作成]** をクリックして、プライベート エンドポイント リソースを作成します。


## <a name="request-a-private-endpoint"></a>プライベート エンドポイントを要求する

リソース ID によって、DPS リソースに対してプライベート エンドポイントを要求できます。 この要求を行うには、リソースの所有者にリソース ID を提供してもらう必要があります。 

1. 次に示すように、リソース ID は DPS リソースの [プロパティ] タブに表示されます。

    ![DPS の [プロパティ] タブ](./media/virtual-network-support/dps-properties.png)

    > [!CAUTION]
    > リソース ID にはサブスクリプション ID が含まれていることに注意してください。 

2. リソース ID を取得したら、上記の [プライベート エンドポイントを設定する](#set-up-a-private-endpoint)のステップから _プライベート エンドポイントリソースを作成する_ ページのステップ 3 までに従います。 **[リソース ID またはエイリアスを使って Azure リソースに接続します]** をクリックし、次の表に示す情報を入力します。 

    | フィールド | 値 |
    | :---- | :-----|
    | **リソース ID またはエイリアス** | DPS リソースのソース ID を入力します。 |
    | **ターゲット サブリソース** | **iotDps** を入力します |
    | **要求メッセージ** | DPS リソース所有者への要求メッセージを入力します。<br>たとえば、次のように入力します。 <br>`Please approve this new private endpoint`<br>`for IoT devices in site 23 to access this DPS instance`  |

    ページの下部の **[次へ:構成]** をクリックして、プライベート エンドポイントの VNET を構成します。

3. _プライベート エンドポイントの構成の作成_ ページで、プライベート エンドポイントの作成先となる仮想ネットワークとサブネットを指定します。
 
    ページの下部の **[次へ:タグ]** をクリックし、必要に応じて、リソースのタグを指定します。

4. **[確認および作成]** 、引き続き **[作成]** をクリックして、プライベート エンドポイント 要求を作成します。

5. DPS 所有者は、[DPS ネットワーク] タブの **[プライベートエンドポイント接続]** 一覧でプライベート エンドポイント要求を確認できます。そのページで、所有者は次に示すように、プライベート エンドポイント要求の **承認** または **拒否** ができます。

    ![DPS の承認](./media/virtual-network-support/approve-dps-private-endpoint.png)


## <a name="pricing-private-endpoints"></a>プライベート エンドポイントの料金

料金の詳細については、「[Azure Private Link の料金](https://azure.microsoft.com/pricing/details/private-link)」をご覧ください。



## <a name="next-steps"></a>次のステップ

DPS セキュリティ機能の詳細について、下記のリンク使用してください。

* [Security](./concepts-service.md#attestation-mechanism)
* [TLS 1.2 のサポート](tls-support.md)