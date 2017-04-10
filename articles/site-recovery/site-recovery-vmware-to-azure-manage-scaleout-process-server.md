---
title: " Azure Site Recovery でのスケールアウト プロセス サーバーの管理 | Microsoft Docs"
description: "この記事では、Azure Site Recovery でスケールアウト プロセス サーバーの設定方法および管理方法について説明します。"
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
ms.sourcegitcommit: cabbce12a07720c37375092962ee1f89c32269ef
ms.openlocfilehash: 03bb87bdbf3dce07a282323f484d0aefae8bad62
ms.lasthandoff: 02/22/2017

---

# <a name="manage-a-scale-out-process-server"></a>スケールアウト プロセス サーバーの管理

スケールアウト プロセス サーバーは、Site Recovery サービスとオンプレミス インフラストラクチャ間でのデータ転送の調整役として機能します。 この記事では、スケールアウト プロセス サーバーを設定、構成、管理する方法について説明します。

## <a name="prerequisites"></a>前提条件
次の表に、スケールアウト プロセス サーバーの設定に最低限必要なハードウェア、ソフトウェア、ネットワークの構成を示します。

> [!NOTE]
> [容量計画](site-recovery-capacity-planner.md)は、負荷要件を満たす構成でスケールアウト プロセス サーバーをデプロイするための重要なステップです。 [スケールアウト プロセス サーバーのスケーリングの特徴](#sizing-requirements-for-a-configuration-server)の詳細を確認してください。

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="downloading-the-scale-out-process-server-software"></a>スケール アウト プロセス サーバー ソフトウェアのダウンロード
1. Azure Portal にログオンし、Recovery Services コンテナーを参照します。
2. **[Site Recovery インフラストラクチャ]** > **[構成サーバー]** ([For VMware & Physical Machines] (VMware および物理マシン) の下) に移動します。
3. 構成サーバーを選択して、構成サーバーの詳細ページに移動します。
4. **[+ プロセス サーバー]** ボタンをクリックします。
5. **[プロセス サーバーの追加]** ページの **[プロセス サーバーのデプロイ先を選択してください]** ボックスで、**[スケールアウト プロセス サーバーをオンプレミスにデプロイします]** オプションを選択します。

  ![[サーバーの追加] ページ](./media/site-recovery-vmware-to-azure-manage-scaleout-process-server/add-process-server.png)
6. **[Download the Microsoft Azure Site Recovery Unified Setup]** (Microsoft Azure Site Recovery 統合セットアップのダウンロード) リンクをクリックして、スケールアウト プロセス サーバーの最新バージョンをダウンロードします。

  > [!WARNING]
  スケールアウト プロセス サーバーのバージョンは、ご使用の環境で実行されている構成サーバーのバージョン以下である必要があります。 バージョンの互換性を保つ簡単な方法は、構成サーバーのインストール/更新で最近使用したものと同じインストーラーを使用することです。

## <a name="installing-and-registering-a-scale-out-process-server-from-gui"></a>GUI を使用したスケールアウト プロセス サーバーのインストールと登録
ソース マシンが 200 台を超えるデプロイ、または合計日次変更率が 2 TB を超えるデプロイでスケールアウトする必要がある場合、トラフィック ボリュームに対応するためにプロセス サーバーを追加する必要があります。

「[プロセス サーバーのサイズの推奨事項](#size-recommendations-for-the-process-server)」を確認し、次の手順に従ってプロセス サーバーをセットアップします。 サーバーをセットアップしたら、サーバーを使用するソース マシンを移行します。

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="installing-and-registering-a-scale-out-process-server-using-command-line"></a>コマンドラインを使用したスケールアウト プロセス サーバーのインストールと登録

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

### <a name="sample-usage"></a>使用例
```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMWare" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```

### <a name="scale-out-process-server-installer-command-line-arguments"></a>スケールアウト プロセス サーバー インストーラーのコマンドライン引数
[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]


### <a name="create-a-proxy-settings-configuration-file"></a>プロキシ設定構成ファイルを作成する
ProxySettingsFilePath パラメーターは、入力としてファイルを受け取ります。 次の形式を使用してファイルを作成し、これを入力 ProxySettingsFilePath パラメーターとして渡します。
```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```
## <a name="modifying-proxy-settings-for-scale-out-process-server"></a>スケール アウト プロセス サーバーのプロキシ設定の変更
1. スケールアウト プロセス サーバーにログインします。
2. 管理者として PowerShell コマンド ウィンドウを開きます。
3. 次のコマンドを実行します。
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```
4. 次に、**%PROGRAMDATA%\ASR\Agent** ディレクトリに移動して次のコマンドを実行します。
  ```
  cmd
  cdpcli.exe --registermt
  
  net stop obengine

  net start obengine

  exit
  ```

## <a name="re-registering-a-scale-out-process-server"></a>スケールアウト プロセス サーバーの再登録
[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

* 次に、管理者のコマンド プロンプトを開きます。
* **%PROGRAMDATA%\ASR\Agent** ディレクトリを参照し、次のコマンドを実行します。

```
cdpcli.exe --registermt

net stop obengine

net start obengine
```

## <a name="upgrading-a-scale-out-process-server"></a>スケールアウト プロセス サーバーのアップグレード
[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

## <a name="decommissioning-a-scale-out-process-server"></a>スケールアウト プロセス サーバーの使用停止
1. 次のことを確認します。
  - Azure Portal で構成サーバーの接続状態が **[接続済み]** と表示されている。
  - プロセス サーバーが構成サーバーと通信できている。
2. 管理者としてプロセス サーバーにログインします。
3. コントロール パネルを開き、[プログラム]、[プログラムのアンインストール] の順に移動します。
4. 次に示す順序でプログラムをアンインストールします。
  * Microsoft Azure Site Recovery 構成サーバー/プロセス サーバー
  * Microsoft Azure Site Recovery 構成サーバーの依存関係
  * Microsoft Azure Recovery Services エージェント

プロセス サーバーの削除結果が Azure Portal に反映されるまでには最大で 15 分かかります。

  > [!NOTE]
  プロセス サーバーが構成サーバーと通信できない (ポータルで接続状態が [切断] になっている) 場合、次の手順に従って構成サーバーからプロセス サーバーを消去する必要があります。

## <a name="unregistering-a-disconnected-scale-out-process-server-from-a-configuration-server"></a>構成サーバーから切断されているスケールアウト プロセス サーバーの登録解除

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="sizing-requirements-for-a-scale-out-process-server"></a>スケール アウト プロセス サーバーのサイズ要件

| **追加のプロセス サーバー** | **キャッシュ ディスク サイズ** | **データの変更率** | **保護されたマシン** |
| --- | --- | --- | --- |
|4 vCPU (2 ソケット * 2 コア @ 2.5 GHz)、8 GB メモリ |300 GB |250 GB 以下 |85 台以下のマシンをレプリケートします。 |
|8 vCPU (2 ソケット * 4 コア @ 2.5 GHz)、12 GB メモリ |600 GB |250 GB ～ 1 TB |85 ～ 150 台のマシンをレプリケートします。 |
|12 vCPU (2 ソケット * 6 コア @ 2.5 GHz)、24 GB メモリ |1 TB (テラバイト) |1 TB ～ 2 TB |150 ～ 225 台のマシンをレプリケートします。 |

