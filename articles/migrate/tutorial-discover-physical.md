---
title: Azure Migrate Discovery and Assessment を使用して物理サーバーを検出する
description: Azure Migrate Discovery and Assessment を使用して、オンプレミスの物理サーバーを検出する方法について説明します。
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 03/11/2021
ms.custom: mvc
ms.openlocfilehash: 4f6dc61b0e05fcb16a03a1183518069b98aa575f
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771415"
---
# <a name="tutorial-discover-physical-servers-with-azure-migrate-discovery-and-assessment"></a>チュートリアル: Azure Migrate: Discovery and Assessment を使用して物理サーバーを検出する

Azure への移行の一環として、評価と移行のためにサーバーを検出します。

このチュートリアルでは、軽量の Azure Migrate アプライアンスを使用して、Azure Migrate: Discovery and Assessment ツールでオンプレミスの物理サーバーを検出する方法について説明します。 アプライアンスを物理サーバーとしてデプロイし、サーバーとパフォーマンスのメタデータを継続的に検出します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure アカウントを設定します。
> * 検出のために物理サーバーを準備します。
> * プロジェクトを作成します。
> * Azure Migrate アプライアンスを設定します。
> * 継続的な検出を開始します。

> [!NOTE]
> 各チュートリアルでは、シナリオを試すための最も簡単な方法を紹介し、既定のオプションを使用します。  

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、次の前提条件を満たしていることを確認してください。

**要件** | **詳細**
--- | ---
**アプライアンス** | Azure Migrate アプライアンスを実行するサーバーが必要です。 サーバーには次が必要です。<br/><br/> - Windows Server 2016 がインストールされていること。<br/> _(現在のところ、アプライアンスは Windows Server 2016 でのみデプロイできます。)_<br/><br/> - 16-GB RAM、8 個の vCPU、約 80 GB のディスク記憶域<br/><br/> - 直接またはプロキシ経由でインターネットにアクセスできる、静的または動的 IP アドレス。
**Windows サーバー** | WinRM ポート 5985 (HTTP) で受信接続を許可して、アプライアンスが構成とパフォーマンスのメタデータをプルできるようにします。
**Linux サーバー** | ポート22 (TCP) で受信接続を許可します。

## <a name="prepare-an-azure-user-account"></a>Azure ユーザー アカウントを準備する

プロジェクトを作成し、Azure Migrate アプライアンスを登録するには、次を備えたアカウントが必要です。
- Azure サブスクリプションに対する共同作成者または所有者のアクセス許可。
- Azure Active Directory (AAD) アプリを登録するためのアクセス許可。

無料の Azure アカウントを作成したばかりであれば、自分のサブスクリプションの所有者になっています。 サブスクリプションの所有者でない場合は、所有者と協力して、次のようにアクセス許可を割り当てます。

1. Azure portal で "サブスクリプション" を検索し、 **[サービス]** で **[サブスクリプション]** を選択します。

    ![Azure サブスクリプションを検索するための検索ボックス](./media/tutorial-discover-physical/search-subscription.png)

2. **[サブスクリプション]** ページで、プロジェクトを作成するサブスクリプションを選択します。
3. サブスクリプションで、 **[アクセス制御 (IAM)]**  >  **[アクセスの確認]** の順に選択します。
4. **[アクセスの確認]** で、適切なユーザー アカウントを検索します。
5. **[ロールの割り当てを追加する]** で、 **[追加]** をクリックします。

    ![ユーザー アカウントを検索してアクセスを確認し、ロールを割り当てる](./media/tutorial-discover-physical/azure-account-access.png)

6. **[ロールの割り当ての追加]** で、共同作成者または所有者ロールを選択し、アカウント (この例では azmigrateuser) を選択します。 **[保存]** をクリックします。

    ![[ロールの割り当ての追加] ページを開いて、アカウントにロールを割り当てる](./media/tutorial-discover-physical/assign-role.png)

1. アプライアンスを登録するには、お使いの Azure アカウントに **AAD アプリを登録するためのアクセス許可** が必要です。
1. Azure portal で、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[ユーザー設定]** に移動します。
1. **[ユーザー設定]** で、Azure AD ユーザーがアプリケーションを登録できることを確認します (既定で **[はい]** に設定されています)。

    ![[ユーザー設定] で、ユーザーが Active Directory アプリを登録できることを確認する](./media/tutorial-discover-physical/register-apps.png)

9. [アプリの登録] 設定が [いいえ] に設定されている場合は、テナントまたはグローバル管理者に、必要なアクセス許可を割り当てるよう依頼してください。 テナントまたはグローバル管理者は、AAD アプリの登録を許可するために、**アプリケーション開発者** ロールをアカウントに割り当てることもできます。 [詳細については、こちらを参照してください](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)。

## <a name="prepare-physical-servers"></a>物理サーバーを準備する

アプライアンスが物理サーバーへのアクセスに使用できるアカウントを設定します。

- **Windows サーバー** の場合、ドメイン参加済みのサーバーにはドメイン アカウントを、ドメインに参加していないサーバーにはローカル アカウントを使用します。 次のグループにユーザー アカウントを追加する必要があります:リモート管理ユーザー、パフォーマンス モニター ユーザー、パフォーマンス ログ ユーザー。
- **Linux サーバー** の場合、検出する Linux サーバーのルート アカウントが必要です。 または、次のコマンドを使用して、必要な機能を持つ非ルート アカウントを設定することもできます。

**コマンド** | **目的**
--- | --- |
setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/fdisk <br></br> setcap CAP_DAC_READ_SEARCH+eip /sbin/fdisk _(/usr/sbin/fdisk が存在しない場合)_ | ディスク構成データを収集するため
setcap "cap_dac_override,cap_dac_read_search,cap_fowner,cap_fsetid,cap_setuid,<br>cap_setpcap,cap_net_bind_service,cap_net_admin,cap_sys_chroot,cap_sys_admin,<br>cap_sys_resource,cap_audit_control,cap_setfcap=+eip" /sbin/lvm | ディスクのパフォーマンス データを収集するため
setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/dmidecode | BIOS のシリアル番号を収集するため
chmod a+r /sys/class/dmi/id/product_uuid | BIOS の GUID を収集するため


## <a name="set-up-a-project"></a>プロジェクトの設定

新しいプロジェクトを設定します。

1. Azure portal の **[すべてのサービス]** で、**Azure Migrate** を検索します。
2. **[サービス]** で **[Azure Migrate]** を選択します。
3. **[概要]** で、 **[プロジェクトの作成]** を選択します。
5. **[プロジェクトの作成]** で、Azure サブスクリプションとリソース グループを選択します。 リソース グループがない場合は作成します。
6. **[プロジェクトの詳細]** で、プロジェクト名と、プロジェクトを作成したい地理的な場所を指定します。 [パブリック](migrate-support-matrix.md#supported-geographies-public-cloud)と [Government クラウド](migrate-support-matrix.md#supported-geographies-azure-government)でサポートされている地域を確認してください。

   ![プロジェクト名とリージョンのボックス](./media/tutorial-discover-physical/new-project.png)

7. **［作成］** を選択します
8. プロジェクトがデプロイされるまで数分待ちます。 **Azure Migrate: Discovery and Assessment** ツールは、新しいプロジェクトに既定で追加されます。

![既定で追加された Server Assessment ツールを示すページ](./media/tutorial-discover-physical/added-tool.png)

> [!NOTE]
> プロジェクトを既に作成している場合は、その同じプロジェクトを使用して追加のアプライアンスを登録することで、より多くのサーバーを検出して評価できます。[詳細をご覧ください](create-manage-projects.md#find-a-project)。

## <a name="set-up-the-appliance"></a>アプライアンスを設定する

Azure Migrate アプライアンスによって、サーバー検出が実行され、サーバーの構成とパフォーマンスのメタデータが Azure Migrate に送信されます。 このアプライアンスは、プロジェクトからダウンロード可能な PowerShell スクリプトを実行することで設定できます。

アプライアンスを設定するには、次のようにします。

1. アプライアンス名を指定し、ポータルでプロジェクト キーを生成します。
2. Azure portal から、Azure Migrate インストーラー スクリプトが含まれた ZIP ファイルをダウンロードします。
3. ZIP ファイルの内容を抽出します。 管理特権で PowerShell コンソールを起動します。
4. PowerShell スクリプトを実行して、アプライアンス Web アプリケーションを起動します。
5. アプライアンスを初めて構成し、プロジェクト キーを使用してプロジェクトに登録します。

### <a name="1-generate-the-project-key"></a>1. プロジェクト キーを生成する

1. **[移行の目標]**  >  **[サーバー]**  >  **[Azure Migrate: Discovery and Assessment]** で、 **[検出]** を選択します。
2. **[サーバーの検出]**  >  **[Are your servers virtualized?]\(サーバーは仮想化されていますか?\)** で、 **[物理またはその他 (AWS、GCP、Xen など)]** を選択します。
3. **[1: プロジェクト キーを生成します]** で、物理または仮想サーバーの検出用に設定する Azure Migrate アプライアンスの名前を指定します。 名前は、14 文字以下の英数字にする必要があります。
1. **[キーの生成]** をクリックして、必要な Azure リソースの作成を開始します。 リソースの作成中に [サーバーの検出] ページを閉じないでください。
1. Azure リソースが正常に作成されると、**プロジェクト キー** が生成されます。
1. このキーはアプライアンスを設定する際、登録を完了するために必要なので、コピーしておきます。

### <a name="2-download-the-installer-script"></a>2. インストーラー スクリプトをダウンロードする

**[2:Azure Migrate アプライアンスをダウンロードします]** で、 **[ダウンロード]** をクリックします。

### <a name="verify-security"></a>セキュリティを確認する

圧縮されたファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたサーバーで、管理者用のコマンド ウィンドウを開きます。
2. 次のコマンドを実行して、圧縮されたファイルのハッシュを生成します。
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用例 (パブリック クラウドの場合): ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256 ```
    - 使用例 (政府機関向けクラウドの場合): ```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256 ```
3.  最新のアプライアンス バージョンとハッシュ値を確認します。
    - パブリック クラウドの場合:

        **シナリオ** | **ダウンロード** _ | _ *ハッシュ値**
        --- | --- | ---
        物理 (85.8 MB) | [最新バージョン](https://go.microsoft.com/fwlink/?linkid=2140334) | ce5e6f0507936def8020eb7b3109173dad60fc51dd39c3bd23099bc9baaabe29

    - Azure Government の場合:

        **シナリオ** | **ダウンロード** _ | _ *ハッシュ値**
        --- | --- | ---
        物理 (85.8 MB) | [最新バージョン](https://go.microsoft.com/fwlink/?linkid=2140338) | ae132ebc574caf231bf41886891040ffa7abbe150c8b50436818b69e58622276
 

### <a name="3-run-the-azure-migrate-installer-script"></a>3. Azure Migrate インストーラー スクリプトを実行する
インストーラー スクリプトでは以下が実行されます。

- エージェントと、物理サーバーの検出と評価のための Web アプリケーションをインストールする。
- Windows の役割 (Windows Activation Service、IIS、PowerShell ISE など) をインストールする。
- IIS 書き込み可能モジュールをダウンロードしてインストールする。 [詳細については、こちらを参照してください](https://www.microsoft.com/download/details.aspx?id=7435)。
- Azure Migrate の永続的な設定の詳細でレジストリ キー (HKLM) を更新する。
- パスに次のファイルを作成する。
    - **構成ファイル**: %Programdata%\Microsoft Azure\Config
    - **ログ ファイル**: %Programdata%\Microsoft Azure\Logs

次のようにスクリプトを実行します。

1. アプライアンスをホストするサーバー上のフォルダーに ZIP ファイルを抽出します。  既存の Azure Migrate アプライアンスが存在するサーバー上でスクリプトを実行しないよう注意してください。
2. 管理 (昇格された) 特権を使用して上記のサーバーで PowerShell を起動します。
3. PowerShell ディレクトリを、ダウンロードした ZIP ファイルの内容が抽出されたフォルダーに変更します。
4. 次のコマンドを実行して、**AzureMigrateInstaller.ps1** という名前のスクリプトを実行します。

    - パブリック クラウドの場合: 
    
        ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 ```
    - Azure Government の場合: 
    
        ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```

    スクリプトが正常に終了すると、アプライアンス Web アプリケーションが起動します。

問題が発生した場合は、トラブルシューティングのために、C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log のスクリプト ログにアクセスできます。

### <a name="verify-appliance-access-to-azure"></a>アプライアンスによる Azure へのアクセスを確認する

[パブリック](migrate-appliance.md#public-cloud-urls) クラウドと[政府機関向け](migrate-appliance.md#government-cloud-urls)クラウドの Azure URL にアプライアンスから接続できることを確認します。

### <a name="4-configure-the-appliance"></a>4. アプライアンスを構成する

アプライアンスを初めて設定します。

1. アプライアンスに接続できる任意のサーバー上でブラウザーを開き、アプライアンス Web アプリの URL (**https://*アプライアンス名または IP アドレス*: 44368**) を開きます。

   または、アプリのショートカットをクリックして、デスクトップからアプリを開くこともできます。
2. **ライセンス条項** に同意し、サード パーティの情報を確認します。
1. Web アプリの **[前提条件のセットアップ]** で、以下を実行します。
    - **接続**:サーバーがインターネットにアクセスできることが、アプリによって確認されます。 サーバーでプロキシを使用する場合は、次の操作を行います。
        - **[プロキシの設定]** をクリックし、プロキシ アドレス (http://ProxyIPAddress または http://ProxyFQDN) の形式) とリッスン ポートを指定します。
        - プロキシで認証が必要な場合は、資格情報を指定します。
        - サポートされるのは HTTP プロキシのみです。
        - プロキシの詳細を追加した場合、またはプロキシまたは認証を無効にした場合は、 **[保存]** をクリックして接続チェックを再度トリガーします。
    - **時刻同期**:時刻が確認されます。 サーバーの検出を正常に機能させるには、アプライアンス上の時刻がインターネットの時刻と同期している必要があります。
    - **更新プログラムのインストール**:Azure Migrate: Discovery and Assessment によって、アプライアンスに最新の更新プログラムがインストールされていることが確認されます。 確認が完了したら、 **[View appliance services]\(アプライアンス サービスを表示\)** をクリックして、アプライアンスで実行されているコンポーネントの状態とバージョンを確認できます。

### <a name="register-the-appliance-with-azure-migrate"></a>Azure Migrate にアプライアンスを登録する

1. ポータルからコピーした **プロジェクト キー** を貼り付けます。 このキーがない場合は、 **[Azure Migrate: Discovery and Assessment] > [検出] > [既存のアプライアンスの管理]** に移動して、キーの生成時に指定したアプライアンス名を選択して、対応するキーをコピーします。
1. Azure で認証するには、デバイス コードが必要です。 **[ログイン]** をクリックすると、次に示すように、デバイス コードを含むモーダルが開きます。

    ![デバイス コードを示すモーダル](./media/tutorial-discover-vmware/device-code.png)

1. **[Copy code & Login]\(コードのコピーとログイン\)** をクリックしてデバイス コードをコピーし、新しいブラウザー タブで Azure ログイン プロンプトを開きます。表示されない場合は、ブラウザーでポップアップ ブロックを無効にしてあることを確認します。
1. 新しいタブで、デバイス コードを貼り付け、Azure のユーザー名とパスワードを使用してサインインします。
   
   PIN を使用したサインインはサポートされていません。
3. ログインせずにログイン タブを誤って閉じた場合は、アプライアンス構成マネージャーのブラウザー タブを最新の情報に更新して、[ログイン] ボタンを再度有効にする必要があります。
1. 正常にログインしたら、アプライアンス構成マネージャーで前のタブに戻ります。
4. ログに使用した Azure ユーザー アカウントに、キーの生成時に作成した Azure リソースに対する正しい[アクセス許可]()が付与されている場合、アプライアンスの登録が開始されます。
1. アプライアンスが正常に登録された後は、 **[詳細の表示]** をクリックすることで登録の詳細を確認できるようになります。


## <a name="start-continuous-discovery"></a>継続的な検出を開始する

次に、アプライアンスから検出対象の物理サーバーに接続し、検出を開始します。

1. **[ステップ 1:Provide credentials for discovery of Windows and Linux physical or virtual servers]\(Windows および Linux 物理サーバーまたは仮想サーバーを検出するための資格情報を指定する\)** で、 **[資格情報の追加]** をクリックします。
1. Windows サーバーの場合、ソースの種類として **[Windows サーバー]** を選択し、資格情報のフレンドリ名を指定して、ユーザー名とパスワードを追加します。 **[Save]** をクリックします。
1. Linux サーバーでパスワードベースの認証を使用している場合は、ソースの種類として **[Linux Server (Password-based)]\(Linux サーバー (パスワードベース)\)** を選択し、資格情報のフレンドリ名を指定して、ユーザー名とパスワードを追加します。 **[Save]** をクリックします。
1. Linux サーバーで SSH キーベースの認証を使用している場合は、ソースの種類として **[Linux Server (SSH key-based)]\(Linux サーバー (SSH キーベース)\)** を選択し、資格情報のフレンドリ名を指定して、ユーザー名を追加します。SSH 秘密キー ファイルを参照して選択します。 **[Save]** をクリックします。

    - Azure Migrate では、RSA、DSA、ECDSA、ed25519 の各アルゴリズムを使用して、ssh-keygen コマンドによって生成された SSH 秘密キーがサポートされています。
    - 現在、Azure Migrate では、パスフレーズベースの SSH キーはサポートされていません。 パスフレーズなしで SSH キーを使用してください。
    - 現在、Azure Migrate では、PuTTY によって生成された SSH 秘密キー ファイルはサポートされていません。
    - Azure Migrate では、次に示すように、OpenSSH 形式の SSH 秘密キー ファイルがサポートされています。
    
    ![SSH 秘密キーのサポートされている形式](./media/tutorial-discover-physical/key-format.png)

1. 複数の資格情報を一度に追加するには、 **[さらに追加]** をクリックして資格情報を保存して追加します。 物理サーバーの検出では、複数の資格情報がサポートされています。
1. **[Step 2:Provide physical or virtual server details]\(ステップ 2:物理サーバーまたは仮想サーバーの詳細を指定する\)** で、 **[Add discovery source]\(検出ソースの追加\)** をクリックして、サーバーの **[IP address/FQDN]\(IP アドレスまたは FQDN\)** と、サーバーに接続するための資格情報のフレンドリ名を指定します。
1. 一度に **1 つの項目を追加** するか、一括で **複数の項目を追加** することができます。 また、 **[Import CSV]\(CSV のインポート\)** を使用して、サーバーの詳細を指定することもできます。


    - **[Add single item]\(1 つの項目を追加\)** を選択した場合は、OS のタイプ、資格情報のフレンドリ名を指定し、サーバーの **IP アドレスまたは FQDN** を追加して、 **[保存]** をクリックします。
    - **[Add multiple items]\(複数の項目を追加\)** を選択した場合は、テキスト ボックスでサーバーの **[IP address/FQDN]\(IP アドレスまたは FQDN\)** を資格情報のフレンドリ名と共に指定することで、一度に複数のレコードを追加できます。 ** 追加したレコードを確認し、 **[保存]** をクリックします。
    - **[Import CSV]\(CSV のインポート\)** _(既定で選択)_ を選択した場合は、CSV テンプレート ファイルをダウンロードし、そのファイルにサーバーの **[IP address/FQDN]\(IP アドレスまたは FQDN\)** および資格情報のフレンドリ名を入力します。 その後、ファイルをアプライアンスにインポートし、ファイル内のレコードを **[Verify]\(検証\)** して、 **[保存]** をクリックします。

1. [保存] をクリックすると、アプライアンスによって追加されたサーバーへの接続が検証され、各サーバーの **[Validation status]\(検証状態\)** が表に表示されます。
    - サーバーの検証に失敗した場合は、表の [状態] 列にある **[Validation failed]\(検証に失敗しました\)** をクリックしてエラーを確認します。 問題を修正し、もう一度検証してください。
    - サーバーを削除するには、 **[削除]** をクリックします。
1. 検出を開始する前に、サーバーへの接続はいつでも **再検証** できます。
1. 正常に検証されたサーバーの検出を開始するには **[Start discovery]\(検出の開始\)** をクリックします。 検出が正常に開始されたら、各サーバーに対する検出の状態を表で確認できます。


これで検出が開始されます。 検出されたサーバーのメタデータが Azure portal に表示されるまでにサーバーあたり約 2 分かかります。

## <a name="verify-servers-in-the-portal"></a>ポータルでサーバーを確認する

検出の完了後、サーバーがポータルに表示されることを確認できます。

1. Azure Migrate ダッシュボードを開きます。
2. **[Azure Migrate - サーバー]**  >  **[Azure Migrate: Discovery and Assessment]** ページで、 **[検出済みサーバー]** の数を表示するアイコンをクリックします。
## <a name="next-steps"></a>次のステップ

- Azure VM への移行のために[物理サーバーを評価](tutorial-assess-physical.md)します。
- 検出中にアプライアンスによって収集される[データを確認](migrate-appliance.md#collected-data---physical)します。