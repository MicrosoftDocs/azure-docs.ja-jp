---
title: "ロジック アプリで SharePoint Online コネクタを使用する | Microsoft Docs"
description: "ロジック アプリで SharePoint Online コネクタを使用する方法の概要について説明します"
services: 
documentationcenter: 
author: msftman
manager: anneta
editor: 
tags: connectors
ms.assetid: 0238a060-d592-4719-b7a2-26064c437a1a
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 9c74b25a2ac5e2088a841d97920035376b7f3f11
ms.openlocfilehash: c462c98e2ecef1afe62ef921265b8842b6ae717d
ms.lasthandoff: 01/20/2017


---
# <a name="get-started-with-the-sharepoint-online-connector"></a>SharePoint Online コネクタの使用
SharePoint コネクタを使用すると、SharePoint リストを操作できます。

> [!NOTE]
> 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。
> 
> 

ロジック アプリに操作を追加する方法については、 [ロジック アプリの作成に関するページ](../logic-apps/logic-apps-create-a-logic-app.md)を参照してください。

## <a name="lets-talk-about-triggers-and-actions"></a>トリガーとアクション
SharePoint コネクタは、アクションとして使用できます。SharePoint コネクタにはトリガーがあります。 すべてのコネクタは、JSON および XML 形式のデータに対応します。 

SharePoint コネクタでは、次のアクションやトリガーを使用できます。

### <a name="sharepoint-actions"></a>SharePoint のアクション
実行できるアクションは以下のとおりです。

| アクション | 説明 |
| --- | --- |
| GetFileMetadata |ドキュメント ライブラリのファイル メタデータを取得するために使用されます |
| UpdateFile |ドキュメント ライブラリのファイルを更新するために使用されます |
| DeleteFile |ドキュメント ライブラリのファイルを削除するために使用されます |
| GetFileMetadataByPath |ドキュメント ライブラリのファイル メタデータを取得するために使用されます |
| GetFileContentByPath |ドキュメント ライブラリのファイルを取得するために使用されます |
| GetFileContent |ドキュメント ライブラリのファイルを取得するために使用されます |
| CreateFile |ドキュメント ライブラリにファイルをアップロードするために使用されます |
| CopyFile |ドキュメント ライブラリのファイルをコピーするために使用されます |
| ExtractFolderV2 |ドキュメント ライブラリのフォルダーを抽出するために使用されます |
| PostItem |SharePoint リストに新しい項目を作成します |
| GetItem |SharePoint リストから&1; つの項目を取得します |
| DeleteItem |SharePoint リストから項目を削除します |
| PatchItem |SharePoint リストの&1; つの項目を更新します |

### <a name="sharepoint-triggers"></a>SharePoint のトリガー
次のイベントをリッスンできます。

| トリガー | 説明 |
| --- | --- |
| OnNewFile |SharePoint フォルダーに新しいファイルが作成されたときにフローをトリガーします |
| OnUpdatedFile |SharePoint フォルダーのファイルが変更されたときにフローをトリガーします |
| GetOnNewItems |SharePoint リストに新しい項目が作成されたとき |
| GetOnUpdatedItems |SharePoint リストの既存の項目が変更されたとき |

## <a name="create-a-connection-to-sharepoint"></a>SharePoint への接続を作成する
SharePoint コネクタを使用するには、まず **接続** を作成してから、以下のプロパティの詳細を指定します。 

| プロパティ | 必須 | 説明 |
| --- | --- | --- |
| トークン |あり |SharePoint の資格情報を指定します |

**SharePoint Online** に接続するには、SharePoint Online で ID (ユーザー名とパスワード、スマート カードの資格情報など) を入力する必要があります。 認証されたら、ロジック アプリで SharePoint Online コネクタを使用できるようになります。 

ロジック アプリのデザイナーで、次の手順に従って SharePoint にサインインし、ロジック アプリで使用する **接続** を作成します。

1. 検索ボックスに「SharePoint」と入力し、名前に SharePoint が含まれるすべてのエントリが返されるまで待ちます。   
   ![SharePoint の構成][1]  
2. **[SharePoint Online - ファイルの作成時]**   
3. **[Sign in to SharePoint Online (SharePoint Online にサインイン)]** をクリックします。   
   ![SharePoint の構成][2]    
4. SharePoint で認証するために、SharePoint 資格情報を入力してサインインします。   
   ![SharePoint の構成][3]     
5. 認証が完了すると、ロジック アプリにリダイレクトされます。SharePoint の **[When a file is created (ファイルの作成時)]** ダイアログを構成して、ロジック アプリを完成させます。          
   ![SharePoint の構成][4]  
6. ロジック アプリを完成させるために必要な他のトリガーやアクションを追加できます。   
7. 上部のメニュー バーの **[保存]** をクリックして、作業内容を保存します。  

## <a name="sharepoint-rest-api-reference"></a>SharePoint REST API リファレンス
#### <a name="this-documentation-is-for-version-10"></a>このドキュメントの対象バージョン: 1.0
### <a name="used-for-getting-a-file-metadata-on-document-library"></a>ドキュメント ライブラリのファイル メタデータを取得するために使用されます
**```GET: /datasets/{dataset}/files/{id}```** 

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |あり |path |なし |SharePoint サイトの URL。 例: http://contoso.sharepoint.com/sites/mysite |
| id |string |あり |path |なし |ファイルの一意識別子 |

### <a name="here-are-the-possible-responses"></a>次のような応答があります。
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

- - -
### <a name="used-for-updating-a-file-on-document-library"></a>ドキュメント ライブラリのファイルを更新するために使用されます
**```PUT: /datasets/{dataset}/files/{id}```** 

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |あり |path |なし |SharePoint サイトの URL。 例: http://contoso.sharepoint.com/sites/mysite |
| id |string |あり |path |なし |ファイルの一意識別子 |
| body | |あり |body |なし |ファイルの内容 |

### <a name="here-are-the-possible-responses"></a>次のような応答があります。
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

- - -
### <a name="used-for-deleting-a-file-on-document-library"></a>ドキュメント ライブラリのファイルを削除するために使用されます
**```DELETE: /datasets/{dataset}/files/{id}```** 

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |あり |path |なし |SharePoint サイトの URL。 例: http://contoso.sharepoint.com/sites/mysite |
| id |string |あり |path |なし |ファイルの一意識別子 |

### <a name="here-are-the-possible-responses"></a>次のような応答があります。
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

- - -
### <a name="used-for-getting-a-file-metadata-on-document-library"></a>ドキュメント ライブラリのファイル メタデータを取得するために使用されます
**```GET: /datasets/{dataset}/GetFileByPath```** 

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |あり |path |なし |SharePoint サイトの URL。 例: http://contoso.sharepoint.com/sites/mysite |
| path |string |○ |query |なし |ファイルのパス |

### <a name="here-are-the-possible-responses"></a>次のような応答があります。
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

- - -
### <a name="used-for-getting-a-file-on-document-library"></a>ドキュメント ライブラリのファイルを取得するために使用されます
**```GET: /datasets/{dataset}/GetFileContentByPath```** 

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |あり |path |なし |SharePoint サイトの URL。 例: http://contoso.sharepoint.com/sites/mysite |
| path |string |○ |query |なし |ファイルのパス |

### <a name="here-are-the-possible-responses"></a>次のような応答があります。
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

- - -
### <a name="used-for-getting-a-file-on-document-library"></a>ドキュメント ライブラリのファイルを取得するために使用されます
**```GET: /datasets/{dataset}/files/{id}/content```** 

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |あり |path |なし |SharePoint サイトの URL。 例: http://contoso.sharepoint.com/sites/mysite |
| id |string |あり |path |なし |ファイルの一意識別子 |

### <a name="here-are-the-possible-responses"></a>次のような応答があります。
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

- - -
### <a name="used-for-uploading-a-file-on-document-library"></a>ドキュメント ライブラリにファイルをアップロードするために使用されます
**```POST: /datasets/{dataset}/files```** 

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |あり |path |なし |SharePoint サイトの URL。 例: http://contoso.sharepoint.com/sites/mysite |
| folderPath |string |○ |query |なし |フォルダーのパス |
| 名前 |string |○ |query |なし |ファイルの名前 |
| body | |あり |body |なし |ファイルの内容 |

### <a name="here-are-the-possible-responses"></a>次のような応答があります。
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

- - -
### <a name="used-for-copying-a-file-on-document-library"></a>ドキュメント ライブラリのファイルをコピーするために使用されます
**```POST: /datasets/{dataset}/copyFile```** 

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |あり |path |なし |SharePoint サイトの URL。 例: http://contoso.sharepoint.com/sites/mysite |
| source セクション |string |○ |query |なし |ソース ファイルのパス |
| destination |string |○ |query |なし |宛先ファイルのパス |
| overwrite |ブール値 |× |query |false |既存のファイルを上書きするかどうか |

### <a name="here-are-the-possible-responses"></a>次のような応答があります。
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

- - -
### <a name="triggers-a-flow-when-a-new-file-is-created-in-a-sharepoint-folder"></a>SharePoint フォルダーに新しいファイルが作成されたときにフローをトリガーします
**```GET: /datasets/{dataset}/triggers/onnewfile```** 

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |あり |path |なし |SharePoint サイトの URL |
| folderId |string |○ |query |なし |SharePoint のフォルダーの一意識別子 |

### <a name="here-are-the-possible-responses"></a>次のような応答があります。
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

- - -
### <a name="triggers-a-flow-when-a-file-is-modified-in-a-sharepoint-folder"></a>SharePoint フォルダーのファイルが変更されたときにフローをトリガーします
**```GET: /datasets/{dataset}/triggers/onupdatedfile```** 

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |あり |path |なし |SharePoint サイトの URL |
| folderId |string |○ |query |なし |SharePoint のフォルダーの一意識別子 |

### <a name="here-are-the-possible-responses"></a>次のような応答があります。
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

- - -
### <a name="used-for-extracting-a-folder-on-document-library"></a>ドキュメント ライブラリのフォルダーを抽出するために使用されます
**```POST: /datasets/{dataset}/extractFolderV2```** 

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |あり |path |なし |SharePoint サイトの URL。 例: http://contoso.sharepoint.com/sites/mysite |
| source セクション |string |○ |query |なし |ソース ファイルのパス |
| destination |string |○ |query |なし |宛先フォルダーのパス |
| overwrite |ブール値 |× |query |false |既存のファイルを上書きするかどうか |

### <a name="here-are-the-possible-responses"></a>次のような応答があります。
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

- - -
### <a name="when-a-new-item-is-created-in-a-sharepoint-list"></a>SharePoint リストに新しい項目が作成されたとき
**```GET: /datasets/{dataset}/tables/{table}/onnewitems```** 

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |あり |path |なし |SharePoint サイト URL (例: http://contoso.sharepoint.com/sites/mysite) |
| テーブル |string |あり |path |なし |SharePoint リストの名前 |
| $skip |integer |× |query |なし |スキップするエントリの数 (既定値 = 0) |
| $top |integer |× |query |なし |取得するエントリの最大数 (既定値 = 256) |
| $filter |string |× |query |なし |エントリ数を制限する ODATA filter クエリ |
| $orderby |string |× |query |なし |エントリの順序を指定する ODATA orderBy クエリ |

### <a name="here-are-the-possible-responses"></a>次のような応答があります。
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

- - -
### <a name="when-an-existing-item-is-modified-in-a-sharepoint-list"></a>SharePoint リストの既存の項目が変更されたとき
**```GET: /datasets/{dataset}/tables/{table}/onupdateditems```** 

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |あり |path |なし |SharePoint サイト URL (例: http://contoso.sharepoint.com/sites/mysite) |
| テーブル |string |あり |path |なし |SharePoint リストの名前 |
| $skip |integer |× |query |なし |スキップするエントリの数 (既定値 = 0) |
| $top |integer |× |query |なし |取得するエントリの最大数 (既定値 = 256) |
| $filter |string |× |query |なし |エントリ数を制限する ODATA filter クエリ |
| $orderby |string |× |query |なし |エントリの順序を指定する ODATA orderBy クエリ |

### <a name="here-are-the-possible-responses"></a>次のような応答があります。
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

- - -
### <a name="creates-a-new-item-in-a-sharepoint-list"></a>SharePoint リストに新しい項目を作成します
**```POST: /datasets/{dataset}/tables/{table}/items```** 

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |あり |path |なし |SharePoint サイト URL (例: http://contoso.sharepoint.com/sites/mysite) |
| テーブル |string |あり |path |なし |SharePoint リストの名前 |
| item | |あり |body |なし |作成する項目 |

### <a name="here-are-the-possible-responses"></a>次のような応答があります。
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

- - -
### <a name="retrieves-a-single-item-from-a-sharepoint-list"></a>SharePoint リストから&1; つの項目を取得します
**```GET: /datasets/{dataset}/tables/{table}/items/{id}```** 

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |あり |path |なし |SharePoint サイト URL (例: http://contoso.sharepoint.com/sites/mysite) |
| テーブル |string |あり |path |なし |SharePoint リストの名前 |
| id |integer |あり |path |なし |取得する項目の一意識別子 |

### <a name="here-are-the-possible-responses"></a>次のような応答があります。
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

- - -
### <a name="deletes-an-item-from-a-sharepoint-list"></a>SharePoint リストから項目を削除します
**```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```** 

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |あり |path |なし |SharePoint サイト URL (例: http://contoso.sharepoint.com/sites/mysite) |
| テーブル |string |あり |path |なし |SharePoint リストの名前 |
| id |integer |あり |path |なし |削除する項目の一意識別子 |

### <a name="here-are-the-possible-responses"></a>次のような応答があります。
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

- - -
### <a name="updates-an-item-in-a-sharepoint-list"></a>SharePoint リストの&1; つの項目を更新します
**```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```** 

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |あり |path |なし |SharePoint サイト URL (例: http://contoso.sharepoint.com/sites/mysite) |
| テーブル |string |あり |path |なし |SharePoint リストの名前 |
| id |integer |あり |path |なし |更新する項目の一意識別子 |
| item | |あり |body |なし |変更されたプロパティがある項目 |

### <a name="here-are-the-possible-responses"></a>次のような応答があります。
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

- - -
## <a name="object-definitions"></a>オブジェクト定義:
 **DataSetsMetadata**:

DataSetsMetadata の必須プロパティ:

必須のプロパティはありません。 

**すべてのプロパティ**: 

| 名前 | データ型 |
| --- | --- |
| tabular |未定義 |
| BLOB |未定義 |

 **TabularDataSetsMetadata**:

TabularDataSetsMetadata の必須プロパティ:

必須のプロパティはありません。 

**すべてのプロパティ**: 

| 名前 | データ型 |
| --- | --- |
| source |string |
| displayName |string |
| urlEncoding |string |
| tableDisplayName |string |
| tablePluralName |string |

 **BlobDataSetsMetadata**:

BlobDataSetsMetadata の必須プロパティ:

必須のプロパティはありません。 

**すべてのプロパティ**: 

| 名前 | データ型 |
| --- | --- |
| source |string |
| displayName |string |
| urlEncoding |string |

 **BlobMetadata**:

BlobMetadata の必須プロパティ:

必須のプロパティはありません。 

**すべてのプロパティ**: 

| 名前 | データ型 |
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

 **Object**:

Object の必須プロパティ:

必須のプロパティはありません。 

**すべてのプロパティ**: 

| 名前 | データ型 |
| --- | --- |
|  | |

 **TableMetadata**:

TableMetadata の必須プロパティ:

必須のプロパティはありません。 

**すべてのプロパティ**: 

| 名前 | データ型 |
| --- | --- |
| 名前 |string |
| title |string |
| x-ms-permission |string |
| schema |未定義 |

 **DataSetsList**:

DataSetsList の必須プロパティ:

必須のプロパティはありません。 

**すべてのプロパティ**: 

| 名前 | データ型 |
| --- | --- |
| 値 |array |

 **DataSet**:

DataSet の必須プロパティ:

必須のプロパティはありません。 

**すべてのプロパティ**: 

| 名前 | データ型 |
| --- | --- |
| 名前 |string |
| displayName |string |

 **テーブル**:

Table の必須プロパティ:

必須のプロパティはありません。 

**すべてのプロパティ**: 

| 名前 | データ型 |
| --- | --- |
| 名前 |string |
| displayName |string |

 **Item**:

Item の必須プロパティ:

必須のプロパティはありません。 

**すべてのプロパティ**: 

| 名前 | データ型 |
| --- | --- |
| ItemInternalId |string |

 **ItemsList**:

ItemsList の必須プロパティ:

必須のプロパティはありません。 

**すべてのプロパティ**: 

| 名前 | データ型 |
| --- | --- |
| 値 |array |

 **TablesList**:

TablesList の必須プロパティ:

必須のプロパティはありません。 

**すべてのプロパティ**: 

| 名前 | データ型 |
| --- | --- |
| 値 |array |

## <a name="next-steps"></a>次のステップ
[ロジック アプリを作成します](../logic-apps/logic-apps-create-a-logic-app.md)  

[1]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig1.png  
[2]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ../../includes/media/connectors-create-api-sharepointonline/connectionconfig5.png

