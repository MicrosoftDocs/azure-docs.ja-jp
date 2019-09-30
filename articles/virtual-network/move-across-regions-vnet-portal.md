---
title: Azure portal を使用して Azure 仮想ネットワークを別の Azure リージョンに移動する
description: Azure portal を使用して Azure 仮想ネットワークを Azure リージョン間で移動するには、Azure Resource Manager テンプレートを使用します。
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: a09ce7b77dfcaa51e7c82f67a5d20000f3e22b61
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71220000"
---
# <a name="move-azure-virtual-network-to-another-region-using-the-azure-portal"></a>Azure portal を使用して Azure 仮想ネットワークを別のリージョンに移動する

既存の Azure 仮想ネットワーク (VNET) を別のリージョンに移動することが必要になるさまざまなシナリオがあります。 たとえば、既存の仮想ネットワークのテストや可用性のために、同じ構成で仮想ネットワークを作成することが必要になる場合があります。 ディザスター リカバリー計画の一部として、運用仮想ネットワークを別のリージョンに移動する必要がある場合もあります。

Azure Resource Manager テンプレートを使用して、別のリージョンへの仮想ネットワークの移動を実行できます。 これを行うには、仮想ネットワークをテンプレートにエクスポートし、移動先リージョンに合わせてパラメーターを変更した後、新しいリージョンにテンプレートをデプロイします。  Resource Manager とテンプレートの詳細については、次の記事を参照してください。[クイックスタート: Azure portal を使用した Azure Resource Manager テンプレートの作成とデプロイ](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)


## <a name="prerequisites"></a>前提条件

- 移動元の Azure リージョンに Azure 仮想ネットワークが存在することを確認します。

- 仮想ネットワークをエクスポートし、テンプレートをデプロイして別のリージョンに仮想ネットワークを作成するには、ネットワーク共同作成者ロール以上が必要です。

- 仮想ネットワーク ピアリングは再作成されず、テンプレートにまだ存在する場合は失敗します。  テンプレートをエクスポートする前に仮想ネットワーク ピアを削除し、仮想ネットワークの移動後にピアを再確立する必要があります。

- ソース ネットワーク レイアウトと現在使用しているすべてのリソースを特定します。 このレイアウトにはロード バランサー、ネットワーク セキュリティ グループ (NSG)、パブリック IP が含まれますが、それらに限定されません。

- 自分の Azure サブスクリプションで、使用されるターゲット リージョンに仮想ネットワークを作成できることを確認します。 サポートに連絡して、必要なクォータを有効にしてください。

- お使いのサブスクリプションに、このプロセス用の仮想ネットワークの追加をサポートするための十分なリソースがあることを確認します。  「[Azure サブスクリプションとサービスの制限、クォータ、制約](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)」をご覧ください。


## <a name="prepare-and-move"></a>準備と移動
次の手順では、Resource Manager テンプレートを使用した仮想ネットワークの移動を準備し、ポータルを使用して仮想ネットワークをターゲット リージョンに移動する方法を示します。

### <a name="export-the-template-and-deploy-from-the-portal"></a>テンプレートをエクスポートし、ポータルからデプロイする

1. [Azure portal](https://portal.azure.com) >  **[リソース グループ]** にログインします。
2. ソース仮想ネットワークが含まれているリソース グループを探し、それをクリックします。
3. **[設定]**  >  **[テンプレートのエクスポート]** を選択します。
4. **[テンプレートのエクスポート]** ブレードで **[デプロイ]** を選択します。
5. **[テンプレート]**  >  **[パラメーターの編集]** をクリックして、オンライン エディターで **parameters.json** ファイルを開きます。
6. 仮想ネットワーク名のパラメーターを編集するには、**parameters** の **value** プロパティを変更します。

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
7. エディターで、ソース仮想ネットワーク名の値を、ターゲット VNET に対して選択した名前に変更します。 名前は必ず引用符で囲んでください。

8.  エディターで **[保存]** をクリックします。

9.  **[テンプレート]**  >  **[テンプレートの編集]** をクリックして、オンライン エディターで **template.json** ファイルを開きます。

10. VNET を移動するターゲット リージョンを編集するには、オンライン エディターで **resources** の下の **location** プロパティを変更します。

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

11. リージョンの場所コードを取得するには、「[Azure の場所](https://azure.microsoft.com/global-infrastructure/locations/)」を参照してください。  リージョンのコードは、スペースを含まないリージョン名です (**Central US** = **centralus**)。

12. また、必要に応じて、テンプレート内の次のような他のパラメーターも変更できます。これらは、要件に基づくオプションです。

    * **アドレス空間** - VNET のアドレス空間は、保存する前に変更できます。変更は、**template.json** ファイルの **resources** > **addressSpace** セクションにある **addressPrefixes** プロパティで行います。

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

    * **サブネット** - サブネットの名前とサブネットのアドレス空間は、変更または追加できます。その操作は、**template.json** ファイルの **subnets** セクションで行います。 サブネットの名前を変更するには、**name** プロパティを変更します。 サブネット アドレス空間を変更するには、**template.json** ファイルの **addressPrefix** プロパティを変更します。

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

         **template.json** ファイルでアドレス プレフィックスを変更するには、上で示したセクションと、後で示す **type** セクションの 2 か所を変更する必要があります。  **addressPrefix** プロパティを上記のものと一致するように変更します。

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

13. オンライン エディターで **[保存]** をクリックします。

14. **[基本]**  >  **[サブスクリプション]** をクリックして、ターゲット VNET をデプロイするサブスクリプションを選択します。

15. **[基本]**  >  **[リソース グループ]** をクリックして、ターゲット VNET をデプロイするリソース グループを選択します。  **[新規作成]** をクリックして、ターゲット VNET 用の新しいリソース グループを作成できます。  名前が既存の VNET のソース リソース グループと同じでないことを確認します。

16. **[基本]**  >  **[場所]** が VNET をデプロイするターゲットの場所に設定されていることを確認します。

17. **[設定]** で、名前が上のパラメーター エディターで入力した名前と一致することを確認します。

18. **[使用条件]** のチェック ボックスをオンにします。

19. **[購入]** ボタンをクリックして、ターゲット仮想ネットワークをデプロイします。

## <a name="discard"></a>破棄

ターゲット仮想ネットワークを破棄する場合は、ターゲット仮想ネットワークが含まれるリソース グループを削除します。  これを行うには、ポータルのダッシュボードでリソース グループを選択し、概要ページの上部にある **[削除]** を選択します。

## <a name="clean-up"></a>クリーンアップ

変更をコミットし、仮想ネットワークの移動を完了するには、ソースの仮想ネットワークまたはリソース グループを削除します。 これを行うには、ポータルのダッシュボードで仮想ネットワークまたはリソース グループを選択し、各ページの上部にある **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure 仮想ネットワークをあるリージョンから別のリージョンに移動し、ソース リソースをクリーンアップしました。  リージョン間でのリソースの移動と Azure でのディザスター リカバリーの詳細については、以下を参照してください。


- [リソースを新しいリソース グループまたはサブスクリプションに移動する](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Azure VM を別のリージョンに移動する](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
