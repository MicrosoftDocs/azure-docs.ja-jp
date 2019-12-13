---
title: Azure マネージド アプリケーションのリソースを更新する | Microsoft Docs
description: Azure マネージド アプリケーションのマネージド リソース グループを操作する方法について説明します。
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/26/2017
ms.author: tomfitz
ms.openlocfilehash: 7f00a99a31a4543ef45c90a86820e627134d8963
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74888701"
---
# <a name="work-with-resources-in-the-managed-resource-group-for-azure-managed-application"></a>Azure マネージド アプリケーションのマネージド リソース グループを操作する

この記事では、マネージド アプリケーションの一部としてデプロイされているリソースを更新する方法について説明します。 マネージド アプリケーションの発行元として、マネージド リソース グループ内のリソースにアクセスできます。 これらのリソースを更新するには、マネージド アプリケーションに関連付けられているマネージド リソース グループを見つけて、そのリソース グループ内のリソースにアクセスする必要があります。

この記事では、マネージド アプリケーションを「[Managed Web Application (IaaS) with Azure management services](https://github.com/Azure/azure-managedapp-samples/tree/master/Managed%20Application%20Sample%20Packages/201-managed-web-app)」(Azure 管理サービスを使用するマネージド Web アプリケーション (IaaS)) サンプル プロジェクトにデプロイしていることを前提としています。 そのマネージド アプリケーションには、**Standard_D1_v2** 仮想マシンが含まれています。 マネージド アプリケーションをデプロイしていない場合でも、この記事で、マネージド リソース グループを更新するための手順に慣れることができます。

次の図は、デプロイ済みのマネージド アプリケーションを示しています。

![デプロイ済みのマネージド アプリケーション](./media/update-managed-resources/deployed.png)

この記事では、Azure CLI を使用して以下の操作を行います。

* マネージド アプリケーションを識別する
* マネージド リソース グループを識別する
* マネージド リソース グループ内の仮想マシン リソースを識別する
* VM のサイズを変更する (利用されていなければ小さなサイズに、高い負荷をサポートする場合は大きなサイズに変更します)
* 許可される場所を指定するポリシーをマネージド リソース グループに割り当てる

## <a name="get-managed-application-and-managed-resource-group"></a>マネージド アプリケーションとマネージド リソース グループを取得する

リソース グループ内のマネージド アプリケーションを取得するには、次のコマンドを使用します。

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')]"
```

マネージド リソース グループの ID を取得するには、次のコマンドを使用します。

```azurecli-interactive
az managedapp list --query "[?contains(resourceGroup,'DemoApp')].{ managedResourceGroup:managedResourceGroupId }"
```

## <a name="resize-vms-in-managed-resource-group"></a>マネージド リソース グループ内の VM のサイズを変更する

マネージド リソース グループ内の仮想マシンを表示するには、マネージド リソース グループの名前を指定します。

```azurecli-interactive
az vm list -g DemoApp6zkevchqk7sfq --query "[].{VMName:name,OSType:storageProfile.osDisk.osType,VMSize:hardwareProfile.vmSize}"
```

VM のサイズを更新するには、次のコマンドを使用します。

```azurecli-interactive
az vm resize --size Standard_D2_v2 --ids $(az vm list -g DemoApp6zkevchqk7sfq --query "[].id" -o tsv)
```

操作が完了したら、アプリケーションが Standard D2 v2 で実行されていることを確認します。

![Standard D2 v2 を使用するマネージド アプリケーション](./media/update-managed-resources/upgraded.png)

## <a name="apply-policy-to-managed-resource-group"></a>マネージド リソース グループにポリシーを適用する

マネージド リソース グループを取得し、そのスコープのポリシーを割り当てます。 ポリシー **e56962a6-4747-49cd-b67b-bf8b01975c4c** は、許可される場所を指定するための組み込みポリシーです。

```azurecli-interactive
managedGroup=$(az managedapp show --name <app-name> --resource-group DemoApp --query managedResourceGroupId --output tsv)

az policy assignment create --name locationAssignment --policy e56962a6-4747-49cd-b67b-bf8b01975c4c --scope $managedGroup --params '{
                            "listofallowedLocations": {
                                "value": [
                                    "northeurope",
                                    "westeurope"
                                ]
                            }
                        }'
```

許可される場所を表示するには、次のコマンドを使用します。

```azurecli-interactive
az policy assignment show --name locationAssignment --scope $managedGroup --query parameters.listofallowedLocations.value
```

ポリシーの割り当てがポータルに表示されます。

![ポリシーの割り当ての表示](./media/update-managed-resources/assignment.png)

## <a name="next-steps"></a>次の手順

* マネージド アプリケーションの概要については、[マネージド アプリケーションの概要](overview.md)に関するページをご覧ください。
* サンプル プロジェクトについては、[Azure マネージド アプリケーションのサンプル プロジェクト](sample-projects.md)に関する記事を参照してください。
