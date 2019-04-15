---
title: Azure Site Recovery を使用した VMware VM と物理サーバーのディザスター リカバリーのためのモビリティ サービスについて | Microsoft Docs
description: Azure Site Recovery を使用して VMware VM と物理サーバーを Azure にディザスター リカバリーするためのモビリティ サービス エージェントについて説明します。
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 523567a0db79e54bea1ed6ff23557c7fa29c74f6
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541113"
---
# <a name="about-the-mobility-service-for-vmware-vms-and-physical-servers"></a>VMware VM と物理サーバーのためのモビリティ サービスについて

[Azure Site Recovery](site-recovery-overview.md) を使用して VMware VM と物理サーバーのためのディザスター リカバリーを設定するとき、オンプレミスの VMware VM と物理サーバーごとに Site Recovery モビリティ サービスをインストールします。  このモビリティ サービスによって、マシン上のデータ書き込みがキャプチャされ、Site Recovery プロセス サーバーに転送されます。 次の方法でモビリティ サービスをデプロイできます。

- [プッシュ インストール](#push-installation):Azure portal で保護が有効になっている場合、Site Recovery ではサーバーにモビリティ エージェントがインストールされます。
- 手動インストール:[UI](#install-mobility-agent-through-ui) または[コマンド プロンプト](#install-mobility-agent-through-command-prompt)を使用して、各マシンにモビリティ サービスを手動でインストールできます。
- [自動デプロイ](vmware-azure-mobility-install-configuration-mgr.md):System Center Configuration Manager など、ソフトウェア デプロイ ツールでインストールを自動化できます。

## <a name="anti-virus-on-replicated-machines"></a>レプリケートされるマシンでのウイルス対策

レプリケートするマシンでウイルス対策ソフトウェアがアクティブに実行されている場合は、モビリティ サービスのインストール フォルダーをウイルス対策の操作から除外してください (*C:\ProgramData\ASR\agent*)。 これにより、レプリケーションが意図したとおりに動作することが保証されます。

## <a name="push-installation"></a>プッシュ インストール

プッシュ インストールは、ポータルでトリガーされる "[レプリケーションを有効にする](vmware-azure-enable-replication.md#enable-replication)" ジョブに不可欠な部分です。 保護して "レプリケーションを有効にする" をトリガーする仮想マシンのセットを選択した後、構成サーバーでは、モビリティ エージェントをサーバー上にプッシュし、エージェントをインストールし、構成サーバーにエージェントを完全に登録します。 この操作が正常に完了するために、次を実行します。

- プッシュ インストールのすべての[前提条件](vmware-azure-install-mobility-service.md)を満たしていることを確認します。
- すべてのサーバーの構成が [VMware から Azure DR シナリオへのサポート マトリックス](vmware-physical-azure-support-matrix.md)に該当することを確認します。

プッシュ インストールのワークフローの詳細は、以降のセクションで説明します。

### <a name="from-923-versionhttpssupportmicrosoftcomen-inhelp4494485update-rollup-35-for-azure-site-recovery-onwards"></a>[9.23 バージョン](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery)以降

モビリティ エージェントのプッシュ インストール中に、次の手順が実行されます

1. エージェントをソース マシンにプッシュします。 エージェントをソース マシンにコピーすると、複数の環境エラーが原因で失敗することがあります。 プッシュ インストールの失敗のトラブルシューティングについては、[ガイダンス](vmware-azure-troubleshoot-push-install.md)を参照してください。
2. エージェントがサーバーに正常にコピーされた後、前提条件の確認がサーバー上で実行されます。 1 つ以上の[前提条件](vmware-physical-azure-support-matrix.md)が満たされていないと、インストールは失敗します。 すべての前提条件が満たされた場合は、インストールがトリガーされます。
3. Azure Site Recovery VSS プロバイダーは、モビリティ エージェントのインストールの一部としてサーバーにインストールされます。 このプロバイダーは、アプリケーション整合性ポイントの生成に使用されます。 VSS プロバイダーのインストールが失敗した場合、この手順はスキップされ、エージェントのインストールは続行されます。
4. エージェントのインストールは成功したが、VSS プロバイダーのインストールが失敗した場合、ジョブの状態は "警告" としてマークされます。 これは、クラッシュ整合性ポイントの生成には影響しません。

    a. アプリケーション整合性ポイントを生成するには、[ガイダンス](vmware-physical-manage-mobility-service.md#install-site-recovery-vss-provider-on-source-machine)を参照して、Site Recovery VSS プロバイダーの手動インストールを完了します。 </br>
    b.  アプリケーション整合性ポイントを生成したくない場合は、[レプリケーション ポリシーを変更](vmware-azure-set-up-replication.md#create-a-policy)して、アプリケーション整合性ポイントをオフにします。

### <a name="before-922-versions"></a>9.22 バージョンより前

1. エージェントをソース マシンにプッシュします。 エージェントをソース マシンにコピーすると、複数の環境エラーが原因で失敗することがあります。 プッシュ インストールの失敗のトラブルシューティングについては、[ガイダンス](vmware-azure-troubleshoot-push-install.md)を参照してください。
2. エージェントがサーバーに正常にコピーされた後、前提条件の確認がサーバー上で実行されます。 1 つ以上の[前提条件](vmware-physical-azure-support-matrix.md)が満たされていないと、インストールは失敗します。 すべての前提条件が満たされた場合は、インストールがトリガーされます。
3. Azure Site Recovery VSS プロバイダーは、モビリティ エージェントのインストールの一部としてサーバーにインストールされます。 このプロバイダーは、アプリケーション整合性ポイントの生成に使用されます。 VSS プロバイダーのインストールが失敗すると、エージェントのインストールは失敗します。 モビリティ エージェントのインストールの失敗を回避するには、[9.23 バージョン](https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery)以降を使用して、クラッシュ整合性ポイントを生成し、VSS プロバイダーを手動でインストールします。

## <a name="install-mobility-agent-through-ui"></a>UI を使用してモビリティ エージェントをインストールする

### <a name="prerequisite"></a>前提条件

- すべてのサーバーの構成が [VMware から Azure DR シナリオへのサポート マトリックス](vmware-physical-azure-support-matrix.md)に該当することを確認します。
- サーバーのオペレーティング システムに基づいて[インストーラを見つけます](#locate-installer-files)。

>[!IMPORTANT]
> Azure IaaS VM を Azure リージョン間でレプリケートしている場合は、この方法を使用しないでください。 代わりに、コマンド ライン ベースのインストール方法を使用します。

1. インストール ファイルをマシンにコピーして実行します。
2. **[Installation Option]\(インストール オプション\)** で **[Install mobility service]\(モビリティ サービスのインストール\)** を選択します。
3. インストール先の場所を選択し、**[インストール]** を選択します。

    ![モビリティ サービスのインストール オプションのページ](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

4. **[インストールの進行状況]** でインストールを監視します。 インストールが完了したら、**[Proceed to Configuration]\(構成に進む\)** を選択して、構成サーバーにサービスを登録します。

    ![モビリティ サービスの登録ページ](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

5. **[Configuration Server Details]\(構成サーバーの詳細\)** で、構成した IP アドレスとパスフレーズを指定します。  

    ![モビリティ サービスの登録ページ](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

6. **[登録]** を選択して登録を完了します。

    ![モビリティ サービス登録の最後のページ](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-mobility-agent-through-command-prompt"></a>コマンド プロンプトを使用してモビリティ エージェントをインストールする

### <a name="prerequisite"></a>前提条件

- すべてのサーバーの構成が [VMware から Azure DR シナリオへのサポート マトリックス](vmware-physical-azure-support-matrix.md)に該当することを確認します。
- サーバーのオペレーティング システムに基づいて[インストーラを見つけます](#locate-installer-files)。

### <a name="on-a-windows-machine"></a>Windows マシンの場合

- 保護するサーバー上のローカル フォルダー (C:\Temp など) にインストーラーをコピーします。

    ```
    cd C:\Temp
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```

- 次のようにインストールします。

    ``` 
    UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    ```

- 構成サーバーにエージェントを登録します。

    ``` 
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
    ```

#### <a name="installation-settings"></a>インストールの設定
**設定** | **詳細**
--- | ---
使用法 | UnifiedAgent.exe /Role <MS|MT> /InstallLocation <Install Location> /Platform “VmWare” /Silent
セットアップ ログ | %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log の下。
/Role | 必須のインストール パラメーターです。 モビリティ サービス (MS) またはマスター ターゲット (MT) をインストールするかどうかを指定します。
/InstallLocation| 省略可能なパラメーター。 モビリティ サービスのインストール場所 (任意のフォルダー) を指定します。
/Platform | 必須。 モビリティ サービスをインストールするプラットフォームを指定します。 VMware VM/物理サーバーの場合は **VMware**、Azure VM の場合は **Azure**。 
/Silent| 省略可能。 インストーラーをサイレント モードで実行するかどうかを指定します。

#### <a name="registration-settings"></a>登録設定
**設定** | **詳細**
--- | ---
使用法 | UnifiedAgentConfigurator.exe  /CSEndPoint \<CSIP> /PassphraseFilePath \<PassphraseFilePath>
エージェント構成ログ | %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log の下。
/CSEndPoint | 必須パラメーターです。 構成サーバーの IP アドレスを指定します。 任意の有効な IP アドレスを使用します。
/PassphraseFilePath |  必須。 パスフレーズの場所。 任意の有効な UNC またはローカル ファイル パスを使用します。

### <a name="on-a-linux-machine"></a>Linux マシンの場合

1. 保護するサーバー上のローカル フォルダー (/tmp など) にインストーラーをコピーします。 ターミナルから次のコマンドを実行します。

    ```
    cd /tmp ;
    tar -xvzf Microsoft-ASR_UA*release.tar.gz
    ```

2. 次のようにインストールします。

    ```
    sudo ./install -d <Install Location> -r MS -v VmWare -q
    ```

3. インストールが完了したら、モビリティ サービスを構成サーバーに登録する必要があります。 以下のコマンドを実行して、モビリティ サービスを構成サーバーに登録できます。

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
    ```

#### <a name="installation-settings"></a>インストールの設定
**設定** | **詳細**
--- | ---
使用法 | ./install -d \<Install Location> -r <MS|MT> -v VmWare -q
-r | 必須のインストール パラメーターです。 モビリティ サービス (MS) またはマスター ターゲット (MT) をインストールするかどうかを指定します。
-d | 省略可能なパラメーター。 モビリティ サービスのインストール場所 (/usr/local/ASR) を指定します。
-v | 必須。 モビリティ サービスをインストールするプラットフォームを指定します。 VMware VM/物理サーバーの場合は **VMware**、Azure VM の場合は **Azure**。 
パラメーター | 省略可能。 インストーラーをサイレント モードで実行するかどうかを指定します。

#### <a name="registration-settings"></a>登録設定
**設定** | **詳細**
--- | ---
使用法 | cd /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i \<CSIP> -P \<PassphraseFilePath>
-i | 必須パラメーターです。 構成サーバーの IP アドレスを指定します。 任意の有効な IP アドレスを使用します。
-P |  必須。 パスフレーズが保存されているファイルの完全なファイル パス。 任意の有効なフォルダーを使用します。

## <a name="azure-virtual-machine-agent"></a>Azure 仮想マシン エージェント

- **Windows VM**:モビリティ サービスのバージョン 9.7.0.0 以降、[Azure VM エージェント](../virtual-machines/extensions/features-windows.md#azure-vm-agent)がモビリティ サービス インストーラーによってインストールされます。 マシンが Azure にフェールオーバーするとき、VM 拡張を使用するためのエージェント インストール前提条件を Azure VM が満たすようにします。
- **Linux VM**:フェールオーバー後、[WALinuxAgent](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) を Azure VM に手動インストールする必要があります。

## <a name="locate-installer-files"></a>インストーラー ファイルを検索する

構成サーバーの %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository フォルダーに移動します。 オペレーティング システムに基づいて、必要なインストーラーを確認します。 VMware VM と物理サーバーのオペレーティング システム別のインストーラー ファイルをまとめたのが次の表です。 開始前に[サポートされているオペレーティング システム](vmware-physical-azure-support-matrix.md#replicated-machines)を確認できます。

**インストーラー ファイル** | **オペレーティング システム (64 ビットのみ)** 
--- | ---
Microsoft-ASR\_UA\*Windows\*release.exe | Windows Server 2016、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 SP1 
Microsoft-ASR\_UA\*RHEL6-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 6.* </br> CentOS 6.*
Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz | Red Hat Enterprise Linux (RHEL) 7.* </br> CentOS 7.* 
Microsoft-ASR\_UA\*SLES12-64\*release.tar.gz | SUSE Linux Enterprise Server 12 SP1、SP2、SP3 
Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP3 
Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz| SUSE Linux Enterprise Server 11 SP4 
Microsoft-ASR\_UA\*OL6-64\*release.tar.gz | Oracle Enterprise Linux 6.4、6.5
icrosoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz | Ubuntu Linux 14.04
Microsoft-ASR\_UA\*UBUNTU-16.04-64\*release.tar.gz | Ubuntu Linux 16.04 LTS サーバー
Microsoft-ASR_UA\*DEBIAN7-64\*release.tar.gz | Debian 7 
Microsoft-ASR_UA\*DEBIAN8-64\*release.tar.gz | Debian 8

## <a name="next-steps"></a>次の手順

[モビリティ サービスにプッシュ インストールを設定する](vmware-azure-install-mobility-service.md)。
