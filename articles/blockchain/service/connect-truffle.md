---
title: Truffle を使用して接続する
description: Truffle を使用して Azure Blockchain Service ネットワークに接続する
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 08/29/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: ea64e3fe4789ac0558463ded81c8179db83469d5
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70932588"
---
# <a name="quickstart-use-truffle-to-connect-to-a-transaction-node"></a>クイック スタート:Truffle を使用してトランザクション ノードに接続する

Truffle は、Azure Blockchain Service トランザクション ノードへの接続に使用できるブロックチェーン開発環境です。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

* 「[Quickstart: Azure portal を使用したブロックチェーン メンバーの作成](create-member.md)に関するページと、「[クイック スタート: Azure CLI を使用して Azure Blockchain Service ブロックチェーン メンバーを作成する](create-member-cli.md)」を完了していること
* [Truffle](https://github.com/trufflesuite/truffle) をインストールする。 Truffle では、[Node.js](https://nodejs.org)、[Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) など、いくつかのツールがインストールされている必要があります。
* [Python 2.7.15](https://www.python.org/downloads/release/python-2715/) をインストールする。 Python は Web3 に必要です。

## <a name="create-truffle-project"></a>Truffle プロジェクトを作成する

1. Node.js コマンド プロンプトまたはシェルを開きます。
1. ディレクトリを Truffle プロジェクト ディレクトリを作成する場所に変更します。
1. プロジェクト用のディレクトリを作成し、パスを新しいディレクトリに変更します。 たとえば、次のように入力します。

    ``` bash
    mkdir truffledemo
    cd truffledemo
    ```

1. Truffle プロジェクトを初期化します。

    ``` bash
    truffle init
    ```

1. プロジェクト フォルダーに Ethereum JavaScript API web3 をインストールします。 現在、バージョン web3 バージョン 1.0.0-beta.37 が必要です。

    ``` bash
    npm install web3@1.0.0-beta.37
    ```

    インストール中に npm の警告が表示されることがあります。
    
## <a name="configure-truffle-project"></a>Truffle プロジェクトを構成する

Truffle プロジェクトを構成するには、Azure portal からいくつかのトランザクション ノード情報が必要です。

### <a name="transaction-node-endpoint-addresses"></a>トランザクション ノードのエンドポイント アドレス

1. Azure portal で既定のトランザクション ノードに移動し、 **[Transaction nodes]\(トランザクション ノード\) > [接続文字列]** の順に選択します。
1. **HTTPS (アクセスキー 1)** からエンドポイント URL をコピーして保存します。 エンドポイント アドレスは、チュートリアルの後半でスマート コントラクトの構成ファイルに必要になります。

    ![トランザクションのエンドポイント アドレス](./media/connect-truffle/endpoint.png)

### <a name="edit-configuration-file"></a>構成ファイルを編集する

次に、トランザクション ノードのエンドポイントを使用して Truffle 構成ファイルを更新する必要があります。

1. **truffledemo** プロジェクト フォルダー内の Truffle 構成ファイル `truffle-config.js` をエディターで開きます。
1. ファイルの内容を、次の構成情報に置き換えます。 エンドポイント アドレスを含む変数を追加します。 山かっこの部分は、前のセクションで収集した値に置き換えます。

    ``` javascript
    var defaultnode = "<default transaction node connection string>";   
    var Web3 = require("web3");
    
    module.exports = {
      networks: {
        defaultnode: {
          provider: new Web3.providers.HttpProvider(defaultnode),
          network_id: "*"
        }
      }
    }
    ```

1. `truffle-config.js` に対する変更を保存します。

## <a name="connect-to-transaction-node"></a>トランザクション ノードに接続する

*Web3* を使用してトランザクション ノードに接続します。

1. Truffle コンソールを使用して、既定のトランザクション ノードに接続します。 コマンド プロンプトまたはシェルで、次のコマンドを実行します。

    ``` bash
    truffle console --network defaultnode
    ```

    Truffle によって既定のトランザクション ノードに接続され、対話型コンソールが提供されます。

    **web3** オブジェクトでメソッドを呼び出して、トランザクション ノードと対話することができます。

1. **getBlockNumber** メソッドを呼び出して現在のブロック番号を返します。

    ```bash
    web3.eth.getBlockNumber();
    ```

    出力例:

    ```bash
    truffle(defaultnode)> web3.eth.getBlockNumber();
    18567
    ```
1. Truffle コンソールを終了します。

    ```bash
    .exit
    ```

## <a name="next-steps"></a>次の手順

このクイックスタートでは、Azure Blockchain Service の既定のトランザクション ノードに接続する Truffle プロジェクトを作成しました。

次のチュートリアルで、Ethereum および Truffle 用の Azure Blockchain Development Kit を使用して、トランザクションを介したスマート コントラクト関数の作成、ビルド、デプロイ、実行を試してみます。

> [!div class="nextstepaction"]
> [Visual Studio Code を使用してスマート コントラクトを作成、ビルド、デプロイする](send-transaction.md)
