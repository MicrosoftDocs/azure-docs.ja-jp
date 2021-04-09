---
title: Azure Service Fabric クラスターの構成をアップグレードする
description: Resource Manager テンプレートを使用して Azure で Service Fabric クラスターを実行する構成をアップグレードする方法について説明します。
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 692dc2162159ab61a3ac527e12dac43438084a60
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91842718"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>Azure でクラスターの構成をアップグレードする 

この記事では、Service Fabric クラスターのさまざまなファブリックの設定をカスタマイズする方法について説明します。 Azure でホストされているクラスターの場合、[Azure portal](https://portal.azure.com) または Azure Resource Manager テンプレートを使って設定をカスタマイズできます。

> [!NOTE]
> すべての設定をポータルで使用できるとは限りません。[ベスト プラクティスは、Azure Resource Manager テンプレートを使用して設定をカスタマイズすること](./service-fabric-best-practices-infrastructure-as-code.md)です。ポータルは Service Fabric の開発とテストのシナリオ専用です。
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Resource Manager テンプレートを使用してクラスター設定をカスタマイズする
Azure クラスターは、JSON の Resource Manager テンプレートを使って構成できます。 さまざまな設定の詳細については、[クラスターの構成設定](service-fabric-cluster-fabric-settings.md)に関する記事をご覧ください。 例として、次の手順はで、Azure Resource Explorer を使って *MaxDiskQuotaInMB* という新しい設定を *Diagnostics* セクションに追加する方法を示します。

1. [https://resources.azure.com](https://resources.azure.com ) に移動します
2. **[サブスクリプション]**  ->  **\<Your Subscription>**  ->  **[resourceGroups]**  ->  **\<Your Resource Group>**  ->  **[プロバイダー]**  ->  **[Microsoft.ServiceFabric]**  ->  **[クラスター]**  ->  **\<Your Cluster Name>** の順に展開してサブスクリプションに移動します
3. 右上隅の **[読み取り/書き込み]** を選択します。
4. **[編集]** を選択して `fabricSettings` JSON 要素を更新し、新しい要素を追加します。

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

Azure Resource Manager を使用して次の方法のいずれかでクラスターの設定をカスタマイズすることもできます。

- [Azure portal](../azure-resource-manager/templates/export-template-portal.md) を使用し、Resource Manager テンプレートをエクスポートして更新します。
- [PowerShell](../azure-resource-manager/management/manage-resources-powershell.md) を使用し、Resource Manager テンプレートをエクスポートして更新します。
- [Azure CLI](../azure-resource-manager/management/manage-resources-cli.md) を使用し、Resource Manager テンプレートをエクスポートして更新します。
- 設定を直接変更するには、Azure PowerShell [Set-AzServiceFabricSetting](/powershell/module/az.servicefabric/set-azservicefabricsetting) および [Remove-AzServiceFabricSetting](/powershell/module/az.servicefabric/remove-azservicefabricsetting) コマンドを使います。
- 設定を直接変更するには、Azure CLI の [az sf cluster setting](/cli/azure/sf/cluster/setting) コマンドを使います。

## <a name="next-steps"></a>次のステップ
* [Service Fabric クラスターの設定](service-fabric-cluster-fabric-settings.md)について学習します。
* [クラスターのスケールアップとスケールダウン](service-fabric-cluster-scale-in-out.md)を行う方法を学習します。
