---
title: Azure portal を使用してブロックチェーン データ マネージャーを構成する - Azure Blockchain Service
description: Azure portal を使用して、Azure Blockchain Service 用のブロックチェーン データ マネージャーを作成および管理します。
ms.date: 11/04/2019
ms.topic: article
ms.reviewer: chroyal
ms.openlocfilehash: 03c22a7a23f1579a846746f21ce048b3425399c3
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977023"
---
# <a name="configure-blockchain-data-manager-using-the-azure-portal"></a>Azure portal を使用してブロックチェーン データ マネージャーを構成する

ブロックチェーン データをキャプチャして Azure Event Grid トピックに送信するように、Azure Blockchain Service のブロックチェーン データ マネージャーを構成します。

ブロックチェーン データ マネージャー インスタンスを構成するには、次のようにします。

* Azure Blockchain Service トランザクション ノードにブロックチェーン データ マネージャー インスタンスを作成する
* ブロックチェーン アプリケーションを追加する

## <a name="prerequisites"></a>前提条件

* 「[Quickstart: Azure portal を使用したブロックチェーン メンバーの作成](create-member.md)に関するページと、「[クイック スタート: Azure CLI を使用して Azure Blockchain Service ブロックチェーン メンバーを作成する](create-member-cli.md)」を完了していること
* [Event Grid トピック](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)を作成する
* [Azure Event Grid のイベント ハンドラー](../../event-grid/event-handlers.md)について学習する

## <a name="create-instance"></a>インスタンスを作成する

ブロックチェーン データ マネージャー インスタンスは、Azure Blockchain Service トランザクション ノードに接続して監視します。 トランザクション ノードにアクセスできるユーザーのみが接続を作成できます。 インスタンスは、トランザクション ノードから生のブロックと生のトランザクション データをすべてキャプチャします。

送信接続により、ブロックチェーン データを Azure Event Grid に送信します。 インスタンスを作成するときに、1 つの送信接続を構成します。 ブロックチェーン データ マネージャーでは、すべてのブロックチェーン データ マネージャー インスタンスに対して複数の Event Grid トピック送信接続がサポートされています。 1 つの宛先にブロックチェーン データを送信することも、複数の宛先にブロックチェーン データを送信することもできます。 別の宛先を追加する場合は、インスタンスへの送信接続を追加するだけです。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. ブロックチェーン データ マネージャーに接続する Azure Blockchain Service メンバーに移動します。 **[Blockchain Data Manager]\(ブロックチェーン データ マネージャー\)** を選択します。
1. **[追加]** を選択します。

    ![Blockchain Data Manager を追加する](./media/data-manager-portal/add-instance.png)

    次の詳細を入力します。

    Setting | 説明
    --------|------------
    名前 | 接続されているブロックチェーン データ マネージャーの一意の名前を入力します。 ブロックチェーン データ マネージャー名には小文字と数字を使用でき、最大文字数は 20 文字です。
    トランザクション ノード | トランザクション ノードを選択します。 自分が読み取りアクセスを持っているトランザクション ノードだけが一覧表示されます。
    接続名 | ブロックチェーン トランザクション データが送信される送信接続の一意の名前を入力します。
    イベント グリッドのエンドポイント | ブロックチェーン データ マネージャー インスタンスと同じサブスクリプション内のイベント グリッド トピックを選択します。

1. **[OK]** を選択します。

    Blockchain Data Manager インスタンスの作成には 1 分もかかりません。 インスタンスは、デプロイされると、自動的に開始されます。 実行中のブロックチェーン データ マネージャー インスタンスは、トランザクション ノードからブロックチェーン イベントをキャプチャし、送信接続にデータを送信します。

    新しいインスタンスは、Azure Blockchain Service メンバーのブロックチェーン データ マネージャー インスタンスの一覧に表示されます。

    ![ブロックチェーン データ メンバー インスタンスの一覧](./media/data-manager-portal/instance-list.png)

## <a name="add-blockchain-application"></a>ブロックチェーン アプリケーションの追加

ブロックチェーン アプリケーションを追加すると、ブロックチェーン データ マネージャーがアプリケーションのイベントとプロパティの状態をデコードします。 それ以外の場合は、生のブロックと生のトランザクション データのみが送信されます。 ブロックチェーン データ マネージャーは、コントラクトがデプロイされているときにはコントラクト アドレスも検出します。 1 つのブロックチェーン データ マネージャー インスタンスには、複数のブロックチェーン アプリケーションを追加できます。

> [!IMPORTANT]
> 現在、Solidity の[配列型](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays)または [mapping 型](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types)を宣言するブロックチェーン アプリケーションは、完全にはサポートされません。 配列型または mapping 型として宣言されたプロパティは、*ContractPropertiesMsg* メッセージや *DecodedContractEventsMsg* メッセージではデコードされません。

アプリケーションを追加するには、ブロックチェーン データ マネージャーにスマート コントラクト ABI とデプロイ済みバイトコード ファイルが必要です。

### <a name="get-contract-abi-and-bytecode"></a>コントラクト ABI とバイトコードの取得

コントラクト ABI では、スマート コントラクトのインターフェイスを定義します。 これは、スマート コントラクトを操作する方法を示しています。 [Azure Blockchain Development Kit for Ethereum 拡張機能](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)を使用して、コントラクト ABI をクリップボードにコピーできます。

1. Visual Studio Code のエクスプローラー ウィンドウで、自分の Solidity プロジェクトの **build/contracts** フォルダーを展開します。
1. コントラクト メタデータの JSON ファイルを右クリックします。 ファイル名は、スマート コントラクト名に **.json** 拡張子を付けたものです。
1. **[Copy Contract ABI]\(コントラクト ABI のコピー\)** を選択します。

    ![[Copy Contract ABI]\(コントラクト ABI のコピー\) が選択されている Visual Studio Code のウィンドウ](./media/data-manager-portal/abi-devkit.png)

    コントラクト ABI がクリップボードにコピーされます。

1. **abi** 配列を JSON ファイルとして保存します。 たとえば、*abi.json* です。 このファイルは、後の手順で使用します。

Blockchain Data Manager には、スマート コントラクトのデプロイ済みバイトコードが必要です。 デプロイ済みバイトコードは、スマート コントラクトのバイトコードとは異なります。 Azure ブロックチェーン開発キット拡張機能を使用して、バイトコードをクリップボードにコピーします。

1. Visual Studio Code のエクスプローラー ウィンドウで、自分の Solidity プロジェクトの **build/contracts** フォルダーを展開します。
1. コントラクト メタデータの JSON ファイルを右クリックします。 ファイル名は、スマート コントラクト名に **.json** 拡張子を付けたものです。
1. **[Copy Transaction Bytecode] (トランザクション バイトコードのコピー)** を選択します。

    ![[Copy Transaction Bytecode] (トランザクション バイトコードのコピー) が選択されている Visual Studio Code のウィンドウ](./media/data-manager-portal/bytecode-devkit.png)

    バイトコードがクリップボードにコピーされます。

1. **バイトコード**値を JSON ファイルとして保存します。 たとえば、*bytecode.json* です。 このファイルは、後の手順で使用します。

次の例は、VS Code エディターで開いている *abi.json* ファイルと *bytecode.json* ファイルを示しています。 ファイルは次のようになります。

![abi.json ファイルと bytecode.json ファイルの例](./media/data-manager-portal/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>コントラクト ABI とバイトコード URL の作成

Blockchain Data Manager では、アプリケーションの追加時に、コントラクト ABI ファイルとバイトコードファイルに URL からアクセスできる必要があります。 Azure Storage アカウントを使用して、プライベートにアクセスできる URL を提供できます。

#### <a name="create-storage-account"></a>ストレージ アカウントの作成

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>コントラクト ファイルのアップロード

1. ストレージ アカウントに新しいコンテナーを作成します。 **[コンテナー] > [コンテナー]** を選択します。

    ![ストレージ アカウント コンテナーの作成](./media/data-manager-portal/create-container.png)

    | フィールド | 説明 |
    |-------|-------------|
    | 名前  | コンテナーに名前を付けます。 たとえば、*smartcontract* です。 |
    | パブリック アクセス レベル | *[Private (no anonymous access)]\(プライベート (匿名アクセスなし)\)* を選択します |

1. **[OK]** を選択してコンテナーを作成します。
1. コンテナーを選択し、 **[アップロード]** を選択します。
1. 「[コントラクトの ABI とバイトコードの取得](#get-contract-abi-and-bytecode)」セクションで作成した両方の JSON ファイルを選択します。

    ![BLOB のアップロード](./media/data-manager-portal/upload-blobs.png)

    **[アップロード]** を選択します。

#### <a name="generate-url"></a>URL の生成

各 BLOB に対して、共有アクセス署名を生成します。

1. ABI JSON BLOB を選択します。
1. **[SAS の生成]** を選択します
1. 必要なアクセス署名の有効期限を設定し、 **[BLOB SAS トークンおよび URL を生成]** を選択します。

    ![SAS トークンの生成](./media/data-manager-portal/generate-sas.png)

1. **[BLOB SAS URL]** をコピーし、次のセクションのために保存します。
1. バイトコード JSON BLOB に対して 「[URL の生成](#generate-url)」の手順を繰り返します。

### <a name="add-application-to-instance"></a>インスタンスへのアプリケーションの追加

1. インスタンスの一覧から、ブロックチェーン データ マネージャー インスタンスを選択します。
1. **ブロックチェーン アプリケーション**を選択します。
1. **[追加]** を選択します。

    ![ブロックチェーン アプリケーションを追加する](./media/data-manager-portal/add-application.png)

    ブロックチェーン アプリケーションの名前と、スマート コントラクト ABI およびバイトコードの URL を入力します。

    Setting | 説明
    --------|------------
    名前 | 追跡するブロックチェーン アプリケーションの一意の名前を入力します。
    コントラクト ABI | コントラクト ABI ファイルへの URL パス。 詳細については、「[コントラクト ABI とバイトコード URL の作成](#create-contract-abi-and-bytecode-url)」を参照してください。
    コントラクトのバイトコード | バイトコード ファイルへの URL パス。 詳細については、「[コントラクト ABI とバイトコード URL の作成](#create-contract-abi-and-bytecode-url)」を参照してください。

1. **[OK]** を選択します。

    アプリケーションが作成されると、ブロックチェーン アプリケーションの一覧にそのアプリケーションが表示されます。

    ![ブロックチェーン アプリケーションの一覧](./media/data-manager-portal/artifact-list.png)

Azure Storage アカウントを削除したり、それを使用してさらに多くのブロックチェーン アプリケーションを構成したりできます。 Azure Storage アカウントを削除する場合は、そのリソース グループを削除できます。 リソース グループを削除すると、関連付けられているストレージ アカウント、およびリソース グループに関連付けられているその他のリソースも削除されます。

## <a name="stop-instance"></a>インスタンスの停止

ブロックチェーン イベントのキャプチャと送信接続へのデータの送信を停止する場合は、ブロックチェーン マネージャー インスタンスを停止します。 インスタンスが停止すると、ブロックチェーン データ マネージャーに対して料金は発生しません。 詳細については、[価格](https://azure.microsoft.com/pricing/details/blockchain-service)に関するページを参照してください。

1. **[概要]** に移動し、 **[停止]** を選択します。

    ![インスタンスの停止](./media/data-manager-portal/stop-instance.png)

## <a name="next-steps"></a>次の手順

次のチュートリアルで、ブロックチェーン データ マネージャーと Azure Cosmos DB を使用してブロックチェーン トランザクション メッセージ エクスプローラーの作成を試みます。

> [!div class="nextstepaction"]
> [Blockchain Data Manager を使用して Azure Cosmos DB にデータを送信する](data-manager-cosmosdb.md)