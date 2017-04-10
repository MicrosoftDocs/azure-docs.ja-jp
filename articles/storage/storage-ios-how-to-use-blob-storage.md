---
title: "iOS から Azure Blob Storage を使用する方法 | Microsoft Docs"
description: "Azure BLOB ストレージ (オブジェクト ストレージ) を使用して、非構造化データをクラウドに格納します。"
services: storage
documentationcenter: ios
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: df188021-86fc-4d31-a810-1b0e7bcd814b
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: objective-c
ms.topic: article
ms.date: 01/30/2017
ms.author: seguler
translationtype: Human Translation
ms.sourcegitcommit: 1bbb2cd3fae094305eb36e8d9958f9c94568e419
ms.openlocfilehash: c23139a755be08fbdee24f4fd87a0347252a7101


---
# <a name="how-to-use-blob-storage-from-ios"></a>iOS から BLOB ストレージを使用する方法
[!INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="overview"></a>概要
この記事では、Microsoft Azure BLOB ストレージを使用して一般的なシナリオを実行する方法について説明します。 サンプルは Objective-C で記述され、 [iOS 用 Azure Storage クライアント ライブラリ](https://github.com/Azure/azure-storage-ios)を使用しています。 紹介するシナリオは、BLOB の**アップロード**、**一覧の取得**、**ダウンロード**、および**削除**です。 BLOB の詳細については、「 [次のステップ](#next-steps) 」のセクションを参照してください。 また、 [サンプル アプリ](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) をダウンロードし、iOS アプリケーションでの Azure Storage の使用例をすぐに確認することもできます。

[!INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>Azure Storage iOS ライブラリをアプリケーションにインポートする
[Azure Storage CocoaPod](https://cocoapods.org/pods/AZSClient) を使用するか、または **フレームワーク** ファイルをインポートするかのいずれかにより、Azure Storage iOS ライブラリをアプリケーションにインポートすることができます。

## <a name="cocoapod"></a>CocoaPod
1. まだ実行していない場合は、ターミナル ウィンドウを開いて次のコマンドを実行し、コンピューターに [CocoaPods をインストール](https://guides.cocoapods.org/using/getting-started.html#toc_3) します。
   
    sudo gem install cocoapods

2. 次に、プロジェクト ディレクトリ (.xcodeproj ファイルが含まれるディレクトリ) に、_Podfile_ という新しいファイル (ファイル拡張子なし) を作成します。 _Podfile_ に次のコードを追加して保存します。
   
    pod 'AZSClient'

3. ターミナル ウィンドウでプロジェクト ディレクトリに移動し、次のコマンドを実行します。
   
    pod install

4. Xcode で .xcodeproj が開いている場合は閉じます。 プロジェクト ディレクトリで、拡張子が .xcworkspace の、新しく作成されたプロジェクト ファイルを開きます。 以降の作業にはこのファイルを使用します。

## <a name="framework"></a>フレームワーク
Azure Storage iOS ライブラリを使用するには、最初にフレームワーク ファイルを作成する必要があります。

1. まずは、 [azure-storage-ios リポジトリ](https://github.com/azure/azure-storage-ios)をダウンロードまたは複製します。
2. *azure-storage-ios* -> *Lib* -> *Azure Storage Client Library* の順に移動し、AZSClient.xcodeproj を Xcode で開きます。
3. Xcode の左上部分で、アクティブ スキームを "Azure Storage Client Library" から "Framework" に変更します。
4. プロジェクトをビルド (⌘ + B キー) します。 これによって、AZSClient.framework ファイルがデスクトップ上に作成されます。

次に、以下の手順を実行すると、フレームワーク ファイルをアプリケーションにインポートできます。

1. Xcode で新しいプロジェクトを作成するか、既存のプロジェクトを開きます。
2. 左側のナビゲーションでプロジェクトをクリックし、プロジェクト エディターの上部にある *[General]* タブをクリックします。
3. *[Linked Frameworks and Libraries]* セクションの下で、追加ボタン (+) をクリックします。
4. *[Add Other]*をクリックします。 作成した `AZSClient.framework` ファイルに移動して、それを追加します。
5. *[Linked Frameworks and Libraries]* セクションの下で、追加ボタン (+) をもう一度クリックします。
6. 既に表示されているライブラリの一覧で `libxml2.2.dylib` を検索し、それをプロジェクトに追加します。
7. プロジェクト エディターの上部にある *[Build Settings]* タブをクリックします。
8. *[Search Paths]* セクションで *[Framework Search Paths]* をダブルクリックし、`AZSClient.framework` ファイルへのパスを追加します。

## <a name="import-statement"></a>import ステートメント
ファイル内の Azure Storage API を呼び出す場所に、次の import ステートメントを加える必要があります。

```objc
// Include the following import statement to use blob APIs.
#import <AZSClient/AZSClient.h>
```

[!INCLUDE [storage-mobile-authentication-guidance](../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>非同期操作
> [!NOTE]
> サービスに対して要求を実行するメソッドは、すべてが非同期操作です。 コード サンプルでは、このようなメソッドに完了ハンドラーが含まれていることがわかります。 完了ハンドラーの中のコードは、要求が完了した **後に** 実行されます。 完了ハンドラーの後のコードは、要求が行われている **間に** 実行されます。
> 
> 

## <a name="create-a-container"></a>コンテナーを作成する
Azure Storage のどの BLOB もコンテナーに格納する必要があります。 次の例では、ストレージ アカウントに *newcontainer*というコンテナーが存在しない場合、それを作成する方法を示します。 コンテナーの名前を選択する場合は、上記の名前付け規則に注意してください。

```objc
-(void)createContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"newcontainer"];

    // Create container in your Storage account if the container doesn't already exist
    [blobContainer createContainerIfNotExistsWithCompletionHandler:^(NSError *error, BOOL exists) {
        if (error){
            NSLog(@"Error in creating container.");
        }
    }];
}
```

このコード例が正常に機能していることを確認するには、 [Microsoft Azure ストレージ エクスプローラー](http://storageexplorer.com) で、 *newcontainer* がストレージ アカウントのコンテナーの一覧に含まれていることを確認します。

## <a name="set-container-permissions"></a>コンテナーのアクセス許可を設定する
コンテナーのアクセス許可は、既定では、 **プライベート** アクセス用に構成されています。 ただし、コンテナーには、コンテナー アクセス用にいくつかの異なるオプションが用意されています。

* **プライベート**: コンテナーと BLOB のデータはアカウント所有者に限り読み取ることができます。
* **BLOB**: コンテナー内の BLOB のデータは匿名要求で読み取ることができますが、コンテナーのデータを読み取ることはできません。 クライアントはコンテナー内の BLOB を匿名要求で列挙することはできません。
* **コンテナー**: コンテナーと BLOB のデータを匿名要求で読み取ることができます。 クライアントは匿名要求でコンテナー内の BLOB を列挙できますが、ストレージ アカウント内のコンテナーを列挙することはできません。

次の例では、**コンテナー** アクセス許可を指定したコンテナーの作成方法を示します。このアクセス許可により、インターネット上のすべてのユーザーに対して読み取り専用のパブリック アクセスが許可されます。

```objc
-(void)createContainerWithPublicAccess{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create container in your Storage account if the container doesn't already exist
    [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists){
        if (error){
            NSLog(@"Error in creating container.");
        }
    }];
}
```

## <a name="upload-a-blob-into-a-container"></a>コンテナーに BLOB をアップロードする
「 [BLOB サービスの概念](#blob-service-concepts) 」セクションで説明したように、BLOB ストレージには、ブロック BLOB、追加 BLOB、ページ BLOB という 3 種類の BLOB が用意されています。 現在、Azure Storage iOS ライブラリでは、ブロック BLOB のみがサポートされています。 ほとんどの場合は、ブロック BLOB を使用することをお勧めします。

次の例では、NSString からブロック BLOB をアップロードする方法を示します。 同じ名前の BLOB が既にこのコンテナーに存在する場合は、この BLOB の内容が上書きされます。

```objc
-(void)uploadBlobToContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists)
        {
            if (error){
                NSLog(@"Error in creating container.");
            }
            else{
                // Create a local blob object
                AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

                // Upload blob to Storage
                [blockBlob uploadFromText:@"This text will be uploaded to Blob Storage." completionHandler:^(NSError *error) {
                    if (error){
                        NSLog(@"Error in creating blob.");
                    }
                }];
            }
        }];
}
```

このコード例が正常に機能していることを確認するには、[Microsoft Azure ストレージ エクスプローラー](http://storageexplorer.com)で、コンテナー *containerpublic* に BLOB *sampleblob* が含まれていることを確認します。 この例ではパブリック コンテナーを使用したため、次の BLOB URI にアクセスすることによっても、これを確認できます。

    https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob

NSString からブロック BLOB をアップロードする場合だけでなく、NSData、NSInputStream、またはローカル ファイルの場合にも同様のメソッドが存在します。

## <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する
次の例では、コンテナー内のすべての BLOB を一覧表示する方法を示します。 この操作を実行する場合は、次のパラメーターに注意してください。     

* **continuationToken** - 継続トークンは、一覧表示操作の開始位置を表します。 トークンが指定されていない場合、最初から BLOB を一覧表示します。 0 から設定された最大値まで、BLOB はいくつでも一覧表示できます。 このメソッドによって返される結果が 0 件でも、 `results.continuationToken` が nil でない場合は、一覧表示されていない BLOB がもっとサービス上に存在する可能性があります。
* **prefix** - BLOB の一覧表示に使用するプレフィックスを指定できます。 このプレフィックスで始まる名前の BLOB のみが一覧表示されます。
* **useFlatBlobListing** - 「 [コンテナーおよび BLOB の名前付けと参照](#naming-and-referencing-containers-and-blobs) 」セクションで説明したように、BLOB サービスはフラット ストレージ スキームですが、パス情報を使用して BLOB に名前を付けることで、仮想階層を作成できます。 ただし、フラットでない一覧表示は現在サポート対象外であり、近日対応予定です。 現時点では、この値は **YES** にする必要があります。
* **blobListingDetails** - BLOB を一覧表示するときに含める項目を指定できます。
  * _AZSBlobListingDetailsNone_: コミット済みの BLOB のみを一覧表示し、BLOB メタデータは返しません。
  * _AZSBlobListingDetailsSnapshots_: コミット済みの BLOB と BLOB スナップショットを一覧表示します。
  * _AZSBlobListingDetailsMetadata_: 一覧に返された BLOB ごとに BLOB メタデータを取得します。
  * _AZSBlobListingDetailsUncommittedBlobs_: コミット済みおよびコミット前の BLOB を一覧表示します。
  * _AZSBlobListingDetailsCopy_: コピー プロパティを一覧に含めます。
  * _AZSBlobListingDetailsAll_: 使用可能なコミット済みの BLOB、コミット前の BLOB、スナップショットをすべて一覧表示し、それらの BLOB に関するすべてのメタデータとコピーの状態を返します。
* **maxResults** - この操作に対して返される結果の最大数。 制限を設けない場合は -1 を使用します。
* **completionHandler** - 一覧表示操作の結果を使用して実行するコードのブロック。

この例では、継続トークンが返されるたびに BLOB の一覧表示メソッドを再帰的に呼び出すために、ヘルパー メソッドが使用されています。

```objc
-(void)listBlobsInContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    //List all blobs in container
    [self listBlobsInContainerHelper:blobContainer continuationToken:nil prefix:nil blobListingDetails:AZSBlobListingDetailsAll maxResults:-1 completionHandler:^(NSError *error) {
        if (error != nil){
            NSLog(@"Error in creating container.");
        }
    }];
}

//List blobs helper method
-(void)listBlobsInContainerHelper:(AZSCloudBlobContainer *)container continuationToken:(AZSContinuationToken *)continuationToken prefix:(NSString *)prefix blobListingDetails:(AZSBlobListingDetails)blobListingDetails maxResults:(NSUInteger)maxResults completionHandler:(void (^)(NSError *))completionHandler
{
    [container listBlobsSegmentedWithContinuationToken:continuationToken prefix:prefix useFlatBlobListing:YES blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:^(NSError *error, AZSBlobResultSegment *results) {
        if (error)
        {
            completionHandler(error);
        }
        else
        {
            for (int i = 0; i < results.blobs.count; i++) {
                NSLog(@"%@",[(AZSCloudBlockBlob *)results.blobs[i] blobName]);
            }
            if (results.continuationToken)
            {
                [self listBlobsInContainerHelper:container continuationToken:results.continuationToken prefix:prefix blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:completionHandler];
            }
            else
            {
                completionHandler(nil);
            }
        }
    }];
}
```

## <a name="download-a-blob"></a>BLOB をダウンロードする
次の例では、BLOB を NSString オブジェクトにダウンロードする方法を示します。

```objc
-(void)downloadBlobToString{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create a local blob object
    AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

    // Download blob
    [blockBlob downloadToTextWithCompletionHandler:^(NSError *error, NSString *text) {
        if (error) {
            NSLog(@"Error in downloading blob");
        }
        else{
            NSLog(@"%@",text);
        }
    }];
}
```

## <a name="delete-a-blob"></a>BLOB を削除する
次の例では、BLOB を削除する方法を示します。

```objc
-(void)deleteBlob{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create a local blob object
    AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob1"];

    // Delete blob
    [blockBlob deleteWithCompletionHandler:^(NSError *error) {
        if (error) {
            NSLog(@"Error in deleting blob.");
        }
    }];
}
```

## <a name="delete-a-blob-container"></a>BLOB コンテナーを削除する
次の例では、コンテナーを削除する方法を示します。

```objc
-(void)deleteContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Delete container
    [blobContainer deleteContainerIfExistsWithCompletionHandler:^(NSError *error, BOOL success) {
        if(error){
            NSLog(@"Error in deleting container");
        }
    }];
}
```

## <a name="next-steps"></a>次のステップ
これで、iOS から BLOB ストレージを使用する方法を学習できました。 iOS ライブラリおよび Storage サービスの詳細については、次のリンク先をご覧ください。

* [iOS 用 Azure Storage クライアント ライブラリ](https://github.com/azure/azure-storage-ios)
* [Azure Storage iOS リファレンス ドキュメント](http://azure.github.io/azure-storage-ios/)
* [Azure Storage Services REST API (Azure Storage サービスの REST API)](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Azure Storage チーム ブログ](http://blogs.msdn.com/b/windowsazurestorage)

このライブラリに関してご質問がある場合は、お気軽に [MSDN Azure フォーラム](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) または [スタック オーバーフロー](http://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files) に投稿してください。
Azure Storage の機能についてご提案がある場合は、 [Azure Storage のフィードバック](https://feedback.azure.com/forums/217298-storage/)に投稿してください。




<!--HONumber=Nov16_HO4-->


