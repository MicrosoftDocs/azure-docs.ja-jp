---
title: 事業継続とディザスター リカバリー (BCDR) のために Azure-SSIS 統合ランタイムを構成する
description: この記事では、事業継続とディザスター リカバリー (BCDR) のために、Azure Data Factory で Azure SQL Database または Managed Instance フェールオーバー グループを使用して Azure-SSIS 統合ランタイムを構成する方法について説明します。
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/05/2021
ms.openlocfilehash: 2744d51b6d68ed494050be10a9f0e4d1f59cdc49
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102204067"
---
# <a name="configure-azure-ssis-integration-runtime-for-business-continuity-and-disaster-recovery-bcdr"></a>事業継続とディザスター リカバリー (BCDR) のために Azure-SSIS 統合ランタイムを構成する 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory (ADF) の Azure SQL Database または Managed Instance と SQL Server Integration Services (SSIS) は、SQL Server の移行に推奨されるすべての PaaS (サービスとしてのプラットフォーム) ソリューションとして組み合わせることができます。 SSIS プロジェクトを Azure SQL Database または Managed Instance によってホストされている SSIS カタログ データベース (SSISDB) にデプロイし、ADF の Azure SSIS 統合ランタイム (IR) 上で SSIS パッケージを実行できます。

事業継続とディザスター リカバリー (BCDR) の場合、[geo レプリケーションまたはフェールオーバー グループ](https://docs.microsoft.com/azure/azure-sql/database/auto-failover-group-overview)を使用して Azure SQL Database または Managed Instance を構成できます。この場合、読み取りおよび書き込みアクセス権を持つプライマリ Azure リージョンの SSISDB (プライマリ ロール) は、読み取り専用アクセス権を持つセカンダリ リージョン (セカンダリ ロール) に継続的にレプリケートされます。 プライマリ リージョンで障害が発生すると、フェールオーバーがトリガーされ、プライマリ とセカンダリの SSISDB の役割が入れ替わります。

BCDR の場合、Azure SQL Database または Managed Instance フェールオーバー グループと同期して機能するデュアル スタンバイ Azure SSIS IR ペアを構成することもできます。 こうすることで、プライマリ SSISDB にアクセスしてパッケージをフェッチおよび実行し、パッケージ実行ログを書き込むことができるのは常に一方のみ (プライマリ ロール) であり、もう一方では別の場所 (たとえば Azure Files) にデプロイされたパッケージに対してのみ同じ処理を実行できる (セカンダリ ロール) という、1 組の動作する Azure-SSIS IR を用意できます。 SSISDB のフェールオーバーが発生すると、プライマリとセカンダリの Azure-SSIS IR は役割が入れ替わります。両方が動作している場合、ダウンタイムはほぼゼロになります。

この記事では、BCDR に対して Azure SQL Database または Managed Instance フェールオーバー グループを使用して Azure-SSIS IR を構成する方法について説明します。

## <a name="configure-a-dual-standby-azure-ssis-ir-pair-with-azure-sql-database-failover-group"></a>Azure SQL Database フェールオーバー グループを使用してデュアル スタンバイ Azure-SSIS IR ペアを構成する

Azure SQL Database フェールオーバー グループと同期して機能するデュアル スタンバイ Azure-SSIS IR ペアを構成するには、次の手順を実行します。

1. Azure portal または ADF UI を使用して、プライマリ Azure SQL Database サーバーで新しい Azure-SSIS IR を作成し、プライマリ リージョンで SSISDB をホストできます。 プライマリ Azure SQL Database サーバーによってホストされている SSIDB に既にアタッチされている既存の Azure-SSIS IR があり、それがまだ実行中の場合は、最初に停止して再構成する必要があります。 これがプライマリ Azure-SSIS IR になります。

   **[Integration runtime setup]\(統合ランタイムの設定\)** ペインの **[デプロイの設定]** ページで [SSISDB の使用を選択する](./tutorial-deploy-ssis-packages-azure.md#creating-ssisdb)場合は、 **[Use dual standby Azure-SSIS Integration Runtime pair with SSISDB failover]\(SSISDB フェールオーバーと共にデュアル スタンバイ Azure-SSIS 統合ランタイム ペアを使用する\)** チェック ボックスもオンにします。 **[Dual standby pair name]\(デュアル スタンバイ ペア名\)** に、プライマリとセカンダリの Azure SSIS IR のペアを識別する名前を入力します。 プライマリ Azure-SSIS IR の作成が完了すると、Azure-SSIS IR が開始され、読み取りおよび書き込みアクセス権を使用してユーザーに代わって作成されるプライマリ SSISDB にアタッチされます。 再構成したばかりの場合は、再起動する必要があります。

1. Azure portal を使用すると、プライマリ Azure SQL Database サーバーの **[概要]** ページでプライマリ SSISDB が作成されているかどうかを確認できます。 作成されたら、[プライマリおよびセカンダリ Azure SQL Database サーバーのフェールオーバー グループを作成し、 **[フェールオーバー グループ]** ページでそこに SSISDB を追加](https://docs.microsoft.com/azure/azure-sql/database/failover-group-add-single-database-tutorial?tabs=azure-portal#2---create-the-failover-group)することができます。 フェールオーバー グループが作成されたら、セカンダリ Azure SQL Database サーバーの **[概要]** ページで、プライマリ SSISDB が読み取り専用アクセス権を持つセカンダリ SSISDB にレプリケートされているかどうかを確認できます。

1. Azure portal または ADF UI を使用して、セカンダリ Azure SQL Database サーバーで別の Azure-SSIS IR を作成し、セカンダリ リージョンで SSISDB をホストできます。 これがセカンダリ Azure-SSIS IR になります。 完全な BCDR を実現するには、依存するすべてのリソースがセカンダリ リージョンにも作成されていることを確認します。たとえば、カスタム設定スクリプトやファイルを格納するための Azure Storage、オーケストレーションやパッケージ実行のスケジュールを設定するための ADF などです。

   **[Integration runtime setup]\(統合ランタイムの設定\)** ペインの **[デプロイの設定]** ページで [SSISDB の使用を選択する](./tutorial-deploy-ssis-packages-azure.md#creating-ssisdb)場合は、 **[Use dual standby Azure-SSIS Integration Runtime pair with SSISDB failover]\(SSISDB フェールオーバーと共にデュアル スタンバイ Azure-SSIS 統合ランタイム ペアを使用する\)** チェック ボックスもオンにします。 **[Dual standby pair name]\(デュアル スタンバイ ペア名\)** に、プライマリとセカンダリの Azure SSIS IR のペアを識別する同じ名前を入力します。 セカンダリ Azure-SSIS IR の作成が完了すると、起動され、セカンダリ SSISDB にアタッチされます。

1. SSISDB のフェールオーバーが発生したときにダウンタイムをほぼゼロにしたい場合は、両方の Azure-SSIS IR を実行したままにします。 プライマリ SSISDB にアクセスしてパッケージをフェッチおよび実行し、パッケージ実行ログを書き込むことができるのはプライマリ Azure-SSIS IR のみです。一方、セカンダリ Azure-SSIS IR では、別の場所 (たとえば Azure Files) にデプロイされたパッケージに対してのみ同じ処理を実行できます。

   ランニング コストを最小限に抑えたい場合は、セカンダリ Azure-SSIS IR を作成後に停止できます。 SSISDB フェールオーバーが発生すると、プライマリとセカンダリの Azure-SSIS IR の役割が入れ替わります。 プライマリ Azure-SSIS IR が停止している場合は、再起動する必要があります。 仮想ネットワークに挿入されているかどうかと、使用されている挿入方法に応じて、実行には 5 分以内、または約 20 分から 30 分かかります。

1. [パッケージ実行のオーケストレーションまたはスケジュール設定に ADF を使用する](./how-to-invoke-ssis-package-ssis-activity.md)場合は、SSIS パッケージの実行アクティビティに関係するすべての ADF パイプラインと、関連するトリガーが、最初にトリガーを無効にしたセカンダリ ADF にコピーされていることを確認します。 SSISDB フェールオーバーが発生した場合は、それらを有効にする必要があります。

1. [Azure SQL Database フェールオーバー グループをテスト](https://docs.microsoft.com/azure/azure-sql/database/failover-group-add-single-database-tutorial?tabs=azure-portal#3---test-failover)し、[ADF ポータルの Azure-SSIS IR の監視ページ](./monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal)でプライマリとセカンダリの Azure-SSIS IR の役割が入れ替わったかどうかを確認できます。 

## <a name="configure-a-dual-standby-azure-ssis-ir-pair-with-azure-sql-managed-instance-failover-group"></a>Azure SQL Managed Instance フェールオーバー グループを使用してデュアル スタンバイ Azure-SSIS IR ペアを構成する

Azure SQL Managed Instance フェールオーバー グループと同期して機能するデュアル スタンバイ Azure-SSIS IR ペアを構成するには、次の手順を実行します。

1. Azure portal を使用すると、プライマリ Azure SQL マネージド インスタンスの **[フェールオーバー グループ]** ページで [プライマリおよびセカンダリの Azure SQL マネージド インスタンスのフェールオーバー グループを作成](https://docs.microsoft.com/azure/azure-sql/managed-instance/failover-group-add-instance-tutorial?tabs=azure-portal)できます。

1. Azure portal または ADF UI を使用して、プライマリ Azure SQL マネージド インスタンスで新しい Azure-SSIS IR を作成し、プライマリ リージョンで SSISDB をホストできます。 プライマリ Azure SQL マネージド インスタンスによってホストされている SSIDB に既にアタッチされている既存の Azure-SSIS IR があり、それがまだ実行中の場合は、最初に停止して再構成する必要があります。 これがプライマリ Azure-SSIS IR になります。

   **[Integration runtime setup]\(統合ランタイムの設定\)** ペインの **[デプロイの設定]** ページで [SSISDB の使用を選択する](./create-azure-ssis-integration-runtime.md#creating-ssisdb)場合は、 **[Use dual standby Azure-SSIS Integration Runtime pair with SSISDB failover]\(SSISDB フェールオーバーと共にデュアル スタンバイ Azure-SSIS 統合ランタイム ペアを使用する\)** チェック ボックスもオンにします。 **[Dual standby pair name]\(デュアル スタンバイ ペア名\)** に、プライマリとセカンダリの Azure SSIS IR のペアを識別する名前を入力します。 プライマリ Azure-SSIS IR の作成が完了すると、Azure-SSIS IR が開始され、読み取りおよび書き込みアクセス権を使用してユーザーに代わって作成されるプライマリ SSISDB にアタッチされます。 再構成したばかりの場合は、再起動する必要があります。 セカンダリ Azure SQL マネージド インスタンスの **[概要]** ページで、プライマリ SSISDB が読み取り専用アクセス権を持つセカンダリ SSISDB にレプリケートされているかどうかを確認することもできます。

1. Azure portal または ADF UI を使用して、セカンダリ Azure SQL マネージド インスタンスで別の Azure-SSIS IR を作成し、セカンダリ リージョンで SSISDB をホストできます。 これがセカンダリ Azure-SSIS IR になります。 完全な BCDR を実現するには、依存するすべてのリソースがセカンダリ リージョンにも作成されていることを確認します。たとえば、カスタム設定スクリプトやファイルを格納するための Azure Storage、オーケストレーションやパッケージ実行のスケジュールを設定するための ADF などです。

   **[Integration runtime setup]\(統合ランタイムの設定\)** ペインの **[デプロイの設定]** ページで [SSISDB の使用を選択する](./create-azure-ssis-integration-runtime.md#creating-ssisdb)場合は、 **[Use dual standby Azure-SSIS Integration Runtime pair with SSISDB failover]\(SSISDB フェールオーバーと共にデュアル スタンバイ Azure-SSIS 統合ランタイム ペアを使用する\)** チェック ボックスもオンにします。 **[Dual standby pair name]\(デュアル スタンバイ ペア名\)** に、プライマリとセカンダリの Azure SSIS IR のペアを識別する同じ名前を入力します。 セカンダリ Azure-SSIS IR の作成が完了すると、起動され、セカンダリ SSISDB にアタッチされます。

1. Azure SQL Managed Instance を使用して、データベース マスター キー (DMK) を使用して暗号化することにより、SSISDB などのデータベース内の機密データを保護できます。 DMK 自体は、既定でサービス マスター キー (SMK) を使用して暗号化されます。 このドキュメントの作成時点で、Azure SQL Managed Instance フェールオーバー グループによってプライマリ Azure SQL マネージド インスタンスから SMK がレプリケートされないため、フェールオーバーが発生した後、セカンダリ Azure SQL マネージド インスタンスで DMK と SSISDB を復号化できません。 この問題を回避するには、セカンダリ Azure SQL マネージド インスタンスで復号化する DMK のパスワード暗号化を追加します。 SSMS を使用して、次の手順を実行します。

   1. プライマリ Azure SQL マネージド インスタンスの SSISDB に対して次のコマンドを実行して、DMK を暗号化するためのパスワードを追加します。

      ```sql
      ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'YourPassword'
      ```
   
   1. プライマリとセカンダリの両方の Azure SQL マネージド インスタンスで SSISDB に対して次のコマンドを実行し、DMK を復号化するための新しいパスワードを追加します。

      ```sql
      EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB', @password = N'YourPassword', @action = N'add'
      ```

1. SSISDB のフェールオーバーが発生したときにダウンタイムをほぼゼロにしたい場合は、両方の Azure-SSIS IR を実行したままにします。 プライマリ SSISDB にアクセスしてパッケージをフェッチおよび実行し、パッケージ実行ログを書き込むことができるのはプライマリ Azure-SSIS IR のみです。一方、セカンダリ Azure-SSIS IR では、別の場所 (たとえば Azure Files) にデプロイされたパッケージに対してのみ同じ処理を実行できます。

   ランニング コストを最小限に抑えたい場合は、セカンダリ Azure-SSIS IR を作成後に停止できます。 SSISDB フェールオーバーが発生すると、プライマリとセカンダリの Azure-SSIS IR の役割が入れ替わります。 プライマリ Azure-SSIS IR が停止している場合は、再起動する必要があります。 仮想ネットワークに挿入されているかどうかと、使用されている挿入方法に応じて、実行には 5 分以内、または約 20 分から 30 分かかります。

1. [パッケージ実行のオーケストレーションとスケジュールに Azure SQL Managed Instance エージェントを使用する](./how-to-invoke-ssis-package-managed-instance-agent.md)場合は、関連するすべての SSIS ジョブとそのジョブ手順、および関連するスケジュールが、最初にスケジュールを無効にしたセカンダリ Azure SQL マネージド インスタンスにコピーされていることを確認します。 SSMS を使用して、次の手順を実行します。

   1. SSIS ジョブごとに、右クリックして **[ジョブをスクリプト化]** 、 **[新規作成]** 、および **[新しいクエリ エディター ウィンドウ]** ドロップダウン メニュー項目を選択して、スクリプトを生成します。

      ![SSIS ジョブ スクリプトを生成する](media/configure-bcdr-azure-ssis-integration-runtime/generate-ssis-job-script.png)

   1. 生成された SSIS ジョブ スクリプトごとに、`sp_add_job` ストアド プロシージャを実行するコマンドを見つけ、必要に応じて `@owner_login_name` 引数への値の割り当てを変更または削除します。

   1. 更新された SSIS ジョブ スクリプトごとに、セカンダリ Azure SQL マネージド インスタンス上で実行し、ジョブ手順と関連するスケジュールと共にジョブをコピーします。

   1. 次のスクリプトを使用して、新しい T-SQL ジョブを作成し、プライマリとセカンダリの両方の Azure SQL マネージド インスタンスでプライマリとセカンダリの SSISDB ロールに基づいて SSIS ジョブ スケジュールをそれぞれ有効または無効にし、定期的に実行します。 SSISDB のフェールオーバーが発生すると、無効にされた SSIS ジョブ スケジュールが有効になります。また、その逆も同様です。

      ```sql
      IF (SELECT Top 1 role_desc FROM SSISDB.sys.dm_geo_replication_link_status WHERE partner_database = 'SSISDB') = 'PRIMARY'
         BEGIN
            IF (SELECT enabled FROM msdb.dbo.sysschedules WHERE schedule_id = <ScheduleID>) = 0
               EXEC msdb.dbo.sp_update_schedule @schedule_id = <ScheduleID >, @enabled = 1
         END
      ELSE
         BEGIN
            IF (SELECT enabled FROM msdb.dbo.sysschedules WHERE schedule_id = <ScheduleID>) = 1
               EXEC msdb.dbo.sp_update_schedule @schedule_id = <ScheduleID >, @enabled = 0
         END
      ```

1. [パッケージ実行のオーケストレーションまたはスケジュール設定に ADF を使用する](./how-to-invoke-ssis-package-ssis-activity.md)場合は、SSIS パッケージの実行アクティビティに関係するすべての ADF パイプラインと、関連するトリガーが、最初にトリガーを無効にしたセカンダリ ADF にコピーされていることを確認します。 SSISDB フェールオーバーが発生した場合は、それらを有効にする必要があります。

1. [Azure SQL Managed Instance フェールオーバー グループをテスト](https://docs.microsoft.com/azure/azure-sql/managed-instance/failover-group-add-instance-tutorial?tabs=azure-portal#test-failover)し、[ADF ポータルの Azure-SSIS IR の監視ページ](./monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal)でプライマリとセカンダリの Azure-SSIS IR の役割が入れ替わったかどうかを確認できます。 

## <a name="attach-a-new-azure-ssis-ir-to-existing-ssisdb-hosted-by-azure-sql-databasemanaged-instance"></a>新しい Azure-SSIS IR を Azure SQL Database または Managed Instance によってホストされている既存の SSISDB にアタッチします

障害が発生し、同じリージョン内の既存の Azure-SSIS IR に影響しても、Azure SQL Database または Managed Instance には影響しない場合は、別のリージョンの新しいものと置き換えることができます。 Azure SQL Database または Managed Instance によってホストされている既存の SSISDB を新しい Azure-SSIS IR にアタッチするには、次の手順を実行します。

1. 既存の Azure-SSIS IR がまだ実行中の場合は、最初に Azure portal、ADF UI、または Azure PowerShell を使用して停止する必要があります。 障害が同じリージョンの ADF にも影響する場合は、この手順を省略できます。

1. SSMS を使用して、Azure SQL Database または Managed Instance で SSISDB に対して次のコマンドを実行し、新しい ADF または Azure-SSIS IR からの接続を許可するメタデータを更新します。

   ```sql
   EXEC [catalog].[failover_integration_runtime] @data_factory_name = 'YourNewADF', @integration_runtime_name = 'YourNewAzureSSISIR'
   ```

1. [Azure portal、ADF UI](./create-azure-ssis-integration-runtime.md#use-the-azure-portal-to-create-an-integration-runtime)、または [Azure PowerShell](./create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime) を使用して、 *<新しい ADF>* / *<新しい Azure SSIS IR>* という名前の新しい ADF または Azure-SSIS IR をそれぞれ別のリージョンに作成します。 Azure portal または ADF UI を使用する場合は、 **[Integration runtime setup]\(統合ランタイムの設定\)** ペインの **[デプロイの設定]** ページのテスト接続エラーを無視できます。

## <a name="next-steps"></a>次のステップ

Azure-SSIS IR の以下のその他の構成オプションを検討できます。

- [Azure-SSIS IR のパッケージ ストアを構成する](./create-azure-ssis-integration-runtime.md#creating-azure-ssis-ir-package-stores)

- [Azure-SSIS IR のカスタム設定を構成する](./how-to-configure-azure-ssis-ir-custom-setup.md)

- [Azure-SSIS IR の仮想ネットワークの挿入を構成する](./join-azure-ssis-integration-runtime-virtual-network.md)

- [セルフホステッド IR を Azure-SSIS IR のプロキシとして構成する](./self-hosted-integration-runtime-proxy-ssis.md)
