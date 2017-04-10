---
title: " Azure Site Recovery での構成サーバーの管理 | Microsoft Docs"
description: "この記事では、構成サーバーを設定して管理する方法について説明します。"
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 2/14/2017
ms.author: anoopkv
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 3a02171d52b6d963a93a654d2506269e29f1be8e
ms.lasthandoff: 03/18/2017

---

# <a name="manage-a-configuration-server"></a>構成サーバーの管理

構成サーバーは、Site Recovery サービスとオンプレミス インフラストラクチャの調整役として機能します。 この記事では、構成サーバーを設定、構成、管理する方法について説明します。

## <a name="prerequisites"></a>前提条件
次の表は、構成サーバーの設定に最低限必要なハードウェア、ソフトウェア、ネットワーク構成を示したものです。

> [!NOTE]
> [容量計画](site-recovery-capacity-planner.md)は、負荷要件を満たす構成で構成サーバーをデプロイするための重要なステップです。 詳細については、「[構成サーバーのサイズ要件](#sizing-requirements-for-a-configuration-server)」を参照してください。

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="downloading-the-configuration-server-software"></a>構成サーバー ソフトウェアのダウンロード
1. Azure Portal にログオンし、Recovery Services コンテナーを参照します。
2. **[Site Recovery インフラストラクチャ]** > **[構成サーバー]** ([For VMware & Physical Machines (VMware および物理マシン)] の下) に移動します。

  ![[サーバーの追加] ページ](./media/site-recovery-vmware-to-azure-manage-configuration-server/AddServers.png)
3. **[+ サーバー]** ボタンをクリックします。
4. **[サーバーの追加]** ページで、[ダウンロード] をクリックして登録キーをダウンロードします。 このキーは、構成サーバーのインストール中に Azure Site Recovery サービスに登録するために必要になります。
5. **[Download the Microsoft Azure Site Recovery Unified Setup (Microsoft Azure Site Recovery 統合セットアップのダウンロード)]** リンクをクリックして構成サーバーの最新バージョンをダウンロードします。

  ![ダウンロード ページ](./media/site-recovery-vmware-to-azure-manage-configuration-server/downloadcs.png)

  > [!TIP]
  構成サーバーの最新バージョンは、[Microsoft ダウンロード センターのダウンロード ページ](http://aka.ms/unifiedsetup)から直接ダウンロードできます。

## <a name="installing-and-registering-a-configuration-server-from-gui"></a>GUI を使用した構成サーバーのインストールと登録
[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

## <a name="installing-and-registering-a-configuration-server-using-command-line"></a>コマンド ラインを使用した構成サーバーのインストールと登録

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>使用例
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="configuration-server-installer-command-line-arguments"></a>構成サーバーのインストーラーのコマンドライン引数
[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]


### <a name="create-a-mysql-credentials-file"></a>MySql の資格情報ファイルを作成する
MySQLCredsFilePath パラメーターは、入力としてファイルを受け取ります。 次の形式を使用してファイルを作成し、これを入力 MySQLCredsFilePath パラメーターとして渡します。
```
[MySQLCredentials]
MySQLRootPassword = "Password>"
MySQLUserPassword = "Password"
```
### <a name="create-a-proxy-settings-configuration-file"></a>プロキシ設定構成ファイルを作成する
ProxySettingsFilePath パラメーターは、入力としてファイルを受け取ります。 次の形式を使用してファイルを作成し、これを入力 ProxySettingsFilePath パラメーターとして渡します。

```
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```
## <a name="modifying-proxy-settings-for-configuration-server"></a>構成サーバーのプロキシ設定の変更
1. 構成サーバーにログインします。
2. ショートカットを使用して cspsconfigtool.exe を起動します。
3. **[Vault Registration (コンテナーの登録)]** タブをクリックします。
4. ポータルから新しいコンテナー登録ファイルをダウンロードし、これをツールへの入力として指定します。

  ![register-configuration-server](./media/site-recovery-vmware-to-azure-manage-configuration-server/register-csonfiguration-server.png)
5. 新しいプロキシ サーバーの詳細を入力し、**[登録]** をクリックします。
6. 管理者の PowerShell コマンド ウィンドウを開きます。
7. 次のコマンドを実行します。
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```

  >[!WARNING]
  スケールアウト プロセス サーバーがこの構成サーバーに接続されている場合は、デプロイ内の[すべてのスケールアウト プロセス サーバーでプロキシ設定を修正する](site-recovery-vmware-to-azure-manage-scaleout-process-server.md#modifying-proxy-settings-for-scale-out-process-server)必要があります。

## <a name="re-register-a-configuration-server-with-the-same-recovery-services-vault"></a>同じ Recovery Services コンテナーを使用した構成サーバーの再登録
  1. 構成サーバーにログインします。
  2. ショートカットを使用して cspsconfigtool.exe を起動します。
  3. **[Vault Registration (コンテナーの登録)]** タブをクリックします。
  4. ポータルから新しい登録ファイルをダウンロードし、これをツールへの入力として指定します。
        ![register-configuration-server](./media/site-recovery-vmware-to-azure-manage-configuration-server/register-csonfiguration-server.png)
  5. プロキシ サーバーの詳細を入力し、**[登録]** をクリックします。  
  6. 管理者の PowerShell コマンド ウィンドウを開きます。
  7. 次のコマンドを実行します。

      ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
      net stop obengine
      net start obengine
      ```

  >[!WARNING]
  この構成サーバーにスケールアウト プロセス サーバーが接続されている場合は、デプロイ内の[すべてのスケールアウト プロセス サーバーを再登録する](site-recovery-vmware-to-azure-manage-scaleout-process-server.md#re-registering-a-scale-out-process-server)必要があります。

## <a name="registering-a-configuration-server-with-a-different-recovery-services-vault"></a>異なる Recovery Services コンテナーを使用した構成サーバーの登録
1. 構成サーバーにログインします。
2. 管理者のコマンド プロンプトで、次のコマンドを実行します

```
reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
net stop dra
```
3. ショートカットを使用して cspsconfigtool.exe を起動します。
4. **[Vault Registration (コンテナーの登録)]** タブをクリックします。
5. ポータルから新しい登録ファイルをダウンロードし、これをツールへの入力として指定します。

    ![register-configuration-server](./media/site-recovery-vmware-to-azure-manage-configuration-server/register-csonfiguration-server.png)
6. プロキシ サーバーの詳細を入力し、**[登録]** をクリックします。  
7. 管理者の PowerShell コマンド ウィンドウを開きます。
8. 次のコマンドを実行します。
    ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="decommissioning-a-configuration-server"></a>構成サーバーの使用停止
構成サーバーの使用停止操作を始める前に、次のことを確認します。
1. この構成サーバーのすべての仮想マシンの保護を無効にします。
2. 構成サーバーからすべてのレプリケーション ポリシーの関連付けを解除します。
3. 構成サーバーに関連付けられているすべての vCenter サーバー/vSphere ホストを削除します。

### <a name="delete-the-configuration-server-from-azure-portal"></a>Azure Portal から構成サーバーを削除する
1. Azure Portal で、[コンテナー] メニューから **[Site Recovery インフラストラクチャ]** > **[構成サーバー]** に移動します。
2. 使用停止にする構成サーバーをクリックします。
3. 構成サーバーの詳細ページで、[削除] をクリックします。

  ![delete-configuration-server](./media/site-recovery-vmware-to-azure-manage-configuration-server/delete-configuration-server.PNG)
4. **[はい]** をクリックして、サーバーの削除を確認します。

  >[!WARNING]
  この構成サーバーに仮想マシン、複製ポリシー、または vCenter サーバー/vSphere ホストが関連付けられている場合は、サーバーを削除できません。 コンテナーを削除する前に、これらのエンティティを削除してください。

### <a name="uninstall-the-configuration-server-software-and-its-dependencies"></a>構成サーバー ソフトウェアとその依存関係をアンインストールする
  > [!TIP]
  Azure Site Recovery で構成サーバーを再利用する予定がある場合は、手順 4. に直接スキップできます。

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

## <a name="renew-configuration-server-secure-socket-layerssl-certificates"></a>構成サーバーの Secure Socket Layer (SSL) 証明書の更新
構成サーバーには Web サーバーが組み込まれていて、この Web サーバーにより、構成サーバーに接続されたモビリティ サービス、プロセス サーバー、マスター ターゲット サーバーのアクティビティが調整されます。 構成サーバーの Web サーバーは、SSL 証明書を使用してそのクライアントを認証します。 この証明書の有効期限は 3 年間で、次の方法でいつでも更新できます。

> [!WARNING]
証明書の有効期限は、バージョン 9.4.XXXX.X 以上でのみ適用できます。 証明書の更新ワークフローを開始する前に、すべての Azure Site Recovery コンポーネント (構成サーバー、プロセス サーバー、マスター ターゲット サーバー、モビリティ サービス) をアップグレードしてください。

1. Azure Portal で、[コンテナー]、[Site Recovery インフラストラクチャ]、[構成サーバー] の順に移動します。
2. SSL 証明書を更新する必要がある構成サーバーをクリックします。
3. SSL 証明書の有効期限は、[構成サーバーの正常性] で確認できます。
4. 次の図に示すように、**[証明書の更新]** アクションをクリックして証明書を更新します。

  ![delete-configuration-server](./media/site-recovery-vmware-to-azure-manage-configuration-server/renew-cert-page.png)

### <a name="secure-socket-layer-certificate-expiry-warning"></a>Secure Socket Layer 証明書の有効期限の警告

> [!NOTE]
2016 年 5 月以前に発生したすべてのインストールの SSL 証明書は、有効期間が 1 年間に設定されていました。 そのため、証明書の有効期限切れに関する通知が Azure Portal に表示されます。

1. 構成サーバーの SSL 証明書の有効期限が切れる 2 か月前になると、Azure Portal と電子メール (Azure Site Recovery 通知をサブスクライブする必要があります) を介してユーザーへの通知が開始されます。 コンテナーのリソース ページに通知バナーが表示されます。

  ![certificate-notification](./media/site-recovery-vmware-to-azure-manage-configuration-server/ssl-cert-renew-notification.png)
2. バナーをクリックすると、証明書の有効期限の詳細が表示されます。

  ![certificate-details](./media/site-recovery-vmware-to-azure-manage-configuration-server/ssl-cert-expiry-details.png)

  >[!TIP]
  **[今すぐ更新]** の代わりに **[今すぐアップグレード]** が表示される場合があります。 現在の環境にまだ 9.4.xxxx.x 以上のバージョンにアップグレードされていないコンポーネントがあることを示します。

## <a name="sizing-requirements-for-a-configuration-server"></a>構成サーバーのサイズ要件

| **CPU** | **メモリ** | **キャッシュ ディスク サイズ** | **データの変更率** | **保護されたマシン** |
| --- | --- | --- | --- | --- |
| 8 vCPU (2 ソケット * 4 コア @ 2.5 GHz) |16 GB |300 GB |500 GB 以下 |100 台未満のマシンをレプリケートします。 |
| 12 vCPU (2 ソケット * 6 コア @ 2.5 GHz) |18 GB |600 GB |500 GB ～ 1 TB |100 ～ 150 台のマシンをレプリケートします。 |
| 16 vCPU (2 ソケット * 8 コア @ 2.5 GHz) |32 GB |1 TB (テラバイト) |1 TB ～ 2 TB |150 ～ 200 台のマシンをレプリケートします。 |

  >[!TIP]
  毎日のデータの変化が 2 TB を超える場合、または 200 を超える仮想マシンをレプリケートする予定がある場合は、レプリケーション トラフィックの負荷を分散するために追加のプロセス サーバーをデプロイすることをお勧めします。 スケールアウト プロセス サーバーをデプロイする方法を確認してください。


## <a name="common-issues"></a>一般的な問題
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

