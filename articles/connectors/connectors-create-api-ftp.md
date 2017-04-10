---
title: "ロジック アプリで FTP コネクタを使用する方法 | Microsoft Docs"
description: "Azure App Service を使用してロジック アプリを作成します。 FTP サーバーに接続して、ファイルを管理します。 FTP サーバーのファイルのアップロード、更新、取得、削除など、多様なアクションを実行できます。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: d83c55fe-eb59-4b7b-a5ec-afac5c772616
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/22/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: 2634d0101c1cd4535922e8844f0beb6b523e2dea


---
# <a name="get-started-with-the-ftp-connector"></a>FTP コネクタの使用
FTP コネクタを使用して、FTP サーバー上のファイルを監視、管理、作成します。 

[任意のコネクタ](apis-list.md)を使用するには、まずロジック アプリを作成する必要があります。 [ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)から始めることができます。

## <a name="connect-to-ftp"></a>FTP に接続する
ロジック アプリから任意のサービスにアクセスできるようにするには、まず、そのサービスへの*接続*を作成する必要があります。 [接続](connectors-overview.md)により、ロジック アプリと別のサービスとの接続が実現します。  

### <a name="create-a-connection-to-ftp"></a>FTP への接続を作成する
> [!INCLUDE [Steps to create a connection to FTP](../../includes/connectors-create-api-ftp.md)]
> 
> 

## <a name="use-a-ftp-trigger"></a>FTP トリガーを使用する
トリガーとは、ロジック アプリで定義されたワークフローの開始に使用できるイベントです。 トリガーの詳細については[こちら](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)を参照してください。  

> [!IMPORTANT]
> FTP コネクタを使用するには、インターネットからアクセスできる、パッシブ モードで動作するように構成されている FTP サーバーが必要です。 また、FTP コネクタは、**暗黙的 FTPS (FTP over SSL)** に対応していません。 FTP コネクタは、明示的 FTPS (FTP over SSL) のみをサポートしています。  
> 
> 

この例では、FTP サーバー上でファイルが追加または変更されたときに、**[FTP - When a file is added or modified (FTP - ファイルが追加または変更されたとき)]** トリガーによりロジック アプリ ワークフローを開始する方法について説明します。 エンタープライズの例では、このトリガーを使用して、FTP フォルダーに顧客からの注文を表す新しいファイルがあるかどうかを監視できます。  次に、**[Get file content (ファイルの内容を取得する)]** などの FTP コネクタ アクションを使用して注文の内容を取得し、後続の処理や注文データベースへの格納を行うことができます。

1. ロジック アプリ デザイナーの検索ボックスに「*ftp*」と入力し、**[FTP - When a file is added or modified (FTP - ファイルが追加または変更されたとき)]** トリガーを選択します。   
   ![FTP トリガーの画像 1](./media/connectors-create-api-ftp/ftp-trigger-1.png)  
   **[When a file is added or modified (ファイルが追加または変更されたとき)]** コントロールを開きます。  
   ![FTP トリガーの画像 2](./media/connectors-create-api-ftp/ftp-trigger-2.png)  
2. コントロールの右側にある **[...]** を選択します。 これによりフォルダー ピッカー コントロールが開きます。  
   ![FTP トリガーの画像 3](./media/connectors-create-api-ftp/ftp-trigger-3.png)  
3. **[>]** (右矢印) を選択し、新しいファイルや変更されたファイルを監視するフォルダーを参照します。 そのフォルダーを選択します。フォルダーが **[フォルダー]** コントロールに表示されます。  
   ![FTP トリガーの画像 4](./media/connectors-create-api-ftp/ftp-trigger-4.png)   

これで、特定の FTP フォルダーでファイルが変更または作成されたときにワークフローの他のトリガーおよびアクションの実行を開始するトリガーがロジック アプリに構成されました。 

> [!NOTE]
> ロジック アプリを機能させるためには、少なくとも&1; つのトリガーとアクションを含まなければなりません。 アクションを追加するには、次のセクションの手順に従います。  
> 
> 

## <a name="use-a-ftp-action"></a>FTP アクションを使用する
アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。 アクションの詳細については[こちら](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)を参照してください。  

トリガーを追加した後は、次の手順に従って、トリガーによって検出された新しいファイルまたは変更されたファイルの内容を取得するアクションを追加します。    

1. **[+ 新しいステップ]** を選択して、FTP サーバー上のファイルの内容を取得するアクションを追加します。  
2. **[アクションの追加]** のリンクを選択します。  
   ![FTP アクションの画像 1](./media/connectors-create-api-ftp/ftp-action-1.png)  
3. 「*FTP*」と入力して、FTP に関連するすべてのアクションを検索します。
4. FTP フォルダーで新しいファイルまたは変更されたファイルが検出されたときに実行するアクションとして **[FTP - Get file content (FTP - ファイルの内容を取得する)]** を選択します。      
   ![FTP アクションの画像 2](./media/connectors-create-api-ftp/ftp-action-2.png)  
   **[Get file content (ファイルの内容を取得する)]** コントロールが開きます。 **注**: ロジック アプリによる FTP サーバー アカウントへのアクセスをまだ承認していない場合は、承認を求められます。  
   ![FTP アクションの画像 3](./media/connectors-create-api-ftp/ftp-action-3.png)   
5. **[ファイル]** コントロール (**[ファイル]*** の下にある空白) を選択します。 ここでは、FTP サーバーで検出された新しいファイルまたは変更されたファイルの各種プロパティを使用することができます。  
6. **[File content (ファイルの内容)]** オプションを選択します。  
   ![FTP アクションの画像 4](./media/connectors-create-api-ftp/ftp-action-4.png)   
7. コントロールが更新され、FTP サーバー上の新しいファイルまたは変更されたファイルについて、**[FTP - Get file content (FTP - ファイルの内容を取得する)]** アクションによって "*ファイルの内容*" が取得されることが示されます。      
   ![FTP アクションの画像 5](./media/connectors-create-api-ftp/ftp-action-5.png)     
8. 作業内容を保存し、ファイルを FTP フォルダーに追加して、ワークフローをテストします。    

これで、FTP サーバー上のフォルダーを監視し、FTP フォルダーで新しいファイルまたは変更されたファイルを検出したときにワークフローを開始するトリガーがロジック アプリに構成されました。 

このロジック アプリには、新しいファイルまたは変更されたファイルの内容を取得するアクションも構成されています。

この段階で、別のアクション (たとえば、新しいファイルまたは変更されたファイルの内容を SQL データベース テーブルに挿入する [[SQL Server - insert row (SQL Server - 行を挿入する)]](connectors-create-api-sqlazure.md#insert-row) アクション) を追加することができます。  

## <a name="technical-details"></a>技術的な詳細
ここでは、この接続でサポートされるトリガー、アクション、応答について詳しく説明します。

## <a name="ftp-triggers"></a>FTP トリガー
FTP には、次のトリガーがあります。  

| トリガー | 説明 |
| --- | --- |
| [ファイルが追加または変更されたとき](connectors-create-api-ftp.md#when-a-file-is-added-or-modified) |この操作では、フォルダー内でファイルが追加または変更されたときにフローをトリガーします。 |

## <a name="ftp-actions"></a>FTP アクション
FTP には、次のアクションがあります。

| [操作] | 説明 |
| --- | --- |
| [ファイルのメタデータを取得する](connectors-create-api-ftp.md#get-file-metadata) |この操作では、ファイルのメタデータを取得します。 |
| [ファイルを更新する](connectors-create-api-ftp.md#update-file) |この操作では、ファイルを更新します。 |
| [ファイルを削除する](connectors-create-api-ftp.md#delete-file) |この操作では、ファイルを削除します。 |
| [パスを使用してファイルのメタデータを取得する](connectors-create-api-ftp.md#get-file-metadata-using-path) |この操作では、パスを使用してファイルのメタデータを取得します。 |
| [パスを使用してファイルの内容を取得する](connectors-create-api-ftp.md#get-file-content-using-path) |この操作では、パスを使用してファイルの内容を取得します。 |
| [ファイルの内容を取得する](connectors-create-api-ftp.md#get-file-content) |この操作では、ファイルの内容を取得します。 |
| [ファイルを作成する](connectors-create-api-ftp.md#create-file) |この操作では、ファイルを作成します。 |
| [ファイルをコピーする](connectors-create-api-ftp.md#copy-file) |この操作では、ファイルを FTP サーバーにコピーします。 |
| [フォルダー内のファイルを一覧表示する](connectors-create-api-ftp.md#list-files-in-folder) |この操作では、フォルダー内のファイルとサブフォルダーの一覧を取得します。 |
| [ルート フォルダー内のファイルを一覧表示する](connectors-create-api-ftp.md#list-files-in-root-folder) |この操作では、ルート フォルダー内のファイルとサブフォルダーの一覧を取得します。 |
| [フォルダーを抽出する](connectors-create-api-ftp.md#extract-folder) |この操作では、フォルダーにアーカイブ ファイル (例: .zip) を抽出します。 |

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
| --- | --- | --- |
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
| --- | --- | --- |
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
| --- | --- | --- |
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
| --- | --- | --- |
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
この操作では、ファイルを FTP サーバーにコピーします。 

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| source* |Source url (コピー元 URL) |ソース ファイルの URL |
| destination* |Destination file path (コピー先ファイル パス) |対象ファイル名を含む、コピー先ファイル パス |
| overwrite |「True」 |’true’ に設定すると、宛先ファイルが上書きされます |

* は、必須のプロパティを示します。

#### <a name="output-details"></a>出力の詳細
BlobMetadata

| プロパティ名 | データ型 |
| --- | --- | --- |
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

### <a name="when-a-file-is-added-or-modified"></a>When a file is added or modified (ファイルの追加または変更時)
この操作では、フォルダー内でファイルが追加または変更されたときにフローをトリガーします。 

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
| --- | --- | --- |
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
| --- | --- | --- |
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

### <a name="extract-folder"></a>フォルダーを抽出する
この操作では、フォルダーにアーカイブ ファイル (例: .zip) を抽出します。 

| プロパティ名 | Displayname Settings | Description |
| --- | --- | --- |
| source* |Source archive file path (ソース アーカイブ ファイルのパス) |アーカイブ ファイルのパス |
| destination* |Destination folder path (抽出先フォルダー パス) |宛先フォルダーのパス |
| overwrite |「True」 |’true’ に設定すると、宛先ファイルが上書きされます |

* は、必須のプロパティを示します。

#### <a name="output-details"></a>出力の詳細
BlobMetadata

| プロパティ名 | データ型 |
| --- | --- | --- |
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


