---
title: Azure SQL Edge でサポートされる機能
description: Azure SQL Edge でサポートされる機能の詳細について説明します。
keywords: SQL Edge の紹介, SQL Edge とは, SQL Edge の概要
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 19dcbbf102a1d8d21f1b14780ea33816a1677c55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93392029"
---
# <a name="supported-features-of-azure-sql-edge"></a>Azure SQL Edge でサポートされる機能 

Azure SQL Edge は、最新バージョンの SQL データベース エンジンを基盤として構築されています。 SQL Server 2019 on Linux でサポートされている機能や、SQL Server 2019 on Linux (または SQL Server on Windows) で現在サポートされていない、または使用できない機能のサブセットがサポートされています。

SQL Server on Linux でサポートされている機能の完全な一覧については、「[SQL Server 2019 on Linux のエディションとサポートされる機能](/sql/linux/sql-server-linux-editions-and-components-2019)」を参照してください。 SQL Server on Windows のエディションとサポートされている機能については、「[SQL Server 2019 (15.x) のエディションとサポートされる機能](/sql/sql-server/editions-and-components-of-sql-server-version-15)」を参照してください。

## <a name="azure-sql-edge-editions"></a>Azure SQL Edge のエディション

Azure SQL Edge は、2 つの異なるエディション (ソフトウェア プラン) で提供されています。 これらのエディションの機能セットはまったく同じで、使用権限と、ホスト システム上でアクセスできるメモリとコアのサイズについてのみ違いがあります。

   |**プラン**  |**説明**  |
   |---------|---------|
   |Azure SQL Edge Developer  |  開発専用。 各 Azure SQL Edge Developer コンテナーは、最大 4 コアおよび 32 GB メモリまでに制限されます。  |
   |Azure SQL Edge    |  運用環境向け。 各 Azure SQL Edge コンテナーは、最大 8 コアおよび 64 GB メモリまでに制限されます。  |

## <a name="operating-system"></a>オペレーティング システム

Azure SQL Edge コンテナーは Ubuntu 18.04 をベースとしているため、Ubuntu 18.04 LTS (推奨) または Ubuntu 20.04 LTS のいずれかを実行している Docker ホストでの実行のみがサポートされています。 Azure SQL Edge コンテナーを他のオペレーティング システム ホストで実行することは可能です。たとえば、Linux の他のディストリビューションまたは Windows で (Docker CE または Docker EE を使用して) 実行できますが、この構成は広範囲にわたってテストされていないため、Microsoft ではこれを行うことはお勧めしません。

Windows 上で Azure SQL Edge を実行するために推奨される構成は、Windows ホスト上で Ubuntu VM を構成し、Linux VM 内で Azure SQL Edge を実行することです。

Azure SQL Edge で推奨およびサポートされているファイル システムは、EXT4 および XFS です。 永続ボリュームを使用して Azure SQL Edge データベース ストレージをバックアップする場合は、基になるホスト ファイル システムが EXT4 および XFS である必要があります。

## <a name="hardware-support"></a>ハードウェア サポート

Azure SQL Edge には 64 ビットプロセッサ (x64 または ARM64 のいずれか) が必要であり、少なくとも 1 個のプロセッサと 1 GB の RAM がホストに搭載されている必要があります。 Azure SQL Edge の初期メモリ占有領域は 450 MB ほどですが、エッジ デバイス上で実行される他の IoT Edge モジュールやプロセスのために追加のメモリが必要です。 Azure SQL Edge の実際のメモリ要件および CPU 要件は、ワークロードの複雑さと処理するデータ量によって異なります。 ソリューションのハードウェアを選択する際は、ソリューションに必要なパフォーマンス特性が満たされていることを確認するために、広範なパフォーマンス テストを実行することをお勧めします。  

## <a name="azure-sql-edge-components"></a>Azure SQL Edge のコンポーネント

Azure SQL Edge では、データベース エンジンのみがサポートされています。 SQL Server 2019 on Windows または SQL Server 2019 on Linux で使用できるその他のコンポーネントのサポートは含まれていません。 具体的には、Analysis Services、Reporting Services、Integration Services、マスター データ サービス、Machine Learning Services (データベース内)、Machine Learning Server (スタンドアロン) のような SQL Server コンポーネントは、Azure SQL Edge ではサポートされません。

## <a name="supported-features"></a>サポートされている機能

Azure SQL Edge では、SQL Server on Linux の機能のサブセットに加えて、次の新機能もサポートされます。 

- Azure Stream Analytics で採用されているのと同じエンジンに基づく SQL ストリーミング。Azure SQL Edge でリアルタイムのデータ ストリーミング機能を提供します。 
- 時系列データ分析のための T-SQL 関数呼び出し `Date_Bucket`。
- SQL エンジンに付属する ONNX ランタイムを使用した機械学習機能。

## <a name="unsupported-features"></a>サポートされていない機能

次の一覧には、Azure SQL Edge で現在サポートされていない SQL Server 2019 on Linux の機能が含まれています。

| 領域 | サポートされていない機能またはサービス |
|-----|-----|
| **データベースの設計** | インメモリ OLTP、関連する DDL コマンドおよび Transact-SQL 関数、カタログ ビュー、動的管理ビュー。 |
| &nbsp; | `HierarchyID` データ型、関連する DDL コマンドおよび Transact-SQL 関数、カタログ ビュー、動的管理ビュー。 |
| &nbsp; | `Spatial` データ型、関連する DDL コマンドおよび Transact-SQL 関数、カタログ ビュー、動的管理ビュー。 |
| &nbsp; | Stretch DB、関連する DDL コマンドおよび Transact-SQL 関数、カタログ ビュー、動的管理ビュー。 |
| &nbsp; | フルテキスト インデックスおよび検索、関連する DDL コマンドおよび Transact-SQL 関数、カタログ ビュー、動的管理ビュー。|
| &nbsp; | `FileTable`、`FILESTREAM`、関連する DDL コマンドおよび Transact-SQL 関数、カタログ ビュー、動的管理ビュー。|
| **データベース エンジン** | レプリケーション。 Azure SQL Edge をレプリケーション トポロジのプッシュ サブスクライバーとして構成できることに注意してください。 |
| &nbsp; | PolyBase。 Azure SQL Edge を Polybase の外部テーブルのターゲットとして構成できることに注意してください。 |
| &nbsp; | Java および Spark による言語拡張 |
| &nbsp; | Active Directory 統合。 |
| &nbsp; | データベースの自動圧縮。 データベースの自動圧縮プロパティは `ALTER DATABASE <database_name> SET AUTO_SHRINK ON` コマンドを使用して設定できますが、変更しても効果はありません。 自動圧縮タスクはデータベースに対して実行されません。 ユーザーは 'DBCC' コマンドを使用してデータベース ファイルを圧縮することができます。 |
| &nbsp; | データベース スナップショット。 |
| &nbsp; | 永続メモリのサポート。 |
| &nbsp; | Microsoft 分散トランザクション コーディネーター。 |
| &nbsp; | リソース ガバナーと IO リソース管理。 |
| &nbsp; | バッファー プール拡張機能。 |
| &nbsp; | サードパーティの接続を使用した分散クエリ。 |
| &nbsp; | リンク サーバー。 |
| &nbsp; | システム拡張ストアド プロシージャ (`XP_CMDSHELL` など)。 |
| &nbsp; | CLR アセンブリ、関連する DDL コマンドおよび Transact-SQL 関数、カタログ ビュー、動的管理ビュー。 |
| &nbsp; | CLR に依存する T-SQL 関数 (`ASSEMBLYPROPERTY`、`FORMAT`、`PARSE`、`TRY_PARSE` など)。 |
| &nbsp; | CLR に依存する日付と時刻のカタログ ビュー、関数、クエリ句。 |
| &nbsp; | バッファー プール拡張機能。 |
| &nbsp; | データベース メール。 |
| &nbsp; | Service Broker |
| &nbsp; | ポリシー ベースの管理 |
| &nbsp; | 管理データ ウェアハウス (management data warehouse) |
| &nbsp; | 包含データベース |
| **SQL Server エージェント** |  サブシステム: CmdExec、PowerShell、キュー リーダー、SSIS、SSAS、SSRS。 |
| &nbsp; | アラート。 |
| &nbsp; | マネージド バックアップ。 |
| **高可用性** | Always On 可用性グループ。  |
| &nbsp; | 基本的な可用性グループ。 |
| &nbsp; | Always On フェールオーバー クラスター インスタンス。 |
| &nbsp; | データベース ミラーリング |
| &nbsp; | ホット アド メモリと CPU。 |
| **Security** | 拡張キー管理。 |
| &nbsp; | Active Directory 統合。|
| &nbsp; | セキュリティで保護されたエンクレーブのサポート。|
| **サービス** | SQL Server Browser。 |
| &nbsp; | R および Python による機械学習。 |
| &nbsp; | StreamInsight。 |
| &nbsp; | Analysis Services。 |
| &nbsp; | Reporting Services。 |
| &nbsp; | Data Quality Services。 |
| &nbsp; | マスター データ サービス。 |
| &nbsp; | 分散再生。 |
| **管理の容易性** | SQL Server ユーティリティ コントロール ポイント。 |

## <a name="next-steps"></a>次のステップ

- [Azure SQL Edge のデプロイ](deploy-portal.md)
- [Azure SQL Edge の構成](configure.md)
- [SQL Server クライアント ツールを使用した Azure SQL Edge のインスタンスへの接続](connect.md)
- [Azure SQL Edge でのデータベースのバックアップと復元](backup-restore.md)