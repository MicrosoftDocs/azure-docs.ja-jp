---
title: 'チュートリアル: Azure VMware Solution のプライベート クラウドをデプロイする'
description: Azure VMware Solution のプライベート クラウドを作成してデプロイする方法を説明します
ms.topic: tutorial
ms.date: 02/22/2021
ms.openlocfilehash: ed916305cd1a67162f07c24e3bf97766e5389b74
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103462169"
---
# <a name="tutorial-deploy-an-azure-vmware-solution-private-cloud"></a>チュートリアル: Azure VMware Solution のプライベート クラウドをデプロイする

Azure VMware Solution を使用すると、Azure に vSphere クラスターをデプロイできます。 初期デプロイ時の最小ホスト数は 3 つです。 追加のホストは、クラスターあたり最大 16 個まで一度に 1 つずつ追加できます。

Azure VMware Solution では、最初はオンプレミスの vCenter でプライベート クラウドを管理することができないため、追加の構成と接続が必要です。 このチュートリアルでは、これらの手順と、関連する前提条件について説明します。

このチュートリアルでは、次の方法について説明します。

> [!div class="checklist"]
> * Azure VMware Solution のプライベート クラウドを作成する
> * デプロイされたプライベート クラウドを確認する

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- プライベート クラウドを作成するための適切な管理者権限とアクセス許可。 サブスクリプションで、少なくとも共同作成者レベルである必要があります。
- [計画](production-ready-deployment-steps.md)の記事で収集した情報に従って、Azure VMware Solution をデプロイします。
- 適切なネットワークが構成されていることを確認するには、[ネットワークのチェックリストに関するチュートリアル](tutorial-network-checklist.md)をご確認ください。
- [ホストの要求と Microsoft.AVS リソース プロバイダーの有効化](enable-azure-vmware-solution.md)に関する記事の説明に基づいて、ホストがプロビジョニングされ、Microsoft.AVS リソース プロバイダーが登録されていること。

## <a name="create-a-private-cloud"></a>プライベート クラウドを作成する

[Azure portal](#azure-portal) または [Azure CLI](#azure-cli) を使用して、Azure VMware Solution のプライベート クラウドを作成できます。

### <a name="azure-portal"></a>Azure portal

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]

### <a name="azure-cli"></a>Azure CLI

Azure portal の代わりに、Azure Cloud Shell が使用されている Azure CLI を使って、Azure VMware Solution のプライベート クラウドを作成できます。  Azure VMware Solution で使用できるコマンドの一覧については、[Azure VMware コマンド](/cli/azure/ext/vmware/vmware)のページを参照してください。

#### <a name="open-azure-cloud-shell"></a>Azure Cloud Shell を開く

コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/bash](https://shell.azure.com/bash) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けます。その後、**Enter** キーを押してそれを実行します。

#### <a name="create-a-resource-group"></a>リソース グループを作成する

['az group create'](/cli/azure/group) コマンドを使用して、リソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive

az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>プライベート クラウドを作成する

リソース グループとプライベート クラウドの名前、場所、クラスターのサイズを指定します。

| プロパティ  | 説明  |
| --------- | ------------ |
| **-g** (リソース グループ名)     | プライベート クラウド リソースのリソース グループの名前。        |
| **-n** (プライベート クラウド名)     | Azure VMware Solution のプライベート クラウドの名前。        |
| **--location**     | プライベート クラウドに使用される場所。         |
| **--cluster-size**     | クラスターのサイズ。 最小値は 3 です。         |
| **--network-block**     | プライベート クラウドに使用する CIDR IP アドレスのネットワーク ブロック。 アドレス ブロックは、サブスクリプションとオンプレミス ネットワークにある他の仮想ネットワークで使用されているアドレス ブロックと重複しないようにする必要があります。        |
| **--sku** | SKU 値: AV36 |

```azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
```

## <a name="azure-vmware-commands"></a>Azure VMware コマンド

Azure VMware Solution で使用できるコマンドの一覧については、[Azure VMware コマンド](/cli/azure/ext/vmware/vmware)のページを参照してください。

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * Azure VMware Solution のプライベート クラウドを作成する
> * デプロイされたプライベート クラウドを確認する
> * Azure VMware Solution のプライベート クラウドを削除する

次のチュートリアルに進み、ジャンプ ボックスの作成方法を習得します。 プライベート クラウドにローカルで接続できるよう、ジャンプ ボックスを使用して環境に接続します。


> [!div class="nextstepaction"]
> [Azure VMware Solution プライベート クラウドにアクセスする](tutorial-access-private-cloud.md)