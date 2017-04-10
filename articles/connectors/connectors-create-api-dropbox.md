---
title: Dropbox | Microsoft Docs
description: "Azure App Service を使用してロジック アプリを作成します。 Dropbox に接続してファイルを管理します。 Dropbox のファイルのアップロード、更新、取得、削除など、多様なアクションを実行できます。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: cb0ae033-aba7-4ac9-beaa-be561a0f0cac
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/15/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 9c74b25a2ac5e2088a841d97920035376b7f3f11
ms.openlocfilehash: a8ecad3552f35c02c07fe33aa8531fb462b27002


---
# <a name="get-started-with-the-dropbox-connector"></a>Dropbox コネクタの使用
Dropbox に接続してファイルを管理します。 Dropbox のファイルのアップロード、更新、取得、削除など、多様なアクションを実行できます。

[任意のコネクタ](apis-list.md)を使用するには、まずロジック アプリを作成する必要があります。 [ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)から始めることができます。

## <a name="connect-to-dropbox"></a>Dropbox に接続する
ロジック アプリから任意のサービスにアクセスできるようにするには、まず、そのサービスへの*接続*を作成する必要があります。 接続により、ロジック アプリと別のサービスとの接続が実現します。 たとえば、Dropbox に接続するには、最初に Dropbox "*接続*" を作成する必要があります。 接続を作成するには、接続先のサービスへのアクセスに通常使用する資格情報を入力する必要があります。 たとえば、Dropbox の場合は、Dropbox への接続を作成するために Dropbox アカウントの資格情報が必要になります。 接続の詳細については、[こちら]()をご覧ください。

### <a name="create-a-connection-to-dropbox"></a>Dropbox への接続を作成する
> [!INCLUDE [Steps to create a connection to Dropbox](../../includes/connectors-create-api-dropbox.md)]
> 
> 

## <a name="use-a-dropbox-trigger"></a>Dropbox トリガーを使用する
トリガーとは、ロジック アプリで定義されたワークフローの開始に使用できるイベントです。 トリガーの詳細については[こちら](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)を参照してください。

この例では、**[When a file is created (ファイルの作成時)]** トリガーを使用します。 このトリガーが発生したら、**[Get file content using path (パスを使用してファイルの内容を取得する)]** Dropbox アクションを呼び出します。 

1. Logic Apps デザイナーの検索ボックスに「*dropbox*」と入力し、**[Dropbox - When a file is created (Dropbox - ファイルの作成時)]** トリガーを選択します。      
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger.PNG)  
2. ファイルの作成をトラッキングするフォルダーを選択します。 [...] (赤で囲まれている部分) を選択し、トリガーの入力用に選択するフォルダーを参照します。  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-trigger-2.PNG)  

## <a name="use-a-dropbox-action"></a>Dropbox アクションを使用する
アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。 アクションの詳細については[こちら](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)を参照してください。

トリガーを追加した後は、次の手順に従って、新しいファイルの内容を取得するアクションを追加します。

1. **[+ 新しいステップ]** を選択し、新しいファイルの作成時に実行するアクションを追加します。  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action.PNG)
2. **[アクションの追加]**を選択します。 これにより検索ボックスが開き、行う操作について検索できます。  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-2.PNG)
3. 「*dropbox*」と入力して、Dropbox に関連するアクションを検索します。  
4. 選択した Dropbox フォルダーに新しいファイルが作成されたときに実行するアクションとして、**[Dropbox - Get file content using path (Dropbox - パスを使用してファイルの内容を取得する)]** を選択します。 アクションの制御ブロックが表示されます。 ロジック アプリによる Dropbox アカウントへのアクセスをまだ承認していない場合は、承認を求められます。  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-3.PNG)  
5. (**[ファイル パス]** コントロールの右側にある) [...] を選択し、使用するファイル パスを参照します。 または、**ファイル パス** トークンを使用してロジック アプリをすばやく作成することもできます。  
   ![](../../includes/media/connectors-create-api-dropbox/using-dropbox-action-4.PNG)  
6. 作業内容を保存し、Dropbox に新しいファイルを作成してワークフローをアクティブ化します。  

## <a name="technical-details"></a>技術的な詳細
ここでは、この接続でサポートされるトリガー、アクション、応答について詳しく説明します。

## <a name="dropbox-triggers"></a>Dropbox トリガー
Dropbox コネクタには、次のトリガーがあります。  

| トリガー | 説明 |
| --- | --- |
| [ファイルの作成時](connectors-create-api-dropbox.md#when-a-file-is-created) |この操作では、フォルダーに新しいファイルが作成されたときにフローをトリガーします。 |
| [ファイルの変更時](connectors-create-api-dropbox.md#when-a-file-is-modified) |この操作では、フォルダー内のファイルが変更されたときにフローをトリガーします。 |

## <a name="dropbox-actions"></a>Dropbox アクション
Dropbox コネクタには、次のアクションがあります。

| [操作] | 説明 |
| --- | --- |
| [ファイルのメタデータを取得する](connectors-create-api-dropbox.md#get-file-metadata) |この操作では、ファイルのメタデータを取得します。 |
| [ファイルを更新する](connectors-create-api-dropbox.md#update-file) |この操作では、ファイルを更新します。 |
| [ファイルを削除する](connectors-create-api-dropbox.md#delete-file) |この操作では、ファイルを削除します。 |
| [パスを使用してファイルのメタデータを取得する](connectors-create-api-dropbox.md#get-file-metadata-using-path) |この操作では、パスを使用してファイルのメタデータを取得します。 |
| [パスを使用してファイルの内容を取得する](connectors-create-api-dropbox.md#get-file-content-using-path) |この操作では、パスを使用してファイルの内容を取得します。 |
| [ファイルの内容を取得する](connectors-create-api-dropbox.md#get-file-content) |この操作では、ファイルの内容を取得します。 |
| [ファイルを作成する](connectors-create-api-dropbox.md#create-file) |この操作では、ファイルを作成します。 |
| [ファイルをコピーする](connectors-create-api-dropbox.md#copy-file) |この操作では、ファイルを Dropbox にコピーします。 |
| [フォルダー内のファイルを一覧表示する](connectors-create-api-dropbox.md#list-files-in-folder) |この操作では、フォルダー内のファイルとサブフォルダーの一覧を取得します。 |
| [ルート フォルダー内のファイルを一覧表示する](connectors-create-api-dropbox.md#list-files-in-root-folder) |この操作では、ルート フォルダー内のファイルとサブフォルダーの一覧を取得します。 |
| [アーカイブをフォルダーに抽出する](connectors-create-api-dropbox.md#extract-archive-to-folder) |この操作では、フォルダーにアーカイブ ファイル (例: .zip) を抽出します。 |

### <a name="action-details"></a>アクションの詳細
ここでは、このコネクタのアクションとトリガー、および応答について詳しく説明します。

### <a name="get-file-metadata"></a>ファイルのメタデータを取得する
この操作では、ファイルのメタデータを取得します。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| id* |ファイル |ファイルを選択する |

* は、必須のプロパティを示します。

#### <a name="output-details"></a>出力の詳細
BlobMetadata

| プロパティ名 | データ型 |
| --- | --- |
| id |string |
| 名前 |string |
| displayName |string |
| path |string |
| LastModified |string |
| サイズ |integer |
| MediaType |string |
| IsFolder |ブール値 |
| ETag |string |
| FileLocator |string |

### <a name="update-file"></a>ファイルを更新する
この操作では、ファイルを更新します。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| id* |ファイル |ファイルを選択する |
| body* |ファイルのコンテンツ |ファイルの内容 |

* は、必須のプロパティを示します。

#### <a name="output-details"></a>出力の詳細
BlobMetadata

| プロパティ名 | データ型 |
| --- | --- |
| id |string |
| 名前 |string |
| displayName |string |
| path |string |
| LastModified |string |
| サイズ |integer |
| MediaType |string |
| IsFolder |ブール値 |
| ETag |string |
| FileLocator |string |

### <a name="delete-file"></a>ファイルを削除する
この操作では、ファイルを削除します。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| id* |ファイル |ファイルを選択する |

* は、必須のプロパティを示します。

### <a name="get-file-metadata-using-path"></a>パスを使用してファイルのメタデータを取得する
この操作では、パスを使用してファイルのメタデータを取得します。 

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| path* |ファイル パス |ファイルを選択する |

* は、必須のプロパティを示します。

#### <a name="output-details"></a>出力の詳細
BlobMetadata

| プロパティ名 | データ型 |
| --- | --- |
| id |string |
| 名前 |string |
| displayName |string |
| path |string |
| LastModified |string |
| サイズ |integer |
| MediaType |string |
| IsFolder |ブール値 |
| ETag |string |
| FileLocator |string |

### <a name="get-file-content-using-path"></a>パスを使用してファイルの内容を取得する
この操作では、パスを使用してファイルの内容を取得します。 

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| path* |ファイル パス |ファイルを選択する |

* は、必須のプロパティを示します。

### <a name="get-file-content"></a>ファイルの内容を取得する
この操作では、ファイルの内容を取得します。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| id* |ファイル |ファイルを選択する |

* は、必須のプロパティを示します。

### <a name="create-file"></a>ファイルを作成する
この操作では、ファイルを作成します。 

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| folderPath* |フォルダー パス |フォルダーを選択する |
| name* |ファイル名 |ファイルの名前 |
| body* |ファイルのコンテンツ |ファイルの内容 |

* は、必須のプロパティを示します。

#### <a name="output-details"></a>出力の詳細
BlobMetadata

| プロパティ名 | データ型 |
| --- | --- |
| id |string |
| 名前 |string |
| displayName |string |
| path |string |
| LastModified |string |
| サイズ |integer |
| MediaType |string |
| IsFolder |ブール値 |
| ETag |string |
| FileLocator |string |

### <a name="copy-file"></a>ファイルをコピーする
この操作では、ファイルを Dropbox にコピーします。 

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| source* |Source url (コピー元 URL) |ソース ファイルの URL |
| destination* |Destination file path (コピー先ファイル パス) |対象ファイル名を含む、コピー先ファイル パス |
| overwrite |「True」 |’true’ に設定すると、宛先ファイルが上書きされます |

* は、必須のプロパティを示します。

#### <a name="output-details"></a>出力の詳細
BlobMetadata

| プロパティ名 | データ型 |
| --- | --- |
| id |string |
| 名前 |string |
| displayName |string |
| path |string |
| LastModified |string |
| サイズ |integer |
| MediaType |string |
| IsFolder |ブール値 |
| ETag |string |
| FileLocator |string |

### <a name="when-a-file-is-created"></a>ファイルの作成時
この操作では、フォルダーに新しいファイルが作成されたときにフローをトリガーします。 

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| folderId* |フォルダー |フォルダーを選択する |

* は、必須のプロパティを示します。

### <a name="when-a-file-is-modified"></a>ファイルの変更時
この操作では、フォルダー内のファイルが変更されたときにフローをトリガーします。 

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| folderId* |フォルダー |フォルダーを選択する |

* は、必須のプロパティを示します。

### <a name="list-files-in-folder"></a>フォルダー内のファイルを一覧表示する
この操作では、フォルダー内のファイルとサブフォルダーの一覧を取得します。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| id* |フォルダー |フォルダーを選択する |

* は、必須のプロパティを示します。

#### <a name="output-details"></a>出力の詳細
BlobMetadata

| プロパティ名 | データ型 |
| --- | --- |
| id |string |
| 名前 |string |
| displayName |string |
| path |string |
| LastModified |string |
| サイズ |integer |
| MediaType |string |
| IsFolder |ブール値 |
| ETag |string |
| FileLocator |string |

### <a name="list-files-in-root-folder"></a>ルート フォルダー内のファイルを一覧表示する
この操作では、ルート フォルダー内のファイルとサブフォルダーの一覧を取得します。 

この呼び出しには、パラメーターはありません

#### <a name="output-details"></a>出力の詳細
BlobMetadata

| プロパティ名 | データ型 |
| --- | --- |
| id |string |
| 名前 |string |
| displayName |string |
| path |string |
| LastModified |string |
| サイズ |integer |
| MediaType |string |
| IsFolder |ブール値 |
| ETag |string |
| FileLocator |string |

### <a name="extract-archive-to-folder"></a>アーカイブをフォルダーに抽出する
この操作では、フォルダーにアーカイブ ファイル (例: .zip) を抽出します。 

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| source* |Source archive file path (ソース アーカイブ ファイルのパス) |アーカイブ ファイルのパス |
| destination* |Destination folder path (抽出先フォルダー パス) |アーカイブの内容を抽出するパス |
| overwrite |「True」 |’true’ に設定すると、宛先ファイルが上書きされます |

* は、必須のプロパティを示します。

#### <a name="output-details"></a>出力の詳細
BlobMetadata

| プロパティ名 | データ型 |
| --- | --- |
| id |string |
| 名前 |string |
| displayName |string |
| path |string |
| LastModified |string |
| サイズ |integer |
| MediaType |string |
| IsFolder |ブール値 |
| ETag |string |
| FileLocator |string |

## <a name="http-responses"></a>HTTP 応答
上記のアクションとトリガーは、次の HTTP 状態コードを&1; つ以上返す場合があります。 

| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| 202 |承認済み |
| 400 |正しくない要求 |
| 401 |権限がありません |
| 403 |許可されていません |
| 404 |見つかりません |
| 500 |内部サーバー エラー。 不明なエラーが発生しました。 |
| default |操作に失敗しました。 |

## <a name="next-steps"></a>次のステップ
[ロジック アプリを作成します](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


