---
title: スマート コントラクトを作成、ビルド、デプロイするチュートリアル - Azure Blockchain Service
description: Visual Studio Code の Ethereum 用 Azure Blockchain 開発キット拡張機能を使用して Azure Blockchain Service にスマート コントラクトを作成、ビルド、デプロイするチュートリアル
ms.date: 12/06/2019
ms.topic: tutorial
ms.reviewer: chrisseg
ms.openlocfilehash: 5b901ab904425a22d2fe9643ffa75a4e978efa88
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74972870"
---
# <a name="tutorial-create-buildanddeploysmartcontracts-on-azure-blockchain-service"></a>チュートリアル:スマート コントラクトの作成、ビルド、Azure Blockchain Service へのデプロイ

このチュートリアルでは、Visual Studio Code の Ethereum 用 Azure Blockchain 開発キット拡張機能を使用して、Azure Blockchain Service にスマート コントラクトを作成、ビルド、デプロイします。 また、開発キットを使用し、トランザクションを介してスマート コントラクト関数を実行します。

Azure Blockchain Development Kit for Ethereum を使用して、以下のことを行います。

> [!div class="checklist"]
> * スマート コントラクトを作成する
> * スマート コントラクトをデプロイする
> * トランザクションを介してスマート コントラクト関数を実行する

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

* 「[Quickstart: Visual Studio Code を使用して Azure Blockchain Service コンソーシアム ネットワークに接続する](connect-vscode.md)」が完了していること
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Azure Blockchain Development Kit for Ethereum 拡張機能](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js 10.15.x 以降](https://nodejs.org/download)
* [Git 2.10.x 以降](https://git-scm.com)
* [Python 2.7.15](https://www.python.org/downloads/release/python-2715/)。python.exe をパスに追加します。 Azure Blockchain 開発キットでは、パス内に Python バージョン 2.7.15 が必要です。
* [Truffle 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI 6.0.0](https://github.com/trufflesuite/ganache-cli)

Windows では、node-gyp モジュール用にインストール済みの C++ コンパイラが必要です。 MSBuild Tools を使用できます。

* Visual Studio 2017 がインストールされている場合は、`npm config set msvs_version 2017 -g` コマンドを使用して、MSBuild Tools を使用するように npm を構成します
* Visual Studio 2019 がインストールされている場合は、npm に対して MSBuild Tools のパスを設定します。 たとえば、`npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"` のように指定します。
* それ以外の場合は、"*管理者として実行された*" 管理者特権を持つコマンド シェルで `npm install --global windows-build-tools` を使用して、スタンドアロンの VS ビルド ツールをインストールします。

node-gyp の詳細については、[GitHub の node-gyp のリポジトリ](https://github.com/node-gyp)を参照してください。

## <a name="create-a-smart-contract"></a>スマート コントラクトを作成する

Azure Blockchain Development Kit for Ethereum では、プロジェクト テンプレートと Truffle ツールを使用して、コントラクトのスキャフォールディング、ビルド、およびデプロイを支援します。 開始する前に、前提条件の「[クイックスタート: Visual Studio Code を使用して Azure Blockchain Service コンソーシアム ネットワークに接続する](connect-vscode.md)」を完了してください。 クイックスタートには、Ethereum 用 Azure Blockchain 開発キットのインストールと構成の手順が紹介されています。

1. VS Code コマンド パレットで、**[Azure Blockchain: New Solidity Project]\(Azure Blockchain: 新しい Solidity プロジェクト\)** を選択します。
1. **[Create basic project]\(基本プロジェクトの作成\)** を選択します。
1. `HelloBlockchain` という名前の新しいフォルダーを作成し、**新しいプロジェクトのパスを選択**します。

Azure Blockchain Development Kit によって、新しい Solidity プロジェクトが作成され初期化されます。 基本プロジェクトには、サンプルの **HelloBlockchain** スマート コントラクトと、ビルドして Azure Blockchain Service のコンソーシアム メンバーにデプロイするために必要なすべてのファイルが含まれています。 プロジェクトが作成されるまでに数分かかる場合があります。 Azure Blockchain の出力を選択すると、VS Code のターミナル パネルで進行状況を監視できます。

プロジェクト構造は、次の例のようになります。

   ![Solidity プロジェクト](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>スマート コントラクトをビルドする

スマート コントラクトは、プロジェクトの **contracts** ディレクトリに配置されます。 スマート コントラクトのコンパイルは、ブロックチェーンにデプロイする前に行います。 プロジェクト内のすべてのスマート コントラクトをコンパイルするには、**[Build Contracts]\(コントラクトのビルド\)** コマンドを使用します。

1. VS Code エクスプローラーのサイドバーで、プロジェクトの **[contracts]** フォルダーを展開します。
1. **HelloBlockchain.sol** を右クリックし、メニューの **[Build Contracts]\(コントラクトのビルド\)** を選択します。

    ![[Build Contracts]\(コントラクトのビルド\) を選択する ](./media/send-transaction/build-contracts.png)

Azure Blockchain Development Kit では、Truffle を使用してスマート コントラクトがコンパイルされます。

![Truffle コンパイラの出力](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>スマート コントラクトをデプロイする

Truffle では、移行スクリプトを使用して、コントラクトが Ethereum ネットワークにデプロイされます。 移行は、プロジェクトの **migrations** ディレクトリに配置されている JavaScript ファイルです。

1. スマート コントラクトをデプロイするには、**HelloBlockchain.sol** を右クリックし、メニューの **[Deploy Contracts]\(コントラクトのデプロイ\)** を選択します。
1. コマンド パレットで Azure Blockchain コンソーシアム ネットワークを選択します。 プロジェクトを作成したときに、コンソーシアムのブロックチェーン ネットワークがプロジェクトの Truffle 構成ファイルに追加されました。
1. **[Generate mnemonic]\(ニーモニックの生成\)** を選択します。 ファイル名を選択し、ニーモニック ファイルをプロジェクト フォルダーに保存します。 たとえば、「 `myblockchainmember.env` 」のように入力します。 ニーモニック ファイルは、ブロックチェーン メンバーの Ethereum 秘密キーを生成するために使用されます。

Azure Blockchain Development Kit では、Truffle を使用して移行スクリプトが実行され、コントラクトがブロックチェーンにデプロイされます。

![正常にデプロイされたコントラクト](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function"></a>コントラクト関数を呼び出す

**HelloBlockchain** コントラクトの **SendRequest** 関数は、**RequestMessage** 状態変数を変更するものです。 ブロックチェーン ネットワークの状態の変更は、トランザクションを介して行われます。 Azure Blockchain 開発キットのスマート コントラクト インタラクション ページを使用すると、トランザクションを介して **SendRequest** 関数を呼び出すことができます。

1. スマート コントラクトを対話的に操作するには、**HelloBlockchain.sol** を右クリックし、メニューから **[Show Smart Contract Interaction Page]\(スマート コントラクト インタラクション ページの表示\)** を選択します。

    ![メニューから [Show Smart Contract Interaction Page]\(スマート コントラクト インタラクション ページの表示\) を選択する](./media/send-transaction/contract-interaction.png)

1. インタラクション ページでは、デプロイされている契約のバージョンを選択したり、関数を呼び出したり、最新の状態を表示したり、メタデータを表示したりすることができます。

    ![スマート コントラクト インタラクション ページの例](./media/send-transaction/interaction-page.png)

1. スマート コントラクト関数を呼び出すには、コントラクト アクションを選択して必要な引数を渡します。 **[SendRequest]** コントラクト アクションを選択し、**[requestMessage]** パラメーターに「**Hello, Blockchain!** 」と入力します。 **[実行]** を選択すると、トランザクションを介して **SendRequest** 関数が呼び出されます。

    ![SendRequest アクションを実行する](./media/send-transaction/sendrequest-action.png)

トランザクションが処理されると、インタラクション セクションに状態の変化が反映されます。

![コントラクトの状態が変化する](./media/send-transaction/contract-state.png)

SendRequest 関数によって **RequestMessage** フィールドと **State** フィールドが設定されています。 **RequestMessage** の最新の状態は、引数として渡した **Hello, Blockchain** です。 **[State]\(状態\)** フィールドの値は **[Request]\(要求\)** のままです。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要なくなったら、前提条件の "*ブロックチェーン メンバーの作成*" に関するクイックスタートで作成した `myResourceGroup` リソース グループを削除することで、リソースを削除できます。

リソース グループを削除するには:

1. Azure Portal で、左側のナビゲーション ウィンドウの **[リソース グループ]** に移動し、削除するリソース グループを選択します。
1. **[リソース グループの削除]** を選択します。 リソース グループ名を入力して削除を確認し、**[削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Blockchain Development Kit を使用してサンプル Solidity プロジェクトを作成しました。 スマート コントラクトをビルドしてデプロイし、Azure Blockchain Service 上でホストされているブロックチェーン コンソーシアム ネットワーク上のトランザクションを介して関数を呼び出しました。

> [!div class="nextstepaction"]
> [Azure Blockchain Service を使用してブロックチェーン アプリケーションを開発する](develop.md)
