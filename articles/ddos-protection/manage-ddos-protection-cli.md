---
title: Azure CLI を使用した Azure DDoS Protection プランの作成と構成
description: Azure CLI を使用して DDoS Protection プランを作成する方法について説明します
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: yitoh
ms.openlocfilehash: 59c5ca9ce9e95319b36e002da0b5d1438ef3fdd1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102203778"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard-using-azure-cli"></a>クイックスタート: Azure CLI を使用した Azure DDoS Protection Standard の作成と構成

Azure CLI を使用して Azure DDoS Protection Standard の使用を開始します。 

DDoS Protection プランでは、サブスクリプションの境界を越えて、DDoS Protection Standard が有効になった仮想ネットワークのセットを定義します。 組織で 1 つの DDoS Protection プランを構成し、複数のサブスクリプションから同じプランに仮想ネットワークをリンクできます。 

このクイックスタートでは、DDoS 保護プランを作成し、それを仮想ネットワークにリンクします。 

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- ローカルにインストールされた Azure CLI または Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このクイックスタートでは Azure CLI バージョン 2.0.28 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="create-a-ddos-protection-plan"></a>DDoS Protection プランを作成する

Azure で、関連するリソースをリソース グループに割り当てます。 既存のリソース グループを使用することも、新しいリソース グループを作成することもできます。

リソース グループを作成するには、[az group create](/cli/azure/group#az-group-create) を使用します。 この例では、リソース グループに _MyResourceGroup_ という名前を付け、"_米国東部_" の場所を使用します。

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus
```

次に、_MyDdosProtectionPlan_ という名前の DDoS Protection プランを作成します。

```azurecli-interactive
az network ddos-protection create \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
```

## <a name="enable-ddos-protection-for-a-virtual-network"></a>仮想ネットワークの DDoS 保護を有効にする

### <a name="enable-ddos-protection-for-a-new-virtual-network"></a>新しい仮想ネットワークの DDoS 保護を有効にする

仮想ネットワークを作成するときに、DDoS 保護を有効にすることができます。 この例では、仮想ネットワークに _MyVnet_ という名前を付けます。 

```azurecli-interactive
az network vnet create \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --location eastus \
    --ddos-protection true
    --ddos-protection-plan MyDdosProtectionPlan
```

仮想ネットワークに対して DDoS Standard が有効になっている場合、仮想ネットワークを別のリソース グループまたはサブスクリプションに移動することはできません。 DDoS Standard が有効になっている仮想ネットワークを移動する必要がある場合は、まず DDoS Standard を無効にし、仮想ネットワークを移動してから、DDoS Standard を有効にします。 移動後に、仮想ネットワーク内のすべての保護されたパブリック IP アドレスの自動調整されたポリシーしきい値がリセットされます。

### <a name="enable-ddos-protection-for-an-existing-virtual-network"></a>既存の仮想ネットワークの DDoS 保護を有効にする

[DDoS Protection プランを作成](#create-a-ddos-protection-plan)するときに、1 つまたは複数の仮想ネットワークをプランに関連付けることができます。 複数の仮想ネットワークを追加するには、名前または ID をスペースで区切って列挙します。 この例では、_MyVnet_ を追加します。

```azurecli-interactive
az group create \
    --name MyResourceGroup \
    --location eastus

az network ddos-protection create \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
    --vnets MyVnet
```

また、特定の仮想ネットワークに対して DDoS 保護を有効にすることもできます。

```azurecli-interactive
az network vnet update \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --ddos-protection true
    --ddos-protection-plan MyDdosProtectionPlan
```

## <a name="validate-and-test"></a>検証とテスト

まず、DDoS 保護プランの詳細を確認します。

```azurecli-interactive
az network ddos-protection show \
    --resource-group MyResourceGroup \
    --name MyDdosProtectionPlan
```

コマンドで DDoS 保護プランの正しい詳細が返されることを確認します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次のチュートリアルのためにリソースを保持しておくことができます。 不要になったら、_MyResourceGroup_ リソース グループを削除します。 リソース グループを削除する際に、DDoS Protection プランとその関連リソースもすべて削除します。 

リソース グループを削除するには、[az group delete](/cli/azure/group#az_group_delete) を使用します。

```azurecli-interactive
az group delete \
--name MyResourceGroup 
```

DDoS 保護を無効にするよう、特定の仮想ネットワークを更新します。

```azurecli-interactive
az network vnet update \
    --resource-group MyResourceGroup \
    --name MyVnet \
    --ddos-protection false
    --ddos-protection-plan ""
```

DDoS Protection プランを削除する場合は、最初にそのプランからすべての仮想ネットワークの関連付けを解除する必要があります。 

## <a name="next-steps"></a>次のステップ

DDoS 保護プラン用にテレメトリを表示および構成する方法を学習するには、チュートリアルに進んでください。

> [!div class="nextstepaction"]
> [DDoS 保護テレメトリの表示と構成](telemetry.md)
