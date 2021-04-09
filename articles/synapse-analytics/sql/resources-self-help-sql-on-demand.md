---
title: サーバーレス SQL プールのセルフヘルプ
description: このセクションには、サーバーレス SQL プールに関する問題のトラブルシューティングに役立つ情報が含まれています。
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: f94134b2a06155b1b1f390175578e501a840038b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101669423"
---
# <a name="self-help-for-serverless-sql-pool"></a>サーバーレス SQL プールのセルフヘルプ

この記事には、Azure Synapse Analytics のサーバーレス SQL プールでよく発生する問題のトラブルシューティング方法に関する情報が含まれています。

## <a name="serverless-sql-pool-is-grayed-out-in-synapse-studio"></a>Synapse Studio でサーバーレス SQL プールがグレー表示される

Synapse Studio がサーバーレス SQL プールへの接続を確立できない場合、サーバーレス SQL プールがグレー表示されるか、状態が "オフライン" と表示されます。 通常、この問題は次のいずれかの場合に発生します。

1) Azure Synapse バックエンドとの通信がネットワークによって妨げられている。 最も頻繁なケースは、ポート 1443 がブロックされているケースです。 サーバーレス SQL プールを動作させるには、このポートのブロックを解除します。 これ以外の問題が原因でサーバーレス SQL プールが動作しなくなることがあります。[詳細については、完全なトラブルシューティング ガイドを参照してください](../troubleshoot/troubleshoot-synapse-studio.md)。
2) サーバーレス SQL プールにログインするアクセス許可がない。 ユーザーがアクセスできるようにするには、Azure Synapse ワークスペース管理者がワークスペース管理者または SQL 管理者ロールにユーザーを追加する必要があります。 [詳細については、アクセス制御に関する完全なガイドを参照してください](../security/synapse-workspace-access-control-overview.md)。

## <a name="query-fails-because-file-cannot-be-opened"></a>ファイルを開くことができないため、クエリが失敗する

"File cannot be opened because it does not exist or it is used by another process (ファイルが存在しないか、別のプロセスで使用されているため、開くことができません)" というエラーでクエリが失敗したときに、ファイルが存在し、かつ別のプロセスで使用されていないことが確認されている場合は、サーバーレス SQL プールがファイルにアクセスできないことを意味します。 この問題は通常、ファイルにアクセスする権限が Azure Active Directory ID にないために発生します。 サーバーレス SQL プールでは、既定で、Azure Active Directory ID を使用してファイルへのアクセスを試みます。 この問題を解決するには、ファイルにアクセスするための適切な権限を持っている必要があります。 最も簡単な方法は、クエリの対象となるストレージ アカウントに対する "ストレージ BLOB データ共同作成者" ロールを自分に付与することです。 
- [詳細については、ストレージの Azure Active Directory アクセス制御に関する完全なガイドを参照してください](../../storage/common/storage-auth-aad-rbac-portal.md)。 
- [「Azure Synapse Analytics でサーバーレス SQL プールのストレージ アカウント アクセスを制御する」を参照してください](develop-storage-files-storage-access-control.md)。

## <a name="query-fails-because-it-cannot-be-executed-due-to-current-resource-constraints"></a>現在のリソース制約によりクエリを実行できないため、クエリが失敗する 

"This query can't be executed due to current resource constraints (現在のリソース制約のため、このクエリを実行できません)" というエラー メッセージでクエリが失敗する場合、リソースの制約により、サーバーレス SQL プールが現時点でクエリを実行できないことを意味します。 

- 適切なサイズのデータ型が使用されていることを確認してください。 また、文字列型の列の場合は既定で VARCHAR (8000) になるため、Parquet ファイルのスキーマを指定します。 

- 対象のクエリが CSV ファイルをターゲットとしている場合は、[統計を作成する](develop-tables-statistics.md#statistics-in-serverless-sql-pool)ことを検討してください。 

- クエリを最適化するには、[サーバーレス SQL プールのパフォーマンスのベスト プラクティス](best-practices-sql-on-demand.md)に関するページを参照してください。  

## <a name="create-statement-is-not-supported-in-master-database"></a>CREATE "ステートメント" はマスター データベースでサポートされていません。

次のエラー メッセージが表示され、クエリが失敗します。

> "クエリを実行できませんでした。 エラー:CREATE EXTERNAL TABLE/DATA SOURCE/DATABASE SCOPED CREDENTIAL/FILE FORMAT はマスター データベースでサポートされていません" 

つまり、サーバーレス SQL プールのマスター データベースでは次を作成できません。
  - 外部テーブル
  - 外部データ ソース
  - データベース スコープ資格情報
  - 外部ファイル形式

解決方法:

  1. ユーザー データベースの作成:

```sql
CREATE DATABASE <DATABASE_NAME>
```

  2. マスター データベースについて前に失敗した <DATABASE_NAME> のコンテキストで CREATE ステートメントを実行します。 
  
  外部ファイル形式の作成例:
    
```sql
USE <DATABASE_NAME>
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] 
WITH ( FORMAT_TYPE = PARQUET)
```

## <a name="next-steps"></a>次のステップ

サーバーレス SQL プールの使用方法について詳しくは、次の記事を参照してください。

- [単一の CSV ファイルに対するクエリを実行する](query-single-csv-file.md)

- [フォルダーと複数の CSV ファイルに対してクエリを実行する](query-folders-multiple-csv-files.md)

- [特定のファイルに対してクエリを実行する](query-specific-files.md)

- [Parquet ファイルに対してクエリを実行する](query-parquet-files.md)

- [Parquet の入れ子にされた型に対してクエリを実行する](query-parquet-nested-types.md)

- [JSON ファイルに対するクエリを実行する](query-json-files.md)

- [ビューの作成と使用](create-use-views.md)

- [外部テーブルの作成と使用](create-use-external-tables.md)

- [クエリ結果をストレージに格納する](create-external-table-as-select.md)
