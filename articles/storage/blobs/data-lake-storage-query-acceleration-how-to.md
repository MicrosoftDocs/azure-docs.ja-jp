---
title: Azure Data Lake Storage のクエリ アクセラレーション (プレビュー) を使用してデータをフィルター処理する | Microsoft Docs
description: クエリ アクセラレーション (プレビュー) を使用して、ストレージ アカウントからデータのサブセットを取得します。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 04/21/2020
ms.author: normesta
ms.reviewer: jamsbak
ms.custom: devx-track-csharp
ms.openlocfilehash: 6de6661e5c970c7c3cbfc944b8539060b8844a36
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89005226"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration-preview"></a>Azure Data Lake Storage のクエリ アクセラレーション (プレビュー) を使用してデータをフィルター処理する

この記事では、クエリ アクセラレーション (プレビュー) を使用して、ストレージ アカウントからデータのサブセットを取得する方法について説明します。 

クエリ アクセラレーション (プレビュー) は、特定の操作を実行するために必要なデータのみを取得することによって、アプリケーションと分析フレームワークでデータ処理を劇的に最適化できるようにする Azure Data Lake Storage の新しい機能です。 詳細については、「[Azure Data Lake Storage のクエリ アクセラレーション (プレビュー)](data-lake-storage-query-acceleration.md)」を参照してください。

> [!NOTE]
> クエリ アクセラレーション機能はパブリック プレビュー段階であり、カナダ中部およびフランス中部リージョンで利用できます。 制限事項を確認するには、[既知の問題](data-lake-storage-known-issues.md)に関する記事を参照してください。 プレビューに登録するには、[こちらのフォーム](https://aka.ms/adls/qa-preview-signup)を参照してください。  

## <a name="prerequisites"></a>前提条件

### <a name="net"></a>[.NET](#tab/dotnet)

- Azure Storage にアクセスするには、Azure サブスクリプションが必要です。 まだサブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

- **汎用 v2** ストレージ アカウント。 「[ストレージ アカウントを作成する](../common/storage-quickstart-create-account.md)」を参照してください。

- [.NET SDK](https://dotnet.microsoft.com/download)。 

### <a name="java"></a>[Java](#tab/java)

- Azure Storage にアクセスするには、Azure サブスクリプションが必要です。 まだサブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

- **汎用 v2** ストレージ アカウント。 「[ストレージ アカウントを作成する](../common/storage-quickstart-create-account.md)」を参照してください。

- [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable) バージョン 8 以降。

- [Apache Maven](https://maven.apache.org/download.cgi)。 

  > [!NOTE] 
  > この記事では、Apache Maven を使用して Java プロジェクトを作成済みであることを前提としています。 Apache Maven を使用してプロジェクトを作成する方法の例については、「[設定](storage-quickstart-blobs-java.md#setting-up)」を参照してください。
  
---

## <a name="install-packages"></a>パッケージをインストールする 

### <a name="net"></a>[.NET](#tab/dotnet)

1. クエリ アクセラレーション パッケージをダウンロードします。 リンク [https://aka.ms/adls/qqsdk/.net](https://aka.ms/adls/qqsdk/.net) を使用して、これらのパッケージを含む圧縮された .zip ファイルを取得できます。 

2. このファイルの内容をプロジェクト ディレクトリに抽出します。

3. プロジェクト ファイル ( *.csproj*) をテキスト エディターで開き、\<Project\> 要素内に以下のパッケージ参照を追加します。

   ```xml
   <ItemGroup>
       <PackageReference Include="Azure.Storage.Blobs" Version="12.5.0-preview.1" />
       <PackageReference Include="Azure.Storage.Common" Version="12.4.0-preview.1" />
       <PackageReference Include="Azure.Storage.QuickQuery" Version="12.0.0-preview.1" />
   </ItemGroup>
   ```

4. プレビュー SDK パッケージを復元します。 このコマンド例では、`dotnet restore` コマンドを使用してプレビュー SDK パッケージを復元します。 

   ```console
   dotnet restore --source C:\Users\contoso\myProject
   ```

5. パブリック NuGet リポジトリから他のすべての依存関係を復元します。

   ```console
   dotnet restore
   ```

### <a name="java"></a>[Java](#tab/java)

1. プロジェクトのルートにディレクトリを作成します。 ルート ディレクトリは、**pom.xml** ファイルが格納されているディレクトリです。

   > [!NOTE]
   > この記事の例では、ディレクトリの名前が **lib** であることを前提としています。

2. クエリ アクセラレーション パッケージをダウンロードします。 リンク [https://aka.ms/adls/qqsdk/java](https://aka.ms/adls/qqsdk/java) を使用して、これらのパッケージを含む圧縮された .zip ファイルを取得できます。 

3. この .zip ファイル内のファイルを、作成したディレクトリに抽出します。 この例では、このディレクトリには **lib** という名前が付いています。 

4. テキスト エディターで *pom.xml* ファイルを開きます。 依存関係のグループに、次の dependency 要素を追加します。 

   ```xml
   <!-- Request static dependencies from Maven -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-core</artifactId>
       <version>1.3.0</version>
   </dependency>
   <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-core-http-netty</artifactId>
      <version>1.3.0</version>
   </dependency>
   <dependency>
      <groupId>org.apache.avro</groupId>
      <artifactId>avro</artifactId>
      <version>1.9.2</version>
   </dependency>
   <dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-csv</artifactId>
    <version>1.8</version>
   </dependency>
   <!-- Local dependencies -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-blob</artifactId>
       <version>12.5.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-blob-12.5.0-beta.1.jar</systemPath>
   </dependency>
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-common</artifactId>
       <version>12.5.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-common-12.5.0-beta.1.jar</systemPath>
   </dependency>
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-quickquery</artifactId>
       <version>12.0.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-quickquery-12.0.0-beta.1.jar</systemPath>
   </dependency>
   ```

---

## <a name="add-statements"></a>ステートメントを追加する


### <a name="net"></a>[.NET](#tab/dotnet)

次の `using` ステートメントをコード ファイルの先頭に追加します。

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
using Azure.Storage.QuickQuery;
using Azure.Storage.QuickQuery.Models;
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
using System.Threading;
using System.Linq;
```

### <a name="java"></a>[Java](#tab/java)

次の `import` ステートメントをコード ファイルの先頭に追加します。

```java
import com.azure.storage.blob.*;
import com.azure.storage.blob.models.*;
import com.azure.storage.common.*;
import com.azure.storage.quickquery.*;
import com.azure.storage.quickquery.models.*;
import java.io.*;
import org.apache.commons.csv.*;
```

---

## <a name="retrieve-data-by-using-a-filter"></a>フィルターを使用してデータを取得する

SQL を使用して、クエリ アクセラレーション要求で行フィルター述語と列のプロジェクションを指定できます。 次のコードでは、ストレージ内の CSV ファイルに対してクエリを行い、3番目の列が `Hemingway, Ernest` 値と一致するすべてのデータの行を返します。 

- SQL クエリでは、クエリ対象のファイルを示すためにキーワード `BlobStorage` が使用されます。

- 列参照は `_N` として指定され、最初の列は `_1` です。 ソース ファイルにヘッダー行が含まれている場合は、ヘッダー行に指定されている名前を使用して列を参照できます。 

### <a name="net"></a>[.NET](#tab/dotnet)

非同期メソッド `BlobQuickQueryClient.QueryAsync` は、クエリをクエリ アクセラレーション API に送信し、その結果を [Stream](https://docs.microsoft.com/dotnet/api/system.io.stream?view=netframework-4.8) オブジェクトとしてアプリケーションにストリーミングで戻します。

```cs
static async Task QueryHemingway(BlockBlobClient blob)
{
    string query = @"SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest'";
    await DumpQueryCsv(blob, query, false);
}

private static async Task DumpQueryCsv(BlockBlobClient blob, string query, bool headers)
{
    try
    {
        using (var reader = new StreamReader((await blob.GetQuickQueryClient().QueryAsync(query,
                new CsvTextConfiguration() { HasHeaders = headers }, 
                new CsvTextConfiguration() { HasHeaders = false }, 
                new ErrorHandler(),
                new BlobRequestConditions(), 
                new ProgressHandler(),
                CancellationToken.None)).Value.Content))
        {
            using (var parser = new CsvReader(reader, new CsvConfiguration(CultureInfo.CurrentCulture) 
            { HasHeaderRecord = false }))
            {
                while (await parser.ReadAsync())
                {
                    parser.Context.Record.All(cell =>
                    {
                        Console.Out.Write(cell + "  ");
                        return true;
                    });
                    Console.Out.WriteLine();
                }
            }
        }
    }
    catch (Exception ex)
    {
        Console.Error.WriteLine("Exception: " + ex.ToString());
    }
}

class ErrorHandler : IBlobQueryErrorReceiver
{
    public void ReportError(BlobQueryError err)
    {
        Console.Error.WriteLine($"Error: {err.Name}:{ err.Description }");
    }
}

class ProgressHandler : IProgress<long>
{
    public void Report(long value)
    {
        Console.Error.WriteLine("Bytes scanned: " + value.ToString());
    }
}

```

### <a name="java"></a>[Java](#tab/java)

メソッド `BlobQuickQueryClient.openInputStream()` は、クエリをクエリ アクセラレーション API に送信し、その結果を、他の InputStream オブジェクトと同様に読み取ることができる `InputStream` オブジェクトとしてアプリケーションにストリーミングで戻します。

```java
static void QueryHemingway(BlobClient blobClient) {
    String expression = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest'";
    DumpQueryCsv(blobClient, expression, false);
}

static void DumpQueryCsv(BlobClient blobClient, String query, Boolean headers) {
    try {
    
        BlobQuickQueryDelimitedSerialization input = new BlobQuickQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(headers)
            .setFieldQuote('\0')
            .setEscapeChar('\\');

        BlobQuickQueryDelimitedSerialization output = new BlobQuickQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(false)
            .setFieldQuote('\0')
            .setEscapeChar('\n');
                
        BlobRequestConditions requestConditions = null;
        /* ErrorReceiver determines what to do on errors. */
        ErrorReceiver<BlobQuickQueryError> errorReceiver = System.out::println;

        /* ProgressReceiver details how to log progress*/
        com.azure.storage.common.ProgressReceiver progressReceiver = System.out::println;
    
        /* Create a query acceleration client to the blob. */
        BlobQuickQueryClient qqClient = new BlobQuickQueryClientBuilder(blobClient)
            .buildClient();
        /* Open the query input stream. */
        InputStream stream = qqClient.openInputStream(query, input, output, requestConditions, errorReceiver, progressReceiver);
            
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(stream))) {
            /* Read from stream like you normally would. */
            for (CSVRecord record : CSVParser.parse(reader, CSVFormat.EXCEL)) {
                System.out.println(record.toString());
            }
        }
    } catch (Exception e) {
        System.err.println("Exception: " + e.toString());
    }
}
```

---

## <a name="retrieve-specific-columns"></a>特定の列を取得する

結果の範囲を列のサブセットに限定することができます。 このようにして、特定の計算を実行するために必要な列のみを取得します。 これにより、ネットワーク経由で転送されるデータが少なくなるため、アプリケーションのパフォーマンスが向上し、コストが削減されます。 

このコードは、データセット内にあるすべてのブックの `PublicationYear` 列のみを取得します。 また、ソース ファイルのヘッダー行の情報を使用して、クエリ内の列を参照します。


### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryPublishDates(BlockBlobClient blob)
{
    string query = @"SELECT PublicationYear FROM BlobStorage";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryPublishDates(BlobClient blobClient)
{
    String expression = "SELECT PublicationYear FROM BlobStorage";
    DumpQueryCsv(blobClient, expression, true);
}
```

---

次のコードでは、行のフィルター処理と列のプロジェクションを同じクエリで組み合わせています。 

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryMysteryBooks(BlockBlobClient blob)
{
    string query = @"SELECT BibNum, Title, Author, ISBN, Publisher FROM BlobStorage WHERE Subjects LIKE '%Mystery%'";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryMysteryBooks(BlobClient blobClient)
{
    String expression = "SELECT BibNum, Title, Author, ISBN, Publisher FROM BlobStorage WHERE Subjects LIKE '%Mystery%'";
    DumpQueryCsv(blobClient, expression, true);
}
```

---

## <a name="next-steps"></a>次のステップ

- [クエリ アクセラレーションの登録フォーム](https://aka.ms/adls/qa-preview-signup)    
- [Azure Data Lake Storage のクエリ アクセラレーション (プレビュー)](data-lake-storage-query-acceleration.md)
- [クエリ アクセラレーション SQL 言語リファレンス (プレビュー)](query-acceleration-sql-reference.md)
