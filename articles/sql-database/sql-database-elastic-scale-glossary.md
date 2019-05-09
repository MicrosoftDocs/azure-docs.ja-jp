---
title: Elastic Database ツールの用語集 | Microsoft Docs
description: エラスティック データベース ツールで使用される用語の説明
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 446203b45744a95c32cd41d9ded26fd960ac8a22
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2019
ms.locfileid: "55561435"
---
# <a name="elastic-database-tools-glossary"></a>Elastic Database ツールの用語集

Azure SQL Database の機能である [Elastic Database ツール](sql-database-elastic-scale-introduction.md)に関する用語の定義を次に示します。 このツールは、[シャード マップ](sql-database-elastic-scale-shard-map-management.md)の管理に使用するものであり、[クライアント ライブラリ](sql-database-elastic-database-client-library.md)、[分割/マージ ツール](sql-database-elastic-scale-overview-split-and-merge.md)、[エラスティック プール](sql-database-elastic-pool.md)、および[クエリ](sql-database-elastic-query-overview.md)が含まれます。 

これらの用語は、「[Elastic Database ツールを使用してシャードを追加する](sql-database-elastic-scale-add-a-shard.md)」と「[RecoveryManager クラスを使用したシャード マップに関する問題の解決](sql-database-elastic-database-recovery-manager.md)」で使用されています。

![Elastic Scale の用語][1]

**データベース**:Azure SQL Database。 

**データ依存ルーティング**:アプリケーションが特定のシャーディング キーを持つシャードに接続することを可能にする機能。 「 [データ依存ルーティング](sql-database-elastic-scale-data-dependent-routing.md)」をご覧ください。 **[マルチシャード クエリ](sql-database-elastic-scale-multishard-querying.md)** と比較してください。

**グローバル シャード マップ**:**シャード セット**内のシャーディング キーとそれに対応するシャードの間のマップ。 グローバル シャード マップは、 **シャード マップ マネージャー**に格納されます。 **ローカル シャード マップ**も参照。

**リスト シャード マップ**:シャーディング キーが個別にマップされたシャード マップ。 **範囲シャード マップ**も参照。   

**ローカル シャード マップ**:ローカル シャード マップは、シャードに格納され、シャードに存在するシャードレットのマッピングを含みます。

**マルチシャード クエリ**:複数のシャードに対してクエリを発行する機能。結果セットは、UNION ALL セマンティクス (「ファンアウト クエリ」とも呼ばれます) を使用して返されます。 **data dependent routing**が含まれます。

**マルチテナント**と**シングルテナント**:これは、シングルテナント データベースとマルチテナント データベースを示します。

![Single and multi-tenant databases](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

次に、 **シャード化された** シングルテナント データベースとマルチテナント データベースを示します。 

![Single and multi-tenant databases](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**範囲シャード マップ**:連続値の複数の範囲に基づくシャード分散戦略を持つシャード マップ。 

**参照テーブル**:シャード化されず、シャード間で複製されるテーブル。 たとえば、郵便番号を参照テーブルに格納できます。 

**シャード**:シャード化されたデータ セットのデータを格納する Azure SQL データベース。 

**シャードの弾力性**:**水平スケーリング**と**垂直スケーリング**の両方を実行する機能。

**シャード化テーブル**:シャード化されたテーブル。つまり、データがシャーディング キー値に基づいて複数のシャードに分散されたテーブル。 

**シャーディング キー**:シャード間でデータを分散する方法を決定する値です。 値の型には、**int**、**bigint**、**varbinary**、**uniqueidentifier** のいずれかを使用できます。 

**シャード セット**:シャード マップ マネージャーの同じシャード マップに属するシャードのコレクション。  

**シャードレット**:シャード上のシャーディング キーの単一の値に関連付けられたデータのすべて。 シャードレットは、シャード化テーブルを再分散する際に使用できる最小データ移動単位です。 

**シャード マップ**:シャーディング キーとそれに対応するシャードの間のマッピングのセット。

**シャード マップ マネージャー**:1 つまたは複数のシャード セットのシャード マップ、シャードの場所、およびマッピングを含む管理オブジェクトおよびデータ ストア。

![マッピング][2]

## <a name="verbs"></a>動詞
**水平スケーリング**:シャード マップに対するシャードの追加や削除を行ってシャードのコレクションをスケール アウト (またはスケール イン) する操作 (下図参照)。

![水平および垂直方向のスケーリング][3]

**マージ**:2 つのシャードから 1 つのシャードにシャードレットを移動し、それに応じてシャード マップを更新する操作。

**シャードレットの移動**:1 つのシャードレットを異なるシャードに移動する操作。 

**シャード**:同じ構造を持つデータをシャーディング キーに基づいて複数のデータベースに水平方向にパーティション分割する操作。

**分割**:1 つのシャードから別の (通常は新しい) シャードに複数のシャードレットを移動する操作。 シャーディング キーは分割ポイントとしてユーザーから提供されます。

**垂直スケーリング**:個々のシャードのコンピューティング サイズをスケール アップ (またはダウン) する操作。 たとえば、シャードを Standard から Premium に変更 (結果、コンピューティング リソースが増大)。 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

