---
title: Java での Azure Files 用の開発 | Microsoft Docs
description: Azure Files を使ってファイル データを格納する Java アプリケーションとサービスを開発する方法を説明します。
services: storage
author: wmgries
ms.service: storage
ms.devlang: Java
ms.topic: article
ms.date: 09/19/2017
ms.author: renash
ms.subservice: files
ms.openlocfilehash: 62a771b831c88f73df91c50f34ba6de0d95b832e
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55453095"
---
# <a name="develop-for-azure-files-with-java"></a>Java での Azure Files 用の開発
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="about-this-tutorial"></a>このチュートリアルについて
このチュートリアルでは、ファイル データの格納に Azure Files を使うアプリケーションまたはサービスを開発するための Java の基本的な使い方を示します。 このチュートリアルでは、コンソール アプリケーションを作成し、Java と Azure Files による次のような基本的な操作の実行方法を示します。

* Azure ファイル共有を作成および削除する
* ディレクトリを作成および削除する
* Azure ファイル共有のファイルとディレクトリを列挙する
* ファイルのアップロード、ダウンロード、および削除

> [!Note]  
> Azure Files は SMB 経由でアクセスできるため、ファイル I/O の標準 Java I/O クラスを使って Azure ファイル共有にアクセスするアプリケーションを作成できます。 この記事では、Azure Storage Java SDK を使うアプリケーションを記述する方法を説明します。この SDK は、Azure Files との通信に [Azure Files REST API](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) を使います。

## <a name="create-a-java-application"></a>Java アプリケーションの作成
サンプルを作成するには、Java Development Kit (JDK) と [Azure Storage SDK for Java](https://github.com/Azure/azure-storage-java) が必要です。 また、Azure ストレージ アカウントを作成しておく必要があります。

## <a name="set-up-your-application-to-use-azure-files"></a>Azure Files を使用するようにアプリケーションを設定する
Azure ストレージ API を使用するには、ストレージ サービスのアクセス元にする Java ファイルの一番上に次の文を追加します。

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Azure Storage 接続文字列の設定
Azure Files を使うには、Azure ストレージ アカウントに接続する必要があります。 最初の手順は、ストレージ アカウントに接続するために使用する接続文字列を構成することです。 そのために静的変数を定義しましょう。

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

> [!NOTE]
> your_storage_account_name と your_storage_account_key を自分のストレージ アカウントの実際の値に換えます。
> 
> 

## <a name="connecting-to-an-azure-storage-account"></a>Azure ストレージ アカウントに接続する
ストレージ アカウントに接続するには、**CloudStorageAccount** オブジェクトを使用し、接続文字列をその **parse** メソッドに渡す必要があります。

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse** は InvalidKeyException を投げます。そのため、try/catch ブロック内にそれを配置する必要があります。

## <a name="create-an-azure-file-share"></a>Azure ファイル共有を作成する
Azure Files のすべてのファイルとディレクトリは **Share** という名前のコンテナーにあります。 ストレージ アカウントには、アカウントの容量が許す限りの共有を置くことができます。 共有とそのコンテンツにアクセスするには、Azure Files クライアントを使う必要があります。

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Azure Files クライアントを使用すると、共有への参照を取得できます。

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

共有を実際に作成するには、CloudFileShare オブジェクトの **createIfNotExists** メソッドを使用します。

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

この時点で、**share** は「**sampleshare**」という名前の共有の参照を保持します。

## <a name="delete-an-azure-file-share"></a>Azure ファイル共有を削除する
CloudFileShare オブジェクトで **deleteIfExists** メソッドを呼び出すと共有が削除されます。 次がそのサンプル コードです。

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

## <a name="create-a-directory"></a>ディレクトリを作成する
ルート ディレクトリにすべてのファイルを置くのではなく、サブディレクトリ内に置いてストレージを整理することもできます。 Azure Files では、自分のアカウントで許可されるだけのディレクトリを作成できます。 下のコードはルート ディレクトリの下に **sampledir** という名前のサブディレクトリを作成します。

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

## <a name="delete-a-directory"></a>ディレクトリを削除する
ディレクトリの削除は単純な作業です。ただし、注意するべきことは、ファイルや他のディレクトリが含まれるディレクトリは削除できないということです。

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

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Azure ファイル共有のファイルとディレクトリを列挙する
CloudFileDirectory 参照で **listFilesAndDirectories** を呼び出すと、共有内のファイルとディレクトリの一覧を簡単に取得できます。 このメソッドは繰り返しできる ListFileItem オブジェクトの一覧を返します。 たとえば、次のコードはルート ディレクトリ内のファイルとディレクトリを一覧表示します。

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

## <a name="upload-a-file"></a>ファイルをアップロードする
このセクションでは、ローカル ストレージから共有のルート ディレクトリにファイルをアップロードする方法を紹介します。

ファイルをアップロードするための最初の手順は、ファイルを置くディレクトリの参照を取得することです。 これを行うには、share オブジェクトの **getRootDirectoryReference** メソッドを呼び出します。

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

## <a name="download-a-file"></a>ファイルをダウンロードする
Azure Files に頻繁に実行する操作はファイルのダウンロードです。 次の例のコードは SampleFile.txt をダウンロードし、そのコンテンツを表示します。

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

## <a name="delete-a-file"></a>ファイルを削除する
Azure Files でのもう 1 つの一般的な操作はファイルの削除です。 次のコードは「 **sampledir**」という名前のディレクトリ内に保存されている「SampleFile.txt」という名前のファイルを削除します。

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

## <a name="next-steps"></a>次の手順
その他の Azure ストレージ API に関する詳細については、次のリンク先をご覧ください。

* [Java 開発者向けの Azure](/java/azure)/)
* [Azure Storage SDK for Java](https://github.com/azure/azure-storage-java)
* [Azure Storage SDK for Android に関するページ に関するページ](https://github.com/azure/azure-storage-android)
* [Azure ストレージ クライアント SDK リファレンス](http://dl.windowsazure.com/storage/javadoc/)
* [Azure Storage Services REST API (Azure Storage サービスの REST API)](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Azure のストレージ チーム ブログ](https://blogs.msdn.com/b/windowsazurestorage/)
* [AzCopy コマンド ライン ユーティリティを使用してデータを転送する](../common/storage-use-azcopy.md)
* [Azure Files に関する問題のトラブルシューティング - Windows](storage-troubleshoot-windows-file-connection-problems.md)
