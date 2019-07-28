---
title: Azure CLI を使用して Azure Blockchain Service を作成する
description: Azure Blockchain Service を使用し、Azure CLI を使用してブロックチェーン メンバーを作成します。
services: azure-blockchain
keywords: ''
author: pataltimore
ms.author: patricka
ms.date: 05/29/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: be5a8151f0de0a33db09194a7159aded6848c78a
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416176"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>クイック スタート:Azure CLI を使用して Azure Blockchain Service ブロックチェーン メンバーを作成する

Azure Blockchain Service は、スマート コントラクト内でビジネス ロジックを実行するために使用できるブロックチェーン プラットフォームです。 このクイックスタートでは、Azure CLI を使用してブロックチェーン メンバーを作成することから始める方法について説明します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/bash](https://shell.azure.com/bash) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、Enter キーを押して実行します。

CLI をローカルにインストールして使用する場合、このクイックスタートでは、Azure CLI バージョン 2.0.51 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)」を参照してください。

## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](https://docs.microsoft.com/cli/azure/group) コマンドでリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-blockchain-member"></a>ブロックチェーン メンバーを作成する

新しいコンソーシアムで Quorum 台帳プロトコルを実行するブロックチェーン メンバーを Azure Blockchain Service で作成します。 渡す必要があるいくつかのパラメーターとプロパティがあります。 パラメーターの例は、実際の値に置き換えてください。

```azurecli-interactive
az resource create --resource-group myResourceGroup --name myblockchainmember --resource-type Microsoft.Blockchain/blockchainMembers --is-full-object --properties "{ \"location\": \"eastus\", \"properties\": {\"password\": \"strongMemberAccountPassword@1\", \"protocol\": \"Quorum\", \"consortium\": \"myConsortiumName\", \"consortiumManagementAccountPassword\": \"strongConsortiumManagementPassword@1\" }, \"sku\": { \"name\": \"S0\" } }"
```

| パラメーター | 説明 |
|---------|-------------|
| **resource-group** | Azure Blockchain Service リソースが作成されるリソース グループ名。 前のセクションで作成したリソース グループを使用します
| **name** | Azure Blockchain Service のブロックチェーン メンバーを識別する一意の名前。 名前はパブリック エンドポイント アドレスに使用されます。 たとえば、「 `myblockchainmember.blockchain.azure.com` 」のように入力します。
| **location** | ブロックチェーン メンバーが作成される Azure リージョン。 たとえば、「 `eastus` 」のように入力します。 ユーザーや他の Azure アプリケーションに最も近い場所を選択します。
| **password** | メンバーの既定のトランザクション ノードのパスワード。 このパスワードは、ブロックチェーン メンバーの既定のトランザクション ノード パブリック エンドポイントに接続する際の基本認証に使用します。
| **consortium** | 参加または作成するコンソーシアムの名前。
| **consortiumAccountPassword** | コンソーシアム アカウントのパスワードは、メンバー アカウントのパスワードとも呼ばれます。 メンバー アカウントのパスワードは、メンバー用に作成される Ethereum アカウントの秘密キーの暗号化に使用されます。 メンバー アカウントとメンバー アカウントのパスワードをコンソーシアムの管理に使用します。
| **skuName** | レベルの種類。 Standard には S0 を使用し、Basic には B0 を使用します。

ブロックチェーン メンバーとサポート リソースの作成には約 10 分かかります。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

作成したブロックチェーン メンバーは、次のクイックスタートまたはチュートリアルに使用できます。 必要なくなったら、Azure Blockchain Service で作成した `myResourceGroup` リソース グループを削除することによって、リソースを削除できます。

次のコマンドを実行して、リソース グループとすべての関連リソースを削除します。

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>次の手順

ブロックチェーン メンバーを作成したら、[Geth](connect-geth.md)、[MetaMask](connect-metamask.md)、または [Truffle](connect-truffle.md) 向けの接続のクイックスタートのいずれかを試してください。

> [!div class="nextstepaction"]
> [Truffle を使用して Azure Blockchain Service ネットワークに接続する](connect-truffle.md)
