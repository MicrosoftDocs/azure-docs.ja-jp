---
title: Azure Data Lake Storage のクエリ アクセラレーションを使用してデータをフィルター処理する | Microsoft Docs
description: クエリ アクセラレーションを使用して、ストレージ アカウントからデータのサブセットを取得します。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 01/06/2021
ms.author: normesta
ms.reviewer: jamsbak
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: a925d3f55395d094c7f19f65de4b72fd20a11a41
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102213676"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration"></a>Azure Data Lake Storage のクエリ アクセラレーションを使用してデータをフィルター処理する

この記事では、クエリ アクセラレーションを使用して、ストレージ アカウントからデータのサブセットを取得する方法について説明します。 

クエリ アクセラレーションでは、特定の操作の実行に必要なデータのみを取得することで、アプリケーションと分析フレームワークによって、データ処理を劇的に最適化することができます。 詳細については、「[Azure Data Lake Storage のクエリ アクセラレーション](data-lake-storage-query-acceleration.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

- Azure Storage にアクセスするには、Azure サブスクリプションが必要です。 まだサブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

- **汎用 v2** ストレージ アカウント。 「[ストレージ アカウントを作成する](../common/storage-account-create.md)」を参照してください。

- タブを選択すると、SDK 固有の前提条件が表示されます。

  ### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

  利用不可

  ### <a name="net"></a>[.NET](#tab/dotnet)

  [.NET SDK](https://dotnet.microsoft.com/download) 

  ### <a name="java"></a>[Java](#tab/java)

  - [Java Development Kit (JDK)](/java/azure/jdk/) バージョン 8 以降

  - [Apache Maven](https://maven.apache.org/download.cgi) 

    > [!NOTE] 
    > この記事では、Apache Maven を使用して Java プロジェクトを作成済みであることを前提としています。 Apache Maven を使用してプロジェクトを作成する方法の例については、「[設定](storage-quickstart-blobs-java.md#setting-up)」を参照してください。
  
  ### <a name="python"></a>[Python](#tab/python)

  [Python](https://www.python.org/downloads/) 3.8 以上。

  ### <a name="nodejs"></a>[Node.js](#tab/nodejs)

  Node.js SDK を使用するために必要な追加の前提条件はありません。

---

## <a name="enable-query-acceleration"></a>クエリ アクセラレーションを有効にする

クエリ アクセラレーションを使用するには、クエリ アクセラレーション機能をサブスクリプションに登録する必要があります。 この機能が登録されていることを確認したら、Azure Storage リソース プロバイダーを登録する必要があります。 

### <a name="step-1-register-the-query-acceleration-feature"></a>手順 1:クエリ アクセラレーション機能を登録する

クエリ アクセラレーションを使用するには、最初にクエリ アクセラレーション機能をサブスクリプションに登録する必要があります。 

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Windows PowerShell コマンド ウィンドウを開きます。

1. `Connect-AzAccount` コマンドを使用して Azure サブスクリプションにサインインし、画面上の指示に従います。

   ```powershell
   Connect-AzAccount
   ```

2. 自分の ID が複数のサブスクリプションに関連付けられている場合は、アクティブなサブスクリプションを設定します。

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   `<subscription-id>` プレースホルダーの値をサブスクリプションの ID に置き換えます。

3. [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) コマンドを使用して、クエリ アクセラレーション機能を登録します。

   ```powershell
   Register-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
   ```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. [Azure Cloud Shell](../../cloud-shell/overview.md) を開きます。または、Azure CLI をローカルに[インストール](/cli/azure/install-azure-cli)した場合は、Windows PowerShell などのコマンド コンソール アプリケーションを開きます。

2. 自分の ID が複数のサブスクリプションに関連付けられている場合は、アクティブなサブスクリプションをストレージ アカウントのサブスクリプションに設定します。

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   `<subscription-id>` プレースホルダーの値をサブスクリプションの ID に置き換えます。

3. [az feature register](/cli/azure/feature#az-feature-register) コマンドを使用して、クエリ アクセラレーション機能を登録します。

   ```azurecli
   az feature register --namespace Microsoft.Storage --name BlobQuery
   ```

---

### <a name="step-2-verify-that-the-feature-is-registered"></a>手順 2:機能が登録されたことを確認する

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

登録が完了したことを確認するには、[Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) コマンドを使用します。

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

登録が完了したことを確認するには、[az feature](/cli/azure/feature#az-feature-show) コマンドを使用します。

```azurecli
az feature show --namespace Microsoft.Storage --name BlobQuery
```

---

### <a name="step-3-register-the-azure-storage-resource-provider"></a>手順 3:Azure Storage リソース プロバイダーを登録する

登録が承認されたら、Azure Storage リソース プロバイダーを再登録する必要があります。 

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

リソース プロバイダーを登録するには、[AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) コマンドを使用します。

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

リソース プロバイダーを登録するには、[az provider register](/cli/azure/provider#az-provider-register) コマンドを使用します。

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

---

## <a name="set-up-your-environment"></a>環境の設定方法

### <a name="step-1-install-packages"></a>手順 1:パッケージをインストールする 

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az モジュール バージョン 4.6.0 以降をインストールします。

```powershell
Install-Module -Name Az -Repository PSGallery -Force
```

古いバージョンの Az から更新するには、次のコマンドを実行します。

```powershell
Update-Module -Name Az
```

#### <a name="net"></a>[.NET](#tab/dotnet)

1. コマンド プロンプトを開き、ディレクトリ (`cd`) をプロジェクト フォルダーに変更します。次に例を示します。

   ```console
   cd myProject
   ```

2. `dotnet add package` コマンドを使用して、.NET パッケージ用 Azure Blob ストレージ クライアント ライブラリの `12.5.0-preview.6` バージョン以降をインストールします。 

   ```console
   dotnet add package Azure.Storage.Blobs -v 12.8.0
   ```

3. この記事に示されている例では、[CsvHelper](https://www.nuget.org/packages/CsvHelper/) ライブラリを使用して CSV ファイルが解析されています。 そのライブラリを使用するには、次のコマンドを使用します。

   ```console
   dotnet add package CsvHelper
   ```

#### <a name="java"></a>[Java](#tab/java)

1. テキスト エディターでプロジェクトの *pom.xml* ファイルを開きます。 依存関係のグループに、次の dependency 要素を追加します。 

   ```xml
   <!-- Request static dependencies from Maven -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-core</artifactId>
       <version>1.6.0</version>
   </dependency>
    <dependency>
        <groupId>org.apache.commons</groupId>
        <artifactId>commons-csv</artifactId>
        <version>1.8</version>
    </dependency>    
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-storage-blob</artifactId>
      <version>12.8.0-beta.1</version>
    </dependency>
   ```

#### <a name="python"></a>[Python](#tab/python)

Python 向けの Azure Data Lake Storage クライアント ライブラリを、[pip](https://pypi.org/project/pip/) を使用してインストールします。

```
pip install azure-storage-blob==12.4.0
```

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

ターミナル ウィンドウを開いてから次のコマンドを入力して、JavaScript 用の Data Lake クライアント ライブラリをインストールします。

```javascript
    npm install @azure/storage-blob
    npm install @fast-csv/parse
```

---

### <a name="step-2-add-statements"></a>手順 2:ステートメントを追加する

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

利用不可

#### <a name="net"></a>[.NET](#tab/dotnet)

次の `using` ステートメントをコード ファイルの先頭に追加します。

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
```

クエリ アクセラレーションでは、CSV および JSON 形式のデータが取得されます。 そのため、使用する CSV または JSON 解析ライブラリに using ステートメントを必ず追加してください。 この記事に記載されている例では、NuGet で利用できる [CsvHelper](https://www.nuget.org/packages/CsvHelper/) ライブラリを使用して CSV ファイルを解析します。 そのため、以下の `using` ステートメントをコード ファイルの先頭に追加します。

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

この記事に記載されている例をコンパイルするには、以下の `using` ステートメントも追加する必要があります。

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
```

#### <a name="java"></a>[Java](#tab/java)

次の `import` ステートメントをコード ファイルの先頭に追加します。

```java
import com.azure.storage.blob.*;
import com.azure.storage.blob.options.*;
import com.azure.storage.blob.models.*;
import com.azure.storage.common.*;
import java.io.*;
import java.util.function.Consumer;
import org.apache.commons.csv.*;
```

#### <a name="python"></a>[Python](#tab/python)

次の import ステートメントを、コード ファイルの先頭に追加します。

```python
import sys, csv
from azure.storage.blob import BlobServiceClient, ContainerClient, BlobClient, DelimitedTextDialect, BlobQueryError
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

このステートメントをコード ファイルの先頭に配置して、`storage-blob` モジュールを追加します。 

```javascript
const { BlobServiceClient } = require("@azure/storage-blob");
```

クエリ アクセラレーションでは、CSV および JSON 形式のデータが取得されます。 そのため、使用する CSV または JSON 解析モジュールにステートメントを必ず追加してください。 この記事に示されている例では、[fast-csv](https://www.npmjs.com/package/fast-csv) モジュールを使用して CSV ファイルが解析されています。 そのため、このステートメントをコード ファイルの先頭に追加しました。

```javascript
const csv = require('@fast-csv/parse');
```

---

## <a name="retrieve-data-by-using-a-filter"></a>フィルターを使用してデータを取得する

SQL を使用して、クエリ アクセラレーション要求で行フィルター述語と列のプロジェクションを指定できます。 次のコードでは、ストレージ内の CSV ファイルに対してクエリを行い、3番目の列が `Hemingway, Ernest` 値と一致するすべてのデータの行を返します。 

- SQL クエリでは、クエリ対象のファイルを示すためにキーワード `BlobStorage` が使用されます。

- 列参照は `_N` として指定され、最初の列は `_1` です。 ソース ファイルにヘッダー行が含まれている場合は、ヘッダー行に指定されている名前を使用して列を参照できます。 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$blob = "csv/csv-general/seattle-library.csv"
Get-QueryCsv $ctx $container $blob "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'" $false

```

### <a name="net"></a>[.NET](#tab/dotnet)

非同期メソッド `BlobQuickQueryClient.QueryAsync` は、クエリをクエリ アクセラレーション API に送信し、その結果を [Stream](/dotnet/api/system.io.stream) オブジェクトとしてアプリケーションにストリーミングで戻します。

```cs
static async Task QueryHemingway(BlockBlobClient blob)
{
    string query = @"SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    await DumpQueryCsv(blob, query, false);
}

private static async Task DumpQueryCsv(BlockBlobClient blob, string query, bool headers)
{
    try
    {
        var options = new BlobQueryOptions() {
            InputTextConfiguration = new BlobQueryCsvTextOptions() { HasHeaders = headers },
            OutputTextConfiguration = new BlobQueryCsvTextOptions() { HasHeaders = true },
            ProgressHandler = new Progress<long>((finishedBytes) => Console.Error.WriteLine($"Data read: {finishedBytes}"))
        };
        options.ErrorHandler += (BlobQueryError err) => {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.Error.WriteLine($"Error: {err.Position}:{err.Name}:{err.Description}");
            Console.ResetColor();
        };
        // BlobDownloadInfo exposes a Stream that will make results available when received rather than blocking for the entire response.
        using (var reader = new StreamReader((await blob.QueryAsync(
                query,
                options)).Value.Content))
        {
            using (var parser = new CsvReader(reader, new CsvConfiguration(CultureInfo.CurrentCulture, hasHeaderRecord: true) { HasHeaderRecord = true }))
            {
                while (await parser.ReadAsync())
                {
                    Console.Out.WriteLine(String.Join(" ", parser.Parser.Record));
                }
            }
        }
    }
    catch (Exception ex)
    {
        Console.Error.WriteLine("Exception: " + ex.ToString());
    }
}

```

### <a name="java"></a>[Java](#tab/java)

メソッド `BlobQuickQueryClient.openInputStream()` は、クエリをクエリ アクセラレーション API に送信し、その結果を、他の InputStream オブジェクトと同様に読み取ることができる `InputStream` オブジェクトとしてアプリケーションにストリーミングで戻します。

```java
static void QueryHemingway(BlobClient blobClient) {
    String expression = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    DumpQueryCsv(blobClient, expression, true);
}

static void DumpQueryCsv(BlobClient blobClient, String query, Boolean headers) {
    try {
        BlobQuerySerialization input = new BlobQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(headers)
            .setFieldQuote('\0')
            .setEscapeChar('\\');
        BlobQuerySerialization output = new BlobQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(true)
            .setFieldQuote('\0')
            .setEscapeChar('\n');
        Consumer<BlobQueryError> errorConsumer = System.out::println;
        Consumer<BlobQueryProgress> progressConsumer = progress -> System.out.println("total bytes read: " + progress.getBytesScanned());
        BlobQueryOptions queryOptions = new BlobQueryOptions(query)
            .setInputSerialization(input)
            .setOutputSerialization(output)
            .setErrorConsumer(errorConsumer)
            .setProgressConsumer(progressConsumer);            

        /* Open the query input stream. */
        InputStream stream = blobClient.openQueryInputStream(queryOptions).getValue();
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(stream))) {
            /* Read from stream like you normally would. */
            for (CSVRecord record : CSVParser.parse(reader, CSVFormat.EXCEL.withHeader())) {
                System.out.println(record.toString());
            }
        }
    } catch (Exception e) {
        System.err.println("Exception: " + e.toString());
        e.printStackTrace(System.err);
    }
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_hemingway(blob: BlobClient):
    query = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'"
    dump_query_csv(blob, query, False)

def dump_query_csv(blob: BlobClient, query: str, headers: bool):
    qa_reader = blob.query_blob(query, blob_format=DelimitedTextDialect(has_header=headers), on_error=report_error, encoding='utf-8')
    # records() returns a generator that will stream results as received. It will not block pending all results.
    csv_reader = csv.reader(qa_reader.records())
    for row in csv_reader:
        print("*".join(row))
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

この例では、クエリがクエリ アクセラレーション API に送信され、結果が戻されています。 `queryHemingway` ヘルパー関数に渡される `blob` オブジェクトは [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) 型です。 [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) オブジェクトを取得する方法の詳細については、「[クイック スタート:Node.js の JavaScript v12 SDK を使用して BLOB を管理する](storage-quickstart-blobs-nodejs.md)」を参照してください。

```javascript
async function queryHemingway(blob)
{
    const query = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    await dumpQueryCsv(blob, query, false);
}

async function dumpQueryCsv(blob, query, headers)
{
    var response = await blob.query(query, {
        inputTextConfiguration: {
            kind: "csv",
            recordSeparator: '\n',
            hasHeaders: headers
        },
        outputTextConfiguration: {
            kind: "csv",
            recordSeparator: '\n',
            hasHeaders: true
        },
        onProgress: (progress) => console.log(`Data read: ${progress.loadedBytes}`),
        onError: (err) => console.error(`Error: ${err.position}:${err.name}:${err.description}`)});
    return new Promise(
        function (resolve, reject) {
            csv.parseStream(response.readableStreamBody)
                .on('data', row => console.log(row))
                .on('error', error => {
                    console.error(error);
                    reject(error);
                })
                .on('end', rowCount => resolve());
    });
}
```

---

## <a name="retrieve-specific-columns"></a>特定の列を取得する

結果の範囲を列のサブセットに限定することができます。 このようにして、特定の計算を実行するために必要な列のみを取得します。 これにより、ネットワーク経由で転送されるデータが少なくなるため、アプリケーションのパフォーマンスが向上し、コストが削減されます。 

このコードは、データセット内にあるすべてのブックの `BibNum` 列のみを取得します。 また、ソース ファイルのヘッダー行の情報を使用して、クエリ内の列を参照します。

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$blob = "csv/csv-general/seattle-library-with-headers.csv"
Get-QueryCsv $ctx $container $blob "SELECT BibNum FROM BlobStorage" $true

```

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryBibNum(BlockBlobClient blob)
{
    string query = @"SELECT BibNum FROM BlobStorage";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryBibNum(BlobClient blobClient)
{
    String expression = "SELECT BibNum FROM BlobStorage";
    DumpQueryCsv(blobClient, expression, true);
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_bibnum(blob: BlobClient):
    query = "SELECT BibNum FROM BlobStorage"
    dump_query_csv(blob, query, True)
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
async function queryBibNum(blob)
{
    const query = "SELECT BibNum FROM BlobStorage";
    await dumpQueryCsv(blob, query, true);
}
```

---

次のコードでは、行のフィルター処理と列のプロジェクションを同じクエリで組み合わせています。 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Get-QueryCsv $ctx $container $blob $query $true

Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType 
            FROM BlobStorage 
            WHERE ItemType IN 
                ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')"

```

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryDvds(BlockBlobClient blob)
{
    string query = @"SELECT BibNum, Title, Author, ISBN, Publisher, ItemType 
        FROM BlobStorage 
        WHERE ItemType IN 
            ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryDvds(BlobClient blobClient)
{
    String expression = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType " +
                        "FROM BlobStorage " +
                        "WHERE ItemType IN " +
                        "   ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    DumpQueryCsv(blobClient, expression, true);
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_dvds(blob: BlobClient):
    query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType "\
        "FROM BlobStorage "\
        "WHERE ItemType IN "\
        "   ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')"
    dump_query_csv(blob, query, True)
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
async function queryDvds(blob)
{
    const query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType " +
                  "FROM BlobStorage " +
                  "WHERE ItemType IN " + 
                  " ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    await dumpQueryCsv(blob, query, true);
}
```

---

## <a name="next-steps"></a>次の手順

- [Azure Data Lake Storage のクエリ アクセラレーション](data-lake-storage-query-acceleration.md)
- [クエリ アクセラレーション SQL 言語リファレンス](query-acceleration-sql-reference.md)
