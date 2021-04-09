---
title: Azure Blockchain Service メンバーを作成する - Azure CLI
description: Azure CLI を使用してブロックチェーン コンソーシアムの Azure Blockchain Service メンバーを作成します。
ms.date: 07/23/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 3442c3b6023edcde97aabcb13e91120ba6811027
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91323075"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>クイック スタート:Azure CLI を使用して Azure Blockchain Service ブロックチェーン メンバーを作成する

このクイックスタートでは、Azure CLI を使用して Azure Blockchain Service 内にブロックチェーンの新しいメンバーとコンソーシアムをデプロイします。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

[なし] :

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/bash](https://shell.azure.com/bash) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、Enter キーを押して実行します。

CLI をローカルにインストールして使用する場合、このクイックスタートでは、Azure CLI バージョン 2.0.51 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

## <a name="prepare-your-environment"></a>環境を準備する

1. サインインします。

    CLI のローカル インストールを使用する場合は、[az login](/cli/azure/reference-index#az-login) コマンドを使用してサインインします。

    ```azurecli
    az login
    ```

    ターミナルに表示される手順に従って、認証プロセスを完了します。

1. Azure CLI 拡張機能をインストールします。

    Azure CLI の拡張機能の参照を操作する場合は、最初に拡張機能をインストールする必要があります。  Azure CLI 拡張機能を使用すると、コア CLI の一部としてまだ出荷されていない実験用コマンドおよびプレリリース コマンドにアクセスできます。  更新とアンインストールを含む拡張機能の詳細については、「[Azure CLI で拡張機能を使用する](/cli/azure/azure-cli-extensions-overview)」を参照してください。

    次のコマンドを実行して、[Azure Blockchain Service の拡張機能](/cli/azure/ext/blockchain/blockchain)をインストールします。

    ```azurecli-interactive
    az extension add --name blockchain
    ```

1. リソース グループを作成します。

    Azure Blockchain Service は、すべての Azure リソースと同様に、リソース グループにデプロイする必要があります。 リソース グループを使用すると、関連する Azure リソースを整理して管理できます。

    このクイックスタートでは、次の [az group create](/cli/azure/group#az-group-create) コマンドを使用して、_myResourceGroup_ という名前のリソース グループを _eastus_ の場所に作成します。

    ```azurecli-interactive
    az group create \
                     --name "myResourceGroup" \
                     --location "eastus"
    ```

## <a name="create-a-blockchain-member"></a>ブロックチェーン メンバーを作成する

Azure Blockchain Service メンバーは、プライベート コンソーシアム ブロックチェーン ネットワーク内のブロックチェーン ノードです。 メンバーをプロビジョニングするときは、コンソーシアム ネットワークを作成するか、またはコンソーシアム ネットワークに参加することができます。 コンソーシアム ネットワークには少なくとも 1 つのメンバーが必要です。 参加者が必要とするブロックチェーン メンバーの数は、シナリオによって異なります。 コンソーシアムの参加者は、1 つまたは複数のブロックチェーン メンバーを有するか、または他の参加者との間でメンバーを共有することができます。 コンソーシアムの詳細については、「[Azure Blockchain Service のコンソーシアム](consortium.md)」を参照してください。

渡す必要があるいくつかのパラメーターとプロパティがあります。 パラメーターの例は、実際の値に置き換えてください。

```azurecli-interactive
az blockchain member create \
                            --resource-group "MyResourceGroup" \
                            --name "myblockchainmember" \
                            --location "eastus" \
                            --password "strongMemberAccountPassword@1" \
                            --protocol "Quorum" \
                            --consortium "myconsortium" \
                            --consortium-management-account-password "strongConsortiumManagementPassword@1" \
                            --sku "Basic"
```

| パラメーター | 説明 |
|---------|-------------|
| **resource-group** | Azure Blockchain Service リソースが作成されるリソース グループ名。 前のセクションで作成したリソース グループを使用します
| **name** | Azure Blockchain Service のブロックチェーン メンバーを識別する一意の名前。 名前はパブリック エンドポイント アドレスに使用されます。 たとえば、「 `myblockchainmember.blockchain.azure.com` 」のように入力します。
| **location** | ブロックチェーン メンバーが作成される Azure リージョン。 たとえば、「 `westus2` 」のように入力します。 ユーザーや他の Azure アプリケーションに最も近い場所を選択します。 一部のリージョンでは、機能が利用できない場合があります。 Azure Blockchain Data Manager は、次の Azure リージョンで利用できます: 米国東部と西ヨーロッパ) で運営される基本的な Web サイトの 2 つのインスタンス。
| **password** | メンバーの既定のトランザクション ノードのパスワード。 このパスワードは、ブロックチェーン メンバーの既定のトランザクション ノード パブリック エンドポイントに接続する際の基本認証に使用します。
| **protocol** | ブロックチェーンのプロトコル。 現時点では、*Quorum* プロトコルがサポートされています。
| **consortium** | 参加または作成するコンソーシアムの名前。 コンソーシアムの詳細については、「[Azure Blockchain Service のコンソーシアム](consortium.md)」を参照してください。
| **consortium-management-account-password** | コンソーシアム アカウントのパスワードは、メンバー アカウントのパスワードとも呼ばれます。 メンバー アカウントのパスワードは、メンバー用に作成される Ethereum アカウントの秘密キーの暗号化に使用されます。 メンバー アカウントとメンバー アカウントのパスワードをコンソーシアムの管理に使用します。
| **sku** | レベルの種類。 *Standard* または *Basic*。 開発、テスト、概念実証には、*Basic* レベルを使用します。 運用グレードのデプロイには、*Standard* レベルを使用します。 また、Blockchain Data Manager を使用している場合や大量のプライベート トランザクションを送信する場合にも、*Standard* レベルを使用します。 メンバーの作成後に価格レベルを Basic と Standard の間で変更することはできません。

ブロックチェーン メンバーとサポート リソースの作成には約 10 分かかります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

作成したブロックチェーン メンバーは、次のクイックスタートまたはチュートリアルに使用できます。 必要なくなったら、このクイックスタートのために作成した `myResourceGroup` リソース グループを削除することによって、リソースを削除できます。

次のコマンドを実行して、リソース グループとすべての関連リソースを削除します。

```azurecli-interactive
az group delete \
                 --name myResourceGroup \
                 --yes
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure Blockchain Service のメンバーと新しいコンソーシアムをデプロイしました。 次は、Azure Blockchain Development Kit for Ethereum を使用して Azure Blockchain Service のメンバーに接続するクイックスタートに挑戦してみてください。

> [!div class="nextstepaction"]
> [Visual Studio Code を使用して Azure Blockchain Service に接続する](connect-vscode.md)
