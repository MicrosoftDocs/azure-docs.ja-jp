---
title: "ロジック アプリで SharePoint Online コネクタを使用する方法 | Microsoft Docs"
description: "SharePoint Online コネクタを使用するロジック アプリを作成して、SharePoint でリストを管理します。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: e0ec3149-507a-409d-8e7b-d5fbded006ce
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/19/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: 3ecf3e30fe2fcb9d6473d7eda450536cddfa97f4


---
# <a name="get-started-with-the-sharepoint-online-connector"></a>SharePoint Online コネクタの概要
SharePoint Online コネクタを使用すると、SharePoint リストを管理できます。  

[任意のコネクタ](apis-list.md)を使用するには、まずロジック アプリを作成する必要があります。 [ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)から始めることができます。

## <a name="connect-to-sharepoint-online"></a>SharePoint Online への接続
ロジック アプリから任意のサービスにアクセスできるようにするには、まず、そのサービスへの*接続*を作成する必要があります。 [接続](connectors-overview.md)により、ロジック アプリと別のサービスとの接続が実現します。  

### <a name="create-a-connection-to-sharepoint-online"></a>SharePoint Online への接続を作成する
> [!INCLUDE [Steps to create a connection to SharePoint](../../includes/connectors-create-api-sharepointonline.md)]
> 
> 

## <a name="use-a-sharepoint-online-trigger"></a>SharePoint Online トリガーの使用
トリガーとは、ロジック アプリで定義されたワークフローの開始に使用できるイベントです。 トリガーの詳細については[こちら](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)を参照してください。  

> [!INCLUDE [Steps to create a SharePoint Online trigger](../../includes/connectors-create-api-sharepointonline-trigger.md)]
> 
> 

## <a name="use-a-sharepoint-online-action"></a>SharePoint Online アクションの使用
アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。 アクションの詳細については[こちら](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)を参照してください。  

> [!INCLUDE [Steps to create a SharePoint Online action](../../includes/connectors-create-api-sharepointonline-action.md)]
> 
> 

## <a name="technical-details"></a>技術的な詳細
ここでは、この接続でサポートされるトリガー、アクション、応答について詳しく説明します。

## <a name="sharepoint-online-triggers"></a>SharePoint Online トリガー
SharePoint には次のトリガーがあります。  

| トリガー | 説明 |
| --- | --- |
| [ファイルの作成時](connectors-create-api-sharepointonline.md#when-a-file-is-created) |この操作では、SharePoint フォルダーに新しいファイルが作成されたときにフローをトリガーします。 |
| [ファイルの変更時](connectors-create-api-sharepointonline.md#when-a-file-is-modified) |この操作では、SharePoint フォルダー内のファイルが変更されたときにフローをトリガーします。 |
| [新しいアイテムの作成時](connectors-create-api-sharepointonline.md#when-a-new-item-is-created) |この操作では、SharePoint リストに新しいアイテムが作成されたときにフローをトリガーします。 |
| [既存のアイテムの変更時](connectors-create-api-sharepointonline.md#when-an-existing-item-is-modified) |この操作では、SharePoint リスト内の既存のアイテムが変更されたときにフローをトリガーします。 |

## <a name="sharepoint-online-actions"></a>SharePoint Online アクション
SharePoint には次のアクションがあります。

| [操作] | 説明 |
| --- | --- |
| [ファイルのメタデータを取得する](connectors-create-api-sharepointonline.md#get-file-metadata) |この操作では、ファイル ID を使用してファイル メタデータを取得します。 |
| [ファイルを更新する](connectors-create-api-sharepointonline.md#update-file) |この操作では、ファイルのコンテンツを更新します。 |
| [ファイルを削除する](connectors-create-api-sharepointonline.md#delete-file) |この操作では、ファイルを削除します。 |
| [パスを使用してファイルのメタデータを取得する](connectors-create-api-sharepointonline.md#get-file-metadata-using-path) |この操作では、ファイル パスを使用してファイル メタデータを取得します。 |
| [パスを使用してファイルの内容を取得する](connectors-create-api-sharepointonline.md#get-file-content-using-path) |この操作では、ファイル パスを使用してファイル コンテンツを取得します。 |
| [ファイルの内容を取得する](connectors-create-api-sharepointonline.md#get-file-content) |この操作では、ファイル ID を使用してファイル コンテンツを取得します。 |
| [ファイルを作成する](connectors-create-api-sharepointonline.md#create-file) |この操作では、SharePoint サイトにファイルをアップロードします。 |
| [ファイルをコピーする](connectors-create-api-sharepointonline.md#copy-file) |この操作では、SharePoint サイトにファイルをコピーします。 |
| [フォルダーの内容を一覧表示する](connectors-create-api-sharepointonline.md#list-folder) |この操作では、SharePoint フォルダーに含まれているファイルを取得します。 |
| [ルート フォルダーの内容を一覧表示する](connectors-create-api-sharepointonline.md#list-root-folder) |この操作では、ルート SharePoint フォルダーのファイルを取得します。 |
| [フォルダーを抽出する](connectors-create-api-sharepointonline.md#extract-folder) |この操作では、アーカイブ ファイルを SharePoint フォルダーに展開します (例: .zip)。 |
| [複数のアイテムを取得する](connectors-create-api-sharepointonline.md#get-items) |この操作では、SharePoint リストからアイテムを取得します。 |
| [アイテムを作成する](connectors-create-api-sharepointonline.md#create-item) |この操作では、SharePoint リストに新しいアイテムを作成します。 |
| [アイテムを取得する](connectors-create-api-sharepointonline.md#get-item) |この操作では、ID を使用して単一のアイテムを SharePoint リストから取得します。 |
| [アイテムを削除する](connectors-create-api-sharepointonline.md#delete-item) |この操作では、SharePoint リストからアイテムを削除します。 |
| [アイテムを更新する](connectors-create-api-sharepointonline.md#update-item) |この操作では、SharePoint リスト内のアイテムを更新します。 |
| [エンティティの値を取得する](connectors-create-api-sharepointonline.md#get-entity-values) |この操作では、SharePoint エンティティに指定できる値を取得します。 |
| [リストを取得する](connectors-create-api-sharepointonline.md#get-lists) |この操作では、サイトから SharePoint リストを取得します。 |

### <a name="action-details"></a>アクションの詳細
ここでは、このコネクタのアクションとトリガー、および応答について詳しく説明します。

### <a name="get-file-metadata"></a>ファイルのメタデータを取得する
この操作では、ファイル ID を使用してファイルのメタデータを取得します。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| dataset* |サイトの URL |SharePoint サイト URL (http://contoso.sharepoint.com/sites/mysite など) |
| id* |ファイル識別子 |ファイルを選択する |

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
この操作では、ファイルの内容を更新します。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| dataset* |サイトの URL |SharePoint サイト URL (http://contoso.sharepoint.com/sites/mysite など) |
| id* |ファイル識別子 |ファイルを選択する |
| body* |ファイル コンテンツ |ファイルの内容 |

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
| dataset* |サイトの URL |SharePoint サイト URL (http://contoso.sharepoint.com/sites/mysite など) |
| id* |ファイル識別子 |ファイルを選択する |

* は、必須のプロパティを示します。

### <a name="get-file-metadata-using-path"></a>パスを使用してファイルのメタデータを取得する
この操作では、ファイル パスを使用してファイル メタデータを取得します。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| dataset* |サイトの URL |SharePoint サイト URL (http://contoso.sharepoint.com/sites/mysite など) |
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
この操作では、ファイル パスを使用してファイル コンテンツを取得します。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| dataset* |サイトの URL |SharePoint サイト URL (http://contoso.sharepoint.com/sites/mysite など) |
| path* |ファイル パス |ファイルを選択する |

* は、必須のプロパティを示します。

### <a name="get-file-content"></a>ファイルの内容を取得する
この操作では、ファイルID を使用してファイルの内容を取得します。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| dataset* |サイトの URL |SharePoint サイト URL (http://contoso.sharepoint.com/sites/mysite など) |
| id* |ファイル識別子 |ファイルを選択する |

* は、必須のプロパティを示します。

### <a name="create-file"></a>ファイルを作成する
この操作では、SharePoint サイトにファイルをアップロードします。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| dataset* |サイトの URL |SharePoint サイト URL (http://contoso.sharepoint.com/sites/mysite など) |
| folderPath* |フォルダー パス |ファイルを選択する |
| name* |ファイル名 |ファイルの名前 |
| body* |ファイル コンテンツ |ファイルの内容 |

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
この操作では、SharePoint サイトにファイルをコピーします。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| dataset* |サイトの URL |SharePoint サイト URL (http://contoso.sharepoint.com/sites/mysite など) |
| source* |Source file path (抽出元ファイル パス) |ソース ファイルのパス |
| destination* |Destination file path (コピー先ファイル パス) |宛先ファイルのパス |
| overwrite |Overwrite flag (上書きフラグ) |抽出先ファイルが存在する場合にファイルを上書きするかどうか |

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

### <a name="when-a-file-is-created"></a>ファイルの作成時
この操作では、SharePoint フォルダーに新しいファイルが作成されたときにフローをトリガーします。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| dataset* |サイトの URL |SharePoint サイトの URL |
| folderId* |フォルダー ID |フォルダーを選択する |

* は、必須のプロパティを示します。

### <a name="when-a-file-is-modified"></a>ファイルの変更時
この操作では、SharePoint フォルダー内のファイルが変更されたときにフローをトリガーします。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| dataset* |サイトの URL |SharePoint サイトの URL |
| folderId* |フォルダー ID |フォルダーを選択する |

* は、必須のプロパティを示します。

### <a name="list-folder"></a>フォルダーの内容を一覧表示する
この操作では、SharePoint フォルダーに含まれているファイルを取得します。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| dataset* |サイトの URL |SharePoint サイト URL (http://contoso.sharepoint.com/sites/mysite など) |
| id* |ファイル識別子 |フォルダーの一意識別子 |

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

### <a name="list-root-folder"></a>ルート フォルダーの内容を一覧表示する
この操作では、ルート SharePoint フォルダー内のファイルを取得します。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| dataset* |サイトの URL |SharePoint サイト URL (http://contoso.sharepoint.com/sites/mysite など) |

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

### <a name="extract-folder"></a>フォルダーを抽出する
この操作では、アーカイブ ファイルを SharePoint フォルダーに展開します (例: .zip)。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| dataset* |サイトの URL |SharePoint サイト URL (http://contoso.sharepoint.com/sites/mysite など) |
| source* |Source file path (抽出元ファイル パス) |ソース ファイルのパス |
| destination* |Destination folder path (抽出先フォルダー パス) |宛先フォルダーのパス |
| overwrite |Overwrite flag (上書きフラグ) |抽出先ファイルが存在する場合にファイルを上書きするかどうか |

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

### <a name="when-a-new-item-is-created"></a>新しいアイテムの作成時
この操作では、SharePoint リストに新しいアイテムが作成されたときにフローをトリガーします。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| dataset* |サイトの URL |SharePoint サイト URL (例: http://contoso.sharepoint.com/sites/mysite) |
| table* |リスト名 |SharePoint リストの名前 |
| $filter |Filter Query (フィルター クエリ) |返されるエントリを制限する ODATA filter クエリ |
| $orderby |Order By (並べ替え) |エントリの順序を指定する ODATA orderBy クエリ |
| $skip |Skip Count (スキップ数) |スキップするエントリの数 (既定値 = 0) |
| $top |Maximum Get Count (最大取得数) |取得するエントリの最大数 (既定値 = 256) |

* は、必須のプロパティを示します。

#### <a name="output-details"></a>出力の詳細
ItemsList

| プロパティ名 | データ型 |
| --- | --- |
| 値 |array |

### <a name="when-an-existing-item-is-modified"></a>既存のアイテムの変更時
この操作では、SharePoint リスト内の既存のアイテムが変更されたときにフローをトリガーします。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| dataset* |サイトの URL |SharePoint サイト URL (例: http://contoso.sharepoint.com/sites/mysite) |
| table* |リスト名 |SharePoint リストの名前 |
| $filter |Filter Query (フィルター クエリ) |返されるエントリを制限する ODATA filter クエリ |
| $orderby |Order By (並べ替え) |エントリの順序を指定する ODATA orderBy クエリ |
| $skip |Skip Count (スキップ数) |スキップするエントリの数 (既定値 = 0) |
| $top |Maximum Get Count (最大取得数) |取得するエントリの最大数 (既定値 = 256) |

* は、必須のプロパティを示します。

#### <a name="output-details"></a>出力の詳細
ItemsList

| プロパティ名 | データ型 |
| --- | --- |
| 値 |array |

### <a name="get-items"></a>複数のアイテムを取得する
この操作では、SharePoint リストから複数のアイテムを取得します。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| dataset* |サイトの URL |SharePoint サイト URL (例: http://contoso.sharepoint.com/sites/mysite) |
| table* |リスト名 |SharePoint リストの名前 |
| $filter |Filter Query (フィルター クエリ) |返されるエントリを制限する ODATA filter クエリ |
| $orderby |Order By (並べ替え) |エントリの順序を指定する ODATA orderBy クエリ |
| $skip |Skip Count (スキップ数) |スキップするエントリの数 (既定値 = 0) |
| $top |Maximum Get Count (最大取得数) |取得するエントリの最大数 (既定値 = 256) |

* は、必須のプロパティを示します。

#### <a name="output-details"></a>出力の詳細
ItemsList

| プロパティ名 | データ型 |
| --- | --- |
| 値 |array |

### <a name="create-item"></a>アイテムを作成する
この操作では、SharePoint リストに新しいアイテムを作成します。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| dataset* |サイトの URL |SharePoint サイト URL (例: http://contoso.sharepoint.com/sites/mysite) |
| table* |リスト名 |SharePoint リストの名前 |
| item* |項目 |作成する項目 |

* は、必須のプロパティを示します。

#### <a name="output-details"></a>出力の詳細
項目

| プロパティ名 | データ型 |
| --- | --- |
| ItemInternalId |string |

### <a name="get-item"></a>アイテムを取得する
この操作では、ID を使用して単一のアイテムを SharePoint リストから取得します。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| dataset* |サイトの URL |SharePoint サイト URL (例: http://contoso.sharepoint.com/sites/mysite) |
| table* |リスト名 |SharePoint リストの名前 |
| id* |ID |取得する項目の一意識別子 |

* は、必須のプロパティを示します。

#### <a name="output-details"></a>出力の詳細
項目

| プロパティ名 | データ型 |
| --- | --- |
| ItemInternalId |string |

### <a name="delete-item"></a>アイテムを削除する
この操作では、SharePoint リストからアイテムを削除します。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| dataset* |サイトの URL |SharePoint サイト URL (例: http://contoso.sharepoint.com/sites/mysite) |
| table* |リスト名 |SharePoint リストの名前 |
| id* |ID |削除する項目の一意識別子 |

* は、必須のプロパティを示します。

### <a name="update-item"></a>アイテムを更新する
この操作では、SharePoint リスト内のアイテムを更新します。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| dataset* |サイトの URL |SharePoint サイト URL (例: http://contoso.sharepoint.com/sites/mysite) |
| table* |リスト名 |SharePoint リストの名前 |
| id* |ID |更新する項目の一意識別子 |
| item* |項目 |変更されたプロパティがある項目 |

* は、必須のプロパティを示します。

#### <a name="output-details"></a>出力の詳細
項目

| プロパティ名 | データ型 |
| --- | --- |
| ItemInternalId |string |

### <a name="get-entity-values"></a>エンティティの値を取得する
この操作では、SharePoint エンティティに指定できる値を取得します。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| dataset* |SharePoint サイトの URL |SharePoint サイトの URL |
| table* |テーブル名 |テーブル名 |
| id* |エンティティ ID |エンティティ ID |

* は、必須のプロパティを示します。

#### <a name="output-details"></a>出力の詳細
### <a name="get-lists"></a>リストを取得する
この操作では、サイトから SharePoint リストを取得します。 

| プロパティ名 | Displayname Settings | 説明 |
| --- | --- | --- |
| dataset* |サイトの URL |SharePoint サイト URL (例: http://contoso.sharepoint.com/sites/mysite) |

* は、必須のプロパティを示します。

#### <a name="output-details"></a>出力の詳細
TablesList

| プロパティ名 | データ型 |
| --- | --- |
| 値 |array |

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


