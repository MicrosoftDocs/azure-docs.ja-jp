---
title: "Azure SQL Elastic Scale の FAQ | Microsoft Docs"
description: "Azure SQL Database の Elastic Scale に関してよく寄せられる質問。"
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: e60dde9c-bb7b-4f2f-b52c-bdb506d49fcb
ms.service: sql-database
ms.custom: multiple databases
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: ddove
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 2420d31c73fcb43e680edf2a95995e4ed4969236


---
# <a name="elastic-database-tools-faq"></a>エラスティック データベース ツールに関する FAQ
#### <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>シャードごとにシングルテナントはあるが、シャーディング キーがない場合、スキーマ情報にどのようにしてシャーディング キーを取り込むのですか。
スキーマ情報オブジェクトは、分割や結合といったシナリオの場合にのみ使用します。 アプリケーションがシングルテナントである場合は、Split Merge ツールは必要ないため、スキーマ情報オブジェクトを取り込む必要はありません。

#### <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>データベースをプロビジョニングし、既にシャード マップ マネージャーもあります。このデータベースをシャードとして登録するにはどうしたらいいですか。
「**[Elastic Database クライアント ライブラリを使用してアプリケーションに共有を追加する](sql-database-elastic-scale-add-a-shard.md)**」をご覧ください。 

#### <a name="how-much-do-elastic-database-tools-cost"></a>エラスティック データベースデータベース ツールにはどの程度のコストがかかりますか。
エラスティック データベース クライアント ライブラリの使用にコストは発生しません。 コストが発生するのは、シャードとシャード マップ マネージャーに使用した Azure SQL データベースと、Split Merge ツールにプロビジョニングした Web/worker ロールに対してのみです。

#### <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>別のサーバーからシャードを追加した場合に、資格情報が機能しないのはどうしてですか。
資格情報は「User ID=username@servername”,」の形式ではなく、単純に「User ID = username」を使用します。  また、「username」ログインがシャードで権限を持っていることを確認します。

#### <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>アプリケーションを起動するたびに、シャード マップ マネージャーを作成してシャードを取り込む必要がありますか。
必要ありません。シャード マップ マネージャー (たとえば **[ShardMapManagerFactory.CreateSqlShardMapManager](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager.aspx)**) の作成は 1 回だけの操作です。  アプリケーションは、アプリケーションの起動時に **[ShardMapManagerFactory.TryGetSqlShardMapManager()](http://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx)** 呼び出しを使用する必要があります。  アプリケーション ドメインごとにそのような呼び出しを 1 回行います。

#### <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>エラスティック データベース ツールの使い方について質問がある場合、どこに問い合わせればよいですか。
[Azure SQL Database](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)フォーラムからお問い合わせください。

#### <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>シャーディング キーを使用してデータベース接続を取得する場合でも、同じシャードの別のシャーディング キーでデータを照会できますか。  設計によって異なりますか。
Elastic Scale API を使用すると、シャーディング キーの接続データベースに接続できますが、シャーディング キーのフィルター機能は利用できません。  提供されるシャーディング キーの範囲を制限するには、必要に応じて、 **WHERE** 句をクエリに追加します。

#### <a name="can-i-use-a-different-azure-database-edition-for-each-shard-in-my-shard-set"></a>自分のシャード セットのシャードごとに異なる Azure データベース エディションを使用できますか。
できます。シャードは個別のデータベースであるため、あるシャードに Premium エディションを使用し、別のシャードに Standard エディションを使用できます。 さらに、シャードの有効期間中に、シャードのエディションを何度もスケール アップまたはスケール ダウンできます。

#### <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>分割や結合といった操作の実行時に、Split Merge ツールではデータベースがプロビジョニング (または削除) されますか。
されません。 **分割** 操作の場合、適切なスキーマを持ったターゲット データベースが存在し、シャード マップ マネージャーに登録されている必要があります。  **結合** 操作の場合、シャード マップ マネージャーからシャードを削除してから、データベースを削除する必要があります。

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]




<!--HONumber=Nov16_HO3-->


