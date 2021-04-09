---
title: 'Azure Migrate: Server Assessment を使用して VMware 環境で実行されているサーバーを検出する'
description: Azure Migrate Server Assessment ツールを使用して、オンプレミスの VMware VM を検出する方法について説明します。
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 9/14/2020
ms.custom: mvc
ms.openlocfilehash: 4d2b0fbb377beacdb75a1a5552855936bee2b205
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041313"
---
# <a name="tutorial-discover-servers-running-in-vmware-environment-with-azure-migrate-server-assessment"></a>チュートリアル: Azure Migrate: Server Assessment を使用して VMware 環境で実行されているサーバーを検出する

Azure への移行の一環として、オンプレミスのインベントリとワークロードを検出します。

このチュートリアルでは、軽量の Azure Migrate アプライアンスを使用して、Azure Migrate: Server Assessment ツールによって VMware 環境で実行されているサーバーを検出する方法について説明します。 このアプライアンスを vCenter Server で実行されるサーバーとしてデプロイして、サーバーとそのパフォーマンス メタデータ、サーバー上で実行されているアプリケーション、サーバーの依存関係、SQL Server インスタンスおよびデータベースを継続的に検出します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure アカウントを設定します。
> * 検出のために VMware 環境を準備します。
> * Azure Migrate プロジェクトを作成します。
> * Azure Migrate アプライアンスを設定します。
> * 継続的な検出を開始します。

> [!NOTE]
> 各チュートリアルでは、シナリオを試すための最も簡単な方法を紹介し、可能であれば既定のオプションを使用します。  

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。


## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、次の前提条件を満たしていることを確認してください。


**要件** | **詳細**
--- | ---
**vCenter Server/ESXi ホスト** | バージョン 5.5、6.0、6.5、または 6.7 を実行している vCenter Server が必要です。<br/><br/> サーバーは、バージョン 5.5 以降が実行されている ESXi ホストでホストされている必要があります。<br/><br/> vCenter Server で、TCP ポート 443 での受信接続を許可して、アプライアンスが構成とパフォーマンスのメタデータを収集できるようにします。<br/><br/> 既定では、アプライアンスはポート 443 で vCenter Server に接続します。 vCenter Server が別のポートでリッスンする場合は、アプライアンス構成マネージャーで vCenter Server の詳細を指定するときにポートを変更できます。<br/><br/> ESXi ホストでは、サーバーでインストールされているアプリケーションの検出とエージェントレスの依存関係の分析を実行するために、TCP ポート 443 で受信アクセスが許可されていることを確認します。
**アプライアンス** | vCenter Server には、Azure Migrate アプライアンスのサーバーを割り当てるためのリソースが必要です。<br/><br/> - 32 GB の RAM、8 個の vCPU、約 80 GB のディスク記憶域。<br/><br/> - 外部仮想スイッチと、アプライアンス サーバー上のインターネット アクセス (直接またはプロキシ経由)。
**サーバー** | 構成とパフォーマンス メタデータの検出では、Windows および Linux OS のすべてのバージョンがサポートされています。 <br/><br/> サーバー上でアプリケーションの検出を実行するために、Windows および Linux OS のすべてのバージョンがサポートされています。 エージェントレスの依存関係分析でサポートされている OS バージョンについては、[こちら](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless)を確認してください。<br/><br/> インストールされているアプリケーションの検出とエージェントレスの依存関係の分析を実行するには、VMware Tools (10.2.0 以降) がサーバーにインストールされ、実行されている必要があります。 Windows サーバーには、PowerShell バージョン 2.0 以降がインストールされている必要があります。<br/><br/> SQL Server インスタンスおよびデータベースを検出するには、[こちら](migrate-support-matrix-vmware.md#requirements-for-discovery-of-sql-server-instances-and-databases)で、サポート対象の SQL Server のバージョンとエディション、サポート対象の Windows OS バージョン、および認証メカニズムを確認してください。

> [!Note]
> VMware 環境で実行されている SQL Server インスタンスおよびデータベースの検出と評価は、現在プレビュー段階にあります。 この機能を試すには、[**このリンク**](https://aka.ms/AzureMigrate/SQL)を使用して、**オーストラリア東部** リージョンにプロジェクトを作成します。 オーストラリア東部に既にプロジェクトがあり、この機能を試したい場合は、ポータルでこれらの [**前提条件**](how-to-discover-sql-existing-project.md)が完了していることを確認してください。

## <a name="prepare-an-azure-user-account"></a>Azure ユーザー アカウントを準備する

Azure Migrate プロジェクトを作成し、Azure Migrate アプライアンスを登録するには、以下を備えたアカウントが必要です。
- Azure サブスクリプションに対する共同作成者または所有者のアクセス許可
- Azure Active Directory (AAD) アプリを登録するためのアクセス許可
- エージェントレスのサーバー移行時に使用されるキー コンテナーを作成するための、Azure サブスクリプションに対する所有者または共同作成者とユーザー アクセス管理者のアクセス許可

無料の Azure アカウントを作成したばかりであれば、自分のサブスクリプションの所有者になっています。 サブスクリプションの所有者でない場合は、所有者と協力して、次のようにアクセス許可を割り当てます。

1. Azure portal で "サブスクリプション" を検索し、 **[サービス]** で **[サブスクリプション]** を選択します。

    :::image type="content" source="./media/tutorial-discover-vmware/search-subscription.png" alt-text="Azure サブスクリプションを検索するための検索ボックス":::


2. **[サブスクリプション]** ページで、Azure Migrate プロジェクトを作成するサブスクリプションを選択します。
3. サブスクリプションで、 **[アクセス制御 (IAM)]**  >  **[アクセスの確認]** の順に選択します。
4. **[アクセスの確認]** で、適切なユーザー アカウントを検索します。
5. **[ロールの割り当てを追加する]** で、 **[追加]** をクリックします。
:::image type="content" source="./media/tutorial-discover-vmware/azure-account-access.png" alt-text="ユーザー アカウントを検索してアクセスを確認し、ロールを割り当てる":::
    
6. **[ロールの割り当ての追加]** で、共同作成者または所有者ロールを選択し、アカウント (この例では azmigrateuser) を選択します。 **[保存]** をクリックします。

    :::image type="content" source="./media/tutorial-discover-vmware/assign-role.png" alt-text="[ロールの割り当ての追加] ページを開いて、アカウントにロールを割り当てる":::

1. アプライアンスを登録するには、お使いの Azure アカウントに **AAD アプリを登録するためのアクセス許可** が必要です。
1. Azure portal で、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[ユーザー設定]** に移動します。
1. **[ユーザー設定]** で、Azure AD ユーザーがアプリケーションを登録できることを確認します (既定で **[はい]** に設定されています)。

    :::image type="content" source="./media/tutorial-discover-vmware/register-apps.png" alt-text="[ユーザー設定] で、ユーザーが Active Directory アプリを登録できることを確認する":::

9. [アプリの登録] 設定が [いいえ] に設定されている場合は、テナントまたはグローバル管理者に、必要なアクセス許可を割り当てるよう依頼してください。 テナントまたはグローバル管理者は、AAD アプリの登録を許可するために、**アプリケーション開発者** ロールをアカウントに割り当てることもできます。 [詳細については、こちらを参照してください](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)。

## <a name="prepare-vmware"></a>VMware を準備する

vCenter Server で、OVA ファイルを使用して VM を作成するためのアクセス許可がお使いのアカウントにあることを確認します。 これは、OVA ファイルを使用して Azure Migrate アプライアンスを VMware VM としてデプロイする場合に必要です。

Azure Migrate では、実際の VMware 環境で実行されているサーバーの検出および評価のために vCenter Server 読み取り専用アカウントが必要です。 さらに、インストールされているアプリケーションの検出とエージェントレスの依存関係の分析も実行する場合は、 **[Virtual Machines]\(仮想マシン\) > [Guest Operations]\(ゲスト操作\)** でそのアカウントの特権が有効になっている必要があります。

### <a name="create-an-account-to-access-vcenter"></a>vCenter にアクセスするためのアカウントを作成する

vSphere Web Client で、次のようにアカウントを設定します。

1. 管理者特権を備えたアカウントを使用して、vSphere Web Client で **[Administration]\(管理\)** を選択します。
2. **[Access]\(アクセス\)** 、 **[SSO Users and Groups]\(SSO ユーザーとグループ\)** の順に選択します。
3. **[Users]\(ユーザー\)** で、新しいユーザーを追加します。
4. **[New User]\(新しいユーザー\)** で、アカウントの詳細を入力します。 次に、 **[OK]** をクリックします
5. **[Global Permissions]\(グローバル アクセス許可\)** でユーザー アカウントを選択し、アカウントに **[Read-only]\(読み取り専用\)** ロールを割り当てます。 次に、 **[OK]** をクリックします
6.  インストールされているアプリケーションの検出とエージェントレスの依存関係の分析も実行する場合は、 **[Roles]\(ロール\)** に移動して、 **[Read-only]\(読み取り専用\)** ロールを選択し、 **[Privileges]\(特権\)** で **[Guest Operations]\(ゲスト操作\)** を選択します。 [Propagate to children]\(子に反映する\) チェック ボックスをオンにすると、vCenter Server のすべてのオブジェクトに特権を反映できます。

    :::image type="content" source="./media/tutorial-discover-vmware/guest-operations.png" alt-text="読み取り専用ロールでのゲスト操作を許可するチェック ボックス":::


> [!NOTE]
> vCenter Server アカウントのスコープを設定することにより、検出を特定の vCenter Server データセンター、クラスター、クラスターのフォルダー、ホスト、ホストのフォルダー、または個々のサーバーに制限することができます。 vCenter Server ユーザー アカウントのスコープを設定する方法の [**詳細を確認**](set-discovery-scope.md)してください。


### <a name="create-an-account-to-access-servers"></a>サーバーにアクセスするためのアカウントを作成する

インストールされているアプリケーションの検出、エージェントレスの依存関係の分析、SQL Server インスタンスおよびデータベースの検出を実行するには、サーバーに対して必要な特権を備えるユーザー アカウントが必要です。 このユーザー アカウントは、アプライアンス構成マネージャーで指定できます。 アプライアンスによってサーバーにエージェントがインストールされることはありません。

1. Windows サーバーの場合は、サーバーに対する管理アクセス許可が付与されたアカウント (ローカルまたはドメイン) を作成します。 SQL Server インスタンスおよびデータベースを検出するには、Windows または SQL Server アカウントが sysadmin サーバー ロールのメンバーである必要があります。 ユーザー アカウントに必要なロールを割り当てる方法の[詳細を確認](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles)してください。
2. Linux サーバーの場合は、root 権限が付与されたアカウントを作成します。 または、/bin/netstat および /bin/ls のファイルに対する次のアクセス許可が付与されたアカウントを作成することもできます: CAP_DAC_READ_SEARCH と CAP_SYS_PTRACE。

> [!NOTE]
> 構成マネージャーに複数のサーバー資格情報を追加して、インストールされているアプリケーションの検出、エージェントレスの依存関係の分析、および SQL Server インスタンスおよびデータベースの検出を実行できるようになりました。複数のドメイン、Windows (ドメイン以外)、Linux (ドメイン以外)、または SQL Server の認証資格情報を追加できます。 [**詳細情報**](add-server-credentials.md)

## <a name="set-up-a-project"></a>プロジェクトの設定

新しい Azure Migrate プロジェクトを設定します。

1. Azure portal の **[すべてのサービス]** で、**Azure Migrate** を検索します。
2. **[サービス]** で **[Azure Migrate]** を選択します。
3. **[概要]** で、 **[プロジェクトの作成]** を選択します。
5. **[プロジェクトの作成]** で、Azure サブスクリプションとリソース グループを選択します。 リソース グループがない場合は作成します。
6. **[プロジェクトの詳細]** で、プロジェクト名と、プロジェクトを作成したい地理的な場所を指定します。 [パブリック](migrate-support-matrix.md#supported-geographies-public-cloud)と [Government クラウド](migrate-support-matrix.md#supported-geographies-azure-government)でサポートされている地域を確認してください。

    :::image type="content" source="./media/tutorial-discover-vmware/new-project.png" alt-text="プロジェクト名とリージョンのボックス":::

7. **［作成］** を選択します
8. Azure Migrate プロジェクトがデプロイされるまで数分待ちます。**Azure Migrate: Server Assessment** ツールは、新しいプロジェクトに既定で追加されます。

> [!NOTE]
> プロジェクトを既に作成している場合は、その同じプロジェクトを使用して追加のアプライアンスを登録することで、より多くのサーバーを検出して評価できます。 [**詳細をご覧ください**](create-manage-projects.md#find-a-project)

## <a name="set-up-the-appliance"></a>アプライアンスを設定する

Azure Migrate: Server Assessment では、軽量の Azure Migrate アプライアンスが使用されます。 このアプライアンスによって、サーバー検出が実行され、サーバーの構成とパフォーマンス メタデータが Azure Migrate に送信されます。 このアプライアンスは、プロジェクトからダウンロードできる OVA テンプレートをデプロイすることで設定できます。

> [!NOTE]
> なんらかの理由で、テンプレートを使用してアプライアンスを設定できない場合は、既存の Windows Server 2016 サーバー上で PowerShell スクリプトを使用して設定できます。 [**詳細情報**](deploy-appliance-script.md#set-up-the-appliance-for-vmware)。

### <a name="deploy-with-ova"></a>OVA を使用してデプロイする

OVA テンプレートを使用してアプライアンスを設定するには:
1. アプライアンス名を指定し、ポータルで Azure Migrate プロジェクト キーを生成します。
1. OVA テンプレート ファイルをダウンロードし、それを vCenter Server にインポートします。 OVA がセキュリティで保護されていることを確認します。
1. OVA ファイルからアプライアンス VM を作成し、それが Azure Migrate に接続できることを確認します。
1. アプライアンスを初めて構成し、Azure Migrate プロジェクト キーを使用してプロジェクトに登録します。

### <a name="1-generate-the-azure-migrate-project-key"></a>1. Azure Migrate プロジェクト キーを生成する

1. **移行の目標** > **サーバー** > **Azure Migrate: Server Assessment** で、**検出** を選択します。
2. **[マシンの検出]**  >  **[マシンは仮想化されていますか?]** で、 **[はい。VMware vSphere Hypervisor を使用します]** を選択します。
3. **[1: Azure Migrate プロジェクト キーを生成します]** で、VMware 環境でのサーバーの検出用に設定する Azure Migrate アプライアンスの名前を指定します。その名前は 14 文字以内の英数字にする必要があります。
1. **[キーの生成]** をクリックして、必要な Azure リソースの作成を開始します。 リソースの作成中に [検出] ページを閉じないでください。
1. Azure リソースが正常に作成されると、**Azure Migrate プロジェクト キー** が生成されます。
1. このキーはアプライアンスを設定する際、登録を完了するために必要なので、コピーしておきます。

### <a name="2-download-the-ova-template"></a>2. OVA テンプレートをダウンロードする

**[2:Azure Migrate アプライアンスをダウンロードする]** で、.OVA ファイルを選択し、 **[ダウンロード]** をクリックします。

### <a name="verify-security"></a>セキュリティを確認する

OVA ファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたサーバーで、管理者用のコマンド ウィンドウを開きます。
2. OVA ファイルのハッシュを生成するには、次のコマンドを実行します。
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   使用例: ```C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256```

3. 最新のアプライアンス バージョンとハッシュ値を確認します。

    - Azure パブリック クラウドの場合:
    
        **アルゴリズム** | **ダウンロード** | **SHA256**
        --- | --- | ---
        VMware (11.9 GB) | [最新バージョン](https://go.microsoft.com/fwlink/?linkid=2140333) | e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74

    - Azure Government の場合:
    
        **アルゴリズム** | **ダウンロード** | **SHA256**
        --- | --- | ---
        VMware (85.8 MB) | [最新バージョン](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca

### <a name="3-create-the-appliance-server"></a>3.アプライアンス サーバーを作成する

ダウンロードしたファイルをインポートし、VMware 環境にサーバーを作成します。

1. vSphere Client コンソールで、 **[File]\(ファイル\)**  >  **[Deploy OVF Template]\(OVF テンプレートのデプロイ\)** の順にクリックします。
2. [Deploy OVF Template]\(OVF テンプレートのデプロイ\) ウィザードで **[Source]\(ソース\)** を選択し、OVA ファイルの場所を指定します。
3. **[Name]\(名前\)** と **[Location]\(場所\)** で、サーバーのフレンドリ名を指定します。 サーバーがホストされるインベントリ オブジェクトを選択します。
5. **[Host/Cluster]\(ホスト/クラスター\)** で、サーバーが実行されるホストまたはクラスターを指定します。
6. **[Storage]\(ストレージ\)** で、サーバーの保存先を指定します。
7. **[Disk Format]\(ディスク フォーマット\)** で、ディスクの種類とサイズを指定します。
8. **[Network Mapping]\(ネットワーク マッピング\)** で、サーバーの接続先となるネットワークを指定します。 このネットワークには、Azure Migrate にメタデータを送信するためのインターネット接続が必要です。
9. 設定を再確認したら、 **[Finish]\(完了\)** をクリックします。


### <a name="verify-appliance-access-to-azure"></a>アプライアンスによる Azure へのアクセスを確認する

[パブリック](migrate-appliance.md#public-cloud-urls)および[政府機関向け](migrate-appliance.md#government-cloud-urls)クラウドの Azure URL にアプライアンス サーバーから接続できることを確認します。


### <a name="4-configure-the-appliance"></a>4. アプライアンスを構成する

アプライアンスを初めて設定します。

> [!NOTE]
> ダウンロードした OVA でなく、[**PowerShell スクリプト**](deploy-appliance-script.md)を使用してアプライアンスを設定する場合、この手順の最初の 2 つの操作は必要ありません。

1. vSphere Client コンソールで、サーバーを右クリックしてから、 **[Open Console]\(コンソールを開く\)** を選択します。
2. アプライアンスの言語、タイム ゾーン、パスワードを指定します。
3. アプライアンス サーバーに接続できる任意のマシン上でブラウザーを開き、アプライアンス構成マネージャーの URL (`https://appliance name or IP address: 44368`) を開きます。

   または、アプライアンス サーバーのデスクトップから構成マネージャーへのショートカットを選択して、構成マネージャーを開くこともできます。
1. **ライセンス条項** に同意し、サード パーティの情報を確認します。
1. 構成マネージャー > **[前提条件のセットアップ]** で、以下を実行します。
   - **接続**:サーバーがインターネットにアクセスできることが、アプライアンスによって確認されます。 サーバーでプロキシを使用する場合は、次の操作を行います。
     - **[Set up proxy]\(プロキシの設定\)** をクリックし、プロキシ アドレス `http://ProxyIPAddress` または `http://ProxyFQDN` とリッスン ポートを指定します。
     - プロキシで認証が必要な場合は、資格情報を指定します。
     - サポートされるのは HTTP プロキシのみです。
     - プロキシの詳細を追加した場合、またはプロキシまたは認証を無効にした場合は、 **[保存]** をクリックして接続チェックを再度トリガーします。
   - **時刻同期**:検出を正常に機能させるには、アプライアンス上の時刻がインターネットの時刻と同期している必要があります。
   - **更新プログラムのインストール**:アプライアンスでは、最新の更新プログラムがインストールされることが保証されます。 確認が完了したら、 **[View appliance services]\(アプライアンス サービスを表示\)** をクリックして、アプライアンス サーバーで実行されているサービスの状態とバージョンを確認できます。
   - **Install VDDK\(VDDK のインストール\)** :アプライアンスでは、VMware vSphere Virtual Disk Development Kit (VDDK) がインストールされていることが確認されます。 インストールされていない場合は、**インストール手順** に記載されているとおりに、VMware から VDDK 6.7 をダウンロードし、ダウンロードした zip コンテンツをアプライアンス上の指定された場所に抽出します。

     Azure Migrate Server Migration では、Azure への移行時に VDDK を使用してサーバーがレプリケートされます。 
1. 必要に応じて、アプライアンス構成中ならいつでも、**前提条件の再実行** を行って、アプライアンスがすべての前提条件を満たしているかどうかを確認できます。

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-prerequisites.png" alt-text="アプライアンス構成マネージャーのパネル 1":::


### <a name="register-the-appliance-with-azure-migrate"></a>Azure Migrate にアプライアンスを登録する

1. ポータルからコピーした **Azure Migrate プロジェクト キー** を貼り付けます。 このキーがない場合は、 **[Server Assessment] > [検出] > [既存のアプライアンスの管理]** に移動して、キーの生成時に指定したアプライアンス名を選択して、対応するキーをコピーします。
1. Azure で認証するには、デバイス コードが必要です。 **[ログイン]** をクリックすると、次に示すように、デバイス コードを含むモーダルが開きます。

    :::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="デバイス コードを示すモーダル":::

1. **[Copy code & Login]\(コードのコピーとログイン\)** をクリックしてデバイス コードをコピーし、新しいブラウザー タブで Azure ログイン プロンプトを開きます。表示されない場合は、ブラウザーでポップアップ ブロックを無効にしてあることを確認します。
1. 新しいタブで、デバイス コードを貼り付け、Azure のユーザー名とパスワードを使用してサインインします。
   
   PIN を使用したサインインはサポートされていません。
3. ログインせずにログイン タブを誤って閉じた場合は、アプライアンス構成マネージャーのブラウザー タブを最新の情報に更新して、[ログイン] ボタンを再度有効にする必要があります。
1. 正常にログインしたら、アプライアンス構成マネージャーで前のタブに戻ります。
1. ログに使用した Azure ユーザー アカウントに、キーの生成時に作成した Azure リソースに対する正しいアクセス許可が付与されている場合、アプライアンスの登録が開始されます。
1. アプライアンスが正常に登録された後は、 **[詳細の表示]** をクリックすることで登録の詳細を確認できるようになります。

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-registration.png" alt-text="アプライアンス構成マネージャーのパネル 2":::

## <a name="start-continuous-discovery"></a>継続的な検出を開始する

### <a name="provide-vcenter-server-details"></a>vCenter Server の詳細を指定する

サーバーの構成およびパフォーマンス データを検出するには、アプライアンスから vCenter Server に接続する必要があります。

1. **[Step 1: Provide vCenter Server credentials]\(ステップ 1: vCenter Server の資格情報を指定する\)** で、 **[資格情報の追加]** をクリックして資格情報のフレンドリ名を指定し、vCenter Server で実行されているサーバーを検出するためにアプライアンスによって使用される vCenter Server アカウントの **ユーザー名** と **パスワード** を追加します。
    - この記事の前述のとおり、必要なアクセス許可を付与したアカウントを設定する必要があります。
    - 検出を特定の VMware オブジェクト (vCenter Server データセンター、クラスター、クラスターのフォルダー、ホスト、ホストのフォルダー、または個々の VM) にスコーピングする場合、Azure Migrate によって使用されるアカウントを制限するには、[この記事](set-discovery-scope.md)の手順を参照してください。
1. **[Step 2: Provide vCenter Server details]\(ステップ 2: vCenter Server の詳細を指定する\)** で、 **[Add discovery source]\(検出ソースの追加\)** をクリックして、ドロップダウンから資格情報のフレンドリ名を選択し、vCenter Server の **IP アドレスまたは FQDN** を指定します。 **[ポート]** を既定 (443) のまま使用することも、vCenter Server でリッスンするカスタム ポートを指定して **[保存]** をクリックしることもできます。
1. **[保存]** をクリックすると、アプライアンスでは、指定された資格情報を使用して vCenter Server への接続の検証が試行され、vCenter Server の IP アドレスまたは FQDN に対する **検証状態** がテーブルに示されます。
1. 検出を開始する前に、vCenter Server への接続はいつでも **再検証** できます。

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="vCenter Server の詳細に関するアプライアンス構成マネージャーのパネル 3":::

### <a name="provide-server-credentials"></a>サーバーの資格情報を指定する

**[Step 3: Provide server credentials to perform software inventory, agentless dependency analysis and discovery of SQL Server instances and databases]\(ステップ 3: サーバーの資格情報を指定して、ソフトウェア インベントリ、エージェントレスの依存関係の分析、SQL Server インスタンスとデータベースの検出を実行する\)** では、複数のサーバー資格情報を指定するか、これらの機能を利用しない場合はこのステップをスキップして vCenter Server 検出に進むかを選択できます。 この意図は後からいつでも変更できます。

:::image type="content" source="./media/tutorial-discover-vmware/appliance-server-credentials-mapping.png" alt-text="サーバーの詳細に関するアプライアンス構成マネージャーのパネル 3":::

> [!Note]
> VMware 環境で実行されている SQL Server インスタンスおよびデータベースの検出と評価は、現在プレビュー段階にあります。 この機能を試すには、[**このリンク**](https://aka.ms/AzureMigrate/SQL)を使用して、**オーストラリア東部** リージョンにプロジェクトを作成します。 オーストラリア東部に既にプロジェクトがあり、この機能を試したい場合は、ポータルでこれらの [**前提条件**](how-to-discover-sql-existing-project.md)が完了していることを確認してください。

これらの機能を利用する場合は、以下の手順に従ってサーバーの資格情報を指定できます。このアプライアンスでは、検出機能を実行するために自動的に資格情報がサーバーにマップされます。

- サーバーの資格情報を追加するには、 **[資格情報の追加]** ボタンをクリックします。これにより、モーダルが開き、ドロップダウンから **[資格情報の種類]** を選択できます。
- ドメイン、Windows (ドメイン以外)、Linux (ドメイン以外)、または SQL Server の認証資格情報を指定できます。 資格情報の指定方法とそれらの処理方法の[詳細を確認](add-server-credentials.md)してください。
- 資格情報の種類ごとに、資格情報のフレンドリ名を指定し、**ユーザー名** と **パスワード** を追加して、 **[保存]** をクリックする必要があります。
- ドメイン資格情報を選択する場合は、ドメインの FQDN も指定する必要があります。この FQDN は、そのドメインの Active Directory で資格情報の信頼性を検証するために必要です。
- インストールされているアプリケーションの検出、エージェントレスの依存関係の分析、または SQL Server インスタンスとデータベースの検出のために、アカウントで[必要なアクセス許可](add-server-credentials.md#required-permissions)を確認します。
- 複数の資格情報を一度に追加するには、 **[さらに追加]** をクリックして資格情報を保存して追加します。
- **[保存]** または **[さらに追加]** をクリックすると、アプライアンスによって、ドメインの Active Directory でドメインの資格情報の信頼性が検証されます。 これは、アプライアンスによって複数回反復処理が実行され、それぞれのサーバーに資格情報がマップされるときにアカウントのロックアウトを回避するために行われます。
- 資格情報テーブルで、すべてのドメイン資格情報の **検証状態** を確認できます。 検証されるのは、ドメイン資格情報のみです。
- 検証に失敗した場合は、 **[失敗]** 状態をクリックすると、発生したエラーを確認できます。また、問題を修正した後に **[資格情報の再検証]** をクリックすると、失敗したドメイン資格情報を再度検証できます。


### <a name="start-discovery"></a>検出を開始する

1. vCenter Server の検出を開始するには、 **[Start discovery]\(検出の開始\)** をクリックします。 検出が正常に開始された後、vCenter Server の IP アドレスまたは FQDN に対する検出状態をソース テーブルで確認できます。
1. サーバーの資格情報を指定した場合は、vCenter Server の検出が完了した後に、ソフトウェア インベントリ (インストールされているアプリケーションの検出) が自動的に開始されます。 ソフトウェア インベントリは 12 時間ごとに実行されます。
1. [ソフトウェア インベントリ](how-to-discover-applications.md)では、サーバー上で実行されている SQL Server インスタンスを特定します。その情報を使用して、アプライアンスは、そのアプライアンスで指定された Windows 認証または SQL Server 認証資格情報を使用してインスタンスに接続し、SQL Server データベースとそれらのプロパティに関するデータを収集しようとします。 SQL 検出は 24 時間ごとに実行されます。
1. ソフトウェア インベントリ時には、追加されたサーバーの資格情報がサーバーに対して反復処理され、エージェントレスの依存関係の分析のために検証されます。サーバーに対するエージェントレスの依存関係の分析は、ポータルから有効にすることができます。 検証に成功したサーバーのみを選択し、エージェントレスの依存関係の分析を有効にすることができます。

> [!Note]
>Azure Migrate により、Azure Migrate アプライアンスとソース SQL Server インスタンスの間の通信が暗号化されます (Encrypt 接続プロパティが TRUE に設定されている場合)。 これらの接続は、[**TrustServerCertificate**](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) を使用して暗号化されます (TRUE に設定)。トランスポート層は、SSL を使用してチャネルを暗号化し、証明書チェーンをバイパスして信頼を検証します。 アプライアンス サーバーは、[**証明書のルート証明機関を信頼**](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)するように設定する必要があります。<br/>
サーバーの起動時に証明書がサーバーにプロビジョニングされていない場合、SQL Server では、ログイン パケットの暗号化に使用される自己署名証明書が生成されます。 [**詳細情報**](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)。

検出は次のように行われます。
- 検出されたサーバーのインベントリがポータルに表示されるまでに、約 15 分かかります。
- インストールされているアプリケーションの検出には時間がかかることがあります。 期間は検出されたサーバーの数によって異なります。 500 台のサーバーがある場合、検出されたインベントリが Azure Migrate ポータルに表示されるまでに約 1 時間かかります。
- サーバーの検出が完了した後、ポータルからサーバーに対するエージェントレスの依存関係の分析を有効にすることができます。
- SQL Server インスタンスおよびデータベースのデータは、検出開始から 24 時間以内にポータルに表示され始めます。

## <a name="next-steps"></a>次のステップ

- Azure VM への移行のために[サーバーを評価](./tutorial-assess-vmware-azure-vm.md)します。
- Azure SQL への移行のために [SQL Server を評価](./tutorial-assess-sql.md)します。
- 検出中にアプライアンスによって収集される[データを確認](migrate-appliance.md#collected-data---vmware)します。