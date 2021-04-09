---
title: Elastic Database クライアント ライブラリの資格情報を管理する
description: エラスティック データベース アプリの適切な資格情報のレベルを設定する方法 (管理者から読み取り専用)
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 5b91986d4f94861b87e413c9ff781107c3ed04a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92786600"
---
# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>Elastic Database クライアント ライブラリへのアクセスに使用する資格情報
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Elastic Database クライアント ライブラリ](elastic-database-client-library.md)では、3 種類の資格情報を使用して[シャード マップ マネージャー](elastic-scale-shard-map-management.md)にアクセスします。 必要に応じて、最低レベルのアクセスが可能な資格情報を使用します。

* **管理の資格情報**: シャード マップ マネージャーを作成または操作します ([用語集](elastic-scale-glossary.md)をご覧ください)。
* **アクセス資格情報**: 既存のシャード マップ マネージャーにアクセスして、シャードに関する情報を取得します。
* **接続の資格情報**: シャードに接続します。

「 [Azure SQL Database におけるデータベースとログインの管理](logins-create-manage.md)」も参照してください。

## <a name="about-management-credentials"></a>管理資格情報について

管理の資格情報は、シャード マップを操作するアプリケーションの **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager)、[.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)) オブジェクトを作成するときに使用されます (例については、「[Elastic Database ツールを使用してシャードを追加する](elastic-scale-add-a-shard.md)」および「[データ依存ルーティング](elastic-scale-data-dependent-routing.md)」をご覧ください)。 エラスティック スケール クライアント ライブラリのユーザーは、SQL ユーザーと SQL ログインを作成し、グローバル シャード マップ データベースとすべてのシャード データベースに対する読み取り/書き込みアクセス許可が各ユーザーに付与されていることを確認する必要があります。 これらの資格情報は、シャード マップに変更を加えるときにグローバル シャード マップとローカル シャード マップを維持するために使用されます。 たとえば、管理の資格情報を使用して、シャード マップ マネージャー オブジェクトを作成します (**GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager)、[.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)) を使用)。

```java
// Obtain a shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmAdminConnectionString,ShardMapManagerLoadPolicy.Lazy);
```

変数 **smmAdminConnectionString** は、管理資格情報を格納する接続文字列です。 ユーザー ID とパスワードにより、シャード マップ データベースと個々のシャードの両方に対する読み取り/書き込みアクセスが提供されます。 管理接続文字列には、グローバル シャード マップ データベースを識別するためのサーバー名とデータベース名も含まれます。 この処理に使用する一般的な接続文字列を次に示します。

```java
"Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;”
```

値の形式としては、"username@server" ではなく、単に "username" という値を使ってください。  これは、シャード マップ マネージャー データベースと個々のシャードの両方で資格情報を有効にする必要があり、それらが別々のサーバー上に存在する場合があるためです。

## <a name="access-credentials"></a>資格情報にアクセスする

シャード マップの管理用ではないアプリケーションでシャード マップ マネージャーを作成するときは、グローバル シャード マップに対する読み取り専用アクセス許可が与えられる資格情報を使用します。 これらの資格情報の下でグローバル シャード マップから取得される情報は、[データ依存ルーティング](elastic-scale-data-dependent-routing.md)の目的と、クライアント上のシャード マップ キャッシュを設定するために使用されます。 資格情報は、**GetSqlShardMapManager** と同じ呼び出しパターンを通じて提供されます。

```java
// Obtain shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmReadOnlyConnectionString, ShardMapManagerLoadPolicy.Lazy);  
```

ここでは、**管理者以外** のユーザーのアクセス用に使用されるさまざまな資格情報を反映するために **smmReadOnlyConnectionString** を使用しています。これらの資格情報では、グローバル シャード マップに対する書き込みアクセス許可は提供されません。

## <a name="connection-credentials"></a>接続の資格情報

**OpenConnectionForKey** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey)、[.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) メソッドを使用してシャーディング キーに関連付けられているシャードにアクセスするには、追加の資格情報が必要になります。 これらの資格情報は、シャード上に存在するローカル シャード マップ テーブルへの読み取り専用アクセス許可を提供する必要があります。 これは、シャード上でのデータ依存ルーティングのための接続検証を行う場合に必要になります。 次のコード スニペットは、データ依存ルーティングのコンテキストでデータ アクセスを許可します。

```csharp
using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>(targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate))
```

この例の **smmUserConnectionString** は、ユーザー資格情報の接続文字列を保持します。 Azure SQL Database に対するユーザー資格情報の一般的な接続文字列を次に示します。

```java
"User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  
```

管理者の資格情報と同じように、"username@server" という形式の値は使わないでください。 代わりに "username" のみを使用します。  接続文字列にはサーバー名とデータベース名が含まれていないことにも注意してください。 これは、**OpenConnectionForKey** の呼び出しによって接続がキーに基づいた正しいシャードに自動的に転送されるためです。 そのため、データベース名とサーバー名を指定しないでください。

## <a name="see-also"></a>関連項目

[Azure SQL Database におけるデータベースとログインの管理](logins-create-manage.md)

[SQL Database の保護](security-overview.md)

[Elastic Database ジョブ](elastic-jobs-overview.md)

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]