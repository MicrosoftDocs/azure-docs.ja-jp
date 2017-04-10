---
title: "Blob Storage を使用するオンプレミスのアプリケーション (Java) | Microsoft Docs"
description: "画像を Azure にアップロードしてブラウザーに表示するコンソール アプリケーションを作成する方法について説明します。 コード サンプルは Java で記述されています。"
services: storage
documentationcenter: java
author: mmacy
manager: carmonm
editor: tysonn
ms.assetid: ccc9a7d7-6fe4-467b-b7fd-a73f17539e3f
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/17/2016
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 27d088291d93ce936a9b2465a13a47e62270fb84


---
# <a name="on-premises-application-with-blob-storage"></a>BLOB ストレージを使用するオンプレミスのアプリケーション
## <a name="overview"></a>Overview
次の例では、Azure Storage を使用して画像を Azure に保存する方法を示しています。 この記事では、画像を Azure にアップロードし、ブラウザーに画像を表示する HTML ファイルを作成するコンソール アプリケーションのコードを紹介します。

## <a name="prerequisites"></a>前提条件
* Java Developer Kit (JDK) バージョン 1.6 以降がインストールされていること。
* Azure SDK がインストールされていること。
* Azure Libraries for Java の JAR および該当する依存関係 JAR がインストールされ、Java コンパイラで使用されるビルド パスに存在すること。 Azure Libraries for Java のインストールについては、「 [Azure SDK for Java のダウンロード](../java-download-azure-sdk.md)」のページをご覧ください。
* Azure ストレージ アカウントがセットアップされていること。 この記事のコードでは、ストレージ アカウントのアカウント名とアカウント キーが使用されます。 ストレージ アカウントの作成については、「[方法: ストレージ アカウントを作成する](storage-create-storage-account.md#create-a-storage-account)」をご覧ください。アカウント キーの取得については、[ストレージ アクセス キーの表示およびコピーに関するページ](storage-create-storage-account.md#view-and-copy-storage-access-keys)をご覧ください。
* ローカル画像ファイルが作成され、c:\\myimages\\image1.jpg に保存されていること。 または、例に含まれている   **FileInputStream** コンストラクターを変更して、別の画像パスとファイル名を使うこともできます。

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="to-use-azure-blob-storage-to-upload-a-file"></a>Azure BLOB ストレージを使用してファイルをアップロードするには
ここでは、手順を追って説明します。 スキップする場合、この記事の後半でコード全体が確認できます。

コードの先頭には、Azure コア ストレージ クラス、Azure BLOB クライアント クラス、Java IO クラス、 **URISyntaxException** クラスの import を含めます。

```java
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.blob.*;
import java.io.*;
import java.net.URISyntaxException;
```

**StorageSample** という名前のクラスを宣言し、左ブラケット **{** を含めます。

```java
public class StorageSample {
```

**StorageSample** クラス内で、Azure ストレージ アカウントで指定されている既定のエンドポイント プロトコル、ストレージ アカウント名、およびストレージ アクセス キーを格納する文字列変数を宣言します。 プレースホルダーの値 **your\_account\_name** と **your\_account\_key** は、自分のアカウント名とアカウント キーにそれぞれ置き換えてください。

```java
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_account_name;" +
    "AccountKey=your_account_name";
```

**main** の宣言を追加し、**try** ブロックと、必要な左ブラケット **{** を含めます。

```java
    public static void main(String[] args)
    {
        try
        {
```

次の型の変数を宣言します (説明は、この例での使用に関するものです)。

* **CloudStorageAccount**: Azure ストレージ アカウント名とキーを使用してアカウント オブジェクトを初期化し、BLOB クライアント オブジェクトを作成するために使用します。
* **CloudBlobClient**: BLOB サービスにアクセスするために使用します。
* **CloudBlobContainer**: BLOB コンテナーの作成、コンテナー内の BLOB のリスト処理、およびコンテナーの削除を行うために使用します。
* **CloudBlockBlob**: ローカルの画像ファイルをコンテナーにアップロードするために使用します。

<!-- -->

```java
    CloudStorageAccount account;
    CloudBlobClient serviceClient;
    CloudBlobContainer container;
    CloudBlockBlob blob;
```

**account** 変数に値を割り当てます。

```java
account = CloudStorageAccount.parse(storageConnectionString);
```

**serviceClient** 変数に値を割り当てます。

```java
serviceClient = account.createCloudBlobClient();
```

**container** 変数に値を割り当てます。 **gettingstarted**という名前のコンテナーへの参照を取得します。

```java
// Container name must be lower case.
container = serviceClient.getContainerReference("gettingstarted");
```

コンテナーを作成します。 コンテナーが存在しない場合、このメソッドはコンテナーを作成し、 **true**を返します。 コンテナーが存在する場合は、 **false**を返します。 **createIfNotExist** の代わりに、**create** メソッドを使うこともできます (ただし、コンテナーが既に存在する場合はエラーが返されます)。

```java
container.createIfNotExists();
```

コンテナーへの匿名アクセスを設定します。

```java
// Set anonymous access on the container.
BlobContainerPermissions containerPermissions;
containerPermissions = new BlobContainerPermissions();
containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
container.uploadPermissions(containerPermissions);
```

ブロック BLOB への参照を取得します。これは Azure Storage 内の BLOB を表します。

```java
blob = container.getBlockBlobReference("image1.jpg");
```

**File** コンストラクターを使って、アップロードするローカルで作成したファイルへの参照を取得します。 このファイルは、コードを実行する前に作成しておいてください。

```java
File fileReference = new File ("c:\\myimages\\image1.jpg");
```

**CloudBlockBlob.upload** メソッドの呼び出しによってローカル ファイルをアップロードします。 **CloudBlockBlob.upload** メソッドの 1 つ目のパラメーターは、Azure Storage にアップロードするローカル ファイルを表す **FileInputStream** オブジェクトです。 2 つ目のパラメーターは、ファイルのサイズ (バイト単位) です。

```java
blob.upload(new FileInputStream(fileReference), fileReference.length());
```

**MakeHTMLPage** という名前のヘルパー関数を呼び出して、**&lt;image&gt;** 要素が 1 つ含まれる基本的な HTML ページを作成します。ソースは、Azure Storage アカウントにある BLOB に設定されています。 **MakeHTMLPage** のコードについては、この記事で後述します。

```java
MakeHTMLPage(container);
```

ステータス メッセージと、作成された HTML ページに関する情報を出力します。

```java
System.out.println("Processing complete.");
System.out.println("Open index.html to see the images stored in your storage account.");
```

右ブラケット **}** を挿入して **try** ブロックを閉じます。

次の例外を処理します。

* **FileNotFoundException**: **FileInputStream** コンストラクターまたは **FileOutputStream** コンストラクターからスローされる可能性があります。
* **StorageException**: Azure クライアント ストレージ ライブラリからスローされる可能性があります。
* **URISyntaxException**: **ListBlobItem.getUri** メソッドからスローされる可能性があります。
* **Exception**: 汎用的な例外処理です。

<!-- -->

```java
catch (FileNotFoundException fileNotFoundException)
{
    System.out.print("FileNotFoundException encountered: ");
    System.out.println(fileNotFoundException.getMessage());
    System.exit(-1);
}
catch (StorageException storageException)
{
    System.out.print("StorageException encountered: ");
    System.out.println(storageException.getMessage());
    System.exit(-1);
}
catch (URISyntaxException uriSyntaxException)
{
    System.out.print("URISyntaxException encountered: ");
    System.out.println(uriSyntaxException.getMessage());
    System.exit(-1);
}
catch (Exception e)
{
    System.out.print("Exception encountered: ");
    System.out.println(e.getMessage());
    System.exit(-1);
}
```

右ブラケット **}** を挿入して **main** を閉じます。

基本的な HTML ページを作成する **MakeHTMLPage** という名前のメソッドを作成します。 このメソッドには **CloudBlobContainer**型のパラメーターがあり、アップロードされた BLOB のリストを反復処理するために使用されます。 このメソッドは、**FileOutputStream** コンストラクターからスローされる可能性のある **FileNotFoundException** 型の例外と、**ListBlobItem.getUri** メソッドからスローされる可能性のある **URISyntaxException** 型の例外をスローします。 左ブラケット **{**を含めます。

```java
public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
{
```

**index.html**という名前のローカル ファイルを作成します。

```java
PrintStream stream;
stream = new PrintStream(new FileOutputStream("index.html"));
```

このローカル ファイルへの書き込みにより、**&lt;html&gt;**、**&lt;header&gt;**、および **&lt;body&gt;** の各要素を追加します。

```java
stream.println("<html>");
stream.println("<header/>");
stream.println("<body>");
```

アップロードされた BLOB のリストに対して反復処理を行います。 各 BLOB について、HTML ページ内に、Microsoft Azure ストレージ アカウント内に存在する BLOB の URI に送信された **src** 属性を持つ **&lt;img&gt;** 要素を作成します。
このサンプルでは画像を 1 つのみ追加しましたが、複数の画像を追加する場合、このコードですべての画像について反復処理を行います。

この例では、わかりやすくするために、アップロードされた各 BLOB が画像であると想定しています。 画像以外の BLOB や、ブロック blob ではなくページ blob をアップロードした場合は、必要に応じてコードを調整してください。

```java
// Enumerate the uploaded blobs.
for (ListBlobItem blobItem : container.listBlobs()) {
// List each blob as an <img> element in the HTML body.
stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
}
```

**&lt;body&gt;** 要素および **&lt;html&gt;** 要素を閉じます。

```java
stream.println("</body>");
stream.println("</html>");
```

ローカル ファイルを閉じます。

```java
stream.close();
```

右ブラケット **}** を挿入して **MakeHTMLPage** を閉じます。

右ブラケット **}** を挿入して **StorageSample** を閉じます。

この例の完全なコードを次に示します。 プレースホルダーの値 **your\_account\_name** と **your\_account\_key** を変更して、自分のアカウント名とアカウント キーをそれぞれ使ってください。

```java
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.blob.*;
import java.io.*;
import java.net.URISyntaxException;

// Create an image, c:\myimages\image1.jpg, prior to running this sample.
// Alternatively, change the value used by the FileInputStream constructor
// to use a different image path and file that you have already created.
public class StorageSample {

    public static final String storageConnectionString =
            "DefaultEndpointsProtocol=http;" +
                    "AccountName=your_account_name;" +
                    "AccountKey=your_account_name";

    public static void main(String[] args) {
        try {
            CloudStorageAccount account;
            CloudBlobClient serviceClient;
            CloudBlobContainer container;
            CloudBlockBlob blob;

            account = CloudStorageAccount.parse(storageConnectionString);
            serviceClient = account.createCloudBlobClient();
            // Container name must be lower case.
            container = serviceClient.getContainerReference("gettingstarted");
            container.createIfNotExists();

            // Set anonymous access on the container.
            BlobContainerPermissions containerPermissions;
            containerPermissions = new BlobContainerPermissions();
            containerPermissions.setPublicAccess(BlobContainerPublicAccessType.CONTAINER);
            container.uploadPermissions(containerPermissions);

            // Upload an image file.
            blob = container.getBlockBlobReference("image1.jpg");

            File fileReference = new File("c:\\myimages\\image1.jpg");
            blob.upload(new FileInputStream(fileReference), fileReference.length());

            // At this point the image is uploaded.
            // Next, create an HTML page that lists all of the uploaded images.
            MakeHTMLPage(container);

            System.out.println("Processing complete.");
            System.out.println("Open index.html to see the images stored in your storage account.");

        } catch (FileNotFoundException fileNotFoundException) {
            System.out.print("FileNotFoundException encountered: ");
            System.out.println(fileNotFoundException.getMessage());
            System.exit(-1);
        } catch (StorageException storageException) {
            System.out.print("StorageException encountered: ");
            System.out.println(storageException.getMessage());
            System.exit(-1);
        } catch (URISyntaxException uriSyntaxException) {
            System.out.print("URISyntaxException encountered: ");
            System.out.println(uriSyntaxException.getMessage());
            System.exit(-1);
        } catch (Exception e) {
            System.out.print("Exception encountered: ");
            System.out.println(e.getMessage());
            System.exit(-1);
        }
    }

    // Create an HTML page that can be used to display the uploaded images.
    // This example assumes all of the blobs are for images.
    public static void MakeHTMLPage(CloudBlobContainer container) throws FileNotFoundException, URISyntaxException
    {
        PrintStream stream;
        stream = new PrintStream(new FileOutputStream("index.html"));

        // Create the opening <html>, <header>, and <body> elements.
        stream.println("<html>");
        stream.println("<header/>");
        stream.println("<body>");

        // Enumerate the uploaded blobs.
        for (ListBlobItem blobItem : container.listBlobs()) {
            // List each blob as an <img> element in the HTML body.
            stream.println("<img src='" + blobItem.getUri() + "'/><br/>");
        }

        stream.println("</body>");

        // Complete the <html> element and close the file.
        stream.println("</html>");
        stream.close();
    }
}
```

例のコードでは、ローカルの画像ファイルを Azure Storage にアップロードするほか、index.html という名前のローカル ファイルも作成しています。このファイルをブラウザーで開くと、アップロードされた画像を表示できます。

コードにはアカウント名とアカウント キーが含まれるため、ソース コードは必ずセキュリティで保護してください。

## <a name="to-delete-a-container"></a>コンテナーを削除するには
ストレージには課金されるため、この例を試した後、 **gettingstarted** コンテナーを削除することをお勧めします。 コンテナーを削除するには、 **CloudBlobContainer.delete** メソッドを使用します。

```java
container = serviceClient.getContainerReference("gettingstarted");
container.delete();
```

**CloudBlobContainer.delete** メソッドを呼び出す場合も、**CloudStorageAccount**、**ClodBlobClient**、**CloudBlobContainer** の各オブジェクトを初期化するプロセスは、**createIfNotExist** メソッドの場合と同じです。 **gettingstarted**という名前のコンテナーを削除する例の完全なコードを次に示します。

```java
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.blob.*;

public class DeleteContainer {

    public static final String storageConnectionString =
            "DefaultEndpointsProtocol=http;" +
                "AccountName=your_account_name;" +
                "AccountKey=your_account_key";

    public static void main(String[] args)
    {
        try
        {
            CloudStorageAccount account;
            CloudBlobClient serviceClient;
            CloudBlobContainer container;

            account = CloudStorageAccount.parse(storageConnectionString);
            serviceClient = account.createCloudBlobClient();
            // Container name must be lower case.
            container = serviceClient.getContainerReference("gettingstarted");
            container.delete();

            System.out.println("Container deleted.");

        }
        catch (StorageException storageException)
        {
            System.out.print("StorageException encountered: ");
            System.out.println(storageException.getMessage());
            System.exit(-1);
        }
        catch (Exception e)
        {
            System.out.print("Exception encountered: ");
            System.out.println(e.getMessage());
            System.exit(-1);
        }
    }
}
```

他の BLOB ストレージ クラスとメソッドの概要については、「 [Java から BLOB ストレージを使用する方法](storage-java-how-to-use-blob-storage.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ
さらに複雑なストレージ タスクの詳細については、次のリンク先をご覧ください。

* [Azure Storage SDK for Java](https://github.com/azure/azure-storage-java)
* [Azure ストレージ クライアント SDK リファレンス](http://dl.windowsazure.com/storage/javadoc/)
* [Azure Storage Services REST API (Azure Storage サービスの REST API)](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Azure Storage チーム ブログ](http://blogs.msdn.com/b/windowsazurestorage/)




<!--HONumber=Nov16_HO3-->


