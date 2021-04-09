---
title: Azure IoT Hub の IP フィルター | Microsoft Docs
description: IP フィルタリングを使用して、特定の IP アドレスから Azure IoT Hub への接続を許可する方法。
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/12/2021
ms.author: jlian
ms.openlocfilehash: 2a76cede4bc72da9f30564f98ab9bb84028680f7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581492"
---
# <a name="use-ip-filters"></a>IP フィルターの使用

セキュリティは、Azure IoT Hub をベースとするすべての IoT ソリューションの重要な側面です。 場合によっては、セキュリティ構成の一部として、デバイスが接続できる IP アドレスを明示的に指定する必要があります。 *IP フィルター* 機能を使用すると、特定の IPv4 アドレスからのトラフィックを拒否または許可するための規則を構成できます。

## <a name="when-to-use"></a>使用する場合

IP フィルターを使用して、指定された範囲の IP アドレスからのトラフィックのみを受信し、それ以外をすべて拒否します。 たとえば、IoT ハブを [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services) と共に使用して、IoT ハブとオンプレミス インフラストラクチャとの間にプライベート接続を作成する場合が該当します。

## <a name="default-setting"></a>既定の設定

[IP フィルター設定] ページに移動するには、 **[ネットワーク]** 、 **[パブリック アクセス]** 、 **[Selected IP Ranges]\(選択された IP 範囲\)** の順に選択します。

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-default.png" alt-text="IoT Hub の既定の IP フィルター設定":::

既定では、ポータルの IoT ハブの **[IP フィルター]** は空白になっています。 この既定の設定は、ハブではすべての IP アドレスからの接続がブロックされることを意味します。 この既定の設定は、`0.0.0.0/0` の IP アドレス範囲をブロックする規則と同じです。

## <a name="add-or-edit-an-ip-filter-rule"></a>IP フィルター規則の追加または編集

IP フィルター規則を追加するには、 **[+ IP フィルター規則の追加]** を選択します。

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-add-rule.png" alt-text="IoT Hub への IP フィルター規則の追加":::

**[IP フィルター規則の追加]** を選択したら、フィールドに入力します。

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png" alt-text="[IP フィルター規則の追加] を選択した後":::

* IP フィルター規則の **名前** を指定します。 この名前は、一意であり、長さが最大 128 文字の、大文字と小文字を区別しない英数字の文字列である必要があります。 ASCII 7 ビット英数字と `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` のみを使用できます。

* 1 つの IPv4 アドレスか、または CIDR 表記法で記述した IP アドレス ブロックを指定します。 たとえば、CIDR 表記 192.168.100.0/22 は、192.168.100.0 ～ 192.168.103.255 までの 1,024 個の IPv4 アドレスを表します。

フィールドに入力したら、 **[保存]** を選択して規則を保存します。 更新が進行中であることを通知するアラートが表示されます。

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png" alt-text="IP フィルター規則の保存に関する通知":::

IP フィルター規則が最大値の 10 個に達すると、 **[追加]** オプションは無効になります。

既存の規則を編集するには、変更するデータを選択して変更を加え、 **[保存]** を選択して編集内容を保存します。

## <a name="delete-an-ip-filter-rule"></a>IP フィルター規則の削除

IP フィルター規則を削除するには、その行のごみ箱アイコンを選択し、次に **[保存]** を選択します。 規則が削除されて、変更が保存されます。

:::image type="content" source="./media/iot-hub-ip-filtering/ip-filter-delete-rule.png" alt-text="IoT Hub の IP フィルター規則の削除":::

## <a name="apply-ip-filter-rules-to-the-built-in-event-hub-compatible-endpoint"></a>イベント ハブと互換性のある組み込みのエンドポイントに IP フィルター規則を適用する

イベント ハブと互換性のある組み込みのエンドポイントに IP フィルター規則を適用するには、 **[Apply IP filters to the built-in endpoint?]\(IP フィルターを組み込みのエンドポイントに適用しますか\)** の横のボックスをオンにし、 **[保存]** を選択します。

:::image type="content" source="media/iot-hub-ip-filtering/ip-filter-built-in-endpoint.png" alt-text="組み込みエンドポイントの切り替えと保存を示す画像":::

> [!NOTE]
> このオプションは、無料 (F1) の IoT ハブでは使用できません。 組み込みのエンドポイントに IP フィルター規則を適用するには、有料の IoT ハブを使用してください。

このオプションを有効にすると、IP フィルター規則が組み込みのエンドポイントにレプリケートされるため、それにアクセスできるのは、信頼された IP 範囲のみになります。

このオプションを無効にすると、すべての IP アドレスからこの組み込みのエンドポイントにアクセスできます。 この動作は、Azure Stream Analytics のような IP アドレスが変化するサービスを使用してエンドポイントから読み取る場合には便利です。 

## <a name="how-filter-rules-are-applied"></a>フィルター規則の適用方法

IP フィルター規則は、IoT Hub サービス レベルで適用されます。 したがって、IP フィルター規則は、サポートされているプロトコルを使用するデバイスおよびバックエンド アプリからのすべての接続に適用されます。 また、[イベント ハブと互換性のある組み込みのエンドポイント](iot-hub-devguide-messages-read-builtin.md) (IoT Hub 接続文字列経由ではない) をこれらの規則にバインドするかどうかも選択できます。 

明示的に許可されていない IP アドレスからの接続試行では、未認証の 401 状態コードと説明が返されます。 IP 規則に関する記述は応答メッセージに含まれません。 IP アドレスを拒否すると、Azure Stream Analytics、Azure Virtual Machines、Azure portal のデバイス エクスプローラーなど、他の Azure サービスが IoT Hub と対話できなくなる可能性があります。

> [!NOTE]
> IP フィルターを有効にした状態で、Azure Stream Analytics (ASA) を使用して IoT ハブからメッセージを読み取る必要がある場合は、 **[Apply IP filters to the built-in endpoint]\(組み込みのエンドポイントに IP フィルターを適用する\)** オプションを **無効** にしてから、IoT ハブのイベント ハブと互換性のある名前とエンドポイントを使用して、ASA に [イベント ハブのストリーム入力](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-event-hubs)を手動で追加します。

### <a name="ordering"></a>順序

IP フィルター規則は "*許可*" 規則であり、順序付けなしで適用されます。 追加された IP アドレスだけが IoT Hub への接続を許可されます。 

たとえば、範囲 `192.168.100.0/22` 内のアドレスを許可し、それ以外をすべて拒否する場合、必要なのは、アドレス範囲 `192.168.100.0/22` を使用してグリッドに 1 つの規則を追加することだけです。

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Azure CLI を使用した IP フィルターの取得および更新

[Azure CLI](/cli/azure/) を使用して、IoT Hub の IP フィルターを取得および更新できます。

IoT Hub の現在の IP フィルターを取得するには、以下を実行します。

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

これにより、既存の IP フィルターが `properties.networkRuleSets` キーの下にリストされている JSON オブジェクトが返されます。

```json
{
...
    "properties": {
        "networkRuleSets": {
            "defaultAction": "Deny",
            "applyToBuiltInEventHubEndpoint": true,
            "ipRules": [{
                    "filterName": "TrustedFactories",
                    "action": "Allow",
                    "ipMask": "1.2.3.4/5"
                },
                {
                    "filterName": "TrustedDevices",
                    "action": "Allow",
                    "ipMask": "1.1.1.1/1"
                }
            ]
        }
    }
}
```

IoT Hub の新しい IP フィルターを追加するには、以下を実行します。

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.networkRuleSets.ipRules "{\"action\":\"Allow\",\"filterName\":\"TrustedIP\",\"ipMask\":\"192.168.0.1\"}"
```

IoT Hub の既存の IP フィルターを削除するには、以下を実行します。

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.networkRuleSets.ipRules <ipFilterIndexToRemove>
```

ここで、`<ipFilterIndexToRemove>` は、IoT Hub の `properties.networkRuleSets.ipRules` 内の IP フィルターの順序に対応している必要があります。

## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Azure PowerShell を使用した IP フィルターの取得および更新

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure PowerShell](/powershell/azure/) を使用して、IoT Hub の IP フィルターを取得および設定できます。

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.networkRuleSets.ipRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='TrustedIP'; 'action'='Allow'; 'ipMask'='192.168.0.1'}

# Add your new IP filter rule
$iothubResource.Properties.networkRuleSets.ipRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.networkRuleSets.ipRules = @($iothubResource.Properties.networkRuleSets.ipRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

## <a name="update-ip-filter-rules-using-rest"></a>REST を使用した IP フィルター規則の更新


Azure リソース プロバイダーの REST エンドポイントを使用することでも、IoT Hub の IP フィルターを取得および変更できます。 [createorupdate メソッド](/rest/api/iothub/iothubresource/createorupdate)の `properties.networkRuleSets` を参照してください。

## <a name="ip-filter-classic-retirement"></a>IP フィルター (クラシック) の提供終了

クラシック IP フィルターは廃止されました。 詳細については、[IoT Hub のクラシック IP フィルターとアップグレード方法](iot-hub-ip-filter-classic.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

IoT Hub の機能を詳しく調べるには、次のリンクを使用してください。

* [IoT Hub メトリック](./monitor-iot-hub.md)
* [Private Link とマネージド ID を使用した仮想ネットワークの IoT Hub サポート](virtual-network-support.md)
* [IoT ハブのパブリック ネットワーク アクセスの管理](iot-hub-public-network-access.md)
* [IoT Hub の監視](monitor-iot-hub.md)
