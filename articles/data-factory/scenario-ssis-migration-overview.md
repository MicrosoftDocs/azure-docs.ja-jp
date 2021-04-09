---
title: オンプレミスの SQL Server Integration Services (SSIS) ワークロードを Azure Data Factory (ADF) の SSIS に移行する
description: ADF でオンプレミスの SSIS ワークロードを SSIS に移行します。
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 9/3/2019
ms.openlocfilehash: 78c488302a874319f79a143e4657d161fe849855
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100373608"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>ADF でオンプレミスの SSIS ワークロードを SSIS に移行する

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>概要

データベース ワークロードをオンプレミスの SQL Server から Azure データベース サービス (つまり Azure SQL Database または Azure SQL Database Managed Instance) に移行する場合、主要な付加価値サービスの 1 つとして SQL Server Integration Services (SSIS) の ETL ワークロードも同様に移行する必要があります。

Azure Data Factory (ADF) の Azure-SSIS Integration Runtime (IR) では、SSIS パッケージの実行がサポートされています。 Azure-SSIS IR がプロビジョニングされると、SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) などの使い慣れたツールや dtinstall/dtutil/dtexec などのコマンドライン ユーティリティを使用して、Azure でパッケージをデプロイして実行できます。 詳細については、[Azure SSIS のリフトアンドシフトの概要](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)に関する記事を参照してください。

この記事では、オンプレミスの SSIS から ADF の SSIS への ETL ワークロードの移行プロセスについて説明します。 移行プロセスは、2 つのフェーズで構成されます。**評価** と **移行** です。

## <a name="assessment"></a>評価

完全な移行計画を立てる場合、徹底的な評価は、移行の成功を妨げるソース SSIS パッケージに関する問題を特定するために役立ちます。

Data Migration Assistant (DMA) は、この目的のために自由にダウンロード可能なツールであり、ローカルにインストールして実行できます。 種類が **Integration Services** の DMA 評価プロジェクトを作成して、SSIS パッケージを一括して評価し、次のカテゴリで示された互換性の問題を特定できます。

- 移行ブロック: Azure-SSIS IR での移行ソース パッケージの実行がブロックされる互換性の問題です。 DMA では、これらの問題に対処するために役立つガイダンスが提供されます。

- 情報通知のみの問題: ソース パッケージで使用される部分的にサポートされている機能または非推奨の機能です。 DMA では、包括的な一連の推奨事項、Azure で利用できる代替アプローチ、および解決するための軽減手順が提供されます。

### <a name="four-storage-types-for-ssis-packages"></a>SSIS パッケージの 4 種類のストレージ

- SSIS カタログ (SSISDB)。 SQL Server 2012 で導入され、SSIS プロジェクト/パッケージの操作に使用される一連のストアド プロシージャ、ビュー、およびテーブル値関数が含まれています。
- ファイル システム。
- SQL Server システム データベース (MSDB)。
- SSIS パッケージ ストア。 次の 2 つのサブタイプの上位にあるパッケージ管理レイヤーです。
  - MSDB。SSIS パッケージの格納に使用される SQL Server 内のシステム データベースです。
  - マネージド ファイル システム。SSIS パッケージの格納に使用される SQL Server インストール パス内の特定のフォルダーです。

現在、DMA は、**DMA バージョン v5.0** 以降、**ファイル システム**、**パッケージ ストア**、および **SSIS カタログ** に格納されているパッケージのバッチ評価をサポートしています。

[DMA](/sql/dma/dma-overview) を取得し、[それを使用してパッケージの評価を実行します](/sql/dma/dma-assess-ssis)。

## <a name="migration"></a>移行

ソース SSIS パッケージの [ストレージの種類](#four-storage-types-for-ssis-packages)とデータベース ワークロードの移行先によって、**SSIS パッケージ** と SSIS パッケージ実行をスケジュールする **SQL Server エージェント ジョブ** を移行する手順は異なる場合があります。 次の 2 つのシナリオがあります。

- [**データベース ワークロードの宛先としての** Azure SQL Database Managed Instance](#azure-sql-managed-instance-as-database-workload-destination)
- [データベース ワークロードの宛先としての **Azure SQL Database**](#azure-sql-database-as-database-workload-destination)

[SSIS DevOps ツール](/sql/integration-services/devops/ssis-devops-overview)を使用して、移行先にバッチ パッケージを再デプロイすることも実用的な方法です。  

### <a name="azure-sql-managed-instance-as-database-workload-destination"></a>データベース ワークロードの宛先としての **Azure SQL Database Managed Instance**

| **パッケージ ストレージの種類** |SSIS パッケージをバッチ移行する方法|SSIS ジョブをバッチ移行する方法|
|-|-|-|
|SSISDB|[**SSISDB** を移行する](scenario-ssis-migration-ssisdb-mi.md)|<li>[SSIS ジョブを Azure SQL Managed Instance エージェントに移行する](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li>スクリプト/SSMS/ADF ポータルを使用して、ADF パイプライン/アクティビティ/トリガーに変換します。 詳細については、[SSMS のスケジュール設定機能](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)に関する記事を参照してください。|
|ファイル システム|dtinstall/dtutil/manual copy を使用してファイル共有/Azure Files に再デプロイするか、ファイル システムを使用して VNet/セルフホステッド IR 経由でアクセスできる状態にします。 詳細については、「[dtutil ユーティリティ](/sql/integration-services/dtutil-utility)」を参照してください。|<li>[SSIS ジョブを Azure SQL Managed Instance エージェントに移行する](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li> [SSMS の SSIS ジョブ移行ウィザード](how-to-migrate-ssis-job-ssms.md)を使用して移行します。 <li>スクリプト/SSMS/ADF ポータルを使用して、ADF パイプライン/アクティビティ/トリガーに変換します。 詳細については、[SSMS のスケジュール設定機能](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)に関する記事を参照してください。|
|SQL Server (MSDB)|SSMS/dtutil を使用してファイル システム/ファイル共有/Azure Files にエクスポートします。 詳細については、[SSIS パッケージのエクスポート](/sql/integration-services/service/package-management-ssis-service#import-and-export-packages)に関する記事を参照してください。|スクリプト/SSMS/ADF ポータルを使用して、ADF パイプライン/アクティビティ/トリガーに変換します。 詳細については、[SSMS のスケジュール設定機能](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)に関する記事を参照してください。|
|パッケージ ストア|SSMS/dtutil によってパッケージ ストアにそれらをエクスポートするか、または dtinstall/dtutil/manual copy によってパッケージ ストアにそれらを再デプロイします。 詳細については、「[Azure-SSIS Integration Runtime パッケージ ストアを使用したパッケージの管理](azure-ssis-integration-runtime-package-store.md)」を参照してください。|<li>[SSIS ジョブを Azure SQL Managed Instance エージェントに移行する](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li> スクリプト/SSMS/ADF ポータルを使用して、ADF パイプライン/アクティビティ/トリガーに変換します。 詳細については、[SSMS のスケジュール設定機能](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)に関する記事を参照してください。|

### <a name="azure-sql-database-as-database-workload-destination"></a>データベース ワークロードの宛先としての **Azure SQL Database**

| **パッケージ ストレージの種類** |SSIS パッケージをバッチ移行する方法|ジョブをバッチ移行する方法|
|-|-|-|
|SSISDB|SSDT/SSMS を使用して Azure-SSISDB に再デプロイします。 詳細については、[Azure での SSIS パッケージのデプロイ](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)に関する記事を参照してください。|スクリプト/SSMS/ADF ポータルを使用して、ADF パイプライン/アクティビティ/トリガーに変換します。 詳細については、[SSMS のスケジュール設定機能](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)に関する記事を参照してください。|
|ファイル システム|dtinstall/dtutil/manual copy を使用してファイル共有/Azure Files に再デプロイするか、ファイル システムを使用して VNet/セルフホステッド IR 経由でアクセスできる状態にします。 詳細については、「[dtutil ユーティリティ](/sql/integration-services/dtutil-utility)」を参照してください。|<li> [SSMS の SSIS ジョブ移行ウィザード](how-to-migrate-ssis-job-ssms.md)を使用して移行します。 <li> スクリプト/SSMS/ADF ポータルを使用して、ADF パイプライン/アクティビティ/トリガーに変換します。 詳細については、[SSMS のスケジュール設定機能](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)に関する記事を参照してください。|
|SQL Server (MSDB)|SSMS/dtutil を使用してファイル システム/ファイル共有/Azure Files にエクスポートします。 詳細については、[SSIS パッケージのエクスポート](/sql/integration-services/service/package-management-ssis-service#import-and-export-packages)に関する記事を参照してください。|スクリプト/SSMS/ADF ポータルを使用して、ADF パイプライン/アクティビティ/トリガーに変換します。 詳細については、[SSMS のスケジュール設定機能](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)に関する記事を参照してください。|
|パッケージ ストア|SSMS/dtutil を使用してファイル システム/ファイル共有/Azure Files にエクスポートするか、dtinstall/dtutil/manual copy を使用してファイル共有/Azure Files に再デプロイするか、VNet/セルフホステッド IR 経由でアクセスできるようにファイル システムに保存します。 詳細については、「dtutil ユーティリティ」を参照してください。 詳細については、「[dtutil ユーティリティ](/sql/integration-services/dtutil-utility)」を参照してください。|スクリプト/SSMS/ADF ポータルを使用して、ADF パイプライン/アクティビティ/トリガーに変換します。 詳細については、[SSMS のスケジュール設定機能](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)に関する記事を参照してください。|

## <a name="additional-resources"></a>その他のリソース

- [Azure Data Factory](./introduction.md)
- [Database Migration Assistant](/sql/dma/dma-overview)
- [SSIS ワークロードをクラウドにリフトアンドシフトする](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)
- [SSIS DevOps ツール](/sql/integration-services/devops/ssis-devops-overview)
- [SSIS パッケージを Azure SQL Managed Instance に移行する](../dms/how-to-migrate-ssis-packages-managed-instance.md)
- [パッケージを Azure SQL Database に再デプロイする](../dms/how-to-migrate-ssis-packages.md)

- [Azure-SSIS Integration Runtime からのオンプレミスのデータ アクセス](https://techcommunity.microsoft.com/t5/sql-server-integration-services/vnet-or-no-vnet-secure-data-access-from-ssis-in-azure-data/ba-p/1062056)
- [Azure-SSIS 統合ランタイムのセットアップのカスタマイズ](how-to-configure-azure-ssis-ir-custom-setup.md)
- [Azure の SSIS パッケージから Windows 認証を使用してデータ ストアとファイル共有にアクセスする](ssis-azure-connect-with-windows-auth.md)
- [マネージド ID 認証の使用](/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
- [Azure Key Vault の使用](store-credentials-in-key-vault.md)
- [Azure-SSIS 統合ランタイムを高パフォーマンス用に構成する](configure-azure-ssis-integration-runtime-performance.md)
- [Azure-SSIS Integration Runtime をスケジュールに従って開始および停止する方法](how-to-schedule-azure-ssis-integration-runtime.md)

## <a name="next-steps"></a>次のステップ

- [Azure にデプロイされた SSIS パッケージを検証する](/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [Azure にデプロイされた SSIS パッケージを実行する](/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [Azure-SSIS 統合ランタイムの監視](./monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Azure で SSIS パッケージの実行スケジュールを設定する](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)