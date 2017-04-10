---
title: OneDrive for Business | Microsoft Docs
description: "Azure App Service を使用してロジック アプリを作成します。 OneDrive for Business に接続してファイルを管理します。 ファイルのアップロード、更新、取得、削除など、多様なアクションを実行できます。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: cf9484e9-7a20-4de0-93c8-0fa132221f2b
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 992b4b7bab8878355cbe936b75768e67b086286e
ms.lasthandoff: 01/20/2017


---
# <a name="get-started-with-the-onedrive-for-business-connector"></a>OneDrive for Business コネクタの使用
OneDrive for Business に接続してファイルを管理します。 ファイルのアップロード、更新、取得、削除など、多様なアクションを実行できます。

> [!NOTE]
> 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。
> 
> 

まず、ロジック アプリを作成します。[ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)に関する記事を参照してください。

## <a name="triggers-and-actions"></a>トリガーとアクション
OneDrive for Business コネクタは、アクションとして使用できます。OneDrive for Business コネクタにはトリガーがあります。 すべてのコネクタは、JSON および XML 形式のデータに対応します。

 OneDrive for Business コネクタでは、次のアクションやトリガーを使用できます。

### <a name="onedrive-for-business-actions"></a>OneDrive for Business のアクション
実行できるアクションは以下のとおりです。

| アクション | 説明 |
| --- | --- |
| [GetFileMetadata](connectors-create-api-onedriveforbusiness.md#getfilemetadata) |ID を使用して、OneDrive for Business のファイルのメタデータを取得します |
| [UpdateFile](connectors-create-api-onedriveforbusiness.md#updatefile) |OneDrive for Business のファイルを更新します |
| [DeleteFile](connectors-create-api-onedriveforbusiness.md#deletefile) |OneDrive for Business からファイルを削除します |
| [GetFileMetadataByPath](connectors-create-api-onedriveforbusiness.md#getfilemetadatabypath) |パスを使用して、OneDrive for Business のファイルのメタデータを取得します |
| [GetFileContentByPath](connectors-create-api-onedriveforbusiness.md#getfilecontentbypath) |パスを使用して、OneDrive for Business のファイルの内容を取得します |
| [GetFileContent](connectors-create-api-onedriveforbusiness.md#getfilecontent) |ID を使用して、OneDrive for Business のファイルの内容を取得します |
| [CreateFile](connectors-create-api-onedriveforbusiness.md#createfile) |OneDrive for Business にファイルをアップロードします |
| [CopyFile](connectors-create-api-onedriveforbusiness.md#copyfile) |OneDrive for Business にファイルをコピーします |
| [ListFolder](connectors-create-api-onedriveforbusiness.md#listfolder) |OneDrive for Business フォルダーのファイルを一覧表示します |
| [ListRootFolder](connectors-create-api-onedriveforbusiness.md#listrootfolder) |OneDrive for Business ルート フォルダーのファイルを一覧表示します |
| [ExtractFolderV2](connectors-create-api-onedriveforbusiness.md#extractfolderv2) |OneDrive for Business にフォルダーを抽出します |

### <a name="onedrive-for-business-triggers"></a>OneDrive for Business のトリガー
次のイベントをリッスンできます。

| トリガー | Description |
| --- | --- |
| ファイルの作成時 |OneDrive for Business フォルダーに新しいファイルが作成されたときにフローをトリガーします |
| ファイルの変更時 |OneDrive for Business フォルダー内のファイルが変更されたときにフローをトリガーします |

## <a name="create-a-connection-to-onedrive-for-business"></a>OneDrive for Business への接続の作成
OneDrive for Business を使用してロジック アプリを作成するには、まず**接続**を作成してから、次のプロパティの詳細を指定する必要があります。

| プロパティ | 必須 | 説明 |
| --- | --- | --- |
| トークン |はい |OneDrive for Business の資格情報を提供します |

接続を作成したら、その接続を使用してアクションを実行し、この記事で説明するトリガーをリッスンできます。

> [!INCLUDE [Steps to create a connection to OneDrive for Business](../../includes/connectors-create-api-onedriveforbusiness.md)]
> 
> [!TIP]
> 他のロジック アプリでもこの接続を使用できます。
> 
> 

## <a name="reference-for-onedrive-for-business"></a>OneDrive for Business のリファレンス
適用されるバージョン: 1.0

## <a name="getfilemetadata"></a>GetFileMetadata
ID を使用してファイルのメタデータを取得: ID を使用して、OneDrive for Business のファイルのメタデータを取得します

```GET: /datasets/default/files/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| id |string |あり |path |なし |ファイルを指定します |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## <a name="updatefile"></a>UpdateFile
ファイルの更新: OneDrive for Business のファイルを更新します

```PUT: /datasets/default/files/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| id |string |あり |path |なし |更新するファイルを指定します |
| body | |あり |body |なし |OneDrive for Business 内の更新するファイルの内容 |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## <a name="deletefile"></a>DeleteFile
ファイルの削除: OneDrive for Business からファイルを削除します

```DELETE: /datasets/default/files/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| id |string |あり |path |なし |削除するファイルを指定します |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## <a name="getfilemetadatabypath"></a>GetFileMetadataByPath
パスを使用してファイルのメタデータを取得: パスを使用して、OneDrive for Business のファイルのメタデータを取得します

```GET: /datasets/default/GetFileByPath```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| path |string |○ |query |なし |OneDrive for Business 内のファイルへの一意のパス |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## <a name="getfilecontentbypath"></a>GetFileContentByPath
パスを使用してファイルの内容を取得: パスを使用して、OneDrive for Business のファイルの内容を取得します

```GET: /datasets/default/GetFileContentByPath```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| path |string |○ |query |なし |OneDrive for Business 内のファイルへの一意のパス |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## <a name="getfilecontent"></a>GetFileContent
ID を使用してファイルの内容を取得: ID を使用して、OneDrive for Business のファイルの内容を取得します

```GET: /datasets/default/files/{id}/content```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| id |string |あり |path |なし |ファイルを指定します |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## <a name="createfile"></a>CreateFile
ファイルの作成: OneDrive for Business にファイルをアップロードします

```POST: /datasets/default/files```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| folderPath |string |○ |query |なし |ファイルを OneDrive for Business にアップロードするフォルダーのパス |
| name |string |○ |query |なし |OneDrive for Business で作成するファイルの名前 |
| body | |あり |body |なし |OneDrive for Business にアップロードするファイルの内容 |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## <a name="copyfile"></a>CopyFile
ファイルのコピー: OneDrive for Business にファイルをコピーします

```POST: /datasets/default/copyFile```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| source |string |○ |query |なし |ソース ファイルの URL |
| destination |string |○ |query |なし |対象ファイル名を含む OneDrive for Business の宛先ファイル パス |
| overwrite |ブール値 |× |query |false |’true’ に設定すると、宛先ファイルが上書きされます |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## <a name="onnewfile"></a>OnNewFile
ファイルの作成時: OneDrive for Business フォルダーに新しいファイルが作成されたときにフローをトリガーします

```GET: /datasets/default/triggers/onnewfile```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| folderId |string |○ |query |なし |フォルダーを指定します |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## <a name="onupdatedfile"></a>OnUpdatedFile
ファイルの変更時: OneDrive for Business フォルダー内のファイルが変更されたときにフローをトリガーします

```GET: /datasets/default/triggers/onupdatedfile```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| folderId |string |○ |query |なし |フォルダーを指定します |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## <a name="listfolder"></a>ListFolder
フォルダー内のファイルの一覧表示: OneDrive for Business フォルダーのファイルを一覧表示します

```GET: /datasets/default/folders/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| id |string |あり |path |なし |フォルダーを指定します |

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## <a name="listrootfolder"></a>ListRootFolder
ルート フォルダーの一覧表示: OneDrive for Business ルート フォルダーのファイルを一覧表示します

```GET: /datasets/default/folders```

この呼び出しには、パラメーターはありません

#### <a name="response"></a>応答
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## <a name="extractfolderv2"></a>ExtractFolderV2
フォルダーの抽出: OneDrive for Business にフォルダーを抽出します

```POST: /datasets/default/extractFolderV2```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| source |string |○ |query |なし |アーカイブ ファイルのパス |
| destination |string |○ |query |なし |アーカイブの内容を抽出する OneDrive for Business のパス |
| overwrite |ブール値 |× |query |false |’true’ に設定すると、宛先ファイルが上書きされます |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## <a name="object-definitions"></a>オブジェクト定義
### <a name="datasetsmetadata"></a>DataSetsMetadata
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| tabular |未定義 |なし |
| BLOB |未定義 |なし |

### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| source セクション |string |なし |
| displayName |string |なし |
| urlEncoding |string |なし |
| tableDisplayName |string |なし |
| tablePluralName |string |なし |

### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| source セクション |string |なし |
| displayName |string |なし |
| urlEncoding |string |なし |

### <a name="blobmetadata"></a>BlobMetadata
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| id |string |なし |
| 名前 |string |なし |
| DisplayName |string |なし |
| パス |string |なし |
| LastModified |string |なし |
| サイズ |integer |なし |
| MediaType |string |なし |
| IsFolder |boolean |なし |
| ETag |string |なし |
| FileLocator |string |なし |

### <a name="object"></a>オブジェクト
## <a name="next-steps"></a>次のステップ
[ロジック アプリを作成します](../logic-apps/logic-apps-create-a-logic-app.md)


