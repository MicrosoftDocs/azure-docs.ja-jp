---
title: Azure Site Recovery で VMware VM または物理サーバーのディザスター リカバリー用のプロセス サーバーを管理する
description: この記事では、Azure Site Recovery を使用して、VMware VM または物理サーバーのディザスター リカバリー用のプロセス サーバーを管理する方法について説明します。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: a547a874c42d06d8453b154847561d8b5f0dabb8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019195"
---
# <a name="manage-process-servers"></a>プロセス サーバーの管理

この記事では、Site Recovery プロセス サーバーを管理する際の一般的なタスクについて説明します。

プロセス サーバーは、レプリケーション データを受け取り、最適化して、Azure に送信するために使用されます。 また、レプリケートする VMware VM および物理サーバーへのモビリティ サービスのプッシュ インストールと、オンプレミス マシンの自動検出も実行されます。 オンプレミスの VMware VM または物理サーバーを Azure にレプリケートする場合、プロセス サーバーは既定で構成サーバー マシンにインストールされます。 

- 大規模なデプロイでは、容量を拡張するためにオンプレミスのプロセス サーバーの追加が必要になる場合があります。
- Azure からオンプレミスへのフェールバックには、Azure に一時的なプロセス サーバーを設定する必要があります。 この VM は、フェールバックが完了したら削除できます。 

このプロセス サーバーの詳細について確認してください。


## <a name="upgrade-a-process-server"></a>プロセス サーバーをアップグレードする

プロセス サーバーをオンプレミスに展開する場合、またはフェールバック用の Azure VM としてデプロイする場合は、最新バージョンのプロセス サーバーがインストールされます。 Site Recovery チームは修正プログラムや拡張機能を定期的にリリースしているため、プロセス サーバーを最新の状態に維持することをお勧めします。 プロセス サーバーは次の手順でアップグレードできます。

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]


## <a name="move-vms-to-balance-the-process-server-load"></a>プロセス サーバーの負荷を分散するために VM を移動する

次のように、2 つのプロセス サーバー間で VM を移動して負荷を分散します。

1. コンテナーの **[管理]** で **[Site Recovery インフラストラクチャ]** をクリックします。 **[For VMware & Physical machines]\(VMware と物理マシン\)** の **[構成サーバー]** をクリックします。
2. プロセス サーバーが登録されている構成サーバーをクリックします。
3. トラフィックを負荷分散するプロセス サーバーをクリックします。

    ![トラフィックを負荷分散できるプロセス サーバーを示すスクリーンショット。](media/vmware-azure-manage-process-server/LoadBalance.png)

4. **[負荷分散]** をクリックして、マシンの移動先のターゲット プロセス サーバーを選択します。 次に、 **[OK]** をクリックします

    ![[ターゲット プロセス サーバーの選択] が選択されている [負荷分散] ウィンドウを示すスクリーンショット。](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. **[マシンの選択]** をクリックし、現在のプロセス サーバーからターゲットに移動するマシンを選択します。 各仮想マシンについて、平均データの変化の詳細が表示されます。 次に、 **[OK]** をクリックします 
3. コンテナーの **[監視]**  >  **[Site Recovery ジョブ]** でジョブの進行状況を監視します。

変更がポータルに反映されるまでには約 15 分かかります。 すぐに反映するには、[構成サーバーを更新します](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。

## <a name="switch-an-entire-workload-to-another-process-server"></a>ワークロード全体を別のプロセス サーバーに切り替える

次のように、プロセス サーバーによって処理されるワークロード全体を別のプロセス サーバーに移動します。

1. コンテナーの **[管理]** で **[Site Recovery インフラストラクチャ]** をクリックします。 **[For VMware & Physical machines]\(VMware と物理マシン\)** の **[構成サーバー]** をクリックします。
2. プロセス サーバーが登録されている構成サーバーをクリックします。
3. ワークロードを切り替えるプロセス サーバーをクリックします。
4. **[切り替え]** をクリックし、ワークロードを移動するターゲット プロセス サーバーを選択します。 次に、 **[OK]** をクリックします

    ![[ターゲット プロセス サーバーの選択] ウィンドウを示すスクリーンショット。](media/vmware-azure-manage-process-server/Switch.PNG)

5. コンテナーの **[監視]**  >  **[Site Recovery ジョブ]** でジョブの進行状況を監視します。

変更がポータルに反映されるまでには約 15 分かかります。 すぐに反映するには、[構成サーバーを更新します](vmware-azure-manage-configuration-server.md#refresh-configuration-server)。

## <a name="register-a-master-target-server"></a>マスター ターゲット サーバーを登録する

マスター ターゲット サーバーは、構成サーバーとスケールアウト プロセス サーバーにあります。 これは構成サーバーに登録されている必要があります。 この登録でエラーが発生した場合は、保護された項目の正常性に影響を与える可能性があります。 マスター ターゲット サーバーを構成サーバーに登録するには、登録が必要な特定の構成サーバーまたはスケールアウト プロセス サーバーにログインします。 **%PROGRAMDATA%\ASR\Agent** フォルダーに移動し、管理者コマンド プロンプトで以下を実行します。

   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="reregister-a-process-server"></a>プロセス サーバーを再登録する

次のように、オンプレミスまたは Azure VM 上で実行されているプロセス サーバーを構成サーバーに登録します。

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

設定を保存した後、次を実行します。

1. プロセス サーバーで、管理者のコマンド プロンプトを開きます。
2. フォルダー **%PROGRAMDATA%\ASR\Agent** を参照し、次のコマンドを実行します。

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>オンプレミスのプロセス サーバーのプロキシ設定を変更する

オンプレミス プロセス サーバーがプロキシを使用して Azure に接続している場合は、次のようにプロキシ設定を変更できます。

1. プロセス サーバー マシンにサインインします。 
2. 管理者の PowerShell コマンド ウィンドウを開き、次のコマンドを実行します。
   ```powershell
   $pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
   net stop obengine
   net start obengine
   ```
2. フォルダー **%PROGRAMDATA%\ASR\Agent** を参照し、次のコマンドを実行します。
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="remove-a-process-server"></a>プロセス サーバーを削除する

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="exclude-folders-from-anti-virus-software"></a>ウイルス対策ソフトウェアからフォルダーを除外する

スケールアウト プロセス サーバー (またはマスター ターゲット サーバー) 上でウイルス対策ソフトウェアが実行されている場合は、次のフォルダーをウイルス対策操作から除外します。


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- プロセス サーバーのインストール ディレクトリ。 次に例を示します。C:\Program Files (x86)\Microsoft Azure Site Recovery