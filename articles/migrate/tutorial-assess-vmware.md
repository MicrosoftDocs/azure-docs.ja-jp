---
title: Azure Migrate Server Assessment を使用して VMware VM を評価する
description: Azure Migrate Server Assessment を使用して Azure に移行するためにオンプレミスの VMware VM を評価する方法について説明します。
ms.topic: tutorial
ms.date: 06/03/2020
ms.custom: mvc
ms.openlocfilehash: 8c2784e999e751972883b6c9ffba2485bb9fe9e1
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950087"
---
# <a name="assess-vmware-vms-with-server-assessment"></a>Server Assessment による VMware VM の評価

この記事では、[Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) ツールを使用して、オンプレミスの VMware 仮想マシン (VM) を評価する方法について説明します。


これはシリーズの 2 番目のチュートリアルであり、VMware VM を評価して Azure に移行する方法を示しています。 このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * Azure Migrate プロジェクトを設定する。
> * VM を評価するためにオンプレミスで実行される Azure Migrate アプライアンスを設定する。
> * オンプレミス VM の継続的な検出を開始する。 アプライアンスでは、検出された VM の構成データとパフォーマンス データが Azure に送信されます。
> * 検出された VM をグループ化し、VM グループを評価する。
> * 評価を確認する。

> [!NOTE]
> チュートリアルでは、概念実証をすばやく設定できるように、シナリオの最も簡単なデプロイ パスを示します。 チュートリアルではできるだけ既定のオプションを使用しており、使用可能な設定とパスをすべて示しているわけではありません。 詳細な手順については、ハウツー記事を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。

## <a name="prerequisites"></a>前提条件

- このシリーズの[最初のチュートリアルを完了します](tutorial-prepare-vmware.md)。 そうしないと、このチュートリアルの手順はうまくいきません。
- 最初のチュートリアルでは、以下のことを行ったはずです。
    - Azure Migrate と連携するように [Azure を準備](tutorial-prepare-vmware.md#prepare-azure)します。
    - [評価のために VMware を準備](tutorial-prepare-vmware.md#prepare-for-assessment)します。 これには、VMware の設定の確認、Azure Migrate が vCenter Server にアクセスするために使用できるアカウントの設定などが含まれています。
    - VMware の評価用の Azure Migrate アプライアンスをデプロイするために必要なものを[確認](tutorial-prepare-vmware.md#verify-appliance-settings-for-assessment)します。

## <a name="set-up-an-azure-migrate-project"></a>Azure Migrate プロジェクトを設定する

次のように、新しい Azure Migrate プロジェクトを設定します。

1. Azure portal の **[すべてのサービス]** で、**Azure Migrate** を検索します。
2. **[サービス]** で **[Azure Migrate]** を選択します。
3. **[概要]** の **[サーバーの検出、評価、移行]** で、 **[サーバーの評価と移行]** を選択します。

   ![サーバーを評価して移行するボタン](./media/tutorial-assess-vmware/assess-migrate.png)

4. **[作業の開始]** で、 **[ツールの追加]** を選択します。
5. **[移行プロジェクト]** で、自分の Azure サブスクリプションを選択し、リソース グループがない場合は作成します。     
6. **[プロジェクトの詳細]** で、プロジェクト名と、プロジェクトを作成したい地理的な場所を指定します。 [パブリック](migrate-support-matrix.md#supported-geographies-public-cloud)と [Government クラウド](migrate-support-matrix.md#supported-geographies-azure-government)でサポートされている地域を確認してください。

   ![プロジェクト名とリージョンのボックス](./media/tutorial-assess-vmware/migrate-project.png)

7. **[次へ]** を選択します。
8. **[評価ツールの選択]** で、次を選択します: **[Azure Migrate: Server Assessment]**  >  **[次へ]** 。

   ![Server Assessment ツールの選択](./media/tutorial-assess-vmware/assessment-tool.png)

9. **[移行ツールの選択]** で、 **[今は移行ツールの追加をスキップします]**  >  **[次へ]** の順に選択します。
10. **[ツールの確認と追加]** で設定を確認し、 **[ツールの追加]** を選択します。
11. Azure Migrate プロジェクトがデプロイされるまで数分待ちます。 プロジェクトのページが表示されます。 プロジェクトが表示されない場合は、Azure Migrate ダッシュボードの **[サーバー]** からアクセスできます。

## <a name="set-up-the-azure-migrate-appliance"></a>Azure Migrate アプライアンスを設定する

Azure Migrate:Server Assessment では、軽量の Azure Migrate アプライアンスが使用されます。 このアプライアンスは VM の検出を実行し、VM のメタデータとパフォーマンス データを Azure Migrate に送信します。 このアプライアンスは、さまざまな方法で設定できます。

- ダウンロードした OVA テンプレートを使用して VMware VM 上に設定します。 **このチュートリアルでは、この方法を使用します。**
- PowerShell インストーラー スクリプトを使用して VMware VM 上または物理マシン上に設定します。 OVA テンプレートを使用して VM を設定できない場合や、Azure Government をご利用の場合は、[この方法](deploy-appliance-script.md)を使用してください。

アプライアンスの作成後、Azure Migrate:Server Assessment に接続できることを確認し、最初の構成を行い、Azure Migrate プロジェクトに登録します。


### <a name="generate-the-azure-migrate-project-key"></a>Azure Migrate プロジェクト キーを生成する

1. **移行の目標** > **サーバー** > **Azure Migrate: Server Assessment** で、**検出** を選択します。
2. **[マシンの検出]**  >  **[マシンは仮想化されていますか?]** で、 **[はい。VMware vSphere Hypervisor を使用します]** を選択します。
3. **[1:Azure Migrate プロジェクト キーを生成します]** で、VMware VM の検出用に設定する Azure Migrate アプライアンスの名前を指定します。名前は 14 文字以内の英数字にする必要があります。
1. **[キーの生成]** をクリックして、必要な Azure リソースの作成を開始します。 リソースの作成中に [マシンの検出] ページを閉じないでください。
1. Azure リソースが正常に作成されると、**Azure Migrate プロジェクト キー**が生成されます。
1. このキーはアプライアンスを設定する際、登録を完了するために必要なので、コピーしておきます。

### <a name="download-the-ova-template"></a>OVA テンプレートをダウンロードする
**[2:Azure Migrate アプライアンスをダウンロードする]** で、.OVA ファイルを選択し、 **[ダウンロード]** をクリックします。 


   ![[マシンの検出] の選択](./media/tutorial-assess-vmware/servers-discover.png)


   ![[キーの生成] の選択](./media/tutorial-assess-vmware/generate-key-vmware.png)


### <a name="verify-security"></a>セキュリティを確認する

OVA ファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたマシンで、管理者用のコマンド ウィンドウを開きます。
2. OVA ファイルのハッシュを生成するには、次のコマンドを実行します。
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   使用例: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```

3. 最新のアプライアンス バージョンとハッシュ値を確認します。

    - Azure パブリック クラウドの場合:
    
        **アルゴリズム** | **ダウンロード** | **SHA256**
        --- | --- | ---
        VMware (11.6 GB) | [最新バージョン](https://go.microsoft.com/fwlink/?linkid=2140333) | e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74

    - Azure Government の場合:
    
        **アルゴリズム** | **ダウンロード** | **SHA256**
        --- | --- | ---
        VMware (85 MB) | [最新バージョン](https://go.microsoft.com/fwlink/?linkid=2140337) | 7dab9445a89b47302994d6de4caddaa092c1c582c8f3c1fc5b9c4908c7d2f9f7


### <a name="create-the-appliance-vm"></a>アプライアンス VM を作成する

ダウンロードしたファイルをインポートし、VM を作成します。

1. vSphere Client コンソールで、 **[File]\(ファイル\)**  >  **[Deploy OVF Template]\(OVF テンプレートのデプロイ\)** の順に選択します。

   ![OVF テンプレートをデプロイするためのメニュー コマンド](./media/tutorial-assess-vmware/deploy-ovf.png)

2. [Deploy OVF Template]\(OVF テンプレートのデプロイ\) ウィザードで **[Source]\(ソース\)** を選択し、OVA ファイルの場所を指定します。
3. **[名前]** と **[場所]** で、VM のフレンドリ名を指定します。 VM がホストされるインベントリ オブジェクトを選択します。
4. **[Host/Cluster]\(ホスト/クラスター\)** で、VM が実行されるホストまたはクラスターを指定します。
5. **[ストレージ]** で、VM の保存先を指定します。
6. **[Disk Format]\(ディスク フォーマット\)** で、ディスクの種類とサイズを指定します。
7. **[ネットワーク マッピング]** で、VM の接続先となるネットワークを指定します。 そのネットワークには、Azure Migrate Server Assessment にメタデータを送信するためのインターネット接続が必要です。
8. 設定を確認し、 **[Finish]\(完了\)** を選択します。

## <a name="verify-appliance-access-to-azure"></a>アプライアンスによる Azure へのアクセスを確認する

[パブリック](migrate-appliance.md#public-cloud-urls) クラウドと[政府機関向け](migrate-appliance.md#government-cloud-urls)クラウドの Azure URL にアプライアンス VM から接続できることを確認します。

### <a name="configure-the-appliance"></a>アプライアンスを構成する

アプライアンスを初めて設定します。

> [!NOTE]
> ダウンロードした OVA でなく、[PowerShell スクリプト](deploy-appliance-script.md)を使用してアプライアンスを設定する場合、この手順の最初の 2 つの操作は必要ありません。

1. vSphere クライアント コンソールで、VM を右クリックし、 **[コンソールを開く]** を選択します。
2. アプライアンスの言語、タイム ゾーン、パスワードを指定します。
3. VM に接続できる任意のマシン上でブラウザーを開き、アプライアンス Web アプリの URL を開きます (**https://*アプライアンス名または IP アドレス*:44368**)。

   または、アプリ ショートカットを選択して、アプライアンス デスクトップからアプリを開くこともできます。
1. **ライセンス条項**に同意し、サード パーティの情報を確認します。
1. Web アプリの **[前提条件のセットアップ]** で、以下を実行します。
   - **接続**:VM でインターネットにアクセスできることが、アプリによって確認されます。 VM でプロキシを使用する場合:
     - **[プロキシの設定]** をクリックし、プロキシ アドレス (http://ProxyIPAddress または http://ProxyFQDN) の形式) とリッスン ポートを指定します。
     - プロキシで認証が必要な場合は、資格情報を指定します。
     - サポートされるのは HTTP プロキシのみです。
     - プロキシの詳細を追加した場合、またはプロキシまたは認証を無効にした場合は、 **[保存]** をクリックして接続チェックを再度トリガーします。
   - **時刻同期**:検出を正常に機能させるには、アプライアンス上の時刻がインターネットの時刻と同期している必要があります。
   - **更新プログラムのインストール**:アプライアンスでは、最新の更新プログラムがインストールされることが保証されます。 確認が完了したら、 **[View appliance services]\(アプライアンス サービスを表示\)** をクリックして、アプライアンスで実行されているコンポーネントの状態とバージョンを確認できます。
   - **Install VDDK\(VDDK のインストール\)** :アプライアンスでは、VMware vSphere Virtual Disk Development Kit (VDDK) がインストールされていることが確認されます。 インストールされていない場合は、**インストール手順**に記載されているとおりに、VMware から VDDK 6.7 をダウンロードし、ダウンロードした zip コンテンツをアプライアンス上の指定された場所に抽出します。

     Azure Migrate Server Migration では、Azure への移行の間に VDDK を使用してマシンがレプリケートされます。 
1. 必要に応じて、アプライアンス構成中ならいつでも、**前提条件の再実行**を行って、アプライアンスがすべての前提条件を満たしているかどうかを確認できます。

### <a name="register-the-appliance-with-azure-migrate"></a>Azure Migrate にアプライアンスを登録する

1. ポータルからコピーした **Azure Migrate プロジェクト キー**を貼り付けます。 このキーがない場合は、 **[Server Assessment] > [検出] > [Manage existing appliances]\(既存のアプライアンスの管理\)** に移動して、キーの生成時に指定したアプライアンス名を選択して、対応するキーをコピーします。
1. **[ログイン]** をクリックします。 新しいブラウザー タブで Azure ログイン プロンプトが開きます。表示されない場合は、ブラウザーでポップアップ ブロックを無効にしてあることを確認します。
1. 新しいタブで、Azure のユーザー名とパスワードを使用してサインインします。
   
   PIN を使用したサインインはサポートされていません。
3. 正常にログインしたら、Web アプリに戻ります。 
4. ログに使用した Azure ユーザー アカウントに、キーの生成時に作成した Azure リソースに対する正しい[アクセス許可](tutorial-prepare-vmware.md#prepare-azure)が付与されている場合、アプライアンスの登録が開始されます。
1. アプライアンスが正常に登録された後は、 **[詳細の表示]** をクリックすることで登録の詳細を確認できるようになります。


## <a name="start-continuous-discovery"></a>継続的な検出を開始する

VM の構成データとパフォーマンス データを検出するには、アプライアンスを vCenter Server に接続する必要があります。

1. **[ステップ 1:Provide vCenter Server credentials]\(ステップ 1: vCenter Server の資格情報を指定する\)** で、 **[資格情報の追加]** をクリックして資格情報のフレンドリ名を指定し、vCenter Server インスタンスでの VM の検出にアプライアンスで使用される vCenter Server またはクラスターの **[ユーザー名]** と **[パスワード]** を追加します。
    - [前のチュートリアル](tutorial-prepare-vmware.md#set-up-permissions-for-assessment)で、必要なアクセス許可を持つアカウントを設定してある必要があります。
    - 検出を特定の VMware オブジェクト (vCenter Server データセンター、クラスター、クラスターのフォルダー、ホスト、ホストのフォルダー、または個々の VM) にスコーピングする場合、Azure Migrate によって使用されるアカウントを制限するには、[この記事](set-discovery-scope.md)の手順を参照してください。
1. **[ステップ 2:Provide vCenter Server details]\(ステップ 2: VCenter Server の詳細を指定する\)** で、 **[Add discovery source]\(検出ソースの追加\)** をクリックして、ドロップダウンから資格情報のフレンドリ名を選択し、vCenter Server インスタンスの **IP アドレスまたは FQDN** を指定します。 **[ポート]** を既定 (443) のまま使用することも、vCenter Server でリッスンするカスタム ポートを指定して **[保存]** をクリックしることもできます。
1. [保存] をクリックすると、アプライアンスは、指定された資格情報を使用して vCenter Server への接続の検証を試み、vCenter Server の IP アドレスまたは FQDN に対して**検証状態**を表に表示します。
1. 検出を開始する前に、vCenter Server への接続はいつでも**再検証**できます。
1. **[ステップ 3:Provide VM credentials to discover installed applications and to perform agentless dependency mapping]\(ステップ 3: インストールされているアプリケーションを検出するための VM 資格情報を指定し、エージェントレスの依存関係マッピングを実行する\)** で、 **[資格情報の追加]** をクリックし、資格情報を提供するオペレーティング システム、資格情報のフレンドリ名、 **[ユーザー名]** と **[パスワード]** を指定します。 その後、 **[保存]** をクリックします。

    - [アプリケーション検出機能](how-to-discover-applications.md)または[エージェントレスの依存関係の分析機能](how-to-create-group-machine-dependencies-agentless.md)のために使用するアカウントを作成した場合は、必要に応じて、ここで資格情報を追加します。
    - これらの機能を使用しない場合は、スライダーをクリックして手順をスキップできます。 後からいつでもインテントを元に戻すことができます。
    - [アプリケーション検出](migrate-support-matrix-vmware.md#application-discovery-requirements)または[エージェントレス分析](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless)に必要な資格情報を確認してください。

5. VM の検出を開始するには、 **[検出の開始]** をクリックします。 検出が正常に開始されたら、vCenter Server の IP アドレスまたは FQDN に対する検出状態を表で確認できます。

検出は次のように行われます。
- 検出された VM のメタデータがポータルに表示されるまでに、約 15 分かかります。
- インストールされているアプリケーション、ロール、および機能の検出には時間がかかります。 検出対象の VM 数によって期間は変わります。 500 台の VM がある場合、アプリケーション インベントリが Azure Migrate ポータルに表示されるまでに約 1 時間かかります。

### <a name="verify-vms-in-the-portal"></a>ポータル内での VM の特定

検出の後で、VM が Azure portal に表示されることを確認できます。

1. Azure Migrate ダッシュボードを開きます。
2. **[Azure Migrate - サーバー]**  >  **[Azure Migrate: Server Assessment]** で、 **[検出済みサーバー]** の数を表示するアイコンを選択します。

## <a name="set-up-an-assessment"></a>評価を設定する

Azure Migrate Server Assessment を使用して、次の 2 種類の評価を作成できます。

**評価の種類** | **詳細**
--- | --- 
**Azure VM** | オンプレミスのサーバーを Azure 仮想マシンに移行するための評価。 <br/><br/> このタイプの評価を使用すると、Azure への移行について、オンプレミスの [VMware VM](how-to-set-up-appliance-vmware.md)、[Hyper-V VM](how-to-set-up-appliance-hyper-v.md)、[物理サーバー](how-to-set-up-appliance-physical.md)を評価できます。 [詳細情報](concepts-assessment-calculation.md)
**Azure VMware Solution (AVS)** | オンプレミスのサーバーを [Azure VMware Solution (AVS)](../azure-vmware/introduction.md) に移行するための評価。 <br/><br/> このタイプの評価を使用すると、Azure VMware Solution (AVS) への移行について、オンプレミスの [VMware VM](how-to-set-up-appliance-vmware.md) を評価できます。 [詳細情報](concepts-azure-vmware-solution-assessment-calculation.md)

Server Assessment には、サイズ変更の設定基準として、次の 2 つのオプションが用意されています。

**サイズ変更の設定基準** | **詳細** | **データ**
--- | --- | ---
**パフォーマンスベース** | 収集されたパフォーマンス データに基づいて推奨を行う評価 | **Azure VM の評価**: VM サイズの推奨値は、CPU とメモリの使用率データに基づきます。<br/><br/> ディスクの種類に関する推奨事項 (標準 HDD/SSD またはプレミアム マネージド ディスク) は、オンプレミス ディスクの IOPS とスループットに基づきます。<br/><br/> **Azure VMware Solution (AVS) の評価**: AVS ノードに関する推奨事項は、CPU とメモリの使用率データに基づきます。
**現状のオンプレミス** | パフォーマンス データを使用せずに推奨を行う評価。 | **Azure VM の評価**: VM サイズに関する推奨事項は、オンプレミスの VM サイズに基づきます<br/><br> 推奨されるディスクの種類は、評価の [ストレージの種類] 設定で選択した内容に基づきます。<br/><br/> **Azure VMware Solution (AVS) の評価**: AVS ノードに関する推奨事項は、オンプレミスの VM サイズに基づきます。

## <a name="run-an-assessment"></a>評価を実行する

"*Azure VM の評価*" は、次のように実行します。

1. 評価を作成するための[ベスト プラクティス](best-practices-assessment.md)を確認します。
2. **[サーバー]** タブの **[Azure Migrate: Server Assessment]** タイルで、 **[評価]** を選択します。

   ![[評価] ボタンの場所](./media/tutorial-assess-vmware/assess.png)

3. **[サーバーの評価]** で、評価の種類に "Azure VM" を選択し、検出ソースを選択して評価名を指定します。

    ![評価の基本](./media/tutorial-assess-vmware/assess-servers-azurevm.png)
 
4. **[すべて表示]** を選択し、評価のプロパティを確認します。

   ![評価のプロパティ](./media/tutorial-assess-vmware/view-all.png)

5. **[次へ]** をクリックして**評価するマシンを選択**します。 **[グループを選択または作成します]** で **[新規作成]** を選択し、グループ名を指定します。 グループで、評価のために 1 つ以上の VM をまとめます。
6. **[グループにマシンを追加します]** で、グループに追加する VM を選択します。
7. **[次へ]** をクリックして **[評価の確認と作成]** を選択し、評価の詳細を確認します。
8. **[評価を作成します]** を選択して、グループを作成し、評価を実行します。

   ![サーバーの評価](./media/tutorial-assess-vmware/assessment-create.png)

8. 評価が作成されたら、それを表示します ( **[サーバー]**  >  **[Azure Migrate: Server Assessment]**  >  **[評価]** )。
9. **[評価のエクスポート]** を選択し、Excel ファイルとしてダウンロードします。

**Azure VMware Solution (AVS) の評価**を実行したい場合は、[こちら](how-to-create-azure-vmware-solution-assessment.md)で紹介されている手順に従います。


## <a name="review-an-assessment"></a>評価を確認する

評価には以下が記述されています。

- **Azure 対応性**: VM が Azure への移行に適しているかどうか。
- **月間コスト見積もり**: Azure で VM を実行するためのコンピューティングとストレージの月間推定コスト。
- **月間ストレージ コスト見積もり**: 移行後のディスク ストレージの推定コスト。

評価を表示するには:

1. **[移行の目標]**  >  **[サーバー]** で、 **[評価]** を **[Azure Migrate: Server Assessment]** でクリックします。
2. **[評価]** で、評価を選択して開きます。

   ![評価の概要](./media/tutorial-assess-vmware/assessment-summary.png)

### <a name="review-azure-readiness"></a>Azure 対応性の確認

1. **[Azure 対応性]** で、VM が Azure への移行の準備ができているかどうかを確認します。
2. VM の状態を確認します。
    - **[Azure に対応]** : 評価内の VM の VM サイズとコスト見積もりが Azure Migrate から推奨される場合に使用します。
    - **[条件付きで対応]** : 問題と推奨される修復方法が示されます。
    - **[Azure に未対応]** : 問題と推奨される修復方法が示されます。
    - **[Readiness 不明]** : データの可用性の問題により、Azure Migrate が対応性を評価できない場合に使用されます。

3. **[Azure 対応性]** の状態を選択します。 VM の対応性の詳細を確認できます。 また、ドリルダウンして、コンピューティング、ストレージ、ネットワークの設定など、VM の詳細を確認することもできます。

### <a name="review-cost-details"></a>コスト詳細の確認

評価の概要には、Azure で実行されている VM のコンピューティングとストレージのコストの見積りが表示されます。 コストは、評価されるグループ内のすべての VM について集計されます。 ドリルダウンして、特定の VM のコストの詳細を見ることができます。

> [!NOTE]
> コストの見積もりは、マシンの推奨サイズ、およびそのディスクとプロパティに基づいています。 見積りは、オンプレミスの VM を IaaS VM として実行するためのものです。 Azure Migrate Server Assessment では、PaaS または SaaS のコストは考慮されません。

評価されたグループの集計されたストレージ コストは、ストレージ ディスクの種類ごとに分けて表示されます。 

### <a name="review-confidence-rating"></a>信頼度レーティングを確認する

Azure Migrate Server Assessment によって、1 つ星 (最低) から 5 つ星 (最高) までの信頼度レーティングがパフォーマンスベースの評価に割り当てられます。

![信頼度レーティング](./media/tutorial-assess-vmware/confidence-rating.png)

信頼度レーティングを使うと、評価の推奨サイズの信頼性を見積もることができます。 このレーティングは、評価の計算に必要なデータ ポイントの可用性に基づいています。

**データ ポイントの可用性** | **信頼度レーティング**
--- | ---
0% - 20% | 1 つ星
21% - 40% | 2 つ星
41% - 60% | 3 つ星
61% - 80% | 4 つ星
81% - 100% | 5 つ星

信頼度レーティングの[ベスト プラクティスの詳細については、こちら](best-practices-assessment.md#best-practices-for-confidence-ratings)を参照してください。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Migrate アプライアンスを設定しました。 また、評価を作成して確認しました。

Azure Migrate Server Migration を使用して VMware VM を Azure に移行する方法について学習するには、このシリーズの 3 番目のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [VMware VM を移行する](./tutorial-migrate-vmware.md)
