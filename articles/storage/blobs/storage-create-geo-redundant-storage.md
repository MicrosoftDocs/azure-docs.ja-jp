---
title: チュートリアル - Blob Storage を使用して高可用性アプリケーションを作成する
titleSuffix: Azure Storage
description: 読み取りアクセス geo ゾーン冗長 (RA-GZRS) ストレージを使用してアプリケーション データに高い可用性を確保します。
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 02/18/2021
ms.author: tamram
ms.reviewer: artek
ms.custom: mvc, devx-track-python, devx-track-js, devx-track-csharp
ms.subservice: blobs
ms.openlocfilehash: 0d597f0742cfc43f1c7fb38568b2a2bbda352beb
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102049340"
---
# <a name="tutorial-build-a-highly-available-application-with-blob-storage"></a>チュートリアル:Blob Storage を使用して高可用性アプリケーションを作成する

このチュートリアルは、シリーズの第 1 部です。 ここでは、Azure でアプリケーション データを高可用にする方法について学習します。

このチュートリアルを完了すると、BLOB を[読み取りアクセス geo ゾーン冗長](../common/storage-redundancy.md) (RA-GZRS) ストレージ アカウントにアップロードし、取得するコンソール アプリケーションが作成されます。

Azure Storage の geo 冗長では、プライマリ リージョンから何百キロも離れたセカンダリ リージョンにトランザクションが非同期にレプリケートされます。 このレプリケーション プロセスにより、セカンダリ リージョンのデータの結果整合性が保証されます。 コンソール アプリケーションでは、[サーキット ブレーカー](/azure/architecture/patterns/circuit-breaker) パターンを使用して、接続先のエンドポイントを判断し、障害と復旧がシミュレートされたときに自動的にエンドポイント間を切り替えます。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

シリーズの第 1 部で学習する内容は次のとおりです。

> [!div class="checklist"]
> * ストレージ アカウントの作成
> * 接続文字列の設定
> * コンソール アプリケーションを実行する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

現在、Microsoft では、Azure Storage クライアント ライブラリのバージョン 12.x を反映したコード スニペットの作成に取り組んでいます。 詳細については、「[Azure Storage v12 クライアント ライブラリの発表](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)」をご覧ください。

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

* **Azure 開発** ワークロードと共に [Visual Studio 2019](https://www.visualstudio.com/downloads/) をインストールします。

  ![Azure 開発 ([Web & Cloud]\(Web とクラウド\) 以下)](media/storage-create-geo-redundant-storage/workloads.png)

# <a name="python-v12"></a>[Python v12](#tab/python)

現在、Microsoft では、Azure Storage クライアント ライブラリのバージョン 12.x を反映したコード スニペットの作成に取り組んでいます。 詳細については、「[Azure Storage v12 クライアント ライブラリの発表](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)」をご覧ください。

# <a name="python-v21"></a>[Python v2.1](#tab/python2)

* [Python](https://www.python.org/downloads/) のインストール
* [Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python) をダウンロードしてインストールします。

# <a name="nodejs-v12"></a>[Node.js v12](#tab/nodejs)

現在、Microsoft では、Azure Storage クライアント ライブラリのバージョン 12.x を反映したコード スニペットの作成に取り組んでいます。 詳細については、「[Azure Storage v12 クライアント ライブラリの発表](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)」をご覧ください。

# <a name="nodejs-v11"></a>[Node.js v11](#tab/nodejs11)

* [Node.js](https://nodejs.org) をインストールします。

---

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com/) にサインインします。

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

ストレージ アカウントは、Azure Storage データ オブジェクトを格納してアクセスするための一意の名前空間を提供します。

次の手順で、読み取りアクセス geo ゾーン冗長 (RA-GZRS) ストレージ アカウントを作成します。

1. Azure portal で、 **[リソースの作成]** ボタンを選択します。
2. **[新規]** ページの **[ストレージ アカウント - Blob、File、Table、Queue]** を選択します。
4. 下図のように、ストレージ アカウント フォームに次の情報を入力し、 **[作成]** を選択します。

   | 設定       | 値の例 | 説明 |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **サブスクリプション** | *My subscription* | サブスクリプションの詳細については、[サブスクリプション](https://account.azure.com/Subscriptions)に関するページを参照してください。 |
   | **ResourceGroup** | *myResourceGroup* | 有効なリソース グループ名については、[名前付け規則と制限](/azure/architecture/best-practices/resource-naming)に関するページを参照してください。 |
   | **名前** | *mystorageaccount* | ストレージ アカウントの一意の名前。 |
   | **場所** | *米国東部* | 場所を選択します。 |
   | **パフォーマンス** | *Standard* | この例のシナリオには Standard パフォーマンスが適しています。 |
   | **アカウントの種類** | *StorageV2* | 汎用 v2 ストレージ アカウントの使用をお勧めします。 Azure Storage アカウントの種類について詳しくは、「[ストレージ アカウントの概要](../common/storage-account-overview.md)」を参照してください。 |
   | **レプリケーション**| *読み取りアクセス geo ゾーン冗長ストレージ (RA-GZRS)* | プライマリ リージョンはゾーン冗長で、セカンダリ リージョンにレプリケートされます。セカンダリ リージョンへの読み取りアクセスが有効となります。 |
   | **アクセス層**| *ホット* | アクセスされる頻度の高いデータにはホット層を使用します。 |

    ![ストレージ アカウントの作成](media/storage-create-geo-redundant-storage/createragrsstracct.png)

## <a name="download-the-sample"></a>サンプルのダウンロード

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

現在、Microsoft では、Azure Storage クライアント ライブラリのバージョン 12.x を反映したコード スニペットの作成に取り組んでいます。 詳細については、「[Azure Storage v12 クライアント ライブラリの発表](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)」をご覧ください。

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

[サンプル プロジェクトをダウンロード](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip)し、storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip ファイルを抽出 (解凍) します。 [git](https://git-scm.com/) を使って、アプリケーションのコピーを開発環境にダウンロードすることもできます。 サンプル プロジェクトにはコンソール アプリケーションが含まれています。

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="python-v12"></a>[Python v12](#tab/python)

現在、Microsoft では、Azure Storage クライアント ライブラリのバージョン 12.x を反映したコード スニペットの作成に取り組んでいます。 詳細については、「[Azure Storage v12 クライアント ライブラリの発表](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)」をご覧ください。

# <a name="python-v21"></a>[Python v2.1](#tab/python2)

[サンプル プロジェクトをダウンロード](https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip)し、storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.zip ファイルを抽出 (解凍) します。 [git](https://git-scm.com/) を使って、アプリケーションのコピーを開発環境にダウンロードすることもできます。 サンプル プロジェクトには基本的な Python アプリケーションが含まれています。

```bash
git clone https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="nodejs-v12"></a>[Node.js v12](#tab/nodejs)

現在、Microsoft では、Azure Storage クライアント ライブラリのバージョン 12.x を反映したコード スニペットの作成に取り組んでいます。 詳細については、「[Azure Storage v12 クライアント ライブラリの発表](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)」をご覧ください。

# <a name="nodejs-v11"></a>[Node.js v11](#tab/nodejs11)

[サンプル プロジェクトをダウンロード](https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs)して、ファイルを解凍します。 [git](https://git-scm.com/) を使って、アプリケーションのコピーを開発環境にダウンロードすることもできます。 サンプル プロジェクトには基本的な Node.js アプリケーションが含まれています。

```bash
git clone https://github.com/Azure-Samples/storage-node-v10-ha-ra-grs
```

---

## <a name="configure-the-sample"></a>サンプルの構成

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

現在、Microsoft では、Azure Storage クライアント ライブラリのバージョン 12.x を反映したコード スニペットの作成に取り組んでいます。 詳細については、「[Azure Storage v12 クライアント ライブラリの発表](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)」をご覧ください。

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

アプリケーションでは、ストレージ アカウントの接続文字列を指定する必要があります。 アプリケーションが実行されているローカル コンピューターの環境変数内に、この接続文字列を格納することができます。 環境変数を作成するオペレーティング システムに応じて、以下の例のいずれかに従います。

Azure Portal のストレージ アカウントに移動します。 ストレージ アカウントの **[設定]** の下にある **[アクセス キー]** を選択します。 プライマリ キーまたはセカンダリ キーの **接続文字列** をコピーします。 オペレーティング システムに応じて次のいずれかのコマンドを実行します。\<yourconnectionstring\> は実際の接続文字列に置き換えてください。 このコマンドで、環境変数をローカル コンピューターに保存します。 Windows では、環境変数は、使用している **コマンド プロンプト** またはシェルを再度読み込むまで使用できません。

### <a name="linux"></a>Linux

```
export storageconnectionstring=<yourconnectionstring>
```

### <a name="windows"></a>Windows

```powershell
setx storageconnectionstring "<yourconnectionstring>"
```

# <a name="python-v12"></a>[Python v12](#tab/python)

現在、Microsoft では、Azure Storage クライアント ライブラリのバージョン 12.x を反映したコード スニペットの作成に取り組んでいます。 詳細については、「[Azure Storage v12 クライアント ライブラリの発表](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)」をご覧ください。

# <a name="python-v21"></a>[Python v2.1](#tab/python2)

アプリケーションには、実際のストレージ アカウントの資格情報を指定する必要があります。 この情報は、アプリケーションが実行されているローカル コンピューターの環境変数に格納することができます。 環境変数を作成するオペレーティング システムに応じて、以下のいずれかの例に従います。

Azure Portal のストレージ アカウントに移動します。 ストレージ アカウントの **[設定]** の下にある **[アクセス キー]** を選択します。 **[ストレージ アカウント名]** と **[キー]** の値を、それぞれ以下のコマンドの \<youraccountname\> と \<youraccountkey\> の部分に貼り付けます。 このコマンドで、環境変数をローカル コンピューターに保存します。 Windows では、環境変数は、使用している **コマンド プロンプト** またはシェルを再度読み込むまで使用できません。

### <a name="linux"></a>Linux

```
export accountname=<youraccountname>
export accountkey=<youraccountkey>
```

### <a name="windows"></a>Windows

```powershell
setx accountname "<youraccountname>"
setx accountkey "<youraccountkey>"
```

# <a name="nodejs-v12"></a>[Node.js v12](#tab/nodejs)

現在、Microsoft では、Azure Storage クライアント ライブラリのバージョン 12.x を反映したコード スニペットの作成に取り組んでいます。 詳細については、「[Azure Storage v12 クライアント ライブラリの発表](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)」をご覧ください。

# <a name="nodejs-v11"></a>[Node.js v11](#tab/nodejs11)

このサンプルを実行するには、実際のストレージ アカウントの資格情報を `.env.example` ファイルに追加し、その名前を `.env` に変更する必要があります。

```
AZURE_STORAGE_ACCOUNT_NAME=<replace with your storage account name>
AZURE_STORAGE_ACCOUNT_ACCESS_KEY=<replace with your storage account access key>
```

この情報は、Azure portal で目的のストレージ アカウントに移動し、 **[設定]** セクションの **[アクセス キー]** を選択すると確認できます。

必要な依存関係をインストールします。 そのためには、コマンド プロンプトを開いてサンプル フォルダーに移動し、「`npm install`」と入力します。

---

## <a name="run-the-console-application"></a>コンソール アプリケーションを実行する

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

現在、Microsoft では、Azure Storage クライアント ライブラリのバージョン 12.x を反映したコード スニペットの作成に取り組んでいます。 詳細については、「[Azure Storage v12 クライアント ライブラリの発表](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)」をご覧ください。

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Visual Studio で **F5** キーを押すか **[スタート]** を選択してアプリケーションのデバッグを開始します。 構成に応じて、不足している NuGet パッケージが Visual Studio で自動的に復元されます。詳細については、[パッケージの復元によるパッケージのインストールと再インストール](/nuget/consume-packages/package-restore#package-restore-overview)に関するセクションを参照してください。

コンソール ウィンドウが起動し、アプリケーションの実行が開始されます。 アプリケーションは、ソリューションの **HelloWorld.png** イメージをストレージ アカウントにアップロードします。 アプリケーションは、イメージがセカンダリ RA-GZRS エンドポイントにレプリケートされたことを確認します。 次に、イメージのダウンロードを開始し、最大 999 回試行します。 各読み取りは **P** または **S** で表されます。この **P** はプライマリ エンドポイント、**S** はセカンダリ エンドポイントを示します。

![コンソール アプリの実行](media/storage-create-geo-redundant-storage/figure3.png)

このサンプル コードで、`Program.cs` ファイルの `RunCircuitBreakerAsync` タスクは、[DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync) メソッドを使用してストレージ アカウントからイメージをダウンロードするために使用されます。 そのダウンロードの前に、[OperationContext](/dotnet/api/microsoft.azure.cosmos.table.operationcontext) が定義されています。 操作コンテキストにイベント ハンドラーが定義され、ダウンロードが正常に完了したとき、またはダウンロードが失敗して再試行する場合に呼び出されます。

# <a name="python-v12"></a>[Python v12](#tab/python)

現在、Microsoft では、Azure Storage クライアント ライブラリのバージョン 12.x を反映したコード スニペットの作成に取り組んでいます。 詳細については、「[Azure Storage v12 クライアント ライブラリの発表](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)」をご覧ください。

# <a name="python-v21"></a>[Python v2.1](#tab/python2)

ターミナルまたはコマンド プロンプトでアプリケーションを実行するには、**circuitbreaker.py** ディレクトリに移動して「`python circuitbreaker.py`」と入力します。 アプリケーションは、ソリューションの **HelloWorld.png** イメージをストレージ アカウントにアップロードします。 アプリケーションは、イメージがセカンダリ RA-GZRS エンドポイントにレプリケートされたことを確認します。 次に、イメージのダウンロードを開始し、最大 999 回試行します。 各読み取りは **P** または **S** で表されます。この **P** はプライマリ エンドポイント、**S** はセカンダリ エンドポイントを示します。

![コンソール アプリの実行](media/storage-create-geo-redundant-storage/figure3.png)

このサンプル コードで、`circuitbreaker.py` ファイルの `run_circuit_breaker` メソッドは、[get_blob_to_path](/python/api/azure-storage-blob/azure.storage.blob.baseblobservice.baseblobservice#get-blob-to-path-container-name--blob-name--file-path--open-mode--wb---snapshot-none--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--lease-id-none--if-modified-since-none--if-unmodified-since-none--if-match-none--if-none-match-none--timeout-none-) メソッドを使用してストレージ アカウントからイメージをダウンロードするために使用されます。

ストレージ オブジェクトの再試行関数は、線形的な再試行ポリシーに設定されています。 この再試行関数によって、要求を再試行するかどうかが判断され、要求が再試行されるまでの秒数が指定されます。 プライマリへの初回要求が失敗したときにセカンダリに対して要求を再試行する場合は、**retry\_to\_secondary** の値を true に設定してください。 サンプル アプリケーションのカスタム再試行ポリシーは、ストレージ オブジェクトの `retry_callback` 関数で定義されます。

ダウンロードの前に、サービス オブジェクトの [retry_callback](/python/api/azure-storage-common/azure.storage.common.storageclient.storageclient) と [response_callback](/python/api/azure-storage-common/azure.storage.common.storageclient.storageclient) 関数が定義されています。 これらの関数に定義されているイベント ハンドラーが、ダウンロードが正常に完了したとき、またはダウンロードが失敗して再試行する場合に呼び出されます。

# <a name="nodejs-v12"></a>[Node.js v12](#tab/nodejs)

現在、Microsoft では、Azure Storage クライアント ライブラリのバージョン 12.x を反映したコード スニペットの作成に取り組んでいます。 詳細については、「[Azure Storage v12 クライアント ライブラリの発表](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)」をご覧ください。

# <a name="nodejs-v11"></a>[Node.js v11](#tab/nodejs11)

サンプルを実行するには、コマンド プロンプトを開いてサンプル フォルダーに移動し、「`node index.js`」と入力します。

BLOB ストレージ アカウントにコンテナーが作成されて、そこに **HelloWorld.png** がアップロードされ、コンテナーとイメージがセカンダリ リージョンにレプリケートされたかどうかが繰り返しチェックされます。 レプリケーション後、「**D**」(ダウンロードする場合) または「**Q**」(終了する場合) と入力して Enter キーを押すように求められます。 出力は次の例のようになります。

```
Created container successfully: newcontainer1550799840726
Uploaded blob: HelloWorld.png
Checking to see if container and blob have replicated to secondary region.
[0] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[1] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
...
[31] Container has not replicated to secondary region yet: newcontainer1550799840726 : ContainerNotFound
[32] Container found, but blob has not replicated to secondary region yet.
...
[67] Container found, but blob has not replicated to secondary region yet.
[68] Blob has replicated to secondary region.
Ready for blob download. Enter (D) to download or (Q) to quit, followed by ENTER.
> D
Attempting to download blob...
Blob downloaded from primary endpoint.
> Q
Exiting...
Deleted container newcontainer1550799840726
```

---

## <a name="understand-the-sample-code"></a>サンプル コードを理解する

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

現在、Microsoft では、Azure Storage クライアント ライブラリのバージョン 12.x を反映したコード スニペットの作成に取り組んでいます。 詳細については、「[Azure Storage v12 クライアント ライブラリの発表](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)」をご覧ください。

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

### <a name="retry-event-handler"></a>イベント ハンドラーを再試行する

`OperationContextRetrying` イベント ハンドラーは、イメージのダウンロードが失敗し、再試行するように設定されたときに呼び出されます。 アプリケーションに定義されている最大試行回数に達すると、要求の [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode) は `SecondaryOnly` に変わります。 この設定で、アプリケーションはセカンダリ エンドポイントからイメージをダウンロードを試行するように強制されます。 この構成では、プライマリ エンドポイントは永続的に再試行されないので、イメージの要求にかかる時間が短縮されます。

```csharp
private static void OperationContextRetrying(object sender, RequestEventArgs e)
{
    retryCount++;
    Console.WriteLine("Retrying event because of failure reading the primary. RetryCount = " + retryCount);

    // Check if we have had more than n retries in which case switch to secondary.
    if (retryCount >= retryThreshold)
    {

        // Check to see if we can fail over to secondary.
        if (blobClient.DefaultRequestOptions.LocationMode != LocationMode.SecondaryOnly)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.SecondaryOnly;
            retryCount = 0;
        }
        else
        {
            throw new ApplicationException("Both primary and secondary are unreachable. Check your application's network connection. ");
        }
    }
}
```

### <a name="request-completed-event-handler"></a>要求が完了したイベント ハンドラー

`OperationContextRequestCompleted` イベント ハンドラーは、イメージのダウンロードが成功したときに呼び出されます。 アプリケーションがセカンダリ エンドポイントを使用している場合、アプリケーションはセカンダリ エンドポイントを最大 20 回継続して使用します。 20 回の後、アプリケーションは [LocationMode](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.locationmode) を `PrimaryThenSecondary` に戻し、プライマリ エンドポイントを再試行します。 要求が成功した場合、アプリケーションはプライマリ エンドポイントからの読み取りを継続します。

```csharp
private static void OperationContextRequestCompleted(object sender, RequestEventArgs e)
{
    if (blobClient.DefaultRequestOptions.LocationMode == LocationMode.SecondaryOnly)
    {
        // You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        //    then switch back to the primary and see if it's available now.
        secondaryReadCount++;
        if (secondaryReadCount >= secondaryThreshold)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.PrimaryThenSecondary;
            secondaryReadCount = 0;
        }
    }
}
```

# <a name="python-v12"></a>[Python v12](#tab/python)

現在、Microsoft では、Azure Storage クライアント ライブラリのバージョン 12.x を反映したコード スニペットの作成に取り組んでいます。 詳細については、「[Azure Storage v12 クライアント ライブラリの発表](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)」をご覧ください。

# <a name="python-v21"></a>[Python v2.1](#tab/python2)

### <a name="retry-event-handler"></a>イベント ハンドラーを再試行する

`retry_callback` イベント ハンドラーは、イメージのダウンロードが失敗し、再試行するように設定されたときに呼び出されます。 アプリケーションに定義されている最大試行回数に達すると、要求の [LocationMode](/python/api/azure-storage-common/azure.storage.common.models.locationmode) は `SECONDARY` に変わります。 この設定で、アプリケーションはセカンダリ エンドポイントからイメージをダウンロードを試行するように強制されます。 この構成では、プライマリ エンドポイントは永続的に再試行されないので、イメージの要求にかかる時間が短縮されます。

```python
def retry_callback(retry_context):
    global retry_count
    retry_count = retry_context.count
    sys.stdout.write(
        "\nRetrying event because of failure reading the primary. RetryCount= {0}".format(retry_count))
    sys.stdout.flush()

    # Check if we have more than n-retries in which case switch to secondary
    if retry_count >= retry_threshold:

        # Check to see if we can fail over to secondary.
        if blob_client.location_mode != LocationMode.SECONDARY:
            blob_client.location_mode = LocationMode.SECONDARY
            retry_count = 0
        else:
            raise Exception("Both primary and secondary are unreachable. "
                            "Check your application's network connection.")
```

### <a name="request-completed-event-handler"></a>要求が完了したイベント ハンドラー

`response_callback` イベント ハンドラーは、イメージのダウンロードが成功したときに呼び出されます。 アプリケーションがセカンダリ エンドポイントを使用している場合、アプリケーションはセカンダリ エンドポイントを最大 20 回継続して使用します。 20 回の後、アプリケーションは [LocationMode](/python/api/azure-storage-common/azure.storage.common.models.locationmode) を `PRIMARY` に戻し、プライマリ エンドポイントを再試行します。 要求が成功した場合、アプリケーションはプライマリ エンドポイントからの読み取りを継続します。

```python
def response_callback(response):
    global secondary_read_count
    if blob_client.location_mode == LocationMode.SECONDARY:

        # You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        # then switch back to the primary and see if it is available now.
        secondary_read_count += 1
        if secondary_read_count >= secondary_threshold:
            blob_client.location_mode = LocationMode.PRIMARY
            secondary_read_count = 0
```

# <a name="nodejs-v12"></a>[Node.js v12](#tab/nodejs)

現在、Microsoft では、Azure Storage クライアント ライブラリのバージョン 12.x を反映したコード スニペットの作成に取り組んでいます。 詳細については、「[Azure Storage v12 クライアント ライブラリの発表](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394)」をご覧ください。

# <a name="nodejs-v11"></a>[Node.js v11](#tab/nodejs11)

Node.js V10 SDK では、コールバック ハンドラーは不要です。 代わりにこのサンプルでは、再試行オプションとセカンダリ エンドポイントを含むパイプラインを作成しています。 これによりアプリケーションは、プライマリ パイプラインで必要なデータに到達できなかった場合でも、自動的にセカンダリ パイプラインに切り替えることができます。

```javascript
const accountName = process.env.AZURE_STORAGE_ACCOUNT_NAME;
const storageAccessKey = process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY;
const sharedKeyCredential = new SharedKeyCredential(accountName, storageAccessKey);

const primaryAccountURL = `https://${accountName}.blob.core.windows.net`;
const secondaryAccountURL = `https://${accountName}-secondary.blob.core.windows.net`;

const pipeline = StorageURL.newPipeline(sharedKeyCredential, {
  retryOptions: {
    maxTries: 3,
    tryTimeoutInMs: 10000,
    retryDelayInMs: 500,
    maxRetryDelayInMs: 1000,
    secondaryHost: secondaryAccountURL
  }
});
```

---

## <a name="next-steps"></a>次のステップ

シリーズの第 1 部では、RA-GZRS ストレージ アカウントでアプリケーションを高可用にする方法について学習しました。

シリーズの第 2 部に進んで、エラーをシミュレートし、アプリケーションがセカンダリ RA-GZRS エンドポイントを使用するように強制する方法について学んでください。

> [!div class="nextstepaction"]
> [プライマリ リージョンからの読み取りで発生するエラーをシミュレートする](simulate-primary-region-failure.md)