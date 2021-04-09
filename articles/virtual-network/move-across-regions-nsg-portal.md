---
title: Azure portal を使用して Azure ネットワーク セキュリティ グループ (NSG) を別の Azure リージョンに移動する
description: Azure portal を使用して Azure ネットワーク セキュリティ グループを Azure リージョン間で移動するには、Azure Resource Manager テンプレートを使用します。
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: 11b0260eb6c1f5edfb246dcf97a43bdd874bf337
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98217279"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-the-azure-portal"></a>Azure portal を使用して Azure ネットワーク セキュリティ グループ (NSG) を別のリージョンに移動する

既存の NSG をリージョン間で移動することが必要になるさまざまなシナリオがあります。 たとえば、テスト用に同じ構成とセキュリティ規則で NSG を作成したい場合があります。 ディザスター リカバリー計画の一部として、NSG を別のリージョンに移動したい場合もあります。

Azure セキュリティ グループは、あるリージョンから別のリージョンに移動することができません。 ただし、Azure Resource Manager テンプレートを使用して、NSG の既存の構成とセキュリティ規則をエクスポートすることはできます。  その後、NSG をテンプレートにエクスポートすることで別のリージョンにリソースを公開し、宛先リージョンに合わせてパラメーターを変更してから、新しいリージョンにテンプレートをデプロイできます。  Resource Manager とテンプレートの詳細については、「[クイック スタート: Azure portal を使用した Azure Resource Manager テンプレートの作成とデプロイ](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)」を参照してください。


## <a name="prerequisites"></a>前提条件

- 移動元の Azure リージョンに Azure ネットワーク セキュリティ グループが存在することを確認します。

- Azure ネットワーク セキュリティ グループはリージョン間で移動できません。  新しい NSG を移動先リージョンのリソースに関連付ける必要があります。

- NSG 構成をエクスポートし、テンプレートをデプロイして別のリージョンで NSG を作成するには、ネットワーク共同作成者ロール以上が必要です。

- ソース ネットワーク レイアウトと現在使用しているすべてのリソースを特定します。 このレイアウトには、ロード バランサー、パブリック IP、仮想ネットワークが含まれますが、この限りではありません。

- 自分の Azure サブスクリプションで、使用される移動先リージョンに NSG を作成できることを確認します。 サポートに連絡して、必要なクォータを有効にしてください。

- 使用するサブスクリプションに、このプロセスでの NSG の追加をサポートするのに十分なリソースがあることを確認します。  「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)」をご覧ください。


## <a name="prepare-and-move"></a>準備と移動
次の手順では、構成とセキュリティ規則を移動する目的で Resource Manager テンプレートを使用してネットワーク セキュリティ グループの準備を行い、ポータルを使用して NSG の構成とセキュリティ規則を移動先リージョンに移動する方法を示します。


### <a name="export-the-template-and-deploy-from-the-portal"></a>テンプレートをエクスポートし、ポータルからデプロイする

1. [Azure portal](https://portal.azure.com) >  **[リソース グループ]** にログインします。
2. 移動元の NSG が含まれているリソース グループを探し、それをクリックします。
3. **[設定]**  >  **[テンプレートのエクスポート]** を選択します。
4. **[テンプレートのエクスポート]** ブレードで **[デプロイ]** を選択します。
5. **[テンプレート]**  >  **[パラメーターの編集]** をクリックして、オンライン エディターで **parameters.json** ファイルを開きます。
6. NSG 名のパラメーターを編集するには、**parameters** の **value** プロパティを変更します。

    ```json
            {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
               "value": "<target-nsg-name>"
                }
               }
            }
    ```

7. エディターで、移動元 NSG の値を、移動先 NSG として選択した名前に変更します。 名前は確実に引用符で囲みます。

8.  エディターで **[保存]** をクリックします。

9.  **[テンプレート]**  >  **[テンプレートの編集]** をクリックして、オンライン エディターで **template.json** ファイルを開きます。

10. NSG の構成とセキュリティ規則を移動する移動先リージョンを編集するには、オンライン エディターで **resources** の下の **location** プロパティを変更します。

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
             }
            }
           ]

    ```

11. リージョンの場所コードを取得するには、「[Azure の場所](https://azure.microsoft.com/global-infrastructure/locations/)」を参照してください。  リージョンのコードは、スペースを含まないリージョン名です (**Central US** = **centralus**)。

12. また、必要に応じて、テンプレート内の他のパラメーターも変更できます。これらは、実際の要件に応じて省略可能です。

    * **セキュリティ規則** - **template.json** ファイルの **securityRules** セクションで規則を追加または削除することで、移動先 NSG にデプロイされた規則を編集できます。

        ```json
           "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
                "securityRules": [
                    {
                        "name": "RDP",
                        "etag": "W/\"c630c458-6b52-4202-8fd7-172b7ab49cf5\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "TCP",
                            "sourcePortRange": "*",
                            "destinationPortRange": "3389",
                            "sourceAddressPrefix": "*",
                            "destinationAddressPrefix": "*",
                            "access": "Allow",
                            "priority": 300,
                            "direction": "Inbound",
                            "sourcePortRanges": [],
                            "destinationPortRanges": [],
                            "sourceAddressPrefixes": [],
                            "destinationAddressPrefixes": []
                             }
                    },
                ]
            }
        ```

      移動先 NSG で規則を追加したり、削除したりするには、下の例の形式で、**template.json** ファイルの末尾でカスタム規則の種類を変更する必要もあります。

      ```json
           {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('networkSecurityGroups_myVM1_nsg_name'), '/Port_80')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_myVM1_nsg_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "protocol": "*",
                "sourcePortRange": "*",
                "destinationPortRange": "80",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 310,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
      ```

13. オンライン エディターで **[保存]** をクリックします。

14. **[基本]**  >  **[サブスクリプション]** をクリックして、移動先 NSG をデプロイするサブスクリプションを選択します。

15. **[基本]**  >  **[リソース グループ]** をクリックして、移動先 NSG をデプロイするリソース グループを選択します。  **[新規作成]** をクリックして、移動先 NSG 用の新しいリソース グループを作成できます。  既存の NSG の移動元リソース グループと確実に違う前にします。

16. **[基本]**  >  **[場所]** が NSG をデプロイする移動先の場所に設定されていることを確認します。

17. **[設定]** で、名前が上記のパラメーター エディターで入力した名前と一致することを確認します。

18. **[使用条件]** のチェック ボックスをオンにします。

19. **[購入]** ボタンをクリックして、移動先のネットワーク セキュリティ グループをデプロイします。

## <a name="discard"></a>破棄

移動先 NSG を破棄する場合は、移動先 NSG が含まれるリソース グループを削除します。  これを行うには、ポータルのダッシュボードでリソース グループを選択し、概要ページの上部にある **[削除]** を選択します。

## <a name="clean-up"></a>クリーンアップ

変更をコミットし、NSG の移動を完了するには、移動元の NSG またはリソース グループを削除します。 これを行うには、ポータルのダッシュボードでネットワーク セキュリティ グループまたはリソース グループを選択し、各ページの上部にある **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure ネットワーク セキュリティ グループをあるリージョンから別のリージョンに移動し、移動元リソースをクリーンアップしました。  リージョン間でのリソースの移動と Azure でのディザスター リカバリーの詳細については、以下を参照してください。


- [リソースを新しいリソース グループまたはサブスクリプションに移動する](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Azure VM を別のリージョンに移動する](../site-recovery/azure-to-azure-tutorial-migrate.md)