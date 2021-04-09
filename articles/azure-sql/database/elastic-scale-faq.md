---
title: Elastic Scale に関する FAQ
description: Azure SQL Database の Elastic Scale に関してよく寄せられる質問。
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 51e15a8dc5e9f918c630397d6d6593f5bf561755
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92786906"
---
# <a name="elastic-database-tools-frequently-asked-questions-faq"></a>エラスティック データベース ツールに関してよく寄せられる質問 (FAQ)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>シャードごとにシングルテナントはあるが、シャーディング キーがない場合、スキーマ情報にどのようにしてシャーディング キーを取り込むのですか

スキーマ情報オブジェクトは、分割や結合といったシナリオの場合にのみ使用します。 アプリケーションがシングルテナントである場合は、Split Merge ツールは必要ないため、スキーマ情報オブジェクトを取り込む必要はありません。

## <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>データベースをプロビジョニングし、既にシャード マップ マネージャーもあります。このデータベースをシャードとして登録するにはどうしたらいいですか

[エラスティック データベース クライアント ライブラリを使用した、アプリケーションへのシャードの追加](elastic-scale-add-a-shard.md)に関するページを参照してください。

## <a name="how-much-do-elastic-database-tools-cost"></a>エラスティック データベース ツールにはどの程度のコストがかかりますか

エラスティック データベース クライアント ライブラリの使用にコストは発生しません。 コストが発生するのは、シャードとシャード マップ マネージャーに使用した Azure SQL Database のデータベースと、Split Merge ツールにプロビジョニングした Web または worker ロールに対してのみです。

## <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>別のサーバーからシャードを追加した場合に、資格情報が機能しないのはどうしてですか

資格情報には、"User ID=username@servername" の形式ではなく、単に "User ID = username" を使ってください。  また、「username」ログインがシャードで権限を持っていることを確認します。

## <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>アプリケーションを起動するたびに、シャード マップ マネージャーを作成してシャードを取り込む必要がありますか

いいえ。シャード マップ マネージャー (たとえば [ShardMapManagerFactory.CreateSqlShardMapManager](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)) の作成は 1 回だけの操作です。  アプリケーションは、アプリケーションの起動時に [ShardMapManagerFactory.TryGetSqlShardMapManager()](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) 呼び出しを使用する必要があります。  アプリケーション ドメインごとにそのような呼び出しを 1 回行います。

## <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>エラスティック データベース ツールの使い方について質問がある場合、どこに問い合わせればよいですか

[SQL Database に関する Microsoft Q&A 質問ページ](/answers/topics/azure-sql-database.html)から Microsoft にご連絡ください。

## <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>シャーディング キーを使用してデータベース接続を取得する場合でも、同じシャードの別のシャーディング キーでデータを照会できますか。  設計によって異なりますか

Elastic Scale API を使用すると、シャーディング キーの接続データベースに接続できますが、シャーディング キーのフィルター機能は利用できません。  提供されるシャーディング キーの範囲を制限するには、必要に応じて、 **WHERE** 句をクエリに追加します。

## <a name="can-i-use-a-different-sql-database-edition-for-each-shard-in-my-shard-set"></a>シャード セット内のシャードごとに異なる SQL Database エディションを使用できますか

できます。シャードは個別のデータベースであるため、あるシャードに Premium エディションを使用し、別のシャードに Standard エディションを使用できます。 さらに、シャードの有効期間中に、シャードのエディションを何度もスケール アップまたはスケール ダウンできます。

## <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>分割や結合などの操作の実行時に、Split Merge ツールではデータベースがプロビジョニング (または削除) されますか

いいえ。 **分割** 操作の場合、適切なスキーマを持ったターゲット データベースが存在し、シャード マップ マネージャーに登録されている必要があります。  **結合** 操作の場合、シャード マップ マネージャーからシャードを削除してから、データベースを削除する必要があります。

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]