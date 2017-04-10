---
title: "Java から File Storage を使用する方法 | Microsoft Docs"
description: "Azure ファイル サービスを使用して、ファイルをアップロード、ダウンロード、一覧表示、削除する方法について説明します。 コード サンプルは Java で記述されています。"
services: storage
documentationcenter: java
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 3bfbfa7f-d378-4fb4-8df3-e0b6fcea5b27
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 12/08/2016
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 60d4bcafa51a6eedea06901a0557d13b4e85ec7a


---
# <a name="how-to-use-file-storage-from-java"></a>Java からファイル ストレージを使用する方法
[!INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../includes/storage-check-out-samples-java.md)]

## <a name="overview"></a>Overview
このガイドでは、Microsoft Azure ファイル ストレージ サービスで基本的な操作を実行する方法を紹介します。 Java で記述されたサンプルを利用し、共有とディレクトリを作成し、ファイルをアップロード、一覧表示、削除する方法を紹介します。 Microsoft Azure のファイル ストレージ サービスを初めて利用する場合、各セクションのコンセプトをお読みください。サンプルを理解する上で非常に役立ちます。

[!INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Java アプリケーションの作成
サンプルを作成するには、Java Development Kit (JDK) と [Azure Storage SDK for Java][] が必要です。 また、Azure ストレージ アカウントを作成しておく必要があります。

## <a name="setup-your-application-to-use-file-storage"></a>ファイル ストレージを使用するようにアプリケーションを設定します。
Azure ストレージ API を使用するには、ストレージ サービスのアクセス元にする Java ファイルの一番上に次の文を追加します。

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

## <a name="setup-an-azure-storage-connection-string"></a>Azure のストレージ接続文字列の設定
ファイル ストレージを使用するには、Azure ストレージ アカウントに接続する必要があります。 最初の手順はストレージ アカウントに接続するために使用する接続文字列を構成することです。 そのために静的変数を定義しましょう。

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

## <a name="how-to-create-a-share"></a>共有を作成する方法
ファイル ストレージのすべてのファイルとディレクトリは「 **Share**」という名前のコンテナーにあります。 ストレージ アカウントには、アカウントの容量が許す限りの共有を置くことができます。 共有とそのコンテンツへのアクセスを取得するには、ファイル ストレージ クライアントを使用する必要があります。

```java
// Create the file storage client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

ファイル ストレージ クライアントを使用し、共有への参照を取得できます。

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

## <a name="how-to-upload-a-file"></a>ファイルをアップロードする方法
Azure ファイル ストレージ共有には、少なくとも、ファイルが置かれるルート ディレクトリが含まれます。 このセクションでは、ローカル ストレージから共有のルート ディレクトリにファイルをアップロードする方法を紹介します。

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

## <a name="how-to-create-a-directory"></a>ディレクトリを作成する方法
ルート ディレクトリにすべてのファイルを置くのではなく、サブディレクトリ内に置いてストレージを整理することもできます。 Azure ファイル ストレージ サービスでは、自分のアカウントで許可されるだけのディレクトリを作成できます。 下のコードはルート ディレクトリの下に「 **sampledir** 」という名前のサブディレクトリを作成します。

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

## <a name="how-to-list-files-and-directories-in-a-share"></a>共有のファイルとディレクトリを一覧表示する方法
CloudFileDirectory 参照で **listFilesAndDirectories** を呼び出すと、共有内のファイルとディレクトリの一覧を簡単に取得できます。 このメソッドは繰り返しできる ListFileItem オブジェクトの一覧を返します。 たとえば、次のコードはルート ディレクトリ内のファイルとディレクトリを一覧表示します。

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

## <a name="how-to-download-a-file"></a>ファイルをダウンロードする方法
ファイル ストレージに頻繁に実行する操作はファイルのダウンロードです。 次の例のコードは SampleFile.txt をダウンロードし、そのコンテンツを表示します。

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

## <a name="how-to-delete-a-file"></a>ファイルを削除する方法
もう 1 つの一般的なファイル ストレージ操作はファイル削除です。 次のコードは「 **sampledir**」という名前のディレクトリ内に保存されている「SampleFile.txt」という名前のファイルを削除します。

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

## <a name="how-to-delete-a-directory"></a>ディレクトリを削除する方法
ディレクトリの削除はかなり単純な作業です。ただし、注意するべきことは、ファイルや他のディレクトリが含まれるディレクトリは削除できないということです。

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

## <a name="how-to-delete-a-share"></a>共有を削除する方法
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

## <a name="next-steps"></a>次のステップ
その他の Azure ストレージ API に関する詳細については、次のリンク先をご覧ください。

* [Java デベロッパー センター](http://azure.microsoft.com/develop/java/)
* [Azure Storage SDK for Java](https://github.com/azure/azure-storage-java)
* [Azure Storage SDK for Android に関するページ に関するページ](https://github.com/azure/azure-storage-android)
* [Azure ストレージ クライアント SDK リファレンス](http://dl.windowsazure.com/storage/javadoc/)
* [Azure Storage Services REST API (Azure Storage サービスの REST API)](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Azure Storage チーム ブログ](http://blogs.msdn.com/b/windowsazurestorage/)
* [AzCopy コマンド ライン ユーティリティを使用してデータを転送する](storage-use-azcopy.md)




<!--HONumber=Nov16_HO3-->


