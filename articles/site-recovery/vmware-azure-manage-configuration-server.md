---
title: Azure Site Recovery でのディザスター リカバリーのために構成サーバーを管理する
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: ramamill
ms.openlocfilehash: 2f1edc14efdeaf70bf4c2acc0e31e1517753ed3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92546349"
---
# <a name="manage-the-configuration-server-for-vmware-vmphysical-server-disaster-recovery"></a>VMware VM/物理サーバー ディザスター リカバリー用の構成サーバーを管理する

Azure への VMware 仮想マシンと物理サーバーのディザスター リカバリーに [Azure Site Recovery](site-recovery-overview.md) を使うときは、オンプレミスの構成サーバーを設定します。 構成サーバーは、オンプレミスの VMware と Azure の間の通信を調整し、データのレプリケーションを管理します。 この記事は、展開後に構成サーバーを管理するための一般的なタスクをまとめたものです。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-windows-license"></a>Windows ライセンスを更新する

OVF テンプレートに付属するライセンスは、180 日間有効な評価版ライセンスです。 中断なく使用するには、購入したライセンスで Windows をライセンス認証する必要があります。 ライセンスの更新は、スタンドアロン キーまたは KMS 標準キーのいずれかを使用して行うことができます。 ガイダンスは、[OS を実行するための DISM Windows コマンド ライン](/windows-hardware/manufacture/desktop/dism-windows-edition-servicing-command-line-options)で入手できます。 キーを取得するには、「[KMS クライアント セットアップ キー](/windows-server/get-started/kmsclientkeys)」を参照してください。

## <a name="access-configuration-server"></a>構成サーバーにアクセスする

次のように構成サーバーにアクセスすることができます。

* デプロイ先の VM にサインインし、デスクトップのショートカットから **Azure Site Recovery 構成マネージャー** を起動します。
* または、 https://*ConfigurationServerName*/:44315/ から構成サーバーにリモートでアクセスできます。 管理者の資格情報でサインインします。

## <a name="modify-vmware-server-settings"></a>VMware サーバーの設定を変更する

1. 別の VMware サーバーを構成サーバーと関連付けるには、[サインイン](#access-configuration-server)後に **[vCenter Server/vSphere ESXi サーバーの追加]** を選択します。
2. 詳細を入力し、 **[OK]** を選択します。

## <a name="modify-credentials-for-automatic-discovery"></a>自動検出用の資格情報を変更する

1. VMware VM の自動検出のための VMware サーバーへの接続に使う資格情報を更新するには、[サインイン](#access-configuration-server)後にアカウントを選択し、 **[編集]** をクリックします。
2. 新しい資格情報を入力して、 **[OK]** を選択します。

    ![VMware の変更](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)

CSPSConfigtool.exe を使用して資格情報を変更することもできます。

1. 構成サーバーにログインして、CSPSConfigtool.exe を起動します
2. 変更するアカウントを選択し、 **[編集]** をクリックします。
3. 変更した資格情報を入力して、 **[OK]** をクリックします

## <a name="modify-credentials-for-mobility-service-installation"></a>モビリティ サービス インストール用の資格情報を変更する

レプリケーションを有効にする VMware VM にモビリティ サービスを自動インストールするために使う資格情報を変更します。

1. [サインイン](#access-configuration-server)後に、 **[仮想マシンの資格情報の管理]** を選択します
2. 変更するアカウントを選択し、 **[編集]** をクリックします
3. 新しい資格情報を入力して、 **[OK]** を選択します。

    ![モビリティ サービスの資格情報を変更する](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

CSPSConfigtool.exe を使用して資格情報を変更することもできます。

1. 構成サーバーにログインして、CSPSConfigtool.exe を起動します
2. 変更するアカウントを選択し、 **[編集]** をクリックします
3. 新しい資格情報を入力して、 **[OK]** をクリックします。

## <a name="add-credentials-for-mobility-service-installation"></a>モビリティ サービス インストール用の資格情報を追加する

構成サーバーの OVF デプロイ時に資格情報を追加しなかった場合は、

1. [サインイン](#access-configuration-server)後に、 **[仮想マシンの資格情報の管理]** を選択します。
2. **[仮想マシンの資格情報の追加]** をクリックします。
    ![[仮想マシンの資格情報の追加] リンクがある [仮想マシンの資格情報の管理] ウィンドウを示すスクリーンショット。](media/vmware-azure-manage-configuration-server/add-mobility-credentials.png)
3. 新しい資格情報を入力して、 **[追加]** をクリックします。

CSPSConfigtool.exe を使用して資格情報を追加することもできます。

1. 構成サーバーにログインして、CSPSConfigtool.exe を起動します
2. **[追加]** をクリックし、新しい資格情報を入力して、 **[OK]** をクリックします。

## <a name="modify-proxy-settings"></a>プロキシの設定を変更する

Azure へのインターネット アクセスのために構成サーバー マシンが使うプロキシの設定を変更します。 構成サーバー マシンで実行されている既定のプロセス サーバーだけでなく、プロセス サーバー マシンがある場合は、両方のマシンで設定を変更します。

1. 構成サーバーに [サインイン](#access-configuration-server)した後で、 **[接続の管理]** を選択します。
2. プロキシの値を更新します。 次に、 **[保存]** を選択して、設定を更新します。

## <a name="add-a-network-adapter"></a>ネットワーク アダプターを追加する

Open Virtualization Format (OVF) テンプレートは、ネットワーク アダプターが 1 つの構成サーバー VM を展開します。

- [VM にさらにアダプターを追加する](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter)ことはできますが、構成サーバーをコンテナーに登録する前に追加する必要があります。
- 構成サーバーをコンテナーに登録した後でアダプターを追加するには、VM のプロパティでアダプターを追加します。 次に、サーバーをコンテナーに[再登録する](#reregister-a-configuration-server-in-the-same-vault)必要があります。

## <a name="how-to-renew-ssl-certificates"></a>SSL 証明書を更新する方法

構成サーバーには、それに接続されているすべての保護されたマシン、組み込み/スケールアウト プロセス サーバー、マスター ターゲット サーバー上のモビリティ エージェントのアクティビティを調整する、組み込みの Web サーバーが含まれています。 Web サーバーは、SSL 証明書を使ってクライアントを認証します。 証明書は 3 年で有効期限が切れ、いつでも更新できます。

### <a name="check-expiry"></a>有効期限を確認する

**[Configuration Server の正常性]** に有効期限日が表示されます。 2016 年 5 月より前の構成サーバーの展開では、証明書の有効期限は 1 年間に設定されていました。 証明書の有効期限が近づくと、次のようになります。

- 有効期限が 2 か月以内になると、サービスはポータルとメール (Site Recovery 通知に登録している場合) で通知の送信を開始します。
- コンテナーのリソース ページに通知バナーが表示されます。 詳細については、バナーを選択してください。
- **[今すぐアップグレード]** ボタンが表示される場合は、現在の環境にまだ 9.4.xxxx.x 以上のバージョンにアップグレードされていないコンポーネントがあることを示しています。 証明書を更新する前に、コンポーネントをアップグレードしてください。 古いバージョンでは更新できません。

### <a name="if-certificates-are-yet-to-expire"></a>証明書の有効期限がまだ切れていない場合

1. 更新するには、コンテナーで、 **[Site Recovery インフラストラクチャ]**  >  **[構成サーバー]** を開きます。 必要な構成サーバーを選択します。
2. スケールアウト プロセス サーバー、マスター ターゲット サーバーのすべてのコンポーネント、すべての保護されたマシン上のモビリティ エージェントが最新バージョンであり、かつ接続状態にあることを確認します。
3. 次に、 **[証明書の更新]** を選択します。
4. このページの手順に注意して従い、[OK] をクリックして、選択された構成サーバーとそれに関連付けられたコンポーネントの証明書を更新します。

### <a name="if-certificates-have-already-expired"></a>証明書の有効期限が既に切れている場合

1. 有効期限が切れると、証明書を **Azure portal から更新することはできません**。 先に進む前に、スケールアウト プロセス サーバー、マスター ターゲット サーバーのすべてのコンポーネント、すべての保護されたマシン上のモビリティ エージェントが最新バージョンであり、かつ接続状態にあることを確認します。
2. **証明書の有効期限が既に切れている場合にのみ、この手順に従ってください。** 構成サーバーにログインし、C ドライブ > [Program Data] > [Site Recovery] > [home] > [svsystems] > [bin] に移動して、管理者として "RenewCerts" 実行ツールを実行します。
3. PowerShell の実行ウィンドウがポップアップ表示され、証明書の更新がトリガーされます。 この処理には最大 15 分かかることがあります。 更新の完了までウィンドウを閉じないでください。

:::image type="content" source="media/vmware-azure-manage-configuration-server/renew-certificates.png" alt-text="証明書の更新":::

## <a name="reregister-a-configuration-server-in-the-same-vault"></a>同じコンテナーに構成サーバーを登録する

必要な場合は、同じコンテナーに構成サーバーを再登録することができます。 構成サーバー マシンで実行されている既定のプロセス サーバーだけでなく、他のプロセス サーバー マシンがある場合は、両方のマシンを再登録します。


1. コンテナーで、 **[管理]**  >  **[Site Recovery インフラストラクチャ]**  >  **[構成サーバー]** を開きます。
2. **[サーバー]** で **[登録キーのダウンロード]** を選択して、コンテナーの資格情報ファイルをダウンロードします。
3. 構成サーバー マシンにサインインします。
4. **%ProgramData%\ASR\home\svsystems\bin** で、**cspsconfigtool.exe** を開きます。
5. **[Vault Registration]\(コンテナーの登録\)** タブで **[参照]** を選択して、ダウンロードしたコンテナー資格情報ファイルを探します。
6. 必要な場合は、プロキシ サーバーの詳細を指定します。 次に、 **[登録]** を選択します。
7. 管理者の PowerShell コマンド ウィンドウを開き、次のコマンドを実行します。
   ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
   ```

    >[!NOTE]
    >構成サーバーからスケールアウト プロセス サーバーに **最新の証明書を取得する** には、コマンド *"\<Installation Drive\Microsoft Azure Site Recovery\agent\cdpcli.exe>"--registermt* を実行します

8. 最後に、次のコマンドを実行して obengine を再起動します。
   ```
        net stop obengine
        net start obengine
   ```


## <a name="register-a-configuration-server-with-a-different-vault"></a>構成サーバーを別のコンテナーに登録する

> [!WARNING]
> 以下の手順では、現在のコンテナーとの構成サーバーの関連付けを解除します。それにより構成サーバーの下で保護されているすべての仮想マシンのレプリケーションが停止されます。

1. 構成サーバーにログインします。
2. 管理者の PowerShell コマンド ウィンドウを開き、次のコマンドを実行します。

    ```
    reg delete "HKLM\Software\Microsoft\Azure Site Recovery\Registration"
    net stop dra
    ```
3. デスクトップのショートカットを使用して、構成サーバーのアプライアンス ブラウザー ポータルを起動します。
4. 新しい構成サーバーの[登録](vmware-azure-tutorial.md#register-the-configuration-server)と同様の登録手順に従います。

## <a name="upgrade-the-configuration-server"></a>構成サーバーをアップグレードする

構成サーバーを更新するには、更新プログラムのロールアップを実行します。 更新は N-4 までのバージョンに適用できます。 次に例を示します。

- 9\.7、9.8、9.9、または 9.10 を実行している場合は、9.11 に直接アップグレードできます。
- 9\.6 以前を実行している場合に、9.11 にアップグレードするには、まずバージョン 9.7 にアップグレードしてから、 9\.11 にアップグレードする必要があります。

Azure Site Recovery コンポーネントのサポート ステートメントに関する詳細なガイダンスについては、[こちら](./service-updates-how-to.md#support-statement-for-azure-site-recovery)を参照してください。
すべてのバージョンの構成サーバーにアップグレードするための更新プログラムのロールアップへのリンクは、[こちら](./service-updates-how-to.md#links-to-currently-supported-update-rollups)にあります。

> [!IMPORTANT]
> Azure Site Recovery コンポーネントの新バージョン "N" がリリースされるたびに、"N - 4" よりも前のすべてのバージョンはサポート対象外と見なされます。 常に使用可能な最新バージョンにアップグレードすることをお勧めします。</br>
> Azure Site Recovery コンポーネントのサポート ステートメントに関する詳細なガイダンスについては、[こちら](./service-updates-how-to.md#support-statement-for-azure-site-recovery)を参照してください。

次のようにサーバーをアップグレードします。

1. コンテナーで、 **[管理]**  >  **[Site Recovery インフラストラクチャ]**  >  **[構成サーバー]** に移動します。
2. 更新プログラムがある場合は、 **[エージェントのバージョン]** 列にリンクが表示されます。
    ![アップデート](./media/vmware-azure-manage-configuration-server/update2.png)
3. 更新プログラムのインストーラー ファイルを構成サーバーにダウンロードします。

    ![クリックして更新プログラムのインストーラー ファイルをダウンロードする場所を示すスクリーンショット。](./media/vmware-azure-manage-configuration-server/update1.png)

4. インストーラーをダブルクリックして実行します。
5. インストーラーがマシン上で実行中の現在のバージョンを検出します。 **[はい]** をクリックしてアップグレードを開始します。
6. アップグレードの完了時に、サーバー構成が検証されます。

    ![完成したサーバー検証構成を示すスクリーンショット。](./media/vmware-azure-manage-configuration-server/update3.png)

7. **[完了]** をクリックしてインストーラーを閉じます。
8. その他の Site Recovery コンポーネントをアップグレードするには、[アップグレードのガイダンス](./service-updates-how-to.md#vmware-vmphysical-server-disaster-recovery-to-azure)に関する記事を参照してください。

## <a name="upgrade-configuration-serverprocess-server-from-the-command-line"></a>コマンド ラインから構成サーバー/プロセス サーバーをアップグレードする

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
MySQLRootPassword = "Password>"
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

## <a name="delete-or-unregister-a-configuration-server"></a>構成サーバーを削除または登録解除する

1. 構成サーバーの下にあるすべての VM の[保護を無効化](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)します。
2. 構成サーバーからすべてのレプリケーション ポリシーの[関連付けを解除](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy)して、レプリケーション ポリシーを[削除](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy)します。
3. 構成サーバーに関連付けられているすべての vCenter サーバー/vSphere ホストを[削除](vmware-azure-manage-vcenter.md#delete-a-vcenter-server)します。
4. コンテナーで、 **[Site Recovery インフラストラクチャ]**  >  **[構成サーバー]** を開きます。
5. 削除する構成サーバーを選択します。 次に、 **[詳細]** ページで、 **[削除]** を選択します。

    ![構成サーバーを削除する](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)


### <a name="delete-with-powershell"></a>PowerShell で削除する

必要に応じて、PowerShell を使って構成サーバーを削除できます。

1. Azure PowerShell モジュールを[インストール](/powershell/azure/install-Az-ps)します。
2. 次のコマンドを使用して Azure アカウントにサインインします。

    `Connect-AzAccount`
3. コンテナーのサブスクリプションを選びます。

     `Get-AzSubscription –SubscriptionName <your subscription name> | Select-AzSubscription`
3.  コンテナーのコンテキストを設定します。

    ```
    $vault = Get-AzRecoveryServicesVault -Name <name of your vault>
    Set-AzSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. 構成サーバーを検索します。

    `$fabric = Get-AzSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. 構成サーバーを削除します。

    `Remove-AzSiteRecoveryFabric -Fabric $fabric [-Force]`

> [!NOTE]
> Remove-AzSiteRecoveryFabric で **-Force** オプションを使うと、構成サーバーを強制的に削除できます。

## <a name="generate-configuration-server-passphrase"></a>構成サーバーのパスフレーズを生成する

1. 構成サーバーにサインインし、コマンド プロンプト ウィンドウを管理者として開きます。
2. bin フォルダーにディレクトリを変更するには、コマンド **cd %ProgramData%\ASR\home\svsystems\bin** を実行します。
3. パスフレーズ ファイルを生成するには、**genpassphrase.exe -v > MobSvc.passphrase** を実行します。
4. **%ProgramData%\ASR\home\svsystems\bin\MobSvc.passphrase** にあるファイルにパスフレーズが格納されます。

## <a name="refresh-configuration-server"></a>構成サーバーを最新の情報に更新する

1. Azure portal で、 **[Recovery Services コンテナー]**  >  **[管理]**  >  **[Site Recovery Infrastructure]\(Site Recovery インフラストラクチャ\)**  >  **[For VMware & Physical machines]\(VMware および物理マシン\)**  >  **[構成サーバー]** の順に移動します。
2. 最新の情報に更新する構成サーバーをクリックします。
3. 選択した構成サーバーの詳細を含むブレードで、 **[More]\(詳細\)**  >  **[サーバーを最新の情報に更新する]** をクリックします。
4. **[Recovery Services コンテナー]**  >  **[監視]**  >  **[Site Recovery jobs]\(Site Recovery ジョブ\)** で、ジョブの進行状況を監視します。

## <a name="failback-requirements"></a>フェールバックの要件

再保護とフェールバック中には、オンプレミスの構成サーバーが実行中で、かつその接続状態である必要があります。 フェールバックが成功するには、障害が発生している仮想マシンが構成サーバーのデータベースに存在している必要があります。

必ず、構成サーバーを定期的にバックアップするようスケジュールを設定します。 障害が発生して構成サーバーが失われた場合は、まず構成サーバーをバックアップ コピーから復元する必要があります。また、復元された構成サーバーの IP アドレスがコンテナーに登録されている IP アドレスと同じであることを確認します。 復元された構成サーバーに別の IP アドレスが使用されている場合、フェールバックは機能しません。

## <a name="next-steps"></a>次のステップ

Azure への [VMware VM](vmware-azure-tutorial.md) のディザスター リカバリーの設定に関するチュートリアルをご覧ください。