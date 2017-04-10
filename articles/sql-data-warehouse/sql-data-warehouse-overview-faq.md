---
title: "Azure SQL Data Warehouse のよく寄せられる質問 | Microsoft Docs"
description: "この記事では、Azure SQL Data Warehouse に関して顧客と開発者からよく寄せられる質問を示します"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: 
ms.assetid: 812CA525-3BF3-49DF-8DF3-FB4342464F4F
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 3/1/2017
ms.author: elbutter
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 7a752bfb349d2730537538f6856fe431204d3329
ms.lasthandoff: 03/04/2017


---

# <a name="sql-data-warehouse-frequently-asked-questions"></a>SQL Data Warehouse のよく寄せられる質問

## <a name="general"></a>全般

Q. データ セキュリティに対して SQL DW にはどのような効果がありますか。

A. SQL DW は、TDE や監査など、データを保護するための複数のソリューションを提供します。 詳細については、 [セキュリティ]に関するページを参照してください。

Q. SQL DW が準拠している法律またはビジネスに関する標準は、どこで確認できますか。

A. SOC や ISO などの製品によって提供されるさまざまなコンプライアンスについては、[Microsoft のコンプライアンスに関するページ]をご覧ください。 最初にコンプライアンス タイトルを選び、ページ右側の [Microsoft in-scope cloud services (Microsoft 対象クラウド サービス)] セクションで Azure を展開して、Azure サービスが準拠しているサービスを確認します。
 
Q. PowerBI に接続できますか。

A. はい。 PowerBI は SQL DW での直接クエリをサポートしますが、多数のユーザーまたはリアルタイム データを意図したものではありません。 PowerBI を運用環境で使う場合は、Azure Analysis Services または Analysis Service IaaS の上で PowerBI を使うことをお勧めします。 

Q. SQL Data Warehouse の容量制限とは何ですか。

A. [容量制限に関するページ]をご覧ください。 

## <a name="user-support"></a>ユーザー サポート

Q. 機能の要求があります。どこにお願いすればいいですか。

A. 機能の要求がある場合は、[UserVoice] ページでお送りください。

Q. 何かを行う方法を知るにはどうすればよいですか。

A. SQL Data Warehouse での開発については、[Stack Overflow のページ]で質問できます。 

Q. サポート チケットはどのように送信すればよいですか。

A. [サポート チケット]は Azure Portal で提出できます。

## <a name="sql-languagefeature-support"></a>SQL 言語/機能のサポート 

Q. SQL Data Warehouse はどのようなデータ型をサポートしますか。

A. SQL Data Warehouse の[データ型に関するページ]をご覧ください。

Q. どのようなテーブル機能がサポートされていますか。

A. SQL Data Warehouse は多くの機能をサポートしますが、サポートされない機能もいくつかあります。詳しくは、「[サポートされていないテーブルの機能]」をご覧ください。

## <a name="tooling-and-administration"></a>ツールと管理

Q. Visual Studio のデータベース プロジェクトはサポートされますか。

A. 現在、SQL Data Warehouse では Visual Studio のデータベース プロジェクトはサポートされていません。 この機能のサポート要求に投票したい場合は、User Voice の[データベース プロジェクト機能要求に関するページ]をご覧ください。

Q. SQL Data Warehouse は REST API をサポートしますか。

A. はい。 SQL Database で使うことができるほとんどの REST 機能は、SQL Data Warehouse でも利用できます。 API の情報については、REST のドキュメントまたは [MSDN] をご覧ください。


## <a name="loading"></a>読み込み

Q. どのようなクライアント ドライバーをサポートしていますか。

A. DW 用のドライバー サポートについては、[接続文字列に関するページ]をご覧ください。

Q: SQL Data Warehouse の PolyBase ではどのようなファイル形式がサポートされていますか。

A: Orc、RC、Parquet、およびフラット区切りテキストです。

Q: PolyBase を使うと SQL DW から何に接続できますか。 

A: [Azure Data Lake Store] と [Azure Storage Blob] です。

Q: Azure Storage Blob または ADLS に接続するとき、計算プッシュダウンは可能ですか。 

A: いいえ、SQL DW PolyBase は記憶域コンポーネントのみを操作します。 

Q: HDI に接続できますか。

A: HDI は、HDFS レイヤーとして ADLS または WASB を使うことができます。 HDFS レイヤーとしていずれかがある場合は、SQL DW にそのデータを読み込むことができます。 ただし、HDI インスタンスに対するプッシュダウン計算を生成することはできません。 

## <a name="next-steps"></a>次のステップ
SQL Data Warehouse 全体について詳しくは、[概要に関するページ]をご覧ください。


<!-- Article references -->
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[接続文字列に関するページ]: ./sql-data-warehouse-connection-strings.md
[Stack Overflow のページ]: http://stackoverflow.com/questions/tagged/azure-sqldw
[サポート チケット]: ./sql-data-warehouse-get-started-create-support-ticket.md
[セキュリティ]: ./sql-data-warehouse-overview-manage-security.md
[Microsoft のコンプライアンスに関するページ]: https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings
[容量制限に関するページ]: ./sql-data-warehouse-service-capacity-limits.md
[データ型に関するページ]: ./sql-data-warehouse-tables-data-types.md
[サポートされていないテーブルの機能]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Azure Data Lake Store]: ./sql-data-warehouse-load-from-azure-data-lake-store.md 
[Azure Storage Blob]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[データベース プロジェクト機能要求に関するページ]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
[MSDN]: https://msdn.microsoft.com/en-us/library/azure/mt163685.aspx
[概要に関するページ]: ./sql-data-warehouse-overview-faq.md
