---
title: "ロジック アプリに Google Drive コネクタを追加する | Microsoft Docs"
description: "Google Drive コネクタと REST API パラメーターの概要"
services: 
suite: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: b2bcebc5-02d2-435b-b0da-ef53bc51c4b6
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 497f963870d24a335379a7f015a515c425737a73
ms.lasthandoff: 02/16/2017


---
# <a name="get-started-with-the-google-drive-connector"></a>Google Drive コネクタの使用
Google Drive に接続して、ファイルの作成、行の取得などを行います。 Google Drive では、次の操作を実行できます。 

* 検索から取得したデータに基づいてビジネス フローを構築することができます。 
* 画像、ニュースなどを検索するアクションを使用できます。 また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。 たとえば、動画を検索し、Twitter を使用して Twitter フィードに動画を投稿することができます。

ロジック アプリに操作を追加する方法については、 [ロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)に関するページを参照してください。

## <a name="triggers-and-actions"></a>トリガーとアクション
Google Drive には、次のアクションがあります。 トリガーはありません。 

| トリガー | アクション |
| --- | --- |
| なし |<ul><li>ファイルを作成する</li><li>行を挿入する</li><li>ファイルをコピーする</li><li>ファイルを削除する</li><li>行を削除する</li><li>アーカイブをフォルダーに抽出する</li><li>ID を使用してファイルの内容を取得する</li><li>パスを使用してファイルの内容を取得する</li><li>ID を使用してファイルのメタデータを取得する</li><li>パスを使用してファイルのメタデータを取得する</li><li>行を取得する</li><li>ファイルを更新する</li><li>行を更新する</li></ul> |

すべてのコネクタは、JSON および XML 形式のデータに対応します。

## <a name="create-the-connection-to-google-drive"></a>Google Drive への接続を作成する
ロジック アプリにこのコネクタを追加するとき、Google Drive に接続するロジック アプリを承認する必要があります。

> [!INCLUDE [Steps to create a connection to googledrive](../../includes/connectors-create-api-googledrive.md)]
> 
> 

接続を作成したら、フォルダー パスやファイル名など、Google Drive のプロパティを入力します。 これらのプロパティについては、このトピックの **REST API リファレンス** をご覧ください。

> [!TIP]
> 他のロジック アプリでも、この同じ Google Drive 接続を使用できます。
> 
> 

## <a name="swagger-rest-api-reference"></a>Swagger REST API リファレンス
適用されるバージョン: 1.0。

### <a name="create-file"></a>ファイルを作成する
Google Drive にファイルをアップロードします。  
```POST: /datasets/default/files```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| folderPath |string |○ |query |なし |ファイルを Google Drive にアップロードするフォルダーのパス |
| 名前 |string |○ |query |なし |Google Drive に作成するファイルの名前 |
| body |string (binary) |あり |body |なし |Google Drive にアップロードするファイルの内容 |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### <a name="insert-row"></a>行を挿入する
Google シートに行を挿入します。  
```POST: /datasets/{dataset}/tables/{table}/items```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |あり |path |なし |Google シートファイルの一意識別子 |
| テーブル |string |あり |path |なし |ワークシートの一意識別子 |
| item |ItemInternalId: string |あり |body |なし |指定されたシートに挿入する行 |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### <a name="copy-file"></a>ファイルをコピーする
Google Drive のファイルをコピーします。  
```POST: /datasets/default/copyFile```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| source |string |○ |query |なし |ソース ファイルの URL |
| destination |string |○ |query |なし |対象ファイル名を含む Google Drive の宛先ファイル パス |
| overwrite |ブール値 |× |query |なし |’true’ に設定すると、宛先ファイルが上書きされます |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### <a name="delete-file"></a>ファイルを削除する
Google Drive からファイルを削除します。  
```DELETE: /datasets/default/files/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| id |string |あり |path |なし |Google Drive から削除するファイルの一意識別子 |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### <a name="delete-row"></a>行を削除する
Google シートから行を削除します。  
```DELETE: /datasets/{dataset}/tables/{table}/items/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |あり |path |なし |Google シートファイルの一意識別子 |
| テーブル |string |あり |path |なし |ワークシートの一意識別子 |
| id |string |あり |path |なし |削除する行の一意識別子 |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### <a name="extract-archive-to-folder"></a>アーカイブをフォルダーに抽出する
Google Drive のフォルダーにアーカイブ ファイル (例: .zip) を抽出します。  
```POST: /datasets/default/extractFolderV2```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| source |string |○ |query |なし |アーカイブ ファイルのパス |
| destination |string |○ |query |なし |アーカイブの内容を抽出する Google Drive のパス |
| overwrite |ブール値 |× |query |なし |’true’ に設定すると、宛先ファイルが上書きされます |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### <a name="get-file-content-using-id"></a>ID を使用してファイルの内容を取得する
ID を使用して Google Drive からファイルの内容を取得します。  
```GET: /datasets/default/files/{id}/content```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| id |string |あり |path |なし |Google Drive 内の取得するファイルの一意識別子 |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### <a name="get-file-content-using-path"></a>パスを使用してファイルの内容を取得する
パスを使用して Google Drive からファイルの内容を取得します。  
```GET: /datasets/default/GetFileContentByPath```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| path |string |○ |query |なし |Google Drive 内のファイルのパス |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### <a name="get-file-metadata-using-id"></a>ID を使用してファイルのメタデータを取得する
ID を使用して Google Drive からファイルのメタデータを取得します。  
```GET: /datasets/default/files/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| id |string |あり |path |なし |Google Drive 内のファイルの一意識別子 |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### <a name="get-file-metadata-using-path"></a>パスを使用してファイルのメタデータを取得する
パスを使用して Google Drive からファイルのメタデータを取得します。  
```GET: /datasets/default/GetFileByPath```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| path |string |○ |query |なし |Google Drive 内のファイルのパス |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### <a name="get-row"></a>行を取得する
Google シートから&1; 行を取得します。  
```GET: /datasets/{dataset}/tables/{table}/items/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |あり |path |なし |Google シートファイルの一意識別子 |
| テーブル |string |あり |path |なし |ワークシートの一意識別子 |
| id |string |あり |path |なし |取得する行の一意識別子 |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### <a name="update-file"></a>ファイルを更新する
Google Drive 内のファイルを更新します。  
```PUT: /datasets/default/files/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| id |string |あり |path |なし |Google Drive 内の更新するファイルの一意識別子 |
| body |string (binary) |あり |body |なし |Google Drive にアップロードするファイルの内容 |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### <a name="update-row"></a>行を更新する
Google シート内の行を更新します。  
```PATCH: /datasets/{dataset}/tables/{table}/items/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| dataset |string |あり |path |なし |Google シートファイルの一意識別子 |
| テーブル |string |あり |path |なし |ワークシートの一意識別子 |
| id |string |あり |path |なし |更新する行の一意識別子 |
| item |ItemInternalId: string |あり |body |なし |更新された値のある行 |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

## <a name="object-definitions"></a>オブジェクト定義
#### <a name="datasetsmetadata"></a>DataSetsMetadata
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| tabular |未定義 |× |
| BLOB |未定義 |× |

#### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| source セクション |string |× |
| displayName |string |× |
| urlEncoding |string |× |
| tableDisplayName |string |× |
| tablePluralName |string |× |

#### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| source セクション |string |× |
| displayName |string |× |
| urlEncoding |string |× |

#### <a name="blobmetadata"></a>BlobMetadata
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| id |string |× |
| 名前 |string |× |
| displayName |string |× |
| path |string |× |
| LastModified |string |× |
| サイズ |integer |× |
| MediaType |string |× |
| IsFolder |ブール値 |× |
| ETag |string |× |
| FileLocator |string |× |

#### <a name="tablemetadata"></a>TableMetadata
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| 名前 |string |× |
| title |string |× |
| x-ms-permission |string |× |
| schema |未定義 |× |

#### <a name="tableslist"></a>TablesList
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| 値 |array |× |

#### <a name="table"></a>テーブル
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| 名前 |string |× |
| displayName |string |× |

#### <a name="item"></a>item
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| ItemInternalId |string |× |

#### <a name="itemslist"></a>ItemsList
| プロパティ名 | データ型 | 必須 |
| --- | --- | --- |
| 値 |array |× |

## <a name="next-steps"></a>次のステップ
[ロジック アプリを作成](../logic-apps/logic-apps-create-a-logic-app.md)します。

[API リスト](apis-list.md)に戻ります。

<!--References-->
[5]: https://console.developers.google.com/
[6]: ./media/connectors-create-api-googledrive/google-developers-console.png
[8]: ./media/connectors-create-api-googledrive/use-google-apis.png
[9]: ./media/connectors-create-api-googledrive/googledrive-api-overview.png
[10]: ./media/connectors-create-api-googledrive/enable-googledrive-api.png
[12]: ./media/connectors-create-api-googledrive/googledrive-api-credentials-add.png
[13]: ./media/connectors-create-api-googledrive/configure-consent-screen.png
[14]: ./media/connectors-create-api-googledrive/create-client-id.png

