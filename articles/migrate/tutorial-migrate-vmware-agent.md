---
title: エージェントベースの Azure Migrate Server Migration を使用して VMware VM を移行する
description: Azure Migrate を使用して VMware VM のエージェントベース移行を実行する方法について説明します。
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: tutorial
ms.date: 06/09/2020
ms.custom: MVC
ms.openlocfilehash: 15bf8f4fde2128181664fa7b94f2479bac7ad5b9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98881519"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>VMware VM を Azure に移行する (エージェントベース)

この記事では、[Azure Migrate:Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) ツールを使用して、エージェントベース移行でオンプレミスの VMware VM を Azure に移行する方法について説明します。  エージェントレス型移行を使用して VMware VM を移行することもできます。 方法を[比較してください](server-migrate-overview.md#compare-migration-methods)。


 このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * Azure Migrate と連携するように Azure を準備します。
> * エージェントベース移行の準備をします。 Azure Migrate で移行対象のマシンを検出できるよう VMware アカウントを設定します。 移行したいマシンに Mobility Service エージェントをインストールできるようアカウントを設定し、レプリケーション アプライアンスとしての役割を果たすマシンを準備します。
> * Azure Migrate:Server Migration ツールを追加する
> * レプリケーション アプライアンスを設定します。
> * VM をレプリケートします。
> * すべてが想定どおりに動作していることを確認するためにテスト移行を実行します。
> * Azure への完全な移行を実行します。

> [!NOTE]
> チュートリアルでは、概念実証をすばやく設定できるように、シナリオの最も簡単なデプロイ パスを示します。 チュートリアルではできるだけ既定のオプションを使用しており、使用可能な設定とパスをすべて示しているわけではありません。 

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。


## <a name="prerequisites"></a>前提条件

このチュートリアルに取り組む前に、VMware のエージェントベース移行のアーキテクチャを[確認](./agent-based-migration-architecture.md)してください。

## <a name="prepare-azure"></a>Azure を準備する

表に記載のタスクを実行して、エージェントベース移行に向けて Azure の準備を行います。

**タスク** | **詳細**
--- | ---
**Azure Migrate プロジェクトの作成** | Azure アカウントには、プロジェクトを作成するために共同作成者または所有者のアクセス許可が必要です。
**Azure アカウントのアクセス許可を確認する** | ご使用の Azure アカウントには、VM を作成するためのアクセス許可と Azure マネージド ディスクへの書き込みアクセス許可が必要です。
**Azure ネットワークを設定する** | 移行後に Azure VM が参加するネットワークを設定します。

### <a name="assign-permissions-to-create-project"></a>プロジェクトを作成するためのアクセス許可を割り当てる
Azure Migrate プロジェクトがない場合は、プロジェクトを作成するためのアクセス許可を確認します。


1. Azure portal でサブスクリプションを開き、 **[アクセス制御 (IAM)]** を選択します。
2. **[アクセスの確認]** で関連するアカウントを探し、それをクリックしてアクセス許可を表示します。
3. **共同作成者** または **所有者** のアクセス許可を持っていることを確認します。

    - 無料の Azure アカウントを作成したばかりであれば、自分のサブスクリプションの所有者になっています。
    - サブスクリプションの所有者でない場合は、所有者と協力してロールを割り当てます。
    
### <a name="assign-azure-account-permissions"></a>Azure アカウントのアクセス許可を割り当てる

仮想マシンの共同作成者ロールをアカウントに割り当てて、以下を行うためのアクセス許可を持てるようにします。

- 選択したリソース グループ内に VM を作成する。
- 選択した仮想ネットワーク内に VM を作成する。
- Azure マネージド ディスクに書き込む。 


### <a name="set-up-an-azure-network"></a>Azure ネットワークをセットアップ

[Azure ネットワークをセットアップ](../virtual-network/manage-virtual-network.md#create-a-virtual-network)します。 オンプレミスのマシンは Azure マネージド ディスクにレプリケートされます。 移行のために Azure にフェールオーバーすると、これらのマネージド ディスクから Azure VM が作成され、設定した Azure ネットワークに参加させられます。

## <a name="prepare-for-migration"></a>移行を準備する

サポートの要件とアクセス許可を確認し、レプリケーション アプライアンスをデプロイするための準備を行います。 

### <a name="prepare-an-account-to-discover-vms"></a>VM を検出するためのアカウントを準備する

Azure Migrate Server Migration は、移行対象の VM を検出するために、VMware サーバーへのアクセスを必要とします。 次の手順に従って、このアカウントを作成します。

1. 専用のアカウントを使用するには、vCenter レベルでロールを作成します。 そのロールに名前を付けます (**Azure_Migrate** など)。
2. 次の表にまとめられているアクセス許可をそのロールに割り当てます。
3. vCenter サーバーまたは vSphere ホストにユーザーを作成します。 ユーザーにロールを割り当てます。

#### <a name="vmware-account-permissions"></a>VMware アカウントのアクセス許可

**タスク** | **ロールとアクセス許可** | **詳細**
--- | --- | ---
**VM 検出** | 読み取り専用以上の権限を持つユーザー<br/><br/> データ センター オブジェクト -> 子オブジェクトへのプロパゲート、ロール=読み取り専用 | ユーザーはデータセンター レベルで割り当てられ、データセンター内のすべてのオブジェクトに対してアクセス権を持ちます。<br/><br/> アクセスを制限するには、**子オブジェクトへの伝達** 特権を持つ **アクセスなし** ロールを子オブジェクト (vSphere ホスト、データストア、VM、ネットワーク) に割り当てます。
**レプリケーション** |  必要なアクセス許可を備えたロール (Azure_Site_Recovery) を作成し、このロールを VMware のユーザーまたはグループに割り当てる<br/><br/> データ センター オブジェクト –> 子オブジェクトへの伝達、ロール = Azure_Site_Recovery<br/><br/> データストア -> 空間の割り当て、データストアの参照、ファイルの低レベルの操作、ファイルの削除、仮想マシン ファイルの更新<br/><br/> ネットワーク -> ネットワークの割り当て<br/><br/> リソース -> リソース プールへの VM の割り当て、電源がオフの VM の移行、電源がオンの VM の移行<br/><br/> タスク -> タスクの作成、タスクの更新<br/><br/> 仮想マシン -> 構成<br/><br/> 仮想マシン -> 対話 -> 質問への回答、デバイス接続、CD メディアの構成、フロッピー メディアの構成、電源オフ、電源オン、VMware ツールのインストール<br/><br/> 仮想マシン -> インベントリ -> 作成、登録、登録解除<br/><br/> 仮想マシン -> プロビジョニング -> 仮想マシンのダウンロードの許可、仮想マシン ファイルのアップロードの許可<br/><br/> 仮想マシン -> スナップショット -> スナップショットの削除 | ユーザーはデータセンター レベルで割り当てられ、データセンター内のすべてのオブジェクトに対してアクセス権を持ちます。<br/><br/> アクセスを制限するには、**子オブジェクトへの伝達** 特権を持つ **アクセスなし** ロールを子オブジェクト (vSphere ホスト、データストア、VM、ネットワーク) に割り当てます。

### <a name="prepare-an-account-for-mobility-service-installation"></a>モビリティ サービスのインストール用のアカウントを準備する

モビリティ サービスは、レプリケートするマシンにインストールする必要があります。

- マシンのレプリケーションを有効にしている場合は、Azure Migrate レプリケーション アプライアンスでこのサービスをプッシュ インストールできます。または、手動でインストールしたり、インストール ツールを使用したりできます。
- このチュートリアルでは、プッシュ インストールでモビリティ サービスをインストールします。
- プッシュ インストールの場合は、Azure Migrate Server Migration で VM へのアクセスに使用できるアカウントを準備する必要があります。 モビリティ サービスを手動でインストールしない場合、このアカウントはプッシュ インストールにのみ使用されます。

アカウントを次のように準備します。

1. VM にインストールするアクセス許可を持つドメイン アカウントまたはローカル アカウントを準備します。
2. Windows VM の場合、ドメイン アカウントを使用していないときはローカル コンピューターでリモート ユーザーのアクセス制御を無効にします。レジストリで、**HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** の下に DWORD エントリ **LocalAccountTokenFilterPolicy** を追加し、値を設定します。
3. Linux VM の場合、ソースの Linux サーバーにルート アカウントを準備します。


### <a name="prepare-a-machine-for-the-replication-appliance"></a>レプリケーション アプライアンス用のマシンの準備

このアプライアンスは、Azure にマシンをレプリケートする目的で使用されます。 高可用性の単一のオンプレミス VMware VM となっており、次のコンポーネントがホストされます。

- **構成サーバー**: 構成サーバーは、オンプレミスと Azure の間の通信を調整し、データのレプリケーションを管理します。
- **プロセス サーバー**:プロセス サーバーはレプリケーション ゲートウェイとして機能します。 レプリケーション データを受信し、それをキャッシュ、圧縮、暗号化によって最適化して、Azure のキャッシュ ストレージ アカウントに送信します。 また、プロセス サーバーでは、レプリケートする VM へのモビリティ サービス エージェントのインストールや、オンプレミスの VMware VM の自動検出も行います。

次のようにアプライアンスの準備をします。

- [アプライアンスの要件を確認します](migrate-replication-appliance.md#appliance-requirements)。 一般に、レプリケーション アプライアンスは、ダウンロードした OVA ファイルを使用し、VMware VM として設定します。 すべての要件を満たしたアプライアンスがテンプレートによって作成されます。
- アプライアンスに MySQL がインストールされている必要があります。 インストール方法を[確認](migrate-replication-appliance.md#mysql-installation)してください。
- アプライアンス マシンからアクセスする必要のある[パブリック クラウドの URL](migrate-replication-appliance.md#url-access) と [Azure Government の URL ](migrate-replication-appliance.md#azure-government-url-access) を確認します。
- レプリケーション アプライアンス マシンからアクセスする必要のある[ポートを確認](migrate-replication-appliance.md#port-access)します。



### <a name="check-vmware-requirements"></a>VMware の要件を確認する

VMware サーバーと VM が、Azure への移行の要件に準拠していることを確認します。 

1. VMware サーバーの要件を[確認](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based)します。
2. 移行のための VM の要件を[確認](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based)します。
3. Azure の設定を確認します。 Azure にレプリケートするオンプレミスの VM は、「[Azure VM の要件](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)」に準拠している必要があります。
4. Azure に VM を移行する前に、それらに対していくつかの変更を行う必要があります。
    - 移行を開始する前にこれらの変更を行うことが重要です。 変更を行う前に VM を移行すると、Azure で VM が起動しない可能性があります。
    - [Windows](prepare-for-migration.md#windows-machines) と [Linux](prepare-for-migration.md#linux-machines) で必要な変更を確認してください。

> [!NOTE]
> Azure Migrate Server Migration を使用したエージェントベースの移行は、Azure Site Recovery サービスの機能に基づいています。 一部の要件は、Site Recovery のドキュメントにリンクされている場合があります。

## <a name="set-up-the-replication-appliance"></a>レプリケーション アプライアンスを設定する

この手順では、ダウンロードした Open Virtualization Application (OVA) テンプレートを使用してアプライアンスを設定する方法について説明します。 この方法が使用できない場合は、[スクリプトを使用](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance)してアプライアンスを設定してください。 

### <a name="download-the-replication-appliance-template"></a>レプリケーション アプライアンスのテンプレートをダウンロードする

次のとおりにテンプレートをダウンロードします。

1. Azure Migrate プロジェクトで、 **[移行の目標]** の **[サーバー]** をクリックします。
2. **[Azure Migrate - サーバー]**  >  **[Azure Migrate: Server Migration]** で、 **[検出]** をクリックします。

    ![Discover VMs](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. **[マシンの検出]**  >  **[マシンは仮想化されていますか?]** で、 **[はい。VMware vSphere Hypervisor を使用します]** をクリックします。
4. **[移行方法]** で **[エージェントベースのレプリケーションを使用する]** を選択します。
5. **[ターゲット リージョン]** で、マシンの移行先にする Azure リージョンを選択します。
6. **[移行先のリージョンが <リージョン名> であることを確認してください]** を選択します。
7. **[リソースの作成]** をクリックします。 これで、Azure Site Recovery コンテナーがバックグラウンドで作成されます。 このボタンのクリック後は、このプロジェクトのターゲット リージョンを変更することはできません。後続の移行はすべて、このリージョンに対して行われます。

    ![Recovery Services コンテナーの作成](./media/tutorial-migrate-vmware-agent/create-resources.png)

8. **[新しいレプリケーション アプライアンスをインストールしますか?]** で、 **[レプリケーション アプライアンスのインストール]** を選択します。
9. **[Download]** をクリックします。 これで OVF テンプレートがダウンロードされます。
    ![OVA のダウンロード](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. リソース グループと Recovery Services コンテナーの名前をメモしてください。 これらはアプライアンスのデプロイ時に必要です。


### <a name="import-the-template-in-vmware"></a>VMware にテンプレートをインポートする

OVF テンプレートのダウンロード後、それを VMware にインポートして、Windows Server 2016 を稼働させている VMware VM 上にレプリケーション アプリケーションを作成します。

1. VMware vSphere Client を使用して、VMware vCenter サーバーまたは vSphere ESXi ホストにサインインします。
2. **[File]\(ファイル\)** メニューの **[Deploy OVF Template]\(OVF テンプレートのデプロイ\)** を選択し、 **[Deploy OVF Template]\(OVF テンプレートのデプロイ\)** ウィザードを起動します。 
3. **[Select source]\(ソースの選択\)** で、ダウンロードした OVF の場所を入力します。
4. **[Review details]\(詳細の確認\)** で、 **[Next]\(次へ\)** を選択します。
5. **[Select name and folder]\(名前とフォルダーの選択\)** および **[Select configuration]\(構成の選択\)** は、既定の設定のままにします。
6. パフォーマンスを最大にするために、 **[Select storage]\(ストレージの選択\)**  >  **[Select virtual disk format]\(仮想ディスクの形式の選択\)** で、 **[Thick Provision Eager Zeroed]\(シック プロビジョニング Eager Zeroed\)** を選択します。
7. ウィザードの残りのページでは、既定の設定をそのまま使います。
8. **[Ready to complete]\(完了の準備完了\)** で、既定の設定で VM をセットアップするには、 **[Power on after deployment]\(デプロイ後に電源をオンにする\)**  >  **[Finish]\(完了\)** の順に選択します。

   > [!TIP]
   > 別の NIC を追加する場合は、 **[Power on after deployment]\(デプロイ後に電源をオンにする\)** をオフにする >  **[Finish]\(完了\)** を選択する、と操作します。 既定では、テンプレートには NIC が 1 つ含まれています。 デプロイ後にさらに NIC を追加することができます。

### <a name="start-appliance-setup"></a>アプライアンスの設定を開始する

1. VMware vSphere Client のコンソールで、VM をオンにします。 VM が Windows Server 2016 のインストール エクスペリエンスで起動します。
2. 使用許諾契約書に同意し、管理者パスワードを入力します。
3. インストールの完了後、管理者パスワードを使用して、管理者として VM にサインインします。 初めてサインインすると、数秒以内にレプリケーション アプライアンス設定ツール (Azure Site Recovery 構成ツール) が起動します。
5. Server Migration へのアプライアンスの登録に使用する名前を入力します。 続けて、 **[次へ]** をクリックします。
6. このツールは、VM が Azure に接続できることを確認します。 接続が確立された後、 **[サインイン]** を選択して、自分の Azure サブスクリプションにサインインします。
7. ツールによる Azure AD アプリの登録が完了し、アプライアンスが識別されるのを待ちます。 アプライアンスが再起動します。
1. 再度マシンにサインインします。 数秒後に、構成サーバーの管理ウィザードが自動的に起動します。

### <a name="register-the-replication-appliance"></a>レプリケーション アプライアンスを登録する

レプリケーション アプライアンスの設定と登録を完了します。

1. アプライアンスの設定で、 **[接続の設定]** を選択します。
2. VM を検出するためとソース マシンに対するモビリティ サービスのプッシュ インストールを実行するためにレプリケーション アプライアンスで使用される NIC を選択します (既定では 1 つの NIC しかありません)。
3. Azure と接続するためにレプリケーション アプライアンスによって使用される NIC を選択します。 次に、 **[保存]** を選択します。 構成後、この設定を変更することはできません。
4. アプライアンスがプロキシ サーバーの内側に配置されている場合は、プロキシ設定を指定する必要があります。
    - プロキシ名は、 **http://ip-address** または **http://FQDN** と指定します。 HTTPS プロキシ サーバーはサポートされていません。
5. サブスクリプション、リソース グループ、コンテナーの詳細を求められたら、アプライアンスのテンプレートをダウンロードしたときにメモした詳細を追加してください。
6. **[サードパーティ製ソフトウェアのインストール]** でライセンス契約に同意します。 **[ダウンロードしてインストール]** を選択して MySQL サーバーをインストールします。
7. **[VMware PowerCLI のインストール]** を選択します。 この操作を行う前に、すべてのブラウザー ウィンドウを閉じてください。 その後 **[続行]** を選択します。
8. **[アプライアンス構成の検証]** で、続行する前に前提条件が検証されます。
9. **[Configure vCenter Server/vSphere ESXi server]\(vCenter Server/vSphere ESXi サーバーの構成\)** で、レプリケートする VM が存在している vCenter サーバーまたは vSphere ホストの FQDN または IP アドレスを入力します。 サーバーがリッスンするポートを入力します。 コンテナーで VMware サーバーに使うフレンドリ名を入力します。
10. VMware の検出用に[作成](#prepare-an-account-to-discover-vms)したアカウントの資格情報を入力します。 **[追加]**  >  **[続行]** を選択します。
11. VM のレプリケーションを有効にした際のモビリティ サービスのプッシュ インストール用に [作成](#prepare-an-account-for-mobility-service-installation)した資格情報を、 **[仮想マシンの資格情報の構成]** に入力します。  
    - Windows マシンの場合、このアカウントは、レプリケートするマシンに対するローカル管理者特権を持っている必要があります。
    - Linux の場合は、ルート アカウントの詳細を指定します。
12. **[構成の確定]** を選択して、登録を完了します。


レプリケーション アプライアンスが登録された後、指定の設定を使用して Azure Migrate Server Assessment が VMware サーバーに接続され、VM が検出されます。 検出された VM は、 **[その他]** タブの **[管理]**  >  **[項目が検出されました]** で確認できます。



## <a name="replicate-vms"></a>VM をレプリケートする

移行の対象となる VM を選択します。

> [!NOTE]
> ポータルでは、レプリケーションのために一度に最大 10 台のマシンを選択できます。 レプリケートするマシンがそれより多い場合は、10 台をひとまとまりとしてグループ化してください。

1. Azure Migrate プロジェクトの **[サーバー]** の **[Azure Migrate: Server Migration]** で、 **[レプリケート]** をクリックします。

    ![Azure Migrate の [サーバー] 画面のスクリーンショット。 Azure Migrate で [レプリケート] ボタンが選択されています。移行ツールでのサーバーの移行。](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. **[レプリケート]** > **[ソースの設定]**  >  **[マシンは仮想化されていますか?]** で、 **[はい (VMware vSphere の場合)]** を選択します。
3. **[オンプレミスのアプライアンス]** で、自分が設定した Azure Migrate アプライアンスの名前を選択します。
4. **[vCenter Server]** で、VM を管理している vCenter サーバー、または VM がホストされている vSphere サーバーの名前を指定します。
5. **[プロセス サーバー]** で、レプリケーション アプライアンスの名前を選択します。
6. **[ゲストの資格情報]** で、モビリティ サービスのプッシュ インストールに使用される VM 管理アカウントを指定します。 その後、 **[次へ:仮想マシン]** をクリックします。

    ![[レプリケート] 画面の [ソース設定] タブのスクリーンショット。 [ゲストの資格情報] フィールドが強調表示され、値に "VM-admin-account" が設定されます。](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. **[仮想マシン]** で、レプリケートするマシンを選択します。

    - VM の評価を実行した場合は、評価結果から VM のサイズ設定とディスクの種類 (Premium または Standard) の推奨事項を適用できます。 これを行うには、 **[Azure Migrate Assessment から移行設定をインポートしますか?]** で **[はい]** オプションを選択します。
    - 評価を実行しなかった場合、または評価の設定を使用しない場合は、 **[いいえ]** オプションを選択します。
    - 評価の使用を選択した場合は、VM グループと評価名を選択します。
8. **[可用性オプション]** で、以下を選択します。
    -  可用性ゾーン。移行されたマシンをリージョン内の特定の可用性ゾーンにピン留めします。 このオプションを使用して、複数ノードのアプリケーション層を形成するサーバーを可用性ゾーン間で分散させます。 このオプションを選択した場合は、[コンピューティング] タブで選択した各マシンに使用する可用性ゾーンを指定する必要があります。このオプションは、移行用に選択したターゲット リージョンで Availability Zones がサポートされている場合にのみ使用できます。
    -  可用性セット。移行されたマシンを可用性セットに配置します。 このオプションを使用するには、選択されたターゲット リソース グループに 1 つ以上の可用性セットが必要です。
    - [インフラストラクチャ冗長は必要ありません] オプション (移行されたマシンに対してこれらの可用性構成がいずれも不要な場合)。
9. 移行したい各 VM を確認します。 その後、 **[次へ:ターゲット設定]** をクリックします。
10. **[ターゲット設定]** で、サブスクリプションと、移行先となるターゲット リージョンを選択し、移行後に Azure VM が配置されるリソース グループを指定します。
11. **[仮想ネットワーク]** で、移行後に Azure VM の参加先となる Azure VNet およびサブネットを選択します。
12. **[可用性オプション]** で、以下を選択します。
    -  可用性ゾーン。移行されたマシンをリージョン内の特定の可用性ゾーンにピン留めします。 このオプションを使用して、複数ノードのアプリケーション層を形成するサーバーを可用性ゾーン間で分散させます。 このオプションを選択した場合は、[コンピューティング] タブで選択した各マシンに使用する可用性ゾーンを指定する必要があります。このオプションは、移行用に選択したターゲット リージョンで Availability Zones がサポートされている場合にのみ使用できます。
    -  可用性セット。移行されたマシンを可用性セットに配置します。 このオプションを使用するには、選択されたターゲット リソース グループに 1 つ以上の可用性セットが必要です。
    - [インフラストラクチャ冗長は必要ありません] オプション (移行されたマシンに対してこれらの可用性構成がいずれも不要な場合)。
13. **[Disk encryption type]\(ディスク暗号化の種類\)** で、以下を選択します。
    - プラットフォーム マネージド キーを使用した保存時の暗号化
    - カスタマー マネージド キーを使用した保存時の暗号化
    - プラットフォーム マネージド キーとカスタマー マネージド キーを使用した二重暗号化

   > [!NOTE]
   > CMK を使用して VM をレプリケートするには、ターゲット リソース グループに[ディスク暗号化セットを作成する](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set)必要があります。 ディスク暗号化セット オブジェクトによって、SSE に使用する CMK を含む Key Vault にマネージド ディスクがマップされます。
  
14. **[Azure ハイブリッド特典]** で、

    - Azure ハイブリッド特典を適用しない場合は、 **[いいえ]** を選択します。 続けて、 **[次へ]** をクリックします。
    - アクティブなソフトウェア アシュアランスまたは Windows Server のサブスクリプションの対象となっている Windows Server マシンがあり、移行中のマシンにその特典を適用する場合は、 **[はい]** を選択します。 続けて、 **[次へ]** をクリックします。

    ![ターゲットの設定](./media/tutorial-migrate-vmware/target-settings.png)

15. **[コンピューティング]** で、VM の名前、サイズ、OS ディスクの種類、および可用性構成 (前の手順で選択した場合) を確認します。 VM は [Azure の要件](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)に準拠している必要があります。

   - **VM サイズ**: 評価の推奨事項を使用している場合は、[VM サイズ] ドロップダウンに推奨サイズが表示されます。 それ以外の場合は、Azure Migrate によって、Azure サブスクリプション内の最も近いサイズが選択されます。 または、 **[Azure VM サイズ]** でサイズを手動で選択します。 
    - **OS ディスク**:VM の OS (ブート) ディスクを指定します。 OS ディスクは、オペレーティング システムのブートローダーとインストーラーがあるディスクです。 
    - **可用性ゾーン**:使用する可用性ゾーンを指定します。
    - **可用性セット**:使用する可用性セットを指定します。

16. **[ディスク]** で、VM ディスクを Azure にレプリケートするかどうかを指定し、Azure でのディスクの種類 (Standard SSD か HDD、または Premium マネージド ディスク) を選択します。 続けて、 **[次へ]** をクリックします。
    - レプリケーションからディスクを除外できます。
    - ディスクは除外すると、移行後に Azure VM 上に存在しなくなります。 

17. **[レプリケーションの確認と開始]** で、設定を確認し、 **[レプリケート]** をクリックして、サーバーの初期レプリケーションを開始します。

> [!NOTE]
> レプリケーションが開始される前であれば、 **[管理]**  >  **[マシンのレプリケート]** でレプリケーションの設定をいつでも更新できます。 レプリケーションの開始後は、設定を変更することができません。


## <a name="track-and-monitor"></a>追跡して監視する

1. ポータルの通知で、ジョブの状態を追跡します。 

    ![ジョブの追跡](./media/tutorial-migrate-vmware-agent/jobs.png)
    
2. レプリケーションの状態を監視するには、**サーバーをレプリケートしています** をクリックします (**Azure Migrate:Server Migration** 内)。

    ![レプリケーションを監視します](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

レプリケーションは、次のように行われます。
- レプリケーションの開始ジョブが正常に終了すると、マシンで Azure への初期レプリケーションが開始されます。
- 初期レプリケーションが完了すると、差分レプリケーションが開始されます。 オンプレミスのディスクに対する増分変更は、Azure のレプリカ ディスクに定期的にレプリケートされます。


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
4. **テスト移行** ジョブが開始されます。 ポータルの通知でジョブを監視します。
5. 移行の完了後、Azure portal の **[仮想マシン]** で、移行された Azure VM を確認します。 マシン名には、サフィックス **-Test** が含まれています。
6. テストが完了したら、 **[マシンのレプリケート]** で Azure VM を右クリックし、 **[テスト移行をクリーンアップ]** をクリックします。

    ![移行のクリーンアップ](./media/tutorial-migrate-vmware-agent/clean-up.png)


## <a name="migrate-vms"></a>VM の移行

テスト移行が想定どおりに動作することを確認したら、オンプレミスのマシンを移行できます。

1. Azure Migrate プロジェクトの **[サーバー]**  >  **[Azure Migrate: Server Migration]** で、 **[サーバーをレプリケートしています]** をクリックします。

    ![サーバーをレプリケートしています](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

2. **[マシンのレプリケート]** で VM を右クリックし、 **[移行]** を選択します。
3. **[移行]**  >  **[仮想マシンをシャットダウンし、データ損失のない計画された移行を実行しますか]** で、 **[はい]**  >  **[OK]** の順に選択します。
    - データの損失を最小限にするため、既定では、オンプレミスの VM が Azure Migrate によってシャットダウンされます。 
    - VM をシャットダウンしたくない場合は、 **[いいえ]** を選択します
4. VM に対して移行ジョブが開始されます。 Azure 通知でジョブを追跡します。
5. ジョブが完了したら、 **[仮想マシン]** ページで VM を表示して管理できます。

## <a name="complete-the-migration"></a>移行を完了する

1. 移行が完了したら、VM を右クリックして、 **[移行を停止する]** を選択します。 次の処理が実行されます。
    - オンプレミス マシンのレプリケーションを停止します。
    - Azure Migrate: Server Migration の **[サーバーをレプリケートしています]** のカウントからマシンを削除します。Server Migration に関するエラーのトラブルシューティングに役立つ情報を提供しています。
    - VM のレプリケーション状態情報をクリーンアップします。
2. Azure VM の [Windows](../virtual-machines/extensions/agent-windows.md) または [Linux](../virtual-machines/extensions/agent-linux.md) エージェントを、移行されたマシンにインストールします。
3. データベース接続文字列、および Web サーバー構成の更新など、移行後のアプリの微調整を実行します。
4. Azure で現在実行されている移行後のアプリケーション上で、最終的なアプリケーションと移行の受け入れのテストを実行します。
5. 移行された Azure VM インスタンスにトラフィックを切り替えます。
6. ローカル VM インベントリからオンプレミスの VM を削除します。
7. ローカル バックアップからオンプレミスの VM を削除します。
8. Azure VM の新しい場所と IP アドレスを示すように内部ドキュメントを更新します。 

## <a name="post-migration-best-practices"></a>移行後のベスト プラクティス

- オンプレミス
    - 移行後の Azure VM インスタンスで実行するアプリに、アプリ トラフィックを移動します。
    - ローカル VM インベントリからオンプレミスの VM を削除します。
    - ローカル バックアップからオンプレミスの VM を削除します。
    - Azure VM の新しい場所と IP アドレスを示すように内部ドキュメントを更新します。
- 移行後の Azure VM の設定の調整:
    - [Azure VM エージェント](../virtual-machines/extensions/agent-windows.md)では、Azure ファブリック コントローラーと VM の対話を管理します。 Azure Backup、Site Recovery、および Azure Security など、一部の Azure サービスでは必須です。 VMare VM をエージェントベースの移行で移行する場合、モビリティ サービスのインストーラーによって Windows マシンに Azure VM エージェントがインストールされます。 Linux VM 上で、以降後にエージェントをインストールすることをお勧めします。
    - 移行後に、Azure VM からモビリティ サービスを手動でアンインストールします。
    - 移行後に、VMware ツールを手動でアンインストールします。
- Azure の場合:
    - データベース接続文字列、および Web サーバー構成の更新など、移行後のアプリの微調整を実行します。
    - Azure で現在実行されている移行後のアプリケーション上で、最終的なアプリケーションと移行の受け入れのテストを実行します。
- ビジネス継続性/ディザスター リカバリー
    - Azure Backup サービスを使用して、Azure VM をバックアップすることで、データの安全性を保持します。 [詳細については、こちらを参照してください](../backup/quick-backup-vm-portal.md)。
    - Azure VM を Site Recovery のセカンダリ リージョンにレプリケートし、継続的にワークロードを実行して利用可能にします。 [詳細については、こちらを参照してください](../site-recovery/azure-to-azure-tutorial-enable-replication.md)。
- セキュリティの強化：
    - [Azure Security Center のジャスト イン タイム管理](../security-center/security-center-just-in-time.md)を利用して、インバウンド トラフィック アクセスをロックダウンして制限します。
    - [ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md)を使って、ネットワーク トラフィックを管理エンドポイントに制限します。
    - [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) をデプロイして、ディスクをセキュリティ保護し、盗難や不正アクセスからデータを安全に保護します。
    - [IaaS リソースのセキュリティ保護](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/)に関する詳細を読み、[Azure Security Center](https://azure.microsoft.com/services/security-center/) を確認してください。
- 監視と管理：
    - [Azure Cost Management](../cost-management-billing/cloudyn/overview.md) をデプロイして、リソースの使用率と消費量を監視します。




 ## <a name="next-steps"></a>次のステップ

Azure クラウド導入フレームワークでの[クラウド移行の工程](/azure/architecture/cloud-adoption/getting-started/migrate)を調査します。