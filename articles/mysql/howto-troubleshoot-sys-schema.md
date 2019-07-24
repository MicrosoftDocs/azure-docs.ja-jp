---
title: Azure Database for MySQL でパフォーマンスのチューニングとデータベースのメンテナンスに sys_schema を使用する方法
description: この記事では、sys_schema を使ってパフォーマンスの問題を見つけ、Azure Database for MySQL のデータベースを保守する方法について説明します。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 08/01/2018
ms.openlocfilehash: 993c77056c09c1dc21d5317ddbfe8e937341718d
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53542851"
---
# <a name="how-to-use-sysschema-for-performance-tuning-and-database-maintenance-in-azure-database-for-mysql"></a>Azure Database for MySQL でパフォーマンスのチューニングとデータベースのメンテナンスに sys_schema を使用する方法

MySQL 5.5 で初めて導入された MySQL performance_schema では、メモリ割り当て、ストアド プログラム、メタデータ ロックなど、多くの重要なサーバー リソースのためのインストルメンテーションが提供されています。ただし、performance_schema には 80 以上のテーブルが含まれ、必要な情報を入手するには performance_schema 内のテーブルや information_schema のテーブルの結合が必要になることがよくあります。 performance_schema と information_schema の両方を基にして作成されている sys_schema は、読み取り専用のデータベースで[ユーザー フレンドリなビュー](https://dev.mysql.com/doc/refman/5.7/en/sys-schema-views.html)の強力なコレクションを提供し、Azure Database for MySQL バージョン 5.7 では完全に有効になっています。

![sys_schema のビュー](./media/howto-troubleshoot-sys-schema/sys-schema-views.png)

sys_schema には 52 個のビューがあり、各ビューには次のいずれかのプレフィックスが付いています。

- host_summary または io: I/O 関連の待機時間。
- innoDB: InnoDB バッファーの状態とロック。
- メモリ:ホストとユーザーによるメモリ使用量。
- スキーマ:自動インクリメントやインデックスなどのスキーマ関連の情報。
- statement: SQL ステートメントに関する情報。フル テーブル スキャンや長いクエリ時間が発生するステートメントである場合があります。
- ユーザー:消費され、ユーザーごとにグループ化されたリソース。 ファイル I/O、接続、メモリなどです。
- wait: ホストまたはユーザーごとにグループ化された待機イベント。

次に、sys_schema の一般的な使用パターンについて説明します。 まず、使用パターンを**パフォーマンスのチューニング**と**データベース メンテナンス**の 2 つのカテゴリにグループ化します。

## <a name="performance-tuning"></a>パフォーマンスのチューニング

### <a name="sysusersummarybyfileio"></a>*sys.user_summary_by_file_io*

IO は、データベースで最もコストのかかる操作です。 *sys.user_summary_by_file_io* ビューのクエリを行うことにより、平均 IO 待機時間がわかります。 既定値の 125 GB でプロビジョニングされたこの例のストレージでは、IO 待機時間は約 15 秒です。

![io latency: 125 GB](./media/howto-troubleshoot-sys-schema/io-latency-125GB.png)

Azure Database for MySQL ではストレージに応じて IO が増減するので、このプロビジョニングされたストレージを 1 TB に増やすと、IO 待機時間は 571 ミリ秒に減ります。

![io latency: 1 TB](./media/howto-troubleshoot-sys-schema/io-latency-1TB.png)

### <a name="sysschematableswithfulltablescans"></a>*sys.schema_tables_with_full_table_scans*

慎重に計画しても、多くのクエリでフル テーブル スキャンが行われる可能性があります。 インデックスの種類とそれらを最適化する方法の詳細については、[クエリのパフォーマンスをトラブルシューティングする方法](./howto-troubleshoot-query-performance.md)に関する記事を参照できます。 フル テーブル スキャンはリソースを大量に消費するため、データベースのパフォーマンスを低下させます。 フル テーブル スキャンが行われたテーブルを調べる最も簡単な方法は、*sys.schema_tables_with_full_table_scans* ビューのクエリを行うことです。

![フル テーブル スキャン](./media/howto-troubleshoot-sys-schema/full-table-scans.png)

### <a name="sysusersummarybystatementtype"></a>*sys.user_summary_by_statement_type*

データベースのパフォーマンスの問題をトラブルシューティングするには、データベースの内部で起こっているイベントを明らかにすると役に立つ場合があり、*sys.user_summary_by_statement_type* ビューがそれに使えることがあります。

![ステートメントごとの概要](./media/howto-troubleshoot-sys-schema/summary-by-statement.png)

この例の Azure Database for MySQL は、クエリ ログを 44579 回フラッシュするのに 53 分かかっています。 長い時間と多くの IO です。 Azure Portal で低速のクエリ ログを無効にするか、低速のクエリ ログの頻度を減らすことにより、このアクティビティを削減できます。

## <a name="database-maintenance"></a>データベース メンテナンス

### <a name="sysinnodbbufferstatsbytable"></a>*sys.innodb_buffer_stats_by_table*

InnoDB バッファー プールはメモリ内に存在し、DBMS とストレージの間の主なキャッシュ メカニズムです。 InnoDB バッファー プールのサイズはパフォーマンス レベルに関連付けられており、別の製品 SKU を選ばない限り変更できません。 オペレーティング システムのメモリと同様に、古いページはスワップ アウトされて新しいデータのための領域が確保されます。 InnoDB バッファー プールのメモリを最も多く消費しているテーブルを調べるには、*sys.innodb_buffer_stats_by_table* ビューのクエリを行います。

![InnoDB バッファーの状態](./media/howto-troubleshoot-sys-schema/innodb-buffer-status.png)

上の図では、システム テーブルとビューを除くと、WordPress サイトの 1 つをホストしている mysqldatabase033 データベース内の各テーブルが、16 KB つまり 1 ページのデータでメモリを占めていることがわかります。

### <a name="sysschemaunusedindexes--sysschemaredundantindexes"></a>*Sys.schema_unused_indexes* & *sys.schema_redundant_indexes*

インデックスは、読み取りのパフォーマンスを向上させる優れたツールですが、挿入とストレージの追加コストがかかります。 *Sys.schema_unused_indexes* と *sys.schema_redundant_indexes* は、使われていないインデックスまたは重複するインデックスについての詳しい情報を提供します。

![使われていないインデックス](./media/howto-troubleshoot-sys-schema/unused-indexes.png)

![冗長なインデックス](./media/howto-troubleshoot-sys-schema/redundant-indexes.png)

## <a name="conclusion"></a>まとめ

まとめると、sys_schema はパフォーマンスのチューニングとデータベースのメンテナンスの両方に対して優れたツールです。 お使いの Azure Database for MySQL でこの機能を活用してください。 

## <a name="next-steps"></a>次の手順
- 最も気になる質問への回答を探したり、新しい質問/回答を投稿したりするには、[MSDN フォーラム](https://social.msdn.microsoft.com/forums/security/en-US/home?forum=AzureDatabaseforMySQL)または [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql) をご覧ください。
