---
title: Geth を使用して Azure Blockchain Service に接続する
description: Geth を使用して Azure Blockchain Service ネットワークに接続する
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: a26899e291c4f44d3c9d91032b2ee191ba03133a
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70931772"
---
# <a name="quickstart-use-geth-to-connect-to-a-transaction-node"></a>クイック スタート:Geth を使用してトランザクション ノードに接続する

Geth は、Azure Blockchain Service トランザクション ノード上の Geth インスタンスにアタッチするために使用できる Go Ethereum クライアントです。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

* [Geth](https://github.com/ethereum/go-ethereum/wiki/geth) をインストールする
* 「[Quickstart: Azure portal を使用したブロックチェーン メンバーの作成](create-member.md)に関するページと、「[クイック スタート: Azure CLI を使用して Azure Blockchain Service ブロックチェーン メンバーを作成する](create-member-cli.md)」を完了していること

## <a name="get-the-geth-connection-string"></a>Geth の接続文字列を取得する

Geth の接続文字列は、Azure portal で確認できます。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. Azure Blockchain Service メンバーに移動します。 **[Transaction nodes]\(トランザクション ノード\)** と既定のトランザクション ノード リンクを選択します。

    ![既定のトランザクション ノードを選択する](./media/connect-geth/transaction-nodes.png)

1. **[接続文字列]** を選択します。
1. **[HTTPS (Access key 1)]\(HTTPS (アクセス キー 1)\)** の接続文字列をコピーします。 このコマンドは次のセクションで必要になります。

    ![接続文字列](./media/connect-geth/connection-string.png)

## <a name="connect-to-geth"></a>Geth に接続する

1. コマンド プロンプトまたはシェルを開きます。
1. Geth attach サブコマンドを使用して、トランザクション ノード上で実行中の Geth インスタンスにアタッチします。 attach サブコマンドの引数として接続文字列を貼り付けます。 たとえば、次のように入力します。

    ```
    geth attach <connection string>
    ```

1. トランザクション ノードの Ethereum コンソールに接続すると、web3 JavaScript Dapp API または admin API を呼び出すことができます。

    たとえば、chainId を見つけるには次の API を使用します。

    ```bash
    admin.nodeInfo.protocols.istanbul.config.chainId
    ```

    この例では、chainId は 297 です。

    ![Azure Blockchain Service のオプション](./media/connect-geth/geth-attach.png)

1. コンソールから切断するには、「`exit`」と入力します。

## <a name="next-steps"></a>次の手順

このクイックスタートでは、Geth クライアントを使用して Azure Blockchain Service トランザクション ノード上の Geth インスタンスにアタッチしました。 次のチュートリアルで、Ethereum および Truffle 用の Azure Blockchain Development Kit を使用して、トランザクションを介したスマート コントラクト関数の作成、ビルド、デプロイ、実行を試してみます。

> [!div class="nextstepaction"]
> [Visual Studio Code を使用してスマート コントラクトを作成、ビルド、デプロイする](send-transaction.md)