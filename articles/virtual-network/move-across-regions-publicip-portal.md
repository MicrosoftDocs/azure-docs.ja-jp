---
title: Azure portal で Azure パブリック IP 構成を別の Azure リージョンに移動する
description: Azure portal を使用して Azure パブリック IP 構成を Azure リージョン間で移動するには、テンプレートを使用します。
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: b00fca8cf39bc44e0e53a112a332e6f6c5f0194e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98218605"
---
# <a name="move-azure-public-ip-configuration-to-another-region-using-the-azure-portal"></a>Azure portal を使用して Azure パブリック IP 構成を別のリージョンに移動する

既存の Azure パブリック IP 構成を別のリージョンに移動することが必要になるさまざまなシナリオがあります。 たとえば、テスト用に同じ構成と SKU を使用してパブリック IP を作成することが必要な場合があります。 ディザスター リカバリー計画の一部として、パブリック IP 構成を別のリージョンに移動することが必要な場合もあります。

**Azure パブリック IP はリージョン固有であり、あるリージョンから別のリージョンに移動することはできません。** ただし、Azure Resource Manager テンプレートを使用して、パブリック IP の既存の構成をエクスポートすることはできます。  その後、パブリック IP をテンプレートにエクスポートすることで別のリージョンにリソースをステージし、移動先リージョンに合わせてパラメーターを変更してから、新しいリージョンにテンプレートをデプロイできます。  Resource Manager とテンプレートの詳細については、「[クイック スタート: Azure portal を使用した Azure Resource Manager テンプレートの作成とデプロイ](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)」を参照してください。


## <a name="prerequisites"></a>前提条件

- 移動元の Azure リージョンに Azure パブリック IP が存在することを確認します。

- Azure パブリック IP をリージョン間で移動することはできません。  新しいパブリック IP を移動先リージョンのリソースに関連付ける必要があります。

- パブリック IP の構成をエクスポートし、テンプレートをデプロイして別のリージョンにパブリック IP を作成するには、ネットワーク共同作成者ロール以上が必要です。

- ソース ネットワーク レイアウトと現在使用しているすべてのリソースを特定します。 このレイアウトには、ロード バランサー、ネットワーク セキュリティ グループ (NSG)、仮想ネットワークが含まれますが、この限りではありません。

- 自分の Azure サブスクリプションで、使用される移動先リージョンにパブリック IP を作成できることを確認します。 サポートに連絡して、必要なクォータを有効にしてください。

- 使用するサブスクリプションに、このプロセスでのパブリック IP の追加をサポートするのに十分なリソースがあることを確認します。  「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)」をご覧ください。


## <a name="prepare-and-move"></a>準備と移動
次の手順では、Resource Manager テンプレートを使用して構成の移動用にパブリック IP を準備し、Azure portal を使用してパブリック IP の構成を移動先リージョンに移動する方法を示します。

### <a name="export-the-template-and-deploy-from-a-script"></a>テンプレートをエクスポートし、スクリプトからデプロイする

1. [Azure portal](https://portal.azure.com) >  **[リソース グループ]** にログインします。
2. 移動元のパブリック IP が含まれているリソース グループを探し、それをクリックします。
3. **[設定]**  >  **[テンプレートのエクスポート]** を選択します。
4. **[テンプレートのエクスポート]** ブレードで **[デプロイ]** を選択します。
5. **[テンプレート]**  >  **[パラメーターの編集]** をクリックして、オンライン エディターで **parameters.json** ファイルを開きます。
8. パブリック IP 名のパラメーターを編集するには、**parameters** > **value** の下にあるプロパティを、移動元のパブリック IP 名から移動先のパブリック IP 名に変更し、名前を確実に引用符で囲みます。

    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "publicIPAddresses_myVM1pubIP_name": {
            "value": "<target-publicip-name>"
              }
             }
            }

    ```
8.  エディターで **[保存]** をクリックします。

9.  **[テンプレート]**  >  **[テンプレートの編集]** をクリックして、オンライン エディターで **template.json** ファイルを開きます。

10. パブリック IP を移動する移動先リージョンを編集するには、**resources** の下の **location** プロパティを変更します。

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]
    ```

11. リージョンの場所コードを取得するには、「[Azure の場所](https://azure.microsoft.com/global-infrastructure/locations/)」を参照してください。  リージョンのコードは、スペースを含まないリージョン名です (**Central US** = **centralus**)。

12. また、必要に応じて、テンプレート内の他のパラメーターも変更できます。これらは、実際の要件に応じて省略可能です。

    * **SKU** - 構成のパブリック IP の SKU を、Standard から Basic、または Basic から Standard に変更できます。そのためには、**template.json** ファイルの **sku** > **name** プロパティを変更します。

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
        ```

        Basic と Standard SKU のパブリック IP の違いについて詳しくは、「[パブリック IP アドレスの作成、変更、削除](./virtual-network-public-ip-address.md)」を参照してください。

    * **パブリック IP の割り当て方法** と **アイドル タイムアウト** - **publicIPAllocationMethod** プロパティを **Dynamic** から **Static** または **Static** から **Dynamic** に変更することで、テンプレートのこれらのオプションの両方を変更できます。 アイドル タイムアウトは、**idleTimeoutInMinutes** プロパティを目的の量に変えることで変更できます。  既定値は **4** です。

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []

        ```

        割り当て方法とアイドル タイムアウト値の詳細については、「[パブリック IP アドレスの作成、変更、削除](./virtual-network-public-ip-address.md)」を参照してください。


13. オンライン エディターで **[保存]** をクリックします。

14. **[基本]**  >  **[サブスクリプション]** をクリックして、移動先のパブリック IP をデプロイするサブスクリプションを選択します。

15. **[基本]**  >  **[リソース グループ]** をクリックして、移動先のパブリック IP をデプロイするリソース グループを選択します。  **[新規作成]** をクリックして、移動先パブリック IP の新しいリソース グループを作成できます。  名前が既存の移動元パブリック IP の移動元リソース グループと同じでないことを確認します。

16. **[基本]**  >  **[場所]** がパブリック IP をデプロイする移動先の場所に設定されていることを確認します。

17. **[設定]** で、名前が上記のパラメーター エディターで入力した名前と一致することを確認します。

18. **[使用条件]** のチェック ボックスをオンにします。

19. **[購入]** ボタンをクリックして、移動先のパブリック IP をデプロイします。

## <a name="discard"></a>破棄

移動先のパブリック IP を破棄する場合は、移動先のパブリック IP が含まれるリソース グループを削除します。  これを行うには、ポータルのダッシュボードでリソース グループを選択し、概要ページの上部にある **[削除]** を選択します。

## <a name="clean-up"></a>クリーンアップ

変更をコミットし、パブリック IP の移動を完了するには、移動元のパブリック IP またはリソース グループを削除します。 これを行うには、ポータルのダッシュボードでパブリック IP またはリソース グループを選択し、各ページの上部にある **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure パブリック IP をあるリージョンから別のリージョンに移動し、移動元リソースをクリーンアップしました。  リージョン間でのリソースの移動と Azure でのディザスター リカバリーの詳細については、以下を参照してください。


- [リソースを新しいリソース グループまたはサブスクリプションに移動する](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Azure VM を別のリージョンに移動する](../site-recovery/azure-to-azure-tutorial-migrate.md)