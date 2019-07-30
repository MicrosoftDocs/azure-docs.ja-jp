---
title: Azure-SSIS 統合ランタイムのプロビジョニング | Microsoft Docs
description: Azure 上で SSIS パッケージをデプロイして実行できるように、Azure-SSIS 統合ランタイムを Azure Data Factory にプロビジョニングする方法について説明します。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: tutorial
ms.date: 06/26/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: bd2c055d2f7f1e90918cb160cfdebfe88f7f8ea4
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484788"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Azure Data Factory に Azure-SSIS 統合ランタイムをプロビジョニングする

このチュートリアルでは、Azure portal を使用して、Azure-SQL Server Integration Services (SSIS) 統合ランタイム (IR) を Azure Data Factory (ADF) にプロビジョニングする手順について説明します。 Azure-SSIS IR では、Azure SQL Database サーバー/マネージド インスタンスによってホストされている SSIS カタログ (SSISDB) にデプロイされたパッケージ (プロジェクト デプロイ モデル) と、ファイル システム/ファイル共有/Azure Files にデプロイされたパッケージ (パッケージ デプロイ モデル) の実行がサポートされます。 Azure-SSIS IR がプロビジョニングされると、SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) などの使い慣れたツールや `dtinstall`/`dtutil`/`dtexec` などのコマンド ライン ユーティリティを使用して、Azure でパッケージをデプロイして実行することができます。 Azure-SSIS IR の概念については、[Azure-SSIS 統合ランタイムの概要](concepts-integration-runtime.md#azure-ssis-integration-runtime)に関する記事を参照してください。

このチュートリアルでは、次の手順を実行します。

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * Azure-SSIS 統合ランタイムをプロビジョニングします。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure サブスクリプション**。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
- **Azure SQL Database サーバー (省略可能)** 。 まだデータベース サーバーをお持ちでない場合は、あらかじめ Azure portal で作成しておいてください。 その後、ADF によって、このデータベース サーバーに SSISDB が作成されます。 このデータベース サーバーは、統合ランタイムと同じ Azure リージョンに作成することをお勧めします。 この構成により、統合ランタイムは、Azure リージョンにまたがることなく SSISDB に実行ログを書き込むことができます。 
    - SSISDB は、選択したデータベース サーバーに基づいて、1 つのデータベースやエラスティック プールの一部として、またはマネージド インスタンス上に自動的に作成でき､パブリック ネットワークから､または仮想ネットワークに参加することでアクセスできます。 SSISDB をホストするデータベース サーバーの種類を選択する際のガイダンスについては、[Azure SQL Database の単一データベース/エラスティック プール/マネージド インスタンスの比較](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance)に関する記事を参照してください。 仮想ネットワーク サービス エンドポイント/仮想ネットワーク内のマネージド インスタンスで Azure SQL Database サーバーを使用して、SSISDB をホストするか、オンプレミスのデータにアクセスする必要がある場合は、Azure-SSIS IR を仮想ネットワークに参加させる必要があります。[仮想ネットワークでの Azure-SSIS IR の作成](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)に関する記事を参照してください。
    - データベース サーバーで **[Azure サービスへのアクセスを許可]** の設定が有効になっていることを確認します。 仮想ネットワーク サービス エンドポイント/仮想ネットワーク内のマネージド インスタンスで Azure SQL Database サーバーを使用して SSISDB をホストする場合、これは適用されません。 詳細については、「[Azure SQL データベースのセキュリティ保護](../sql-database/sql-database-security-tutorial.md#create-firewall-rules)」を参照してください。 PowerShell を使用してこの設定を有効にするには、「[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)」を参照してください。
    - クライアント マシンの IP アドレス、またはクライアント マシンの IP アドレスを含む IP アドレスの範囲を、データベース サーバーのファイアウォール設定にあるクライアント IP アドレスの一覧に追加します。 詳細については、「[Azure SQL Database のサーバーレベルとデータベースレベルのファイアウォール規則](../sql-database/sql-database-firewall-configure.md)」を参照してください。
    - SQL 認証とサーバー管理者の資格情報、または Azure Active Directory (AAD) 認証と ADF のマネージド ID を使用して、データベース サーバーに接続できます。 後者の場合、データベース サーバーへのアクセス許可が割り当てられている AAD グループに ADF のマネージド ID を追加する必要があります。[AAD 認証を使用する場合の Azure-SSIS IR の作成](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)に関するページを参照してください。
    - データベース サーバーに SSISDB がまだないことを確認します。 Azure-SSIS IR のプロビジョニングでは、既存の SSISDB の使用がサポートされていません。

> [!NOTE]
> - ADF と Azure-SSIS IR が現在使用可能な Azure リージョンの一覧については、[リージョン別の ADF + SSIS IR の利用可能性](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)に関するページを参照してください。 

## <a name="create-a-data-factory"></a>Data Factory を作成する。

1. Web ブラウザー (**Microsoft Edge** または **Google Chrome**) を起動します。 現在、Data Factory の UI がサポートされる Web ブラウザーは Microsoft Edge と Google Chrome だけです。 
1. [Azure Portal](https://portal.azure.com/) にサインインします。 
1. 左側のメニューの **[新規]** を選択し、 **[データ + 分析]** を選択して、 **[Data Factory]** を選択します。 

   ![[新規] ウィンドウでの [Data Factory] の選択](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)

1. **[新しいデータ ファクトリ]** ページで、 **[名前]** に「**MyAzureSsisDataFactory**」と入力します。 

   ![[新しいデータ ファクトリ] ページ](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)

   Azure データ ファクトリの名前は *グローバルに一意*にする必要があります。 次のエラーが発生した場合は、データ ファクトリの名前を変更して ( **&lt;yourname&gt;MyAzureSsisDataFactory** など) 作成し直してください。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事を参照してください。 

   `Data factory name “MyAzureSsisDataFactory” is not available`

1. **[サブスクリプション]** で、データ ファクトリを作成する Azure サブスクリプションを選択します。 
1. **リソース グループ**について、次の手順のいずれかを行います。 

   - **[既存のものを使用]** を選択し、一覧から既存のリソース グループを選択します。 
   - **[新規作成]** を選択し、リソース グループの名前を入力します。 

   リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/resource-group-overview.md)に関するページを参照してください。 
1. **[バージョン]** では、 **[V2 (プレビュー)]** を選択します。 
1. **[場所]** で、データ ファクトリの場所を選択します。 一覧には、データ ファクトリの作成がサポートされている場所のみが表示されます。 
1. **[ダッシュボードにピン留めする]** をオンにします。 
1. **作成** を選択します。 
1. ダッシュボードに、 **[Deploying Data Factory]\(データ ファクトリをデプロイしています\)** というステータスを示した次のタイルが表示されます。 

   ![[Deploying data factory]\(データ ファクトリをデプロイしています\) タイル](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)

1. 作成が完了すると、 **[データ ファクトリ]** ページが表示されます。 

   ![データ ファクトリのホーム ページ](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)

1. **[作成と監視]** を選択して、別のタブで Data Factory ユーザー インターフェイス (UI) を開きます。 

## <a name="create-an-azure-ssis-integration-runtime"></a>Azure-SSIS 統合ランタイムを作成します

### <a name="from-the-data-factory-overview"></a>Data Factory の概要から

1. **[Let's get started]\(始めましょう\)** ページで、 **[Configure SSIS Integration Runtime]\(SSIS 統合ランタイムの構成\)** タイルをクリックします。 

   ![[Configure SSIS Integration Runtime]\(SSIS 統合ランタイムの構成\) タイル](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Azure-SSIS IR を設定する残りの手順については、「[Azure SSIS 統合ランタイムをプロビジョニングする](#provision-an-azure-ssis-integration-runtime)」セクションを参照してください。 

### <a name="from-the-authoring-ui"></a>オーサリング UI から

1. Azure Data Factory UI で、 **[編集]** タブに切り替えます。 **[接続]** を選択し、 **[Integration Runtimes]\(統合ランタイム\)** タブに切り替えて、データ ファクトリ内の既存の統合ランタイムを表示します。 

   ![既存の IR を表示するための選択](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. **[新規]** を選択して Azure-SSIS IR を作成します。 

   ![メニューによる統合ランタイム](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. **[Integration Runtime Setup]\(統合ランタイムの設定\)** ウィンドウで、 **[Lift-and-shift existing SSIS packages to execute in Azure]\(Azure で実行する既存の SSIS パッケージをリフトアンドシフトする\)** を選択し、 **[次へ]** を選択します。 

   ![統合ランタイムの種類を指定する](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Azure-SSIS IR を設定する残りの手順については、「[Azure SSIS 統合ランタイムをプロビジョニングする](#provision-an-azure-ssis-integration-runtime)」セクションを参照してください。 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Azure-SSIS 統合ランタイムのプロビジョニング

1. **[Integration Runtime Setup]\(統合ランタイムの設定\)** の **[全般設定]** ページで、次の手順を実行します。 

   ![全般設定](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. **[名前]** に統合ランタイムの名前を入力します。 

   b. **[説明]** に統合ランタイムの説明を入力します。 

   c. **[場所]** で、統合ランタイムの場所を選択します。 サポートされている場所のみが表示されます。 SSISDB をホストするデータベース サーバーと同じ場所を選択することをお勧めします。 

   d. **[ノード サイズ]** で、統合ランタイム クラスター内のノードのサイズを選択します。 サポートされているノード サイズのみが表示されます。 コンピューティングやメモリを大量に使用するパッケージを多数実行する場合は、大きなノード サイズを選択します (スケールアップ)。 

   e. **[Node Number]\(ノード数\)** で、統合ランタイム クラスター内のノードの数を選択します。 サポートされているノード数のみが表示されます。 多くのパッケージを並列で実行する場合は、ノード数が多い大規模なクラスターを選択します (スケールアウト)。 

   f. **[Edition/License]\(エディション/ライセンス)** で、統合ランタイムの SQL Server エディション/ライセンス([Standard] または [Enterprise]) を選択します。 統合ランタイムで高度な機能やプレミアム機能を使用する場合は、[Enterprise] を選択します。 

   g. **[お金を節約]** で、統合ランタイムの Azure ハイブリッド特典 (AHB) オプション([はい] または [いいえ]) を選択します。 ソフトウェア アシュアランス付き SQL Server ライセンスを移行して、ハイブリッド使用によるコスト節約の恩恵を受ける場合は、[はい] を選択します。 

   h. **[次へ]** をクリックします。 

1. **[SQL の設定]** ページで、次の手順を完了します。 

   ![SQL の設定](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. **[Create SSIS catalog]\(SSIS カタログの作成\)** チェック ボックスで、パッケージを Azure SSIS IR 上で実行するためのデプロイ モデルとして次のいずれかを選択します。データベース サーバーによってホストされている SSISDB にパッケージがデプロイされるプロジェクト デプロイ モデルか、ファイル システム/ファイル共有/Azure Files にパッケージがデプロイされるパッケージ デプロイ モデル。 このチェック ボックスをオンにした場合、自動的に作成および管理される SSISDB をホストするデータベース サーバーを用意する必要があります。
   
   b. **[サブスクリプション]** で、SSISDB をホストするデータベース サーバーを保有する Azure サブスクリプションを選択します。 

   c. **[場所]** で、SSISDB をホストするデータベース サーバーの場所を選択します。 統合ランタイムと同じ場所を選択することをお勧めします。 

   d. **[Catalog Database Server Endpoint]\(カタログ データベース サーバー エンドポイント\)** で、SSISDB をホストするデータベース サーバーのエンドポイントを選択します。 SSISDB は、選択したデータベース サーバーに基づいて、1 つのデータベースやエラスティック プールの一部として、またはマネージド インスタンス上に自動的に作成でき､パブリック ネットワークから､または仮想ネットワークに参加することでアクセスできます。 SSISDB をホストするデータベース サーバーの種類を選択する際のガイダンスについては、[Azure SQL Database の単一データベース/エラスティック プール/マネージド インスタンスの比較](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance)に関する記事を参照してください。 仮想ネットワーク サービス エンドポイント/仮想ネットワーク内のマネージド インスタンスで Azure SQL Database サーバーを選択して、SSISDB をホストするか、オンプレミスのデータにアクセスする必要がある場合は、Azure-SSIS IR を仮想ネットワークに参加させる必要があります。[仮想ネットワークでの Azure-SSIS IR の作成](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)に関する記事を参照してください。 

   e. **[Use AAD authentication]\(AAD 認証を使用する\)** チェック ボックスで、SSISDB をホストするデータベース サーバーの認証方法としてSQL 認証か、ADF のマネージド ID を使用する AAD 認証を選択します。 このチェック ボックスをオンにすると、データベース サーバーへのアクセス許可が割り当てられている AAD グループに ADF のマネージド ID を追加する必要があります。[AAD 認証を使用する場合の Azure-SSIS IR の作成](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)に関するページを参照してください。 

   f. **[管理者ユーザー名]** に、SSISDB をホストするデータベース サーバーの SQL 認証ユーザー名を入力します。 

   g. **[管理者パスワード]** に、SSISDB をホストするデータベース サーバーの SQL 認証パスワードを入力します。 

   h. **[Catalog Database Service Tier]\(カタログ データベースのサービス レベル)** で、SSISDB をホストするデータベース サーバーのサービス レベル (Basic/Standard/Premium レベルまたはエラスティック プール名) を選択します。 

   i. **[接続テスト]** をクリックし、成功した場合は **[次へ]** をクリックします。 

1. **[詳細設定]** ページで、次の手順を実行します。 

   ![詳細設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. **[Maximum Parallel Executions Per Node]\(ノードあたりの最大並列実行数\)** で、統合ランタイム クラスター内のノードあたりの同時に実行するパッケージの最大数を選択します。 サポートされているパッケージ数のみが表示されます。 コンピューティングやメモリを大量に使用する 1 つの大規模/重量なパッケージを実行するために複数のコアを使用する場合は、低い数値を選択します。 1 つのコアで 1 つ以上の小規模/軽量なパッケージを実行する場合は、高い数値を選択します。 

   b. **[Custom Setup Container SAS URI]\(カスタム セットアップ コンテナー SAS URI\)** に、必要に応じて、セットアップ スクリプトとその関連ファイルが格納されている Azure Storage Blob コンテナーの Shared Access Signature (SAS) Uniform Resource Identifier (URI) を入力します。「[Azure SSIS 統合ランタイムのカスタム セットアップ](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)」をご覧ください。 

   c. **[Select a VNet for your Azure-SSIS Integration Runtime to join and allow Azure services to configure VNet permissions/settings]\(参加させる Azure-SSIS 統合ランタイム用の VNet を選択し、Azure サービスが VNet の権限/設定を構成することを許可する\)** チェック ボックスで、統合ランタイムを仮想ネットワークに参加させるかどうかを選択します。 仮想ネットワーク サービス エンドポイント/仮想ネットワーク内のマネージド インスタンスで Azure SQL Database サーバーを使用して、SSISDB をホストするか、オンプレミスのデータにアクセスする必要がある場合は、このチェック ボックスをオンにする必要があります。[仮想ネットワークでの Azure-SSIS IR の作成](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)に関するページを参照してください。 

1. **[完了]** をクリックして、統合ランタイムの作成を開始します。 

   > [!NOTE]
   > この処理は、カスタムのセットアップ時間があればそれを除いて、5 分以内で完了するはずです。
   >
   > SSISDB を使用する場合、ADF サービスは、SSISDB を準備するためにデータベース サーバーに接続します。 
   > 
   > Azure-SSIS IR をプロビジョニングすると、Access の再頒布可能パッケージと Azure Feature Pack for SSIS もインストールされます。 これらのコンポーネントは、組み込みのコンポーネントで既にサポートされているデータ ソースに加えて、Excel ファイルと Access ファイルのほか、さまざまな Azure データ ソースに対する接続を実現するものです。 追加のコンポーネントのインストールもできます。[Azure-SSIS IR のカスタム セットアップ](how-to-configure-azure-ssis-ir-custom-setup.md)に関する記事を参照してください。

1. **[接続]** タブで、必要に応じて **[Integration Runtimes]\(統合ランタイム\)** に切り替えます。 **[最新の情報に更新]** を選択して、状態を更新します。 

   ![作成状態、[最新の情報に更新] ボタン](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. **[アクション]** 列のリンクを使用して、統合ランタイムを停止/起動、編集、または削除します。 最後のリンクを使用すると、統合ランタイムの JSON コードを表示できます。 編集ボタンと削除ボタンは、IR が停止している場合にのみ有効になります。 

   ![[アクション] 列のリンク](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="deploy-ssis-packages"></a>SSIS パッケージのデプロイ

SSISDB を使用する場合は、そこにパッケージをデプロイし、データベース サーバーにそのサーバー エンドポイント経由で接続する SSDT ツールまたは SSMS ツールを使用して Azure-SSIS IR でそれらのパッケージを実行できます。 パブリック エンドポイントを持つ Azure SQL Database サーバー/マネージド インスタンスの場合、サーバー エンドポイントの形式はそれぞれ `<server name>.database.windows.net`/`<server name>.public.<dns prefix>.database.windows.net,3342` です。 SSISDB を使用しない場合、パッケージをファイル システム/ファイル共有/Azure Files にデプロイして、`dtinstall`/`dtutil`/`dtexec` コマンド ライン ユーティリティを使用して Azure-SSIS IR で実行できます。 詳しくは、[SSIS パッケージのデプロイ](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server)に関する記事を参照してください。 どちらの場合も、ADF パイプラインの SSIS パッケージの実行アクティビティを使用して、デプロイしたパッケージを Azure-SSIS IR で実行することもできます。[ファースト クラスの ADF アクティビティとしての SSIS パッケージ実行の呼び出し](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)に関する記事を参照してください。 

SSIS ドキュメントの次の記事もご覧ください。 

- [Azure での SSIS パッケージのデプロイ、実行、監視](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Azure での SSISDB への接続](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Azure でのパッケージ実行のスケジュール設定](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Windows 認証を使用したオンプレミス データ ソースへの接続](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。 

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * Azure-SSIS 統合ランタイムをプロビジョニングします。
> * SSIS パッケージのデプロイ

Azure-SSIS Integration Runtime のカスタマイズの詳細については、次の記事に進んでください。 

> [!div class="nextstepaction"]
> [Azure-SSIS IR のカスタマイズ](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)