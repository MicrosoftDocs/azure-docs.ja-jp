---
title: Contoso のオンプレミス アプリを Azure にリビルドする | Microsoft Docs
description: Contoso が Azure App Services、Kubernetes サービス、CosmosDB、Azure Functions、および Cognitive サービスを使用して Azure にアプリをリビルドする方法について説明します。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 74c33d73f15c4edf63a02ea5c9a0cdcad88bb68c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59049747"
---
# <a name="contoso-migration-rebuild-an-on-premises-app-to-azure"></a>Contoso の移行: オンプレミス アプリを Azure に再構築する

この記事では、Contoso が SmartHotel360 アプリを Azure に移行し、リビルドする方法について説明します。 Contoso は、アプリのフロントエンド VM を、Azure App Services の Web アプリに移行します。 アプリのバックエンドは、Azure Kubernetes Service (AKS) によって管理されるコンテナーにデプロイされたマイクロサービスを使用してビルドされます。 サイトは、ペットの写真の機能を提供する Azure Functions と対話します。 

このドキュメントは、架空の会社 Contoso がオンプレミス リソースを Microsoft Azure クラウドに移行する方法を説明するシリーズ記事の 1 つです。 このシリーズには背景情報やシナリオが含まれ、移行インフラストラクチャのセットアップ、移行のためのオンプレミス リソースへのアクセス、およびさまざまな種類の移行の実行が説明されています。 シナリオは複雑になってきています。 今後、徐々に記事を追加する予定です。


**記事** | **詳細** | **状態**
--- | --- | ---
[記事 1:概要](contoso-migration-overview.md) | Contoso の移行戦略、記事シリーズ、および使用するサンプル アプリの概要を示します。 | 使用可能
[記事 2: Azure インフラストラクチャのデプロイ](contoso-migration-infrastructure.md) | Contoso が移行に備えて、オンプレミスと Azure のインフラストラクチャをどのように準備するかを説明します。 移行に関するすべてのアーティクルでは同じインフラストラクチャが使用されます。 | 使用可能
[記事 3:オンプレミスのリソースの評価](contoso-migration-assessment.md)  | VMware で実行されているオンプレミスの 2 階層 SmartHotel360 アプリの評価を Contoso が実行する方法を説明します。 Contoso は、アプリの VM は [Azure Migrate](migrate-overview.md) サービスを使用して、アプリの SQL Server データベースは [Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) を使用して評価します。 | 使用可能
[記事 4: Azure VM および SQL Managed Instance でのアプリのリホスト](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso が、SmartHotel360 アプリの Azure へのリフトアンドシフト移行を実行する方法を説明します。 Contoso は、[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) を使用してアプリのフロントエンド VM を移行し、[Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview) を使用してアプリのデータベースを SQL Managed Instance に移行します。 | 使用可能
[記事 5:Azure VM でのアプリのリホスト](contoso-migration-rehost-vm.md) | Contoso が Site Recovery のみを使用して SmartHotel360 アプリ VM を移行する方法を説明します。 | 使用可能
[記事 6:Azure VM および SQL Server Always On 可用性グループへのアプリのリホスト](contoso-migration-rehost-vm-sql-ag.md) | Contoso が SmartHotel360 アプリを移行する方法を示します。 Contoso は、Site Recovery を使用してアプリの VM を移行し、Database Migration Service を使用してアプリのデータベースを AlwaysOn 可用性グループで保護されている SQL Server クラスターに移行します。 | 使用可能
[記事 7:Linux アプリの Azure VM への再ホスト](contoso-migration-rehost-linux-vm.md) | Contoso が Site Recovery を使用して Azure VM への Linux osTicket アプリのリフトアンドシフト移行を実行する方法を説明します。 | 使用可能
[記事 8: Azure VM および Azure MySQL Server での Linux アプリのリホスト](contoso-migration-rehost-linux-vm-mysql.md) | Contoso が Site Recovery を使用して Linux osTicket アプリを Azure VM に移行する方法、および MySQL Workbench を使用してアプリのデータベースを Azure MySQL Server インスタンスに移行する方法を説明します。 | 使用可能
[記事 9: Azure Web Apps と Azure SQL Database でアプリをリファクタリングする](contoso-migration-refactor-web-app-sql.md) | Contoso が SmartHotel360 アプリを Azure Web アプリに移行して、アプリ データベースを Azure SQL Server インスタンスに移行する方法を示します | 使用可能
[記事 10:Azure Web Apps および Azure MySQL への Linux アプリのリファクター](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso が Linux osTicket アプリを複数のサイトの (継続的デリバリーのために GitHub と統合された) Azure Web Apps に移行する方法を示します。 Contoso は、アプリ データベースを Azure MySQL インスタンスに移行します。 | 使用可能
[記事 11: Azure DevOps Services で TFS をリファクタリングする](contoso-migration-tfs-vsts.md) | Contoso がオンプレミスの Team Foundation Server (TFS) デプロイを Azure の Azure DevOps Services に移行する方法を示します。 | 使用可能
[記事 12:Azure コンテナーおよび Azure SQL Database へのアプリの再構築](contoso-migration-rearchitect-container-sql.md) | Contoso が SmartHotel アプリを Azure に移行して再構築する方法を示します。 アプリの Web 層を Windows コンテナーとして再構築し、Azure SQL データベースでアプリ データベースを再構築します。 | 使用可能
記事 13: Azure へのアプリの再構築 | Contoso が Azure のさまざまな機能とサービス (App Services、Azure Kubernetes、Azure Functions、Cognitive Services、Cosmos DB など) を使用して SmartHotel アプリをリビルドする方法を示します。 | この記事の内容は次のとおりです。
[記事 14:Azure への移行のスケーリング](contoso-migration-scale.md) | 移行の組み合わせを試した後、Contoso は Azure への完全移行に向けてスケーリングを準備します。 | 使用可能

この記事では、Contoso は、VMware VM で実行されている 2 階層の Windows . VMware VM 上で実行されている NET SmartHotel360 アプリを Azure に移行します。 このアプリを使用したい場合は、オープン ソースとして提供されていますので、[GitHub](https://github.com/Microsoft/SmartHotel360-Backend) からダウンロードしてください。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="business-drivers"></a>ビジネス ドライバー

IT リーダーシップ チームは、ビジネス パートナーと密接に連絡を取り合い、彼らがこの移行で何を達成しようとしているのかを理解しました。

- **ビジネスの成長への対応**: Contoso は成長を続けており、Contoso の Web サイトで、差別化されたエクスペリエンスを顧客に提供したいと考えています。
- **機敏性**: Contoso は、グローバル経済で成功を収めるために、より迅速に市場の変化に対応できる必要があります。 
- **スケール**:ビジネスが順調に成長していく中で、Contoso IT チームは、同じペースで拡張可能なシステムを提供する必要があります。
- **コスト**: Contoso はライセンス コストを最小限に抑えたいと考えています。

## <a name="migration-goals"></a>移行の目標

Contoso クラウド チームは、この移行のためのアプリの要件を設定しました。 これらの要件を使用して、最良の移行方法を決定しました。
 - このアプリは、Azure でも現在と同じくクリティカルです。 優れたパフォーマンスを発揮し、容易にスケールできる必要があります。
 - このアプリでは、IaaS コンポーネントを使用しません。 PaaS またはサーバーレス サービスを使用するようにすべてをビルドする必要があります。
 - アプリのビルドはクラウド サービスで実行する必要があり、コンテナーはクラウド内のプライベートなエンタープライズ規模のコンテナー レジストリに存在する必要があります。
 - アプリで下された決定がホテルで受け入れられる必要があるため、ペットの写真に使用される API サービスは正確かつ実世界で信頼できるものである必要があります。 アクセス権を付与されたペットは、ホテルでの滞在を許可されます。
 - DevOps パイプラインの要件を満たすために、Contoso は Git リポジトリと共に Azure DevOps のソース コード管理 (SCM) を使用します。  コードのビルドと Azure Web Apps、Azure Functions、および AKS へのデプロイには、自動ビルドとリリースが使用されます。
 - バックエンドのマイクロサービスと、フロントエンドの Web サイトに対して別個の CI/CD パイプラインが必要です。
 - バックエンド サービスのリリース サイクルはフロントエンド Web アプリのリリース サイクルとは異なります。  この要件を満たすため、2 つの異なる DevOps パイプラインが展開されます。
 - Contoso はすべてのフロント エンド web サイトの配置の管理承認を必要とし、CI/CD パイプラインは、これを提供する必要があります。


## <a name="solution-design"></a>ソリューション設計

Contoso は目標と要件を決定した後、デプロイ ソリューションを設計およびレビューし、移行に使用する Azure サービスを含め、移行プロセスを決めます。

### <a name="current-app"></a>現在のアプリ

- SmartHotel360 オンプレミス アプリは 2 つの VM (WEBVM と SQLVM) に階層化されています。
- VM は、VMware ESXi ホスト **contosohost1.contoso.com** (バージョン 6.5) 上にあります。
- VMware 環境は、VM 上で実行中の vCenter Server 6.5 (**vcenter.contoso.com**) によって管理されています。
- Contoso にはオンプレミスのデータセンター (contoso-datacenter) があり、そこにオンプレミスのドメイン コントローラー (**contosodc1**) が含まれています。
- Contoso データセンター内のオンプレミス VM は、移行が行われた後に使用停止にされます。


### <a name="proposed-architecture"></a>提案されたアーキテクチャ

- アプリのフロントエンドは、プライマリ Azure リージョンに Azure App Services の Web アプリとしてデプロイされます。
- Azure 関数がペットの写真をアップロードし、サイトはこの機能と対話します。
- ペットの写真の関数は、Cognitive Services Vision API と Cosmos DB を利用します。
- サイトのバックエンドは、マイクロ サービスを使用してビルドされます。 これらは、Azure Kubernetes Service (AKS) で管理されるコンテナーにデプロイされます。
- コンテナーは Azure DevOps を使用してビルドされ、Azure Container Registry (ACR) にプッシュされます。
- Contoso は当面、Visual Studio を使用して Web アプリと関数コードを手動でデプロイする予定です。
- マイクロサービスは、Kubernetes コマンドライン ツールを呼び出す PowerShell script スクリプトを使用してデプロイされます。

    ![シナリオのアーキテクチャ](./media/contoso-migration-rebuild/architecture.png) 

  
### <a name="solution-review"></a>ソリューションのレビュー

Contoso は、長所と短所の一覧をまとめて、提案されたデザインを評価します。

**考慮事項** | **詳細**
--- | ---
**長所** | Contoso はエンドツーエンドのデプロイに PaaS とサーバーレス ソリューションを使用することで、管理に要する時間を大幅に削減できます。<br/><br/> マイクロサービス アーキテクチャへの移行により、Contoso は時間の経過とともにソリューションを容易に拡張できるようになります。<br/><br/> 新しい機能は、既存のソリューションのコード ベースを妨げずにオンライン状態にすることができます。<br/><br/> Web アプリは複数のインスタンスで構成されるため、単一障害点が発生しません。<br/><br/> アプリがさまざまなトラフィック量を処理できるように、自動スケールが有効化されます。<br/><br/> PaaS サービスへの移行に伴い、Contoso は Windows Server 2008 R2 オペレーティング システム上で実行されている古いソリューションを廃止できます。<br/><br/> CosmosDB にはフォールト トレランスが組み込まれており、Contoso による構成は必要ありません。 これは、データ層が単一のフェールオーバー ポイントでなくなることを意味します。
**短所** | コンテナーは他の移行オプションより複雑です。 Contoso にとって、学習曲線が問題になる可能性があります。  この曲線にもかかわらず、Contoso は多くの価値を提供する新しいレベルの複雑さを導入します。<br/><br/> Azure、コンテナー、アプリのマイクロサービスを理解し、サポートする Contoso の運用チームを立ち上げる必要があります。<br/><br/> Contoso は、ソリューション全体の DevOps を完全に実装していません。 Contoso は、AKS、関数、および App Services へのサービスのデプロイに関して、そのことを考慮する必要があります。



### <a name="migration-process"></a>移行プロセス

1. Contoso は、ACR、AKS、および CosmosDB をプロビジョニングします。
2. デプロイのインフラストラクチャ (Azure Web App、ストレージ アカウント、関数、および API) をプロビジョニングします。 
3. インフラストラクチャの準備が整ったら、Azure DevOps を使用してマイクロサービスのコンテナー イメージをビルドし、ACR にプッシュします。
4. Contoso は、PowerShell スクリプトを使用してこれらのマイクロサービスを ASK にデプロイします。
5. 最後に、Azure 関数と Web App をデプロイします。

    ![移行プロセス](./media/contoso-migration-rebuild/migration-process.png) 

### <a name="azure-services"></a>Azure サービス

**サービス** | **説明** | **コスト**
--- | --- | ---
[AKS](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Kubernetes の管理、デプロイ、操作を簡略化します。 完全に管理された Kubernetes コンテナー オーケストレーション サービスを提供します。  | AKS は無料サービスです。  仮想マシンと、関連するストレージとネットワーク リソースの使用した分に対してのみ料金が発生します。 [詳細情報](https://azure.microsoft.com/pricing/details/kubernetes-service/)。
[Azure Functions](https://azure.microsoft.com/services/functions/) | イベント ドリブン型のサーバーレス コンピューティング エクスペリエンスにより、開発を高速化できます。 オンデマンドでスケールできます。  | 使用したリソースに対してのみ料金が発生します。 プランでは、1 秒あたりのリソースの使用量と実行回数に基づいて課金されます。 [詳細情報](https://azure.microsoft.com/pricing/details/functions/)。
[Azure Container Registry](https://azure.microsoft.com/services/container-registry/) | あらゆる種類のコンテナー デプロイのイメージを保存します。 | コストは機能、ストレージ、使用期間に基づいて発生します。 [詳細情報](https://azure.microsoft.com/pricing/details/container-registry/)。
[Azure App Service](https://azure.microsoft.com/services/app-service/containers/) | あらゆるプラットフォームで稼働するエンタープライズグレードの Web アプリ、モバイル アプリ、API アプリをすばやくビルド、デプロイ、およびスケーリングできます。 | App Service プランは、1 秒単位で課金されます。 [詳細情報](https://azure.microsoft.com/pricing/details/app-service/windows/)。

## <a name="prerequisites"></a>前提条件

このシナリオで Contoso に必要なことを以下に示します。

**要件** | **詳細**
--- | ---
**Azure サブスクリプション** | Contoso は前の記事でサブスクリプションを作成しました。 Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)を作成してください。<br/><br/> 無料アカウントを作成する場合、サブスクリプションの管理者としてすべてのアクションを実行できます。<br/><br/> 既存のサブスクリプションを使用しており、管理者でない場合は、管理者に依頼して所有者アクセス許可または共同作成者アクセス許可を割り当ててもらう必要があります。
**Azure インフラストラクチャ** | [Contoso で Azure インフラストラクチャを設定する方法](contoso-migration-infrastructure.md)を確認してください。
**開発者の前提条件** | Contoso は、開発者用ワークステーションに次のツールをインストールする必要があります。<br/><br/> - [Visual Studio 2017 Community エディション: バージョン 15.5](https://www.visualstudio.com/)<br/><br/> .NET ワークロードが有効。<br/><br/> [Git](https://git-scm.com/)<br/><br/> [Azure PowerShell](https://azure.microsoft.com/downloads/)<br/><br/> [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)<br/><br/> Windows コンテナーを使用するように設定された [Docker CE (Windows 10) または Docker EE (Windows Server)](https://docs.docker.com/docker-for-windows/install/)。



## <a name="scenario-steps"></a>シナリオのステップ

Contoso が移行を実行する方法を次に示します。

> [!div class="checklist"]
> * **手順 1:AKS と ACR をプロビジョニングする**: Contoso は PowerShell を使用して、管理対象の AKS クラスターと Azure Container Registry をプロビジョニングします。
> * **手順 2:Docker コンテナーをビルドする**: Azure DevOps を使用して Docker コンテナーの CI を設定し、ACR にプッシュします。
> * **手順 3:バックエンドのマイクロサービスをデプロイする**: バックエンドのマイクロサービスによって利用される、残りのインフラストラクチャをデプロイします。
> * **手順 4:フロントエンドのインフラストラクチャをデプロイする**: フロントエンドのインフラストラクチャ (ペットの写真用の Blob Storage、Cosmos DB、Vision API を含む) をデプロイします。
> * **手順 5:バックエンドを移行する**: マイクロサービスをデプロイして AKS 上で実行し、バックエンドを移行します。
> * **手順 6:フロントエンドを公開する**: SmartHotel360 アプリを、Azure App Service と、ペット サービスによって呼び出される Function App に公開します。



## <a name="step-1-provision-back-end-resources"></a>手順 1:バックエンド リソースをプロビジョニングする

Contoso の管理者は、AKS と Azure Container Registry (ACR) を使用して、管理対象の Kubernetes クラスターを作成するデプロイ スクリプトを実行します。

- このセクションの手順では、**SmartHotel360-Azure-backend** リポジトリを使用します。
- **SmartHotel360-Azure-backend** GitHub リポジトリには、デプロイのこの部分のためのソフトウェアがすべて含まれています。

### <a name="prerequisites"></a>前提条件

1. Contoso の管理者は、開始する前に、デプロイに使用する開発用マシンにすべての前提条件ソフトウェアがインストールされていることを確認します。
2. 管理者は Git: **git clone https://github.com/Microsoft/SmartHotel360-Azure-backend.git**を使用して、開発用マシンにリポジトリをローカルに複製します。


### <a name="provision-aks-and-acr"></a>AKS および ACR をプロビジョニングする

Contoso の管理者は次のようにプロビジョニングします。

1.  Visual Studio Code を使用してフォルダーを開き、**/deploy/k8s** ディレクトリに移動します。ここに、スクリプト **gen-aks-env.ps1** が含まれています。
2. AKS と ACR を使用して、管理対象の Kubernetes クラスターを作成するスクリプトを実行します。

    ![AKS](./media/contoso-migration-rebuild/aks1.png)
 
3.  ファイルを開いた状態で $location パラメーターを **eastus2** に更新し、ファイルを保存します。

    ![AKS](./media/contoso-migration-rebuild/aks2.png)

4. **[表示]** > **[統合ターミナル]** をクリックして、Code の統合ターミナルを開きます。

    ![AKS](./media/contoso-migration-rebuild/aks3.png)

5. PowerShell 統合ターミナルで、Connect-AzAccount コマンドを使用して Azure にサインインします。 PowerShell の使用の[詳細については、こちら](https://docs.microsoft.com/powershell/azure/get-started-azureps)を参照してください。

    ![AKS](./media/contoso-migration-rebuild/aks4.png)

6. **az login** コマンドを実行し、Web ブラウザーを使用した認証の手順に従って、Azure CLI の認証を行います。 Azure CLI でのログインの[詳細については、こちら](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)を参照してください。

    ![AKS](./media/contoso-migration-rebuild/aks5.png)

7. 次のコマンドを、リソース グループ名 ContosoRG、AKS クラスター名 smarthotel-aks-eus2、および新しいレジストリ名を渡して実行します。

    ```
    .\gen-aks-env.ps1  -resourceGroupName ContosoRg -orchestratorName smarthotelakseus2 -registryName smarthotelacreus2
    ```
    ![AKS](./media/contoso-migration-rebuild/aks6.png)

8. Azure によって、AKS クラスターのリソースを含んだ別のリソース グループが作成されます。

    ![AKS](./media/contoso-migration-rebuild/aks7.png)

9. デプロイが完了したら、**kubectl** コマンドライン ツールをインストールします。 このツールは、Azure CloudShell では既にインストールされています。

    **az aks install-cli**

10. **kubectl get nodes** コマンドを実行して、クラスターへの接続を確認します。 ノードは、自動的に作成されたリソース グループ内の VM と同じ名前です。

    ![AKS](./media/contoso-migration-rebuild/aks8.png)

11. 次のコマンドを実行して、Kubernetes ダッシュボードを起動します。 

    **az aks browse --resource-group ContosoRG --name smarthotelakseus2**

12. ブラウザー タブが開き、ダッシュボードが表示されます。 これは Azure CLI を使用したトンネル接続です。 

    ![AKS](./media/contoso-migration-rebuild/aks9.png)




## <a name="step-2-configure-the-back-end-pipeline"></a>手順 2:バックエンド パイプラインを構成する

### <a name="create-an-azure-devops-project-and-build"></a>Azure DevOps プロジェクトとビルドを作成する

Contoso は Azure DevOps プロジェクトを作成し、CI ビルドを構成してコンテナーを作成し、それを ACR にプッシュします。 このセクションの手順では、[SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend) リポジトリを使用します。

1. visualstudio.com から新しい組織 (**contosodevops360.visualstudio.com**) を作成し、それを Git を使用するように構成します。

2. バージョン コントロールに Git、ワークフローにアジャイルを使用して、新しいプロジェクト (**SmartHotelBackend**) を作成します。

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts1.png) 


3. [GitHub リポジトリ](https://github.com/Microsoft/SmartHotel360-Backend)をインポートします。

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts2.png)
    
4. **[パイプライン]** で、**[ビルド]** をクリックし、リポジトリから、Azure Repos Git をソースとして使用して新しいパイプラインを作成します。 

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts3.png)

6. 空のジョブでの開始を選択します。

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts4.png)  

7. ビルド パイプラインで **[Hosted Linux Preview]\(ホストされている Linux プレビュー\)** を選択します。

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts5.png) 
 
8. **[Phase 1]\(フェーズ 1\)** で、**[Docker Compose]** タスクを追加します。 このタスクは、Docker Compose をビルドします。

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts6.png) 

9. 手順を繰り返し、**Docker Compose** タスクをもう 1 つ追加します。 このタスクは、コンテナーを ACR にプッシュします。

     ![Azure DevOps](./media/contoso-migration-rebuild/vsts7.png) 

8. 1 番目のタスク (ビルドの実行) を選択し、ビルドに Azure サブスクリプション、認可、および ACR を構成します。 

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts8.png)

9. リポジトリの **src** フォルダーにある **docket-compose.yaml** ファイルのパスを指定します。 サービス イメージのビルドを選択し、最新のタグを含めます。 アクションが **[Build service images]\(サービス イメージのビルド\)** に変わると、Azure DevOps タスクの名前が **[Build services automatically]\(サービスを自動的にビルド\)** に変わります。

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts9.png)

10. Contoso は次に、2 番目の Docker タスク (プッシュの実行) を構成します。 サブスクリプションと **[smarthotelacreus2]** ACR を選択します。 

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts10.png)

11. 前回と同様に、ファイルには docker-compose.yaml ファイルを入力し、**[Push service images]\(サービス イメージのプッシュ\)** を選択し、最新のタグを含めます。 アクションが **[Push service images]\(サービス イメージのプッシュ\)** に変わると、Azure DevOps タスクの名前が **[Push services automatically]\(サービスを自動的にプッシュ\)** に変わります。

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts11.png)

12. Azure DevOps タスクが構成されたので、Contoso はビルド パイプラインを保存し、ビルド プロセスを開始します。

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts12.png)

13. ビルド ジョブをクリックして、進行状況を確認します。

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts13.png)

14. ビルドが完了すると、ACR に、マイクロサービスによって使用されるコンテナーが設定された新しいリポジトリが表示されます。

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts14.png)


### <a name="deploy-the-back-end-infrastructure"></a>バックエンド インフラストラクチャをデプロイする

AKS クラスターを作成し、Docker イメージをビルドしたので、Contoso の管理者は次に、バックエンドのマイクロサービスによって利用される残りのインフラストラクチャをデプロイします。

- このセクションの手順では、[SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend) リポジトリを使用します。
- **/deploy/k8s/arm** フォルダーに、すべての項目を作成する 1 つのスクリプトが含まれています。 

デプロイは次のように行います。

1. 開発者コマンド プロンプトを開き、Azure サブスクリプションのコマンド az ログインを使用します。
2. deploy.cmd ファイルを使用し、次のコマンドを入力して ContosoRG リソース グループと EUS2 リージョンに Azure リソースをデプロイします。

    **.\deploy.cmd azuredeploy ContosoRG -c eastus2**

    ![バックエンドのデプロイ](./media/contoso-migration-rebuild/backend1.png)

2. Azure ポータルで、後で使用するために、各データベースの接続文字列をキャプチャします。

    ![バックエンドのデプロイ](./media/contoso-migration-rebuild/backend2.png)

### <a name="create-the-back-end-release-pipeline"></a>バックエンド リリース パイプラインを作成する

ここで、Contoso の管理者は以下のことを行います。

- サービスへの受信トラフィックを許可する NGINX イングレス コントローラーをデプロイします。
- AKS クラスターにマイクロサービスを送ります。
- 最初のステップとして、マイクロサービスへの接続文字列を Azure DevOps を使用して更新します。 次に、新しい Azure DevOps リリース パイプラインを構成して、マイクロサービスをデプロイします。
- このセクションの手順では、[SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend) リポジトリを使用します。
- 一部の構成設定 (Active Directory B2C など) については、この記事では説明しないためご注意ください。 これらの設定についての詳細情報は、リポジトリをご覧ください。

次のパイプラインを作成します。

1. Visual Studio を使用して、前に説明したデータベース接続情報で **/deploy/k8s/config_local.yml** ファイルを更新します。

    ![DB の接続](./media/contoso-migration-rebuild/back-pipe1.png)

2. Azure DevOps を開き、**Releases** の SmartHotel360 プロジェクトで **[+新しいパイプライン]** をクリックします。

    ![新しいパイプライン](./media/contoso-migration-rebuild/back-pipe2.png)

3. **[Empty Job]\(空ジョブ)** をクリックし、テンプレートを使用せずにパイプラインを開始します。
4. ステージとパイプラインの名前を付けます。

      ![ステージ名](./media/contoso-migration-rebuild/back-pipe4.png)

      ![パイプライン名](./media/contoso-migration-rebuild/back-pipe5.png)

5. 成果物を追加します。

     ![成果物を追加する](./media/contoso-migration-rebuild/back-pipe6.png)

6. ソース タイプとして **[Git]** を選択し、SmartHotel360 アプリのプロジェクト、ソース、およびマスター ブランチを指定します。

    ![成果物の設定](./media/contoso-migration-rebuild/back-pipe7.png)

7. タスクのリンクをクリックします。

    ![タスクのリンク](./media/contoso-migration-rebuild/back-pipe8.png)

8. PowerShell スクリプトを Azure 環境で実行できるように、新しい Azure PowerShell タスクを追加します。

    ![Azure での PowerShell](./media/contoso-migration-rebuild/back-pipe9.png)

9. タスクの Azure サブスクリプションを選択し、Git レポジトリから **deploy.ps1** スクリプトを選択します。

    ![スクリプトの実行](./media/contoso-migration-rebuild/back-pipe10.png)


10. スクリプトに引数を追加します。 スクリプトは、すべてのクラスター コンテンツ (**イングレス**および**イングレス コントローラー**を除く) を削除し、マイクロサービスをデプロイします。

    ![[スクリプトの引数]](./media/contoso-migration-rebuild/back-pipe11.png)

11. 指定の Azure PowerShell バージョンを最新に設定し、パイプラインを保存します。
12. **リリース** ページに戻り、新しいリリースを手動で作成します。

    ![新しいリリース](./media/contoso-migration-rebuild/back-pipe12.png)

13. リリースを作成した後にクリックし、**[アクション]** の **[デプロイ]** をクリックします。

      ![リリースのデプロイ](./media/contoso-migration-rebuild/back-pipe13.png)  

14. デプロイが完了したら、Azure Cloud Shell: **kubectl get services** を使用して、以下のコマンドを実行することでサービスの状態を確認します。


## <a name="step-3-provision-front-end-services"></a>手順 3:フロントエンド サービスをプロビジョニングする

Contoso の管理者は、フロントエンド アプリによって使用されるインフラストラクチャをデプロイする必要があります。 ペットの写真を格納するための BLOB ストレージ コンテナー、ペットの情報を含んだドキュメントを格納するための Cosmos データベース、および Web サイト用の Vision API を作成します。 

このセクションの手順では、[SmartHotel360-public-web](https://github.com/Microsoft/SmartHotel360-public-web) リポジトリを使用します。

### <a name="create-blob-storage-containers"></a>BLOB ストレージ コンテナーの作成

1.  Azure portal で、作成済みのストレージ アカウントを開き、**[BLOB]** をクリックします。
2.  パブリック アクセス レベル をコンテナーに設定した新しいコンテナー (**Pets**) を作成します。 ユーザーは、ペットの写真をこのコンテナーにアップロードします。

    ![Storage Blob](./media/contoso-migration-rebuild/blob1.png)

3. **settings** という名前の 2 つめのコンテナーを作成します。 このコンテナーに、フロントエンド アプリのすべての設定が含まれるファイルが配置されます。

    ![Storage Blob](./media/contoso-migration-rebuild/blob2.png)

4. 後で参照できるように、ストレージ アカウントのアクセスの詳細をテキスト ファイルにキャプチャします。

    ![Storage Blob](./media/contoso-migration-rebuild/blob2.png)

### <a name="provision-a-cosmos-database"></a>Cosmos データベースをプロビジョニングする

Contoso の管理者は、ペットの情報に使用する Cosmos データベースをプロビジョニングします。

1. Azure Marketplace で、**Azure Cosmos DB** を作成します。

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos1.png)

2. 名前 (**contosomarthotel**) を指定し、SQL API を選択し、米国東部 2 メイン リージョンの ContosoRG リソース グループに配置します。

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos2.png)

3. 既定の容量およびスループットを指定した新しいコレクションをデータベースに追加します。

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos3.png)


4. 後で参照できるように、このデータベースの接続情報をメモします。 

    ![Cosmos DB](./media/contoso-migration-rebuild/cosmos4.png)


### <a name="provision-computer-vision"></a>Computer Vision をプロビジョニングする

Contoso の管理者は、Computer Vision API をプロビジョニングします。 この API は、ユーザーによってアップロードされた写真を評価するために関数によって呼び出されます。

1. Azure Marketplace で、**Computer Vision** インスタンスを作成します。

     ![Computer Vision](./media/contoso-migration-rebuild/vision1.png)

2. ContosoRG 運用リソース グループ、米国東部 2 メイン リージョンで、API (**smarthotelpets**) をプロビジョニングします。

    ![Computer Vision](./media/contoso-migration-rebuild/vision2.png)

3. 後で参照できるように、この API の接続文字列をテキスト ファイルに保存します。

     ![Computer Vision](./media/contoso-migration-rebuild/vision3.png)


### <a name="provision-the-azure-web-app"></a>Azure Web アプリをプロビジョニングする

Contoso の管理者は、Azure portal を使用して Web アプリをプロビジョニングします。


1. ポータルで **[Web アプリ]** を選択します。

    ![Web アプリ](media/contoso-migration-rebuild/web-app1.png)

2. アプリ名 (**smarthotelcontoso**) を指定し、Windows 上で実行し、運用リソース グループ **ContosoRG** に配置します。 アプリのモニタリング用に新しい Application Insights リソースを作成します。

    ![Web アプリの名前](media/contoso-migration-rebuild/web-app2.png)

3. 完了したら、アプリのアドレスを参照して、アプリが正常に作成されたことを確認します。

4. ここで、Azure Portal でコードのステージング スロットを作成します。 パイプラインがこのスロットにデプロイされます。 これにより、管理者がリリースを実行するまで、コードは運用環境に移行されません。

    ![Web アプリのステージング スロット](media/contoso-migration-rebuild/web-app3.png)



### <a name="provision-the-azure-function-app"></a>Azure Function アプリのプロビジョニング

Azure portal で、Contoso の管理者は Function App をプロビジョニングします。

1. **[Function App]** を選択します。

    ![Function App の作成](./media/contoso-migration-rebuild/function-app1.png)

2. アプリ名 (**smarthotelpetchecker**) を指定します。 運用リソース グループ **ContosoRG** にアプリを配置します。ホスティングの場所を**従量課金プラン**に設定し、アプリを米国東部 2 リージョンに配置します。 監視用の Application Insights インスタンスと共に新しいストレージ アカウントが作成されます。

    ![Function App の設定](./media/contoso-migration-rebuild/function-app2.png)


3. アプリがデプロイされたら、アプリのアドレスを参照して、アプリが正常に作成されたことを確認します。


## <a name="step-4-set-up-the-front-end-pipeline"></a>手順 4:フロントエンド パイプラインを設定する

Contoso 管理者は、フロントエンド サイトに 2 つの異なるプロジェクトを作成します。 

1. Azure DevOps で、プロジェクト **SmartHotelFrontend** を作成します。

    ![フロントエンド プロジェクト](./media/contoso-migration-rebuild/function-app1.png)

2. [SmartHotel360 フロントエンド](https://github.com/Microsoft/SmartHotel360-public-web.git) Git リポジトリを新しいプロジェクトにインポートします。
3. Function App では、別の Azure DevOps プロジェクト (SmartHotelPetChecker) を作成し、[PetChecker](https://github.com/Microsoft/SmartHotel360-PetCheckerFunction ) Git リポジトリをこのプロジェクトにインポートします。

### <a name="configure-the-web-app"></a>Web アプリを構成する

次に、Contoso の管理者は Contoso リソースを使用するように Web アプリを構成します。

1. Azure DevOps プロジェクトに接続し、リポジトリを開発用マシンにローカルに複製します。
2. Visual Studio でフォルダーを開いて、リポジトリ内のすべてのファイルを表示します。

    ![リポジトリ ファイル](./media/contoso-migration-rebuild/configure-webapp1.png)

3. 必要に応じて構成の変更を更新します。

    - Web アプリは、起動時に **SettingsUrl** アプリ設定を探します。
    - この変数には、構成ファイルをポイントする URL が含まれている必要があります。
    - 既定では、使用される設定はパブリック エンドポイントです。

4.  /config-sample.json/sample.json ファイルを更新します。

    - これは、パブリック エンドポイントを使用する場合の Web 用の構成ファイルです。
    - **urls** セクションと **pets_config** セクションを、AKS API エンドポイント、ストレージ アカウント、Cosmos データベースの値を使用して編集します。
    - URL は、Contoso が作成する新しい Web アプリの DNS 名と一致している必要があります。
    - Contoso の場合、これは **smarthotelcontoso.eastus2.cloudapp.azure.com** です。

    ![Json 設定](./media/contoso-migration-rebuild/configure-webapp2.png)

5. ファイルが更新されたら、名前を **smarthotelsettingsurl** に変更し、前に作成した BLOB ストレージにアップロードします。

    ![名前の変更とアップロード](./media/contoso-migration-rebuild/configure-webapp3.png)

6. ファイルをクリックして、URL を取得します。 この URL は、アプリが構成ファイルプル ダウンするときに使用されます。

    ![アプリケーションの URL](./media/contoso-migration-rebuild/configure-webapp4.png)

7. **appsettings.Production.json** ファイルで、**SettingsUrl** を、新しいファイルの URL に更新します。

    ![更新 URL](./media/contoso-migration-rebuild/configure-webapp5.png)

### <a name="deploy-the-website-to-the-azure-app-service"></a>Azure App Service に Web サイトをデプロイする

これで Contoso の管理者は Web サイトを公開できます。


1. Azure DevOps を開き、**ビルドとリリース**の **SmartHotelFrontend** プロジェクトで、**[+新しいパイプライン]** をクリックします。
2. **[Azure DevOps Git]** をソースとして選択します。
3. **[ASP.NET Core]** テンプレートを選択します。
4. パイプラインを確認し、**[Web プロジェクトの発行]** と **[発行されたプロジェクトの zip 圧縮]** が選択されていることを確認します。

    ![パイプラインの設定](./media/contoso-migration-rebuild/vsts-publishfront2.png)

5. **[トリガー]** で継続的インテグレーションを有効にして、マスター ブランチを追加します。 これにより、マスター ブランチにコミットされた新しいコードをソリューションが持つたびに、ビルド パイプラインが起動します。

    ![継続的インテグレーション](./media/contoso-migration-rebuild/vsts-publishfront3.png)

6. **[Save & queue]\(保存とキュー\)** をクリックしてビルドを開始します。
7. ビルドが完了すると、**Azure App Service のデプロイ**を使用してリリース パイプラインを構成します。
8. ステージ名 **Staging** を指定します。

    ![環境名](./media/contoso-migration-rebuild/vsts-publishfront4.png)

9. 成果物を追加し、構成したビルドを選択します。

     ![成果物を追加する](./media/contoso-migration-rebuild/vsts-publishfront5.png)

10. 成果物の上にある稲妻アイコンをクリックして、継続的配置を有効にします。

    ![継続的なデプロイ](./media/contoso-migration-rebuild/vsts-publishfront6.png)
11. **[環境]** の **[ステージング]** の下の **[1 job, 1 task]\(1 ジョブ、1 タスク\)** をクリックします。
12. サブスクリプションおよびアプリ名を選択した後、**[Azure App Service のデプロイ]** タスクを開きます。 **ステージング** デプロイ スロットを使用するようにデプロイが構成されます。 これによって、このスロットでレビューと承認のコードが自動的にビルドされます。

     ![スロット](./media/contoso-migration-rebuild/vsts-publishfront7.png)

13. **[パイプライン]** で、新しいステージを追加します。

    ![新しい環境](./media/contoso-migration-rebuild/vsts-publishfront8.png)

14. **[スロットを使用した Azure App Service の配置]** を選択し、環境名を **Prod** とします。
15. **[1 job, 2 task]\(1 ジョブ、2 タスク\)** をクリックし、サブスクリプション、アプリ サービス名、および**ステージング** スロットを選択します。

    ![環境名](./media/contoso-migration-rebuild/vsts-publishfront10.png)

16. 次に、パイプラインから **[Deploy Azure App Service to Slot]\(Azure App Service をスロットにデプロイ\)** をクリックします。 これは直前のステップからここに配置されています。

    ![パイプラインから削除する](./media/contoso-migration-rebuild/vsts-publishfront11.png)

13. パイプラインを保存します。 パイプラインで、**[配置後の条件]** をクリックします。

    ![配置後](./media/contoso-migration-rebuild/vsts-publishfront12.png)

14. **[展開後承認]** を有効にし、承認者として開発リーダーを追加します。

    ![展開後承認](./media/contoso-migration-rebuild/vsts-publishfront13.png)

15. ビルド パイプラインで、ビルドを手動でキックオフします。 これによって、サイトをステージング スロットにデプロイする新しいリリース パイプラインがトリガーされます。 Contoso については、このスロットの URL は **https://smarthotelcontoso-staging.azurewebsites.net/** です。
16. ビルドが完了すると、リリースがスロットにデプロイされ、Azure DevOps から承認のために開発リーダーにメールが送られます。
17. 開発リーダーは **[View approval]\(承認を表示\)** をクリックし、Azure DevOps portal で要求を承認または拒否できます。

    ![承認メール](./media/contoso-migration-rebuild/vsts-publishfront14.png)

16. リーダーはコメントと承認を行います。 **ステージング**と **prod** スロットの交換が開始され、ビルドが運用環境に移行します。

    ![承認とスワップ](./media/contoso-migration-rebuild/vsts-publishfront15.png)

17. パイプラインがスワップを完了します。

    ![完全なスワップ](./media/contoso-migration-rebuild/vsts-publishfront16.png)

18. チームは **prod** スロットを確認し、**https://smarthotelcontoso.azurewebsites.net/** で Web アプリが運用環境にあることを確認します。


### <a name="deploy-the-petchecker-function-app"></a>PetChecker 関数アプリをデプロイする

Contoso の管理者はアプリを次のようにデプロイします。

1. Azure DevOps プロジェクトに接続することで、リポジトリを開発用マシンにローカルに複製します。
2. Visual Studio でフォルダーを開いて、リポジトリ内のすべてのファイルを表示します。
3. **src/PetCheckerFunction/local.settings.json** ファイルを開き、ストレージ、Cosmos データベース、および Computer Vision API 用のアプリ設定を追加します。

    ![関数のデプロイ](./media/contoso-migration-rebuild/function5.png)

4. コードをコミットし、Azure DevOps に同期して戻し、変更をプッシュします。
5. 新しいビルド パイプラインを追加し、ソースに **[Azure DevOps Git]** を選択します。
6. **[ASP.NET Core (.NET Framework)]** テンプレートを選択します。
7. テンプレートの既定値をそのまま使用します。
8. **[トリガー]** で、**[継続的インテグレーションを有効にする]** を選択し、**[保存してキューに登録]** をクリックしてビルドを開始します。
9. ビルドが完了すると、**[スロットを使用した Azure App Service の配置]** を追加してリリース パイプラインをビルドします。
10. 環境に **Prod** という名前を付けて、サブスクリプションを選択します。 **[アプリの種類]** を **[関数アプリ]** に設定し、アプリサービス名を **smarthotelpetchecker** とします。

    ![関数アプリ](./media/contoso-migration-rebuild/petchecker2.png)

11. 成果物 **ビルド** を追加します。

    ![アーティファクト](./media/contoso-migration-rebuild/petchecker3.png)

12. **[継続的配置トリガー]** を有効にし、**[Save]** タブをクリックします。
13. **[Queue new build]\(新しいビルドをキューに登録\)** をクリックし、フル CI/CD パイプラインを実行します。
14. 関数はデプロイされた後、Azure portal に **[実行中]** 状態で表示されます。

    ![関数のデプロイ](./media/contoso-migration-rebuild/function6.png)


7. Pet Checker アプリが期待どおりに動作していることを確認するために、アプリを参照します ([http://smarthotel360public.azurewebsites.net/Pets](http://smarthotel360public.azurewebsites.net/Pets))。
8. アバターをクリックして、写真をアップロードします。

    ![関数のデプロイ](./media/contoso-migration-rebuild/function7.png)

9. 最初に確認したい写真は、小さい犬の写真です。

    ![関数のデプロイ](./media/contoso-migration-rebuild/function8.png)

10. アプリが受領のメッセージを返します。

    ![関数のデプロイ](./media/contoso-migration-rebuild/function9.png)






## <a name="review-the-deployment"></a>デプロイを再調査する

リソースを Azure に移行したら、新しいインフラストラクチャを完全に操作可能にして、セキュリティ保護する必要があります。

### <a name="security"></a>セキュリティ

- Contoso は新しいデータベースが安全であることを確認する必要があります。 [詳細情報](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview)。
- アプリは、SSL と証明書を使用するように更新する必要があります。 コンテナー インスタンスは 443 で応答するように再デプロイする必要があります。
- Contoso は KeyVault を使用して、Service Fabric アプリのシークレットを保護することを検討する必要があります。 [詳細情報](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management)。

### <a name="backups-and-disaster-recovery"></a>バックアップとディザスター リカバリー

- Contoso は、Azure SQL Database のバックアップ要件を確認する必要があります。 [詳細情報](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups)。
- Contoso は、データベースのリージョン内フェールオーバーを提供するように SQL フェールオーバー グループを実装することを検討する必要があります。 [詳細情報](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview)。
- Contoso は ACR Premium SKU の geo レプリケーションを利用できます。 [詳細情報](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)。
- Cosmos DB は自動的にバックアップされます。 Contoso は、このプロセスの詳細情報を[こちらで確認](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore)できます。

### <a name="licensing-and-cost-optimization"></a>ライセンスとコストの最適化

- すべてのリソースをデプロイした後、Contoso は[インフラストラクチャ計画](contoso-migration-infrastructure.md#set-up-tagging)に基づいて Azure タグを割り当てる必要があります。
- すべてのライセンスは、Contoso が使用している PaaS サービスのコストに組み込まれています。 これは EA から差し引かれます。
- Contoso は、Microsoft の子会社である Cloudyn からライセンスが供与される Azure Cost Management を有効にします。 これは、Azure やその他のクラウド リソースの利用や管理に役立つ、マルチクラウド対応のコスト管理ソリューションです。  Azure Cost Management の詳細については、[こちら](https://docs.microsoft.com/azure/cost-management/overview)を参照してください。

## <a name="conclusion"></a>まとめ

この記事で、Contoso は SmartHotel360 アプリを Azure にリビルドしました。 オンプレミス アプリのフロントエンド VM が Azure App Services の Web アプリに再ビルドされました。 アプリのバックエンドは、Azure Kubernetes Service (AKS) によって管理されるコンテナーにデプロイされたマイクロサービスを使用してビルドされます。 Contoso はアプリの機能を、ペットの写真アプリで強化しました。




