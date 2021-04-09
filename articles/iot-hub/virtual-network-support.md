---
title: 仮想ネットワークの Azure IoT Hub サポート
description: IoT Hub で仮想ネットワークの接続パターンを使用する方法
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: jlian
ms.openlocfilehash: 1e28c7767868904fb20ae6d27c6aea9e7077eb62
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98630295"
---
# <a name="iot-hub-support-for-virtual-networks-with-private-link-and-managed-identity"></a>Private Link とマネージド ID を使用した仮想ネットワークの IoT Hub サポート

既定では、IoT Hub のホスト名は、インターネット経由でパブリックにルーティング可能な IP アドレスを持つパブリック エンドポイントにマップされます。 この IoT Hub パブリック エンドポイントはさまざまな顧客によって共有され、IoT デバイスはワイドエリア ネットワークやオンプレミス ネットワーク経由ですべてアクセスできます。

![IoT Hub のパブリック エンドポイント](./media/virtual-network-support/public-endpoint.png)

[メッセージのルーティング](./iot-hub-devguide-messages-d2c.md)、[ファイルのアップロード](./iot-hub-devguide-file-upload.md)、[デバイスの一括インポートとエクスポート](./iot-hub-bulk-identity-mgmt.md)などの IoT Hub 機能では、パブリック エンドポイント経由での IoT Hub から顧客所有の Azure リソースへの接続も必要になります。 IoT Hub から顧客リソースへのエグレス トラフィックは、これらの接続パスでまとめて構成されています。

自分が所有して運用する VNet を介した Azure リソース (IoT Hub を含む) への接続を制限したい場合があります。 これらの理由には以下のものが含まれます。

* パブリック インターネットへの接続の露出を防ぐことにより、IoT Hub にネットワークの分離を導入する。

* オンプレミスのネットワーク資産からのプライベート接続エクスペリエンスを有効にし、データとトラフィックが Azure バックボーン ネットワークに直接送信されるようにする。

* 機微なオンプレミス ネットワークからの抜き取り攻撃を防ぐ。 

* [プライベート エンドポイント](../private-link/private-endpoint-overview.md)を使用して確立された Azure 全体の接続パターンに従う。

この記事では、IoT Hub へのイングレス接続に [Azure Private Link](../private-link/private-link-overview.md) を使用し、IoT Hub から他の Azure リソースへのエグレス接続に信頼された Microsoft サービスの例外を使用して、これらの目標を達成する方法について説明します。

## <a name="ingress-connectivity-to-iot-hub-using-azure-private-link"></a>Azure Private Link を使用した IoT Hub へのイングレス接続

プライベート エンドポイントは、Azure リソースに到達可能な、顧客所有の VNet 内に割り当てられたプライベート IP アドレスです。 Azure Private Link を使用すると、IoT Hub 用にプライベート エンドポイントを設定して、IoT Hub のパブリック エンドポイントにトラフィックを送信しなくても、VNet 内のサービスが IoT Hub に到達できるようにすることができます。 同様に、オンプレミスのデバイスでは [仮想プライベート ネットワーク (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) または [ExpressRoute](https://azure.microsoft.com/services/expressroute/) ピアリングを使用して、VNet と (プライベート エンドポイント経由で) IoT Hub に接続することができます。 その結果、[IoT Hub の IP フィルター](./iot-hub-ip-filtering.md)または[パブリック ネットワーク アクセスの切り替え](iot-hub-public-network-access.md)を使用することで、IoT Hub のパブリック エンドポイントへの接続を制限または完全にブロックすることができます。 この方法では、デバイスのプライベート エンドポイントを使用して、ハブへの接続を維持します。 この設定の主な対象は、オンプレミス ネットワーク内のデバイスです。 ワイドエリア ネットワークにデプロイされているデバイスには、この設定はお勧めできません。

![IoT Hub 仮想ネットワークのエグレス](./media/virtual-network-support/virtual-network-ingress.png)

続行する前に、確実に次の前提条件が満たされているようにしてください。

* プライベート エンドポイントが作成されるサブネットを持つ [Azure VNet を作成している](../virtual-network/quick-create-portal.md)。

* オンプレミス ネットワークの内部で動作するデバイスの場合は、[仮想プライベート ネットワーク (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md)、または Azure VNET への [ExpressRoute](https://azure.microsoft.com/services/expressroute/) プライベート ピアリングを設定する。

### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>IoT Hub のイングレス用のプライベート エンドポイントを設定する

プライベート エンドポイントは、IoT Hub デバイス API (device-to-cloud メッセージなど) とサービス API (デバイスの作成や更新など) で機能します。

1. Azure portal で、 **[ネットワーク]** 、 **[プライベート エンドポイント接続]** の順に選択し、 **[+ プライベート エンドポイント]** をクリックします。

    :::image type="content" source="media/virtual-network-support/private-link.png" alt-text="IoT Hub 用にプライベート エンドポイントを追加する場所を示すスクリーンショット":::

1. サブスクリプション、リソース グループ、名前、およびリージョンを指定して、新しいプライベート エンドポイントを作成します。 理想としては、プライベート エンドポイントをハブと同じリージョンに作成することをお勧めします。

1. **[次へ: リソース]** をクリックし、IoT Hub リソースのサブスクリプションを指定し、リソースの種類として **"Microsoft.Devices/IotHubs"** 、**リソース** として IoT Hub の名前、ターゲット サブリソースとして **iotHub** を選択します。

1. **[次へ: 構成]** をクリックし、プライベート エンドポイントの作成先となる仮想ネットワークとサブネットを指定します。 必要に応じて、Azure プライベート DNS ゾーンと統合するオプションを選択します。

1. **[次へ: タグ]** をクリックし、必要に応じて、リソースのタグを指定します。

1. **[確認および作成]** をクリックして、プライベート リンク リソースを作成します。

### <a name="built-in-event-hub-compatible-endpoint"></a>組み込みのイベント ハブ互換エンドポイント 

[組み込みのイベント ハブ互換エンドポイント](iot-hub-devguide-messages-read-builtin.md)には、プライベート エンドポイント経由でもアクセスできます。 プライベート リンクを構成すると、組み込みエンドポイントの追加のプライベート エンドポイント接続が表示されます。 FQDN に `servicebus.windows.net` が含まれているものです。

:::image type="content" source="media/virtual-network-support/private-built-in-endpoint.png" alt-text="それぞれ IoT Hub プライベート リンクが指定されている、2 つのプライベート エンドポイントを示す画像":::

IoT Hub の [IP フィルター](iot-hub-ip-filtering.md)によって、必要に応じて、組み込みのエンドポイントへのパブリック アクセスを制御できます。 

IoT Hub へのパブリック ネットワーク アクセスを完全にブロックするには、[パブリック ネットワーク アクセスをオフにする](iot-hub-public-network-access.md)か、IP フィルターを使用してすべての IP をブロックし、組み込みのエンドポイントに規則を適用するオプションを選択します。

### <a name="pricing-for-private-link"></a>Private Link の価格

料金の詳細については、「[Azure Private Link の料金](https://azure.microsoft.com/pricing/details/private-link)」をご覧ください。

## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>IoT Hub から他の Azure リソースへのエグレス接続

IoT Hub は、リソースのパブリック エンドポイントを経由した[メッセージのルーティング](./iot-hub-devguide-messages-d2c.md)、[ファイルのアップロード](./iot-hub-devguide-file-upload.md)、[デバイスの一括インポートとエクスポート](./iot-hub-bulk-identity-mgmt.md)のために Azure BLOB ストレージ、イベント ハブ、Service Bus のリソースに接続できます。 リソースを VNet にバインドすると、リソースへの接続が既定でブロックされます。 その結果、この構成では、IoT Hub がリソースにデータを送信できなくなります。 この問題を解決するには、**信頼された Microsoft サービス** オプションを使用して、IoT Hub リソースからストレージ アカウント、イベント ハブ、または Service Bus のリソースへの接続を有効にします。

### <a name="turn-on-managed-identity-for-iot-hub"></a>IoT Hub のマネージド ID を有効にする

他のサービスが IoT Hub を信頼された Microsoft サービスとして検出できるようにするには、システムに割り当てられたマネージド ID が必要です。

1. IoT Hub ポータルで **[ID]** に移動します。

1. **[状態]** の下で **[オン]** を選択し、 **[保存]** をクリックします。

    :::image type="content" source="media/virtual-network-support/managed-identity.png" alt-text="IoT Hub のマネージド ID を有効にする方法を示すスクリーンショット":::

Azure CLI を使用してマネージド ID を有効にするには:

```azurecli-interactive
az iot hub update --name <iot-hub-resource-name> --set identity.type="SystemAssigned"
```

### <a name="assign-managed-identity-to-your-iot-hub-at-creation-time-using-arm-template"></a>ARM テンプレートを使用して、作成時にマネージド ID を IoT Hub に割り当てる

リソースのプロビジョニング時にマネージド ID を IoT Hub に割り当てるには、次の ARM テンプレートを使用します。 この ARM テンプレートには、2 つの必須リソースがあり、これらはどちらも、`Microsoft.Devices/IotHubs/eventHubEndpoints/ConsumerGroups` などの他のリソースを作成する前にデプロイする必要があります。 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Devices/IotHubs",
      "apiVersion": "2020-03-01",
      "name": "<provide-a-valid-resource-name>",
      "location": "<any-of-supported-regions>",
      "identity": {
        "type": "SystemAssigned"
      },
      "sku": {
        "name": "<your-hubs-SKU-name>",
        "tier": "<your-hubs-SKU-tier>",
        "capacity": 1
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-02-01",
      "name": "createIotHub",
      "dependsOn": [
        "[resourceId('Microsoft.Devices/IotHubs', '<provide-a-valid-resource-name>')]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "0.9.0.0",
          "resources": [
            {
              "type": "Microsoft.Devices/IotHubs",
              "apiVersion": "2020-03-01",
              "name": "<provide-a-valid-resource-name>",
              "location": "<any-of-supported-regions>",
              "identity": {
                "type": "SystemAssigned"
              },
              "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
              }
            }
          ]
        }
      }
    }
  ]
}
```

リソースの `name`、`location`、`SKU.name`、`SKU.tier` の値を置き換えた後、以下の Azure CLI を使用して、既存のリソース グループにリソースをデプロイすることができます。

```azurecli-interactive
az deployment group create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

リソースが作成された後、以下の Azure CLI を使用して、ハブに割り当てられたマネージド サービス ID を取得できます。

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

### <a name="pricing-for-managed-identity"></a>マネージド ID の価格

信頼された Microsoft ファースト パーティ サービスの例外機能は、無料でご利用いただけます。 プロビジョニング済みのストレージ アカウント、Event Hubs、または Service Bus リソースの料金は個別に適用されます。

### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>ルーティングのためのストレージ アカウント エンドポイントへのエグレス接続

IoT Hub は、顧客所有のストレージ アカウントにメッセージをルーティングできます。 ファイアウォールの制限が適用されているときに、ルーティング機能でストレージ アカウントにアクセスできるようにするには、IoT Hub に[マネージド ID](#turn-on-managed-identity-for-iot-hub) が必要です。 マネージド ID がプロビジョニングされたら、以下の手順に従って、ストレージ アカウントにアクセスするためにハブのリソース ID に Azure RBAC アクセス許可を付与します。

1. Azure portal で、ストレージ アカウントの **[アクセス制御 (IAM)]** タブに移動し、 **[ロールの割り当てを追加する]** セクションの下にある **[追加]** をクリックします。

2. **ロール** として **[ストレージ BLOB データ共同作成者]** ([共同作成者やストレージ アカウント共同作成者 "*ではなく*"](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)) を選択し、**アクセスの割当先** として **[Azure AD のユーザー、グループ、サービス プリンシパル]** を選んでから、ドロップダウン リストで IoT Hub のリソース名を選択します。 **[保存]** ボタンをクリックします。

3. ストレージ アカウントの **[ファイアウォールと仮想ネットワーク]** タブに移動し、 **[Allow access from selected networks]\(選択したネットワークからのアクセスを許可する\)** オプションを有効にします。 **[例外]** 一覧で、 **[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** のボックスをオンにします。 **[保存]** ボタンをクリックします。

4. IoT Hub のリソース ページで、 **[メッセージ ルーティング]** タブに移動します。

5. **[カスタム エンドポイント]** セクションに移動し、 **[追加]** をクリックします。 エンドポイントの種類として、 **[ストレージ]** を選択します。

6. 表示されるページで、エンドポイントの名前を指定し、BLOB ストレージで使用する予定のコンテナーを選択して、エンコード、およびファイル名の形式を指定します。 ストレージ エンドポイントに対する **[認証の種類]** として、 **[ID ベース]** を選択します。 **[作成]** ボタンをクリックします。

これで、カスタム ストレージ エンドポイントが、ハブのシステム割り当て ID を使用するように設定され、ファイアウォールの制限に関係なく、ストレージ リソースにアクセスするためのアクセス許可が付与されました。 このエンドポイントを使用して、ルーティング規則を設定できるようになりました。

### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>ルーティングのための Event Hubs エンドポイントへのエグレス接続

IoT Hub は、顧客所有の Event Hubs 名前空間にメッセージをルーティングするように構成できます。 ファイアウォールの制限が適用されているときに、ルーティング機能でイベント ハブ リソースにアクセスできるようにするには、IoT Hub にマネージド ID が必要です。 マネージド ID が作成されたら、以下の手順に従って、イベント ハブにアクセスするためにハブのリソース ID に Azure RBAC アクセス許可を付与します。

1. Azure portal で、Event Hubs の **[アクセス制御 (IAM)]** タブに移動し、 **[ロールの割り当てを追加する]** セクションの下にある **[追加]** をクリックします。

2. **ロール** として **[Event Hubs Data Sender]\(Event Hubs のデータ送信者\)** 、**アクセスの割当先** として **[Azure AD のユーザー、グループ、サービス プリンシパル]** を選んでから、ドロップダウン リストで IoT Hub のリソース名を選択します。 **[保存]** ボタンをクリックします。

3. Event Hubs の **[ファイアウォールと仮想ネットワーク]** タブに移動し、 **[Allow access from selected networks]\(選択したネットワークからのアクセスを許可する\)** オプションを有効にします。 **[例外]** 一覧で、 **[Allow trusted Microsoft services to access event hubs]\(信頼された Microsoft サービスによる Event Hubs に対するアクセスを許可します\)** のボックスをオンにします。 **[保存]** ボタンをクリックします。

4. IoT Hub のリソース ページで、 **[メッセージ ルーティング]** タブに移動します。

5. **[カスタム エンドポイント]** セクションに移動し、 **[追加]** をクリックします。 エンドポイントの種類として、 **[Event Hubs]** を選択します。

6. 表示されたページで、エンドポイントの名前を指定し、Event Hubs の名前空間とインスタンスを選択します。 **[認証の種類]** として、 **[ID ベース]** を選択し、 **[作成]** ボタンをクリックします。

これで、カスタム Event Hubs エンドポイントが、ハブのシステム割り当て ID を使用するように設定され、ファイアウォールの制限に関係なく、Event Hubs リソースにアクセスするためのアクセス許可が付与されました。 このエンドポイントを使用して、ルーティング規則を設定できるようになりました。

### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>ルーティングのための Service Bus エンドポイントへのエグレス接続

IoT Hub は、顧客所有の Service Bus 名前空間にメッセージをルーティングするように構成できます。 ファイアウォールの制限が適用されているときに、ルーティング機能で Service Bus リソースにアクセスできるようにするには、IoT Hub にマネージド ID が必要です。 マネージド ID がプロビジョニングされたら、以下の手順に従って、Service Bus にアクセスするためにハブのリソース ID に Azure RBAC アクセス許可を付与します。

1. Azure portal で、Service Bus の **[アクセス制御 (IAM)]** タブに移動し、 **[ロールの割り当てを追加する]** セクションの下にある **[追加]** をクリックします。

2. **ロール** として **[Service bus Data Sender]\(Service Bus のデータ送信者\)** 、**アクセスの割当先** として **[Azure AD のユーザー、グループ、サービス プリンシパル]** を選んでから、ドロップダウン リストで IoT Hub のリソース名を選択します。 **[保存]** ボタンをクリックします。

3. Service Bus の **[ファイアウォールと仮想ネットワーク]** タブに移動し、 **[Allow access from selected networks]\(選択したネットワークからのアクセスを許可する\)** オプションを有効にします。 **[例外]** 一覧で、 **[Allow trusted Microsoft services to access this service bus]\(信頼された Microsoft サービスによるこの Service Bus に対するアクセスを許可します\)** のボックスをオンにします。 **[保存]** ボタンをクリックします。

4. IoT Hub のリソース ページで、 **[メッセージ ルーティング]** タブに移動します。

5. **[カスタム エンドポイント]** セクションに移動し、 **[追加]** をクリックします。 エンドポイントの種類として、 **[Service Bus キュー]** または **[Service Bus トピック]** (該当する場合) を選択します。

6. 表示されたページで、エンドポイントの名前を指定し、Service Bus の名前空間とキューまたはトピック (該当する場合) を選択します。 **[認証の種類]** として、 **[ID ベース]** を選択し、 **[作成]** ボタンをクリックします。

これで、カスタム Service Bus エンドポイントが、ハブのシステム割り当て ID を使用するように設定され、ファイアウォールの制限に関係なく、Service Bus リソースにアクセスするためのアクセス許可が付与されました。 このエンドポイントを使用して、ルーティング規則を設定できるようになりました。

### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>ファイルのアップロードのためのストレージ アカウントへのエグレス接続

IoT Hub のファイルのアップロード機能を使用すると、デバイスで顧客所有のストレージ アカウントにファイルをアップロードできます。 ファイルのアップロードを機能させるには、デバイスと IoT Hub の両方をストレージ アカウントに接続する必要があります。 ストレージ アカウントにファイアウォールの制限が適用されている場合、デバイスでは、サポートされているストレージ アカウントのいずれかのメカニズム ([プライベート エンドポイント](../private-link/tutorial-private-endpoint-storage-portal.md)、[サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)、または[ファイアウォールのダイレクト構成](../storage/common/storage-network-security.md)を含む) を使用して接続する必要があります。 同様に、ストレージ アカウントにファイアウォールの制限が適用されている場合は、信頼された Microsoft サービスの例外を使用して、ストレージ リソースにアクセスするように IoT Hub を構成する必要があります。 このため、IoT Hub にはマネージド ID が必要です。 マネージド ID がプロビジョニングされたら、以下の手順に従って、ストレージ アカウントにアクセスするためにハブのリソース ID に Azure RBAC アクセス許可を付与します。

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

1. Azure portal で、ストレージ アカウントの **[アクセス制御 (IAM)]** タブに移動し、 **[ロールの割り当てを追加する]** セクションの下にある **[追加]** をクリックします。

2. **ロール** として **[ストレージ BLOB データ共同作成者]** ([共同作成者やストレージ アカウント共同作成者 "*ではなく*"](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)) を選択し、**アクセスの割当先** として **[Azure AD のユーザー、グループ、サービス プリンシパル]** を選んでから、ドロップダウン リストで IoT Hub のリソース名を選択します。 **[保存]** ボタンをクリックします。

3. ストレージ アカウントの **[ファイアウォールと仮想ネットワーク]** タブに移動し、 **[Allow access from selected networks]\(選択したネットワークからのアクセスを許可する\)** オプションを有効にします。 **[例外]** 一覧で、 **[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** のボックスをオンにします。 **[保存]** ボタンをクリックします。

4. IoT Hub のリソース ページで、 **[ファイルのアップロード]** タブに移動します。

5. 表示されたページで、BLOB ストレージで使用する予定のコンテナーを選択し、 **[ファイル通知の設定]** 、 **[SAS TTL]** 、 **[既定の TTL]** 、 **[最大配信回数]** を必要に応じて構成します。 ストレージ エンドポイントに対する **[認証の種類]** として、 **[ID ベース]** を選択します。 **[作成]** ボタンをクリックします。 この手順でエラーが発生した場合は、一時的にストレージ アカウントを設定して、**すべてのネットワーク** からのアクセスを許可してから、再試行してください。 ファイルのアップロードの構成が完了したら、ストレージ アカウントでファイアウォールを構成できます。

これで、ファイルのアップロード用のストレージ エンドポイントが、ハブのシステム割り当て ID を使用するように設定され、ファイアウォールの制限に関係なく、ストレージ リソースにアクセスするためのアクセス許可が付与されました。

### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>デバイスの一括インポートとエクスポートのためのストレージ アカウントへのエグレス接続

IoT Hub では、顧客指定のストレージ BLOB 間で、デバイスの情報を一括で[インポートおよびエクスポートする](./iot-hub-bulk-identity-mgmt.md)機能がサポートされています。 一括インポートとエクスポートを機能させるには、デバイスと IoT Hub の両方でストレージ アカウントに接続する必要があります。

この機能では、IoT Hub からストレージ アカウントへの接続が必要です。 ファイアウォールの制限が適用されているときに、Service Bus リソースにアクセスするには、IoT Hub にマネージド ID が必要です。 マネージド ID がプロビジョニングされたら、以下の手順に従って、Service Bus にアクセスするためにハブのリソース ID に Azure RBAC アクセス許可を付与します。

1. Azure portal で、ストレージ アカウントの **[アクセス制御 (IAM)]** タブに移動し、 **[ロールの割り当てを追加する]** セクションの下にある **[追加]** をクリックします。

2. **ロール** として **[ストレージ BLOB データ共同作成者]** ([共同作成者やストレージ アカウント共同作成者 "*ではなく*"](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)) を選択し、**アクセスの割当先** として **[Azure AD のユーザー、グループ、サービス プリンシパル]** を選んでから、ドロップダウン リストで IoT Hub のリソース名を選択します。 **[保存]** ボタンをクリックします。

3. ストレージ アカウントの **[ファイアウォールと仮想ネットワーク]** タブに移動し、 **[Allow access from selected networks]\(選択したネットワークからのアクセスを許可する\)** オプションを有効にします。 **[例外]** 一覧で、 **[信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します]** のボックスをオンにします。 **[保存]** ボタンをクリックします。

これで、[インポートとエクスポート ジョブの作成](/rest/api/iothub/service/jobs/getimportexportjobs) (一括インポートとエクスポート機能を使用する方法については、こちらを参照してください) に Azure IoT REST API を使用できるようになりました。 要求本文に `storageAuthenticationType="identityBased"` を指定し、ストレージ アカウントの入力および出力 URL として `inputBlobContainerUri="https://..."` と `outputBlobContainerUri="https://..."` をそれぞれ使用する必要があります。

Azure IoT Hub SDK では、サービス クライアントのレジストリ マネージャーでもこの機能がサポートされます。 次のコード スニペットでは、C# SDK を使用してインポート ジョブまたはエクスポート ジョブを開始する方法を示しています。

```csharp
// Call an import job on the IoT Hub
JobProperties importJob = 
await registryManager.ImportDevicesAsync(
  JobProperties.CreateForImportJob(inputBlobContainerUri, outputBlobContainerUri, null, StorageAuthenticationType.IdentityBased), 
  cancellationToken);

// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
await registryManager.ExportDevicesAsync(
    JobProperties.CreateForExportJob(outputBlobContainerUri, true, null, StorageAuthenticationType.IdentityBased),
    cancellationToken);
```

C#、Java、および Node.js 用の仮想ネットワークをサポートする Azure IoT SDK のこのバージョンを使用するには、次のようにします。

1. `EnableStorageIdentity` という名前の環境変数を作成し、その値を `1` に設定します。

2. 次のいずれかの SDK をダウンロードします: [Java](https://aka.ms/vnetjavasdk) | [C#](https://aka.ms/vnetcsharpsdk) | [Node.js](https://aka.ms/vnetnodesdk)
 
Python の場合は、GitHub から限定バージョンをダウンロードします。

1. [GitHub リリース ページ](https://aka.ms/vnetpythonsdk)に移動します。

2. 次のファイルをダウンロードします。これは、リリース ページの下部にある **assets** という名前のヘッダーの下にあります。
    > *azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl*

3. ターミナルを開き、ダウンロードされたファイルがあるフォルダーに移動します。

4. 次のコマンドを実行し、仮想ネットワークがサポートされる Python Service SDK をインストールします。
    > pip install ./azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl


## <a name="next-steps"></a>次のステップ

IoT Hub の機能の詳細について、下記のリンク使用してください。

* [メッセージ ルーティング](./iot-hub-devguide-messages-d2c.md)
* [ファイルのアップロード](./iot-hub-devguide-file-upload.md)
* [デバイスの一括デインポートとエクスポート](./iot-hub-bulk-identity-mgmt.md)
