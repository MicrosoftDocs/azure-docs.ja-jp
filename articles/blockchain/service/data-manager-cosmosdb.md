---
title: Blockchain Data Manager を使用して Azure Cosmos DB を更新する - Azure Blockchain Service
description: Azure Blockchain Service の Blockchain Data Manager を使用して、Azure Cosmos DB にブロックチェーン データを送信します
ms.date: 12/04/2019
ms.topic: tutorial
ms.reviewer: chroyal
ms.openlocfilehash: 79c39d9883b5ba618e368b0ff6d3e95f1af5bd96
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977398"
---
# <a name="tutorial-use-blockchain-data-manager-to-send-data-to-azure-cosmos-db"></a>チュートリアル:Blockchain Data Manager を使用して Azure Cosmos DB にデータを送信する

このチュートリアルでは、Azure Blockchain Service 用の Blockchain Data Manager を使用して、Azure Cosmos DB にブロックチェーン トランザクション データを記録します。 Blockchain Data Manager を使うと、ブロックチェーン台帳データをキャプチャし、変換して、Azure Event Grid トピックに配信できます。 Azure Event Grid からは、Azure Logic App コネクタを使用して、Azure Cosmos DB データベースにドキュメントを作成します。 チュートリアルを完了すると、Azure Cosmos DB Data Explorer でブロックチェーン トランザクション データを探索できます。

[![Blockchain トランザクションの詳細](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

このチュートリアルでは、次のことを行いました。

> [!div class="checklist"]
> * Blockchain Data Manager インスタンスを作成する
> * トランザクションのプロパティとイベントをデコードするブロックチェーン アプリケーションを追加する
> * トランザクション データを保存する Azure Cosmos DB アカウントとデータベースを作成する
> * Azure Event Grid トピックを Azure Cosmos DB に接続する Azure ロジック アプリを作成する
> * ブロックチェーン台帳にトランザクションを送信する
> * Azure Cosmos DB でデコードされたトランザクション データを表示する

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

* 「[Quickstart: Azure portal を使用したブロックチェーン メンバーの作成](create-member.md)に関するページと、「[クイック スタート: Azure CLI を使用して Azure Blockchain Service ブロックチェーン メンバーを作成する](create-member-cli.md)」を完了していること
* 「[Quickstart: Visual Studio Code を使用して Azure Blockchain Service コンソーシアム ネットワークに接続する](connect-vscode.md)」を完了します。 このクイックスタートでは、[Azure Blockchain Development Kit for Ethereum](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain) のインストール方法とブロックチェーン開発環境の設定方法について説明しています。
* 「[チュートリアル: Visual Studio Code を使用してスマート コントラクトを作成、ビルド、デプロイする](send-transaction.md)」を完了します。 このチュートリアルでは、サンプルのスマート コントラクトを作成する手順について説明します。
* [Event Grid トピック](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)を作成する
* [Azure Event Grid のイベント ハンドラー](../../event-grid/event-handlers.md)について学習する

## <a name="create-instance"></a>インスタンスを作成する

Blockchain Data Manager インスタンスは、Azure Blockchain Service トランザクション ノードに接続して監視します。 インスタンスは、トランザクション ノードから生のブロックと生のトランザクション データをすべてキャプチャします。 送信接続により、ブロックチェーン データを Azure Event Grid に送信します。 インスタンスを作成するときに、1 つの送信接続を構成します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 前提条件で作成した Azure Blockchain Service メンバーに移動します (「[クイックスタート:Azure portal を使用してブロックチェーン メンバーを作成する](create-member.md)」)。 **[Blockchain Data Manager]** を選択します。
1. **[追加]** を選択します。

    ![Blockchain Data Manager を追加する](./media/data-manager-cosmosdb/add-instance.png)

    次の詳細を入力します。

    Setting | 例 | 説明
    --------|---------|------------
    名前 | mywatcher | 接続されている Blockchain Data Manager の一意の名前を入力します。
    トランザクション ノード | myblockchainmember | 前提条件で作成した Azure Blockchain Service メンバーの既定のトランザクション ノードを選択します。
    接続名 | cosmosdb | ブロックチェーン トランザクション データが送信される送信接続の一意の名前を入力します。
    イベント グリッドのエンドポイント | myTopic | 前提条件で作成した Event Grid トピックを選択します。 注:Blockchain Data Manager インスタンスと Event Grid トピックは同じサブスクリプション内に存在する必要があります。

1. **[OK]** を選択します。

    Blockchain Data Manager インスタンスの作成には 1 分もかかりません。 インスタンスは、デプロイされると、自動的に開始されます。 実行中の Blockchain Data Manager インスタンスにより、トランザクション ノードからブロックチェーン イベントがキャプチャされ、イベント グリッドにデータが送信されます。

## <a name="add-application"></a>[アプリケーションの追加]

**helloblockchain** ブロックチェーン アプリケーションを追加して、Blockchain Data Manager によってイベントとプロパティの状態がデコードされるようにします。 アプリケーションを追加するには、Blockchain Data Manager にスマート コントラクト ABI とバイトコード ファイルが必要です。

### <a name="get-contract-abi-and-bytecode"></a>コントラクト ABI とバイトコードを取得する

コントラクト ABI では、スマート コントラクトのインターフェイスを定義します。 これは、スマート コントラクトを操作する方法を示しています。 [Azure Blockchain Development Kit for Ethereum 拡張機能](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)を使用して、コントラクト ABI をクリップボードにコピーできます。

1. Visual Studio Code のエクスプローラー ウィンドウで、前提条件で作成した **helloblockchain** Solidity プロジェクトの **build/contracts** フォルダーを 展開します (「[チュートリアル:Visual Studio Code を使用してスマート コントラクトを作成、ビルド、デプロイする](send-transaction.md)」)。
1. コントラクト メタデータの JSON ファイルを右クリックします。 ファイル名は、スマート コントラクト名に **.json** 拡張子を付けたものです。
1. **[Copy Contract ABI]\(コントラクト ABI のコピー\)** を選択します。

    ![[Copy Contract ABI]\(コントラクト ABI のコピー\) が選択されている Visual Studio Code のウィンドウ](./media/data-manager-cosmosdb/abi-devkit.png)

    コントラクト ABI がクリップボードにコピーされます。

1. **abi** 配列を JSON ファイルとして保存します。 たとえば、*abi.json* です。 このファイルは、後の手順で使用します。

Blockchain Data Manager には、スマート コントラクトのデプロイ済みバイトコードが必要です。 デプロイ済みバイトコードは、スマート コントラクトのバイトコードとは異なります。 Azure Blockchain 開発キット拡張機能を使用してバイトコードをクリップボードにコピーします。

1. Visual Studio Code のエクスプローラー ウィンドウで、自分の Solidity プロジェクトの **build/contracts** フォルダーを展開します。
1. コントラクト メタデータの JSON ファイルを右クリックします。 ファイル名は、スマート コントラクト名に **.json** 拡張子を付けたものです。
1. **[Copy Transaction Bytecode]\(トランザクションのバイトコードのコピー\)** を選択します。

    ![[Copy Transaction Bytecode]\(トランザクションのバイトコードのコピー\) が選択されている Visual Studio Code のペイン](./media/data-manager-cosmosdb/bytecode-devkit.png)

    バイトコードがクリップボードにコピーされます。

1. **バイトコード**値を JSON ファイルとして保存します。 たとえば、*bytecode.json* です。 このファイルは、後の手順で使用します。

次の例は、VS Code エディターで開いている *abi.json* ファイルと *bytecode.json* ファイルを示しています。 ファイルは次のようになります。

![abi.json ファイルと bytecode.json ファイルの例](./media/data-manager-cosmosdb/contract-files.png)

### <a name="create-contract-abi-and-bytecode-url"></a>コントラクト ABI とバイトコード URL の作成

Blockchain Data Manager では、アプリケーションの追加時に、コントラクト ABI ファイルとバイトコードファイルに URL からアクセスできる必要があります。 Azure Storage アカウントを使用して、プライベートにアクセスできる URL を提供できます。

#### <a name="create-storage-account"></a>ストレージ アカウントの作成

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

#### <a name="upload-contract-files"></a>コントラクト ファイルのアップロード

1. ストレージ アカウントに新しいコンテナーを作成します。 **[コンテナー] > [コンテナー]** を選択します。

    ![ストレージ アカウント コンテナーの作成](./media/data-manager-cosmosdb/create-container.png)

    | Setting | 説明 |
    |---------|-------------|
    | 名前  | コンテナーに名前を付けます。 たとえば、*smartcontract* です。 |
    | パブリック アクセス レベル | *[Private (no anonymous access)]\(プライベート (匿名アクセスなし)\)* を選択します |

1. **[OK]** を選択してコンテナーを作成します。
1. コンテナーを選択し、 **[アップロード]** を選択します。
1. 「[コントラクトの ABI とバイトコードの取得](#get-contract-abi-and-bytecode)」セクションで作成した両方の JSON ファイルを選択します。

    ![BLOB のアップロード](./media/data-manager-cosmosdb/upload-blobs.png)

    **[アップロード]** を選択します。

#### <a name="generate-url"></a>URL の生成

各 BLOB に対して、共有アクセス署名を生成します。

1. ABI JSON BLOB を選択します。
1. **[SAS の生成]** を選択します
1. 必要なアクセス署名の有効期限を設定し、 **[BLOB SAS トークンおよび URL を生成]** を選択します。

    ![SAS トークンの生成](./media/data-manager-cosmosdb/generate-sas.png)

1. **[BLOB SAS URL]** をコピーし、次のセクションのために保存します。
1. バイトコード JSON BLOB に対して 「[URL の生成](#generate-url)」の手順を繰り返します。

### <a name="add-helloblockchain-application-to-instance"></a>インスタンスに helloblockchain アプリケーションを追加する

1. インスタンスの一覧から、Blockchain Data Manager インスタンスを選択します。
1. **ブロックチェーン アプリケーション**を選択します。
1. **[追加]** を選択します。

    ![ブロックチェーン アプリケーションを追加する](./media/data-manager-cosmosdb/add-application.png)

    ブロックチェーン アプリケーションの名前と、スマート コントラクト ABI およびバイトコードの URL を入力します。

    Setting | 説明
    --------|------------
    名前 | 追跡するブロックチェーン アプリケーションの一意の名前を入力します。
    コントラクト ABI | コントラクト ABI ファイルへの URL パス。 詳細については、「[コントラクト ABI とバイトコード URL の作成](#create-contract-abi-and-bytecode-url)」を参照してください。
    コントラクトのバイトコード | バイトコード ファイルへの URL パス。 詳細については、「[コントラクト ABI とバイトコード URL の作成](#create-contract-abi-and-bytecode-url)」を参照してください。

1. **[OK]** を選択します。

    アプリケーションが作成されると、ブロックチェーン アプリケーションの一覧にそのアプリケーションが表示されます。

    ![ブロックチェーン アプリケーションの一覧](./media/data-manager-cosmosdb/artifact-list.png)

Azure Storage アカウントを削除したり、それを使用してさらに多くのブロックチェーン アプリケーションを構成したりできます。 Azure Storage アカウントを削除する場合は、そのリソース グループを削除できます。 リソース グループを削除すると、関連付けられているストレージ アカウント、およびリソース グループに関連付けられているその他のリソースも削除されます。

## <a name="create-azure-cosmos-db"></a>Azure Cosmos DB の作成

[!INCLUDE [cosmos-db-create-storage-account](../../../includes/cosmos-db-create-dbaccount.md)]

### <a name="add-a-database-and-container"></a>データベースとコンテナーを追加する

Azure portal のデータ エクスプローラーを使用してデータベースとコンテナーを作成できます。

1. ご使用の Azure Cosmos DB アカウント ページの左側のナビゲーションで **[データ エクスプローラー]** を選択し、 **[新しいコンテナー]** を選択します。
1. **[コンテナーの追加]** ウィンドウで、新しいコンテナーの設定を入力します。

    ![コンテナー設定を追加する](./media/data-manager-cosmosdb/add-container.png)

    | Setting | 説明
    |---------|-------------|
    | データベース ID | 新しいデータベースの名前として「**blockchain-data**」を入力します。 |
    | スループット | スループットを **400** 要求ユニット/秒 (RU/秒) のままにします。 待ち時間を短縮する場合、後でスループットをスケールアップできます。|
    | コンテナー ID | 新しいコンテナーの名前として「**Messages**」と入力します。 |
    | パーティション キー | パーティション キーとして **/MessageType** を使用します。 |

1. **[OK]** を選択します。 作成した新しいデータベースとコンテナーがデータ エクスプローラーに表示されます。

## <a name="create-logic-app"></a>ロジック アプリの作成

Azure Logic Apps は、システムとサービスを統合する必要があるときに、ビジネス プロセスやワークフローをスケジュール設定および自動化するのに役立ちます。 ロジック アプリを使用して、Event Grid を Azure Cosmos DB に接続できます。

1. [Azure portal](https://portal.azure.com) で、 **[リソースの作成]**  >  **[統合]**  >  **[Logic App]** を選択します。
1. ロジック アプリを作成する場所の詳細を指定します。 完了したら、 **[作成]** を選択します。

    ロジック アプリの作成の詳細については、[Azure Logic Apps を使用して自動化されたワークフローを作成する方法](../../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページを参照してください。

1. Azure によってアプリがデプロイされたら、自分のロジック アプリのリソースを選択します。
1. Logic Apps デザイナーの **[テンプレート]** で、 **[空のロジック アプリ]** を選択します。

### <a name="add-event-grid-trigger"></a>イベント グリッド トリガーを追加する

すべてのロジック アプリは必ずトリガーから起動され、トリガーは、特定のイベントが発生するか特定の条件が満たされたときに起動されます。 トリガーが起動するたびに、Logic Apps エンジンによって、ワークフローを開始および実行するロジック アプリ インスタンスが作成されます。 Azure Event Grid トリガーを使用して、ブロックチェーン トランザクション データを Event Grid から Cosmos DB に送信します。

1. Logic Apps デザイナーで、**Azure Event Grid** コネクタを探して選択します。
1. **[トリガー]** タブで、 **[リソース イベントが発生したとき]** を選択します。
1. Event Grid トピックへの API 接続を作成します。

    ![イベント グリッド トリガーの設定](./media/data-manager-cosmosdb/event-grid-trigger.png)

    | Setting | 説明
    |---------|-------------|
    | サブスクリプション | Event Grid トピックが含まれているサブスクリプションを選択します。 |
    | リソースの種類 | **Microsoft.EventGrid.Topics** を選択します。 |
    | リソース名 | Blockchain Data Manager がトランザクション データ メッセージを送信している Event Grid トピックの名前を選択します。 |

### <a name="add-cosmos-db-action"></a>Cosmos DB アクションを追加する

トランザクションごとに Cosmos DB でドキュメントを作成するアクションを追加します。 トランザクション メッセージの種類をパーティション キーとして使用して、メッセージを分類します。

1. **[新しいステップ]** を選択します。
1. **[アクションを選択してください]** で **Azure Cosmos DB** を探します。
1. **[Azure Cosmos DB] > [アクション] > [ドキュメントを作成または更新する]** を選択します。
1. Cosmos DB データベースへの API 接続を作成します。

    ![Cosmos DB 接続の設定](./media/data-manager-cosmosdb/cosmosdb-connection.png)

    | Setting | 説明
    |---------|-------------|
    | 接続名 | Event Grid トピックが含まれているサブスクリプションを選択します。 |
    | DocumentDB アカウント | [Azure Cosmos DB アカウントの作成](#create-azure-cosmos-db)のセクションで作成した DocumentDB アカウントを選択します。 |

1. 以前に「[データベースとコンテナーを追加する](#add-a-database-and-container)」セクションで作成した Azure Cosmos DB の **[データベース ID]** および **[コレクション ID]** を入力します。

1. **[ドキュメント]** 設定を選択します。 *[動的なコンテンツの追加]* ポップアウトで **[式]** を選択し、次の式をコピーして貼り付けます。

    ```
    addProperty(triggerBody()?['data'], 'id', utcNow())
    ```

    この式を使うと、メッセージのデータ部分を取得し、その ID をタイムスタンプ値に設定できます。

1. **[新しいパラメーターの追加]** を選択し、 **[パーティション キーの値]** を選択します。
1. **[パーティション キーの値]** を `"@{triggerBody()['data']['MessageType']}"` に設定します。 値は二重引用符で囲む必要があります。

    ![Cosmos DB 設定が表示された Logic Apps デザイナー](./media/data-manager-cosmosdb/create-action.png)

    この値で、パーティション キーはトランザクション メッセージの種類に設定されます。

1. **[保存]** を選択します。

ロジック アプリを使って、Event Grid トピックを監視します。 Blockchain Data Manager から新しいトランザクション メッセージが送信されると、ロジック アプリにより、Cosmos DB にドキュメントが作成されます。

## <a name="send-a-transaction"></a>トランザクションを開始する

次に、トランザクションをブロックチェーン台帳に送信して、作成した内容をテストします。 前提条件で作成した **sendrequest.js** スクリプトを使用します (「[チュートリアル:Visual Studio Code を使用してスマート コントラクトを作成、ビルド、デプロイする](send-transaction.md)」)。

VS Code のターミナル ウィンドウで Truffle を使用して、コンソーシアムのブロックチェーン ネットワーク上でスクリプトを実行します。 ターミナル ウィンドウのメニュー バーで **[ターミナル]** タブを選択し、ドロップダウンで **[PowerShell]** を選択します。

``` PowerShell
truffle exec sendrequest.js --network <blockchain network>
```

\<blockchain network\> を、**truffle-config.js** 内で定義されているブロックチェーン ネットワークの名前に置き換えます。

![トランザクションを送信する](./media/data-manager-cosmosdb/send-request.png)

## <a name="view-transaction-data"></a>トランザクション データを表示する

Blockchain Data Manager を Azure Cosmos DB に接続したので、Cosmos DB Data Explorer でブロックチェーン トランザクション メッセージを表示できます。

1. Cosmos DB Data Explorer ビューにアクセスします。 たとえば、**cosmosdb-blockchain > データ エクスプローラー > blockchain-data > [メッセージ] > [項目]** です。

    ![Cosmos DB Data Explorer](./media/data-manager-cosmosdb/data-explorer.png)

    Data Explorer には、Cosmos DB データベースで作成されたブロックチェーン データ メッセージが一覧表示されます。

1. 項目 ID を選択してメッセージを参照し、一致するトランザクション ハッシュを持つメッセージを見つけます。

    [![Blockchain トランザクションの詳細](./media/data-manager-cosmosdb/raw-msg.png)](./media/data-manager-cosmosdb/raw-msg.png#lightbox)

    生のトランザクション メッセージには、トランザクションに関する詳細が含まれています。 ただし、プロパティ情報は暗号化されます。

    HelloBlockchain スマート コントラクトを Blockchain Data Manager インスタンスに追加したため、デコードされたプロパティ情報を含む **ContractProperties** メッセージの種類も送信されます。

1. トランザクションの **ContractProperties** メッセージを見つけます。 リスト内の次のメッセージである必要があります。

    [![Blockchain トランザクションの詳細](./media/data-manager-cosmosdb/properties-msg.png)](./media/data-manager-cosmosdb/properties-msg.png#lightbox)

    **DecodedProperties** 配列には、トランザクションのプロパティが含まれています。

お疲れさまでした。 Blockchain Data Manager と Azure Cosmos DB を使用して、トランザクション メッセージ エクスプローラーを正常に作成しました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

不要になったら、このチュートリアルで使用したリソースとリソース グループを削除できます。 リソース グループを削除するには、以下の手順を実行します。

1. Azure Portal で、左側のナビゲーション ウィンドウの **[リソース グループ]** に移動し、削除するリソース グループを選択します。
1. **[リソース グループの削除]** を選択します。 リソース グループ名を入力して削除を確認し、 **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

ブロックチェーン台帳との統合の詳細を確認します。

> [!div class="nextstepaction"]
> [Azure Logic Apps で Ethereum ブロックチェーン コネクタを使用する](ethereum-logic-app.md)
