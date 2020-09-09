---
title: Azure Synapse Analytics (旧称 SQL DW) のよく寄せられる質問
description: この記事では、Azure Synapse Analytics (旧称 SQL DW) に関して顧客と開発者からよく寄せられる質問を示します
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: b3ee3f729e1e2f03d7c72a72081ad2e3f0316cbb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85482532"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-frequently-asked-questions"></a>Azure Synapse Analytics (旧称 SQL DW) のよく寄せられる質問

## <a name="general"></a>全般

Q. Azure Synapse とは

A. Azure Synapse は、データ ウェアハウスとビッグ データ分析がまとめられた分析サービスです。 Azure Synapse では、これら 2 つの環境を 1 つにした統合エクスペリエンスを使用して、データの取り込み、準備、管理、提供を行い、BI と機械学習のニーズに対応できます。 詳しくは、[Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md) に関する記事をご覧ください。

Q. Azure SQL Data Warehouse はどうなりましたか

A. Azure Synapse は、Azure SQL Data Warehouse (SQL DW) の進化版です。 業界をリードする同じデータ ウェアハウスに、まったく新しいレベルのパフォーマンスと機能を搭載しました。 Azure Synapse を使用すると、運用環境で既存のデータ ウェアハウスのワークロードを引き続き実行できます。 詳しくは、[Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md) に関する記事をご覧ください。

Q. Synapse SQL プールとはなんですか。

A. Synapse SQL プールは、Azure Synapse で一般提供されているエンタープライズ データ ウェアハウス機能を表します。 詳しくは、[Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md) に関する記事をご覧ください。

Q. Azure Synapse の使用を開始する方法

A. [Azure の無料アカウント](https://azure.microsoft.com/free/sql-data-warehouse/)で使用を開始するか、[詳細について営業担当者にお問い合わせください](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html)。

Q. データ セキュリティに対して Azure Synapse にはどのような効果がありますか

A. Azure Synapse は、TDE や監査など、データを保護するための複数のソリューションを提供します。 詳細については、 [セキュリティ](sql-data-warehouse-overview-manage-security.md)に関するページを参照してください。

Q. Azure Synapse が準拠している法律またはビジネスに関する標準は、どこで確認できますか。

A. SOC や ISO などの製品によって提供されるさまざまなコンプライアンスについては、[Microsoft のコンプライアンスに関するページ](https://www.microsoft.com/trustcenter/compliance/complianceofferings)をご覧ください。 まず、コンプライアンスのタイトルを選びます。 次に、ページ右側の [Microsoft in-scope cloud services] (Microsoft 対象クラウド サービス) セクションで Azure を展開して、Azure Synapse が準拠しているサービスを確認します。

Q. Power BI は接続できますか

A. はい。 Power BI は Azure Synapse での直接クエリをサポートしますが、多数のユーザーまたはリアルタイム データを意図したものではありません。 Power BI のパフォーマンスをさらに最適化したい場合は、Azure Analysis Services または Analysis Services IaaS 上で Power BI を使用することを検討してください。

Q. Synapse SQL プールの容量制限とは何ですか。

A. [容量制限に関するページ](sql-data-warehouse-service-capacity-limits.md)をご覧ください。

Q. スケール/一時停止/再開にそれほど時間がかかるのはなぜでしょうか。

A. いくつかの要因がコンピューティングの管理操作に必要な時間に影響します。 長時間を要する操作に共通するのは、トランザクションのロールバックです。 スケール操作または一時停止操作が開始されると、すべての着信セッションがブロックされ、クエリが廃棄されます。 システムを安定した状態に維持するには、操作を開始する前にトランザクションをロールバックする必要があります。 トランザクションのログ数が多く、サイズが大きいほど、システムを安定した状態に復元するために操作を停止する時間が長くなります。

## <a name="user-support"></a>ユーザー サポート

Q. 機能の要求があります。どこにお願いすればいいですか。

A. 機能の要求がある場合は、[UserVoice](https://feedback.azure.com/forums/307516-sql-data-warehouse) ページでお送りください。

Q. 何かを行う方法を知るにはどうすればよいですか。

A. Azure Synapse を使用した開発については、[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw) に関するページで質問できます。

Q. サポート チケットはどのように送信すればよいですか。

A. [サポート チケット](sql-data-warehouse-get-started-create-support-ticket.md)は Azure Portal で提出できます。

## <a name="sql-languagefeature-support"></a>SQL 言語/機能のサポート

Q. サポートされているデータ型は何ですか。

A. [データの種類](sql-data-warehouse-tables-data-types.md)に関する記事を参照してください。

Q. どのようなテーブル機能がサポートされていますか。

A. 多くの機能がサポートされています。 サポートされていない機能は、[サポートされていないテーブルの機能](sql-data-warehouse-tables-data-types.md)に関するページで確認できます。

## <a name="tooling-and-administration"></a>ツールと管理

Q. Synapse SQL プールは REST API をサポートしていますか。

A. はい。 SQL Database で使うことができるほとんどの REST 機能は、Synapse SQL プールでも使用できます。 API の情報については、REST のドキュメントまたは[データベース](/rest/api/sql/databases?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)をご覧ください。

## <a name="loading"></a>読み込み

Q. どのようなクライアント ドライバーをサポートしていますか。

A. Synapse SQL プール用のドライバー サポートについては、[接続文字列](../sql/connection-strings.md)に関するページをご覧ください。

Q:PolyBase ではどのようなファイル形式がサポートされていますか

A:Orc、RC、Parquet、およびフラット区切りテキストです。

Q:PolyBase を使用して接続できるデータ ソースは何ですか

A:[Azure Data Lake Storage](sql-data-warehouse-load-from-azure-data-lake-store.md) と [Azure Storage Blobs](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) です。

Q:Azure Storage Blob または ADLS に接続するとき、計算プッシュダウンは可能ですか

A:いいえ、PolyBase は記憶域コンポーネントのみを操作します。

Q:HDI に接続できますか。

A:HDI は、HDFS レイヤーとして ADLS または WASB を使うことができます。 HDFS レイヤーとしていずれかがある場合は、Synapse SQL プールにそのデータを読み込むことができます。 ただし、HDI インスタンスに対するプッシュダウン計算を生成することはできません。

## <a name="next-steps"></a>次のステップ

Azure Synapse の詳細については、「[概要](sql-data-warehouse-overview-faq.md)」ページをご覧ください。
