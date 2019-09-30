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
ms.date: 09/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: b3b180fe4b465f3e0c7de888043326fd1a43cf23
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2019
ms.locfileid: "71009644"
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
- **Azure SQL Database サーバー (省略可能)** 。 まだデータベース サーバーをお持ちでない場合は、あらかじめ Azure portal で作成しておいてください。 その後、ADF によって、このデータベース サーバーに SSISDB が作成されます。 このデータベース サーバーは、統合ランタイムと同じ Azure リージョンに作成することをお勧めします。 この構成により、統合ランタイムは複数の Azure リージョンにまたがることなく SSISDB に実行ログを書き込むことができます。 
    - SSISDB は、選択したデータベース サーバーに基づいて、1 つのデータベースやエラスティック プールの一部として、またはマネージド インスタンス上に自動的に作成でき､パブリック ネットワークから､または仮想ネットワークに参加することでアクセスできます。 SSISDB をホストするデータベース サーバーの種類を選択するときのガイダンスについては、[Azure SQL Database の単一データベース/エラスティック プールと Managed Instance の比較](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance)に関するセクションを参照してください。 仮想ネットワーク サービス エンドポイントを備えた Azure SQL Database サーバー (またはプライベート エンドポイントを備えた Managed Instance) を使用して SSISDB をホストする場合、またはセルフホステッド IR を構成せずにオンプレミスのデータにアクセスする必要がある場合は、Azure-SSIS IR を仮想ネットワークに参加させる必要があります。[仮想ネットワークでの Azure-SSIS IR の作成](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)に関する記事を参照してください。
    - データベース サーバーで **[Azure サービスへのアクセスを許可]** の設定が有効になっていることを確認します。 仮想ネットワーク サービス エンドポイントを備えた Azure SQL Database サーバー (またはプライベート エンドポイントを備えた Managed Instance) を使用して SSISDB をホストする場合、これは該当しません。 詳細については、「[Azure SQL データベースのセキュリティ保護](../sql-database/sql-database-security-tutorial.md#create-firewall-rules)」を参照してください。 PowerShell を使用してこの設定を有効にするには、「[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)」を参照してください。
    - クライアント マシンの IP アドレス、またはクライアント マシンの IP アドレスを含む IP アドレスの範囲を、データベース サーバーのファイアウォール設定にあるクライアント IP アドレスの一覧に追加します。 詳細については、「[Azure SQL Database のサーバーレベルとデータベースレベルのファイアウォール規則](../sql-database/sql-database-firewall-configure.md)」を参照してください。
    - サーバー管理者の資格情報による SQL 認証または ADF のマネージド ID による Azure Active Directory (AAD) 認証を使用して、データベース サーバーに接続できます。 後者の場合、データベース サーバーへのアクセス許可が割り当てられている AAD グループに ADF のマネージド ID を追加する必要があります。[AAD 認証を使用する場合の Azure-SSIS IR の作成](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)に関するページを参照してください。
    - データベース サーバーに SSISDB がまだないことを確認します。 Azure-SSIS IR のプロビジョニングでは、既存の SSISDB の使用がサポートされていません。

> [!NOTE]
> - ADF と Azure-SSIS IR が現在使用可能な Azure リージョンの一覧については、[リージョン別の ADF + SSIS IR の利用可能性](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)に関するページを参照してください。 

## <a name="create-a-data-factory"></a>Data Factory の作成

Azure portal で ADF を作成するには、[UI を使用した ADF の作成](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)に関する記事の手順に従ってください。その際、作成後にすぐアクセスできるよう、 **[ダッシュボードにピン留めする]** を選択します。 

ADF が作成されたら、Azure portal でその概要ページを開き、 **[Author & Monitor]\(作成と監視\)** タイルをクリックして、その **[Let's get started]\(始めましょう\)** ページを別のタブで起動します。続けて Azure-SSIS IR の作成に進むことができます。   

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

   d. **[Catalog Database Server Endpoint]\(カタログ データベース サーバー エンドポイント\)** で、SSISDB をホストするデータベース サーバーのエンドポイントを選択します。 SSISDB は、選択したデータベース サーバーに基づいて、1 つのデータベースやエラスティック プールの一部として、またはマネージド インスタンス上に自動的に作成でき､パブリック ネットワークから､または仮想ネットワークに参加することでアクセスできます。 SSISDB をホストするデータベース サーバーの種類を選択するときのガイダンスについては、[Azure SQL Database の単一データベース/エラスティック プールと Managed Instance の比較](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance)に関するセクションを参照してください。 仮想ネットワーク サービス エンドポイントを備えた Azure SQL Database サーバー (またはプライベート エンドポイントを備えた Managed Instance) を選択して SSISDB をホストする場合、またはセルフホステッド IR を構成せずにオンプレミスのデータにアクセスする必要がある場合は、Azure-SSIS IR を仮想ネットワークに参加させる必要があります。[仮想ネットワークでの Azure-SSIS IR の作成](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)に関する記事を参照してください。 

   e. **[Use AAD authentication]\(AAD 認証を使用する\)** チェック ボックスで、SSISDB をホストするデータベース サーバーの認証方法としてSQL 認証か、ADF のマネージド ID を使用する AAD 認証を選択します。 このチェック ボックスをオンにすると、データベース サーバーへのアクセス許可が割り当てられている AAD グループに ADF のマネージド ID を追加する必要があります。[AAD 認証を使用する場合の Azure-SSIS IR の作成](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)に関するページを参照してください。 

   f. **[管理者ユーザー名]** に、SSISDB をホストするデータベース サーバーの SQL 認証ユーザー名を入力します。 

   g. **[管理者パスワード]** に、SSISDB をホストするデータベース サーバーの SQL 認証パスワードを入力します。 

   h. **[Catalog Database Service Tier]\(カタログ データベースのサービス レベル)** で、SSISDB をホストするデータベース サーバーのサービス レベル (Basic/Standard/Premium レベルまたはエラスティック プール名) を選択します。

   i. **[接続テスト]** をクリックし、成功した場合は **[次へ]** をクリックします。 

1. **[詳細設定]** ページで、次の手順を実行します。 

   ![詳細設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. **[Maximum Parallel Executions Per Node]\(ノードあたりの最大並列実行数\)** で、統合ランタイム クラスター内のノードあたりの同時に実行するパッケージの最大数を選択します。 サポートされているパッケージ数のみが表示されます。 コンピューティングやメモリを大量に使用する 1 つの大規模/重量なパッケージを実行するために複数のコアを使用する場合は、低い数値を選択します。 1 つのコアで 1 つ以上の小規模/軽量なパッケージを実行する場合は、高い数値を選択します。 

   b. **[Custom Setup Container SAS URI]\(カスタム セットアップ コンテナー SAS URI\)** に、必要に応じて、セットアップ スクリプトとその関連ファイルが格納されている Azure Storage Blob コンテナーの Shared Access Signature (SAS) Uniform Resource Identifier (URI) を入力します。「[Azure SSIS 統合ランタイムのカスタム セットアップ](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)」をご覧ください。 

   c. **[Select a VNet for your Azure-SSIS Integration Runtime to join and allow Azure services to configure VNet permissions/settings]\(参加させる Azure-SSIS 統合ランタイム用の VNet を選択し、Azure サービスが VNet の権限/設定を構成することを許可する\)** チェック ボックスで、統合ランタイムを仮想ネットワークに参加させるかどうかを選択します。 仮想ネットワーク サービス エンドポイントを備えた Azure SQL Database サーバー (またはプライベート エンドポイントを備えた Managed Instance) を使用して SSISDB をホストするのか、またはセルフホステッド IR を構成せずにオンプレミスのデータにアクセスする必要があるのかを確認する必要があります。[仮想ネットワークでの Azure-SSIS IR の作成](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)に関する記事を参照してください。 

   d. **[Set up Self-Hosted]\(セルフホステッドの設定\)** チェック ボックスで、セルフホステッド IR を Azure-SSIS IR のプロキシとして構成するかどうかを選択します。[セルフホステッド IR をプロキシとして設定](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis)する方法に関するページを参照してください。 

1. **[次へ]** をクリックします。 

1. **[Summary]\(概要\)** ページで、すべてのプロビジョニング設定を確認し、推奨されるドキュメントのリンクをブックマークに登録します。その後 **[完了]** をクリックすると、統合ランタイムの作成が開始されます。 

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