---
title: 共有アクセス署名 (SAS) を作成して Azure Blob Storage で使用する | Microsoft Docs
description: このチュートリアルでは、Blob Storage で使用する共有アクセス署名を作成する方法と、クライアント アプリケーションから共有アクセス署名を使用する方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.devlang: dotnet
ms.date: 05/15/2017
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 5dcb9f16b589b8332d5fcf35c9d8b4cd914460f2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58012545"
---
# <a name="shared-access-signatures-part-2-create-and-use-a-sas-with-blob-storage"></a>Shared Access Signature、第 2 部: BLOB ストレージでの SAS の作成と使用

[第 1 部](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) では、共有アクセス署名 (SAS) を紹介し、そのベスト プラクティスについて説明しました。 第 2 部では、BLOB ストレージで共有アクセス署名を生成し、使用する方法を説明します。 例は C# で記述され、Azure .NET 用ストレージ クライアント ライブラリを利用しています。 このチュートリアルの例では、次の操作を行います。

* コンテナーで共有アクセス署名を生成する
* BLOB で共有アクセス署名を生成する
* 保存されたアクセス ポリシーを作成して、コンテナーのリソースの署名を管理する
* クライアント アプリケーションで共有アクセス署名をテストする

## <a name="about-this-tutorial"></a>このチュートリアルについて
このチュートリアルでは、コンテナーと BLOB の共有アクセス署名の作成と使用方法を示す 2 種類のコンソール アプリケーションを作成します。

**アプリケーション 1**: 管理アプリケーションです。 コンテナーと BLOB の共有アクセス署名を生成します。 ソース コードにストレージ アカウント アクセス キーが含まれます。

**アプリケーション 2**:クライアント アプリケーションです。 最初のアプリケーションが作成した共有アクセス署名を使用して、コンテナー リソースと BLOB リソースにアクセスします。 共有アクセス署名のみを使用して、コンテナーと BLOB リソースにアクセスします。ストレージ アカウント アクセス キーは "*含まれません"*。

## <a name="part-1-create-a-console-application-to-generate-shared-access-signatures"></a>パート 1:共有アクセス署名を生成するコンソール アプリケーションを作成する
まず、Azure .NET 用ストレージ クライアント ライブラリがインストールされていることを確認します。 クライアント ライブラリの最新のアセンブリを含む [NuGet パッケージ](https://nuget.org/packages/WindowsAzure.Storage/ "NuGet パッケージ")をインストールできます。 これは、最新の修正プログラムが適用されていることを保証するため、推奨される方法です。 また、[Azure SDK for .NET](https://azure.microsoft.com/downloads/) の最新バージョンの一部として、クライアント ライブラリをダウンロードすることもできます。

Visual Studio で、新しい Windows コンソール アプリケーションを作成し、 **GenerateSharedAccessSignatures**という名前を付けます。 次の方法のいずれかを使用して、[Microsoft.WindowsAzure.ConfigurationManager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager) と [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/) への参照を追加します。

* Visual Studio で [NuGet Package Manager ](https://docs.nuget.org/consume/installing-nuget) を使用します。 **[プロジェクト]** > **[NuGet パッケージの管理]** を選択し、各パッケージ (Microsoft.WindowsAzure.ConfigurationManager と WindowsAzure.Storage) をオンラインで検索し、それらをインストールします。
* または、インストールした Azure SDK でアセンブリを探し、そのアセンブリへの参照を追加します。
  * Microsoft.WindowsAzure.Configuration.dll
  * Microsoft.WindowsAzure.Storage.dll

Program.cs ファイルの先頭に、次の **using** ディレクティブを追加します。

```csharp
using System.IO;
using Microsoft.Azure;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
```

自分のストレージ アカウントを指定する接続文字列を含む構成設定が含まれるように、app.config ファイルを編集します。 編集した app.config ファイルは、次のようになります。

```xml
<configuration>
  <startup>
    <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
  </startup>
  <appSettings>
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey"/>
  </appSettings>
</configuration>
```

### <a name="generate-a-shared-access-signature-uri-for-a-container"></a>コンテナーの共有アクセス署名 URI を生成する
最初に、新しいコンテナーで共有アクセス署名を生成するメソッドを追加します。 この場合、署名は保存されたアクセス ポリシーには関連付けられないため、その有効期限および付与するアクセス許可を示す情報が URI に含まれます。

まず、ストレージ アカウントへのアクセスを承認し、新しいコンテナーを作成するために、次のコードを **Main()** メソッドに追加します。

```csharp
static void Main(string[] args)
{
    //Parse the connection string and return a reference to the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create the blob client object.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container to use for the sample code, and create it if it does not exist.
    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
    container.CreateIfNotExists();

    //Insert calls to the methods created below here...

    //Require user input before closing the console window.
    Console.ReadLine();
}
```

次に、コンテナーの共有アクセス署名を生成し、署名 URI を返すメソッドを追加します。

```csharp
static string GetContainerSasUri(CloudBlobContainer container)
{
    //Set the expiry time and permissions for the container.
    //In this case no start time is specified, so the shared access signature becomes valid immediately.
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
    sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Write;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

**Main()** メソッドの末尾で、**Console.ReadLine()** の呼び出しの前に次の行を追加します。これで、**GetContainerSasUri()** を呼び出し、署名 URI をコンソール ウィンドウに書き込みます。

```csharp
//Generate a SAS URI for the container, without a stored access policy.
Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
Console.WriteLine();
```

コンパイルし、実行して、新しいコンテナーの共有アクセス署名 URI を出力します。 URI は、次のようになります。

```
https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3D
```

このコードを実行すると、コンテナーで作成した共有アクセス署名は、次の 24 時間にわたって有効になります。 署名によってクライアントには、コンテナー内の BLOB の一覧を表示し、新しい BLOB をコンテナーに書き込むアクセス許可が付与されます。

### <a name="generate-a-shared-access-signature-uri-for-a-blob"></a>BLOB の共有アクセス署名 URI を生成する
次に、同様のコードを作成して、新しい BLOB をコンテナー内に作成し、その共有アクセス署名を生成します。 この共有アクセス署名は、保存されるアクセス ポリシーに関連付けられないため、開始時刻、有効期限、およびアクセス許可情報が URI に含まれています。

新しい BLOB を作成してテキストを書き込んだ後に共有アクセス署名を生成し、署名 URI を返す新しいメソッドを追加します。

```csharp
static string GetBlobSasUri(CloudBlobContainer container)
{
    //Get a reference to a blob within the container.
    CloudBlockBlob blob = container.GetBlockBlobReference("sasblob.txt");

    //Upload text to the blob. If the blob does not yet exist, it will be created.
    //If the blob does exist, its existing content will be overwritten.
    string blobContent = "This blob will be accessible to clients via a shared access signature (SAS).";
    blob.UploadText(blobContent);

    //Set the expiry time and permissions for the blob.
    //In this case, the start time is specified as a few minutes in the past, to mitigate clock skew.
    //The shared access signature will be valid immediately.
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
    sasConstraints.SharedAccessStartTime = DateTimeOffset.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;

    //Generate the shared access signature on the blob, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

**Main()** メソッドの末尾で、**Console.ReadLine()** の呼び出しの前に、**GetBlobSasUri()** を呼び出す次の行を追加し、共有アクセス署名 URI をコンソール ウィンドウに書き込みます。

```csharp
//Generate a SAS URI for a blob within the container, without a stored access policy.
Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
Console.WriteLine();
```

コンパイルし、実行して、新しい BLOB の共有アクセス署名 URI を出力します。 URI は、次のようになります。

```
https://storageaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-12T23%3A37%3A08Z&se=2013-04-13T00%3A12%3A08Z&sr=b&sp=rw&sig=dF2064yHtc8RusQLvkQFPItYdeOz3zR8zHsDMBi4S30%3D
```

### <a name="create-a-stored-access-policy-on-the-container"></a>保存されるアクセス ポリシーをコンテナーで作成する
今度は、保存されているアクセス ポリシーをコンテナーで作成します。これで、関連付けられているすべての共有アクセス署名の制約が定義されます。

前の例で、共有アクセス署名 URI 自体の開始時刻 (暗黙の、または明示的な)、有効期限、およびアクセス許可を指定しました。 次の例では、これらを、共有アクセス署名ではなく、保存されるアクセス ポリシーで指定します。 こうすると、共有アクセス署名を再発行せずに、これらの制約を変更できます。

共有アクセス署名に 1 つ以上の制約を指定し、保存されるアクセス ポリシーに残りの制約を指定することができます。 ただし、開始時刻、有効期限、およびアクセス許可は、どちらか 1 つの場所にのみ指定できます。 たとえば、共有アクセス署名でアクセス許可を指定した場合、保存されるアクセス ポリシーにアクセス許可を指定することはできません。

保存されるアクセス ポリシーをコンテナーに追加するときは、コンテナーの既存のアクセス許可を取得し、新しいアクセス ポリシーを追加した後、コンテナーのアクセス許可を設定する必要があります。

コンテナーに保存される新しいアクセス ポリシーを作成し、そのポリシーの名前を返す新しいメソッドを、次のように追加します。

```csharp
static void CreateSharedAccessPolicy(CloudBlobClient blobClient, CloudBlobContainer container,
    string policyName)
{
    //Get the container's existing permissions.
    BlobContainerPermissions permissions = container.GetPermissions();

    //Create a new shared access policy and define its constraints.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
    };

    //Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    container.SetPermissions(permissions);
}
```

**Main()** メソッドの末尾にある **Console.ReadLine()** の呼び出しの前に次の行を追加します。このコードは、最初に既存のアクセス ポリシーを消去してから、**CreateSharedAccessPolicy()** メソッドを呼び出します。

```csharp
//Clear any existing access policies on container.
BlobContainerPermissions perms = container.GetPermissions();
perms.SharedAccessPolicies.Clear();
container.SetPermissions(perms);

//Create a new access policy on the container, which may be optionally used to provide constraints for
//shared access signatures on the container and the blob.
string sharedAccessPolicyName = "tutorialpolicy";
CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);
```

コンテナーのアクセス ポリシーを消去するときは、最初にコンテナーの既存のアクセス許可を取得し、アクセス許可を消去した後、もう一度アクセス許可を設定する必要があります。

### <a name="generate-a-shared-access-signature-uri-on-the-container-that-uses-an-access-policy"></a>アクセス ポリシーを使用するコンテナーで共有アクセス署名 URI を生成する
次に、先ほど作成したコンテナーで別の共有アクセス署名を作成しますが、今度は、前の例で作成した保存されるアクセス ポリシーに署名を関連付けます。

コンテナーで別の共有アクセス署名を生成する新しいメソッドを追加します。

```csharp
static string GetContainerSasUriWithPolicy(CloudBlobContainer container, string policyName)
{
    //Generate the shared access signature on the container. In this case, all of the constraints for the
    //shared access signature are specified on the stored access policy.
    string sasContainerToken = container.GetSharedAccessSignature(null, policyName);

    //Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

**Main()** メソッドの末尾で、**Console.ReadLine()** の呼び出しの前に次の行を追加します。これで、**GetContainerSasUriWithPolicy** メソッドを呼び出します。

```csharp
//Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
Console.WriteLine();
```

### <a name="generate-a-shared-access-signature-uri-on-the-blob-that-uses-an-access-policy"></a>アクセス ポリシーを使用する BLOB での共有アクセス署名 URI の生成
最後に、同様のメソッドを追加することで、別の BLOB を作成し、保存されるアクセス ポリシーに関連付けられた共有アクセス署名を生成します。

BLOB を作成し、共有アクセス署名を生成する新しいメソッドを追加します。

```csharp
static string GetBlobSasUriWithPolicy(CloudBlobContainer container, string policyName)
{
    //Get a reference to a blob within the container.
    CloudBlockBlob blob = container.GetBlockBlobReference("sasblobpolicy.txt");

    //Upload text to the blob. If the blob does not yet exist, it will be created.
    //If the blob does exist, its existing content will be overwritten.
    string blobContent = "This blob will be accessible to clients via a shared access signature. " +
    "A stored access policy defines the constraints for the signature.";
    MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
    ms.Position = 0;
    using (ms)
    {
        blob.UploadFromStream(ms);
    }

    //Generate the shared access signature on the blob.
    string sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

**Main()** メソッドの末尾で、**Console.ReadLine()** の呼び出しの前に次の行を追加します。これで、**GetBlobSasUriWithPolicy** メソッドを呼び出します。

```csharp
//Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
Console.WriteLine();
```

**Main()** メソッドは、全体として、次のようになります。 これを実行して、共有アクセス署名 URI をコンソール ウィンドウに書き込み、それをコピーしてテキスト ファイルに貼り付けます。これは、このチュートリアルの第 2 部で使用します。

```csharp
static void Main(string[] args)
{
    //Parse the connection string and return a reference to the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create the blob client object.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container to use for the sample code, and create it if it does not exist.
    CloudBlobContainer container = blobClient.GetContainerReference("sascontainer");
    container.CreateIfNotExists();

    //Generate a SAS URI for the container, without a stored access policy.
    Console.WriteLine("Container SAS URI: " + GetContainerSasUri(container));
    Console.WriteLine();

    //Generate a SAS URI for a blob within the container, without a stored access policy.
    Console.WriteLine("Blob SAS URI: " + GetBlobSasUri(container));
    Console.WriteLine();

    //Clear any existing access policies on container.
    BlobContainerPermissions perms = container.GetPermissions();
    perms.SharedAccessPolicies.Clear();
    container.SetPermissions(perms);

    //Create a new access policy on the container, which may be optionally used to provide constraints for
    //shared access signatures on the container and the blob.
    string sharedAccessPolicyName = "tutorialpolicy";
    CreateSharedAccessPolicy(blobClient, container, sharedAccessPolicyName);

    //Generate a SAS URI for the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Container SAS URI using stored access policy: " + GetContainerSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

    //Generate a SAS URI for a blob within the container, using a stored access policy to set constraints on the SAS.
    Console.WriteLine("Blob SAS URI using stored access policy: " + GetBlobSasUriWithPolicy(container, sharedAccessPolicyName));
    Console.WriteLine();

    Console.ReadLine();
}
```

GenerateSharedAccessSignatures コンソール アプリケーションを実行すると、次のような出力が表示されます。 これが、チュートリアルの第 2 部で使用する共有アクセス署名です。

```
Container SAS URI: https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=pFlEZD%2F6sJTNLxD%2FQ26Hh85j%2FzYPxZav6mP1KJwnvJE%3D&se=2017-05-16T16%3A16%3A47Z&sp=wl

Blob SAS URI: https://storagesample.blob.core.windows.net/sascontainer/sasblob.txt?sv=2016-05-31&sr=b&sig=%2FiBWAZbXESzCMvRcm7JwJBK0gT0BtPSWEq4pRwmlBRI%3D&st=2017-05-15T16%3A11%3A48Z&se=2017-05-16T16%3A16%3A48Z&sp=rw

Container SAS URI using stored access policy: https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&si=tutorialpolicy&sig=aMb6rKDvvpfiGVsZI2rCmyUra6ZPpq%2BZ%2FLyTgAeec%2Bk%3D

Blob SAS URI using stored access policy: https://storagesample.blob.core.windows.net/sascontainer/sasblobpolicy.txt?sv=2016-05-31&sr=b&si=tutorialpolicy&sig=%2FkTWkT23SS45%2FoF4bK2mqXkN%2BPKs%2FyHuzkfQ4GFoZVU%3D
```

## <a name="part-2-create-a-console-application-to-test-the-shared-access-signatures"></a>パート 2:共有アクセス署名をテストするコンソール アプリケーションを作成する
前の例で作成した共有アクセス署名をテストするために、署名を使用して、コンテナーと BLOB で操作を実行する、もう 1 つのコンソール アプリケーションを作成します。

> [!NOTE]
> チュートリアルの第 1 部の完了後、24 時間を超える時間が経過している場合、生成した署名は無効になっています。 その場合は、最初のコンソール アプリケーションでコードを実行して、チュートリアルの第 2 部で使用するための新しい共有アクセス署名を生成する必要があります。
>

Visual Studio で、新しい Windows コンソール アプリケーションを作成し、 **ConsumeSharedAccessSignatures**という名前を付けます。 前に追加した場合と同様に、[Microsoft.WindowsAzure.ConfigurationManager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager) と [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/) への参照を追加します。

Program.cs ファイルの先頭に、次の **using** ディレクティブを追加します。

```csharp
using System.IO;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
```

**Main()** メソッドの本文に、次の文字列定数を追加し、その値を、チュートリアルの第 1 部で生成した共有アクセス署名に変更します。

```csharp
static void Main(string[] args)
{
    const string containerSAS = "<your container SAS>";
    const string blobSAS = "<your blob SAS>";
    const string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    const string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
}
```

### <a name="add-a-method-to-try-container-operations-using-a-shared-access-signature"></a>共有アクセス署名を使用してコンテナーの操作を試すメソッドを追加する
次に、いくつかのコンテナー操作を、コンテナーの共有アクセス署名を使用してテストするメソッドを追加します。 共有アクセス署名は、署名だけに基づいてコンテナーへのアクセスを認証し、コンテナーに参照を返すために使用されます。

次のメソッドを Program.cs に追加します。

```csharp
static void UseContainerSAS(string sas)
{
    //Try performing container operations with the SAS provided.

    //Return a reference to the container using the SAS URI.
    CloudBlobContainer container = new CloudBlobContainer(new Uri(sas));

    //Create a list to store blob URIs returned by a listing operation on the container.
    List<ICloudBlob> blobList = new List<ICloudBlob>();

    //Write operation: write a new blob to the container.
    try
    {
        CloudBlockBlob blob = container.GetBlockBlobReference("blobCreatedViaSAS.txt");
        string blobContent = "This blob was created with a shared access signature granting write permissions to the container. ";
        blob.UploadText(blobContent);

        Console.WriteLine("Write operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Write operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //List operation: List the blobs in the container.
    try
    {
        foreach (ICloudBlob blob in container.ListBlobs())
        {
            blobList.Add(blob);
        }
        Console.WriteLine("List operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("List operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //Read operation: Get a reference to one of the blobs in the container and read it.
    try
    {
        CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
        MemoryStream msRead = new MemoryStream();
        msRead.Position = 0;
        using (msRead)
        {
            blob.DownloadToStream(msRead);
            Console.WriteLine(msRead.Length);
        }
        Console.WriteLine("Read operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Read operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
    Console.WriteLine();

    //Delete operation: Delete a blob in the container.
    try
    {
        CloudBlockBlob blob = container.GetBlockBlobReference(blobList[0].Name);
        blob.Delete();
        Console.WriteLine("Delete operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Delete operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
}
```

**Main()** メソッドを更新して、コンテナーで作成した両方の共有アクセス署名で **UseContainerSAS()** を呼び出します。

```csharp
static void Main(string[] args)
{
    string containerSAS = "<your container SAS>";
    string blobSAS = "<your blob SAS>";
    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

    //Call the test methods with the shared access signatures created on the container, with and without the access policy.
    UseContainerSAS(containerSAS);
    UseContainerSAS(containerSASWithAccessPolicy);

    Console.ReadLine();
}
```

### <a name="add-a-method-to-try-blob-operations-using-a-shared-access-signature"></a>共有アクセス署名を使用して BLOB の操作を試すメソッドを追加する
最後に、いくつかの BLOB 操作を、BLOB の共有アクセス署名を使用してテストするメソッドを追加します。 ここでは、コンストラクター **CloudBlockBlob(String)** を使用して共有アクセス署名で渡し、BLOB に参照を返します。 他の認証は不要で、署名だけに基づいて認証されます。

次のメソッドを Program.cs に追加します。

```csharp
static void UseBlobSAS(string sas)
{
    //Try performing blob operations using the SAS provided.

    //Return a reference to the blob using the SAS URI.
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(sas));

    //Write operation: Write a new blob to the container.
    try
    {
        string blobContent = "This blob was created with a shared access signature granting write permissions to the blob. ";
        MemoryStream msWrite = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
        msWrite.Position = 0;
        using (msWrite)
        {
            blob.UploadFromStream(msWrite);
        }
        Console.WriteLine("Write operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Write operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //Read operation: Read the contents of the blob.
    try
    {
        MemoryStream msRead = new MemoryStream();
        using (msRead)
        {
            blob.DownloadToStream(msRead);
            msRead.Position = 0;
            using (StreamReader reader = new StreamReader(msRead, true))
            {
                string line;
                while ((line = reader.ReadLine()) != null)
                {
                    Console.WriteLine(line);
                }
            }
        }
        Console.WriteLine("Read operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Read operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }

    //Delete operation: Delete the blob.
    try
    {
        blob.Delete();
        Console.WriteLine("Delete operation succeeded for SAS " + sas);
        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine("Delete operation failed for SAS " + sas);
        Console.WriteLine("Additional error information: " + e.Message);
        Console.WriteLine();
    }
}
```

**Main()** メソッドを更新して、BLOB で作成した両方の共有アクセス署名で **UseBlobSAS()** を呼び出します。

```csharp
static void Main(string[] args)
{
    string containerSAS = "<your container SAS>";
    string blobSAS = "<your blob SAS>";
    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";

    //Call the test methods with the shared access signatures created on the container, with and without the access policy.
    UseContainerSAS(containerSAS);
    UseContainerSAS(containerSASWithAccessPolicy);

    //Call the test methods with the shared access signatures created on the blob, with and without the access policy.
    UseBlobSAS(blobSAS);
    UseBlobSAS(blobSASWithAccessPolicy);

    Console.ReadLine();
}
```

コンソール アプリケーションを実行し、出力で、どの署名に対してどの操作が許可されているかを確認します。 コンソール ウィンドウでの出力は、次のようになります。

```
Write operation succeeded for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl

List operation succeeded for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl

Read operation failed for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl
Additional error information: The remote server returned an error: (403) Forbidden.

Delete operation failed for SAS https://storagesample.blob.core.windows.net/sascontainer?sv=2016-05-31&sr=c&sig=32EaQGuFyDMb3yOAey3wq%2B%2FLwgPQxAgSo7UhzLdyIDU%3D&se=2017-05-16T15%3A41%3A20Z&sp=wl
Additional error information: The remote server returned an error: (403) Forbidden.

...
```

## <a name="next-steps"></a>次の手順

* [Shared Access Signature、第 1 部: SAS モデルについて](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [コンテナーと BLOB への匿名読み取りアクセスを管理する](storage-manage-access-to-resources.md)
* [共有アクセス署名を使用したアクセスの委任 (REST API)](https://msdn.microsoft.com/library/azure/ee395415.aspx)
* [テーブルおよびキュー SAS についての MSDN ブログ](https://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)
