---
title: Azure portal を使用して Azure 仮想ネットワークを別の Azure リージョンに移動する
description: Resource Manager テンプレートと Azure portal を使用して Azure 仮想ネットワークを Azure リージョン間で移動します。
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: 0a80c731e4245b1a295364e5b8c87f90290f7f74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98223310"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-the-azure-portal"></a>Azure portal を使用して Azure 仮想ネットワークを別のリージョンに移動する

既存の Azure 仮想ネットワークをリージョン間で移動する場合、さまざまなシナリオがあります。 たとえば、テストや可用性のために、既存の仮想ネットワークと同じ構成で仮想ネットワークを作成することが必要になる場合があります。 または、ディザスター リカバリー計画の一環として、運用仮想ネットワークを別のリージョンに移動する必要がある場合もあります。

Azure Resource Manager テンプレートを使用して、別のリージョンへの仮想ネットワークの移動を実行できます。 これを行うには、仮想ネットワークをテンプレートにエクスポートし、移動先リージョンに合わせてパラメーターを変更した後、新しいリージョンにテンプレートをデプロイします。 Resource Manager テンプレートの詳細については、「[クイック スタート: Azure portal を使用した Azure Resource Manager テンプレートの作成とデプロイ](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)」を参照してください。


## <a name="prerequisites"></a>前提条件

- 移動元の Azure リージョンに仮想ネットワークが存在することを確認します。

- 仮想ネットワークをエクスポートし、テンプレートをデプロイして別のリージョンに仮想ネットワークを作成するには、ネットワーク共同作成者ロール以上が必要です。

- 仮想ネットワーク ピアリングは再作成されず、テンプレートにまだ存在する場合は失敗します。 テンプレートをエクスポートする前に、仮想ネットワーク ピアをすべて削除する必要があります。 それらは、仮想ネットワークを移動した後に再確立できます。

- ソース ネットワーク レイアウトと現在使用しているすべてのリソースを特定します。 このレイアウトにはロード バランサー、ネットワーク セキュリティ グループ (NSG)、パブリック IP が含まれますが、それらに限定されません。

- お使いの Azure サブスクリプションで、ターゲット リージョンに仮想ネットワークを作成できることを確認します。 必要なクォータを有効にするには、サポートに連絡してください。

- お使いのサブスクリプションに、このプロセス用の仮想ネットワークの追加をサポートするための十分なリソースがあることを確認します。 詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)」をご覧ください。


## <a name="prepare-for-the-move"></a>移動を準備する
このセクションでは、Resource Manager テンプレートを使用して、移動するために仮想ネットワークを準備します。 その後に、Azure portal を使用して仮想ネットワークをターゲット リージョンに移動します。

Azure portal を使用して仮想ネットワークをエクスポートしてターゲット仮想ネットワークにデプロイするには、以下を行います。

1. [Azure portal](https://portal.azure.com) にサインインし、 **[リソース グループ]** を選択します。
1. ソース仮想ネットワークが含まれているリソース グループを探し、それを選択します。
1. **[設定]**  >  **[テンプレートのエクスポート]** の順に選択します。
1. **[テンプレートのエクスポート]** ウィンドウで **[デプロイ]** を選択します。
1. オンライン エディターで *parameters.json* ファイルを開くには、**[テンプレート]** > **[パラメーターの編集]** を選択します。
1. 仮想ネットワーク名のパラメーターを編集するには、**parameters** の **value** プロパティを変更します。

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "virtualNetworks_myVNET1_name": {
                "value": "<target-virtual-network-name>"
            }
        }
    }
    ```

1. エディターで、エディター内のソース仮想ネットワーク名の値をターゲット仮想ネットワークに使用する名前に変更します。 名前は、必ず引用符で囲みます。

1. エディターで **[保存]** を選択します。

1. オンライン エディターで *template.json* ファイルを開くには、**[テンプレート]** > **[テンプレートの編集]** を選択します。

1. オンライン エディターで、仮想ネットワークを移動するターゲット リージョンを編集するには、**resources** の下の **location** プロパティを変更します。

    ```json
    "resources": [
                {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region>",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                        "addressSpace": {
                            "addressPrefixes": [
                                "10.0.0.0/16"
                            ]
                        },

    ```

1. リージョンの場所コードを取得するには、「[Azure の場所](https://azure.microsoft.com/global-infrastructure/locations/)」を参照してください。 リージョンのコードは、スペースを含まないリージョン名です (たとえば、**Central US** = **centralus**)。

1. (省略可能) また、必要に応じて、テンプレート内の他のパラメーターも変更できます。

    * **[アドレス空間]** : ファイルを保存する前に、仮想ネットワークのアドレス空間を変更できます。これを行うには、**resources** > **addressSpace** セクションを変更し、**addressPrefixes** プロパティを変更します。

        ```json
                "resources": [
                    {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                    "addressSpace": {
                        "addressPrefixes": [
                        "10.0.0.0/16"
                        ]
                    },

        ```

    * **[サブネット]** : テンプレートの **subnets** セクションを変更して、サブネット名とサブネット アドレス空間の変更や、これらに対する追加を行うことができます。 サブネットの名前を変更するには、**name** プロパティを変更します。 また、サブネット アドレス空間を変更するには、**addressPrefix** プロパティを変更します。

        ```json
                "subnets": [
                    {
                    "name": "subnet-1",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.0.0/24",
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    },
                    {
                    "name": "GatewaySubnet",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.1.0/29",
                    "serviceEndpoints": [],
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    }

                ]
        ```

        *template.json* ファイルのアドレス プレフィックスを変更するには、2 つの場所 (前のセクションのコード内と、次のコードの **type** セクション内) で編集します。 前のセクションのコード内の **addressPrefix** プロパティと一致するように、次のコードの **addressPrefix** プロパティを変更します。

        ```json
         "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-06-01",
           "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/GatewaySubnet')]",
              "dependsOn": [
                 "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                   ],
              "properties": {
                 "provisioningState": "Succeeded",
                 "addressPrefix": "10.0.1.0/29",
                 "serviceEndpoints": [],
                 "delegations": [],
                 "privateEndpointNetworkPolicies": "Enabled",
                 "privateLinkServiceNetworkPolicies": "Enabled"
                  }
                 },
                  {
                  "type": "Microsoft.Network/virtualNetworks/subnets",
                  "apiVersion": "2019-06-01",
                  "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/subnet-1')]",
                     "dependsOn": [
                        "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                          ],
                     "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "10.0.0.0/24",
                        "delegations": [],
                        "privateEndpointNetworkPolicies": "Enabled",
                        "privateLinkServiceNetworkPolicies": "Enabled"
                         }
                  }
         ]
        ```

1. オンライン エディターで **[保存]** を選択します。

1. ターゲット仮想ネットワークをデプロイするサブスクリプションを選択するには、 **[基本]**  >  **[サブスクリプション]** を選択します。

1. ターゲット仮想ネットワークをデプロイするリソース グループを選択するには、 **[基本]**  >  **[リソース グループ]** を選択します。 

    ターゲット仮想ネットワークの新しいリソース グループを作成する必要がある場合は、**[新規作成]** を選択します。 名前が、既存の仮想ネットワーク内のソース リソース グループ名と同じでないことを確認します。

1. **[基本]**  >  **[場所]** が、仮想ネットワークをデプロイするターゲットの場所に設定されていることを確認します。

1. **[設定]** で、名前が、前にパラメーター エディターで入力した名前と一致することを確認します。

1. **[使用条件]** チェック ボックスをオンにします。

1. ターゲット仮想ネットワークをデプロイするには、**[購入]** を選択します。

## <a name="delete-the-target-virtual-network"></a>ターゲット仮想ネットワークを削除する

ターゲット仮想ネットワークを破棄するには、そのターゲット仮想ネットワークが含まれるリソース グループを削除します。 そのためには次を行います。
1. Azure portal のダッシュボードで、リソース グループを選択します。
1. **[概要]** ウィンドウの上部で、**[削除]** を選択します。

## <a name="clean-up"></a>クリーンアップ

変更をコミットし、仮想ネットワークの移動を完了するには、ソースの仮想ネットワークまたはリソース グループを削除します。 そのためには次を行います。
1. Azure portal のダッシュボードで、仮想ネットワークまたはリソース グループを選択します。
1. 各ウィンドウの上部で **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure portal を使用して Azure 仮想ネットワークをあるリージョンから別のリージョンに移動してから、不要なソース リソースをクリーンアップしました。 リージョン間でのリソースの移動と Azure でのディザスター リカバリーの詳細については、以下を参照してください。


- [リソースを新しいリソース グループまたはサブスクリプションに移動する](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Azure 仮想マシンを別のリージョンに移動する](../site-recovery/azure-to-azure-tutorial-migrate.md)