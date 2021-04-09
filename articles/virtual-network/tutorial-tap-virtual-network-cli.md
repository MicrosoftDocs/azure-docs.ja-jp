---
title: VNet TAP を作成、変更、または削除する - Azure CLI
description: Azure CLI を使用して仮想ネットワーク TAP を作成、変更、削除する方法について説明します。
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/18/2018
ms.author: kaanan
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8e3a56e4a6eb1fb6eb633021178ef78f8ac7287d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96014790"
---
# <a name="work-with-a-virtual-network-tap-using-the-azure-cli"></a>Azure CLI を使用して仮想ネットワーク TAP の作業を行う

Azure 仮想ネットワーク TAP (ターミナル アクセス ポイント) を使用すると、仮想マシン ネットワークのトラフィックをネットワーク パケット コレクターまたは分析ツールに連続してストリーミングできます。 コレクターまたは分析ツールは、[ネットワーク仮想アプライアンス](https://azure.microsoft.com/solutions/network-appliances/) パートナーから提供されています。 仮想ネットワーク TAP を使用できることが検証されたパートナー ソリューションの一覧については、[パートナー ソリューション](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions)に関するページを参照してください。 

## <a name="create-a-virtual-network-tap-resource"></a>仮想ネットワーク TAP リソースを作成する

仮想ネットワーク TAP リソースを作成する前に、[前提条件](virtual-network-tap-overview.md#prerequisites)をお読みください。 以下のコマンドは、[Azure Cloud Shell](https://shell.azure.com/bash) で、またはコンピューターから Azure コマンド ライン インターフェイス (CLI) を実行することで、実行できます。 Azure Cloud Shell は無料の対話型シェルであり、コンピューターに Azure CLI をインストールする必要はありません。 適切な[アクセス許可](virtual-network-tap-overview.md#permissions)を持っているアカウントで Azure にサインインする必要があります。 この記事では、Azure CLI バージョン 2.0.46 以降が必要です。 インストールされているバージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」を参照してください。 仮想ネットワーク TAP は、現在、拡張機能として使用できます。 拡張機能をインストールするには、`az extension add -n virtual-network-tap` を実行する必要があります。 Azure CLI をローカルで実行している場合、`az login` を実行して Azure との接続を作成することも必要です。

1. サブスクリプションの ID を取得して変数に格納し、後の手順で使用します。

   ```azurecli-interactive
   subscriptionId=$(az account show \
   --query id \
   --out tsv)
   ```

2. 仮想ネットワーク TAP リソースの作成に使用するサブスクリプション ID を設定します。

   ```azurecli-interactive
   az account set --subscription $subscriptionId
   ```

3. 仮想ネットワーク TAP リソースの作成に使用するサブスクリプション ID を再登録します。 TAP リソースを作成するときに登録エラーが発生する場合は、次のコマンドを実行します。

   ```azurecli-interactive
   az provider register --namespace Microsoft.Network --subscription $subscriptionId
   ```

4. 仮想ネットワーク TAP のターゲットがコレクターまたは分析ツールに対するネットワーク仮想アプライアンス上のネットワーク インターフェイスの場合は、次のようにします。

   - ネットワーク仮想アプライアンスのネットワーク インターフェイスの IP 構成を変数に取得し、後の手順で使用します。 ID は、TAP のトラフィックを集約するエンド ポイントです。 次の例では、*myResourceGroup* という名前のリソース グループ内にある *myNetworkInterface* という名前のネットワーク インターフェイスに対する *ipconfig1* IP 構成の ID を取得しています。

      ```azurecli-interactive
       IpConfigId=$(az network nic ip-config show \
       --name ipconfig1 \
       --nic-name myNetworkInterface \
       --resource-group myResourceGroup \
       --query id \
       --out tsv)
      ```

   - ターゲットとしての IP 構成の ID とオプションのポート プロパティを使用して、westcentralus Azure リージョン内に仮想ネットワーク TAP を作成します。 ポートでは、TAP トラフィックを受信するネットワーク インターフェイス IP 構成上のターゲット ポートを指定します。  

      ```azurecli-interactive
       az network vnet tap create \
       --resource-group myResourceGroup \
       --name myTap \
       --destination $IpConfigId \
       --port 4789 \
       --location westcentralus
      ```

5. 仮想ネットワーク TAP のターゲットが Azure 内部ロード バランサーの場合は、次のようにします。
  
   - Azure 内部ロード バランサーのフロントエンド IP 構成を変数に取得し、後の手順で使用します。 ID は、TAP のトラフィックを集約するエンド ポイントです。 次の例では、*myResourceGroup* という名前のリソース グループ内の *myInternalLoadBalancer* という名前のロード バランサーに対する *frontendipconfig1* フロントエンド IP 構成の ID を取得しています。

      ```azurecli-interactive
      FrontendIpConfigId=$(az network lb frontend-ip show \
      --name frontendipconfig1 \
      --lb-name myInternalLoadBalancer \
      --resource-group myResourceGroup \
      --query id \
      --out tsv)
      ```

   - ターゲットとしてのフロントエンド IP 構成の ID とオプションのポート プロパティを使用して、仮想ネットワーク TAP を作成します。 ポートでは、TAP トラフィックを受信するフロントエンド IP 構成上のターゲット ポートを指定します。  

      ```azurecli-interactive
      az network vnet tap create \
      --resource-group myResourceGroup \
      --name myTap \
      --destination $FrontendIpConfigId \
      --port 4789 \
     --location westcentralus
     ```

6. 仮想ネットワーク TAP の作成を確認します。

   ```azurecli-interactive
   az network vnet tap show \
   --resource-group myResourceGroup
   --name myTap
   ```

## <a name="add-a-tap-configuration-to-a-network-interface"></a>TAP 構成をネットワーク インターフェイスに追加する

1. 既存の仮想ネットワーク TAP リソースの ID を取得します。 次の例では、*myResourceGroup* という名前のリソース グループ内の *myTap* という名前の仮想ネットワーク TAP を取得しています。

   ```azurecli-interactive
   tapId=$(az network vnet tap show \
   --name myTap \
   --resource-group myResourceGroup \
   --query id \
   --out tsv)
   ```

2. 監視対象仮想マシンのネットワーク インターフェイスに TAP 構成を作成します。 次の例では、*myNetworkInterface* という名前のネットワーク インターフェイスに対する TAP 構成を作成します。

   ```azurecli-interactive
   az network nic vtap-config create \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --vnet-tap $tapId \
   --name mytapconfig \
   --subscription subscriptionId
   ```

3. TAP 構成の作成を確認します。

   ```azurecli-interactive
   az network nic vtap-config show \
   --resource-group myResourceGroup \
   --nic-name myNetworkInterface \
   --name mytapconfig \
   --subscription subscriptionId
   ```

## <a name="delete-the-tap-configuration-on-a-network-interface"></a>ネットワーク インターフェイス上の TAP 構成を削除する

   ```azurecli-interactive
   az network nic vtap-config delete \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --name myTapConfig \
   --subscription subscriptionId
   ```

## <a name="list-virtual-network-taps-in-a-subscription"></a>サブスクリプション内の仮想ネットワーク TAP の一覧を表示する

   ```azurecli-interactive
   az network vnet tap list
   ```

## <a name="delete-a-virtual-network-tap-in-a-resource-group"></a>リソース グループ内の仮想ネットワーク TAP を削除する

   ```azurecli-interactive
   az network vnet tap delete \
   --resource-group myResourceGroup \
   --name myTap
   ```
