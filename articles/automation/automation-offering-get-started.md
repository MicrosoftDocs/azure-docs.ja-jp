--- 
title: "Azure Automation の概要 | Microsoft Docs"
description: "この記事では、Auzre Marketplace のサービスをオンボードする準備として、設計と実装の詳細を確認しながら Azure Automation サービスの概要について説明します。"
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/31/2017
ms.author: magoedte
ms.translationtype: HT
ms.sourcegitcommit: a16daa1f320516a771f32cf30fca6f823076aa96
ms.openlocfilehash: 3269af400d091435af56b0093abbb839d8dd7068
ms.contentlocale: ja-jp
ms.lasthandoff: 09/02/2017

---

# <a name="getting-started-with-azure-automation"></a>Azure Automation の概要

この概要ガイドでは、Azure Automation のデプロイに関連する主要な概念を紹介します。 Azure 上の Automation を初めて使用する方や、System Center Orchestrator などの自動化ワークフロー ソフトウェアの使用経験がある方向けに、まず Automation の準備とオンボードの方法について説明します。  この後、プロセス自動化ニーズに対応するために Runbook の開発を始める準備が整います。 


## <a name="automation-architecture-overview"></a>Automation アーキテクチャの概要

![Azure Automation の概要](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Azure Automation は、Runbook を使用してプロセスを自動化し、Desired State Configuration (DSC) を使用して Azure、他のクラウド サービス、またはオンプレミスの Windows システムと Linux システムに対する構成の変更を管理する、拡張性と信頼性の高いマルチテナント環境を提供するサービスとしてのソフトウェア (SaaS) アプリケーションです。 Runbook、資産、実行アカウントなど、Automation アカウントに含まれるエンティティは、自分や自分以外のサブスクリプション内の他の Automation アカウントから分離されます。  

Azure で実行する Runbook は、Azure のプラットフォームとしてのサービス (PaaS) 仮想マシンでホストされる Automation サンドボックスで実行されます。  Automation サンドボックスの使用により、Runbook の実行のあらゆる側面 (モジュール、ストレージ、メモリ、ネットワーク通信、ジョブ ストリームなど) においてテナントの分離性が生み出されます。このロールは、サービスによって管理されており、Azure アカウントまたは Azure Automation アカウントからアクセスして自分で管理することはできません。         

ご利用のローカル データ センターまたは他のクラウド サービス上のリソースのデプロイと管理を自動化する場合は、Automation アカウントを作成した後で、[Hybrid Runbook Worker (HRW)](automation-hybrid-runbook-worker.md) ロールを実行する 1 つ以上のマシンを指定できます。  各 HRW には、Microsoft Management Agent のほかに、Automation アカウントと Log Analytics ワークスペースへの接続が必要です。  Log Analytics は、インストールのブートストラップ、Microsoft Management Agent の管理、および HRW の機能の監視に使用されます。  Runbook の配信と Runbook の実行指示は、Azure Automation によって行われます。

複数の HRW をデプロイすると、Runbook の高可用性を実現し、Runbook ジョブの負荷を分散できます。場合によっては、Runbook を特定のワークロードや環境専用にすることもできます。  HRW で Microsoft Monitoring Agent は、TCP ポート 443 経由で Automation サービスとの通信を開始します。入力方向のファイアウォール要件はありません。  環境内の他のコンピューターやサービスに対する管理タスクを実行する Runbook をその環境内の HRW で実行する場合、その Runbook では他のポートにアクセスすることが必要になる可能性があります。  IT セキュリティ ポリシーでネットワーク上のコンピューターがインターネットに接続することが許可されていない場合は、[OMS ゲートウェイ](../log-analytics/log-analytics-oms-gateway.md)に関する記事を参照してください。OMS ゲートウェイは HRW のプロキシとして機能して、ジョブの状態を収集し、Automation アカウントから構成情報を受け取ります。

HRW で実行される Runbook は、コンピューターのローカル システム アカウントのコンテキストで実行されます。これは、ローカルの Windows コンピューターで管理操作を実行する場合に推奨されるセキュリティ コンテキストです。 Runbook でローカル コンピューターの外部にあるリソースに対するタスクを実行する必要がある場合は、Runbook からアクセスでき、外部リソースへの認証に使用できる、セキュリティで保護された資格情報資産を Automation アカウントに定義することが必要なことがあります。 資格情報を指定できるコマンドレットで Runbook の[資格情報](automation-credentials.md)資産、[証明書](automation-certificates.md)資産、および[接続](automation-connections.md)資産を使用することで、さまざまなリソースへの認証が可能になります。

Azure Automation に格納されている DSC 構成 は、Azure 仮想マシンに直接適用できます。 それ以外の物理マシンと仮想マシンは、Azure Automation DSC プル サーバーに構成を要求できます。  オンプレミスの物理または仮想 Windows システムおよび Linux システムの構成を管理するために、Automation DSC プル サーバーをサポートするインフラストラクチャをデプロイする必要はありません。必要なのは Automation DSC によって管理される各システムからの発信インターネット アクセスのみで、OMS サービスとは TCP ポート 443 を介して通信します。   

## <a name="prerequisites"></a>前提条件

### <a name="automation-dsc"></a>Automation DSC
以下のさまざまなマシンを管理する際に、Azure Automation DSC を使用できます。

* Windows または Linux を実行する Azure 仮想マシン (クラシック)
* Windows または Linux を実行する Azure 仮想マシン
* Windows または Linux を実行する Amazon Web Services (AWS) 仮想マシン
* オンプレミス、または Azure と AWS 以外のクラウド内の物理/仮想 Windows コンピューター
* オンプレミス、または Azure と AWS 以外のクラウド内の物理/仮想 Linux コンピューター

Windows 用 PowerShell DSC エージェントを Azure Automation と通信できるようにするには、最新バージョンの WMF 5 をインストールする必要があります。 Linux と Azure Automation 間で通信できるようにするには、最新バージョンの [Linux 用 PowerShell DSC エージェント](https://www.microsoft.com/en-us/download/details.aspx?id=49150)をインストールする必要があります。

### <a name="hybrid-runbook-worker"></a>Hybrid Runbook Worker  
Hybrid Runbook のジョブを実行するコンピューターを指定する際に、そのコンピューターに必要なものは以下のとおりです。

* Windows Server 2012 またはそれ以降
* Windows PowerShell 4.0 またはそれ以降。  信頼性向上のため、コンピューターに Windows PowerShell 5.0 をインストールすることをお勧めします。 新しいバージョンは、[Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=50395) からダウンロードできます。
* 少なくとも 2 つのコア
* 少なくとも 4 GB の RAM

### <a name="permissions-required-to-create-automation-account"></a>Automation アカウントを作成するために必要なアクセス許可
Automation アカウントを作成したり更新したりするには、このトピックの作業で要求される以下に記載した特権とアクセス許可が必要となります。   
 
* Automation アカウントを作成するためには、ご利用の AD ユーザー アカウントが、「[Azure Automation におけるロールベースのアクセス制御](automation-role-based-access-control.md)」の記事に記載されている Microsoft.Automation リソースの所有者ロールに相当するアクセス許可を備えたロールに追加されている必要があります。  
* [アプリの登録] が **[はい]** に設定されている場合、Azure AD テナントの非管理者ユーザーが [AD アプリケーションを登録](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions)できます。  [アプリの登録] が **[いいえ]** に設定されている場合、この操作を行うユーザーは、Azure AD の全体管理者であることが必要です。 

サブスクリプションの Active Directory インスタンスのメンバーになっていない状態で、サブスクリプションの全体管理者/共同管理者ロールに追加された場合、Active Directory にゲストとして追加されることになります。 この場合、"…を作成するためのアクセス許可がありません" という 警告が **[Automation アカウントの追加]** ブレードに表示されます。 先に全体管理者/共同管理者ロールに追加されていたユーザーは、サブスクリプションの Active Directory インスタンスから削除した後、Active Directory の完全なユーザーとして再度追加できます。 このような状況を検証するには、Azure Portal の **[Azure Active Directory]** ウィンドウで、**[ユーザーとグループ]**、**[すべてのユーザー]**、特定のユーザー、**[プロファイル]** の順に選択します。 ユーザーのプロファイルの下部にある **[ユーザー タイプ]** 属性の値は、**[ゲスト]** と一致しないようにする必要があります。

## <a name="authentication-planning"></a>認証の計画
Azure Automation を使用すると、Azure 内のリソース、オンプレミスのリソース、他のクラウド プロバイダーのリソースに対するタスクを自動化できます。  Runbook が必要な操作を実行するためには、操作対象のリソースに安全に、サブスクリプション内で必要な最低限の権限だけでアクセスするための、アクセス許可が必要です。  

### <a name="what-is-an-automation-account"></a>Automation アカウントとは 
Azure Automation で Azure コマンドレットを使用してリソースに対して実行するすべての Automation タスクは、Azure Active Directory の組織 ID 資格情報に基づく認証を使用して、Azure に対する認証を行います。  Automation アカウントは、Azure リソースを構成および使用するためにポータルにサインインするときに使用するアカウントとは異なります。  アカウントに含まれている Automation リソースを次に示します。

* **証明書** - Runbook または DSC 構成で認証に使用される証明書が含まれます。または追加されます。
* **接続** - Runbook または DSC 構成から外部サービスまたはアプリケーションに接続するために必要な認証情報と構成情報が含まれます。
* **資格情報** - Runbook または DSC 構成から認証するために必要なユーザー名やパスワードなど、セキュリティ資格情報を含む PSCredential オブジェクトです。
* **統合モジュール** - Azure Automation アカウントに含まれる、コマンドレットを Runbook や DSC 構成内で利用するための PowerShell モジュールです。
* **スケジュール** - Runbook を指定時刻に開始または終了するためのスケジュールと繰り返し頻度が含まれます。
* **変数** - Runbook または DSC 構成で使用可能な値が含まれます。
* **DSC 構成** - オペレーティング システムの機能や設定の構成方法、Windows コンピューターまたは Linux コンピューターへのアプリケーションのインストール方法を記述した PowerShell スクリプトです。  
* **Runbook** - Windows PowerShell をベースに自動化された何らかの処理を Azure Automation で実行する一連のタスクです。    

各 Automation アカウントの Automation リソースは単一の Azure リージョンと関連付けられていますが、Automation アカウントではサブスクリプションのすべてのリソースを管理できます。 ポリシーにより、データやリソースを特定のリージョンに分離する必要がある場合は、異なるリージョンで Automation アカウントを作成します。

> [!NOTE]
> Azure ポータルで作成した Automation アカウントおよび各アカウントに含まれるリソースには、Azure クラシック ポータルでアクセスすることはできません。 これらのアカウントとリソースを Windows PowerShell で管理するには、[Azure リソース マネージャー] モジュールを使用する必要があります。
> 

Azure Portal で Automation アカウントを作成すると、次の 2 つの認証エンティティが自動的に作成されます。

* 実行アカウント。 Azure Active Directory (Azure AD) のサービス プリンシパルと証明書は、このアカウントで作成します。 また、Runbook を使って Resource Manager リソースを管理する共同作成者の RBAC (ロールベースのアクセス制御) も、このアカウントで割り当てます。
* クラシック実行アカウント。 クラシック リソースを Runbook で管理する際に必要な管理証明書は、このアカウントでアップロードします。

ロール ベースのアクセス制御は、Azure AD ユーザー アカウントおよび実行アカウントに対して許可されたアクションを付与し、そのサービス プリンシパルを認証するために、Azure Resource Manager で使用できます。  Automation アクセス許可を管理するためのモデルの開発に役立つ詳細については、「[Azure Automation におけるロールベースのアクセス制御](automation-role-based-access-control.md)」を参照してください。  

#### <a name="authentication-methods"></a>認証方法
次の表は、Azure Automation によってサポートされる各環境のさまざまな認証方法をまとめたものです。

| メソッド | 環境 
| --- | --- | 
| Azure 実行アカウントとクラシック実行アカウント |Azure Resource Manager と Azure クラシック デプロイメント |  
| Azure AD ユーザー アカウント |Azure Resource Manager と Azure クラシック デプロイメント |  
| Windows 認証 |Hybrid Runbook Worker を使用するローカル データ センターまたは他のクラウド プロバイダー |  
| AWS 資格情報 |Amazon Web Services |  

**「方法」の「認証とセキュリティ」**セクションにあるサポート記事では、そうした環境専用の既存または新規のアカウントを使用して、その環境の認証を構成するための概要と実装手順を説明しています。  Azure 実行アカウントとクラシック実行アカウントについては、「[Automation 実行アカウントを更新する](automation-create-runas-account.md)」を参照してください。既存の Automation アカウントが最初に実行アカウントまたはクラシック実行アカウントと共に構成されていない場合に、ポータルまたは PowerShell を使用して、既存の Automation アカウントと実行アカウントを更新する方法について説明します。 エンタープライズ証明機関 (CA) から発行された証明書で実行アカウントとクラシック実行アカウントを作成しようとする場合は、この記事を読み、この構成を使用してアカウントを作成する方法を確認してください。     
 
## <a name="network-planning"></a>ネットワークの計画
Hybrid Runbook Worker を Microsoft Operations Management Suite (OMS) に接続して登録するには、その Hybrid Runbook Worker は、以下で説明するポート番号と URL にアクセスできる必要があります。  OMS に接続するには、[Microsoft Monitoring Agent に必要なポートと URL](../log-analytics/log-analytics-windows-agents.md#network) のほかに、このポートと URL が必要です。 エージェントと OMS サービス間の通信にプロキシ サーバーを使用する場合、適切なリソースにアクセスできることを確認する必要があります。 ファイアウォールを使用してインターネットへのアクセスを制限している場合は、アクセスを許可するようにファイアウォールを構成する必要があります。

Automation と通信するには、次の一覧に示すポートと URL が Hybrid Runbook Worker に必要です。

* ポート: 発信インターネット アクセスには TCP 443 のみが必要です
* グローバル URL: *.azure-automation.net

Automation アカウントが特定のリージョンに対して定義されており、そのリージョンのデータ センターとの通信を制限する必要がある場合は、次の表に示す各リージョンの DNS レコードを使用します。

| **[リージョン]** | **DNS レコード** |
| --- | --- |
| 米国中南部 |scus-jobruntimedata-prod-su1.azure-automation.net |
| 米国東部 2 |eus2-jobruntimedata-prod-su1.azure-automation.net |
| 米国中西部 | wcus-jobruntimedata-prod-su1.azure-automation.net |
| 西ヨーロッパ |we-jobruntimedata-prod-su1.azure-automation.net |
| 北ヨーロッパ |ne-jobruntimedata-prod-su1.azure-automation.net |
| カナダ中部 |cc-jobruntimedata-prod-su1.azure-automation.net |
| 東南アジア |sea-jobruntimedata-prod-su1.azure-automation.net |
| インド中部 |cid-jobruntimedata-prod-su1.azure-automation.net |
| 東日本 |jpe-jobruntimedata-prod-su1.azure-automation.net |
| オーストラリア東南部 |ase-jobruntimedata-prod-su1.azure-automation.net |
| 英国南部 | uks-jobruntimedata-prod-su1.azure-automation.net |
| 米国政府バージニア州 | usge-jobruntimedata-prod-su1.azure-automation.us |

名前ではなく、IP アドレスの一覧を確認するには、Microsoft ダウンロード センターから [Azure データ センターの IP アドレス](https://www.microsoft.com/download/details.aspx?id=41653) xml ファイルをダウンロードして確認してください。 

> [!NOTE]
> このファイルには、Microsoft Azure データ センターで使用される IP アドレス範囲 (Compute、SQL、および Storage の範囲を含む) が含まれています。 毎週投稿される最新のファイルには、現在デプロイされている範囲と今後変更される IP 範囲が反映されています。 ファイルに含まれている新しい範囲は、少なくとも 1 週間はデータ センターで使用されません。 Azure で実行されているサービスを正しく識別するために、毎週新しい xml ファイルをダウンロードし、サイトで必要な変更を実行してください。 ExpressRoute ユーザーは、このファイルを使用して、毎月第 1 週に Azure 領域の BGP アドバタイズが更新されていることに注目してください。 
> 

## <a name="creating-an-automation-account"></a>Automation アカウントの作成

Azure Portal で Automation アカウントを作成する方法は複数あります。  次の表では、各種のデプロイ方法とそれらの違いについて説明しています。  

|メソッド | Description |
|-------|-------------|
| Marketplace から [Automation & Control] を選択する | 相互に関連付けられた Automation アカウントと OMS ワークスペースの両方を同じリソース グループとリージョンに作成するサービス。  OMS との統合には、Log Analytics を使用して Runbook のジョブ ステータスとジョブ ストリームを長期にわたって監視および分析したり、問題をエスカレートまたは調査する高度な機能を利用したりするメリットも含まれます。 また、Change Tracking & Update Management ソリューションのデプロイも行います。これらのソリューションは、既定で有効になります。 |
| Marketplace から [Automation] を選択する | OMS ワークスペースに関連付けられていない Automation アカウントを新規または既存のリソース グループ内に作成します。Automation & Control サービスで利用可能なソリューションは含まれません。 これは、Automation の概要がわかる基本的な構成です。Runbook、DSC 構成を作成し、サービスの機能を使用する方法を学習するのに役立ちます。 |
| 選択した管理ソリューション | ソリューション (**[更新管理](../operations-management-suite/oms-solution-update-management.md)**、**[作業時間外に VM を開始/停止する](automation-solution-vm-management.md)**、または**[変更の追跡](../log-analytics/log-analytics-change-tracking.md)**) を選択した場合、ソリューションをサブスクリプションにデプロイするために、既存の Automation と OMS ワークスペースを選択するよう求められるか、必要に応じて両方を作成するオプションが提示されます。 |

このトピックでは、Automation & Control サービスをオンボードして、Automation アカウントと OMS ワークスペースを作成する方法について説明します。  テスト用のスタンドアロン Automation アカウントを作成するか、サービスをプレビューする場合は、「[Create standalone Automation account (スタンドアロン Automation アカウントを作成する)](automation-create-standalone-account.md)」を参照してください。  

### <a name="create-automation-account-integrated-with-oms"></a>OMS と統合された Automation アカウントを作成する
Automation の推奨されるオンボード方法は、Marketplace から [Automation & Control] サービスを選択することです。  このサービスでは、Automation アカウントが作成され、OMS ワークスペースとの統合が確立されます。このサービスで使用できる管理ソリューションをインストールすることもできます。  

1. サブスクリプション管理ロールのメンバーかつサブスクリプションの共同管理者であるアカウントを使用して、Azure Portal にサインインします。

2. **[新規]**をクリックします。<br><br> ![Azure Portal での [新規] オプションの選択](media/automation-offering-get-started/automation-portal-martketplacestart.png)<br>  

3. **Automation** を検索し、検索結果から **[Automation & Control]** を選択します。<br><br> ![Marketplace からの Automation & Control の検索と選択](media/automation-offering-get-started/automation-portal-martketplace-select-automationandcontrol.png)<br>   

4. サービスの説明を読んだら、**[作成]** をクリックします。  

5. **[Automation & Control]** 設定ブレードで、**[OMS ワークスペース]** を選択します。  **[OMS ワークスペース]** ブレードで、Automation アカウントと同じ Azure サブスクリプションに関連付けられている OMS ワークスペースを選択するか、OMS ワークスペースを作成します。  OMS ワークスペースがない場合は、**[新しいワークスペースの作成]** を選択し、**[OMS ワークスペース]** ブレードで次の手順を実行します。 
   - 新しい **OMS ワークスペース**の名前を指定します。
   - 関連付ける**サブスクリプション**をドロップダウン リストから選択します (既定値が適切でない場合)。
   - **[リソース グループ]** では、リソース グループを作成するか、既存のリソース グループを選択できます。  
   - **[場所]**を選択します。  詳細については、[Azure Automation を使用できるリージョン](https://azure.microsoft.com/regions/services/)に関するページを参照してください。  ソリューションは、Free とノードごと (OMS) の 2 つのレベルで提供されています。  Free レベルは、1 日に収集できるデータの量、リテンション期間、Runbook ジョブの実行時間 (分) に上限が設けられています。  ノードごと (OMS) レベルでは 1 日に収集できるデータの量に上限がありません。  
   - **[Automation アカウント]** を選択します。  OMS ワークスペースを新たに作成する場合はさらに、先ほど指定した新しい OMS ワークスペース (Azure サブスクリプション、リソース グループ、リージョンを含む) に関連付ける Automation アカウントを作成する必要があります。  **[Automation アカウントの作成]** を選択し、**[Automation アカウント]** ブレードで次の情報を入力します。 
  - **[名前]** フィールドに、Automation アカウントの名前を入力します。

    それ以外のオプションはすべて、選択した OMS ワークスペースに基づいて自動的に入力されます。これらのオプションを変更することはできません。  このサービスでは、Azure 実行アカウントが既定の認証方法になります。  **[OK]** をクリックすると、設定したオプションが検証され、Automation アカウントが作成されます。  進行状況は、メニューの **[通知]** で追跡できます。 

    または、既存の Automation 実行アカウントを選択します。  既に別の OMS ワークスペースに関連付けられているアカウントは選択できません。選択すると、通知メッセージがブレードに表示されます。  既に関連付けられている場合は、別の Automation 実行アカウントを選択するか、作成する必要があります。

    必要な情報をすべて入力したら、**[作成]** をクリックします。  情報が検証され、Automation アカウントと実行アカウントが作成されます。  自動的に **[OMS ワークスペース]** ブレードに戻ります。  

6. **[OMS ワークスペース]** ブレードに必要な情報を入力したら、**[作成]** をクリックします。  情報が検証され、ワークスペースが作成されている間、メニューの **[通知]** でその進行状況を追跡することができます。  その後、自動的に **[ソリューションの追加]** ブレードに戻ります。  

7. **[Automation & Control]** 設定ブレードで、事前に選択された推奨ソリューションをインストールすることを確認します。 選択を解除したソリューションは、後で個別にインストールできます。  

8. **[作成]** をクリックして、Automation と OMS ワークスペースのオンボードを続行します。 すべての設定が検証された後、ご利用のサブスクリプションへのサービスのデプロイが試行されます。  このプロセスは、完了までに数秒かかる場合があります。進行状況は、メニューの **[通知]** で確認してください。 

サービスがオンボードされた後は、Runbook の作成、有効にした管理ソリューションの利用の開始、[Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) ロールのデプロイ、または [Log Analytics](https://docs.microsoft.com/azure/log-analytics) を使用して、クラウドまたはオンプレミス環境のリソースによって生成されたデータの収集を開始することができます。   

## <a name="next-steps"></a>次のステップ
* 新しい Automation アカウントが Azure リソースに対して認証できることを確認するには、「[Azure Automation 実行アカウントの認証をテストする](automation-verify-runas-authentication.md)」を参照してください。
* Runbook を作成するには、開始する前に、サポートされている [Automation の Runbook の種類](automation-runbook-types.md)と関連する考慮事項を確認してください。



