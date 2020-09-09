---
title: チュートリアル - Azure に vSphere クラスターをデプロイする
description: Azure VMware Solution を使用して Azure に vSphere クラスターをデプロイする方法について説明します
ms.topic: tutorial
ms.date: 09/07/2020
ms.openlocfilehash: 69a29a459ba283bb34169112ac2fa174ac6a14af
ms.sourcegitcommit: 8791f69d44150767807d215cafc4076f3ed43f9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/08/2020
ms.locfileid: "89512373"
---
# <a name="tutorial-deploy-an-azure-vmware-solution-private-cloud-in-azure"></a>チュートリアル:Azure に Azure VMware Solution のプライベート クラウドをデプロイする

Azure VMware Solution を使用すると、Azure に vSphere クラスターをデプロイできます。 初期デプロイ時の最小ホスト数は 3 つです。 追加のホストは、クラスターあたり最大 16 個まで一度に 1 つずつ追加できます。 

Azure VMware Solution では、最初はオンプレミスの vCenter でプライベート クラウドを管理することができないため、ローカルの vCenter インスタンス、仮想ネットワークなどに対する追加の構成と接続が必要です。 このチュートリアルでは、これらの手順と、関連する前提条件について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure VMware Solution のプライベート クラウドを作成する
> * デプロイされたプライベート クラウドを確認する

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- プライベート クラウドを作成するための適切な管理者権限とアクセス許可。
- 適切なネットワークが構成されていることを確認するには、[ネットワークのチェックリストに関するチュートリアル](tutorial-network-checklist.md)をご確認ください。

## <a name="register-the-resource-provider"></a>リソース プロバイダーの登録

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="create-a-private-cloud"></a>プライベート クラウドを作成する

[Azure portal](#azure-portal) または [Azure CLI](#azure-cli) を使用して、Azure VMware Solution のプライベート クラウドを作成できます。

### <a name="azure-portal"></a>Azure portal

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-avs-private-cloud-azure-portal-steps.md)]

### <a name="azure-cli"></a>Azure CLI

Azure portal の代わりに、Azure Cloud Shell が使用されている Azure CLI を使って、Azure VMware Solution のプライベート クラウドを作成できます。 これは、無料の対話型シェルで、一般的な Azure ツールがプレインストールされ、お客様のアカウントで使用するよう構成されています。 

#### <a name="open-azure-cloud-shell"></a>Azure Cloud Shell を開く

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/bash](https://shell.azure.com/bash) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けます。その後、**Enter** キーを押してそれを実行します。

#### <a name="create-a-resource-group"></a>リソース グループを作成する

[az group create](/cli/azure/group) コマンドを使用して、リソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```
azurecli-interactive
az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>プライベート クラウドを作成する

リソース グループ名、プライベート クラウド名、場所、クラスターのサイズを指定します。

| プロパティ  | 説明  |
| --------- | ------------ |
| **-g** (リソース グループ名)     | プライベート クラウド リソースのリソース グループの名前。        |
| **-n** (プライベート クラウド名)     | Azure VMware Solution のプライベート クラウドの名前。        |
| **--location**     | プライベート クラウドに使用される場所。         |
| **--cluster-size**     | クラスターのサイズ。 最小値は 3 です。         |
| **--network-block**     | プライベート クラウドに使用する CIDR IP アドレスのネットワーク ブロック。 アドレス ブロックは、サブスクリプションとオンプレミス ネットワークにある他の仮想ネットワークで使用されているアドレス ブロックと重複しないようにする必要があります。        |
| **--sku** | SKU 値: AV36 |

```
azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
```

## <a name="delete-a-private-cloud-azure-portal"></a>プライベート クラウドを削除する (Azure portal)

Azure VMware Solution のプライベート クラウドが不要になった場合は、削除できます。 プライベート クラウドを削除すると、すべてのクラスターとそのすべてのコンポーネントが削除されます。

これを行うには、Azure portal でプライベート クラウドに移動し、 **[削除]** を選択します。 確認ページでプライベート クラウドの名前を確認し、 **[はい]** を選択します。

> [!CAUTION]
> プライベート クラウドを削除する操作は、元に戻すことができません。 プライベート クラウドを削除すると、実行中のすべてのワークロードとコンポーネントが中止され、パブリック IP アドレスを含むすべてのプライベート クラウド データと構成設定が破棄されるので、データを回復することはできなくなります。 

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Azure VMware Solution のプライベート クラウドを作成する
> * デプロイされたプライベート クラウドを確認する

次のチュートリアルに進み、プライベート クラウド クラスターのローカル管理を設定する一環として、プライベート クラウドで使用する仮想ネットワークを作成する方法を学習します。

> [!div class="nextstepaction"]
> [仮想ネットワークの作成](tutorial-configure-networking.md)
