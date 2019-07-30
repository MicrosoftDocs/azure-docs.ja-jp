---
title: エージェントベースの Azure Migrate Server Migration を使用してオンプレミスの VMware VM を Azure に移行する | Microsoft Docs
description: この記事では、Azure Migrate Server Migration を使用して、オンプレミス マシンの Azure へのエージェントベース移行を実行する方法を説明します
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: d222936f93f90573a46cd7f6216fbde8043332c7
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68261407"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>VMware VM を Azure に移行する (エージェントベース)

この記事では、Azure Migrate Server Migration ツールによるエージェントベースの移行を使用して、オンプレミスの VMware VM を Azure に移行する方法について説明します。

[Azure Migrate](migrate-services-overview.md) では、オンプレミスのアプリとワークロード、および AWS または GCP VM インスタンスの検出、評価、Azure への移行を追跡するための中央ハブが提供されます。 このハブには、評価および移行のための Azure Migrate ツールのほか、サードパーティの独立系ソフトウェア ベンダー (ISV) のオファリングが用意されています。


このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * ソース環境を設定し、エージェントベースの移行のために Azure Migrate レプリケーション アプライアンスをデプロイします。
> * 移行のターゲット環境を設定します。
> * レプリケーション ポリシーを設定します。
> * レプリケーションを有効にします。
> * テスト移行を実行して、すべてが想定どおりに動作していることを確認します。
> * Azure への完全な移行を実行します。

> [!NOTE]
> チュートリアルでは、概念実証をすばやく設定できるように、シナリオの最も簡単なデプロイ パスを示します。 チュートリアルではできるだけ既定のオプションを使用しており、使用可能な設定とパスをすべて示しているわけではありません。 詳細な手順については、VMware の評価と移行に関するハウツーを参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。

## <a name="before-you-begin"></a>開始する前に

VM を Azure に移行する前に、Azure Migrate Server Assessment を使用して VMware VM の評価を試みることをお勧めします。 次のとおりに評価を設定します。

1. チュートリアルに従って、評価のために [Azure と VMware を準備](tutorial-prepare-vmware.md)します。
2. 次に、[こちらのチュートリアル](tutorial-assess-vmware.md)に従って、評価のために Azure Migrate アプライアンスを設定し、VM を検出して評価します。


評価を試してみることをお勧めしますが、VM を移行する前に評価を実行しなければならないわけではありません。

## <a name="migration-methods"></a>移行の方法

Azure Migrate Server Migration ツールを使用して、VMware VM を Azure に移行できます。 このツールには、VMware VM の移行に関していくつかのオプションが用意されています。

- エージェントレス レプリケーション。 何もインストールすることなく VM を移行します。
- エージェントベースの移行、 またはレプリケーション。 レプリケーションのためにエージェント (モビリティ サービス エージェント) を VM にインストールします。

エージェントレスとエージェントベース、いずれの移行を使用するかを決定するには、これらの記事を確認してください。

- VMware の移行オプション[について学習](server-migrate-overview.md)してください。
- エージェントレス移行の[制限事項を確認](server-migrate-overview.md#agentless-migration-limitations)してください。
- エージェントレス移行を試すには、[こちらの記事に従って](tutorial-migrate-vmware.md)ください。



## <a name="prerequisites"></a>前提条件

このチュートリアルを始める前に、次の準備が必要です。

1. VMware の移行アーキテクチャを[確認](migrate-architecture.md)します。
2. ご自分の Azure アカウントに仮想マシンの共同作成者ロールが割り当てられ、以下を行うためのアクセス許可を持っていることを確認します。

    - 選択したリソース グループ内に VM を作成する。
    - 選択した仮想ネットワーク内に VM を作成する。
    - Azure マネージド ディスクに書き込む。 

3. [Azure ネットワークをセットアップ](../virtual-network/manage-virtual-network.md#create-a-virtual-network)します。 オンプレミスのマシンは Azure マネージド ディスクにレプリケートされます。 移行のために Azure にフェールオーバーすると、これらのマネージド ディスクから Azure VM が作成され、移行の設定時に指定した Azure ネットワークに参加させられます。


## <a name="prepare-azure"></a>Azure を準備する

Azure Migrate Server Assessment を使用して評価を既に実行している場合、このセクションの手順はスキップしてかまいません。これらの手順は既に完了しているためです。 

評価を実行していない場合は、Azure Migrate Server Migration を使用して移行する前に、Azure のアクセス許可を設定する必要があります。

- **プロジェクトを作成する**: ご自分の Azure アカウントには、Azure Migrate プロジェクトを作成するためのアクセス許可が必要です。 
- **Azure Migrate レプリケーション アプライアンスを登録する**: レプリケーション アプライアンスによって、ご自分の Azure アカウントに Azure Active Directory アプリが作成され、登録されます。 これに対するアクセス許可を委任する必要があります。
- **キー コンテナーを作成する**: Azure Migrate Server Migration を使用して VMware VM を移行するために、Azure Migrate によってリソース グループにキー コンテナーが作成され、ご自分のサブスクリプションのレプリケーション ストレージ アカウントへのアクセス キーが管理されます。 コンテナーを作成するには、Azure Migrate プロジェクトが存在しているリソース グループに対するロールの割り当てアクセス許可が必要です。 


### <a name="assign-permissions-to-create-project"></a>プロジェクトを作成するためのアクセス許可を割り当てる

1. Azure portal でサブスクリプションを開き、 **[アクセス制御 (IAM)]** を選択します。
2. **[アクセスの確認]** で関連するアカウントを探し、それをクリックしてアクセス許可を表示します。
3. **共同作成者**または**所有者**のアクセス許可を持っている必要があります。
    - 無料の Azure アカウントを作成したばかりであれば、自分のサブスクリプションの所有者になっています。
    - サブスクリプションの所有者でない場合は、所有者と協力してロールを割り当てます。

### <a name="assign-permissions-to-register-the-replication-appliance"></a>レプリケーション アプライアンスを登録するためのアクセス許可を割り当てる

エージェントベースの移行の場合、ご自分のアカウントで Azure AD アプリを作成および登録するためのアクセス許可を Azure Migrate Server Migration に委任します。 次のいずれかの方法を使用して、アクセス許可を割り当てることができます。

- テナントおよびグローバル管理者は、Azure AD アプリを作成および登録するためのアクセス許可を、テナント内のユーザーに付与できます。
- テナントおよびグローバル管理者は、アプリケーション開発者ロール (アクセス許可が含まれています) をアカウントに割り当てることができます。

次の点に注意してください。

- アプリには、上記で説明した以外に、サブスクリプションに対するアクセス許可はありません。
- 新しいレプリケーション アプライアンスを登録するときに必要なのは、これらのアクセス許可だけです。 アクセス許可はレプリケーション アプライアンスを設定した後で削除できます。 


#### <a name="grant-account-permissions"></a>アカウントへのアクセス許可の付与

テナントおよびグローバル管理者は、次のようにアクセス許可を付与できます

1. テナント/グローバル管理者は Azure AD で **[Azure Active Directory]**  >  **[ユーザー]**  >  **[ユーザー設定]** の順に移動します。
2. 管理者は、 **[アプリの登録]** を **[はい]** に設定する必要があります。

    ![Azure AD のアクセス許可](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> これは、重要ではない既定の設定です。 [詳細情報](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance)。

#### <a name="assign-application-developer-role"></a>アプリケーション開発者ロールの割り当て 

テナントおよびグローバル管理者は、アプリケーション開発者ロールをアカウントに割り当てることができます。 [詳細情報](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)。

## <a name="assign-permissions-to-create-key-vault"></a>キー コンテナーを作成するためのアクセス許可を割り当てる

次のように、Azure Migrate プロジェクトが存在しているリソース グループに対してロールの割り当てアクセス許可を割り当てます。

1. Azure portal で、リソース グループの **[アクセス制御 (IAM)]** を選択します。
2. **[アクセスの確認]** で関連するアカウントを探し、それをクリックしてアクセス許可を表示します。 **所有者** (または**共同作成者**および**ユーザー アクセス管理者**) のアクセス許可が必要です。
3. 必要なアクセス許可がない場合は、リソース グループの所有者にそれらを依頼してください。 


## <a name="prepare-on-premises-vmware"></a>オンプレミスの VMware を準備する

### <a name="prepare-an-account-for-automatic-discovery"></a>自動検出用のアカウントを準備する

Azure Migrate Server Migration では、以下を実行するために、VMware サーバーへのアクセスが必要です。

- VM を自動的に検出します。 少なくとも読み取り専用のアカウントが必要です。
- レプリケーション、フェールオーバー、およびフェールバックを調整します。 ディスクを作成および削除する、VM の電源をオンにするなどの操作を実行できるアカウントが必要です。

次の手順に従って、このアカウントを作成します。

1. 専用のアカウントを使用するには、vCenter レベルでロールを作成します。 ロールに **Azure_Site_Recovery** のような名前を付けます。
2. 次の表にまとめられているアクセス許可をそのロールに割り当てます。
3. vCenter サーバーまたは vSphere ホストにユーザーを作成します。 ユーザーにロールを割り当てます。

#### <a name="vmware-account-permissions"></a>VMware アカウントのアクセス許可

**タスク** | **ロールとアクセス許可** | **詳細**
--- | --- | ---
**VM 検出** | 読み取り専用以上の権限を持つユーザー<br/><br/> データ センター オブジェクト -> 子オブジェクトへのプロパゲート、ロール=読み取り専用 | ユーザーはデータセンター レベルで割り当てられ、データセンター内のすべてのオブジェクトに対してアクセス権を持ちます。<br/><br/> アクセスを制限するには、**子オブジェクトへの伝達**特権を持つ**アクセスなし**ロールを子オブジェクト (vSphere ホスト、データストア、VM、ネットワーク) に割り当てます。
**完全なレプリケーション、フェールオーバー、フェールバック** |  必要なアクセス許可を備えたロール (Azure_Site_Recovery) を作成し、このロールを VMware のユーザーまたはグループに割り当てる<br/><br/> データ センター オブジェクト –> 子オブジェクトへの伝達、ロール = Azure_Site_Recovery<br/><br/> データストア -> 空間の割り当て、データストアの参照、ファイルの低レベルの操作、ファイルの削除、仮想マシン ファイルの更新<br/><br/> ネットワーク -> ネットワークの割り当て<br/><br/> リソース -> リソース プールへの VM の割り当て、電源がオフの VM の移行、電源がオンの VM の移行<br/><br/> タスク -> タスクの作成、タスクの更新<br/><br/> 仮想マシン -> 構成<br/><br/> 仮想マシン -> 対話 -> 質問への回答、デバイス接続、CD メディアの構成、フロッピー メディアの構成、電源オフ、電源オン、VMware ツールのインストール<br/><br/> 仮想マシン -> インベントリ -> 作成、登録、登録解除<br/><br/> 仮想マシン -> プロビジョニング -> 仮想マシンのダウンロードの許可、仮想マシン ファイルのアップロードの許可<br/><br/> 仮想マシン -> スナップショット -> スナップショットの削除 | ユーザーはデータセンター レベルで割り当てられ、データセンター内のすべてのオブジェクトに対してアクセス権を持ちます。<br/><br/> アクセスを制限するには、**子オブジェクトへの伝達**特権を持つ**アクセスなし**ロールを子オブジェクト (vSphere ホスト、データストア、VM、ネットワーク) に割り当てます。

### <a name="prepare-an-account-for-mobility-service-installation"></a>モビリティ サービスのインストール用のアカウントを準備する

モビリティ サービスは、レプリケートするマシンにインストールする必要があります。

- マシンのレプリケーションを有効にしている場合は、Azure Migrate レプリケーション アプライアンスでこのサービスをプッシュ インストールできます。または、手動でインストールしたり、インストール ツールを使用したりできます。
- このチュートリアルでは、プッシュ インストールでモビリティ サービスをインストールします。
- プッシュ インストールの場合は、Azure Migrate Server Migration で VM へのアクセスに使用できるアカウントを準備する必要があります。

アカウントを次のように準備します。

1. VM にインストールするアクセス許可を持つドメイン アカウントまたはローカル アカウントを準備します。
2. Windows VM の場合、ドメイン アカウントを使用していないときはローカル コンピューターでリモート ユーザーのアクセス制御を無効にします。レジストリで、**HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** の下に DWORD エントリ **LocalAccountTokenFilterPolicy** を追加し、値を設定します。
3. Linux VM の場合、ソースの Linux サーバーにルート アカウントを準備します。


### <a name="check-vmware-requirements"></a>VMware の要件を確認する

VMware サーバーと VM が、Azure への移行の要件に準拠していることを確認します。 


> [!NOTE]
> Azure Migrate Server Migration を使用したエージェントベースの移行は、Azure Site Recovery サービスの機能に基づいています。 一部の要件は、Site Recovery のドキュメントにリンクされている場合があります。

1. VMware サーバーの要件を[確認](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements)します。
2. 移行のための VM サポート要件を[確認](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements)します。
3. VM 設定を確認します。 Azure にレプリケートするオンプレミスの VM は、「[Azure VM の要件](migrate-support-matrix-vmware.md#azure-vm-requirements)」に準拠している必要があります。



## <a name="add-the-azure-migrate-server-migration-tool"></a>Azure Migrate Server Migration ツールを追加する

チュートリアルに従って VMware VM を評価していない場合は、Azure Migrate プロジェクトを設定して、Azure Migrate Server Migration ツールを追加します。

1. Azure portal の **[すべてのサービス]** で、**Azure Migrate** を検索します。
2. **[サービス]** で **[Azure Migrate]** を選択します。

    ![Azure Migrate の設定](./media/tutorial-migrate-vmware-agent/azure-migrate-search.png)

3. **[概要]** で **[サーバーの評価と移行]** をクリックします。
4. **[サーバーの検出、評価、移行]** で、 **[サーバーの評価と移行]** をクリックします。

    ![サーバーの検出と評価](./media/tutorial-migrate-vmware-agent/assess-migrate.png)

1. **[サーバーの検出、評価、移行]** で、 **[ツールの追加]** をクリックします。
2. **[移行プロジェクト]** で、自分の Azure サブスクリプションを選択し、リソース グループがない場合は作成します。
3. **[プロジェクトの詳細]** で、プロジェクト名と、プロジェクトを作成したい地域を指定し、 **[次へ]** をクリックします。

    ![Azure Migrate プロジェクトの作成](./media/tutorial-migrate-vmware-agent/migrate-project.png)

    Azure Migrate プロジェクトは、これらのいずれの地域でも作成できます。

    **地理的な場所** | **[リージョン]**
    --- | ---
    アジア | 東南アジア
    ヨーロッパ | 北ヨーロッパまたは西ヨーロッパ
    米国 | 米国東部または米国中西部

    プロジェクトのために指定した地理的な場所は、オンプレミスの VM から収集されたメタデータを格納するためにのみ使用されます。 実際の移行では、任意のターゲット リージョンを選択できます。
4. **[評価ツールの選択]** で、 **[今は評価ツールの追加をスキップします]**  >  **[次へ]** の順に選択します。
5. **[移行ツールの選択]** で、次を選択します: **[Azure Migrate: Server Migration]**  >  **[次へ]** 。
6. **[ツールの確認と追加]** で設定を確認し、 **[ツールの追加]** をクリックします
7. ツールを追加すると、Azure Migrate プロジェクトの **[サーバー]**  >  **[移行ツール]** に表示されます。

## <a name="set-up-the-replication-appliance"></a>レプリケーション アプライアンスを設定する

移行の最初の手順は、レプリケーション アプライアンスを設定することです。 レプリケーション アプライアンスは、可用性の高い単一のオンプレミスの VMware VM であり、これらのコンポーネントをホストします。

- **構成サーバー**:構成サーバーは、オンプレミスと Azure の間の通信を調整し、データのレプリケーションを管理します。
- **プロセス サーバー**:プロセス サーバーはレプリケーション ゲートウェイとして機能します。 レプリケーション データを受信し、それをキャッシュ、圧縮、暗号化によって最適化して、Azure のキャッシュ ストレージ アカウントに送信します。 また、プロセス サーバーでは、レプリケートする VM へのモビリティ サービス エージェントのインストールや、オンプレミスの VMware VM の自動検出も行います。


レプリケーション アプライアンスを設定するには、用意されている Open Virtualization Application (OVA) テンプレートをダウンロードします。 テンプレートを VMware にインポートして、レプリケーション アプライアンス VM を作成します。 

### <a name="download-the-replication-appliance-template"></a>レプリケーション アプライアンスのテンプレートをダウンロードする

次のとおりにテンプレートをダウンロードします。

1. Azure Migrate プロジェクトで、 **[移行の目標]** の **[サーバー]** をクリックします。
2. **[Azure Migrate - サーバー]**  >  **[Azure Migrate: Server Migration]** で、 **[検出]** をクリックします。

    ![Discover VMs](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. **[マシンの検出]**  >  **[マシンは仮想化されていますか?]** で、 **[はい。VMware vSphere Hypervisor を使用します]** をクリックします。
4. **[移行方法]** で **[エージェントベースのレプリケーションを使用する]** を選択します。
5. **[ターゲット リージョン]** で、マシンの移行先にする Azure リージョンを選択します。
6. **[移行先のリージョンが <リージョン名> であることを確認してください]** を選択します。
7. **[リソースの作成]** をクリックします。 これで、Azure Site Recovery コンテナーがバックグラウンドで作成されます。
    - このボタンのクリック後は、このプロジェクトのターゲット リージョンを変更することはできません。
    - 後続のすべての移行は、このリージョンに対して行われます。

    ![Recovery Services コンテナーの作成](./media/tutorial-migrate-vmware-agent/create-resources.png)

8. **[新しいレプリケーション アプライアンスをインストールしますか?]** で、 **[レプリケーション アプライアンスのインストール]** を選択します。
9. **[ダウンロード]** をクリックし、レプリケーション アプライアンスをダウンロードします。 これにより、アプライアンスを実行する新しい VMware VM の作成に使用する OVF テンプレートがダウンロードされます。
    ![OVA のダウンロード](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. リソース グループと Recovery Services コンテナーの名前をメモしてください。 これらはアプライアンスのデプロイ時に必要です。


### <a name="import-the-template-in-vmware"></a>VMware にテンプレートをインポートする

OVF テンプレートのダウンロード後、それを VMware にインポートして、Windows Server 2016 を稼働させている VMware VM 上にレプリケーション アプリケーションを作成します。

1. VMWare vSphere Client を使用して、VMware vCenter サーバーまたは vSphere ESXi ホストにサインインします。
2. **[File]\(ファイル\)** メニューの **[Deploy OVF Template]\(OVF テンプレートのデプロイ\)** を選択し、 **[Deploy OVF Template]\(OVF テンプレートのデプロイ\)** ウィザードを起動します。 
3. **[Select source]\(ソースの選択\)** で、ダウンロードした OVF の場所を入力します。
4. **[Review details]\(詳細の確認\)** で、 **[Next]\(次へ\)** を選択します。
5. **[Select name and folder]\(名前とフォルダーの選択\)** および **[Select configuration]\(構成の選択\)** は、既定の設定のままにします。
6. パフォーマンスを最大にするために、 **[Select storage]\(ストレージの選択\)**  >  **[Select virtual disk format]\(仮想ディスクの形式の選択\)** で、 **[Thick Provision Eager Zeroed]\(シック プロビジョニング Eager Zeroed\)** を選択します。
7. ウィザードの残りのページでは、既定の設定をそのまま使います。
8. **[Ready to complete]\(完了の準備完了\)** で、既定の設定で VM をセットアップするには、 **[Power on after deployment]\(デプロイ後に電源をオンにする\)**  >  **[Finish]\(完了\)** の順に選択します。

   > [!TIP]
   > 別の NIC を追加する場合は、 **[Power on after deployment]\(デプロイ後に電源をオンにする\)** をオフにする >  **[Finish]\(完了\)** を選択する、と操作します。 既定では、テンプレートには NIC が 1 つ含まれています。 デプロイ後にさらに NIC を追加することができます。

### <a name="kick-off-replication-appliance-setup"></a>レプリケーション アプライアンスの設定を開始する

1. VMWare vSphere Client のコンソールで、VM をオンにします。
2. VM が Windows Server 2016 のインストール エクスペリエンスで起動します。 使用許諾契約書に同意し、管理者パスワードを入力します。
3. インストールの完了後、管理者パスワードを使用して、管理者として VM にサインインします。
4. 初めてサインインすると、数秒以内にレプリケーション アプライアンス設定ツール (Azure Site Recovery 構成ツール) が起動します。
5. Azure Migrate Server Migration へのアプライアンスの登録に使用する名前を入力します。 その後、 **[次へ]** をクリックします。
6. このツールは、VM が Azure に接続できることを確認します。 接続が確立された後、 **[サインイン]** を選択して、自分の Azure サブスクリプションにサインインします。
7. ツールによる Azure AD アプリの登録が完了し、アプライアンスが識別されるのを待ちます。 アプライアンスが再起動します。
1. 再度マシンにサインインします。 数秒後に、構成サーバーの管理ウィザードが自動的に起動します。

### <a name="register-the-replication-appliance"></a>レプリケーション アプライアンスを登録する

レプリケーション アプライアンスの設定と登録を完了します。

1. 構成サーバーの管理ウィザードで、 **[接続の設定]** を選択します。
2. VM を検出するためとソース マシンに対するモビリティ サービスのプッシュ インストールを実行するためにレプリケーション アプライアンスで使用される NIC を選択します (既定では 1 つの NIC しかありません)。
3. Azure と接続するためにレプリケーション アプライアンスによって使用される NIC を選択します。 次に、 **[保存]** を選択します。 構成後、この設定を変更することはできません。
4. アプライアンスがプロキシ サーバーの内側に配置されている場合は、プロキシ設定を指定する必要があります。
    - プロキシ名は、 **http://ip-address** または **http://FQDN** と指定します。 HTTPS プロキシ サーバーはサポートされていません。
5. サブスクリプション、リソース グループ、コンテナーの詳細を求められたら、アプライアンスのテンプレートをダウンロードしたときにメモした詳細を追加してください。
6. **[サードパーティ製ソフトウェアのインストール]** でライセンス契約に同意します。 **[ダウンロードしてインストール]** を選択して MySQL サーバーをインストールします。
7. **[VMware PowerCLI のインストール]** を選択します。 この操作を行う前に、すべてのブラウザー ウィンドウを閉じてください。 その後 **[続行]** を選択します。
8. **[アプライアンス構成の検証]** で、続行する前に前提条件が検証されます。
9. **[Configure vCenter Server/vSphere ESXi server]\(vCenter Server/vSphere ESXi サーバーの構成\)** で、レプリケートする VM が存在している vCenter サーバーまたは vSphere ホストの FQDN または IP アドレスを入力します。 サーバーがリッスンするポートを入力します。 コンテナーで VMware サーバーに使うフレンドリ名を入力します。
10. VMware の検出用に[作成](#prepare-an-account-for-automatic-discovery)したアカウントの資格情報を入力します。 **[追加]**  >  **[続行]** を選択します。
11. VM のレプリケーションを有効にした際のモビリティ サービスのプッシュ インストール用に[作成](#prepare-an-account-for-mobility-service-installation)した資格情報を、 **[仮想マシンの資格情報の構成]** に入力します。  
    - Windows マシンの場合、このアカウントは、レプリケートするマシンに対するローカル管理者特権を持っている必要があります。
    - Linux の場合は、ルート アカウントの詳細を指定します。
12. **[構成の確定]** を選択して、登録を完了します。


レプリケーション アプライアンスが登録された後、指定の設定を使用して Azure Migrate Server Assessment が VMware サーバーに接続され、VM が検出されます。 検出された VM は、 **[その他]** タブの **[管理]**  >  **[項目が検出されました]** で確認できます。


## <a name="replicate-vms"></a>VM をレプリケートする

1. Azure Migrate プロジェクトの **[サーバー]** の **[Azure Migrate: Server Migration]** で、 **[レプリケート]** をクリックします。

    ![VM をレプリケートする](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. **[レプリケート]** > **[ソースの設定]**  >  **[マシンは仮想化されていますか?]** で、 **[はい (VMware vSphere の場合)]** を選択します。
3. **[オンプレミスのアプライアンス]** で、自分が設定した Azure Migrate アプライアンスの名前を選択します。
4. **[vCenter Server]** で、VM を管理している vCenter サーバー、または VM がホストされている vSphere サーバーの名前を指定します。
5. **[プロセス サーバー]** で、レプリケーション アプライアンスの名前を選択します。
6. **[ゲストの資格情報]** で、モビリティ サービスのプッシュ インストールに使用される VM 管理アカウントを指定します。 その後、 **[次へ:仮想マシン]** をクリックします。

    ![VM をレプリケートする](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. **[仮想マシン]** で、レプリケートするマシンを選択します。

    - VM の評価を実行した場合は、評価結果から VM のサイズ設定とディスクの種類 (Premium または Standard) の推奨事項を適用できます。 これを行うには、 **[Azure Migrate Assessment から移行設定をインポートしますか?]** で **[はい]** オプションを選択します。
    - 評価を実行しなかった場合、または評価の設定を使用しない場合は、 **[いいえ]** オプションを選択します。
    - 評価の使用を選択した場合は、VM グループと評価名を選択します。

8. 移行したい各 VM を確認します。 その後、 **[次へ:ターゲット設定]** をクリックします。
9. **[ターゲット設定]** で、サブスクリプションと、移行先となるターゲット リージョンを選択し、移行後に Azure VM が配置されるリソース グループを指定します。
10. **[仮想ネットワーク]** で、移行後に Azure VM の参加先となる Azure VNet およびサブネットを選択します。
11. **[Azure ハイブリッド特典]** で、

    - Azure ハイブリッド特典を適用しない場合は、 **[いいえ]** を選択します。 その後、 **[次へ]** をクリックします。
    - アクティブなソフトウェア アシュアランスまたは Windows Server のサブスクリプションの対象となっている Windows Server マシンがあり、移行中のマシンにその特典を適用する場合は、 **[はい]** を選択します。 その後、 **[次へ]** をクリックします。

12. **[コンピューティング]** で、VM の名前、サイズ、OS ディスクの種類、可用性セットを確認します。 VM は [Azure の要件](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements)に準拠している必要があります。

    - **VM サイズ**: 評価の推奨事項を使用している場合は、[VM サイズ] ドロップダウンに推奨サイズが表示されます。 それ以外の場合は、Azure Migrate によって、Azure サブスクリプション内の最も近いサイズが選択されます。 または、 **[Azure VM サイズ]** でサイズを手動で選択します。 
    - **OS ディスク**:VM の OS (ブート) ディスクを指定します。 OS ディスクは、オペレーティング システムのブートローダーとインストーラーがあるディスクです。 
    - **可用性セット**:移行後に VM を Azure 可用性セットに配置する必要がある場合は、セットを指定します。 このセットは、移行用に指定するターゲット リソース グループ内に存在する必要があります。

13. **[ディスク]** で、VM ディスクを Azure にレプリケートするかどうかを指定し、Azure でのディスクの種類 (Standard SSD か HDD、または Premium マネージド ディスク) を選択します。 その後、 **[次へ]** をクリックします。
    - レプリケーションからディスクを除外できます。
    - ディスクは除外すると、移行後に Azure VM 上に存在しなくなります。 

14. **[レプリケーションの確認と開始]** で、設定を確認し、 **[レプリケート]** をクリックして、サーバーの初期レプリケーションを開始します。

> [!NOTE]
> レプリケーションが開始される前であれば、 **[管理]**  >  **[マシンのレプリケート]** でレプリケーションの設定をいつでも更新できます。 レプリケーションの開始後は、設定を変更することができません。




## <a name="track-and-monitor"></a>追跡して監視する

- **[レプリケート]** をクリックすると、レプリケーションの開始ジョブが開始されます。 
- レプリケーションの開始ジョブが正常に終了すると、マシンで Azure への初期レプリケーションが開始されます。
- 初期レプリケーションが完了すると、差分レプリケーションが開始されます。 オンプレミスのディスクに対する増分変更は、Azure のレプリカ ディスクに定期的にレプリケートされます。


ジョブの状態は、ポータルの通知で追跡できます。

![ジョブの追跡](./media/tutorial-migrate-vmware-agent/jobs.png)

レプリケーションの状態を監視するには、**サーバーをレプリケートしています** をクリックします (**Azure Migrate: Server Migration** 内)。
![レプリケーションの監視](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

## <a name="run-a-test-migration"></a>テスト移行を実行する


差分レプリケーションが開始されるとき、Azure への完全な移行を実行する前に、VM のテスト移行を実行できます。 各マシンで少なくとも 1 回は、移行前にこれを実行することを強くお勧めします。

- テスト移行を実行すると、移行が想定どおりに動作することが確認されます。オンプレミスのマシンに影響はなく、稼働状態が維持され、レプリケーションが続行されます。 
- テスト移行では、レプリケートされたデータを使用して Azure VM を作成することによって、移行がシミュレートされます (通常は、自分の Azure サブスクリプション内の非運用 VNet に移行されます)。
- レプリケートされたテスト Azure VM を使用して、移行を検証し、アプリのテストを実行して、完全な移行前に問題に対処することができます。

テスト移行を実行するには、次のようにします。


1. **[移行の目標]**  >  **[サーバー]**  >  **[Azure Migrate: Server Migration]** で、 **[移行されたサーバーをテストします]** をクリックします。

     ![移行されたサーバーのテスト](./media/tutorial-migrate-vmware-agent/test-migrated-servers.png)

2. テストする VM を右クリックし、 **[テスト移行]** をクリックします。

    ![テスト移行](./media/tutorial-migrate-vmware-agent/test-migrate.png)

3. **[テスト移行]** で、移行後に Azure VM が配置される Azure VNet を選択します。 非運用環境の VNet を使用することをお勧めします。
4. **テスト移行**ジョブが開始されます。 ポータルの通知でジョブを監視します。
5. 移行の完了後、Azure portal の **[仮想マシン]** で、移行された Azure VM を確認します。 マシン名には、サフィックス **-Test** が含まれています。
6. テストが完了したら、 **[マシンのレプリケート]** で Azure VM を右クリックし、 **[テスト移行をクリーンアップ]** をクリックします。

    ![移行のクリーンアップ](./media/tutorial-migrate-vmware-agent/clean-up.png)


## <a name="migrate-vms"></a>VM の移行

テスト移行が想定どおりに動作することを確認したら、オンプレミスのマシンを移行できます。

1. Azure Migrate プロジェクトの **[サーバー]**  >  **[Azure Migrate: Server Migration]** で、 **[サーバーをレプリケートしています]** をクリックします。

    ![サーバーをレプリケートしています](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

2. **[マシンのレプリケート]** で VM を右クリックし、 **[移行]** を選択します。
3. **[移行]**  >  **[仮想マシンをシャットダウンし、データ損失のない計画された移行を実行しますか]** で、 **[はい]**  >  **[OK]** の順に選択します。
    - 既定では、Azure Migrate によってオンプレミスの VM がシャットダウンされ、前回のレプリケーションが発生した後に発生したすべての VM の変更を同期するためにオンデマンド レプリケーションが実行されます。 こうすることで、データ損失がなくなります。
    - VM をシャットダウンしたくない場合は、 **[いいえ]** を選択します
4. VM に対して移行ジョブが開始されます。 Azure 通知でジョブを追跡します。
5. ジョブが完了したら、 **[仮想マシン]** ページで VM を表示して管理できます。

## <a name="complete-the-migration"></a>移行を完了する

1. 移行が完了したら、VM を右クリックして、 **[移行を停止する]** を選択します。 これで、オンプレミスのマシンのレプリケーションが停止し、VM のレプリケーション状態情報がクリーンアップされます。
2. Azure VM の [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) または [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) エージェントを、移行されたマシンにインストールします。
3. データベース接続文字列、および Web サーバー構成の更新など、移行後のアプリの微調整を実行します。
4. Azure で現在実行されている移行後のアプリケーション上で、最終的なアプリケーションと移行の受け入れのテストを実行します。
5. 移行された Azure VM インスタンスにトラフィックを切り替えます。
6. ローカル VM インベントリからオンプレミスの VM を削除します。
7. ローカル バックアップからオンプレミスの VM を削除します。
8. Azure VM の新しい場所と IP アドレスを示すように内部ドキュメントを更新します。 

## <a name="post-migration-best-practices"></a>移行後のベスト プラクティス

- 復元性の向上:
    - Azure Backup サービスを使用して、Azure VM をバックアップすることで、データの安全性を保持します。 [詳細情報](../backup/quick-backup-vm-portal.md)。
    - Azure VM を Site Recovery のセカンダリ リージョンにレプリケートし、継続的にワークロードを実行して利用可能にします。 [詳細情報](../site-recovery/azure-to-azure-tutorial-enable-replication.md)。
- セキュリティの強化：
    - [Azure Security Center のジャスト イン タイム管理](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)を利用して、インバウンド トラフィック アクセスをロックダウンして制限します。
    - [ネットワーク セキュリティ グループ](https://docs.microsoft.com/azure/virtual-network/security-overview)を使って、ネットワーク トラフィックを管理エンドポイントに制限します。
    - [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) をデプロイして、ディスクをセキュリティ保護し、盗難や不正アクセスからデータを安全に保護します。
    - [IaaS リソースのセキュリティ保護](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/)に関する詳細を読み、[Azure Security Center](https://azure.microsoft.com/services/security-center/) を確認してください。
- 監視と管理：
-  [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) をデプロイして、リソースの使用率と消費量を監視します。


## <a name="next-steps"></a>次の手順

Azure クラウド導入フレームワークでの[クラウド移行の工程](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate)を調査します。
