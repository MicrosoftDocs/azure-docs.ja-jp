---
title: Azure IoT DPS の IP 接続フィルター | Microsoft Docs
description: Azure IoT DPS インスタンスへの特定の IP アドレスからの接続をブロックする IP フィルター処理を使用する方法。 個別の IP アドレスまたは一定の範囲の IP アドレスからの接続をブロックできます。
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: e1b175a176255da465433b2db45cb3cb67d360d1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98934494"
---
# <a name="use-azure-iot-dps-ip-connection-filters"></a>Azure IoT DPS の IP 接続フィルターを使用する

セキュリティは IoT ソリューションの重要な 1 つの側面です。 場合によっては、セキュリティ構成の一部として、デバイスが接続できる IP アドレスを明示的に指定する必要があります。 Azure IoT Hub Device Provisioning Service (DPS) の *IP フィルター* 機能を使用すると、特定の IPv4 アドレスからのトラフィックを拒否または許可するための規則を構成できます。

## <a name="when-to-use"></a>使用する場合

特定の IP アドレスから DPS エンドポイントへの接続をブロックすると有用な特定のユース ケースには、次の 2 つがあります。

* DPS が指定された範囲の IP アドレスからのトラフィックのみを受信し、それ以外のトラフィックをすべて拒否する必要がある場合。 たとえば、DPS を [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services) と共に使用して、DPS と使用しているデバイスとの間にプライベート接続を作成する場合が該当します。

* DPS の管理者によって疑わしいと識別された IP アドレスからのトラフィックを拒否する必要がある場合。

## <a name="how-filter-rules-are-applied"></a>フィルター規則の適用方法

IP フィルター規則は、DPS インスタンス レベルで適用されます。 したがって、IP フィルター規則は、サポートされているプロトコルを使用するデバイスおよびバックエンド アプリからのすべての接続に適用されます。

DPS インスタンスの拒否 IP 規則に一致する IP アドレスからの接続試行に対しては、認証されていないアクセスを示す 401 状態コードとその説明が返されます。 IP 規則に関する記述は応答メッセージに含まれません。

## <a name="default-setting"></a>既定の設定

既定では、ポータルの DPS の **[IP フィルター]** グリッドは空白になっています。 この既定の設定は、DPS が任意の IP アドレスからの接続を受け入れることを意味します。 この既定の設定は、IP アドレス範囲 0.0.0.0/0 を受け入れる規則と同じです。

![IoT DPS の既定の IP フィルター設定](./media/iot-dps-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>IP フィルター規則の追加または編集

IP フィルター規則を追加するには、 **[+ IP フィルター規則の追加]** を選択します。

![IoT DPS への IP フィルター規則の追加](./media/iot-dps-ip-filtering/ip-filter-add-rule.png)

**[IP フィルター規則の追加]** を選択したら、フィールドに入力します。

![[IP フィルター規則の追加] を選択した後](./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png)

* IP フィルター規則の **名前** を指定します。 これは、一意であり、長さが最大 128 文字の、大文字と小文字を区別しない英数字の文字列である必要があります。 ASCII 7 ビット英数字と `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` のみを使用できます。

* 1 つの IPv4 アドレスか、または CIDR 表記法で記述した IP アドレス ブロックを指定します。 たとえば、CIDR 表記 192.168.100.0/22 は、192.168.100.0 ～ 192.168.103.255 までの 1,024 個の IPv4 アドレスを表します。

* IP フィルター規則の **[アクション]** として **[許可]** または **[ブロック]** を選択します。

フィールドに入力したら、 **[保存]** を選択して規則を保存します。 更新が進行中であることを通知するアラートが表示されます。

![IP フィルター規則の保存に関する通知](./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png)

IP フィルター規則が最大値の 100 個に達すると、 **[追加]** オプションは無効になります。

既存の規則を編集するには、変更するデータを選択して変更を加え、 **[保存]** を選択して編集内容を保存します。

> [!NOTE]
> IP アドレスを拒否すると、他の Azure サービスが DPS インスタンスと対話できなくなる可能性があります。

## <a name="delete-an-ip-filter-rule"></a>IP フィルター規則の削除

IP フィルター規則を削除するには、その行のごみ箱アイコンを選択し、次に **[保存]** を選択します。 規則が削除されて、変更が保存されます。

![IoT DPS の IP フィルター規則の削除](./media/iot-dps-ip-filtering/ip-filter-delete-rule.png)


## <a name="update-ip-filter-rules-in-code"></a>コード内の IP フィルター規則を更新する

Azure リソース プロバイダーの REST エンドポイントを使用して、DPS IP フィルターを取得および変更できます。 [createorupdate メソッド](/rest/api/iot-dps/iotdpsresource/createorupdate)の `properties.ipFilterRules` を参照してください。

DPS IP フィルター規則の更新は、現在 Azure CLI または Azure PowerShell ではサポートされていませんが、Azure Resource Manager テンプレートを使用して実行できます。 Resource Manager テンプレートの使用方法については、[Azure Resource Manager テンプレート](../azure-resource-manager/templates/overview.md)に関する記事をご覧ください。 以下のテンプレートの例では、DPS IP フィルター規則を作成、編集、削除する方法を示しています。

### <a name="add-an-ip-filter-rule"></a>IP フィルター規則の追加

次のテンプレート例では、すべてのトラフィックを受け入れる "AllowAll" という名前の新しい IP フィルター規則を作成します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#", 
    "contentVersion": "1.0.0.0", 
    "parameters": {
        "iotDpsName": {
            "type": "string",
            "defaultValue": "[resourceGroup().name]",
            "minLength": 3,
            "metadata": {
                "description": "Specifies the name of the IoT DPS service."
            }
        }, 
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for Iot DPS resource."
            }
        }        
    }, 
    "variables": {
        "iotDpsApiVersion": "2020-01-01"
    }, 
    "resources": [
        {
            "type": "Microsoft.Devices/provisioningServices",
            "apiVersion": "[variables('iotDpsApiVersion')]",
            "name": "[parameters('iotDpsName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "IpFilterRules": [
                    {
                        "FilterName": "AllowAll",
                        "Action": "Accept",
                        "ipMask": "0.0.0.0/0"
                    }
                ]
            }            
        }
    ]
}
```

要件に基づいて、テンプレートの IP フィルター規則の属性を更新します。

| 属性                | 説明 |
| ------------------------ | ----------- |
| **FilterName**           | IP フィルター規則の名前を指定します。 これは、一意であり、長さが最大 128 文字の、大文字と小文字を区別しない英数字の文字列である必要があります。 ASCII 7 ビット英数字と  `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}`  のみを使用できます。 |
| **操作**               | IP フィルター規則のアクションとして許容される値は、**Accept**  または  **Reject**  です。 |
| **ipMask**               | 1 つの IPv4 アドレスか、または CIDR 表記法で記述した IP アドレス ブロックを指定します。 たとえば、CIDR 表記 192.168.100.0/22 は、192.168.100.0 ～ 192.168.103.255 までの 1,024 個の IPv4 アドレスを表します。 |


### <a name="update-an-ip-filter-rule"></a>IP フィルター規則の更新

次のテンプレート例では、前に示した "AllowAll" という名前の IP フィルター規則を更新して、すべてのトラフィックを拒否します。

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
                "IpFilterRules": [ 
                    { 
                        "FilterName": "AllowAll", 
                        "Action": "Reject", 
                        "ipMask": "0.0.0.0/0" 
                    } 
                ] 
            }             
        } 
    ] 
}
```

### <a name="delete-an-ip-filter-rule"></a>IP フィルター規則の削除

次のテンプレート例では、DPS インスタンスのすべての IP フィルター規則を削除します。

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
            }             
        } 
    ] 
}
```



## <a name="ip-filter-rule-evaluation"></a>IP フィルター規則の評価

IP フィルター規則は順に適用され、IP アドレスと一致する最初の規則に基づいて許可アクションまたは拒否アクションが決定されます。

たとえば、範囲 192.168.100.0/22 内のアドレスを許可し、それ以外のアドレスをすべて拒否するには、グリッドの最初の規則でアドレス範囲 192.168.100.0/22 を許可する必要があります。 さらに、次の規則で範囲 0.0.0.0/0 を使用してすべてのアドレスを拒否する必要があります。

グリッド内の IP フィルター規則の順序を変更するには、行の先頭のドットが縦に 3 つ並んだ部分をクリックし、ドラッグ アンド ドロップします。

新しい IP フィルター規則の順序を保存するには、 **[保存]** をクリックします。

![DPS IP フィルター規則の順序の変更](./media/iot-dps-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>次のステップ

DPS の管理の詳細については、以下を参照してください。

* [IoT DPS IP アドレスについて](iot-dps-understand-ip-address.md)
* [Azure CLI を使用して DPS を設定する](quick-setup-auto-provision-cli.md)
* [DPS へのアクセスを制御する](how-to-control-access.md)
