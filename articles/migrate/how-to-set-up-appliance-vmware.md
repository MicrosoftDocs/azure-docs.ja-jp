---
title: VMware 向け Azure Migrate アプライアンスを設定する
description: Azure Migrate アプライアンスを設定して VMware VM を評価し移行する方法について説明します。
ms.topic: article
ms.date: 04/16/2020
ms.openlocfilehash: c9a9f1567f984fc5770b47d3998610cb69643360
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88923622"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>VMware VM のアプライアンスを設定する

[Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) ツールによる評価と [Azure Migrate:Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) ツールを使用したエージェントレス移行を行うには、この記事に従って Azure Migrate アプライアンスを設定してください。

[Azure Migrate アプライアンス](migrate-appliance.md) は、エージェントレス移行時にオンプレミスの VMware VM の検出、VM のメタデータ/パフォーマンス データの Azure への送信、VMware VM のレプリケーションを行うために、Azure Migrate:Server Assessment および Server Migration によって使用される軽量のアプライアンスです。

アプライアンスは、次の 2 とおりの方法でデプロイできます。

- ダウンロードした OVA テンプレートを使用して VMware VM 上に設定します。 この記事で説明しているのは、こちらの方法です。
- PowerShell インストーラー スクリプトを使用して VMware VM 上または物理マシン上に設定します。 OVA テンプレートを使用して VM を設定できない場合や、Azure Government をご利用の場合は、[この方法](deploy-appliance-script.md)を使用してください。

アプライアンスの作成後、Azure Migrate:Server Assessment に接続できることを確認し、最初の構成を行い、Azure Migrate プロジェクトに登録します。


## <a name="appliance-deployment-ova"></a>アプライアンスのデプロイ (OVA)

OVA テンプレートを使用してアプライアンスを設定するには:
- アプライアンス名を指定し、ポータルで Azure Migrate プロジェクト キーを生成します
- OVA テンプレート ファイルをダウンロードし、それを vCenter Server にインポートします。
- アプライアンスを作成し、それが Azure Migrate Server Assessment に接続できることを確認します。
- アプライアンスを初めて構成し、Azure Migrate プロジェクト キーを使用して Azure Migrate プロジェクトに登録します。

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
2. 次のコマンドを実行して、OVA のハッシュを生成します。
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用例: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. アプライアンスの最新のバージョンでは、生成されたハッシュがこれらの[設定](./tutorial-assess-vmware.md#verify-security)と一致する必要があります。



## <a name="create-the-appliance-vm"></a>アプライアンス VM を作成する

ダウンロードしたファイルをインポートし、VM を作成します。

1. vSphere Client コンソールで、 **[File]\(ファイル\)**  >  **[Deploy OVF Template]\(OVF テンプレートのデプロイ\)** の順にクリックします。
![OVF テンプレートをデプロイするためのメニュー コマンド](./media/tutorial-assess-vmware/deploy-ovf.png)

2. [Deploy OVF Template]\(OVF テンプレートのデプロイ\) ウィザードで **[Source]\(ソース\)** を選択し、OVA ファイルの場所を指定します。
3. **[名前]** と **[場所]** で、VM のフレンドリ名を指定します。 VM がホストされるインベントリ オブジェクトを選択します。
5. **[Host/Cluster]\(ホスト/クラスター\)** で、VM が実行されるホストまたはクラスターを指定します。
6. **[ストレージ]** で、VM の保存先を指定します。
7. **[Disk Format]\(ディスク フォーマット\)** で、ディスクの種類とサイズを指定します。
8. **[ネットワーク マッピング]** で、VM の接続先となるネットワークを指定します。 そのネットワークには、Azure Migrate Server Assessment にメタデータを送信するためのインターネット接続が必要です。
9. 設定を再確認したら、 **[Finish]\(完了\)** をクリックします。


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

## <a name="next-steps"></a>次のステップ

[VMware の評価](tutorial-assess-vmware.md)と[エージェントレスの移行](tutorial-migrate-vmware.md)に関するチュートリアルを確認します。
