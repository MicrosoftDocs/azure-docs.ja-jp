---
title: Azure Site Recovery を使用した VMware VM および物理サーバーの Azure へのディザスター リカバリーのためにプロセス サーバーを管理する | Microsoft Docs
description: この記事では、Azure Site Recovery を使用して、VMware VM および物理サーバーを Azure にディザスター リカバリーするために設定されたプロセス サーバーを管理する方法について説明します。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: ramamill
ms.openlocfilehash: ba80c8ce57495eaa46e915cb0c472eb4aabcee57
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57863629"
---
# <a name="manage-process-servers"></a>プロセス サーバーの管理

既定では、VMware VM または物理サーバーを Azure にレプリケートするときに使用されるプロセス サーバーはオンプレミスの構成サーバー マシンにインストールされます。 個別のプロセス サーバーを設定することが必要な例がいくつか存在します。

- 大規模なデプロイでは、容量を拡張するためにオンプレミスのプロセス サーバーの追加が必要になる場合があります。
- フェールバックには、Azure で設定された一時的なプロセス サーバーが必要です。 この VM は、フェールバックが完了したら削除できます。 

この記事では、これらの追加のプロセス サーバーのための標準的な管理タスクを要約しています。

## <a name="upgrade-a-process-server"></a>プロセス サーバーをアップグレードする

オンプレミスで、または (フェールバックの目的で) Azure で実行されているプロセス サーバーを次のようにアップグレードします。

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

> [!NOTE]
>   通常、フェールバックの目的で Azure でプロセス サーバーを作成するために Azure ギャラリー イメージを使用する場合、そのサーバーは使用可能な最新バージョンを実行しています。 Site Recovery チームは修正プログラムや拡張機能を定期的にリリースしているため、プロセス サーバーを最新の状態に維持することをお勧めします。

## <a name="balance-the-load-on-process-server"></a>プロセス サーバーの負荷を分散させる

2 つのプロセス サーバーの間で負荷を分散させるには

1. **[Recovery Services コンテナー]** > **[管理]** > **[Site Recovery Infrastructure]\(Site Recovery インフラストラクチャ\)** > **[For VMware & Physical machines]\(VMware および物理マシン\)** > **[構成サーバー]** の順に移動します。
2. プロセス サーバーが登録されている構成サーバーをクリックします。
3. ページでは、構成サーバーに登録されているプロセス サーバーの一覧を利用できます。
4. ワークロードを変更するプロセス サーバーをクリックします。

    ![LoadBalance](media/vmware-azure-manage-process-server/LoadBalance.png)

5. 以下で説明するように、必要に応じて、**[負荷分散]** または **[切り替え]** オプションを使用できます。

### <a name="load-balance"></a>負荷分散

このオプションでは、1 つまたは複数の仮想マシンを選択し、別のプロセス サーバーに転送できます。

1. **[負荷分散]** をクリックし、ドロップダウンからターゲット プロセス サーバーを選択します。 **[OK]**

    ![LoadPS](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. **[コンピューターの選択]** をクリックして、現在のプロセス サーバーからターゲット プロセス サーバーに移動する仮想マシンを選択します。 各仮想マシンについて、平均データの変化の詳細が表示されます。
3. Click **OK**. **[Recovery Services コンテナー]** > **[監視]** > **[Site Recovery jobs]\(Site Recovery ジョブ\)** で、ジョブの進行状況を監視します。
4. この操作の正常完了後に変更が反映されるまで 15 分かかります。または、すぐに反映するには[構成サーバーを更新](vmware-azure-manage-configuration-server.md#refresh-configuration-server)します。

### <a name="switch"></a>Switch

このオプションでは、プロセス サーバーで保護されているすべてのワークロードが、別のプロセス サーバーに移動されます。

1. **[切り替え]** をクリックし、ターゲット プロセス サーバーを選択して、**[OK]** をクリックします。

    ![Switch](media/vmware-azure-manage-process-server/Switch.PNG)

2. **[Recovery Services コンテナー]** > **[監視]** > **[Site Recovery jobs]\(Site Recovery ジョブ\)** で、ジョブの進行状況を監視します。
3. この操作の正常完了後に変更が反映されるまで 15 分かかります。または、すぐに反映するには[構成サーバーを更新](vmware-azure-manage-configuration-server.md#refresh-configuration-server)します。

## <a name="reregister-a-process-server"></a>プロセス サーバーを再登録する

オンプレミスで、または Azure で実行されているプロセス サーバーを構成サーバーに再登録する必要がある場合は、次を実行します。

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

プロセス サーバーがプロキシを使用して Azure の Site Recovery に接続しているとき、既存のプロキシ設定を変更する必要がある場合は、この手順を使用します。

1. プロセス サーバー マシンにログオンします。 
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

## <a name="manage-anti-virus-software-on-process-servers"></a>プロセス サーバーでウイルス対策ソフトウェアを管理する

スタンドアロン プロセス サーバーまたはマスター ターゲット サーバー上でウイルス対策ソフトウェアがアクティブになっている場合は、次のフォルダーをウイルス対策操作から除外します。


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- プロセス サーバー インストール ディレクトリ。例:C:\Program Files (x86)\Microsoft Azure Site Recovery

