---
title: Azure Data Factory で Azure-SSIS 統合ランタイムを作成する | Microsoft Docs
description: Azure 上で SSIS パッケージをデプロイして実行できるように、Azure-SSIS 統合ランタイムを Azure Data Factory に作成する方法について説明します。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/26/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: fb5335c8dfd94006ba3f0d8d6b890869dd9f3717
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484833"
---
# <a name="create-azure-ssis-integration-runtime-in-azure-data-factory"></a>Azure Data Factory で Azure-SSIS 統合ランタイムを作成する

このチュートリアルでは、Azure-SQL Server Integration Services (SSIS) 統合ランタイム (IR) を Azure Data Factory (ADF) にプロビジョニングする手順について説明します。 Azure-SSIS IR では、Azure SQL Database サーバー/Managed Instance によってホストされている SSIS カタログ (SSISDB) にデプロイされたパッケージ (プロジェクト デプロイ モデル) と、ファイル システム/ファイル共有/Azure Files にデプロイされたパッケージ (パッケージ デプロイ モデル) の実行がサポートされます。 Azure-SSIS IR がプロビジョニングされると、SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) などの使い慣れたツールや `dtinstall`/`dtutil`/`dtexec` などのコマンド ライン ユーティリティを使用して、Azure でパッケージをデプロイして実行できます。

[Azure-SSIS IR のプロビジョニングに関するチュートリアル](tutorial-create-azure-ssis-runtime-portal.md)では、Azure portal/ADF アプリ経由で Azure-SSIS IR を作成する方法と、必要に応じて Azure SQL Database サーバー/Managed Instance を使用して SSISDB をホストする方法を示します。 この記事では、チュートリアルをさらに掘り下げ、次の操作を行う方法を示します。

- 必要に応じて、仮想ネットワーク サービス エンドポイント/仮想ネットワーク内の Managed Instance で Azure SQL Database サーバーを使用して、SSISDB をホストします。 前提条件として、Azure-SSIS IR を仮想ネットワークに参加させるために、仮想ネットワークのアクセス許可/設定を構成する必要があります。

- 必要に応じて、Azure Active Directory (AAD) 認証と ADF のマネージド ID を使用して、Azure SQL Database サーバー/Managed Instance に接続します。 前提条件として、SSISDB を作成できるデータベース ユーザーとして ADF のマネージド ID を追加する必要があります。

## <a name="overview"></a>概要

この記事では、Azure SSIS IR のさまざまなプロビジョニング方法を示します。

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure Resource Manager テンプレート](#azure-resource-manager-template)

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure サブスクリプション**。 まだサブスクリプションをお持ちでない場合は、[無料試用版](https://azure.microsoft.com/pricing/free-trial/)アカウントを作成できます。
- **Azure SQL Database サーバー/Managed Instance (オプション)** 。 まだデータベース サーバーをお持ちでない場合は、あらかじめ Azure portal で作成しておいてください。 その後、ADF によって、このデータベース サーバーに SSISDB が作成されます。 このデータベース サーバーは、統合ランタイムと同じ Azure リージョンに作成することをお勧めします。 この構成により、統合ランタイムは複数の Azure リージョンにまたがることなく SSISDB に実行ログを書き込むことができます。 
    - SSISDB は、選択したデータベース サーバーに基づいて、1 つのデータベースやエラスティック プールの一部として、またはマネージド インスタンス上に自動的に作成でき､パブリック ネットワークから､または仮想ネットワークに参加することでアクセスできます。 SSISDB をホストするデータベース サーバーの種類を選択するときのガイダンスについては、[Azure SQL Database の単一データベース/エラスティック プールと Managed Instance の比較](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance)に関するセクションを参照してください。 仮想ネットワーク サービス エンドポイント/仮想ネットワーク内の Managed Instance で Azure SQL Database サーバーを使用して、SSISDB をホストするか、オンプレミスのデータにアクセスする必要がある場合は、Azure-SSIS IR を仮想ネットワークに参加させる必要があります。[仮想ネットワークへの Azure-SSIS IR の参加](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)に関する記事を参照してください。
    - データベース サーバーで **[Azure サービスへのアクセスを許可]** の設定が有効になっていることを確認します。 仮想ネットワーク サービス エンドポイント/仮想ネットワーク内の Managed Instance で Azure SQL Database サーバーを使用して SSISDB をホストする場合、この設定は適用されません。 詳細については、「[Azure SQL データベースのセキュリティ保護](../sql-database/sql-database-security-tutorial.md#create-firewall-rules)」を参照してください。 PowerShell を使用してこの設定を有効にするには、「[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)」を参照してください。
    - クライアント マシンの IP アドレス、またはクライアント マシンの IP アドレスを含む IP アドレスの範囲を、データベース サーバーのファイアウォール設定にあるクライアント IP アドレスの一覧に追加します。 詳細については、「[Azure SQL Database のサーバーレベルとデータベースレベルのファイアウォール規則](../sql-database/sql-database-firewall-configure.md)」を参照してください。
    - サーバー管理者の資格情報による SQL 認証または ADF のマネージド ID による Azure Active Directory (AAD) 認証を使用して、データベース サーバーに接続できます。  後者の場合、データベース サーバーへのアクセス許可が割り当てられている AAD グループに ADF のマネージド ID を追加する必要があります。[Azure-SSIS IR に対する AAD 認証の有効化](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)に関するページを参照してください。
    - データベース サーバーに SSISDB がまだないことを確認します。 Azure-SSIS IR のプロビジョニングでは、既存の SSISDB の使用はサポートされていません。
- **Azure Resource Manager 仮想ネットワーク (オプション)** 。 次の条件に 1 つでも当てはまる場合は、Azure Resource Manager 仮想ネットワークが必要です。
    - 仮想ネットワーク サービス エンドポイント/仮想ネットワーク内の Managed Instance で Azure SQL Database サーバー上に SSISDB をホストしている。
    - Azure-SSIS IR 上で実行される SSIS パッケージからオンプレミス データ ストアに接続する必要がある。
- **Azure PowerShell (オプション)** . PowerShell スクリプトを実行して Azure-SSIS IR をプロビジョニングする場合は、[Azure PowerShell のインストールおよび構成方法](/powershell/azure/install-az-ps)に関する記事の指示に従ってください。

### <a name="region-support"></a>リージョンのサポート

ADF と Azure-SSIS IR が現在使用可能な Azure リージョンの一覧については、[リージョン別の ADF + SSIS IR の利用可能性](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)に関するページを参照してください。

### <a name="compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance"></a>SQL Database の単一データベース/エラスティック プールと SQL Database Managed Instance の比較

次の表では、Azure-SSIR IR に関連する Azure SQL Database サーバーと Managed Instance の一部の機能を比較しています。

| 機能 | 単一データベース/エラスティック プール| マネージド インスタンス |
|---------|--------------|------------------|
| **スケジュール設定** | SQL Server エージェントは使用できません。<br/><br/>[ADF パイプラインでのパッケージの実行のスケジュール](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity)に関するページを参照してください。| マネージド インスタンス エージェントを使用できます。 |
| **認証** | **db_owner** ロールのメンバーとして ADF のマネージド ID を持つ AAD グループを表す包含データベース ユーザーを使用して、SSISDB を作成できます。<br/><br/>[Azure SQL Database サーバーで SSISDB を作成するための Azure AD 認証の有効化](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database)に関するページを参照してください。 | ADF のマネージド ID を表す包含データベース ユーザーを使用して、SSISDB を作成できます。 <br/><br/>[Azure SQL Database Managed Instance で SSISDB を作成するための Azure AD 認証の有効化](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance)に関するページを参照してください。 |
| **サービス レベル** | Azure SQL Database サーバーで Azure-SSIS IR を作成するときに、SSISDB のサービス レベルを選択できます。 複数のサービス レベルがあります。 | Managed Instance で Azure-SSIS IR を作成するときは、SSISDB のサービス レベルを選択することはできません。 Managed Instance 上のすべてのデータベースは、そのインスタンスに割り当てられた同じリソースを共有します。 |
| **Virtual Network** | Azure SQL Database サーバーと仮想ネットワーク サービス エンドポイントを使用する場合、またはオンプレミスのデータ ストアにアクセスする必要がある場合は、Azure-SSIS IR の参加用に、Azure Resource Manager 仮想ネットワークのみがサポートされます。 | Azure-SSIS IR の参加用に、Azure Resource Manager 仮想ネットワークのみがサポートされます。 仮想ネットワークは常に必須です。<br/><br/>Managed Instance と同じ仮想ネットワークに Azure-SSIS IR を参加させる場合は、Azure-SSIS IR を、必ず Managed Instance とは異なるサブネットに配置します。 Managed Instance とは異なる仮想ネットワークに Azure-SSIS IR を参加させる場合、仮想ネットワーク ピアリングまたは仮想ネットワーク接続への 1 つの仮想ネットワークのどちらかをお勧めします。 「[Azure SQL Database Managed Instance にアプリケーションを接続する](../sql-database/sql-database-managed-instance-connect-app.md)」を参照してください。 |
| **分散トランザクション** | エラスティック トランザクションを介してサポートされます。 Microsoft 分散トランザクション コーディネーター (MSDTC) トランザクションはサポートされていません。 SSIS パッケージが MSDTC を使用して分散トランザクションを調整している場合は、Azure SQL Database 用のエラスティック トランザクションに移行することを検討してください。 詳細については、「[クラウド データベースにまたがる分散トランザクション](../sql-database/sql-database-elastic-transactions-overview.md)」を参照してください。 | サポートされていません。 |
| | | |

## <a name="azure-portal"></a>Azure ポータル

このセクションでは、Azure portal、具体的には ADF ユーザー インターフェイス (UI)/アプリを使用して、Azure-SSIS IR を作成します。

### <a name="create-a-data-factory"></a>Data Factory の作成

1. Web ブラウザー (**Microsoft Edge** または **Google Chrome**) を起動します。 現在、Data Factory の UI がサポートされる Web ブラウザーは Microsoft Edge と Google Chrome だけです。
1. [Azure Portal](https://portal.azure.com/) にサインインします。
1. 左側のメニューで **[新規]** をクリックし、 **[データ + 分析]** 、 **[Data Factory]** の順にクリックします。

   ![New->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)

1. **[新しいデータ ファクトリ]** ページで、 **[名前]** に「**MyAzureSsisDataFactory**」と入力します。

   ![[新しいデータ ファクトリ] ページ](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)

   Azure データ ファクトリの名前は **グローバルに一意**にする必要があります。 次のエラーが発生した場合は、データ ファクトリの名前を変更して (yournameMyAzureSsisDataFactory など) 作成し直してください。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事を参照してください。

   `Data factory name “MyAzureSsisDataFactory” is not available`

1. データ ファクトリを作成する Azure **サブスクリプション**を選択します。
1. **[リソース グループ]** について、次の手順のいずれかを行います。

   - **[Use existing (既存のものを使用)]** を選択し、ドロップダウン リストから既存のリソース グループを選択します。
   - **[新規作成]** を選択し、リソース グループの名前を入力します。

   リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/resource-group-overview.md)に関するページを参照してください。

1. **バージョン**として **[V2]** を選択します。
1. データ ファクトリの **場所** を選択します。 データ ファクトリの作成がサポートされている場所のみが一覧に表示されます。
1. **[ダッシュボードにピン留めする]** をオンにします。
1. **Create** をクリックしてください。
1. ダッシュボードに、 **[Deploying data factory]\(データ ファクトリをデプロイしています\)** というステータスを示したタイルが表示されます。

    ![[Deploying data factory]\(データ ファクトリをデプロイしています\) タイル](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)

1. 作成が完了すると、図に示されているような **[Data Factory]** ページが表示されます。

    ![データ ファクトリのホーム ページ](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)

1. **[Author & Monitor]\(作成と監視\)** をクリックして、別のタブで Data Factory ユーザー インターフェイス (UI) を起動します。

### <a name="provision-an-azure-ssis-integration-runtime"></a>Azure SSIS 統合ランタイムをプロビジョニングする

1. 開始ページで、 **[Configure SSIS Integration Runtime]\(SSIS 統合ランタイムの構成\)** タイルをクリックします。

   ![[Configure SSIS Integration Runtime]\(SSIS 統合ランタイムの構成\) タイル](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

2. **[Integration Runtime Setup]\(統合ランタイムの設定\)** の **[全般設定]** ページで、次の手順を実行します。

   ![全般設定](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    a. **[名前]** に統合ランタイムの名前を入力します。

    b. **[説明]** に統合ランタイムの説明を入力します。

    c. **[場所]** で、統合ランタイムの場所を選択します。 サポートされている場所のみが表示されます。 SSISDB をホストするデータベース サーバーと同じ場所を選択することをお勧めします。

    d. **[ノード サイズ]** で、統合ランタイム クラスター内のノードのサイズを選択します。 サポートされているノード サイズのみが表示されます。 コンピューティングやメモリを大量に使用するパッケージを多数実行する場合は、大きなノード サイズを選択します (スケールアップ)。

    e. **[Node Number]\(ノード数\)** で、統合ランタイム クラスター内のノードの数を選択します。 サポートされているノード数のみが表示されます。 多くのパッケージを並列で実行する場合は、ノード数が多い大規模なクラスターを選択します (スケールアウト)。

    f. **[Edition/License]\(エディション/ライセンス)** で、統合ランタイムの SQL Server エディション/ライセンス([Standard] または [Enterprise]) を選択します。 統合ランタイムで高度な機能やプレミアム機能を使用する場合は、[Enterprise] を選択します。

    g. **[お金を節約]** で、統合ランタイムの Azure ハイブリッド特典 (AHB) オプション([はい] または [いいえ]) を選択します。 ソフトウェア アシュアランス付き SQL Server ライセンスを移行して、ハイブリッド使用によるコスト節約の恩恵を受ける場合は、[はい] を選択します。

    h. **[次へ]** をクリックします。

3. **[SQL の設定]** ページで、次の手順を完了します。

   ![SQL の設定](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    a. **[Create SSIS catalog]\(SSIS カタログの作成\)** チェック ボックスで、パッケージを Azure SSIS IR 上で実行するためのデプロイ モデルとして次のいずれかを選択します。データベース サーバーによってホストされている SSISDB にパッケージがデプロイされるプロジェクト デプロイ モデルか、ファイル システム/ファイル共有/Azure Files にパッケージがデプロイされるパッケージ デプロイ モデル。 このチェック ボックスをオンにした場合、自動的に作成および管理される SSISDB をホストするデータベース サーバーを用意する必要があります。
   
    b. **[サブスクリプション]** で、SSISDB をホストするデータベース サーバーを保有する Azure サブスクリプションを選択します。 

    c. **[場所]** で、SSISDB をホストするデータベース サーバーの場所を選択します。 統合ランタイムと同じ場所を選択することをお勧めします。 

    d. **[Catalog Database Server Endpoint]\(カタログ データベース サーバー エンドポイント\)** で、SSISDB をホストするデータベース サーバーのエンドポイントを選択します。 SSISDB は、選択したデータベース サーバーに基づいて、1 つのデータベースやエラスティック プールの一部として、またはマネージド インスタンス上に自動的に作成でき､パブリック ネットワークから､または仮想ネットワークに参加することでアクセスできます。 SSISDB をホストするデータベース サーバーの種類を選択するときのガイダンスについては、[Azure SQL Database の単一データベース/エラスティック プールと Managed Instance の比較](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance)に関するセクションを参照してください。 仮想ネットワーク サービス エンドポイント/仮想ネットワーク内の Managed Instance で Azure SQL Database サーバーを選択して、SSISDB をホストするか、オンプレミスのデータにアクセスする必要がある場合は、Azure-SSIS IR を仮想ネットワークに参加させる必要があります。[仮想ネットワークへの Azure-SSIS IR の参加](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)に関する記事を参照してください。 

    e. **[Use AAD authentication]\(AAD 認証を使用する\)** チェック ボックスで、SSISDB をホストするデータベース サーバーの認証方法としてSQL 認証か、ADF のマネージド ID を使用する AAD 認証を選択します。 このチェック ボックスをオンにすると、データベース サーバーへのアクセス許可が割り当てられている AAD グループに ADF のマネージド ID を追加する必要があります。[Azure-SSIS IR に対する AAD 認証の有効化](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)に関するページを参照してください。 

    f. **[管理者ユーザー名]** に、SSISDB をホストするデータベース サーバーの SQL 認証ユーザー名を入力します。 

    g. **[管理者パスワード]** に、SSISDB をホストするデータベース サーバーの SQL 認証パスワードを入力します。 

    h. **[Catalog Database Service Tier]\(カタログ データベースのサービス レベル)** で、SSISDB をホストするデータベース サーバーのサービス レベル (Basic/Standard/Premium レベルまたはエラスティック プール名) を選択します。 

    i. **[接続テスト]** をクリックし、成功した場合は **[次へ]** をクリックします。 

4. **[詳細設定]** ページで、次の手順を実行します。

    ![詳細設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

    a. **[Maximum Parallel Executions Per Node]\(ノードあたりの最大並列実行数\)** で、統合ランタイム クラスター内のノードあたりの同時に実行するパッケージの最大数を選択します。 サポートされているパッケージ数のみが表示されます。 コンピューティングやメモリを大量に使用する 1 つの大規模/重量なパッケージを実行するために複数のコアを使用する場合は、低い数値を選択します。 1 つのコアで 1 つ以上の小規模/軽量なパッケージを実行する場合は、高い数値を選択します。

    b. **[Custom Setup Container SAS URI]\(カスタム セットアップ コンテナー SAS URI\)** に、必要に応じて、セットアップ スクリプトとその関連ファイルが格納されている Azure Storage Blob コンテナーの Shared Access Signature (SAS) Uniform Resource Identifier (URI) を入力します。「[Azure SSIS 統合ランタイムのカスタム セットアップ](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)」をご覧ください。

5. **[Select a virtual network...]\(参加させる...\)** チェック ボックスで、統合ランタイムを仮想ネットワークに参加させるかどうかを選択します。 仮想ネットワーク サービス エンドポイント/仮想ネットワーク内の Managed Instance で Azure SQL Database サーバーを使用して SSISDB をホストする場合、またはオンプレミスのデータにアクセスする必要がある、すなわち､SSIS パッケージにオンプレミスのデータ ソース/ターゲットがある場合は、[仮想ネットワークへの Azure-SSIS IR の参加](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)に関するページを参照してください｡ オンにした場合は、次の手順のようにします。

   ![仮想ネットワークの詳細設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

    a. **[サブスクリプション]** で、仮想ネットワークを含む Azure サブスクリプションを選択します。

    b. **[場所]** では、統合ランタイムと同じ場所が選択されています。

    c. **[種類]** で、仮想ネットワークの種類 (クラシックまたは Azure Resource Manager) を選択します。 クラシック仮想ネットワークは間もなく非推奨になるので、Azure Resource Manager 仮想ネットワークを選択することをお勧めします。

    d. **[VNet 名]** で、仮想ネットワークの名前を選択します。 この仮想ネットワークは、仮想ネットワーク サービス エンドポイント/仮想ネットワーク内の Managed Instance で SSISDB をホストするために Azure SQL Database サーバーに使用されているものと同じか、オンプレミスのネットワークに接続されているネットワークである必要があります。

    e. **[サブネット名]** で、お使いの仮想ネットワークのサブネットの名前を選択します。 これは、SSISDB をホストするために仮想ネットワーク内の Managed Instance に使われているものとは異なるサブネットにする必要があります。

6. **[VNet Validation]\(VNet の検証\)** をクリックし、成功した場合は、 **[完了]** をクリックして Azure-SSIS 統合ランタイムの作成を始めます。

    > [!NOTE]
    > カスタム セットアップ時間を除けば、このプロセスは 5 分以内に完了しますが、Azure SSIS IR が仮想ネットワークに参加するのに約 20 から 30 分かかる場合があります。
    >
    > SSISDB を使用する場合、ADF サービスは、SSISDB を準備するためにデータベース サーバーに接続します。 また、仮想ネットワーク (指定されている場合) に必要なアクセス許可と設定を構成し、Azure-SSIS IR を仮想ネットワークに参加させます。
    > 
    > Azure-SSIS IR をプロビジョニングすると、Access の再頒布可能パッケージと Azure Feature Pack for SSIS もインストールされます。 これらのコンポーネントは、組み込みのコンポーネントで既にサポートされているデータ ソースに加えて、Excel ファイルと Access ファイルのほか、さまざまな Azure データ ソースに対する接続を実現するものです。 追加のコンポーネントのインストールもできます。[Azure-SSIS IR のカスタム セットアップ](how-to-configure-azure-ssis-ir-custom-setup.md)に関する記事を参照してください。

7. **[接続]** ウィンドウで、必要に応じて **[Integration Runtimes]\(統合ランタイム\)** に切り替えます。 **[最新の情報に更新]** をクリックして、状態を更新します。

   ![作成の状態](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

8. **[アクション]** 列のリンクを使用して、統合ランタイムを停止/起動、編集、または削除します。 最後のリンクを使用すると、統合ランタイムの JSON コードを表示できます。 編集ボタンと削除ボタンは、IR が停止している場合にのみ有効になります。

   ![Azure SSIS IR - アクション](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>ポータルでの Azure SSIS 統合ランタイム

1. Azure Data Factory UI で、 **[編集]** タブに切り替えます。 **[接続]** をクリックし、 **[Integration Runtimes]\(統合ランタイム\)** タブに切り替えて、データ ファクトリ内の既存の統合ランタイムを表示します。

   ![既存の IR を表示する](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

2. **[新規]** をクリックして新しい Azure-SSIS IR を作成します。

   ![メニューによる統合ランタイム](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

3. Azure-SSIS 統合ランタイムを作成するには、図に示すように **[新規]** をクリックします。

4. [Integration Runtime Setup]\(統合ランタイムの設定\) ウィンドウで、 **[Lift-and-shift existing SSIS packages to execute in Azure]\(Azure で実行する既存の SSIS パッケージをリフトアンドシフトする\)** を選択し、 **[次へ]** をクリックします。

   ![統合ランタイムの種類を指定する](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

5. Azure-SSIS IR を設定する残りの手順については、「[Azure SSIS 統合ランタイムをプロビジョニングする](#provision-an-azure-ssis-integration-runtime)」セクションを参照してください。

## <a name="azure-powershell"></a>Azure PowerShell

このセクションでは、Azure PowerShell を使用して、Azure-SSIS IR を作成します。

### <a name="create-variables"></a>変数の作成

次のスクリプトをコピーして貼り付け、変数の値を指定します。 

```powershell
### Azure Data Factory information
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database server with virtual network service endpoints/Managed Instance in a virtual network/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database server with virtual network service endpoints or a different subnet than the one used for your Managed Instance in a virtual network

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.DNS prefix.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"
```

### <a name="sign-in-and-select-subscription"></a>サインインしてサブスクリプションを選択する

サインインして Azure サブスクリプションを選択するには、スクリプトに次のコードを追加します。

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>データベース サーバーへの接続を検証する

Azure SQL Database サーバー/Managed Instance を検証するには、次のスクリプトを追加します。

```powershell
# Validate only if you use SSISDB and do not use VNet or AAD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}
```

### <a name="configure-virtual-network"></a>仮想ネットワークを構成する

Azure-SSIS 統合ランタイムが参加するための仮想ネットワークのアクセス許可/設定を自動的に構成するには、次のスクリプトを追加します。

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>リソース グループの作成

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドで [Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)を作成します。 リソース グループとは、複数の Azure リソースをまとめてデプロイ、管理する際の論理コンテナーです。

リソース グループが既に存在する場合は、このコードをスクリプトにコピーしないでください。 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Data Factory の作成

次のコマンドを実行して、データ ファクトリを作成します。

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>統合ランタイムの作成

Azure 内で SSIS パッケージを実行する Azure-SSIS 統合ランタイムを作成するには、次のコマンドを実行します。

SSISDB を使用しない場合は、CatalogServerEndpoint、CatalogPricingTier、CatalogAdminCredential の各パラメーターを省略できます。

SSISDB をホストするために仮想ネットワーク サービス エンドポイント/仮想ネットワーク内の Managed Instance で Azure SQL Database サーバーを使用しない場合、またはオンプレミスのデータにアクセスする必要がない場合は、VNetId パラメーターと Subnet パラメーターを省略するか、それらのパラメーター値として空の値を渡すことができます｡ そうではない場合は、これらのパラメーターを省略することはできず、お使いの仮想ネットワーク構成の有効な値を渡す必要があります。「[仮想ネットワークへの Azure-SSIS IR の参加](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)」をご覧ください。

マネージ インスタンスを使って SSISDB をホストする場合は、CatalogPricingTier パラメーターを省略するか、空の値を渡すことができます。 それ以外の場合は、このパラメーターを省略することはできず、Azure SQL Database でサポートされる価格レベルの一覧から有効な値を渡す必要があります。[SQL Database リソースの制限](../sql-database/sql-database-resource-limits.md)に関するページをご覧ください。

Azure Active Directory (AAD) 認証と ADF のマネージド ID を使用してデータベース サーバーに接続する場合は、CatalogAdminCredential パラメーターを省略できますが、データベース サーバーへのアクセス許可を持つ AAD グループに ADF のマネージド ID を追加する必要があります。[Azure-SSIS IR に対する AAD 認証の有効化](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir)に関するページを参照してください。 それ以外の場合は、省略することができません。SQL 認証のためのサーバー管理者のユーザー名とパスワードから構成された有効なオブジェクトを渡す必要があります。

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add CatalogAdminCredential parameter if you do not use AAD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                   -DataFactoryName $DataFactoryName `
                                                   -Name $AzureSSISName `
                                                   -CatalogAdminCredential $serverCreds
    }
}

# Add SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
```

### <a name="start-integration-runtime"></a>統合ランタイムの起動

Azure-SSIS 統合ランタイムを起動するには、次のコマンドを実行します。

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

> [!NOTE]
> カスタム セットアップ時間を除けば、このプロセスは 5 分以内に完了しますが、Azure SSIS IR が仮想ネットワークに参加するのに約 20 から 30 分かかる場合があります。
>
> SSISDB を使用する場合、ADF サービスは、SSISDB を準備するためにデータベース サーバーに接続します。 また、仮想ネットワーク (指定されている場合) に必要なアクセス許可と設定を構成し、Azure-SSIS IR を仮想ネットワークに参加させます。
> 
> Azure-SSIS IR をプロビジョニングすると、Access の再頒布可能パッケージと Azure Feature Pack for SSIS もインストールされます。 これらのコンポーネントは、組み込みのコンポーネントで既にサポートされているデータ ソースに加えて、Excel ファイルと Access ファイルのほか、さまざまな Azure データ ソースに対する接続を実現するものです。 追加のコンポーネントのインストールもできます。[Azure-SSIS IR のカスタム セットアップ](how-to-configure-azure-ssis-ir-custom-setup.md)に関する記事を参照してください。

### <a name="full-script"></a>完全なスクリプト

Azure-SSIS 統合ランタイムを作成する完全なスクリプトを次に示します。

```powershell
### Azure Data Factory information
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database server with virtual network service endpoints/Managed Instance in a virtual network/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database server with virtual network service endpoints or a different subnet than the one used for your Managed Instance in a virtual network

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.DNS prefix.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"

### Sign in and select subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database server
# Validate only if you use SSISDB and do not use VNet or AAD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}

### Configure virtual network
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add CatalogAdminCredential parameter if you do not use AAD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                   -DataFactoryName $DataFactoryName `
                                                   -Name $AzureSSISName `
                                                   -CatalogAdminCredential $serverCreds
    }
}

# Add SetupScriptContainerSasUri parameter when you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

### Start integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="azure-resource-manager-template"></a>Azure Resource Manager テンプレート

このセクションでは、Azure Resource Manager テンプレートを使用して、Azure-SSIS 統合ランタイムを作成します。 サンプル チュートリアルを次に示します。

1. 次の Azure Resource Manager テンプレートを使用して、JSON ファイルを作成します。 山かっこ内の値 (プレース ホルダー) を独自の値に置き換えます。

    ```json
    {
      "contentVersion": "1.0.0.0",
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2018-06-01",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2018-06-01",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D8_v3",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```

2. Azure Resource Manager テンプレートをデプロイするには、次の例で示すように New-AzResourceGroupDeployment コマンドを実行します。ADFTutorialResourceGroup はリソース グループの名前、ADFTutorialARM.json はデータ ファクトリと Azure-SSIS IR の JSON 定義を含むファイルです。

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    このコマンドは、データ ファクトリを作成し、その中に Azure-SSIS IR を作成しますが、IR の起動は行いません。

3. Azure-SSIS IR を起動するには、Start-AzDataFactoryV2IntegrationRuntime コマンドを実行します。

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
                                                 -DataFactoryName "<Data Factory Name>" `
                                                 -Name "<Azure SSIS IR Name>" `
                                                 -Force
    ```

> [!NOTE]
> カスタム セットアップ時間を除けば、このプロセスは 5 分以内に完了しますが、Azure SSIS IR が仮想ネットワークに参加するのに約 20 から 30 分かかる場合があります。
>
> SSISDB を使用する場合、ADF サービスは、SSISDB を準備するためにデータベース サーバーに接続します。 また、仮想ネットワーク (指定されている場合) に必要なアクセス許可と設定を構成し、Azure-SSIS IR を仮想ネットワークに参加させます。
> 
> Azure-SSIS IR をプロビジョニングすると、Access の再頒布可能パッケージと Azure Feature Pack for SSIS もインストールされます。 これらのコンポーネントは、組み込みのコンポーネントで既にサポートされているデータ ソースに加えて、Excel ファイルと Access ファイルのほか、さまざまな Azure データ ソースに対する接続を実現するものです。 追加のコンポーネントのインストールもできます。[Azure-SSIS IR のカスタム セットアップ](how-to-configure-azure-ssis-ir-custom-setup.md)に関する記事を参照してください。

## <a name="deploy-ssis-packages"></a>SSIS パッケージのデプロイ

SSISDB を使用する場合は、そこにパッケージをデプロイし、データベース サーバーにそのサーバー エンドポイント経由で接続する SSDT ツールまたは SSMS ツールを使用して Azure-SSIS IR でそれらのパッケージを実行できます。  Azure SQL Database サーバー/仮想ネットワーク内の Managed Instance/パブリック エンドポイントを持つ Managed Instance についてのサーバー エンドポイントの形式は、それぞれ `<server name>.database.windows.net`/`<server name>.<dns prefix>.database.windows.net`/`<server name>.public.<dns prefix>.database.windows.net,3342` です。 SSISDB を使用しない場合、パッケージをファイル システム/ファイル共有/Azure Files にデプロイして、`dtinstall`/`dtutil`/`dtexec` コマンド ライン ユーティリティを使用して Azure-SSIS IR で実行できます。 詳しくは、[SSIS パッケージのデプロイ](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server)に関する記事を参照してください。 どちらの場合も、ADF パイプラインの SSIS パッケージの実行アクティビティを使用して、デプロイしたパッケージを Azure-SSIS IR で実行することもできます。[ファースト クラスの ADF アクティビティとしての SSIS パッケージ実行の呼び出し](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

このドキュメントに示す、Azure SSIS IR に関する他のトピックも参照してください。

- [Azure-SSIS 統合ランタイム](concepts-integration-runtime.md#azure-ssis-integration-runtime): この記事では、Azure-SSIS IR など、統合ランタイムに関する一般的な情報が説明されています。
- [Azure-SSIS IR の監視](monitor-integration-runtime.md#azure-ssis-integration-runtime): この記事では、Azure-SSIS IR についての情報を取得して理解するための方法を示します。
- [Azure-SSIS IR の管理](manage-azure-ssis-integration-runtime.md): この記事では、Azure-SSIS IR を停止、開始、または削除する方法を示します。また、ノードを追加することによって Azure-SSIS IR をスケールアウトする方法も示します。
- [仮想ネットワークへの Azure-SSIS IR の参加](join-azure-ssis-integration-runtime-virtual-network.md): この記事では、仮想ネットワークへの Azure-SSIS IR の参加に関する情報が説明されています。