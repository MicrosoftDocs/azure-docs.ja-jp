---
title: Azure Migrate を使用してマシンを物理サーバーとして Azure に移行する
description: この記事では、Azure Migrate を使用して、物理マシンを Azure に移行する方法について説明します。
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: tutorial
ms.date: 01/02/2021
ms.custom: MVC
ms.openlocfilehash: aeeb810174ff5c21a81bcec8aa9265ff100edf91
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99626327"
---
# <a name="migrate-machines-as-physical-servers-to-azure"></a>マシンを物理サーバーとして Azure に移行する

この記事では、Azure Migrate: Server Migration ツールを使用して、マシンを物理サーバーとして Azure に移行する方法について説明します。サーバー移行ツール。 マシンを物理サーバーとして扱うことによって移行することは、さまざまなシナリオで役立ちます。

- オンプレミスの物理サーバーを移行する。
- Xen、KVM などのプラットフォームで仮想化された VM を移行する。
- Hyper-V または VMware VM を移行する (なんらかの理由で、[Hyper-V](tutorial-migrate-hyper-v.md) の標準移行プロセスや [VMware ](server-migrate-overview.md) 移行を使用できない場合)。
- プライベート クラウドで実行されている VM を移行する。
- アマゾン ウェブ サービス (AWS) や Google Cloud Platform (GCP) などのパブリック クラウドで実行されている VM を移行する。


これは、物理サーバーを評価して Azure に移行する方法を示すシリーズの 3 番目のチュートリアルです。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure Migrate: Server Migration で Azure を使用するための準備を行います。Server Migration に関するエラーのトラブルシューティングに役立つ情報を提供しています。
> * 移行したいマシンの要件を確認し、マシンの検出と Azure への移行に使用される Azure Migrate レプリケーション アプライアンス用のマシンを準備します。
> * Azure Migrate:Server Migration ツールを Azure Migrate ハブに追加します。
> * レプリケーション アプライアンスを設定します。
> * 移行したいマシンにモビリティ サービスをインストールします。
> * レプリケーションを有効にします。
> * すべてが想定どおりに動作していることを確認するためにテスト移行を実行します。
> * Azure への完全な移行を実行します。

> [!NOTE]
> チュートリアルでは、概念実証をすばやく設定できるように、シナリオの最も簡単なデプロイ パスを示します。 チュートリアルではできるだけ既定のオプションを使用しており、使用可能な設定とパスをすべて示しているわけではありません。 詳細な手順については、Azure Migrate のハウツーを参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。


## <a name="prerequisites"></a>前提条件

このチュートリアルを始める前に、次の準備が必要です。

移行のアーキテクチャを[確認](./agent-based-migration-architecture.md)します。

## <a name="prepare-azure"></a>Azure を準備する

Azure Migrate: Server Migration ツールを使用した移行のために Azure を準備します。Server Migration に関するエラーのトラブルシューティングに役立つ情報を提供しています。

**タスク** | **詳細**
--- | ---
**Azure Migrate プロジェクトの作成** | Azure アカウントには、[新規プロジェクトを作成する](./create-manage-projects.md)ために共同作成者または所有者のアクセス許可が必要です。
**Azure アカウントのアクセス許可の確認** | ご使用の Azure アカウントには、VM を作成するためのアクセス許可と Azure マネージド ディスクへの書き込みアクセス許可が必要です。


### <a name="assign-permissions-to-create-project"></a>プロジェクトを作成するためのアクセス許可を割り当てる

1. Azure portal でサブスクリプションを開き、 **[アクセス制御 (IAM)]** を選択します。
2. **[アクセスの確認]** で関連するアカウントを探し、それをクリックしてアクセス許可を表示します。
3. **共同作成者** または **所有者** のアクセス許可を持っている必要があります。
    - 無料の Azure アカウントを作成したばかりであれば、自分のサブスクリプションの所有者になっています。
    - サブスクリプションの所有者でない場合は、所有者と協力してロールを割り当てます。


### <a name="assign-azure-account-permissions"></a>Azure アカウントのアクセス許可を割り当てる

仮想マシン共同作成者ロールを Azure アカウントに割り当てます。 これで、次の作業を行うためのアクセス許可が得られます。

- 選択したリソース グループ内に VM を作成する。
- 選択した仮想ネットワーク内に VM を作成する。
- Azure マネージド ディスクに書き込む。 

### <a name="create-an-azure-network"></a>Azure ネットワークを作成する

Azure 仮想ネットワーク (VNet) を[設定](../virtual-network/manage-virtual-network.md#create-a-virtual-network)します。 Azure にレプリケートすると、Azure VM が作成され、移行の設定時に指定した Azure VNet に参加させられます。

## <a name="prepare-for-migration"></a>移行を準備する

物理サーバーの移行を準備するには、物理サーバーの設定を確認し、レプリケーション アプライアンスをデプロイする準備をする必要があります。

### <a name="check-machine-requirements-for-migration"></a>移行のためのマシン要件の確認

マシンが Azure に移行するための要件を満たしていることを確認します。 

> [!NOTE]
> 物理マシンを移行するとき、Azure Migrate: Server Migration では、Azure Site Recovery サービスのエージェントベースのディザスター リカバリーと同じレプリケーション アーキテクチャが使用されており、一部のコンポーネントでは、同じコード ベースが共有されています。 一部のコンテンツは、Site Recovery のドキュメントにリンクされている場合があります。

1. 物理サーバーの要件を[確認](migrate-support-matrix-physical-migration.md#physical-server-requirements)します。
2. Azure にレプリケートするオンプレミスのマシンが、[Azure VM の要件](migrate-support-matrix-physical-migration.md#azure-vm-requirements)に準拠していることを確認します。
3. VM を Azure に移行する前に、いくつかの変更を行う必要があります。
    - 一部のオペレーティング システムでは、これらの変更が Azure Migrate によって自動的に行われます。 
    - 移行を開始する前にこれらの変更を行うことが重要です。 変更を行う前に VM を移行すると、Azure で VM が起動しない可能性があります。 [Windows](prepare-for-migration.md#windows-machines) と [Linux](prepare-for-migration.md#linux-machines) で必要な変更を確認してください。

### <a name="prepare-a-machine-for-the-replication-appliance"></a>レプリケーション アプライアンス用のマシンの準備

Azure Migrate: Server Migration では、レプリケーション アプライアンスを使用してマシンを Azure にレプリケートします。 レプリケーション アプライアンスは、次のコンポーネントを実行します。

- **構成サーバー**: 構成サーバーは、オンプレミスと Azure の間の通信を調整し、データのレプリケーションを管理します。
- **プロセス サーバー**:プロセス サーバーはレプリケーション ゲートウェイとして機能します。 レプリケーション データを受信し、それをキャッシュ、圧縮、暗号化によって最適化して、Azure のキャッシュ ストレージ アカウントに送信します。 

次のようにして、アプライアンスのデプロイの準備をします。

- レプリケーション アプライアンスのホストとなるマシンを準備します。 マシンの要件を[確認](migrate-replication-appliance.md#appliance-requirements)します。
- レプリケーション アプライアンスでは MySQL が使用されます。 アプライアンスに MySQL をインストールするためのいくつかの[方法](migrate-replication-appliance.md#mysql-installation)を確認します。
- [パブリック](migrate-replication-appliance.md#url-access) クラウドおよび[政府機関向け](migrate-replication-appliance.md#azure-government-url-access)クラウドにアクセスするレプリケーション アプライアンスに必要な Azure URL を確認します。
- レプリケーション アプライアンスの[ポート](migrate-replication-appliance.md#port-access) アクセス要件を確認します。

> [!NOTE]
> レプリケート対象のソース マシン、または以前にインストールした Azure Migrate 検出および評価アプライアンスに、レプリケーション アプライアンスをインストールすることはできません。

## <a name="set-up-the-replication-appliance"></a>レプリケーション アプライアンスを設定する

移行の最初の手順は、レプリケーション アプライアンスを設定することです。 物理サーバーの移行に使用するアプライアンスを設定するには、アプライアンスのインストーラー ファイルをダウンロードし、[準備しておいたマシン](#prepare-a-machine-for-the-replication-appliance)上でインストーラー ファイルを実行します。 アプライアンスをインストールしたら、Azure Migrate: Server Migration に登録します。Server Migration に関するエラーのトラブルシューティングに役立つ情報を提供しています。


### <a name="download-the-replication-appliance-installer"></a>レプリケーション アプライアンス インストーラーのダウンロード

1. Azure Migrate プロジェクトの **[サーバー]** で、 **[Azure Migrate: Server Migration]** で、 **[検出]** をクリックします。

    ![Discover VMs](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

3. **[マシンの検出]**  >  **[マシンは仮想化されていますか?]** で、 **[非仮想化/その他]** をクリックします。
4. **[ターゲット リージョン]** で、マシンの移行先にする Azure リージョンを選択します。
5. **[移行先のリージョンが <リージョン名> であることを確認してください]** を選択します。
6. **[リソースの作成]** をクリックします。 これで、Azure Site Recovery コンテナーがバックグラウンドで作成されます。
    - Azure Migrate: Server Migration を使用した移行を既に設定してある場合は、リソースが以前に設定されているため、ターゲット オプションを構成できません。    
    - このボタンのクリック後は、このプロジェクトのターゲット リージョンを変更することはできません。
    - 後続のすべての移行は、このリージョンに対して行われます。

7. **[新しいレプリケーション アプライアンスをインストールしますか?]** で、 **[レプリケーション アプライアンスのインストール]** を選択します。
9. **[レプリケーション アプライアンス ソフトウェアをダウンロードしてインストールする]** で、アプライアンスのインストーラーと登録キーをダウンロードします。 このキーは、アプライアンスを登録するために必要です。 キーは、ダウンロード後 5 日間有効です。

    ![プロバイダーのダウンロード](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

10. アプライアンスの設定ファイルとキー ファイルを、アプライアンス用に作成した Windows Server 2016 マシンにコピーします。
11. インストールが完了すると、アプライアンス構成ウィザードが自動的に起動します (アプライアンスのデスクトップに作成された cspsconfigtool ショートカットを使用して、ウィザードを手動で起動することもできます)。 ウィザードの [アカウントの管理] タブを使用して、モビリティ サービスのプッシュ インストールに使用するアカウントの詳細を追加します。 このチュートリアルでは、レプリケートされるソース VM にMobility Service を手動でインストールします。そのため、この手順でダミー アカウントを作成し、続行します。 ダミー アカウントを作成するための詳細情報には、"ゲスト" としてフレンドリ名、"ユーザー名" としてユーザー名、"パスワード" としてアカウントのパスワードを指定できます。 このダミー アカウントは、レプリケーションの有効化ステージで使用します。 

12. アプライアンスが設定後に再起動されたら、 **[マシンの検出]** の **[構成サーバーの選択]** で新しいアプライアンスを選択し、 **[Finalize registration]\(登録の終了処理\)** をクリックします。 登録の終了処理では、レプリケーション アプライアンスを準備するための終了タスクがいくつか実行されます。

    ![登録の終了処理](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

登録の終了処理後、検出されたマシンが Azure Migrate: Server Migration に表示されるまでに、いくらか時間がかかることがあります。Server Migration に関するエラーのトラブルシューティングに役立つ情報を提供しています。 VM が検出されると、 **[検出済みサーバー]** の数が増えます。

![検出済みサーバー](./media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)


## <a name="install-the-mobility-service"></a>モビリティ サービスをインストールする

移行したいマシンにモビリティ サービス エージェントをインストールする必要があります。 エージェント インストーラーは、レプリケーション アプライアンス上で使用できます。 適切なインストーラーを見つけて、移行したい各マシンにエージェントをインストールします。 これを行うには、次の手順を実行します。

1. レプリケーション アプライアンスにサインインします。
2. **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository** に移動します。
3. マシンのオペレーティング システムとバージョンに合ったインストーラーを見つけます。 [サポートされているオペレーティング システム](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines)を確認してください。 
4. 移行したいマシンにインストーラー ファイルをコピーします。
5. アプライアンスをデプロイしたときに生成されたパスフレーズを持っていることを確認します。
    - マシン上の一時テキスト ファイルにファイルを格納します。
    - レプリケーション アプライアンスのパスフレーズを取得できます。 現在のパスフレーズを表示するには、コマンド ラインで **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe -v** を実行します。
    - パスフレーズを再生成しないでください。 そうすると接続が切断され、レプリケーション アプライアンスを再登録しなければならなくなります。

> [!NOTE]
> VMware の VM または物理マシンを移行する場合、 */Platform* パラメーターに *VMware* を指定します。

### <a name="install-on-windows"></a>Windows へのインストール

1. 次のように、マシン上のローカル フォルダー (C:\Temp など) にインストーラー ファイルの内容を展開します。

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```
2. モビリティ サービスのインストーラーを実行します。
    ```
   UnifiedAgent.exe /Role "MS" /Platform "VmWare" /Silent
    ```
3. レプリケーション アプライアンスにエージェントを登録します。
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="install-on-linux"></a>Linux をインストールする

1. 次のように、マシン上のローカル フォルダー (/tmp/MobSvcInstaller など) にインストーラー tarball の内容を展開します。
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```
2. インストーラー スクリプトを実行します。
    ```
    sudo ./install -r MS -v VmWare -q
    ```
3. レプリケーション アプライアンスにエージェントを登録します。
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="replicate-machines"></a>マシンをレプリケートする

今度は、移行の対象となるマシンを選択します。 

> [!NOTE]
> 最大 10 台のマシンをまとめてレプリケートできます。 レプリケートするマシンがそれより多い場合は、10 台をひとまとまりとして同時にレプリケートしてください。

1. Azure Migrate プロジェクトの **[サーバー]** の **[Azure Migrate: Server Migration]** で、 **[レプリケート]** をクリックします。

    ![Azure Migrate で選択した [レプリケート] ボタンが表示されている Azure Migrate の [サーバー] 画面のスクリーンショット。移行ツールでのサーバーの移行。](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. **[レプリケート]** で、 **[ソースの設定]**  >  **[マシンは仮想化されていますか?]** で、 **[非仮想化/その他]** を選択します。
3. **[オンプレミスのアプライアンス]** で、自分が設定した Azure Migrate アプライアンスの名前を選択します。
4. **[プロセス サーバー]** で、レプリケーション アプライアンスの名前を選択します。
6. **[ゲストの資格情報]** では、前の [レプリケーション インストーラーのセットアップ](#download-the-replication-appliance-installer)の間に作成したダミー アカウントを選択して、Mobility Service を手動でインストールしてください (プッシュ インストールはサポートされていません)。 その後、 **[次へ:仮想マシン]** をクリックします。   

    ![[レプリケート] 画面で [ゲストの資格情報] フィールドが強調表示されている [ソースの設定] タブのスクリーンショット。](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)

7. **[仮想マシン]** の **[評価から移行設定をインポートしますか?]** は、既定の設定である **[いいえ。移行設定を手動で指定します]** のままにしておきます。
8. 移行したい各 VM を確認します。 その後、 **[次へ:ターゲット設定]** をクリックします。

    ![VM を選択する](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)


9. **[ターゲット設定]** で、サブスクリプションと、移行先となるターゲット リージョンを選択し、移行後に Azure VM が配置されるリソース グループを指定します。
10. **[仮想ネットワーク]** で、移行後に Azure VM の参加先となる Azure VNet およびサブネットを選択します。
11. **[可用性オプション]** で、以下を選択します。
    -  可用性ゾーン。移行されたマシンをリージョン内の特定の可用性ゾーンにピン留めします。 このオプションを使用して、複数ノードのアプリケーション層を形成するサーバーを可用性ゾーン間で分散させます。 このオプションを選択した場合は、[コンピューティング] タブで選択した各マシンに使用する可用性ゾーンを指定する必要があります。このオプションは、移行用に選択したターゲット リージョンで Availability Zones がサポートされている場合にのみ使用できます。
    -  可用性セット。移行されたマシンを可用性セットに配置します。 このオプションを使用するには、選択されたターゲット リソース グループに 1 つ以上の可用性セットが必要です。
    - [インフラストラクチャ冗長は必要ありません] オプション (移行されたマシンに対してこれらの可用性構成がいずれも不要な場合)。
    
12. **[Disk encryption type]\(ディスク暗号化の種類\)** で、以下を選択します。
    - プラットフォーム マネージド キーを使用した保存時の暗号化
    - カスタマー マネージド キーを使用した保存時の暗号化
    - プラットフォーム マネージド キーとカスタマー マネージド キーを使用した二重暗号化

   > [!NOTE]
   > CMK を使用して VM をレプリケートするには、ターゲット リソース グループに[ディスク暗号化セットを作成する](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set)必要があります。 ディスク暗号化セット オブジェクトによって、SSE に使用する CMK を含む Key Vault にマネージド ディスクがマップされます。
  
13. **[Azure ハイブリッド特典]** で、

    - Azure ハイブリッド特典を適用しない場合は、 **[いいえ]** を選択します。 続けて、 **[次へ]** をクリックします。
    - アクティブなソフトウェア アシュアランスまたは Windows Server のサブスクリプションの対象となっている Windows Server マシンがあり、移行中のマシンにその特典を適用する場合は、 **[はい]** を選択します。 続けて、 **[次へ]** をクリックします。

    ![ターゲットの設定](./media/tutorial-migrate-vmware/target-settings.png)

14. **[コンピューティング]** で、VM の名前、サイズ、OS ディスクの種類、および可用性構成 (前の手順で選択した場合) を確認します。 VM は [Azure の要件](migrate-support-matrix-physical-migration.md#azure-vm-requirements)に準拠している必要があります。

    - **VM サイズ**: 評価の推奨事項を使用している場合は、[VM サイズ] ドロップダウンに推奨サイズが表示されます。 それ以外の場合は、Azure Migrate によって、Azure サブスクリプション内の最も近いサイズが選択されます。 または、 **[Azure VM サイズ]** でサイズを手動で選択します。
    - **OS ディスク**:VM の OS (ブート) ディスクを指定します。 OS ディスクは、オペレーティング システムのブートローダーとインストーラーがあるディスクです。
    - **可用性ゾーン**:使用する可用性ゾーンを指定します。
    - **可用性セット**:使用する可用性セットを指定します。

![コンピューティングの設定](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

15. **[ディスク]** で、VM ディスクを Azure にレプリケートするかどうかを指定し、Azure でのディスクの種類 (Standard SSD か HDD、または Premium マネージド ディスク) を選択します。 続けて、 **[次へ]** をクリックします。
    - レプリケーションからディスクを除外できます。
    - ディスクは除外すると、移行後に Azure VM 上に存在しなくなります。 

    ![ディスクの設定](./media/tutorial-migrate-physical-virtual-machines/disks.png)

16. **[レプリケーションの確認と開始]** で、設定を確認し、 **[レプリケート]** をクリックして、サーバーの初期レプリケーションを開始します。

> [!NOTE]
> レプリケーションが開始される前であれば、 **[管理]**  >  **[マシンのレプリケート]** でレプリケーションの設定をいつでも更新できます。 レプリケーションの開始後は、設定を変更することができません。

## <a name="track-and-monitor"></a>追跡して監視する

- **[レプリケート]** をクリックすると、レプリケーションの開始ジョブが開始されます。 
- レプリケーションの開始ジョブが正常に終了すると、マシンで Azure への初期レプリケーションが開始されます。
- 初期レプリケーションが完了すると、差分レプリケーションが開始されます。 オンプレミスのディスクに対する増分変更は、Azure のレプリカ ディスクに定期的にレプリケートされます。


ジョブの状態は、ポータルの通知で追跡できます。

レプリケーションの状態を監視するには、**サーバーをレプリケートしています** をクリックします (**Azure Migrate: Server Migration** 内)。
![レプリケーションの監視](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>テスト移行を実行する


差分レプリケーションが開始されるとき、Azure への完全な移行を実行する前に、VM のテスト移行を実行できます。 各マシンで少なくとも 1 回は、移行前にこれを実行することを強くお勧めします。

- テスト移行を実行すると、移行が想定どおりに動作することが確認されます。オンプレミスのマシンに影響はなく、稼働状態が維持され、レプリケーションが続行されます。 
- テスト移行では、レプリケートされたデータを使用して Azure VM を作成することによって、移行がシミュレートされます (通常は、自分の Azure サブスクリプション内の非運用 VNet に移行されます)。
- レプリケートされたテスト Azure VM を使用して、移行を検証し、アプリのテストを実行して、完全な移行前に問題に対処することができます。

テスト移行を実行するには、次のようにします。


1. **[移行の目標]**  >  **[サーバー]**  >  **[Azure Migrate: Server Migration]** で、 **[移行されたサーバーをテストします]** をクリックします。

     ![移行されたサーバーのテスト](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. テストする VM を右クリックし、 **[テスト移行]** をクリックします。

    ![テスト移行](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. **[テスト移行]** で、移行後に Azure VM が配置される Azure VNet を選択します。 非運用環境の VNet を使用することをお勧めします。
4. **テスト移行** ジョブが開始されます。 ポータルの通知でジョブを監視します。
5. 移行の完了後、Azure portal の **[仮想マシン]** で、移行された Azure VM を確認します。 マシン名には、サフィックス **-Test** が含まれています。
6. テストが完了したら、 **[マシンのレプリケート]** で Azure VM を右クリックし、 **[テスト移行をクリーンアップ]** をクリックします。

    ![移行のクリーンアップ](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="migrate-vms"></a>VM の移行

テスト移行が想定どおりに動作することを確認したら、オンプレミスのマシンを移行できます。

1. Azure Migrate プロジェクトの **[サーバー]**  >  **[Azure Migrate: Server Migration]** で、 **[サーバーをレプリケートしています]** をクリックします。

    ![サーバーをレプリケートしています](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. **[マシンのレプリケート]** で VM を右クリックし、 **[移行]** を選択します。
3. **[移行]**  >  **[仮想マシンをシャットダウンし、データ損失のない計画された移行を実行しますか]** で、 **[はい]**  >  **[OK]** の順に選択します。
    - VM をシャットダウンしたくない場合は、 **[いいえ]** を選択します
    
    注:物理サーバーを移行する場合は、移行期間の一環としてアプリケーションを停止させ (アプリケーションが接続を受け付けないようにして)、移行を開始することをお勧めします (移行の完了前に残りの変更を同期できるように、サーバーは動作させ続けておく必要があります)。

4. VM に対して移行ジョブが開始されます。 Azure 通知でジョブを追跡します。
5. ジョブが完了したら、 **[仮想マシン]** ページで VM を表示して管理できます。

## <a name="complete-the-migration"></a>移行を完了する

1. 移行が完了したら、VM を右クリックして、 **[移行を停止する]** を選択します。 次の処理が実行されます。
    - オンプレミス マシンのレプリケーションを停止します。
    - Azure Migrate: Server Migration の **[サーバーをレプリケートしています]** のカウントからマシンを削除します。Server Migration に関するエラーのトラブルシューティングに役立つ情報を提供しています。
    - マシンのレプリケーション状態情報をクリーンアップします。
2. Azure VM の [Windows](../virtual-machines/extensions/agent-windows.md) または [Linux](../virtual-machines/extensions/agent-linux.md) エージェントを、移行されたマシンにインストールします。
3. データベース接続文字列、および Web サーバー構成の更新など、移行後のアプリの微調整を実行します。
4. Azure で現在実行されている移行後のアプリケーション上で、最終的なアプリケーションと移行の受け入れのテストを実行します。
5. 移行された Azure VM インスタンスにトラフィックを切り替えます。
6. ローカル VM インベントリからオンプレミスの VM を削除します。
7. ローカル バックアップからオンプレミスの VM を削除します。
8. Azure VM の新しい場所と IP アドレスを示すように内部ドキュメントを更新します。 

## <a name="post-migration-best-practices"></a>移行後のベスト プラクティス

- 復元性の向上:
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
