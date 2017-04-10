---
title: "Box コネクタをロジック アプリに追加する | Microsoft Docs"
description: "Box コネクタと REST API パラメーターの概要"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 949579cf-a81c-4790-9ef5-fe39b4fbd0c5
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: bb6bef230f4ba4f7019fd2f120c2115d97649b81
ms.lasthandoff: 02/16/2017


---
# <a name="get-started-with-the-box-connector"></a>Box コネクタの使用
Box に接続し、ファイルの作成、削除などを行います。 

> [!NOTE]
> 本記事は、ロジック アプリの 2015-08-01-preview スキーマ バージョンを対象としています。
> 
> 

Box では、次の操作を実行できます。

* Box から取得したデータに基づいてビジネス フローを構築できます。 
* ファイルを作成または更新するときにトリガーを使用できます。
* ファイルのコピー、ファイルの削除などのアクションを使用できます。 また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。 たとえば、Box でファイルを変更するときに、Office 365 でそのファイルを取得して電子メールで送信することができます。

ロジック アプリに操作を追加する方法については、「 [SaaS サービスを接続する新しいロジック アプリの作成](../logic-apps/logic-apps-create-a-logic-app.md)」をご覧ください。

## <a name="triggers-and-actions"></a>トリガーとアクション
Box には、次のトリガーとアクションがあります。

| トリガー | アクション |
| --- | --- |
| <ul><li>ファイルの作成時</li><li>ファイルの変更時</li></ul> |<ul><li>ファイルを作成する</li><li>ファイルの作成時</li><li>ファイルをコピーする</li><li>ファイルを削除する</li><li>アーカイブをフォルダーに抽出する</li><li>ID を使用してファイルの内容を取得する</li><li>パスを使用してファイルの内容を取得する</li><li>ID を使用してファイルのメタデータを取得する</li><li>パスを使用してファイルのメタデータを取得する</li><li>ファイルを更新する</li><li>ファイルの変更時</li></ul> |

すべてのコネクタは、JSON および XML 形式のデータに対応します。

## <a name="create-a-connection-to-box"></a>Box への接続を作成する
ロジック アプリにこのコネクタを追加するとき、Box に接続するロジック アプリを承認する必要があります。

> [!INCLUDE [Steps to create a connection to box](../../includes/connectors-create-api-box.md)]
> 
> 

接続を作成したら、Box のプロパティを入力します。 これらのプロパティについては、このトピックの **REST API リファレンス** をご覧ください。

> [!TIP]
> 他のロジック アプリでも、この同じ Box 接続を使用できます。
> 
> 

## <a name="swagger-rest-api-reference"></a>Swagger REST API リファレンス
適用されるバージョン: 1.0。

### <a name="create-file"></a>ファイルを作成する
Box にファイルをアップロードします。  
```POST: /datasets/default/files```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| folderPath |string |あり |query |なし |ファイルを Box にアップロードするフォルダーのパス |
| 名前 |string |あり |query |なし |Box で作成するファイルの名前 |
| body |string (binary) |あり |body |なし |Box にアップロードするファイルの内容 |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### <a name="when-a-file-is-created"></a>ファイルの作成時
Box フォルダーに新しいファイルが作成されたときにフローをトリガーします。  
```GET: /datasets/default/triggers/onnewfile```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| folderId |string |あり |query |なし |Box のフォルダーの一意識別子 |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### <a name="copy-file"></a>ファイルをコピーする
ファイルを Box にコピーします。  
```POST: /datasets/default/copyFile```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| source |string |あり |query |なし |ソース ファイルの URL |
| destination |string |あり |query |なし |対象ファイル名を含む Box の宛先ファイル パス |
| overwrite |ブール値 |いいえ |query |なし |’true’ に設定すると、宛先ファイルが上書きされます |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### <a name="delete-file"></a>ファイルを削除する
Box からファイルを削除します。  
```DELETE: /datasets/default/files/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| id |string |あり |path |なし |Box から削除するファイルの一意識別子 |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### <a name="extract-archive-to-folder"></a>アーカイブをフォルダーに抽出する
Box のフォルダーにアーカイブ ファイル (例: .zip) を抽出します。  
```POST: /datasets/default/extractFolderV2```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| source |string |あり |query | |アーカイブ ファイルのパス |
| destination |string |あり |query | |アーカイブの内容を抽出する Box 内のパス |
| overwrite |ブール値 |いいえ |query | |’true’ に設定すると、宛先ファイルが上書きされます |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### <a name="get-file-content-using-id"></a>ID を使用してファイルの内容を取得する
ID を使用して Box からファイルの内容を取得します。  
```GET: /datasets/default/files/{id}/content```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| id |string |あり |path |なし |Box 内のファイルの一意識別子 |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### <a name="get-file-content-using-path"></a>パスを使用してファイルの内容を取得する
パスを使用して Box からファイルの内容を取得します。  
```GET: /datasets/default/GetFileContentByPath```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| path |string |あり |query |なし |Box 内のファイルの一意のパス |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### <a name="get-file-metadata-using-id"></a>ID を使用してファイルのメタデータを取得する
ファイル ID を使用して Box からファイルのメタデータを取得します。  
```GET: /datasets/default/files/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| id |string |あり |path |なし |Box 内のファイルの一意識別子 |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### <a name="get-file-metadata-using-path"></a>パスを使用してファイルのメタデータを取得する
パスを使用して Box からファイルのメタデータを取得します。  
```GET: /datasets/default/GetFileByPath```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| path |string |あり |query |なし |Box 内のファイルの一意のパス |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### <a name="update-file"></a>ファイルを更新する
Box 内のファイルを更新します。  
```PUT: /datasets/default/files/{id}```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| id |string |あり |path |なし |Box 内の更新するファイルの一意識別子 |
| body |string (binary) |あり |body |なし |Box 内の更新するファイルの内容 |

#### <a name="response"></a>Response
| 名前 | 説明 |
| --- | --- |
| 200 |OK |
| default |操作に失敗しました。 |

### <a name="when-a-file-is-modified"></a>ファイルの変更時
Box フォルダー内のファイルが変更されたときにフローをトリガーします。  
```GET: /datasets/default/triggers/onupdatedfile```

| 名前 | データ型 | 必須 | 場所 | 既定値 | 説明 |
| --- | --- | --- | --- | --- | --- |
| folderId |string |あり |query |なし |Box のフォルダーの一意識別子 |

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

## <a name="next-steps"></a>次のステップ
[ロジック アプリを作成](../logic-apps/logic-apps-create-a-logic-app.md)します。


