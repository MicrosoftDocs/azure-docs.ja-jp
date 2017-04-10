---
title: "モビリティ サービスのインストール (VMware または物理から Azure へ) | Microsoft Docs"
description: "オンプレミスのコンピューターを保護するモビリティ サービス エージェントをインストールする方法について説明します。"
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
ms.date: 2/20/2017
ms.author: anoopkv
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 79beda244007649253fb7130f0403f587fcf76bf
ms.lasthandoff: 03/21/2017

---

# <a name="install-mobility-service-vmware-or-physical-to-azure"></a>モビリティ サービスのインストール (VMware または物理から Azure へ)
Azure Site Recovery モビリティ サービスは、コンピューター上のデータの書き込みをキャプチャし、プロセス サーバーに転送します。 モビリティ サービスは、Azure にレプリケートするすべてのコンピューター (VMware VM または物理サーバー) にデプロイします。 次のメソッドを使用して保護するサーバーにモビリティ サービスをデプロイできます。


* [System Center Configuration Manager などのソフトウェア デプロイメント ツールを使用してモビリティ サービスをインストールする](site-recovery-install-mobility-service-using-sccm.md)
* [Azure Automation と Desired State Configuration (Automation DSC) を使用してモビリティ サービスをインストールする](site-recovery-automate-mobility-service-install.md)
* [グラフィカル ユーザー インターフェイス (GUI) を使用して、モビリティ サービスを手動でインストールする](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui)
* [コマンド プロンプトで、モビリティ サービスを手動でインストールする](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-at-a-command-prompt)
* [Azure Site Recovery からのプッシュ インストールを使用してモビリティ サービスをインストールする](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery)


>[!IMPORTANT]
> バージョン 9.7.0.0 以降、Windows 仮想マシン (VM) では、モビリティ サービスのインストーラーによって、公開されている最新の [Azure VM エージェント](../virtual-machines/virtual-machines-windows-extensions-features.md#azure-vm-agent)もインストールされます。 コンピューターが Azure にフェールオーバーされたときに、コンピューターは VM 拡張機能を使用するためのエージェント インストール前提条件を満たしています。

## <a name="prerequisites"></a>前提条件
モビリティ サービスをサーバーに手動でインストールする前に、次の前提条件を完了してください。
1. 構成サーバーにサインインし、コマンド プロンプト ウィンドウを管理者として開きます。
2. bin フォルダーに移動し、パスフレーズ ファイルを作成します。

    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```
3. 安全な場所にパスフレーズ ファイルを格納してください。 モビリティ サービスのインストール中にファイルを使用します。
4. サポートされているすべてのオペレーティング システムのモビリティ サービスのインストーラーは、%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository フォルダーにあります。

### <a name="mobility-service-installer-to-operating-system-mapping"></a>モビリティ サービスのインストーラーからオペレーティング システムへのマッピング

| インストーラー ファイルのテンプレート名| オペレーティング システム |
|---|--|
|Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2008 R2 SP1 (64 ビット) </br> Windows Server 2012 (64 ビット) </br> Windows Server 2012 R2 (64 ビット) |
|Microsoft-ASR\_UA\*RHEL6-64*release.tar.gz| Red Hat Enterprise Linux (RHEL) 6.4、6.5、6.6、6.7、6.8 (64 ビットのみ) </br> CentOS 6.4、6.5、6.6、6.7、6.8 (64 ビットのみ) |
|Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 (64 ビットのみ)|
|Microsoft-ASR_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4、6.5 (64 ビットのみ)|


## <a name="install-mobility-service-manually-by-using-the-gui"></a>GUI を使用して、モビリティ サービスを手動でインストールする

>[!NOTE]
> GUI ベースのインストールは、Windows オペレーティング システムでのみ機能します。

[!INCLUDE [site-recovery-install-mob-svc-gui](../../includes/site-recovery-install-mob-svc-gui.md)]

## <a name="install-mobility-service-manually-at-a-command-prompt"></a>コマンド プロンプトで、モビリティ サービスを手動でインストールする

### <a name="command-line-installation-on-a-windows-computer"></a>Windows コンピューターへのコマンドライン インストール
[!INCLUDE [site-recovery-install-mob-svc-win-cmd](../../includes/site-recovery-install-mob-svc-win-cmd.md)]

### <a name="command-line-installation-on-a-linux-computer"></a>Linux コンピューターへのコマンドライン インストール
[!INCLUDE [site-recovery-install-mob-svc-lin-cmd](../../includes/site-recovery-install-mob-svc-lin-cmd.md)]


## <a name="install-mobility-service-by-push-installation-from-azure-site-recovery"></a>Azure Site Recovery からのプッシュ インストールを使用してモビリティ サービスをインストールする
Site Recovery を使用して、モビリティ サービスのプッシュ インストールを実行するには、すべてのターゲット コンピューターで、次の前提条件が満たされている必要があります。

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-win](../../includes/site-recovery-prepare-push-install-mob-svc-win.md)]

[!INCLUDE [site-recovery-prepare-push-install-mob-svc-lin](../../includes/site-recovery-prepare-push-install-mob-svc-lin.md)]


> [!NOTE]
モビリティ サービスがインストールされたら、Azure Portal で、**[レプリケート]** ボタンを選択し、これらの VM の保護を開始します。

## <a name="uninstall-mobility-service-on-a-windows-server-computer"></a>Windows Server コンピューターのモビリティ サービスをアンインストールする
Windows Server コンピューターのモビリティ サービスをアンインストールするには、次のメソッドのいずれかを使用します。

### <a name="uninstall-by-using-the-gui"></a>GUI を使用してアンインストールする
1. コントロール パネルで、**[プログラム]** をクリックします。
2. **Microsoft Azure Site Recovery Mobility Service/マスター ターゲット サーバー**を選択して **[アンインストール]** を選択します。

### <a name="uninstall-at-a-command-prompt"></a>コマンド プロンプトでアンインストールする
1. 管理者としてコマンド プロンプト ウィンドウを開きます。
2. モビリティ サービスをアンインストールするには、次のコマンドを実行します。

```
MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
```

## <a name="uninstall-mobility-service-on-a-linux-computer"></a>Linux コンピューターのモビリティ サービスをアンインストールする
1. Linux サーバーで、**root** ユーザーとしてサインインします。
2. ターミナルで /user/local/ASR に移動します。
3. モビリティ サービスをアンインストールするには、次のコマンドを実行します。

```
uninstall.sh -Y
```

