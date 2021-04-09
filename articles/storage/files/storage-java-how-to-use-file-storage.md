---
title: Java での Azure Files 用の開発 | Microsoft Docs
description: Azure Files を使ってファイル データを格納する Java アプリケーションとサービスを開発する方法を説明します。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2020
ms.custom: devx-track-java
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 25baa278961b93b04e60f2e997b98753cb6cf3ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95024111"
---
# <a name="develop-for-azure-files-with-java"></a>Java での Azure Files 用の開発

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

データを格納するために Azure Files を使用する Java アプリケーションの開発の基本について説明します。 コンソール アプリケーションを作成し、Azure Files API を使用して基本的な操作を学習します。

- Azure ファイル共有を作成および削除する
- ディレクトリを作成および削除する
- Azure ファイル共有のファイルとディレクトリを列挙する
- ファイルのアップロード、ダウンロード、および削除

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="create-a-java-application"></a>Java アプリケーションの作成

サンプルを作成するには、Java Development Kit (JDK) と [Azure Storage SDK for Java](https://github.com/azure/azure-sdk-for-java) が必要です。 また、Azure ストレージ アカウントを作成しておく必要があります。

## <a name="set-up-your-application-to-use-azure-files"></a>Azure Files を使用するようにアプリケーションを設定する

Azure Files API を使用するには、Azure Files のアクセス元にする Java ファイルの一番上に次のコードを追加します。

# <a name="java-v12"></a>[Java v12](#tab/java)

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ImportStatements":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

```java
// Include the following imports to use Azure Files APIs v11
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

---

## <a name="set-up-an-azure-storage-connection-string"></a>Azure Storage 接続文字列の設定

Azure Files を使うには、Azure ストレージ アカウントに接続する必要があります。 接続文字列を構成し、それを使用してストレージ アカウントに接続します。 接続文字列を保持する静的変数を定義します。

# <a name="java-v12"></a>[Java v12](#tab/java)

*\<storage_account_name\>* と *\<storage_account_key\>* をストレージ アカウントの実際の値に置き換えます。

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_ConnectionString":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

*your_storage_account_name* と *your_storage_account_key* を自分のストレージ アカウントの実際の値に換えます。

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

---

## <a name="access-azure-files-storage"></a>Azure Files ストレージへのアクセス

# <a name="java-v12"></a>[Java v12](#tab/java)

Azure Files にアクセスするため、[ShareClient](/java/api/com.azure.storage.file.share.shareclient) オブジェクトを作成します。 [ShareClientBuilder](/java/api/com.azure.storage.file.share.shareclientbuilder) クラスを使用して、新しい **ShareClient** オブジェクトを作成します。

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createClient":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

ストレージ アカウントにアクセスするには、**CloudStorageAccount** オブジェクトを使用し、接続文字列をその **parse** メソッドに渡します。

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse** は InvalidKeyException を投げます。そのため、try/catch ブロック内にそれを配置する必要があります。

---

## <a name="create-a-file-share"></a>ファイル共有を作成する

Azure Files のすべてのファイルとディレクトリは共有という名前のコンテナーにあります。

# <a name="java-v12"></a>[Java v12](#tab/java)

共有が既に存在する場合は、[ShareClient.create](/java/api/com.azure.storage.file.share.shareclient.create) メソッドによって例外がスローされます。 `try/catch` ブロックの **create** に呼び出しを配置して例外を処理します。

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createFileShare":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

共有とそのコンテンツにアクセスするには、Azure Files クライアントを作成します。

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Azure Files クライアントを使用すると、共有への参照を取得できます。

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

共有を実際に作成するには、**CloudFileShare** オブジェクトの **createIfNotExists** メソッドを使用します。

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

この時点で、**share** は「**sampleshare**」という名前の共有の参照を保持します。

---

## <a name="delete-a-file-share"></a>ファイル共有の削除

次のサンプル コードは、ファイル共有を削除します。

# <a name="java-v12"></a>[Java v12](#tab/java)

[ShareClient.delete](/java/api/com.azure.storage.file.share.shareclient.delete) メソッドを呼び出して共有を削除します。

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFileShare":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

**CloudFileShare** オブジェクトで **deleteIfExists** メソッドを呼び出して共有を削除します。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the file client.
   CloudFileClient fileClient = storageAccount.createCloudFileClient();

   // Get a reference to the file share
   CloudFileShare share = fileClient.getShareReference("sampleshare");

   if (share.deleteIfExists()) {
       System.out.println("sampleshare deleted");
   }
} catch (Exception e) {
    e.printStackTrace();
}
```

---

## <a name="create-a-directory"></a>ディレクトリを作成する

ルート ディレクトリにすべてのファイルを置くのではなく、サブディレクトリ内に置くことで、ストレージを整理することができます。

# <a name="java-v12"></a>[Java v12](#tab/java)

次のコードは、[ShareDirectoryClient.create](/java/api/com.azure.storage.file.share.sharedirectoryclient.create) を呼び出してディレクトリを作成します。 このメソッド例は、ディレクトリが正常に作成されたかどうかを示す `Boolean` 値を返します。

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_createDirectory":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

次のコードは、ルート ディレクトリの下に **sampledir** という名前のサブディレクトリを作成します。

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the sampledir directory
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

if (sampleDir.createIfNotExists()) {
    System.out.println("sampledir created");
} else {
    System.out.println("sampledir already exists");
}
```

---

## <a name="delete-a-directory"></a>ディレクトリを削除する

ディレクトリの削除は簡単な操作です。 ファイルまたはサブディレクトリが残っているディレクトリは削除できません。

# <a name="java-v12"></a>[Java v12](#tab/java)

ディレクトリがない、またはディレクトリが空の場合、[ShareDirectoryClient.delete](/java/api/com.azure.storage.file.share.sharedirectoryclient.delete) メソッドは例外をスローします。 `try/catch` ブロックの **delete** に呼び出しを配置して例外を処理します。

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteDirectory":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory you want to delete
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

// Delete the directory
if ( containerDir.deleteIfExists() ) {
    System.out.println("Directory deleted");
}
```

---

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Azure ファイル共有のファイルとディレクトリを列挙する

# <a name="java-v12"></a>[Java v12](#tab/java)

[ShareDirectoryClient.listFilesAndDirectories](/java/api/com.azure.storage.file.share.sharedirectoryclient.listfilesanddirectories) を呼び出して、ファイルとディレクトリの一覧を取得します。 このメソッドは、繰り返しできる [ShareFileItem](/java/api/com.azure.storage.file.share.models.sharefileitem) オブジェクトの一覧を返します。 次のコードは、*dirName* パラメーターで指定されたディレクトリ内のファイルとディレクトリを一覧表示します。

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_enumerateFilesAndDirs":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

**CloudFileDirectory** 参照で **listFilesAndDirectories** を呼び出すと、ファイルとディレクトリの一覧を取得できます。 このメソッドは、繰り返しできる **ListFileItem** オブジェクトの一覧を返します。 次のコードは、ルート ディレクトリ内のファイルとディレクトリを一覧表示します。

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

---

## <a name="upload-a-file"></a>ファイルをアップロードする

ローカル ストレージからファイルをアップロードする方法を説明します。

# <a name="java-v12"></a>[Java v12](#tab/java)

次のコードは、[ShareFileClient.uploadFromFile](/java/api/com.azure.storage.file.share.sharefileclient.uploadfromfile) メソッドを呼び出して、ローカル ファイルを Azure File Storage にアップロードします。 次のメソッド例は、指定したファイルが正常にアップロードされたかどうかを示す `Boolean` 値を返します。

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_uploadFile":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

共有オブジェクトで **getRootDirectoryReference** メソッドを呼び出して、ファイルがアップロードされるディレクトリへの参照を取得します。

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

共有のルート ディレクトリの参照が与えられたので、次のコードを利用してディレクトリにファイルをアップロードできます。

```java
// Define the path to a local file.
final String filePath = "C:\\temp\\Readme.txt";

CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
cloudFile.uploadFromFile(filePath);
```

---

## <a name="download-a-file"></a>ファイルをダウンロードする

頻繁に実行する操作の 1 つに、Azure Files ストレージからのファイルのダウンロードがあります。

# <a name="java-v12"></a>[Java v12](#tab/java)

次の例は、指定されたファイルを、*destDir* パラメーターで指定したローカル ディレクトリにダウンロードします。 このメソッド例は、ダウンロードしたファイルの名前を、日付と時刻の前に付けることで一意の名前にします。

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_downloadFile":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

次の例は、SampleFile.txt をダウンロードし、そのコンテンツを表示します。

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the directory that contains the file
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

//Get a reference to the file you want to download
CloudFile file = sampleDir.getFileReference("SampleFile.txt");

//Write the contents of the file to the console.
System.out.println(file.downloadText());
```

---

## <a name="delete-a-file"></a>ファイルを削除する

Azure Files でのもう 1 つの一般的な操作はファイルの削除です。

# <a name="java-v12"></a>[Java v12](#tab/java)

次のコードは、指定したファイルを削除します。 この例はまず、*dirName* パラメーターに基づいて [ShareDirectoryClient](/java/api/com.azure.storage.file.share.sharedirectoryclient) を作成します。 次にこのコードは、*fileName* パラメーターに基づいて [ShareFileClient](/java/api/com.azure.storage.file.share.sharefileclient) をディレクトリ クライアントから取得します。 最後に、このメソッド例は、[ShareFileClient.delete](/java/api/com.azure.storage.file.share.sharefileclient.delete) を呼び出してファイルを削除します。

:::code language="java" source="~/azure-storage-snippets/files/howto/java/java-v12/files-howto-v12/src/main/java/com/files/howto/App.java" id="Snippet_deleteFile":::

# <a name="java-v11"></a>[Java v11](#tab/java11)

次のコードは「 **sampledir**」という名前のディレクトリ内に保存されている「SampleFile.txt」という名前のファイルを削除します。

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory where the file to be deleted is in
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

String filename = "SampleFile.txt"
CloudFile file;

file = containerDir.getFileReference(filename)
if ( file.deleteIfExists() ) {
    System.out.println(filename + " was deleted");
}
```

---

## <a name="next-steps"></a>次のステップ

その他の Azure ストレージ API に関する詳細については、次のリンク先をご覧ください。

- [Java 開発者向けの Azure](/azure/developer/java)
- [Azure SDK for Java](https://github.com/azure/azure-sdk-for-java)
- [Azure SDK for Android](https://github.com/azure/azure-sdk-for-android)
- [Java SDK 用 Azure File Share クライアント ライブラリ リファレンス](/java/api/overview/azure/storage-file-share-readme)
- [Azure Storage Services REST API (Azure Storage サービスの REST API)](/rest/api/storageservices/)
- [Azure のストレージ チーム ブログ](https://azure.microsoft.com/blog/topics/storage-backup-and-recovery/)
- [AzCopy コマンド ライン ユーティリティを使用してデータを転送する](../common/storage-use-azcopy-v10.md)
- [Azure Files に関する問題のトラブルシューティング - Windows](storage-troubleshoot-windows-file-connection-problems.md)
