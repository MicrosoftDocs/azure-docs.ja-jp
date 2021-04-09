---
title: Azure 仮想ネットワークの Cloud Shell
description: Azure 仮想ネットワークに Cloud Shell をデプロイする
services: Azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/15/2020
ms.author: damaerte
ms.openlocfilehash: 58f6c7a3b5d68d2825cead545ba1b683d1faf1af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222804"
---
# <a name="deploy-cloud-shell-into-an-azure-virtual-network"></a>Azure 仮想ネットワークに Cloud Shell をデプロイする

通常の Cloud Shell セッションは、リソースとは別の Microsoft ネットワーク内のコンテナーで実行されます。 これは、コンテナー内で実行されているコマンドが、特定の仮想ネットワークからのみアクセスできるリソースにアクセスできないことを意味します。 たとえば、SSH を使用して、Cloud Shell からプライベート IP アドレスのみを持つ仮想マシンに接続したり、kubectl を使用して、アクセスがロックダウンされた Kubernetes クラスターに接続したりすることはできません。 

このオプションの機能は、これらの制限事項に対応しており、管理対象の Azure 仮想ネットワークに Cloud Shell をデプロイすることができます。 そこから、コンテナーと、選択した仮想ネットワーク内のリソースとのやりとりができます。  

次の図は、このシナリオでデプロイおよび使用されるリソース アーキテクチャを示しています。

![Cloud Shell 分離 VNET アーキテクチャを示す図。](media/private-vnet/data-diagram.png)

独自の Azure 仮想ネットワークで Cloud Shell を使用する前に、この機能をサポートするためにいくつかのリソースを作成する必要があります。 この記事では、ARM テンプレートを使用して必要なリソースを設定する方法について説明します。

> [!NOTE]
> これらのリソースは、仮想ネットワークに対して一度だけ設定する必要があります。 その後、この仮想ネットワークへのアクセス権を持つすべての管理者が共有できます。

## <a name="required-network-resources"></a>必要なネットワーク リソース

### <a name="virtual-network"></a>仮想ネットワーク
仮想ネットワークによって、1 つ以上のサブネットが作成されるアドレス空間が定義されます。

Cloud Shell に使用する目的の仮想ネットワークを識別する必要があります。 これは通常、管理対象のリソースを含む既存の仮想ネットワーク、または自分のリソースを含むネットワークとピアリングされたネットワークです。

### <a name="subnet"></a>Subnet
選択した仮想ネットワーク内で、Cloud Shell コンテナーに専用サブネットを使用する必要があります。 このサブネットは Azure Container Instances (ACI) サービスに委任されます。  ユーザーが仮想ネットワーク内の Cloud Shell コンテナーを要求すると、Cloud Shell によって ACI が使用されて、この委任されたサブネット内にコンテナーが作成されます。  このサブネット内に他のリソースを作成することはできません。

### <a name="network-profile"></a>ネットワーク プロファイル
ネットワーク プロファイルは、リソースの特定のネットワーク プロパティを指定する Azure リソースのネットワーク構成テンプレートです。

### <a name="azure-relay"></a>Azure Relay
[Azure Relay](../azure-relay/relay-what-is-it.md) を使用すると、直接到達できない 2 つのエンドポイントが通信できるようになります。 この場合、これは管理者のブラウザーがプライベート ネットワーク内のコンテナーと通信できるようにするために使用されます。

Cloud Shell に使用される Azure Relay インスタンスは、コンテナー リソースにアクセスできるネットワークを制御するように構成できます。 
- パブリック インターネットからアクセス可能:この構成では、Cloud Shell は、外部から内部リソースに接続する手段を提供します。 
- 指定されたネットワークからアクセス可能:この構成では、管理者は、Cloud Shell を使用するために、適切なネットワークで実行されているコンピューターから Azure portal にアクセスする必要があります。

## <a name="storage-requirements"></a>ストレージの要件
標準の Cloud Shell と同様に、仮想ネットワークで Cloud Shell を使用する場合は、ストレージ アカウントが必要です。 各管理者は、ファイルを格納するためのファイル共有を必要とします。  ストレージ アカウントは、Cloud Shell によって使用される仮想ネットワークからアクセスできる必要があります。 

## <a name="virtual-network-deployment-limitations"></a>仮想ネットワークのデプロイに関する制限事項
* 追加のネットワーク リソースが関係しているため、通常、仮想ネットワークで Cloud Shell を開始するには、標準の Cloud Shell セッションよりも時間がかかります。

* 現在、インド中部以外のすべての Cloud Shell リージョンがサポートされています。 

* [Azure Relay](../azure-relay/relay-what-is-it.md) は無料のサービスではありません。[価格](https://azure.microsoft.com/pricing/details/service-bus/)をご確認ください。 Cloud Shell シナリオでは、Cloud Shell を使用している間、管理者ごとに 1 つのハイブリッド接続が使用されます。 Cloud Shell セッションが完了すると、接続は自動的にシャットダウンされます。

## <a name="register-the-resource-provider"></a>リソース プロバイダーの登録

使用する仮想ネットワークが保持されているサブスクリプションに、Microsoft.ContainerInstances リソース プロバイダーを登録する必要があります。 `Set-AzContext -Subscription {subscriptionName}` で適切なサブスクリプションを選択し、次を実行します。

```powershell
PS> Get-AzResourceProvider -ProviderNamespace Microsoft.ContainerInstance | select ResourceTypes,RegistrationState

ResourceTypes                             RegistrationState
-------------                             -----------------
{containerGroups}                         Registered
...
```

**RegistrationState** が `Registered` の場合は、アクションは必要ありません。 `NotRegistered` の場合は、`Register-AzResourceProvider -ProviderNamespace Microsoft.ContainerInstance` を実行します。 

## <a name="deploy-network-resources"></a>ネットワーク リソースをデプロイする
 
### <a name="create-a-resource-group-and-virtual-network"></a>リソース グループと仮想ネットワークを作成する
必要な VNET が既に接続されている場合は、このセクションをスキップします。

Azure portal で、または Azure CLI や Azure PowerShell などを使用して、リソース グループと、その新しいリソース グループ内に仮想ネットワークを作成します。**リソース グループと仮想ネットワークは同じリージョンに存在する必要があります**。

### <a name="arm-templates"></a>ARM テンプレート
[Azure クイックスタート テンプレート](https://aka.ms/cloudshell/docs/vnet/template)を利用して仮想ネットワーク内に Cloud Shell リソースを作成し、[Azure クイックスタート テンプレート](https://aka.ms/cloudshell/docs/vnet/template/storage)を使用して必要なストレージを作成します。 リソース名 (主にファイル共有名) をメモしておきます。

### <a name="open-relay-firewall"></a>リレー ファイアウォールを開く
上記のテンプレートを使用して作成されたリレーに移動し、設定で [ネットワーク] を選択して、ブラウザー ネットワークからリレーへのアクセスを許可します。 既定では、リレーにアクセスできるのは、リレーが作成された仮想ネットワークからのみです。 

### <a name="configuring-cloud-shell-to-use-a-virtual-network"></a>仮想ネットワークを使用するように Cloud Shell を構成する
> [!NOTE]
> 各管理者が Cloud Shell を使用するには、この手順が完了している必要があります。

上記の手順を完了したら、Azure portal または https://shell.azure.com の Cloud Shell に移動します。 これらのエクスペリエンスのいずれかを、分離 Cloud Shell エクスペリエンスに接続するたびに使用する必要があります。

> [!NOTE]
> 過去に Cloud Shell が使用されている場合は、既存の clouddrive をマウント解除する必要があります。 これを行うには、アクティブな Cloud Shell セッションから `clouddrive unmount` を実行し、ページを更新します。

Cloud Shell に接続すると、最初の実行エクスペリエンスを示す画面が表示されます。 目的のシェル エクスペリエンスを選択し、[高度な設定を表示する] を選択して、[Show VNET isolation settings]\(VNET の分離設定を表示する\) チェックボックスをオンにします。 ポップアップのフィールドに入力します。  ほとんどのフィールドは、仮想ネットワーク内の Cloud Shell に関連付けることができる使用可能なリソースにオートコンプリートされます。  ファイル共有名は、ユーザーが入力する必要があります。


![Cloud Shell 分離 VNET の最初のエクスペリエンス設定を示す図。](media/private-vnet/vnet-settings.png)

## <a name="next-steps"></a>次のステップ
[Azure 仮想ネットワークについて](../virtual-network/virtual-networks-overview.md)
