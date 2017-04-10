---
title: "Blob Storage での SAS の作成と使用 | Microsoft Docs"
description: "このチュートリアルでは、BLOB ストレージで使用する共有アクセス署名を作成する方法と、クライアント アプリケーションから共有アクセス署名を使用する方法について説明します。"
services: storage
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 491e0b3c-76d4-4149-9a80-bbbd683b1f3e
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/08/2016
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 931503f56b32ce9d1b11283dff7224d7e2f015ae
ms.openlocfilehash: 62ad4d3edeca07f1c4fe11fb6904dcbfb8f91435


---
# <a name="shared-access-signatures-part-2-create-and-use-a-sas-with-blob-storage"></a>Shared Access Signature、第 2 部: BLOB ストレージでの SAS の作成と使用
## <a name="overview"></a>概要
[第 1 部](storage-dotnet-shared-access-signature-part-1.md) では、共有アクセス署名 (SAS) を紹介し、そのベスト プラクティスについて説明しました。 第 2 部では、BLOB ストレージで共有アクセス署名を生成し、使用する方法を説明します。 例は C# で記述され、Azure .NET 用ストレージ クライアント ライブラリを利用しています。 説明しているシナリオには、共有アクセス署名を使用する場合の次の側面が含まれます。

* コンテナーでの共有アクセス署名の生成
* BLOB での共有アクセス署名の生成
* 保存されているアクセス ポリシーの作成と、コンテナーのリソースでの署名の管理
* クライアント アプリケーションからの共有アクセス署名のテスト

## <a name="about-this-tutorial"></a>このチュートリアルについて
このチュートリアルでは、2 種類のコンソール アプリケーションを作成して、コンテナーおよび BLOB の共有アクセス署名を作成する方法について特に説明します。 最初のコンソール アプリケーションで、コンテナーと BLOB に共有アクセス署名が生成されます。 このアプリケーションは、ストレージ アカウント キーを認識しています。 2 番目のコンソール アプリケーションはクライアント アプリケーションとして機能するアプリケーションであり、最初のアプリケーションが作成した共有アクセス署名を使って、コンテナー リソースと BLOB リソースにアクセスします。 このアプリケーションは、コンテナー リソースと BLOB リソースへのアクセスを認証するためにだけ、共有アクセス署名を使用します。アカウント キーは認識していません。

## <a name="part-1-create-a-console-application-to-generate-shared-access-signatures"></a>第 1 部 : コンソール アプリケーションの作成と共有アクセス署名の生成
まず、Azure .NET 用ストレージ クライアント ライブラリがインストールされていることを確認します。 クライアント ライブラリの最新アセンブリを含む [NuGet package](http://nuget.org/packages/WindowsAzure.Storage/ "NuGet パッケージ")をインストールできます。最新の修正プログラムが確実に含まれるように、この方法をお勧めします。 また、[Azure SDK for .NET](https://azure.microsoft.com/downloads/) の最新バージョンの一部として、クライアント ライブラリをダウンロードすることもできます。

Visual Studio で、新しい Windows コンソール アプリケーションを作成し、 **GenerateSharedAccessSignatures**という名前を付けます。 次の方法のいずれかを使用して、**Microsoft.WindowsAzure.Configuration.dll** と **Microsoft.WindowsAzure.Storage.dll** への参照を追加します。

* NuGet パッケージをインストールする場合は、まず [NuGet クライアント](https://docs.nuget.org/consume/installing-nuget)をインストールします。 Visual Studio で、**[プロジェクト]、[NuGet パッケージの管理]** の順に選択し、オンラインで "**Azure のストレージ**" を検索して、見つかったインストール手順に従います。
* または、インストールした Azure SDK でアセンブリを探し、そのアセンブリへの参照を追加することもできます。

Program.cs ファイルの先頭に、次の **using** ステートメントを追加します。

```csharp
using System.IO;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
```

自分のストレージ アカウントを指定する接続文字列を含む構成設定が含まれるように、app.config ファイルを編集します。 編集した app.config ファイルは、次のようになります。

```xml
<configuration>
  <startup>
    <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
  </startup>
  <appSettings>
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey"/>
  </appSettings>
</configuration>
```

### <a name="generate-a-shared-access-signature-uri-for-a-container"></a>コンテナーの共有アクセス署名 URI の生成
最初に、新しいコンテナーで共有アクセス署名を生成するメソッドを追加します。 この場合、署名は保存されたアクセス ポリシーには関連付けられないため、その有効期限および付与するアクセス許可を示す情報が URI に含まれます。

まず、ストレージ アカウントへのアクセスを認証し、新しいコンテナーを作成するために、次のコードを **Main()** メソッドに追加します。

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

次に、コンテナーの共有アクセス署名を生成し、署名 URI を返す新しいメソッドを追加します。

```csharp
static string GetContainerSasUri(CloudBlobContainer container)
{
    //Set the expiry time and permissions for the container.
    //In this case no start time is specified, so the shared access signature becomes valid immediately.
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List;

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

コンパイルし、実行して、新しいコンテナーの共有アクセス署名 URI を出力します。 URI は、次の URI のようになります。

`https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2013-04-13T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3D`

コードを実行すると、コンテナーで作成した共有アクセス署名が&24; 時間だけ有効になります。 署名によってクライアントには、コンテナー内の BLOB の一覧を表示し、新しい BLOB をコンテナーに書き込むアクセス許可が付与されます。

### <a name="generate-a-shared-access-signature-uri-for-a-blob"></a>BLOB の共有アクセス署名 URI の生成
次に、同様のコードを作成して、新しい BLOB をコンテナー内に作成し、その共有アクセス署名を生成します。 この共有アクセス署名は、保存されているアクセス ポリシーに関連付けられておらず、開始時刻、有効期限、およびアクセス許可情報が URI に含まれています。

新しい BLOB を作成してテキストを書き込んだ後に共有アクセス署名を生成し、署名 URI を返す新しいメソッドを追加します。

```csharp
static string GetBlobSasUri(CloudBlobContainer container)
{
    //Get a reference to a blob within the container.
    CloudBlockBlob blob = container.GetBlockBlobReference("sasblob.txt");

    //Upload text to the blob. If the blob does not yet exist, it will be created.
    //If the blob does exist, its existing content will be overwritten.
    string blobContent = "This blob will be accessible to clients via a Shared Access Signature.";
    MemoryStream ms = new MemoryStream(Encoding.UTF8.GetBytes(blobContent));
    ms.Position = 0;
    using (ms)
    {
        blob.UploadFromStream(ms);
    }

    //Set the expiry time and permissions for the blob.
    //In this case the start time is specified as a few minutes in the past, to mitigate clock skew.
    //The shared access signature will be valid immediately.
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
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

コンパイルし、実行して、新しい BLOB の共有アクセス署名 URI を出力します。 URI は、次の URI のようになります。

    https://storageaccount.blob.core.windows.net/sascontainer/sasblob.txt?sv=2012-02-12&st=2013-04-12T23%3A37%3A08Z&se=2013-04-13T00%3A12%3A08Z&sr=b&sp=rw&sig=dF2064yHtc8RusQLvkQFPItYdeOz3zR8zHsDMBi4S30%3D

### <a name="create-a-stored-access-policy-on-the-container"></a>保存されているアクセス ポリシーのコンテナーでの作成
今度は、保存されているアクセス ポリシーをコンテナーで作成します。これで、関連付けられているすべての共有アクセス署名の制約が定義されます。

前の例で、共有アクセス署名 URI 自体の開始時刻 (暗黙の、または明示的な)、有効期限、およびアクセス許可を指定しました。 次の例では、これらを、共有アクセス署名ではなく、保存されているアクセス ポリシーで指定します。 こうすると、共有アクセス署名を再発行せずに、これらの制約を変更できます。

共有アクセス署名に&1; つ以上の制約を指定し、保存されているアクセス ポリシーに残りの制約を指定することができます。 ただし、開始時刻、有効期限、およびアクセス許可はどちらか一方にだけ指定できます。たとえば、アクセス許可を共有アクセス署名に指定し、保存されているアクセス ポリシーにも指定することはできません。

アクセス ポリシーをコンテナーに追加するときは、コンテナーの既存のアクセス許可を取得し、新しいアクセス ポリシーを追加した後、コンテナーのアクセス許可を設定する必要があります。

コンテナーに保存される新しいアクセス ポリシーを作成し、そのポリシーの名前を返す新しいメソッドを、次のように追加します。

```csharp
static void CreateSharedAccessPolicy(CloudBlobClient blobClient, CloudBlobContainer container,
    string policyName)
{
    //Create a new shared access policy and define its constraints.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
    };

    //Get the container's existing permissions.
    BlobContainerPermissions permissions = container.GetPermissions();

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

### <a name="generate-a-shared-access-signature-uri-on-the-container-that-uses-an-access-policy"></a>アクセス ポリシーを使用するコンテナーでの共有アクセス署名 URI の生成
次に、先に作成したコンテナーで別の共有アクセス署名を作成しますが、今度は、前の例で作成したアクセス ポリシーに署名を関連付けます。

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
最後に、同様のメソッドを追加することで、別の BLOB を作成し、アクセス ポリシーに関連付けられた共有アクセス署名を生成します。

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

**Main()** メソッドは、全体として、次のようになります。 これを実行して、共有アクセス署名 URI をコンソール ウィンドウに書き込み、それをコピーしてテキスト ファイルに貼り付けます。これは、このチュートリアルの第&2; 部で使用します。

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

GenerateSharedAccessSignatures コンソール アプリケーションを実行すると、コンソール ウィンドウに次のような出力が表示されます。 これが、チュートリアルの第 2 部で使用する共有アクセス署名です。

![sas-console-output-1][sas-console-output-1]

## <a name="part-2-create-a-console-application-to-test-the-shared-access-signatures"></a>第 2 部 : コンソール アプリケーションの作成と共有アクセス署名のテスト
前の例で作成した共有アクセス署名をテストするために、署名を使用して、コンテナーと BLOB で操作を実行する、もう&1; つのコンソール アプリケーションを作成します。

> [!NOTE]
> チュートリアルの第 1 部の完了後、24 時間を超える時間が経過している場合、生成した署名は無効になっています。 その場合は、最初のコンソール アプリケーションでコードを実行して、チュートリアルの第&2; 部で使用するための新しい共有アクセス署名を生成する必要があります。
>
>

Visual Studio で、新しい Windows コンソール アプリケーションを作成し、 **ConsumeSharedAccessSignatures**という名前を付けます。 前に追加した場合と同様に、**Microsoft.WindowsAzure.Configuration.dll** および **Microsoft.WindowsAzure.Storage.dll** への参照を追加します。

Program.cs ファイルの先頭に、次の **using** ステートメントを追加します。

```csharp
using System.IO;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
```

**Main()** メソッドの本文で、次の定数を追加し、その値を、チュートリアルのパート 1 で生成した共有アクセス署名に更新します。

```csharp
static void Main(string[] args)
{
    string containerSAS = "<your container SAS>";
    string blobSAS = "<your blob SAS>";
    string containerSASWithAccessPolicy = "<your container SAS with access policy>";
    string blobSASWithAccessPolicy = "<your blob SAS with access policy>";
}
```

### <a name="add-a-method-to-try-container-operations-using-a-shared-access-signature"></a>メソッドの追加と、共有アクセス署名を使用するコンテナー操作の実行
次に、コンテナーで共有アクセス署名を使用する代表的なコンテナー操作をいくつかテストするメソッドを追加します。 共有アクセス署名は、署名だけに基づいてコンテナーへのアクセスを認証し、コンテナーに参照を返すために使用されることに注意してください。

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


### <a name="add-a-method-to-try-blob-operations-using-a-shared-access-signature"></a>メソッドの追加と、共有アクセス署名を使用する BLOB 操作の実行
最後に、BLOB で共有アクセス署名を使用する代表的な BLOB 操作をいくつかテストするメソッドを追加します。 ここでは、コンストラクター **CloudBlockBlob(String)**を使用して共有アクセス署名で渡し、BLOB に参照を返します。 他の認証は不要で、署名だけに基づいて認証されます。

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

![sas-console-output-2][sas-console-output-2]

## <a name="next-steps"></a>次のステップ
[共有アクセス署名、パート 1: SAS モデルについて](storage-dotnet-shared-access-signature-part-1.md)

[コンテナーと BLOB への匿名読み取りアクセスを管理する](storage-manage-access-to-resources.md)

[共有アクセス署名を使用したアクセスの委任 (REST API) に関するページ](http://msdn.microsoft.com/library/azure/ee395415.aspx)

[テーブルおよびキュー SAS についての MSDN ブログ](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-table-sas-shared-access-signature-queue-sas-and-update-to-blob-sas.aspx)

[sas-console-output-1]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-1.PNG
[sas-console-output-2]: ./media/storage-dotnet-shared-access-signature-part-2/sas-console-output-2.PNG



<!--HONumber=Dec16_HO2-->


