---
title: Azure Service Fabric クラスターの構成をアップグレードする | Microsoft Docs
description: Resource Manager テンプレートを使用して Azure で Service Fabric クラスターを実行する構成をアップグレードする方法について説明します。
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 77b9b20f99f00ef87c4907c2890cb3a21d20ec75
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59789773"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>Azure でクラスターの構成をアップグレードする 

この記事では、Service Fabric クラスターのさまざまなファブリックの設定をカスタマイズする方法について説明します。 Azure でホストされているクラスターの場合、[Azure portal](https://portal.azure.com) または Azure Resource Manager テンプレートを使って設定をカスタマイズできます。

> [!NOTE]
> すべての設定をポータルで使用できるとは限りません。[ベスト プラクティスは、Azure Resource Manager テンプレートを使用して設定をカスタマイズすること](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)です。ポータルは Service Fabric の開発とテストのシナリオ専用です。
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Resource Manager テンプレートを使用してクラスター設定をカスタマイズする
Azure クラスターは、JSON の Resource Manager テンプレートを使って構成できます。 さまざまな設定の詳細については、[クラスターの構成設定](service-fabric-cluster-fabric-settings.md)に関する記事をご覧ください。 例として、次の手順はで、Azure Resource Explorer を使って *MaxDiskQuotaInMB* という新しい設定を *Diagnostics* セクションに追加する方法を示します。

1. https://resources.azure.com に移動します
2. **[サブスクリプション]** -> **\<ご使用のサブスクリプション>** -> **[resourceGroups]** -> **\<ご使用のリソース グループ>** -> **[プロバイダー]** -> **[Microsoft.ServiceFabric]** -> **[クラスター]** -> **\<ご使用のクラスター名 >** の順に展開して、サブスクリプションに移動します
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

- [Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template) を使用し、Resource Manager テンプレートをエクスポートして更新します。
- [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell) を使用し、Resource Manager テンプレートをエクスポートして更新します。
- [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli) を使用し、Resource Manager テンプレートをエクスポートして更新します。
- 設定を直接変更するには、Azure PowerShell [Set-AzServiceFabricSetting](https://docs.microsoft.com/powershell/module/az.servicefabric/Set-azServiceFabricSetting) および [Remove-AzServiceFabricSetting](https://docs.microsoft.com/powershell/module/az.servicefabric/Remove-azServiceFabricSetting) コマンドを使います。
- 設定を直接変更するには、Azure CLI の [az sf cluster setting](https://docs.microsoft.com/cli/azure/sf/cluster/setting) コマンドを使います。

## <a name="next-steps"></a>次の手順
* [Service Fabric クラスターの設定](service-fabric-cluster-fabric-settings.md)について学習します。
* [クラスターのスケールアップとスケールダウン](service-fabric-cluster-scale-up-down.md)を行う方法を学習します。
