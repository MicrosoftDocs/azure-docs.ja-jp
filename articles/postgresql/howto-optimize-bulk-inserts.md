---
title: 一括挿入を最適化する - Azure Database for PostgreSQL - Single Server
description: この記事では、Azure Database for PostgreSQL - Single Server での一括挿入操作を最適化する方法について説明します。
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 4d10f06577738364e3f4a0ea40221d37ebfb31c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86116286"
---
# <a name="optimize-bulk-inserts-and-use-transient-data-on-an-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - Single Server で一括挿入を最適化し、一時データを使用する 
この記事では、Azure Database for PostgreSQL サーバーでの一括挿入操作を最適化し、一時データを使用する方法について説明します。

## <a name="use-unlogged-tables"></a>ログ記録されないテーブルを使用する
一時データが関係するか、大きなデータセットを一括で挿入するワークロード操作を行う場合は、ログ記録されないテーブルの使用を検討してください。

ログ記録されないテーブルは、一括挿入を最適化するために効果的に使用できる PostgreSQL の機能です。 PostgreSQL では先書きログ (WAL) を使用します。 既定で、原子性と持続性が提供されます。 原子性、一貫性、分離性、持続性で ACID プロパティが構成されます。 

ログ記録されないテーブルに挿入することは、PostgreSQL でトランザクション ログへの書き込みなしで挿入が行われることを意味し、それ自体が I/O 操作となります。 その結果、これらのテーブルは、通常のテーブルよりも処理が大幅に高速になります。

ログ記録されないテーブルを作成するには、次のオプションを使用します。
- `CREATE UNLOGGED TABLE <tableName>` という構文を使用して、新しいログ記録されないテーブルを作成します。
- `ALTER TABLE <tableName> SET UNLOGGED` という構文を使用して、既存のログ記録されるテーブルをログ記録されないテーブルに変換します。  

プロセスを元に戻すには、`ALTER TABLE <tableName> SET LOGGED` という構文を使用します。

## <a name="unlogged-table-tradeoff"></a>ログ記録されないテーブルのトレードオフ
ログ記録されないテーブルは、クラッシュ セーフではありません。 ログ記録されないテーブルはクラッシュ後に自動的に切り捨てられるか、不完全なシャットダウンの対象になります。 ログ記録されないテーブルの内容は、スタンバイ サーバーにレプリケートされることもありません。 ログ記録されないテーブルに対して作成されたインデックスも、ログに自動的に記録されることはありません。 挿入操作が完了した後、テーブルをログ記録されるテーブルに変換して、挿入を持続的なものにします。

一部のお客様のワークロードでは、ログ記録されないテーブルの使用時に約 15% から 20% にパフォーマンスが向上しています。

## <a name="next-steps"></a>次のステップ
一時データと大規模な一括挿入の使用について、ワークロードを見直します。 次の PostgreSQL のドキュメントを参照してください。
 
- [Create Table SQL コマンド](https://www.postgresql.org/docs/current/static/sql-createtable.html)
