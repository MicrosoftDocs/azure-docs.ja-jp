---
title: Azure Site Recovery で物理サーバーの構成サーバーを管理する
description: この記事では、物理サーバーの Azure へのディザスター リカバリーのために Azure Site Recovery 構成サーバーを管理する方法について説明します。
services: site-recovery
author: mayurigupta13
ms.service: site-recovery
ms.topic: article
ms.date: 02/28/2019
ms.author: mayg
ms.openlocfilehash: ff612b7c052ead5658ea4bbfafd7aace51ba3c02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017442"
---
# <a name="manage-the-configuration-server-for-physical-server-disaster-recovery"></a>物理サーバー ディザスター リカバリー用の構成サーバーの管理

Azure への物理サーバーのディザスター リカバリーに [Azure Site Recovery](site-recovery-overview.md) サービスを使うときは、オンプレミスの構成サーバーを設定します。 構成サーバーは、オンプレミスのマシンと Azure の間の通信を調整し、データのレプリケーションを管理します。 この記事は、展開後に構成サーバーを管理するための一般的なタスクをまとめたものです。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

オンプレミスの構成サーバー マシンをデプロイするための前提条件を表にまとめます。

| **コンポーネント** | **要件** |
| --- |---|
| CPU コア数| 8 |
| RAM | 16 GB|
| ディスクの数 | 3、OS ディスク、プロセス サーバーのキャッシュ ディスク、フェールバック用リテンション ドライブを含みます |
| ディスクの空き領域 (プロセス サーバー キャッシュ) | 600 GB
| ディスクの空き領域 (リテンション ディスク) | 600 GB|
| オペレーティング システム  | Windows Server 2012 R2 <br> Windows Server 2016 |
| オペレーティング システムのロケール | 英語 (米国)|
| VMware vSphere PowerCLI のバージョン | 必要なし|
| Windows Server の役割 | これらの役割を有効にしないでください。 <br> - Active Directory Domain Services <br>- インターネット インフォメーション サービス <br> - Hyper-V |
| グループ ポリシー| これらのグループ ポリシーを有効にしないでください。 <br> - コマンド プロンプトへのアクセス禁止 <br> - レジストリ編集ツールへのアクセス禁止 <br> - ファイル添付の信頼ロジック <br> - スクリプト実行の有効化 <br> [詳細情報](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))|
| IIS | - 既存の Web サイトが存在しない <br> - [匿名認証](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10))を有効にする <br> - [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) 設定を有効にする  <br> - ポート 443 でリッスンしている既存の Web サイト/アプリケーションが存在しない<br>|
| NIC の種類 | VMXNET3 (VMware VM としてデプロイされている場合) |
| IP アドレスの種類 | 静的 |
| インターネットへのアクセス | サーバーは、次の URL にアクセスできる必要があります。 <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - `https://management.azure.com` <br> - *.services.visualstudio.com <br> - https://dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi (スケールアウト プロセス サーバーには必要なし) <br> - time.nist.gov <br> - time.windows.com |
| Port | 443 (コントロール チャネルのオーケストレーション)<br>9443 (データ転送)|

## <a name="download-the-latest-installation-file"></a>最新のインストール ファイルのダウンロード

構成サーバーのインストール ファイルの最新バージョンは、Site Recovery ポータルで入手できます。 また、[Microsoft ダウンロード センター](https://aka.ms/unifiedsetup)から直接ダウンロードできます。

1. Azure Portal にログオンし、Recovery Services コンテナーを参照します。
2. **[Site Recovery インフラストラクチャ]**  >  **[構成サーバー]** \([For VMware & Physical Machines] \(VMware および物理マシン) の下) に移動します。
3. **[+ サーバー]** ボタンをクリックします。
4. **[サーバーの追加]** ページで、[ダウンロード] をクリックして登録キーをダウンロードします。 このキーは、構成サーバーのインストール中に Azure Site Recovery サービスに登録するために必要になります。
5. **[Download the Microsoft Azure Site Recovery Unified Setup (Microsoft Azure Site Recovery 統合セットアップのダウンロード)]** リンクをクリックして構成サーバーの最新バージョンをダウンロードします。

   ![ダウンロード ページ](./media/physical-manage-configuration-server/downloadcs.png)


## <a name="install-and-register-the-server"></a>サーバーをインストールして登録する

1. 統合セットアップ インストール ファイルを実行します。
2. **[開始する前に]** で **[Install the configuration server and process server]\(構成サーバーとプロセス サーバーをインストールする\)** を選択します。

    ![開始する前に](./media/physical-manage-configuration-server/combined-wiz1.png)

3. **[Third-Party Software License (サードパーティ製ソフトウェア ライセンス)]** で、 **[同意する]** をクリックして MySQL をダウンロードし、インストールします。
4. **[インターネット設定]** で、構成サーバーで実行されているプロバイダーがインターネット経由で Azure Site Recovery に接続する方法を指定します。 必要な URL へのアクセスが許可されていることを確認してください。

    - マシンで現在セットアップされているプロキシを使用して接続する場合は、 **[プロキシ サーバーを使用して Azure Site Recovery に接続する]** を選択します。
    - プロバイダーから直接接続するように指定する場合は、 **[プロキシを使用せずに直接 Azure Site Recovery に接続する]** を選択します。
    - 既存のプロキシで認証が必要な場合、またはプロバイダー接続にカスタム プロキシを使用する場合は、 **[Connect with custom proxy setting]\(カスタム プロキシ設定を使用して接続する\)** を選択して、アドレス、ポート、資格情報を指定します。
     ![ファイアウォール](./media/physical-manage-configuration-server/combined-wiz4.png)
6. **[前提条件の確認]** では、インストールを実行できることを確認するためのチェックが実行されます。 **グローバル時刻の同期チェック** に関する警告が表示された場合は、システム クロックの時刻 ( **[日付と時刻]** 設定) がタイム ゾーンと同じであることを確認します。

    ![前提条件](./media/physical-manage-configuration-server/combined-wiz5.png)
7. **[MySQL Configuration (MySQL の構成)]** で、インストールする MySQL サーバー インスタンスにログオンするための資格情報を作成します。

    ![MySQL](./media/physical-manage-configuration-server/combined-wiz6.png)
8. **[環境の詳細]** で、VMware VM をレプリケートするかどうかを選択します。 レプリケートする場合、PowerCLI 6.0 がインストールされているかどうかが確認されます。
9. **[インストール場所]** で、バイナリをインストールしキャッシュを格納する場所を選択します。 選択するドライブには使用可能なディスク領域が 5 GB 以上必要ですが、600 GB 以上の空き領域があるキャッシュ ドライブを使用することをお勧めします。

    ![インストール場所](./media/physical-manage-configuration-server/combined-wiz8.png)
10. **[ネットワークの選択]** で、最初に、モビリティ サービスの検出とソース マシンへのプッシュ インストールのために組み込みプロセス サーバーによって使用される NIC を選択し、次に、構成サーバーによって Azure との接続に使用される NIC を選択します。 既定では、ポート 9443 がレプリケーション トラフィックの送受信用に使用されます。このポート番号は、実際の環境の要件に合わせて変更できます。 ポート 9443 に加え、ポート 443 も開きます。このポートは、Web サーバーがレプリケーション操作を調整するために使用されます。 ポート 443 はレプリケーション トラフィックの送受信用に使用しないでください。

    ![[ネットワークの選択]](./media/physical-manage-configuration-server/combined-wiz9.png)


11. **[概要]** で情報を確認し、 **[インストール]** をクリックします。 インストールが完了すると、パスフレーズが生成されます。 このパスフレーズは、レプリケーションを有効にするときに必要になるので、コピーしてセキュリティで保護された場所に保管してください。


登録が完了すると、コンテナーの **[設定]**  >  **[サーバー]** ブレードに、サーバーが表示されます。


## <a name="install-from-the-command-line"></a>コマンドラインからインストールする

インストール ファイルを次のように実行します。

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>使用例
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="parameters"></a>パラメーター

|パラメーター名| Type | 説明| 値|
|-|-|-|-|
| /ServerMode|必須|構成サーバーとプロセス サーバーの両方をインストールするか、プロセス サーバーだけをインストールするかを指定します。|CS<br>PS|
|/InstallLocation|必須|コンポーネントがインストールされているフォルダー。| コンピューター上の任意のフォルダー|
|/MySQLCredsFilePath|必須|MySQL サーバーの資格情報が保存されているファイルのパス。|ファイルは下記の形式である必要があります。|
|/VaultCredsFilePath|必須|コンテナーの資格情報ファイルのパス。|有効なファイル パス|
|/EnvType|必須|保護する環境の種類 |VMware<br>NonVMware|
|/PSIP|必須|レプリケーション データの転送に使用する NIC の IP アドレス。| 任意の有効な IP アドレス|
|/CSIP|必須|構成サーバーがリッスンする NIC の IP アドレス。| 任意の有効な IP アドレス|
|/PassphraseFilePath|必須|パスフレーズ ファイルの場所の完全パス。|有効なファイル パス|
|/BypassProxy|省略可能|構成サーバーがプロキシを介さずに Azure に接続することを指定します。|この値は Venu から取得します。|
|/ProxySettingsFilePath|省略可能|プロキシ設定 (認証を必要とする既定のプロキシ、またはカスタム プロキシ)。|ファイルは下記の形式である必要があります。|
|DataTransferSecurePort|省略可能|レプリケーション データに使用する PSIP のポート番号。| 有効なポート番号 (既定値は 9433)|
|/SkipSpaceCheck|省略可能|キャッシュ ディスクの領域チェックをスキップします。| |
|/AcceptThirdpartyEULA|必須|サード パーティのライセンス条項への同意を意味するフラグ。| |
|/ShowThirdpartyEULA|省略可能|サード パーティのライセンス条項を表示します。 入力として提供された場合、他のすべてのパラメーターが無視されます。| |



### <a name="create-file-input-for-mysqlcredsfilepath"></a>MYSQLCredsFilePath のファイル入力を作成する

MySQLCredsFilePath パラメーターは、入力としてファイルを受け取ります。 次の形式を使用してファイルを作成し、これを入力 MySQLCredsFilePath パラメーターとして渡します。
```ini
[MySQLCredentials]
MySQLRootPassword = "Password"
MySQLUserPassword = "Password"
```
### <a name="create-file-input-for-proxysettingsfilepath"></a>ProxySettingsFilePath のファイル入力を作成する
ProxySettingsFilePath パラメーターは、入力としてファイルを受け取ります。 次の形式を使用してファイルを作成し、これを入力 ProxySettingsFilePath パラメーターとして渡します。

```ini
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```
## <a name="modify-proxy-settings"></a>プロキシの設定を変更する

次のように、構成サーバー マシンのプロキシ設定を変更できます。

1. 構成サーバーにログオンします。
2. デスクトップのショートカットを使用して cspsconfigtool.exe を起動します。
3. **[Vault Registration (コンテナーの登録)]** タブをクリックします。
4. ポータルから新しいコンテナー登録ファイルをダウンロードし、これをツールへの入力として指定します。

   ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. 新しいプロキシの詳細を入力し、 **[登録]** をクリックします。
6. 管理者として PowerShell コマンド ウィンドウを開きます。
7. 次のコマンドを実行します。

   ```powershell
   $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
   net stop obengine
   net start obengine
   ```

   > [!WARNING]
   > 追加のプロセス サーバーがこの構成サーバーに接続されている場合は、デプロイ内の[すべてのスケールアウト プロセス サーバーでプロキシ設定を修正する](vmware-azure-manage-process-server.md#modify-proxy-settings-for-an-on-premises-process-server)必要があります。

## <a name="reregister-a-configuration-server-with-the-same-vault"></a>同じコンテナーに構成サーバーを再登録する
1. 構成サーバーにログインします。
2. デスクトップのショートカットを使用して cspsconfigtool.exe を起動します。
3. **[Vault Registration (コンテナーの登録)]** タブをクリックします。
4. ポータルから新しい登録ファイルをダウンロードし、これをツールへの入力として指定します。
      ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. プロキシ サーバーの詳細を入力し、 **[登録]** をクリックします。  
6. 管理者として PowerShell コマンド ウィンドウを開きます。
7. 次のコマンドを実行します。

    ```powershell
    $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
    net stop obengine
    net start obengine
    ```

   > [!WARNING]
   > 複数のプロセス サーバーがある場合は、[それらを再登録](vmware-azure-manage-process-server.md#reregister-a-process-server)必要があります。

## <a name="register-a-configuration-server-with-a-different-vault"></a>構成サーバーを別のコンテナーに登録する

> [!WARNING]
> 以下の手順では、現在のコンテナーとの構成サーバーの関連付けを解除します。それにより構成サーバーの下で保護されているすべての仮想マシンのレプリケーションが停止されます。

1. 構成サーバー マシンにログオンします
2. 管理者のコマンド プロンプトで、次のコマンドを実行します。

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. デスクトップのショートカットを使用して cspsconfigtool.exe を起動します。
4. **[Vault Registration (コンテナーの登録)]** タブをクリックします。
5. ポータルから新しい登録ファイルをダウンロードし、これをツールへの入力として指定します。
6. プロキシ サーバーの詳細を入力し、 **[登録]** をクリックします。  
7. 管理者として PowerShell コマンド ウィンドウを開きます。
8. 次のコマンドを実行します。
    ```powershell
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="upgrade-a-configuration-server"></a>構成サーバーをアップグレードする

構成サーバーを更新するには、更新プログラムのロールアップを実行します。 更新は N-4 までのバージョンに適用できます。 次に例を示します。

- 9\.7、9.8、9.9、または 9.10 を実行している場合は、9.11 に直接アップグレードできます。
- 9\.6 以前を実行している場合に、9.11 にアップグレードするには、まずバージョン 9.7 にアップグレードしてから、 9\.11 にアップグレードする必要があります。

すべてのバージョンの構成サーバーにアップグレードするための更新プログラムのロールアップへのリンクは、[wiki の更新プログラムのページ](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx)にあります。

次のようにサーバーをアップグレードします。

1. 更新プログラムのインストーラー ファイルを構成サーバーにダウンロードします。
2. インストーラーをダブルクリックして実行します。
3. インストーラーがマシン上で実行中の現在のバージョンを検出します。
4. **[OK]** をクリックして確認し、アップグレードを実行します。 


## <a name="delete-or-unregister-a-configuration-server"></a>構成サーバーを削除または登録解除する

> [!WARNING]
> 構成サーバーの使用停止操作を始める前に、次のことを確認します。
> 1. この構成サーバーのすべての仮想マシンの[保護を無効化](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)します。
> 2. 構成サーバーからすべてのレプリケーション ポリシーの[関連付けを解除](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy)し、レプリケーション ポリシーを[削除](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy)します。
> 3. 構成サーバーに関連付けられているすべての vCenter サーバー/vSphere ホストを[削除](vmware-azure-manage-vcenter.md#delete-a-vcenter-server)します。


### <a name="delete-the-configuration-server-from-azure-portal"></a>Azure Portal から構成サーバーを削除する
1. Azure Portal で、[コンテナー] メニューから **[Site Recovery インフラストラクチャ]**  >  **[構成サーバー]** に移動します。
2. 使用停止にする構成サーバーをクリックします。
3. 構成サーバーの詳細ページで、 **[削除]** をクリックします。
4. **[はい]** をクリックして、サーバーの削除を確認します。

### <a name="uninstall-the-configuration-server-and-its-dependencies"></a>構成サーバーとその依存関係をアンインストールする
> [!TIP]
>   Azure Site Recovery で構成サーバーを再利用する予定がある場合は、手順 4. に直接スキップできます。

1. 管理者として構成サーバーにログオンします。
2. コントロール パネルを開き、[プログラム]、[プログラムのアンインストール] の順に移動します。
3. 次の順序でプログラムをアンインストールします。
   * Microsoft Azure Recovery Services エージェント
   * Microsoft Azure Site Recovery Mobility Service/マスター ターゲット サーバー
   * Microsoft Azure Site Recovery プロバイダー
   * Microsoft Azure Site Recovery 構成サーバー/プロセス サーバー
   * Microsoft Azure Site Recovery 構成サーバーの依存関係
   * MySQL Server 5.5
4. 管理者のコマンド プロンプトで、次のコマンドを実行します。
   ```
   reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
   ```

## <a name="delete-or-unregister-a-configuration-server-powershell"></a>構成サーバーの削除または登録解除 (PowerShell)

1. Azure PowerShell モジュールを[インストール](/powershell/azure/install-Az-ps)します。
2. 次のコマンドを使用して、Azure アカウントにログインします。
    
    `Connect-AzAccount`
3. コンテナーが存在するサブスクリプションを選択します。

     `Get-AzSubscription –SubscriptionName <your subscription name> | Select-AzSubscription`
3.  コンテナーのコンテキストを設定します。
    
    ```powershell
    $Vault = Get-AzRecoveryServicesVault -Name <name of your vault>
    Set-AzSiteRecoveryVaultSettings -ARSVault $Vault
    ```
4. 構成サーバーを取得して選択します。

    `$Fabric = Get-AzSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. 構成サーバーを削除します。

    `Remove-AzSiteRecoveryFabric -Fabric $Fabric [-Force]`

> [!NOTE]
> Remove-AzSiteRecoveryFabric の **-Force** オプションを使用すると、構成サーバーを強制的に削除できます。

## <a name="renew-tlsssl-certificates"></a>TLS/SSL 証明書を更新する
構成サーバーには Web サーバーが組み込まれていて、この Web サーバーにより、構成サーバーに接続されたモビリティ サービス、プロセス サーバー、マスター ターゲット サーバーのアクティビティが調整されます。 Web サーバーは、TLS/SSL 証明書を使ってクライアントを認証します。 証明書は 3 年で有効期限が切れ、いつでも更新できます。

### <a name="check-expiry"></a>有効期限を確認する

2016 年 5 月より前の構成サーバーの展開では、証明書の有効期限は 1 年間に設定されていました。 証明書の有効期限が近づくと、次のようになります。

- 有効期限が 2 か月以内になると、サービスはポータルとメール (Azure Site Recovery 通知に登録している場合) で通知の送信を開始します。
- コンテナーのリソース ページに通知バナーが表示されます。 バナーをクリックすると詳細が表示されます。
- **[今すぐアップグレード]** ボタンが表示される場合は、現在の環境にまだ 9.4.xxxx.x 以上のバージョンにアップグレードされていないコンポーネントがあることを示します。 証明書を更新する前に、コンポーネントをアップグレードします。 古いバージョンでは更新できません。

### <a name="renew-the-certificate"></a>証明書を更新する

1. コンテナーで、 **[Site Recovery インフラストラクチャ]**  >  **[構成サーバー]** を開き、必要な構成サーバーをクリックします。
2. **[Configuration Server の正常性]** に有効期限日が表示されます
3. **[証明書の更新]** をクリックします。 




## <a name="common-issues"></a>一般的な問題
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>次のステップ

Azure への[物理サーバー](./physical-azure-disaster-recovery.md)のディザスター リカバリーの設定に関するチュートリアルをご覧ください。
