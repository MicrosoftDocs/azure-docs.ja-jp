---
title: Google Cloud Platform (GCP) VM インスタンスの検出、評価、および Azure への移行
description: この記事では、Azure Migrate を使用して、GCP VM を Azure に移行する方法について説明します。
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: tutorial
ms.date: 08/19/2020
ms.custom: MVC
ms.openlocfilehash: 12b8e30b0107b6b008cbd6467ada7c2d44f5e6d6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98871640"
---
# <a name="discover-assess-and-migrate-google-cloud-platform-gcp-vms-to-azure"></a>Google Cloud Platform (GCP) VM の検出、評価、および Azure への移行

このチュートリアルでは、Azure Migrate を使用して Google Cloud Platform (GCP) の仮想マシン (VM) を検出して評価し、Azure VM に移行する方法について説明します。Server Assessment、Azure Migrate: 選択します。

このチュートリアルでは、次の内容を学習します。
> [!div class="checklist"]
>
> * 移行の前提条件を確認します。
> * Azure Migrate: Server Migration を使用して Azure リソースを準備します。Server Migration に関するエラーのトラブルシューティングに役立つ情報を提供しています。 Azure Migrate を操作するために、自分の Azure アカウントとリソースのアクセス許可を設定します。
> * 移行のために GCP VM インスタンスを準備します。
> * Azure Migrate:Server Migration ツールを Azure Migrate ハブに追加します。
> * レプリケーション アプライアンスを設定し、構成サーバーをデプロイします。
> * 移行する GCP VM に Mobility Service をインストールします。
> * VM のレプリケーションを有効にします。
> * レプリケーションの状態を追跡して監視します。 
> * すべてが想定どおりに動作していることを確認するためにテスト移行を実行します。
> * Azure への完全な移行を実行します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。

## <a name="discover-and-assess"></a>検出と評価

Azure に移行する前に、VM の検出と移行の評価を行うことが推奨されています。 この評価は、Azure への移行に向けて GCP VM を適切にサイズ変更し、Azure の実行にかかる予想コストを見積もるのに役立ちます。

次のとおりに評価を設定します。

1. [チュートリアル](./tutorial-discover-gcp.md)に従って、評価のために Azure の設定と GCP VM の準備を行います。 以下の点に注意してください。

    - Azure Migrate では、GCP VM インスタンスを検出するときにパスワード認証が使用されます。 GCP インスタンスは、既定ではパスワード認証をサポートしていません。 検出するには、事前にパスワード認証を有効にする必要があります。
        - Windows マシンの場合、WinRM ポート 5985 (HTTP) を許可します。 これにより、リモート WMI 呼び出しが可能になります。
        - Linux マシンの場合:
            1. 各 Linux マシンにサインインします。
            2. 次のようにして sshd_config ファイルを開きます。vi /etc/ssh/sshd_config
            3. ファイルで、**PasswordAuthentication** 行を見つけ、値を **yes** に変更します。
            4. ファイルを保存して閉じます。 sshdサービスを再起動します。
    - ルート ユーザーを使用して Linux VM を検出する場合、VM でルート ログインが許可されることを確認してください。
        1. 各 Linux マシンにサインインします。
        2. 次のようにして sshd_config ファイルを開きます。vi /etc/ssh/sshd_config
        3. ファイルで、**PermitRootLogin** 行を見つけ、値を **yes** に変更します。
        4. ファイルを保存して閉じます。 sshdサービスを再起動します。

2. 次に、[このチュートリアル](./tutorial-assess-gcp.md)に従って Azure Migrate プロジェクトとアプライアンスを設定し、対象の GCP VM を検出して評価します。

評価を試すことが推奨されていますが、評価を実行することは、VM を移行できるようになるための必須の手順ではありません。



## <a name="prerequisites"></a>前提条件 

- 移行する GCP VM で、サポートされている OS バージョンが実行されていることを確認します。 GCP VM は、移行目的では物理マシンのように処理されます。 物理サーバーの移行ワークフローについては、[サポートされているオペレーティング システムおよびカーネル バージョン](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines)に関するページを参照してください。 *hostnamectl* や *uname -a* のような標準コマンドを使用して、Linux VM の OS とカーネル バージョンを確認できます。  実際の移行を進める前に、テスト移行を実行し、VM が想定どおりに動作するかどうかを検証することをお勧めします。
- GCP VM が、Azure への移行で[サポートされる構成](./migrate-support-matrix-physical-migration.md#physical-server-requirements)に準拠していることを確認します。
- Azure にレプリケートする GCP VM が、[Azure VM の要件](./migrate-support-matrix-physical-migration.md#azure-vm-requirements)に準拠していることを確認します。
- Azure に VM を移行する前に、それらに対するいくつかの変更が必要です。
    - 一部のオペレーティング システムでは、これらの変更が Azure Migrate によって自動的に行われます。
    - 移行を開始する前にこれらの変更を行うことが重要です。 変更を行う前に VM を移行すると、Azure で VM が起動しない可能性があります。
[Windows](prepare-for-migration.md#windows-machines) と [Linux](prepare-for-migration.md#linux-machines) で必要な変更を確認してください。

### <a name="prepare-azure-resources-for-migration"></a>移行のために Azure リソースを準備する

Azure Migrate: Server Migration ツールを使用した移行のために Azure を準備します。サーバー移行ツール。

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

Azure 仮想ネットワーク (VNet) を[設定](../virtual-network/manage-virtual-network.md#create-a-virtual-network)します。 Azure にレプリケートすると、作成された Azure VM は、移行の設定時に指定した Azure VNet に参加させられます。

## <a name="prepare-gcp-instances-for-migration"></a>移行のために GCP インスタンスを準備する

GCP から Azure への移行を準備するために、移行用のレプリケーション アプライアンスを準備してデプロイする必要があります。

### <a name="prepare-a-machine-for-the-replication-appliance"></a>レプリケーション アプライアンス用のマシンの準備

Azure Migrate: Server Migration では、レプリケーション アプライアンスを使用してマシンを Azure にレプリケートします。 レプリケーション アプライアンスは、次のコンポーネントを実行します。

- **構成サーバー**: 構成サーバーは、GCP VM と Azure の間の通信を調整し、データのレプリケーションを管理します。
- **プロセス サーバー**:プロセス サーバーはレプリケーション ゲートウェイとして機能します。 レプリケーション データを受信し、キャッシュ、圧縮、暗号化を使用してそれを最適化して、Azure のキャッシュ ストレージ アカウントに送信します。

次のようにして、アプライアンスのデプロイの準備をします。

- レプリケーション アプライアンスをホストする別の GCP VM を設定します。 このインスタンスは、Windows Server 2012 R2 または Windows Server 2016 を実行している必要があります。 アプライアンスのハードウェア、ソフトウェア、およびネットワークの要件を[確認](./migrate-replication-appliance.md#appliance-requirements)します。
- レプリケート対象のソース VM、または以前にインストールした Azure Migrate 検出および評価アプライアンスに、アプライアンスをインストールすることはできません。 これは異なる VM にデプロイする必要があります。
- 移行するソース GCP VM には、レプリケーション アプライアンスへのネットワーク接続が必要です。 これを実現するため、必要なファイアウォール規則を構成します。 レプリケーション アプライアンスは、移行するソース VM と同じ VPC ネットワークにデプロイすることをお勧めします。 レプリケーション アプライアンスが異なる VPC 内に存在する必要がある場合は、VPC を VPC ピアリングを介して接続する必要があります。
- ソース GCP VM は、レプリケーション管理とレプリケーション データ転送のために、HTTPS 443 (コントロール チャネルのオーケストレーション) および TCP 9443 (データ転送) の受信ポートでレプリケーション アプライアンスと通信します。 レプリケーション アプライアンスは次に、HTTPS 443 送信ポート経由でレプリケーション データを Azure に送信します。 これらの規則を構成するには、ポートとソース IP の適切な情報を使用して、セキュリティ グループのインバウンド/アウトバウンド規則を編集します。

   ![GCP ファイアウォール規則](./media/tutorial-migrate-gcp-virtual-machines/gcp-firewall-rules.png)
     
 
   ![ファイアウォール規則を編集する](./media/tutorial-migrate-gcp-virtual-machines/gcp-edit-firewall-rule.png)

- レプリケーション アプライアンスでは MySQL が使用されます。 アプライアンスに MySQL をインストールするためのいくつかの[方法](migrate-replication-appliance.md#mysql-installation)を確認します。
- [パブリック](migrate-replication-appliance.md#url-access) クラウドおよび[政府機関向け](migrate-replication-appliance.md#azure-government-url-access)クラウドにアクセスするレプリケーション アプライアンスに必要な Azure URL を確認します。

## <a name="set-up-the-replication-appliance"></a>レプリケーション アプライアンスを設定する

移行の最初の手順は、レプリケーション アプライアンスを設定することです。 GCP VM 移行用のアプライアンスを設定するには、アプライアンスのインストーラー ファイルをダウンロードし、それを、[準備した VM](#prepare-a-machine-for-the-replication-appliance) で実行する必要があります。

### <a name="download-the-replication-appliance-installer"></a>レプリケーション アプライアンス インストーラーのダウンロード

1. Azure Migrate プロジェクトの **[サーバー]** で、 **[Azure Migrate: Server Migration]** で、 **[検出]** をクリックします。

    ![Discover VMs](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

2. **[マシンの検出]**  >  **[マシンは仮想化されていますか?]** で、 **[非仮想化/その他]** をクリックします。
3. **[ターゲット リージョン]** で、マシンの移行先にする Azure リージョンを選択します。
4. **[移行先のリージョンが <リージョン名> であることを確認してください]** を選択します。
5. **[リソースの作成]** をクリックします。 これで、Azure Site Recovery コンテナーがバックグラウンドで作成されます。
    - Azure Migrate Server Migration を使用した移行を既に設定してある場合は、リソースが以前に設定されているため、ターゲット オプションを構成できません。
    - このボタンのクリック後は、このプロジェクトのターゲット リージョンを変更することはできません。
    - VM を別のリージョンに移行するには、Azure Migrate の新しいプロジェクトや別のプロジェクトを作成する必要があります。

6. **[新しいレプリケーション アプライアンスをインストールしますか?]** で、 **[レプリケーション アプライアンスのインストール]** を選択します。
7. **[レプリケーション アプライアンス ソフトウェアをダウンロードしてインストールする]** で、アプライアンスのインストーラーと登録キーをダウンロードします。 このキーは、アプライアンスを登録するために必要です。 キーは、ダウンロード後 5 日間有効です。

    ![プロバイダーのダウンロード](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

8. アプライアンスの設定ファイルとキー ファイルを、レプリケーション アプライアンス用に作成した Windows Server 2016 または Windows Server 2012 GCP VM にコピーします。
9. 次の手順の説明に従って、レプリケーション アプライアンスの設定ファイルを実行します。  
    9.1. **[Before you Begin]\(開始する前に\)** で、 **[Install the configuration server and process server]\(構成サーバーとプロセス サーバーをインストールする\)** を選択して **[次へ]** を選択します。   
    9.2 **[Third-Party Software License] (サードパーティ ソフトウェアのライセンス)** で、 **[I Accept the third party license agreement] (サード パーティの使用許諾契約書に同意する)** を選択し、 **[次へ]** を選択します。   
    9.3 **[登録]** で **[参照]** を選択し、コンテナーの登録キー ファイルを格納した場所に移動します。 **[次へ]** を選択します。  
    9.4 **[Internet Settings] (インターネット設定)** で、 **[Connect to Azure Site Recovery without a proxy server] (プロキシ サーバーを使用せずに Azure Site Recovery に接続する)** を選択し、 **[次へ]** を選択します。  
    9.5 **[前提条件の確認]** ページで、いくつかの項目について確認が実行されます。 完了したら、 **[次へ]** を選択します。  
    9.6 **[MySQL Configuration] (MySQL の構成)** で、MySQL DB のパスワードを指定してから、 **[次へ]** をクリックします。  
    9.7 **[環境の詳細]** ページで **[いいえ]** を選択します。 VM を保護する必要はありません。 次に、 **[次へ]** を選択します。  
    9.8 **[インストール場所]** で、既定値のまま **[次へ]** を選択します。  
    9.9 **[ネットワークの選択]** で、既定値のまま **[次へ]** を選択します。  
    9.10 **[サマリー]** で **[インストール]** を選択します。   
    9.11 **[インストールの進行状況]** に、インストール プロセスに関する情報が表示されます。 完了したら、 **[完了]** を選択します。 ウィンドウに再起動に関するメッセージが表示されます。 **[OK]** を選択します。   
    9.12 次に、構成サーバーの接続パスフレーズに関するメッセージがウィンドウに表示されます。 パスフレーズをクリップボードにコピーし、そのパスフレーズをソース VM の一時テキスト ファイルに保存します。 このパスフレーズは後で、Mobility Service のインストール プロセス中に必要になります。
10. インストールが完了すると、アプライアンス構成ウィザードが自動的に起動します (アプライアンスのデスクトップに作成された cspsconfigtool ショートカットを使用して、ウィザードを手動で起動することもできます)。 ウィザードの [アカウントの管理] タブを使用して、モビリティ サービスのプッシュ インストールに使用するアカウントの詳細を追加します。 このチュートリアルでは、レプリケートされるソース VM にMobility Service を手動でインストールします。そのため、この手順でダミー アカウントを作成し、続行します。 ダミー アカウントを作成するための詳細情報には、"ゲスト" としてフレンドリ名、"ユーザー名" としてユーザー名、"パスワード" としてアカウントのパスワードを指定できます。 このダミー アカウントは、レプリケーションの有効化ステージで使用します。 
11. アプライアンスが設定後に再起動されたら、 **[マシンの検出]** の **[構成サーバーの選択]** で新しいアプライアンスを選択し、 **[Finalize registration]\(登録の終了処理\)** をクリックします。 登録の終了処理では、レプリケーション アプライアンスを準備するための終了タスクがいくつか実行されます。

    ![登録の終了処理](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

## <a name="install-the-mobility-service"></a>モビリティ サービスをインストールする

移行するソース GCP VM に、Mobility Service エージェントがインストールされている必要があります。 エージェント インストーラーは、レプリケーション アプライアンス上で使用できます。 適切なインストーラーを見つけて、移行したい各マシンにエージェントをインストールします。 次のようにします。

1. レプリケーション アプライアンスにサインインします。
2. **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository** に移動します。
3. ソース GCP VM のオペレーティング システムとバージョンに合ったインストーラーを見つけます。 [サポートされているオペレーティング システム](../site-recovery/vmware-physical-azure-support-matrix.md#replicated-machines)を確認してください。
4. 移行するソース GCP VM にインストーラー ファイルをコピーします。
5. レプリケーション アプライアンスをインストールしたときに作成されたパスフレーズ テキスト ファイルを保存済みであることを確認します。
    - パスフレーズを保存し忘れた場合は、この手順で、レプリケーション アプライアンスでのパスフレーズを参照できます。 現在のパスフレーズを表示するには、コマンド ラインで **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe -v** を実行します。
    - ここで、このパスフレーズをクリップボードにコピーし、それをソース VM 上の一時テキスト ファイルに保存します。

### <a name="installation-guide-for-windows-gcp-vms"></a>Windows GCP VM 用のインストール ガイド

1. 次のように、GCP VM 上のローカル フォルダー (C:\Temp など) にインストーラー ファイルの内容を展開します。

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```  

2. モビリティ サービスのインストーラーを実行します。
    ```
   UnifiedAgent.exe /Role "MS" /Silent
    ```  

3. レプリケーション アプライアンスにエージェントを登録します。
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="installation-guide-for-linux-gcp-vms"></a>Linux GCP VM 用のインストール ガイド

1. 次のように、GCP VM 上のローカル フォルダー (/tmp/MobSvcInstaller など) にインストーラー tarball の内容を展開します。
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```  

2. インストーラー スクリプトを実行します。
    ```
    sudo ./install -r MS -q
    ```  

3. レプリケーション アプライアンスにエージェントを登録します。
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="enable-replication-for-gcp-vms"></a>GCP VM のレプリケーションを有効にする

> [!NOTE]
> ポータルからは、一度に最大 10 台の VM をレプリケーションに追加できます。 複数の VM を同時にレプリケートするため、10 台のバッチ単位で追加できます。

1. Azure Migrate プロジェクトの **[サーバー]** の **[Azure Migrate: Server Migration]** で、 **[レプリケート]** をクリックします。

    ![VM をレプリケートする](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. **[レプリケート]** で、 **[ソースの設定]**  >  **[マシンは仮想化されていますか?]** で、 **[非仮想化/その他]** を選択します。
3. **[オンプレミスのアプライアンス]** で、自分が設定した Azure Migrate アプライアンスの名前を選択します。
4. **[プロセス サーバー]** で、レプリケーション アプライアンスの名前を選択します。 
5. **[ゲストの資格情報]** では、前の [レプリケーション インストーラーのセットアップ](#download-the-replication-appliance-installer)の間に作成したダミー アカウントを選択して、Mobility Service を手動でインストールしてください (プッシュ インストールはサポートされていません)。 その後、 **[次へ:仮想マシン]** をクリックします。   
 
    ![[レプリケート] 設定](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)
6. **[仮想マシン]** の **[評価から移行設定をインポートしますか?]** は、既定の設定である **[いいえ。移行設定を手動で指定します]** のままにしておきます。
7. 移行したい各 VM を確認します。 その後、 **[次へ:ターゲット設定]** をクリックします。

    ![VM を選択する](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)

8. **[ターゲット設定]** で、サブスクリプションと、移行先となるターゲット リージョンを選択し、移行後に Azure VM が配置されるリソース グループを指定します。
9. **[仮想ネットワーク]** で、移行後に Azure VM の参加先となる Azure VNet およびサブネットを選択します。
10. **[可用性オプション]** で、以下を選択します。
    -  可用性ゾーン。移行されたマシンをリージョン内の特定の可用性ゾーンにピン留めします。 このオプションを使用して、複数ノードのアプリケーション層を形成するサーバーを可用性ゾーン間で分散させます。 このオプションを選択した場合は、[コンピューティング] タブで選択した各マシンに使用する可用性ゾーンを指定する必要があります。このオプションは、移行用に選択したターゲット リージョンで Availability Zones がサポートされている場合にのみ使用できます。
    -  可用性セット。移行されたマシンを可用性セットに配置します。 このオプションを使用するには、選択されたターゲット リソース グループに 1 つ以上の可用性セットが必要です。
    - [インフラストラクチャ冗長は必要ありません] オプション (移行されたマシンに対してこれらの可用性構成がいずれも不要な場合)。
11. **[Disk encryption type]\(ディスク暗号化の種類\)** で、以下を選択します。
    - プラットフォーム マネージド キーを使用した保存時の暗号化
    - カスタマー マネージド キーを使用した保存時の暗号化
    - プラットフォーム マネージド キーとカスタマー マネージド キーを使用した二重暗号化

   > [!NOTE]
   > CMK を使用して VM をレプリケートするには、ターゲット リソース グループに[ディスク暗号化セットを作成する](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set)必要があります。 ディスク暗号化セット オブジェクトによって、SSE に使用する CMK を含む Key Vault にマネージド ディスクがマップされます。
  
12. **[Azure ハイブリッド特典]** で、

    - Azure ハイブリッド特典を適用しない場合は、 **[いいえ]** を選択します。 続けて、 **[次へ]** をクリックします。
    - アクティブなソフトウェア アシュアランスまたは Windows Server のサブスクリプションの対象となっている Windows Server マシンがあり、移行中のマシンにその特典を適用する場合は、 **[はい]** を選択します。 続けて、 **[次へ]** をクリックします。

    ![ターゲットの設定](./media/tutorial-migrate-vmware/target-settings.png)

13. **[コンピューティング]** で、VM の名前、サイズ、OS ディスクの種類、および可用性構成 (前の手順で選択した場合) を確認します。 VM は [Azure の要件](migrate-support-matrix-physical-migration.md#azure-vm-requirements)に準拠している必要があります。

    - **VM サイズ**: 評価の推奨事項を使用している場合は、[VM サイズ] ドロップダウンに推奨サイズが表示されます。 それ以外の場合は、Azure Migrate によって、Azure サブスクリプション内の最も近いサイズが選択されます。 または、 **[Azure VM サイズ]** でサイズを手動で選択します。
    - **OS ディスク**:VM の OS (ブート) ディスクを指定します。 OS ディスクは、オペレーティング システムのブートローダーとインストーラーがあるディスクです。
    - **可用性ゾーン**:使用する可用性ゾーンを指定します。
    - **可用性セット**:使用する可用性セットを指定します。

![コンピューティングの設定](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

14. **[ディスク]** で、VM ディスクを Azure にレプリケートするかどうかを指定し、Azure でのディスクの種類 (Standard SSD か HDD、または Premium マネージド ディスク) を選択します。 続けて、 **[次へ]** をクリックします。
    - レプリケーションからディスクを除外できます。
    - ディスクは除外すると、移行後に Azure VM 上に存在しなくなります。 

    ![ディスクの設定](./media/tutorial-migrate-physical-virtual-machines/disks.png)

15. **[レプリケーションの確認と開始]** で、設定を確認し、 **[レプリケート]** をクリックして、サーバーの初期レプリケーションを開始します。

> [!NOTE]
> レプリケーションが開始される前であれば、 **[管理]**  >  **[マシンのレプリケート]** でレプリケーションの設定をいつでも更新できます。 レプリケーションの開始後は、設定を変更することができません。

## <a name="track-and-monitor-replication-status"></a>レプリケーションの状態を追跡して監視する

- **[レプリケート]** をクリックすると、レプリケーションの開始ジョブが開始されます。
- レプリケーションの開始ジョブが正常に終了すると、VM で Azure への初期レプリケーションが開始されます。
- 初期レプリケーションが完了すると、差分レプリケーションが開始されます。 GCP VM のディスクに対する増分変更は、Azure のレプリカ ディスクに定期的にレプリケートされます。

ジョブの状態は、ポータルの通知で追跡できます。

レプリケーションの状態を監視するには、**サーバーをレプリケートしています** をクリックします (**Azure Migrate: Server Migration** 内)。  

![レプリケーションを監視します](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>テスト移行を実行する

差分レプリケーションが開始されるとき、Azure への完全な移行を実行する前に、VM のテスト移行を実行できます。 テスト移行が強く推奨されており、実際の移行を進める前に、潜在的な問題があれば検出して修正する機会となります。 移行前に、それぞれの VM で少なくとも 1 回これを実行することお勧めします。

- テスト移行を実行すると、GCP VM に影響を与えることなく、移行が想定どおりに動作することが確認されます。VM の稼働状態は維持されて、レプリケーションが続行されます。
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


## <a name="migrate-gcp-vms"></a>GCP VM を移行する

テスト移行が想定どおりに動作することを確認したら、GCP VM を移行できます。

1. Azure Migrate プロジェクトの **[サーバー]**  >  **[Azure Migrate: Server Migration]** で、 **[サーバーをレプリケートしています]** をクリックします。

    ![サーバーをレプリケートしています](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. **[マシンのレプリケート]** で VM を右クリックし、 **[移行]** を選択します。
3. **[移行]**  >  **[仮想マシンをシャットダウンし、データ損失のない計画された移行を実行しますか]** で、 **[はい]**  >  **[OK]** の順に選択します。
    - VM をシャットダウンしたくない場合は、 **[いいえ]** を選択します。
4. VM に対して移行ジョブが開始されます。 ジョブの状態は、ポータル ページの右上にある通知ベルのアイコンをクリックするか、Server Migration ツールのジョブ ページに移動する (ツール タイルの [概要] をクリックし、左側のメニューから [ジョブ] を選択する) ことで表示できます。
5. ジョブが完了したら、[仮想マシン] ページで VM を表示して管理できます。

### <a name="complete-the-migration"></a>移行を完了する

1. 移行が完了したら、VM を右クリックして、 **[移行を停止する]** を選択します。 次の処理が実行されます。
    - GCP VM のレプリケーションが停止されます。
    - Azure Migrate: Server Migration の **[サーバーをレプリケートしています]** のカウントから GCP VM を削除します。
    - VM のレプリケーション状態情報をクリーンアップします。
2. Azure VM の [Windows](../virtual-machines/extensions/agent-windows.md) または [Linux](../virtual-machines/extensions/agent-linux.md) エージェントを、移行されたマシンにインストールします。
3. データベース接続文字列、および Web サーバー構成の更新など、移行後のアプリの微調整を実行します。
4. Azure で現在実行されている移行後のアプリケーション上で、最終的なアプリケーションと移行の受け入れのテストを実行します。
5. 移行された Azure VM インスタンスにトラフィックを切り替えます。
6. Azure VM の新しい場所と IP アドレスを示すように内部ドキュメントを更新します。 




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



## <a name="troubleshooting--tips"></a>トラブルシューティングとヒント

**質問:** 移行のために検出されたサーバーの一覧に自分の GCP VM が表示されない   
**回答:** レプリケーション アプライアンスが要件を満たしているかどうかを調べます。 移行するソース VM にモビリティ エージェントがインストールされ、構成サーバーに登録されていることを確認してください。 ファイアウォール規則を調べて、レプリケーション アプライアンスとソース GCP VM 間のネットワーク パスを有効にします。  

**質問:** どうすれば、VM が正常に移行されたかどうかわかるか   
**回答:** 移行後に、[仮想マシン] ページで VM を表示して管理できます。 移行された VM に接続して検証します。  

**質問:** 以前に作成した Server Assessment の結果から、移行用の VM をインポートできない   
**回答:** 現時点では、このワークフローのために評価のインポートがサポートされていません。 回避策として、評価をエクスポートしてから手動で、「レプリケーションを有効にする」の手順の間に VM の推奨事項を選択できます。
  
**質問:** GCP VM の検出を試みているときに "Failed to fetch BIOS GUID (BIOS GUID をフェッチできませんでした)" というエラーが発生する   
**回答:** 認証には、擬似ユーザーではなく、ルート ログインを使用してください。 ルート ユーザーを使用できない場合は、[サポート マトリックス](migrate-support-matrix-physical.md#physical-server-requirements)に記載されている手順に従って、ユーザーに必要な機能が設定されていることを確認します。 また、GCP VM でサポートされているオペレーティング システムを確認してください。  

**質問:** レプリケーションの状態が進んでいない   
**回答:** レプリケーション アプライアンスが要件を満たしているかどうかを調べます。 レプリケーション アプライアンスの TCP ポート 9443 とデータ転送用の HTTPS 443 で、必要なポートが有効になっていることを確認します。 同じプロジェクトに、古い複製バージョンのレプリケーション アプライアンスが接続されていないことを確認します。   

**質問:** リモートの Windows 管理サービスからの HTTP 状態コード 504 のために、Azure Migrate を使用して GCP インスタンスを検出できない    
**回答:** Azure Migrate アプライアンスの要件と URL アクセスのニーズを必ず確認してください。 アプライアンスの登録をブロックしているプロキシ設定がないことを確認します。

**質問:** GCP VM を Azure に移行する前に、何か変更を加える必要がありますか   
**回答:** EC2 VM を Azure に移行する前に、これらの変更を行うことが必要になる場合があります。

- VM のプロビジョニングに cloud-init を使用している場合は、Azure にレプリケートする前に、VM の cloud-init を無効にすることをお勧めします。 VM の cloud-init によって実行されるプロビジョニング手順は、GCP に固有のものである可能性があり、Azure への移行後には有効ではありません。  
- 「[前提条件](#prerequisites)」セクションを確認して、Azure に移行する前に、オペレーティング システムに必要な変更があるかどうかを判断します。
- 最終的な移行の前に、必ずテスト移行を実行することをお勧めします。  

## <a name="next-steps"></a>次のステップ

Azure クラウド導入フレームワークでの[クラウド移行の工程](/azure/architecture/cloud-adoption/getting-started/migrate)を調査します。