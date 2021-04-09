---
title: 仮想マシン スケール セット テンプレートの詳細情報
description: いくつかの簡単な手順に従って、Azure 仮想マシン スケール セット用の基本的なスケール セット テンプレートを作成する方法について説明します。
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: template
ms.date: 04/26/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 55c826b4baf38732684aaa0465aeaab6a45564db
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "87831500"
---
# <a name="learn-about-virtual-machine-scale-set-templates"></a>仮想マシン スケール セット テンプレートの詳細情報
[Azure Resource Manager テンプレート](../azure-resource-manager/templates/overview.md#template-deployment-process)は、関連するリソースのグループをデプロイするための優れた方法です。 このチュートリアル シリーズでは、基本のスケール セット テンプレートを作成する方法と、そのテンプレートをさまざまなシナリオに適合するように変更する方法を示します。 すべての例は、こちらの [GitHub リポジトリ](https://github.com/gatneil/mvss)を基にしています。

このテンプレートは単純なものにしてあります。 スケール セット テンプレートの完全な例については、[Azure クイックスタート テンプレート GitHub リポジトリ](https://github.com/Azure/azure-quickstart-templates)で、`vmss` という文字列を含むフォルダーを検索してください。

テンプレートの作成を熟知している場合は、"次の手順" セクションに進んで、このテンプレートを変更する方法をご覧ください。

## <a name="define-schema-and-contentversion"></a>$schema と contentVersion を定義する
まず、テンプレートの `$schema` と `contentVersion` を定義します。 `$schema` 要素は、テンプレートの言語のバージョンを定義し、Visual Studio 構文の強調表示や同様の検証機能で使用されます。 `contentVersion` 要素は、Azure では使用されません。 テンプレートのバージョンを追跡するときに役立ちます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
```

## <a name="define-parameters"></a>パラメーターを定義する
次に、2 つのパラメーター (`adminUsername` と `adminPassword`) を定義します。 これらのパラメーターは、デプロイ時に指定する値です。 パラメーター `adminUsername` は単なる `string` 型ですが、`adminPassword` はシークレットであるため、`securestring` 型にします。 これらのパラメーターは、後ほどスケール セット構成に渡されます。

```json
  "parameters": {
    "adminUsername": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    }
  },
```
## <a name="define-variables"></a>変数の定義
Resource Manager テンプレートでは、テンプレート内で後ほど使用される変数を定義することもできます。 この例では変数を使用しないため、JSON オブジェクトは空のままです。

```json
  "variables": {},
```

## <a name="define-resources"></a>リソースを定義する
次に、テンプレートの resources セクションに進みます。 ここでは、実際に何をデプロイするかを定義します。 `parameters` と `variables` とは異なり (これらは JSON オブジェクトです)、`resources` は JSON オブジェクトの JSON リストです。

```json
   "resources": [
```

すべてのリソースのプロパティ `type`、`name`、`apiVersion`、`location` を指定する必要があります。 この例の最初のリソースには、type [Microsoft.Network/virtualNetwork](/azure/templates/microsoft.network/virtualnetworks)、name `myVnet`、apiVersion `2018-11-01` が含まれています。 (リソースの種類に対する最新の API バージョンを確認するには、[Azure Resource Manager テンプレートのリファレンス](/azure/templates/)を参照してください。)

```json
     {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "apiVersion": "2018-11-01",
```

## <a name="specify-location"></a>場所を指定する
仮想マシンの場所を指定するには、[Resource Manager テンプレート関数](../azure-resource-manager/templates/template-functions.md)を使用します。 この関数は、`"[<template-function>]"` のように、引用符や角かっこで囲む必要があります。 この例では、`resourceGroup` 関数を使用します。 この関数には引数がなく、JSON オブジェクトとこのデプロイのデプロイ先であるリソース グループに関するメタデータを返します。 リソース グループは、デプロイ時にユーザーによって設定されます。 この値はその後、JSON オブジェクトから場所を取得するために、`.location` を使用してこの JSON オブジェクト内にインデックス化されます。

```json
       "location": "[resourceGroup().location]",
```

## <a name="specify-virtual-network-properties"></a>仮想ネットワークのプロパティを指定する
各 Resource Manager リソースには、リソース固有の構成を指定するための独自の `properties` セクションがあります。 この例では、仮想ネットワークにはプライベート IP アドレス範囲 `10.0.0.0/16` を使用するサブネットが 1 つだけ存在するように指定しています。 スケール セットは、常に 1 つのサブネットに収まります。 複数のサブネットにまたがることできません。

```json
       "properties": {
         "addressSpace": {
           "addressPrefixes": [
             "10.0.0.0/16"
           ]
         },
         "subnets": [
           {
             "name": "mySubnet",
             "properties": {
               "addressPrefix": "10.0.0.0/16"
             }
           }
         ]
       }
     },
```

## <a name="add-dependson-list"></a>dependsOn リストを追加する
必須のプロパティである `type`、`name`、`apiVersion`、`location` に加えて、必要に応じて各リソースに `dependsOn` 文字列リストを追加することができます。 このリストには、このリソースをデプロイする前にデプロイを完了する必要があるその他のリソースを指定します。

この例では、リストには前の例の仮想ネットワークという要素が 1 つだけ指定されています。 この依存関係を指定するのは、スケール セットで VM を作成するには、ネットワークが存在している必要があるためです。 これにより、スケール セットは、作成する VM に対して、ネットワーク プロパティに指定されている IP アドレス範囲からプライベート IP アドレスを割り当てることができます。 dependsOn リストの各文字列の形式は `<type>/<name>` です。 `type` と `name` は、仮想ネットワークのリソース定義で先ほど使用したものと同じです。

```json
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "apiVersion": "2019-03-01",
       "location": "[resourceGroup().location]",
       "dependsOn": [
         "Microsoft.Network/virtualNetworks/myVnet"
       ],
```
## <a name="specify-scale-set-properties"></a>スケール セットのプロパティを指定する
スケール セットには、スケール セットで VM をカスタマイズするための多くのプロパティがあります。 これらのプロパティの完全な一覧については、[テンプレート リファレンス](/azure/templates/microsoft.compute/virtualmachinescalesets)を参照してください。 このチュートリアルでは、一般的に使用されるいくつかのプロパティのみが設定されます。
### <a name="supply-vm-size-and-capacity"></a>VM のサイズと容量を指定する
スケール セットは、作成する VM のサイズ ("SKU 名") と、作成する VM の数 ("SKU 容量") を把握する必要があります。 利用可能な VM サイズを確認するには、[VM サイズに関するドキュメント](../virtual-machines/sizes.md)を参照してください。

```json
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
       },
```

### <a name="choose-type-of-updates"></a>更新の種類を選択する
さらに、スケール セットは、スケール セットに関する更新を処理する方法を知る必要があります。 現時点では、`Manual`、`Rolling` と `Automatic` の 3 つのオプションがあります。 この 2 つの違いの詳細については、「[仮想マシン スケール セットのアップグレード](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)」を参照してください。

```json
       "properties": {
         "upgradePolicy": {
           "mode": "Manual"
         },
```

### <a name="choose-vm-operating-system"></a>VM のオペレーティング システムを選択する
スケール セットは、VM に配置するオペレーティング システムを知る必要があります。 ここでは、完全にパッチが適用された Ubuntu 16.04-LTS イメージを使用して VM を作成します。

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.04-LTS",
               "version": "latest"
             }
           },
```

### <a name="specify-computernameprefix"></a>computerNamePrefix を指定する
スケール セットは複数の VM をデプロイします。 個別の VM 名を指定する代わりに、`computerNamePrefix` を指定します。 スケール セットは、各 VM のプレフィックスにインデックスを追加するため、VM 名の形式は `<computerNamePrefix>_<auto-generated-index>` になります。

次のスニペットでは、スケール セット内のすべての VM に対して、すでに説明した管理者のユーザー名とパスワードを設定するパラメーターが使用されています。 このプロセスでは、`parameters` テンプレート関数が使用されます。 この関数は、参照するパラメーターを指定する文字列を受け取り、そのパラメーターの値を出力します。

```json
           "osProfile": {
             "computerNamePrefix": "vm",
             "adminUsername": "[parameters('adminUsername')]",
             "adminPassword": "[parameters('adminPassword')]"
           },
```

### <a name="specify-vm-network-configuration"></a>VM のネットワーク構成を指定する
最後に、スケール セット内の VM のネットワーク構成を指定します。 この場合、必要なのは、以前に作成したサブネットの ID の指定だけです。 これにより、このサブネットにネットワーク インターフェイスを配置するようスケール セットに通知されます。

サブネットを含む仮想ネットワークの ID は、`resourceId` テンプレート関数を使用して取得できます。 この関数は、リソースの種類と名前を引数として受け取り、そのリソースの完全修飾識別子を返します。 この ID の形式は `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>` になります。

ただし、仮想ネットワークの ID では十分ではありません。 スケール セットの VM が配置される特定のサブネットを指定します。 これを行うには、仮想ネットワークの ID に `/subnets/mySubnet` を連結します。 結果は、サブネットの完全修飾 ID です。 この連結は、一連の文字列を引数として受け取り、それらを連結したものを返す `concat` 関数で実行します。

```json
           "networkProfile": {
             "networkInterfaceConfigurations": [
               {
                 "name": "myNic",
                 "properties": {
                   "primary": "true",
                   "ipConfigurations": [
                     {
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
                           "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
                         }
                       }
                     }
                   ]
                 }
               }
             ]
           }
         }
       }
     }
   ]
}

```

## <a name="next-steps"></a>次のステップ

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]