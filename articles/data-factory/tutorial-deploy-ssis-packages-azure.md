---
title: Azure-SSIS 統合ランタイムをプロビジョニングする
description: Azure 上で SSIS パッケージをデプロイして実行できるように、Azure-SSIS 統合ランタイムを Azure Data Factory にプロビジョニングする方法について説明します。
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 02/22/2021
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 7c439d71806d2deba508ce35131f21ebfbd7a3ec
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740411"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Azure Data Factory に Azure-SSIS Integration Runtime をプロビジョニングする

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

このチュートリアルでは、Azure portal を使用して、Azure-SQL Server Integration Services (SSIS) 統合ランタイム (IR) を Azure Data Factory (ADF) にプロビジョニングする手順について説明します。 Azure-SSIS IR では、次のことがサポートされます。

- Azure SQL Database サーバーまたは Managed Instance をホストとする SSIS カタログ (SSISDB) にデプロイされたパッケージを実行する (プロジェクト デプロイ モデル)
- Azure SQL Managed Instance にホストされた SQL Server データベース (MSDB)、Azure Files、ファイル システムのいずれかにデプロイされたパッケージを実行する (パッケージ デプロイ モデル)

Azure-SSIS IR のプロビジョニング後は、使い慣れたツールを使用して、Azure にパッケージをデプロイして実行することができます。 これらのツールは既に Azure に対応しています。たとえば、SQL Server Data Tools (SSDT) や SQL Server Management Studio (SSMS) のほか、[dtutil](/sql/integration-services/dtutil-utility)、[AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md) などのコマンド ライン ユーティリティがあります。

Azure-SSIS IR の概念については、[Azure-SSIS 統合ランタイムの概要](concepts-integration-runtime.md#azure-ssis-integration-runtime)に関する記事を参照してください。

このチュートリアルでは、次の手順を実行します。

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * Azure-SSIS 統合ランタイムをプロビジョニングします。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure サブスクリプション**。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

- **Azure SQL Database サーバー (省略可能)** 。 まだデータベース サーバーをお持ちでない場合は、あらかじめ Azure Portal でデータベース サーバーを作成しておいてください。 その後、Data Factory によって、このデータベース サーバーに SSISDB インスタンスが作成されます。 

  このデータベース サーバーは、統合ランタイムと同じ Azure リージョンに作成することをお勧めします。 この構成により、統合ランタイムは、Azure リージョンをまたがることなく SSISDB に実行ログを書き込むことができます。

  次の点に留意してください。

  - SSISDB インスタンスは、選択したデータベース サーバーに基づいて、単一データベースやエラスティック プールの一部として、またはマネージド インスタンス上に自動的に作成できます。 パブリック ネットワークからアクセスできるほか、仮想ネットワークに参加させてアクセスすることもできます。 SSISDB をホストするデータベース サーバーの種類を選択する際のガイダンスについては、[SQL Database と SQL Managed Instance の比較](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance)に関するセクションを参照してください。 
  
    IP ファイアウォール規則/仮想ネットワーク サービス エンドポイントを備えた Azure SQL Database サーバーまたはプライベート エンドポイントを備えたマネージド インスタンスを使用して SSISDB をホストする場合、またはセルフホステッド IR を構成せずにオンプレミスのデータへのアクセスを必要としている場合は、Azure-SSIS IR を仮想ネットワークに参加させる必要があります。 詳細については、[仮想ネットワークへの Azure-SSIS IR の作成](./create-azure-ssis-integration-runtime.md)に関するページを参照してください。

  - データベース サーバーで **[Azure サービスへのアクセスを許可]** の設定が有効になっていることを確認します。 この設定は、IP ファイアウォール規則/仮想ネットワーク サービス エンドポイントを備えた Azure SQL Database サーバーまたはプライベート エンドポイントを備えたマネージド インスタンスを使用して SSISDB をホストする場合は適用されません。 詳細については、「[Azure SQL Database のセキュリティ保護](../azure-sql/database/secure-database-tutorial.md#create-firewall-rules)」を参照してください。 PowerShell を使用してこの設定を有効にするには、「[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)」を参照してください。

  - クライアント マシンの IP アドレス、またはクライアント マシンの IP アドレスを含む IP アドレスの範囲を、データベース サーバーのファイアウォール設定にあるクライアント IP アドレスの一覧に追加します。 詳細については、「[Azure SQL Database のサーバーレベルとデータベースレベルのファイアウォール規則](../azure-sql/database/firewall-configure.md)」を参照してください。

  - サーバー管理者の資格情報による SQL 認証またはデータ ファクトリのマネージド ID による Azure AD 認証を使用して、データベース サーバーに接続できます。 後者の場合、データベース サーバーへのアクセス許可が割り当てられている Azure AD グループにデータ ファクトリのマネージド ID を追加する必要があります。 詳細については、[Azure AD 認証を使用した Azure-SSIS IR の作成](./create-azure-ssis-integration-runtime.md)に関するページを参照してください。

  - データベース サーバーに SSISDB インスタンスがまだないことを確認します。 Azure-SSIS IR のプロビジョニングでは、既存の SSISDB インスタンスの使用はサポートされていません。

> [!NOTE]
> Data Factory と Azure-SSIS IR が現在使用可能な Azure リージョンの一覧については、[Data Factory と SSIS IR が利用可能なリージョン](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)に関するページを参照してください。 

## <a name="create-a-data-factory"></a>Data Factory の作成

Azure portal でデータ ファクトリを作成するには、[UI を使用してデータ ファクトリを作成する](./quickstart-create-data-factory-portal.md#create-a-data-factory)手順に従ってください。 その時に、作成後にすぐアクセスできるよう、 **[ダッシュボードにピン留めする]** を選択します。 

データ ファクトリが作成されたら、その概要ページが Azure portal で開きます。 **[作成と監視]** タイルを選択して、別のタブで **[Let's get started]\(はじめに\)** ページを開きます。そこから Azure-SSIS IR の作成に進むことができます。

## <a name="create-an-azure-ssis-integration-runtime"></a>Azure-SSIS 統合ランタイムを作成します

### <a name="from-the-data-factory-overview"></a>Data Factory の概要から

1. **[Let's get started]\(始めましょう\)** ページで、 **[Configure SSIS Integration Runtime]\(SSIS 統合ランタイムの構成\)** タイルをクリックします。 

   ![[Configure SSIS Integration Runtime]\(SSIS 統合ランタイムの構成\) タイル](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Azure-SSIS IR を設定する残りの手順については、「[Azure SSIS 統合ランタイムをプロビジョニングする](#provision-an-azure-ssis-integration-runtime)」セクションを参照してください。 

### <a name="from-the-authoring-ui"></a>オーサリング UI から

1. Azure Data Factory の UI で、 **[編集]** タブに切り替えて **[接続]** を選択します。 次に、 **[Integration Runtimes]\(統合ランタイム\)** タブに切り替えて、データ ファクトリ内の既存の統合ランタイムを表示します。 

   ![既存の IR を表示するための選択](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. **[New]\(新規\)** を選択して Azure-SSIS IR を作成し、 **[Integration runtime setup]\(統合ランタイムのセットアップ\)** ペインを開きます。 

   ![メニューによる統合ランタイム](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. **[統合ランタイムのセットアップ]** ペインで、 **[Lift-and-shift existing SSIS packages to execute in Azure]\(Azure で実行する既存の SSIS パッケージをリフト アンド シフトする\)** タイルを選択してから、 **[次へ]** を選択します。

   ![統合ランタイムの種類を指定する](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Azure-SSIS IR を設定する残りの手順については、「[Azure SSIS 統合ランタイムをプロビジョニングする](#provision-an-azure-ssis-integration-runtime)」セクションを参照してください。 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Azure-SSIS 統合ランタイムのプロビジョニング

**[Integration runtime setup]\(統合ランタイムのセットアップ\)** ペインには、全般設定、デプロイ設定、詳細設定という一連の構成を行うための 3 つのページがあります。

### <a name="general-settings-page"></a>[全般設定] ページ

**[統合ランタイムのセットアップ]** ペインの **[全般設定]** ページで、次の手順を行います。 

   ![全般設定](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. **[名前]** に統合ランタイムの名前を入力します。 

   1. **[説明]** に統合ランタイムの説明を入力します。 

   1. **[場所]** で、統合ランタイムの場所を選択します。 サポートされている場所のみが表示されます。 SSISDB をホストするデータベース サーバーと同じ場所を選択することをお勧めします。 

   1. **[ノード サイズ]** で、統合ランタイム クラスター内のノードのサイズを選択します。 サポートされているノード サイズのみが表示されます。 コンピューティングやメモリを大量に使用するパッケージを多数実行する場合は、大きなノード サイズを選択します (スケールアップ)。 

   1. **[Node Number]\(ノード数\)** で、統合ランタイム クラスター内のノードの数を選択します。 サポートされているノード数のみが表示されます。 多くのパッケージを並列で実行する場合は、ノード数が多い大規模なクラスターを選択します (スケールアウト)。 

   1. **[Edition/License]\(エディション/ライセンス\)** で、統合ランタイムの SQL Server エディション ([Standard] または [Enterprise]) を選択します。 統合ランタイムで高度な機能を使用する場合は、[Enterprise] を選択します。 

   1. **[お金を節約]** で、統合ランタイムの Azure ハイブリッド特典オプション ( **[はい]** または **[いいえ]** ) を選択します。 ソフトウェア アシュアランス付き SQL Server ライセンスを移行して、ハイブリッド使用によるコスト節約の利点を得るには、 **[はい]** を選択します。 

   1. **[次へ]** を選択します。 

### <a name="deployment-settings-page"></a>[デプロイ設定] ページ

**[Integration runtime setup]\(統合ランタイムのセットアップ\)** ペインの **[デプロイ設定]** ページには、SSISDB や Azure-SSIS IR パッケージ ストアを作成するオプションがあります。

#### <a name="creating-ssisdb"></a>SSISDB を作成する

**[Integration runtime setup]\(統合ランタイムのセットアップ\)** ペインの **[デプロイ設定]** ページで、パッケージを SSISDB (プロジェクト デプロイ モデル) にデプロイしたい場合は、 **[Create SSIS catalog (SSISDB) hosted by Azure SQL Database server/Managed Instance to store your projects/packages/environments/execution logs]\(Azure SQL Database サーバーおよびマネージド インスタンスによってホストされた SSIS カタログ (SSISDB) を作成してプロジェクト、パッケージ、環境、実行ログを格納する\)** チェック ボックスをオンにします。 Azure SQL Managed Instance にホストされた SQL Server データベース (MSDB)、Azure Files、ファイル システムのいずれかにパッケージをデプロイする場合 (パッケージ デプロイ モデル) は、SSISDB を作成する必要はなく、チェック ボックスをオンにする必要もありません。

デプロイ モデルに関係なく、Azure SQL Managed Instance にホストされた SQL Server エージェントを使用してパッケージの実行を調整 (スケジュール) したい場合は、SSISDB によって有効になっているため、このチェック ボックスをオンにしてください。 詳細については、[Azure SQL Managed Instance エージェントを使用して SSIS パッケージの実行をスケジュールする](./how-to-invoke-ssis-package-managed-instance-agent.md)方法に関するページを参照してください。
   
このチェック ボックスをオンにした場合は、自動的に作成および管理される SSISDB のホストとなる独自のデータベース サーバーを次の手順に従って用意してください。

   ![SSISDB のデプロイ設定](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings.png)
   
   1. **[サブスクリプション]** で、SSISDB をホストするデータベース サーバーを保有する Azure サブスクリプションを選択します。 

   1. **[場所]** で、SSISDB をホストするデータベース サーバーの場所を選択します。 統合ランタイムと同じ場所を選択することをお勧めします。

   1. **[Catalog Database Server Endpoint]\(カタログ データベース サーバー エンドポイント\)** で、SSISDB をホストするデータベース サーバーのエンドポイントを選択します。 
   
      SSISDB インスタンスは、選択したデータベース サーバーに基づいて、単一データベースやエラスティック プールの一部として、またはマネージド インスタンス上に自動的に作成できます。 パブリック ネットワークからアクセスできるほか、仮想ネットワークに参加させてアクセスすることもできます。 SSISDB をホストするデータベース サーバーの種類を選択する際のガイダンスについては、[SQL Database と SQL Managed Instance の比較](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance)に関するセクションを参照してください。   

      IP ファイアウォール規則/仮想ネットワーク サービス エンドポイントを備えた Azure SQL Database サーバーまたはプライベート エンドポイントを備えたマネージド インスタンスを選択して SSISDB をホストする場合、またはセルフホステッド IR を構成せずにオンプレミスのデータへのアクセスを必要としている場合は、Azure-SSIS IR を仮想ネットワークに参加させる必要があります。 詳細については、[仮想ネットワークへの Azure-SSIS IR の作成](./create-azure-ssis-integration-runtime.md)に関するページを参照してください。

   1. **[Use Azure AD authentication with the managed identity for your ADF]\(ADF のマネージド ID で Azure AD 認証を使用する\)** チェック ボックスをオンにして、SSISDB をホストするためのデータベース サーバーの認証方法を選択します。 SQL 認証を選択するか、またはデータ ファクトリのマネージド ID を使用した Azure AD 認証を選択することになります。

      このチェック ボックスをオンにした場合は、データベース サーバーへのアクセス許可が割り当てられている Azure AD グループにデータ ファクトリのマネージド ID を追加する必要があります。 詳細については、[Azure AD 認証を使用した Azure-SSIS IR の作成](./create-azure-ssis-integration-runtime.md)に関するページを参照してください。
   
   1. **[管理者ユーザー名]** に、SSISDB をホストするデータベース サーバーの SQL 認証ユーザー名を入力します。 

   1. **[管理者パスワード]** に、SSISDB をホストするデータベース サーバーの SQL 認証パスワードを入力します。 

   1. 事業継続とディザスター リカバリー (BCDR) のために Azure SQL Database または Managed Instance フェールオーバー グループと同期して機能するデュアル スタンバイ Azure SSIS IR ペアを構成するには、 **[Use dual standby Azure-SSIS Integration Runtime pair with SSISDB failover]\(SSISDB フェールオーバーと共にデュアル スタンバイ Azure-SSIS Integration Runtime ペアを使用する\)** チェック ボックスをオンにします。
   
      このチェック ボックスをオンにした場合は、 **[Dual standby pair name]\(デュアル スタンバイ ペア名\)** ボックスに、プライマリとセカンダリの Azure-SSIS IR ペアを識別する名前を入力します。 プライマリとセカンダリの Azure SSIS IR を作成するときは、同じペア名を入力する必要があります。

      詳細については、[BCDR のための Azure-SSIS IR の構成](./configure-bcdr-azure-ssis-integration-runtime.md)に関する記事をご覧ください。

   1. **[Catalog Database Service Tier]\(カタログ データベースのサービス レベル)** で、SSISDB をホストするデータベース サーバーのサービス レベルを選択します。 Basic、Standard、Premium のいずれかのレベルを選択するか、エラスティック プール名を選択してください。

**[テスト接続]** を選択し (該当する場合)、成功したら **[次へ]** を選択します。

#### <a name="creating-azure-ssis-ir-package-stores"></a>Azure-SSIS IR パッケージ ストアを作成する

**[Integration runtime setup]\(統合ランタイムのセットアップ\)** ペインの **[デプロイ設定]** ページで、MSDB、ファイル システム、または Azure Files (パッケージ デプロイ モデル) にデプロイされたパッケージを Azure-SSIS IR パッケージ ストアで管理したい場合は、 **[Create package stores to manage your packages that are deployed into file system/Azure Files/SQL Server database (MSDB) hosted by Azure SQL Managed Instance]\(パッケージ ストアを作成して、Azure SQL Managed Instance をホストとする SQL Server データベース (MSDB)、Azure Files、ファイル システムにデプロイされたパッケージを管理する\)** チェック ボックスをオンにします。
   
Azure-SSIS IR パッケージ ストアを使用すると、パッケージのインポート、エクスポート、削除、実行のほか、実行中のパッケージの監視と停止を、[従来の SSIS パッケージ ストア](/sql/integration-services/service/package-management-ssis-service)と同様、SSMS を介して行うことができます。 詳細については、[Azure-SSIS IR パッケージ ストアを使用した SSIS パッケージの管理](./azure-ssis-integration-runtime-package-store.md)に関するページを参照してください。
   
このチェック ボックスをオンにした場合、 **[新規]** を選択することで Azure-SSIS IR に複数のパッケージ ストアを追加できます。 逆に、1 つのパッケージ ストアを複数の Azure-SSIS IR で共有することもできます。

![MSDB、ファイル システム、Azure Files のデプロイ設定](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings2.png)

**[Add package store]\(パッケージ ストアの追加\)** ペインで、次の手順を実行します。
   
   1. **[Package store name]\(パッケージ ストア名\)** に、パッケージ ストアの名前を入力します。 

   1. **[Package store linked service]\(パッケージ ストアのリンクされたサービス\)** で、パッケージのデプロイ先のファイル システム、Azure Files、Azure SQL Managed Instance へのアクセス情報を格納する既存のリンクされたサービスを選択するか、 **[New]\(新規\)** を選択して新たに作成します。 **[New Linked Service]\(新しいリンクされたサービス\)** ペインで、次の手順を実行します。 

      > [!NOTE]
      > **Azure File Storage** か **File System** にリンクされているサービスを利用し、Azure Files にアクセスできます。 **Azure File Storage** にリンクされているサービスを使用する場合、Azure-SSIS IR パッケージ ストアでは現在のところ、(**アカウント キー** でも **SAS URI** でもなく) **基本** 認証方法のみがサポートされています。 **Azure File Storage** にリンクされているサービスで **基本** 認証を使用するには、お使いのブラウザーで ADF ポータル URL に `?feature.upgradeAzureFileStorage=false` を追加できます。 あるいは、**File System** にリンクされているサービスを代わりに使用し、Azure Files にアクセスできます。 

      ![リンクされたサービスのデプロイ設定](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings-linked-service.png)

      1. **[名前]** に、リンクされたサービスの名前を入力します。 
         
      1. **[説明]** に、リンクされたサービスの説明を入力します。 
         
      1. **[Type]\(種類\)** で、 **[Azure File Storage]** 、 **[Azure SQL Managed Instance]** 、 **[ファイル システム]** のいずれかを選択します。

      1. **[統合ランタイム経由で接続]** は無視してかまいません。パッケージ ストアのアクセス情報は、常に Microsoft がお客様の Azure-SSIS IR を使用してフェッチするからです。

      1. **[Azure File Storage]** を選択した場合は、次の手順を実行します。 

         1. **[Account selection method]\(アカウントの選択方法\)** で、 **[From Azure subscription]\(Azure サブスクリプションから\)** または **[Enter manually]\(手動で入力\)** を選択します。
         
         1. **[From Azure subscription]\(Azure サブスクリプションから\)** を選択した場合は、適切な **Azure サブスクリプション**、**ストレージ アカウント名**、**ファイル共有** を選択します。
            
         1. **[Enter manually]\(手動で入力\)** を選択した場合は、 **[ホスト]** に「`\\<storage account name>.file.core.windows.net\<file share name>`」を、 **[ユーザー名]** に「`Azure\<storage account name>`」を、 **[パスワード]** に「`<storage account key>`」を入力するか、それがシークレットとして保存されている **Azure Key Vault** を選択します。

      1. **[Azure SQL Managed Instance]** を選択した場合は、次の手順を実行します。 

         1. **[接続文字列]** を選択して手動で入力するか、それがシークレットとして保存されている **[Azure Key Vault]** を選択します。
         
         1. **[接続文字列]** を選択した場合は、次の手順を実行します。 

            1. Azure SQL Managed Instance のプライベート エンドポイントまたはパブリック エンドポイントとして、それぞれ「`<server name>.<dns prefix>.database.windows.net`」または「`<server name>.public.<dns prefix>.database.windows.net,3342`」を **[完全修飾ドメイン名]** に入力します。 プライベート エンドポイントを入力した場合、ADF UI からは到達できないため、 **[テスト接続]** は利用できません。

            1. **[データベース名]** に「`msdb`」と入力します。
               
            1. **[認証の種類]** で、 **[SQL 認証]** 、 **[マネージド ID]** 、 **[サービス プリンシパル]** のいずれかを選択します。

            1. **[SQL 認証]** を選択した場合は、適切な **ユーザー名** と **パスワード** を入力するか、それがシークレットとして保存されている **Azure Key Vault** を選択します。

            1. **[マネージド ID]** を選択した場合は、ご利用の ADF マネージド ID に Azure SQL Managed Instance へのアクセス権を付与します。

            1. **[サービス プリンシパル]** を選択した場合は、適切な **サービス プリンシパル ID** と **サービス プリンシパル キー** を入力するか、それがシークレットとして保存されている **Azure Key Vault** を選択します。

      1. **[ファイル システム]** を選択した場合は、パッケージのデプロイ先フォルダーの UNC パスを **[ホスト]** に入力すると共に、適切な **ユーザー名** と **パスワード** を入力するか、それがシークレットとして保存されている **Azure Key Vault** を選択します。

      1. **[テスト接続]** を選択し (該当する場合)、成功したら **[作成]** を選択します。

   1. 追加したパッケージ ストアが **[Deployment settings]\(デプロイ設定\)** ページに表示されます。 これらを削除する場合は、対応するチェック ボックスをオンにしてから **[削除]** を選択します。

**[テスト接続]** を選択し (該当する場合)、成功したら **[次へ]** を選択します。

### <a name="advanced-settings-page"></a>[詳細設定] ページ

**[統合ランタイムのセットアップ]** ペインの **[詳細設定]** ページで、次の手順を行います。 

   ![詳細設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. **[Maximum Parallel Executions Per Node]\(ノードあたりの最大並列実行数\)** で、統合ランタイム クラスター内のノードあたりの同時に実行するパッケージの最大数を選択します。 サポートされているパッケージ数のみが表示されます。 コンピューティングやメモリを大量に使用する 1 つの大規模なパッケージを実行するために複数のコアを使用する場合は、低い数値を選択します。 1 つのコアで 1 つ以上の小規模なパッケージを実行する場合は、高い数値を選択します。 

   1. Azure-SSIS IR 上で標準および高速カスタム セットアップを追加するかどうかを選択するには、 **[Customize your Azure-SSIS Integration Runtime with additional system configurations/component installations]\(追加のシステム構成およびコンポーネントのインストールで Azure-SSIS 統合ランタイムをカスタマイズする\)** チェック ボックスをオンにします。 詳細については、「[Azure-SSIS 統合ランタイムの設定のカスタマイズ](./how-to-configure-azure-ssis-ir-custom-setup.md)」を参照してください。
   
   1. Azure-SSIS IR を仮想ネットワークに参加させるかどうかを選択するには、 **[Select a VNet for your Azure-SSIS Integration Runtime to join, allow ADF to create certain network resources, and optionally bring your own static public IP addresses]\(参加させる Azure-SSIS 統合ランタイムの VNet を選択し、ADF で特定のネットワーク リソースを作成できるようにし、必要に応じて独自の静的パブリック IP アドレスを使用する\)** チェック ボックスをオンにします。

      これは、IP ファイアウォール規則および仮想ネットワーク サービス エンドポイントを利用する Azure SQL Database サーバーまたはプライベート エンドポイントを利用するマネージド インスタンスを使用して SSISDB をホストする場合、またはセルフホステッド IR を構成せずにオンプレミス データにアクセスする必要がある場合にオンにしてください。 詳細については、[仮想ネットワークへの Azure-SSIS IR の作成](./create-azure-ssis-integration-runtime.md)に関するページを参照してください。 
   
   1. セルフホステッド IR を Azure-SSIS IR のプロキシとして構成するかどうかを選択するには、 **[Set up Self-Hosted Integration Runtime as a proxy for your Azure-SSIS Integration Runtime]\(セルフホステッド統合ランタイムを Azure-SSIS 統合ランタイムのプロキシとして設定する\)** チェック ボックスをオンにします。 詳細については、[セルフホステッド IR のプロキシとしての設定](./self-hosted-integration-runtime-proxy-ssis.md)に関するページを参照してください。   

   1. **[続行]** をクリックします。 

**[Integration runtime setup]\(統合ランタイムのセットアップ\)** ペインの **[Summary]\(概要\)** ページで、すべてのプロビジョニング設定を確認し、推奨されるドキュメントのリンクをブックマークに登録します。その後 **[完了]** を選択すると、統合ランタイムの作成が開始されます。 

   > [!NOTE]
   > この処理は、カスタムのセットアップ時間があればそれを除くと、5 分以内で完了するはずです。
   >
   > SSISDB を使用する場合、Data Factory サービスは、SSISDB を準備するためにデータベース サーバーに接続します。 
   > 
   > Azure-SSIS IR をプロビジョニングすると、Access の再頒布可能パッケージと Azure Feature Pack for SSIS もインストールされます。 これらのコンポーネントは、組み込みのコンポーネントで既にサポートされているデータ ソースに加えて、Excel ファイルと Access ファイルのほか、さまざまな Azure データ ソースに対する接続を実現するものです。 組み込みコンポーネントとプレインストール コンポーネントの詳細については、[Azure-SSIS IR の組み込みコンポーネントとプレインストール コンポーネント](./built-in-preinstalled-components-ssis-integration-runtime.md)に関するページを参照してください。 その他インストールできるコンポーネントの詳細については、[Azure-SSIS IR のカスタム設定](./how-to-configure-azure-ssis-ir-custom-setup.md)に関するページを参照してください。

### <a name="connections-pane"></a>[接続] ペイン

**[管理]** ハブの **[接続]** ペインで、 **[Integration runtimes]\(統合ランタイム\)** ページに切り替え、 **[Refresh]\(更新\)** を選択します。 

   ![[接続] ペイン](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   Azure-SSIS IR は、その名前を選択することで編集したり再構成したりすることができます。 該当するボタンを選択することで、Azure-SSIS IR を監視、開始、停止、削除したり、Azure-SSIS IR 上で動作する SSIS パッケージの実行アクティビティを含む ADF パイプラインを自動生成したりできます。また、Azure-SSIS IR の JSON コード (またはペイロード) を表示することもできます。  Azure-SSIS IR の編集と削除は、Azure-SSIS IR が停止しているときにしか実行できません。

## <a name="deploy-ssis-packages"></a>SSIS パッケージのデプロイ

SSISDB を使用する場合は、そこにパッケージをデプロイし、Azure 対応の SSDT または SSMS ツールを使用して、それらを Azure-SSIS IR 上で実行することができます。 これらのツールは、ご利用のデータベース サーバーに対し、そのサーバーのエンドポイントを介して接続します。 

- Azure SQL Database サーバーの場合、サーバー エンドポイントの形式は `<server name>.database.windows.net` です。
- プライベート エンドポイントを備えたマネージド インスタンスの場合、サーバー エンドポイントの形式は `<server name>.<dns prefix>.database.windows.net` です。
- パブリック エンドポイントを備えたマネージド インスタンスの場合、サーバー エンドポイントの形式は `<server name>.public.<dns prefix>.database.windows.net,3342` です。 

SSISDB を使用しない場合は、Azure SQL Managed Instance にホストされた MSDB、ファイル システム、Azure Files のいずれかにパッケージをデプロイし、[dtutil](/sql/integration-services/dtutil-utility) および [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md) コマンド ライン ユーティリティを使用してそれらのパッケージを Azure-SSIS IR 上で実行できます。 

詳細については、[SSIS プロジェクトおよびパッケージのデプロイ](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages)に関する記事を参照してください。

どちらの場合も、Data Factory パイプラインの SSIS パッケージの実行アクティビティを使用して、デプロイしたパッケージを Azure-SSIS IR 上で実行することもできます。 詳細については、[ファーストクラスの Data Factory アクティビティとして SSIS パッケージの実行を呼び出す方法](./how-to-invoke-ssis-package-ssis-activity.md)に関するページを参照してください。

次の SSIS ドキュメントもご覧ください。 

- [Azure での SSIS パッケージのデプロイ、実行、監視](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Azure での SSISDB への接続](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Azure でのパッケージ実行のスケジュール設定](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Windows 認証を使用したオンプレミス データ ソースへの接続](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>次のステップ

Azure-SSIS Integration Runtime のカスタマイズの詳細については、次の記事に進んでください。 

> [!div class="nextstepaction"]
> [Azure-SSIS IR のカスタマイズ](./how-to-configure-azure-ssis-ir-custom-setup.md)