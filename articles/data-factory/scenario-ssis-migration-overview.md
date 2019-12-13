---
title: Azure Data Factory でオンプレミスの SSIS ワークロードを SSIS に移行する
description: ADF でオンプレミスの SSIS ワークロードを SSIS に移行します。
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/3/2019
ms.openlocfilehash: 52629b8e2e190cc041116e6f65488480712baf01
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929804"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>ADF でオンプレミスの SSIS ワークロードを SSIS に移行する

## <a name="overview"></a>概要

データベース ワークロードをオンプレミスの SQL Server から Azure データベース サービス (つまり Azure SQL Database または Azure SQL Database マネージド インスタンス) に移行する場合、主要な付加価値サービスの 1 つとして SQL Server Integration Services (SSIS) の ETL ワークロードも同様に移行する必要があります。

Azure Data Factory (ADF) の Azure-SSIS Integration Runtime (IR) では、SSIS パッケージの実行がサポートされています。 Azure-SSIS IR がプロビジョニングされると、SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) などの使い慣れたツールや dtinstall/dtutil/dtexec などのコマンドライン ユーティリティを使用して、Azure でパッケージをデプロイして実行できます。 詳細については、[Azure SSIS のリフトアンドシフトの概要](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)に関する記事を参照してください。

この記事では、オンプレミスの SSIS から ADF の SSIS への ETL ワークロードの移行プロセスについて説明します。 移行プロセスは、2 つのフェーズで構成されます。**評価**と**移行**です。

## <a name="assessment"></a>評価

完全な移行計画を立てる場合、徹底的な評価は、移行の成功を妨げるソース SSIS パッケージに関する問題を特定するために役立ちます。

Data Migration Assistant (DMA) は、この目的のために自由にダウンロード可能なツールであり、ローカルにインストールして実行できます。 種類が **Integration Services** の DMA 評価プロジェクトを作成して、SSIS パッケージを一括して評価し、次のカテゴリで示された互換性の問題を特定できます。

- 移行ブロック:これらは、Azure-SSIS IR での移行ソース パッケージの実行がブロックされる互換性の問題です。 DMA では、これらの問題に対処するために役立つガイダンスが提供されます。

- 情報通知のみの問題:これらは、ソース パッケージで使用される部分的にサポートされている機能または非推奨の機能です。 DMA では、包括的な一連の推奨事項、Azure で利用できる代替アプローチ、および解決するための軽減手順が提供されます。

### <a name="four-storage-types-for-ssis-packages"></a>SSIS パッケージの 4 種類のストレージ

- SSIS カタログ (SSISDB)。 これは SQL Server 2012 で導入され、SSIS プロジェクト/パッケージの操作に使用される一連のストア ド プロシージャ、ビュー、およびテーブル値関数が含まれています。
- ファイル システム。
- SQL Server システム データベース (MSDB)。
- SSIS パッケージ ストア。 これは、次の 2 つのサブタイプの上位にあるパッケージ管理レイヤーです。
  - MSDB。SSIS パッケージの格納に使用される SQL Server 内のシステム データベースです。
  - マネージド ファイル システム。SSIS パッケージの格納に使用される SQL Server インストール パス内の特定のフォルダーです。

現在、DMA は、**DMA バージョン v5.0** 以降、**ファイル システム**、**パッケージ ストア**、および **SSIS カタログ**に格納されているパッケージのバッチ評価をサポートしています。

[DMA](https://docs.microsoft.com/sql/dma/dma-overview) を取得し、[それを使用してパッケージの評価を実行します](https://docs.microsoft.com/sql/dma/dma-assess-ssis)。

## <a name="migration"></a>移行

ソース SSIS パッケージの[ストレージの種類](#four-storage-types-for-ssis-packages)とデータベース ワークロードの移行先によって、**SSIS パッケージ**と SSIS パッケージ実行をスケジュールする **SQL Server エージェント ジョブ**を移行する手順は異なる場合があります。 次の 2 つのシナリオがあります。

- [データベース ワークロードの宛先としての **Azure SQL Database マネージド インスタンス**](#azure-sql-database-managed-instance-as-database-workload-destination)
- [データベース ワークロードの宛先としての **Azure SQL Database**](#azure-sql-database-as-database-workload-destination)

### <a name="azure-sql-database-managed-instance-as-database-workload-destination"></a>データベース ワークロードの宛先としての **Azure SQL Database マネージド インスタンス**

| **パッケージ ストレージの種類** |SSIS パッケージをバッチ移行する方法|SSIS ジョブをバッチ移行する方法|
|-|-|-|
|SSISDB|[**SSISDB** を移行する](scenario-ssis-migration-ssisdb-mi.md)|[SSIS ジョブを Azure SQL Database マネージド インスタンス エージェントに移行する](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-azure-sql-database-managed-instance-agent)|
|ファイル システム|dtinstall/dtutil/manual copy を使用してファイル共有/Azure Files に再デプロイするか、ファイル システムを使用して VNet/セルフホステッド IR 経由でアクセスできる状態にします。 詳細については、「[dtutil ユーティリティ](https://docs.microsoft.com/sql/integration-services/dtutil-utility)」を参照してください。|スクリプト/SSMS/ADF ポータルを使用して、ADF パイプライン/アクティビティ/トリガーに変換します。 詳細については、[SSMS のスケジュール設定機能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)に関する記事を参照してください。|
|SQL Server (MSDB)|SSMS/dtutil を使用してファイル システム/ファイル共有/Azure Files にエクスポートします。 詳細については、[SSIS パッケージのエクスポート](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service)に関する記事を参照してください。|スクリプト/SSMS/ADF ポータルを使用して、ADF パイプライン/アクティビティ/トリガーに変換します。 詳細については、[SSMS のスケジュール設定機能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)に関する記事を参照してください。|
|パッケージ ストア|SSMS/dtutil を使用してファイル システム/ファイル共有/Azure Files にエクスポートするか、dtinstall/dtutil/manual copy を使用してファイル共有/Azure Files に再デプロイするか、VNet/セルフホステッド IR 経由でアクセスできるようにファイル システムに保存します。 詳細については、「dtutil ユーティリティ」を参照してください。 詳細については、「[dtutil ユーティリティ](https://docs.microsoft.com/sql/integration-services/dtutil-utility)」を参照してください。|スクリプト/SSMS/ADF ポータルを使用して、ADF パイプライン/アクティビティ/トリガーに変換します。 詳細については、[SSMS のスケジュール設定機能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)に関する記事を参照してください。|

### <a name="azure-sql-database-as-database-workload-destination"></a>データベース ワークロードの宛先としての **Azure SQL Database**

| **パッケージ ストレージの種類** |SSIS パッケージをバッチ移行する方法|ジョブをバッチ移行する方法|
|-|-|-|
|SSISDB|SSDT/SSMS を使用して Azure-SSISDB に再デプロイします。 詳細については、[Azure での SSIS パッケージのデプロイ](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)に関する記事を参照してください。|スクリプト/SSMS/ADF ポータルを使用して、ADF パイプライン/アクティビティ/トリガーに変換します。 詳細については、[SSMS のスケジュール設定機能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)に関する記事を参照してください。|
|ファイル システム|dtinstall/dtutil/manual copy を使用してファイル共有/Azure Files に再デプロイするか、ファイル システムを使用して VNet/セルフホステッド IR 経由でアクセスできる状態にします。 詳細については、「[dtutil ユーティリティ](https://docs.microsoft.com/sql/integration-services/dtutil-utility)」を参照してください。|スクリプト/SSMS/ADF ポータルを使用して、ADF パイプライン/アクティビティ/トリガーに変換します。 詳細については、[SSMS のスケジュール設定機能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)に関する記事を参照してください。|
|SQL Server (MSDB)|SSMS/dtutil を使用してファイル システム/ファイル共有/Azure Files にエクスポートします。 詳細については、[SSIS パッケージのエクスポート](https://docs.microsoft.com/sql/integration-services/import-and-export-packages-ssis-service)に関する記事を参照してください。|スクリプト/SSMS/ADF ポータルを使用して、ADF パイプライン/アクティビティ/トリガーに変換します。 詳細については、[SSMS のスケジュール設定機能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)に関する記事を参照してください。|
|パッケージ ストア|SSMS/dtutil を使用してファイル システム/ファイル共有/Azure Files にエクスポートするか、dtinstall/dtutil/manual copy を使用してファイル共有/Azure Files に再デプロイするか、VNet/セルフホステッド IR 経由でアクセスできるようにファイル システムに保存します。 詳細については、「dtutil ユーティリティ」を参照してください。 詳細については、「[dtutil ユーティリティ](https://docs.microsoft.com/sql/integration-services/dtutil-utility)」を参照してください。|スクリプト/SSMS/ADF ポータルを使用して、ADF パイプライン/アクティビティ/トリガーに変換します。 詳細については、[SSMS のスケジュール設定機能](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)に関する記事を参照してください。|

## <a name="additional-resources"></a>その他のリソース

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview)
- [SSIS ワークロードをクラウドにリフトアンドシフトする](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview?view=sql-server-2017)
- [SSIS パッケージを Azure SQL Database マネージド インスタンスに移行する](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)
- [パッケージを Azure SQL Database に再デプロイする](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)

## <a name="next-steps"></a>次の手順

- [Azure にデプロイされた SSIS パッケージを検証する](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [Azure にデプロイされた SSIS パッケージを実行する](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [Azure-SSIS 統合ランタイムの監視](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)
- [Azure で SSIS パッケージの実行スケジュールを設定する](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
