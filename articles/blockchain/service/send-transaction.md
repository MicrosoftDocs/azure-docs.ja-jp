---
title: Visual Studio Code と Azure Blockchain Service を使用してスマート コントラクトを作成、ビルド、デプロイする
description: Visual Studio Code の Ethereum 用 Azure Blockchain 開発キット拡張機能を使用して Azure Blockchain Service にスマート コントラクトを作成、ビルド、デプロイするチュートリアル
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 09/10/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: chrisseg
ms.openlocfilehash: 96fe4d77efdd1fda309d7da021bcc208edd2dfe9
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934984"
---
# <a name="tutorial-usevisual-studio-code-to-create-buildanddeploysmartcontracts"></a>チュートリアル:Visual Studio Code を使用してスマート コントラクトを作成、ビルド、デプロイする

このチュートリアルでは、Visual Studio Code の Ethereum 用 Azure Blockchain 開発キット拡張機能を使用して、Azure Blockchain Service にスマート コントラクトを作成、ビルド、デプロイします。 また、Truffle を使用し、トランザクションを介してスマート コントラクト関数を実行します。

Azure Blockchain Development Kit for Ethereum を使用して、以下のことを行います。

> [!div class="checklist"]
> * スマート コントラクトを作成する
> * スマート コントラクトをデプロイする
> * トランザクションを介してスマート コントラクト関数を実行する
> * コントラクトの状態を照会する

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

* 「[Quickstart: Visual Studio Code を使用して Azure Blockchain Service コンソーシアム ネットワークに接続する](connect-vscode.md)」が完了していること

## <a name="create-a-smart-contract"></a>スマート コントラクトを作成する

Azure Blockchain Development Kit for Ethereum では、プロジェクト テンプレートと Truffle ツールを使用して、コントラクトのスキャフォールディング、ビルド、およびデプロイを支援します。 前提条件である「[クイックスタート:Visual Studio Code を使用して Azure Blockchain Service コンソーシアム ネットワークに接続する](connect-vscode.md)」を事前に済ませておいてください。 クイックスタートには、Ethereum 用 Azure Blockchain 開発キットのインストールと構成の手順が紹介されています。

1. VS Code コマンド パレットで、 **[Azure Blockchain: New Solidity Project]\(Azure Blockchain: 新しい Solidity プロジェクト\)** を選択します。
1. **[Create basic project]\(基本プロジェクトの作成\)** を選択します。
1. `HelloBlockchain` という名前の新しいフォルダーを作成し、**新しいプロジェクトのパスを選択**します。

Azure Blockchain Development Kit によって、新しい Solidity プロジェクトが作成され初期化されます。 基本プロジェクトには、サンプルの **HelloBlockchain** スマート コントラクトと、ビルドして Azure Blockchain Service のコンソーシアム メンバーにデプロイするために必要なすべてのファイルが含まれています。 プロジェクトが作成されるまでに数分かかる場合があります。 Azure Blockchain の出力を選択すると、VS Code のターミナル パネルで進行状況を監視できます。

プロジェクト構造は、次の例のようになります。

   ![Solidity プロジェクト](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>スマート コントラクトをビルドする

スマート コントラクトは、プロジェクトの **contracts** ディレクトリに配置されます。 スマート コントラクトのコンパイルは、ブロックチェーンにデプロイする前に行います。 プロジェクト内のすべてのスマート コントラクトをコンパイルするには、 **[Build Contracts]\(コントラクトのビルド\)** コマンドを使用します。

1. VS Code エクスプローラーのサイドバーで、プロジェクトの **[contracts]** フォルダーを展開します。
1. **HelloBlockchain.sol** を右クリックし、メニューの **[Build Contracts]\(コントラクトのビルド\)** を選択します。

    ![コントラクトをビルドする](./media/send-transaction/build-contracts.png)

Azure Blockchain Development Kit では、Truffle を使用してスマート コントラクトがコンパイルされます。

![コンパイルの出力](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>スマート コントラクトをデプロイする

Truffle では、移行スクリプトを使用して、コントラクトが Ethereum ネットワークにデプロイされます。 移行は、プロジェクトの **migrations** ディレクトリに配置されている JavaScript ファイルです。

1. スマート コントラクトをデプロイするには、**HelloBlockchain.sol** を右クリックし、メニューの **[Deploy Contracts]\(コントラクトのデプロイ\)** を選択します。
1. コマンド パレットで Azure Blockchain コンソーシアム ネットワークを選択します。 プロジェクトを作成したときに、コンソーシアムのブロックチェーン ネットワークがプロジェクトの Truffle 構成ファイルに追加されました。
1. **[Generate mnemonic]\(ニーモニックの生成\)** を選択します。 ファイル名を選択し、ニーモニック ファイルをプロジェクト フォルダーに保存します。 たとえば、「 `myblockchainmember.env` 」のように入力します。 ニーモニック ファイルは、ブロックチェーン メンバーの Ethereum 秘密キーを生成するために使用されます。

Azure Blockchain Development Kit では、Truffle を使用して移行スクリプトが実行され、コントラクトがブロックチェーンにデプロイされます。

![正常にデプロイされたコントラクト](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function"></a>コントラクト関数を呼び出す

**HelloBlockchain** コントラクトの **SendRequest** 関数は、**RequestMessage** 状態変数を変更するものです。 ブロックチェーン ネットワークの状態の変更は、トランザクションを介して行われます。 トランザクションを介して **SendRequest** 関数を実行するスクリプトを作成できます。

1. Truffle プロジェクトのルートに新しいファイルを作成し、`sendrequest.js` という名前を付けます。 そのファイルに次の Web3 JavaScript コードを追加します。

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
        
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling SendRequest function for contract ", instance.address);
        return instance.SendRequest("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. Azure Blockchain Development Kit によってプロジェクトが作成されると、Truffle 構成ファイルがコンソーシアムのブロックチェーン ネットワーク エンドポイントの詳細と共に生成されます。 プロジェクトの **truffle-config.js** を開きます。 構成ファイル内の一覧には、2 つのネットワークが含まれています。一方は development という名前で、もう一方はコンソーシアムと同じ名前です。
1. VS Code のターミナル ウィンドウで Truffle を使用して、コンソーシアムのブロックチェーン ネットワーク上でスクリプトを実行します。 ターミナル ウィンドウのメニュー バーで **[ターミナル]** タブを選択し、ドロップダウンで **[PowerShell]** を選択します。

    ```PowerShell
    truffle exec sendrequest.js --network <blockchain network>
    ```

    \<blockchain network\> を、**truffle-config.js** 内で定義されているブロックチェーン ネットワークの名前に置き換えます。

Truffle によってブロックチェーン ネットワーク上でスクリプトが実行されます。

![スクリプト出力](./media/send-transaction/execute-transaction.png)

トランザクションを介してコントラクトの関数を実行した場合、そのトランザクションはブロックが作成されるまで処理されません。 トランザクションを介して実行される関数は、戻り値ではなくトランザクション ID を返します。

## <a name="query-contract-state"></a>コントラクトの状態を照会する

スマート コントラクト関数は、状態変数の現在の値を返すことができます。 状態変数の値を返す関数を追加してみましょう。

1. **HelloBlockchain.sol** で、**getMessage** 関数を **HelloBlockchain** スマート コントラクトに追加します。

    ``` solidity
    function getMessage() public view returns (string memory)
    {
        if (State == StateType.Request)
            return RequestMessage;
        else
            return ResponseMessage;
    }
    ```

    この関数は、コントラクトの現在の状態を基に、状態変数に格納されているメッセージを返します。

1. **HelloBlockchain.sol** を右クリックし、メニューの **[Build Contracts]\(コントラクトのビルド\)** を選択して、スマート コントラクトへの変更をコンパイルします。
1. デプロイするには、**HelloBlockchain.sol** を右クリックし、メニューの **[Deploy Contracts]\(コントラクトのデプロイ\)** を選択します。 確認を求められたら、コマンド パレットで Azure Blockchain コンソーシアム ネットワークを選択します。
1. 次に、**getMessage** 関数を呼び出すスクリプトを作成します。 Truffle プロジェクトのルートに新しいファイルを作成し、`getmessage.js` という名前を付けます。 そのファイルに次の Web3 JavaScript コードを追加します。

    ```javascript
    var HelloBlockchain = artifacts.require("HelloBlockchain");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the HelloBlockchain smart contract")
      HelloBlockchain.deployed().then(function(instance) {
        console.log("Calling getMessage function for contract ", instance.address);
        return instance.getMessage();
      }).then(function(result) {
        console.log("Request message value: ", result);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. VS Code のターミナル ウィンドウで Truffle を使用して、ブロックチェーン ネットワーク上でスクリプトを実行します。 ターミナル ウィンドウのメニュー バーで **[ターミナル]** タブを選択し、ドロップダウンで **[PowerShell]** を選択します。

    ```bash
    truffle exec getmessage.js --network <blockchain network>
    ```

    \<blockchain network\> を、**truffle-config.js** 内で定義されているブロックチェーン ネットワークの名前に置き換えます。

このスクリプトは、getMessage 関数を呼び出すことによって、スマート コントラクトを照会します。 **RequestMessage** 状態変数の現在の値が返されます。

![スクリプト出力](./media/send-transaction/execute-get.png)

値が "**Hello, blockchain!** " ではないことに注意してください。 返される値は、プレースホルダーです。 コントラクトを変更してデプロイすると、コントラクトは新しいコントラクト アドレスを取得し、状態変数にはスマート コントラクト コンストラクターの値が割り当てられます。 Truffle サンプルの **2_deploy_contracts.js** 移行スクリプトは、スマート コントラクトをデプロイし、引数としてプレースホルダー値を渡します。 コンストラクターは、**RequestMessage** 状態変数をプレースホルダーの値に設定し、この値が返されます。

1. **RequestMessage** 状態変数を設定して値を照会するには、**sendrequest.js** スクリプトと **getmessage.js** スクリプトをもう一度実行します。

    ![スクリプト出力](./media/send-transaction/execute-set-get.png)

    **sendrequest.js** によって **RequestMessage** 状態変数が "**Hello, blockchain!** " に設定されます。 また、**getmessage.js** によってコントラクトに対して **RequestMessage** 状態変数の値が照会され、"**Hello, blockchain!** " が返されます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要なくなったら、前提条件の "*ブロックチェーン メンバーの作成*" に関するクイックスタートで作成した `myResourceGroup` リソース グループを削除することで、リソースを削除できます。

リソース グループを削除するには:

1. Azure Portal で、左側のナビゲーション ウィンドウの **[リソース グループ]** に移動し、削除するリソース グループを選択します。
1. **[リソース グループの削除]** を選択します。 リソース グループ名を入力して削除を確認し、 **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Blockchain Development Kit を使用してサンプル Solidity プロジェクトを作成しました。 スマート コントラクトをビルドしてデプロイし、Azure Blockchain Service 上でホストされているブロックチェーン コンソーシアム ネットワーク上のトランザクションを介して関数を呼び出しました。

> [!div class="nextstepaction"]
> [Azure Blockchain Service を使用してブロックチェーン アプリケーションを開発する](develop.md)
