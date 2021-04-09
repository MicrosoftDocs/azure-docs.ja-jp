---
title: Azure Site Recovery でインストールのディザスター リカバリー用のモビリティ サービスを自動化する
description: Azure Site Recovery で、VMware/物理サーバーのディザスター リカバリー用のモビリティ サービスのインストールを自動化する方法。
author: Rajeswari-Mamilla
ms.topic: how-to
ms.date: 2/5/2020
ms.author: ramamill
ms.openlocfilehash: 2159ab8c2639f0f87fd53e8559dad518a3daa663
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92544819"
---
# <a name="automate-mobility-service-installation"></a>モビリティ サービスのインストールを自動化する

この記事では、[Azure Site Recovery](site-recovery-overview.md)で Mobility Service エージェントのインストールと更新を自動化する方法について説明します。

オンプレミスの VMware VM と物理サーバーのディザスター リカバリーのために Site Recovery を Azure に展開する場合、複製する各マシンに Mobility Service エージェントをインストールします。 モビリティサービスは、マシン上のデータ書き込みをキャプチャし、複製のために Site Recovery プロセスサーバーに転送します。 Mobility Service はいくつかの方法で展開できます。

- **プッシュ インストール**:Azure portal でマシンのレプリケーションを有効にする場合に、Site Recovery によってモビリティ サービス エージェントをインストールします。
- **手動インストール**:各マシンにモビリティ サービスを手動でインストールします。 プッシュ インストールと手動インストールの[詳細情報](vmware-physical-mobility-service-overview.md)を参照してください。
- **自動デプロイ**:Microsoft Endpoint Configuration Manager などのソフトウェア デプロイ ツールや、Intigua JetPatch などのサード パーティ ツールなどで、インストールを自動化します。

インストールと更新を自動化することで、以下の場合の解決策が得られます。

- 組織で、保護されたサーバーへのプッシュ インストールが許可されない。
- 会社のポリシーで、パスワードを定期的に変更することが求められている。 プッシュ インストール用のパスワードを指定する必要がある。
- セキュリティ ポリシーで、特定のマシンにファイアウォール例外を追加することが許可されていない。
- ホスティング サービス プロバイダーとしてサービスを提供しており、Site Recovery を使用したプッシュ インストールに必要な顧客のマシン資格情報を提供したくない。
- エージェントのインストールを多数のサーバーに同時にスケーリングする必要があります。
- 計画メンテナンスの期間中にインストールとアップグレードをスケジュールしたい。

## <a name="prerequisites"></a>前提条件

インストールを自動化するには、次のアイテムが必要です。

- [Configuration Manager](/configmgr/) や [JetPatch](https://jetpatch.com/microsoft-azure-site-recovery/) などの、デプロイ済みのソフトウェア インストール ソリューション。
- [Azure](tutorial-prepare-azure.md) と[オンプレミス](vmware-azure-tutorial-prepare-on-premises.md)で、VMware のディザスター リカバリー用、または[物理サーバー](physical-azure-disaster-recovery.md)のディザスター リカバリー用のデプロイの前提条件が満たされている。 ディザスター リカバリーのための[サポート要件](vmware-physical-azure-support-matrix.md)を確認してください。

## <a name="prepare-for-automated-deployment"></a>自動デプロイを準備する

次の表に、Mobility Service のデプロイを自動化するためのツールとプロセスをまとめます。

**ツール** | **詳細** | **手順**
--- | --- | ---
**構成マネージャー** | 1.上記の[前提条件](#prerequisites)が満たされていることを確認します。 <br/><br/> 2.ソース環境をセットアップ (OVF テンプレートを使用して Site Recovery 構成サーバーを VMware VM としてデプロイするための OVA ファイルのダウンロードなど) して、ディザスター リカバリーをデプロイします。<br/><br/> 3.構成サーバーを Site Recovery サービスに登録し、ターゲット Azure 環境を設定して、レプリケーション ポリシーを構成します。<br/><br/> 4.モビリティサービスの自動デプロイでは、構成サーバーのパスフレーズとモビリティサービスのインストールファイルを含むネットワーク共有を作成します。<br/><br/> 5.インストールまたは更新を含む Configuration Manager パッケージを作成し、Mobility サービスのデプロイの準備を行います。<br/><br/> 6.その後、Mobility Service がインストールされているマシンの Azure へのレプリケーションを有効にできます。 | [Configuration Manager で自動化](#automate-with-configuration-manager)
**JetPatch** | 1.上記の[前提条件](#prerequisites)が満たされていることを確認します。 <br/><br/> 2.ソース環境をセットアップ (OVF テンプレートを使用して Azure Site Recovery 用の JetPatch Agent Manager を Site Recovery 環境にダウンロードしてデプロイするなど) して、ディザスター リカバリーをデプロイします。<br/><br/> 3.構成サーバーを Site Recovery に登録し、ターゲット Azure 環境を設定して、レプリケーション ポリシーを構成します。<br/><br/> 4.自動デプロイのために、JetPatch Agent Manager の構成を初期化して完了します。<br/><br/> 5.JetPatch では、サイト回復ポリシーを作成して、Mobility サービスエージェントのデプロイとアップグレードを自動化できます。 <br/><br/> 6.その後、Mobility Service がインストールされているマシンの Azure へのレプリケーションを有効にできます。 | [JetPatch Agent Manager による自動化](https://jetpatch.com/microsoft-azure-site-recovery-deployment-guide/)<br/><br/> [JetPatch でのエージェントインストールのトラブルシューティング](https://kc.jetpatch.com/hc/articles/360035981812)

## <a name="automate-with-configuration-manager"></a>Configuration Manager を使用して自動化する

### <a name="prepare-the-installation-files"></a>インストール ファイルを準備する

1. 前提条件を満たしていることを確認します。
1. 構成サーバーを実行しているマシンからアクセスできる、セキュリティで保護されたネットワーク ファイル共有 (SMB 共有) を作成します。
1. Configuration Manager で、モビリティサービスをインストールまたは更新する[サーバーをカテゴリー化します](/sccm/core/clients/manage/collections/automatically-categorize-devices-into-collections)。 1 つのコレクションにはすべての Windows サーバが含まれ、もう 1 つのコレクションにはすべての Linux サーバが含まれます。
1. ネットワーク共有で、次のフォルダーを作成します。

   - Windows マシンにインストールする場合は、_MobSvcWindows_ という名前のフォルダーを作成してください。
   - Linux マシンにインストールする場合は、_MobSvcLinux_ という名前のフォルダーを作成します。

1. 構成サーバー マシンにサインインします。
1. 構成サーバーマシンで、管理コマンド プロンプトを開きます。
1. パスフレーズファイルを生成するには、次のコマンドを実行します。

    ```Console
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```

1. _MobSvc をコピーします。パスフレーズ_ ファイルをWindows フォルダと Linux フォルダにコピーします。
1. インストールファイルを含むフォルダを参照するには、次のコマンドを実行します。

    ```Console
    cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository
    ```

1. これらのインストール ファイルをネットワーク共有にコピーします。

   - Windows の場合、 _Microsoft-ASR_UA_version_Windows_GA_date_Release.exe_ を _MobSvcWindows_ にコピーします。
   - Linux の場合、次のファイルを _MobSvcLinux_ にコピーします。
     - _Microsoft-ASR_UARHEL6-64release.tar.gz_
     - _Microsoft-ASR_UARHEL7-64release.tar.gz_
     - _Microsoft-ASR_UASLES11-SP3-64release.tar.gz_
     - _Microsoft-ASR_UASLES11-SP4-64release.tar.gz_
     - _Microsoft-ASR_UAOL6-64release.tar.gz_
     - _Microsoft-ASR_UAUBUNTU-14.04-64release.tar.gz_

1. 次の手順で説明するように、コードを Windows または Linux フォルダーにコピーします。 次のことが前提となっています。

   - 構成サーバーの IP アドレスは`192.168.3.121`です。
   - 安全なネットワークファイル共有は`\\ContosoSecureFS\MobilityServiceInstallers`です。

### <a name="copy-code-to-the-windows-folder"></a>Windows フォルダーにコードをコピーする

次のコードをコピーします。

- _MobSvcWindows_ フォルダーにコードを _install.bat_ として保存します。
- このスクリプトの`[CSIP]`プレースホルダーを、構成サーバーのIPアドレスの実際の値に置き換えます。
- このスクリプトは、Mobility Service エージェントの新規インストールと、すでにインストールされているエージェントの更新をサポートしています。

```DOS
Time /t >> C:\Temp\logfile.log
REM ==================================================
REM ==== Clean up the folders ========================
RMDIR /S /q %temp%\MobSvc
MKDIR %Temp%\MobSvc
MKDIR C:\Temp
REM ==================================================

REM ==== Copy new files ==============================
COPY M*.* %Temp%\MobSvc
CD %Temp%\MobSvc
REN Micro*.exe MobSvcInstaller.exe
REM ==================================================

REM ==== Extract the installer =======================
MobSvcInstaller.exe /q /x:%Temp%\MobSvc\Extracted
REM ==== Wait 10s for extraction to complete =========
TIMEOUT /t 10
REM =================================================

REM ==== Perform installation =======================
REM =================================================

CD %Temp%\MobSvc\Extracted
whoami >> C:\Temp\logfile.log
SET PRODKEY=HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall
REG QUERY %PRODKEY%\{275197FC-14FD-4560-A5EB-38217F80CBD1}
IF NOT %ERRORLEVEL% EQU 0 (
    echo "Product is not installed. Goto INSTALL." >> C:\Temp\logfile.log
    GOTO :INSTALL
) ELSE (
    echo "Product is installed." >> C:\Temp\logfile.log

    echo "Checking for Post-install action status." >> C:\Temp\logfile.log
    GOTO :POSTINSTALLCHECK
)

:POSTINSTALLCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "PostInstallActions" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Post-install actions succeeded. Checking for Configuration status." >> C:\Temp\logfile.log
        GOTO :CONFIGURATIONCHECK
    ) ELSE (
        echo "Post-install actions didn't succeed. Goto INSTALL." >> C:\Temp\logfile.log
        GOTO :INSTALL
    )

:CONFIGURATIONCHECK
    REG QUERY "HKLM\SOFTWARE\Wow6432Node\InMage Systems\Installed Products\5" /v "AgentConfigurationStatus" | Find "Succeeded"
    If %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded. Goto UPGRADE." >> C:\Temp\logfile.log
        GOTO :UPGRADE
    ) ELSE (
        echo "Configuration didn't succeed. Goto CONFIGURE." >> C:\Temp\logfile.log
        GOTO :CONFIGURE
    )


:INSTALL
    echo "Perform installation." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Role MS /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Installation has succeeded." >> C:\Temp\logfile.log
        (GOTO :CONFIGURE)
    ) ELSE (
        echo "Installation has failed." >> C:\Temp\logfile.log
        GOTO :ENDSCRIPT
    )

:CONFIGURE
    echo "Perform configuration." >> C:\Temp\logfile.log
    cd "C:\Program Files (x86)\Microsoft Azure Site Recovery\agent"
    UnifiedAgentConfigurator.exe  /CSEndPoint "[CSIP]" /PassphraseFilePath %Temp%\MobSvc\MobSvc.passphrase
    IF %ERRORLEVEL% EQU 0 (
        echo "Configuration has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Configuration has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:UPGRADE
    echo "Perform upgrade." >> C:\Temp\logfile.log
    UnifiedAgent.exe /Platform "VmWare" /Silent
    IF %ERRORLEVEL% EQU 0 (
        echo "Upgrade has succeeded." >> C:\Temp\logfile.log
    ) ELSE (
        echo "Upgrade has failed." >> C:\Temp\logfile.log
    )
    GOTO :ENDSCRIPT

:ENDSCRIPT
    echo "End of script." >> C:\Temp\logfile.log
```

### <a name="copy-code-to-the-linux-folder"></a>Linux フォルダーにコードをコピーする

次のコードをコピーします。

- _MobSvcLinux_ フォルダーにコードを _install_linux.sh_ として保存します。
- このスクリプトの`[CSIP]`プレースホルダーを、構成サーバーのIPアドレスの実際の値に置き換えます。
- このスクリプトは、Mobility Service エージェントの新規インストールと、すでにインストールされているエージェントの更新をサポートしています。

```Bash
#!/usr/bin/env bash

rm -rf /tmp/MobSvc
mkdir -p /tmp/MobSvc
INSTALL_DIR='/usr/local/ASR'
VX_VERSION_FILE='/usr/local/.vx_version'

echo "=============================" >> /tmp/MobSvc/sccm.log
echo `date` >> /tmp/MobSvc/sccm.log
echo "=============================" >> /tmp/MobSvc/sccm.log

if [ -f /etc/oracle-release ] && [ -f /etc/redhat-release ]; then
    if grep -q 'Oracle Linux Server release 6.*' /etc/oracle-release; then
        if uname -a | grep -q x86_64; then
            OS="OL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *OL6*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/redhat-release ]; then
    if grep -q 'Red Hat Enterprise Linux Server release 6.* (Santiago)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 6.* (Final)' /etc/redhat-release || \
        grep -q 'CentOS release 6.* (Final)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL6-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL6*.tar.gz /tmp/MobSvc
        fi
    elif grep -q 'Red Hat Enterprise Linux Server release 7.* (Maipo)' /etc/redhat-release || \
        grep -q 'CentOS Linux release 7.* (Core)' /etc/redhat-release; then
        if uname -a | grep -q x86_64; then
            OS="RHEL7-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *RHEL7*.tar.gz /tmp/MobSvc
                fi
    fi
elif [ -f /etc/SuSE-release ] && grep -q 'VERSION = 11' /etc/SuSE-release; then
    if grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 3' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP3-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP3*.tar.gz /tmp/MobSvc
        fi
    elif grep -q "SUSE Linux Enterprise Server 11" /etc/SuSE-release && grep -q 'PATCHLEVEL = 4' /etc/SuSE-release; then
        if uname -a | grep -q x86_64; then
            OS="SLES11-SP4-64"
            echo $OS >> /tmp/MobSvc/sccm.log
            cp *SLES11-SP4*.tar.gz /tmp/MobSvc
        fi
    fi
elif [ -f /etc/lsb-release ] ; then
    if grep -q 'DISTRIB_RELEASE=14.04' /etc/lsb-release ; then
       if uname -a | grep -q x86_64; then
           OS="UBUNTU-14.04-64"
           echo $OS >> /tmp/MobSvc/sccm.log
           cp *UBUNTU-14*.tar.gz /tmp/MobSvc
       fi
    fi
else
    exit 1
fi

if [ -z "$OS" ]; then
    exit 1
fi

Install()
{
    echo "Perform Installation." >> /tmp/MobSvc/sccm.log
    ./install -q -d ${INSTALL_DIR} -r MS -v VmWare
    RET_VAL=$?
    echo "Installation Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Installation has succeeded. Proceed to configuration." >> /tmp/MobSvc/sccm.log
        Configure
    else
        echo "Installation has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Configure()
{
    echo "Perform configuration." >> /tmp/MobSvc/sccm.log
    ${INSTALL_DIR}/Vx/bin/UnifiedAgentConfigurator.sh -i [CSIP] -P MobSvc.passphrase
    RET_VAL=$?
    echo "Configuration Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Configuration has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Configuration has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

Upgrade()
{
    echo "Perform Upgrade." >> /tmp/MobSvc/sccm.log
    ./install -q -v VmWare
    RET_VAL=$?
    echo "Upgrade Returncode: $RET_VAL" >> /tmp/MobSvc/sccm.log
    if [ $RET_VAL -eq 0 ]; then
        echo "Upgrade has succeeded." >> /tmp/MobSvc/sccm.log
    else
        echo "Upgrade has failed." >> /tmp/MobSvc/sccm.log
        exit $RET_VAL
    fi
}

cp MobSvc.passphrase /tmp/MobSvc
cd /tmp/MobSvc

tar -zxvf *.tar.gz

if [ -e ${VX_VERSION_FILE} ]; then
    echo "${VX_VERSION_FILE} exists. Checking for configuration status." >> /tmp/MobSvc/sccm.log
    agent_configuration=$(grep ^AGENT_CONFIGURATION_STATUS "${VX_VERSION_FILE}" | cut -d"=" -f2 | tr -d " ")
    echo "agent_configuration=$agent_configuration" >> /tmp/MobSvc/sccm.log
     if [ "$agent_configuration" == "Succeeded" ]; then
        echo "Agent is already configured. Proceed to Upgrade." >> /tmp/MobSvc/sccm.log
        Upgrade
    else
        echo "Agent is not configured. Proceed to Configure." >> /tmp/MobSvc/sccm.log
        Configure
    fi
else
    Install
fi

cd /tmp

```

### <a name="create-a-package"></a>パッケージを作成する

1. Configuration Manager コンソールにサインインし、**ソフトウェアライブラリ** > **アプリケーション管理** > **パッケージ** に移動します。
1. **[パッケージ]** を右クリックして **[パッケージの作成]** に進みます。
1. 名前、説明、製造元、言語、バージョンなど、パッケージの詳細を指定します。
1. **[このパッケージはソース ファイルを含む]** を選択します。
1. **参照** をクリックし、関連するインストーラー（ _MobSvcWindows_ または _MobSvcLinux_）を含むネットワーク共有とフォルダーを選択します。 次に、 **[次へ]** を選択します。

   ![パッケージとプログラムの作成ウィザードのスクリーンショット](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

1. **[作成するプログラムの種類の選択]** ページで、 **[標準プログラム]**  >  **[次へ]** を選択します。

   ![[標準プログラム] オプションを示すパッケージとプログラムの作成ウィザードのスクリーンショット。](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

1. **[この標準プログラムに関する情報の指定]** ページで、次の値を指定します。

    **パラメーター** | **Windows の値** | **Linux の値**
    --- | --- | ---
    **名前** | Microsoft Azure Mobility Service のインストール (Windows) | Microsoft Azure Mobility Service のインストール (Linux)。
    **コマンド ライン** | install.bat | ./install_linux.sh
    **プログラムの実行条件** | ユーザーがログオンしているかどうか | ユーザーがログオンしているかどうか
    **その他のパラメーター** | 既定の設定を使用する | 既定の設定を使用する

   ![標準プログラムに指定できる情報を示すスクリーンショット。](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

1. **この標準プログラムの要件を指定** で、次のタスクを実行します。

   - Windows マシンの場合は、 **[指定したプラットフォームだけで実行可能]** を選択します。 次に、[サポートされているWindowsオペレーティングシステム](vmware-physical-azure-support-matrix.md#replicated-machines)を選択し、**次へ** を選択します。
   - Linux マシンでは、 **[任意のプラットフォームで実行可能]** を選択します。 **[次へ]** を選択します。

1. ウィザードを終了します。

### <a name="deploy-the-package"></a>パッケージをデプロイする

1. Configuration Manager コンソールで、パッケージを右クリックし、**コンテンツの配布** を選択します。

   ![Configuration Manager コンソールのスクリーンショット](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)

1. パッケージのコピー先とする配布ポイントを選択します。 [詳細については、こちらを参照してください](/sccm/core/servers/deploy/configure/install-and-configure-distribution-points)。
1. ウィザードを完了します。 指定した配布ポイントへのパッケージのレプリケートが開始されます。
1. パッケージの配布が完了したら、パッケージを右クリックして **[展開]** に進みます。

   ![[展開] メニュー オプションを表示する Configuration Manager コンソールのスクリーンショット。](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)

1. 以前に作成した Windows または Linux デバイスのコレクションを選択します。
1. **[コンテンツの配布先の指定]** ページで **[配布ポイント]** を選択します。
1. **[このソフトウェアの展開を制御する設定の指定]** ページで、 **[目的]** を **[必須]** に設定します。

   ![ソフトウェアの展開ウィザードのスクリーンショット](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

1. **[この展開のスケジュールを指定します]** でスケジュールを設定します。 [詳細については、こちらを参照してください](/sccm/apps/deploy-use/deploy-applications#bkmk_deploy-sched)。

   - モビリティサービスは、指定したスケジュールに従ってインストールされます。
   - 不要な再起動を避けるためには、月次のメンテナンス期間中またはソフトウェアの更新期間中にパッケージのインストールをスケジュールします。

1. **[配布ポイント]** ページで、設定を構成し、ウィザードを完了します。
1. Configuration Manager コンソールでデプロイの進行状況を監視します。 **[監視]**  >  **[デプロイ]**  >  _\<your package name\>_ に移動します。

### <a name="uninstall-the-mobility-service"></a>モビリティサービスをアンインストールする

Configuration Manager パッケージを作成して、Mobility Service をアンインストールできます。 たとえば、次のスクリプトはモビリティサービスをアンインストールします。

```DOS
Time /t >> C:\logfile.log
REM ==================================================
REM ==== Check if Mob Svc is already installed =======
REM ==== If not installed no operation required ========
REM ==== Else run uninstall command =====================
REM ==== {275197FC-14FD-4560-A5EB-38217F80CBD1} is ====
REM ==== guid for Mob Svc Installer ====================
whoami >> C:\logfile.log
NET START | FIND "InMage Scout Application Service"
IF  %ERRORLEVEL% EQU 1 (GOTO :INSTALL) ELSE GOTO :UNINSTALL
:NOOPERATION
                echo "No Operation Required." >> c:\logfile.log
                GOTO :ENDSCRIPT
:UNINSTALL
                echo "Uninstall" >> C:\logfile.log
                MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
:ENDSCRIPT
```

## <a name="next-steps"></a>次のステップ

VM の[レプリケーションを有効にします](vmware-azure-enable-replication.md)。
