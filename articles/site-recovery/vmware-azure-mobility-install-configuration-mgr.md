---
title: System Center Configuration Manager を使用した VMware VM と物理サーバーの Azure へのディザスター リカバリーのために Azure Site Recovery モビリティ サービスのインストールを自動化する | Microsoft Docs
description: この記事は、Site Recovery を使用した VMware VM と物理サーバーの Azure へのディザスター リカバリーのために、System Center Configuration Manager を使用してモビリティ サービスのインストールを自動化するのに役立ちます。
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: ramamill
ms.openlocfilehash: 35c317c4b73e9a22e3b0d6192abcfc2a596066b8
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149998"
---
# <a name="automate-mobility-service-installation-with-system-center-configuration-manager"></a>System Center Configuration Manager を使用して Mobility Service のインストールを自動化する

Mobility Service は、[Azure Site Recovery](site-recovery-overview.md) を使用して Azure にレプリケートする、VMware VM および物理サーバーにインストールされます。

この記事では、System Center Configuration Manager を使用して、Azure Site Recovery の Mobility Service を VMware VM にデプロイする方法の例を示します。 Configuration Manager のようなソフトウェア展開ツールを使用することには、次の利点があります。

* ソフトウェア更新の計画的なメンテナンス期間中に、新規インストールとアップグレードをスケジュールできる。
* 同時に数百のサーバーへのデプロイをスケーリングできる。

この記事では、System Center Configuration Manager 2012 R2 を使用して、展開アクティビティを示します。 **9.9.4510.1** 以降のバージョンの Mobility Service を使用することを前提とします。

代わりに、[Azure Automation DSC](vmware-azure-mobility-deploy-automation-dsc.md) を使用して、モビリティ サービスのインストールを自動化することもできます。

## <a name="prerequisites"></a>前提条件

1. 環境内に既にデプロイされている Configuration Manager などのソフトウェア展開ツール。
2. 2 つの[デバイス コレクション](https://technet.microsoft.com/library/gg682169.aspx)を作成する必要があります。1 つはすべての **Windows サーバー**用、もう 1 つは Site Recovery を使用して保護するすべての **Linux サーバー**用です。
3. Recovery Services コンテナーに既に登録されている構成サーバー。
4. Configuration Manager コンピューターからアクセスできるセキュリティで保護されたネットワーク ファイル共有 (SMB 共有)。

## <a name="deploy-on-windows-machines"></a>Windows コンピューターにデプロイする
> [!NOTE]
> この記事は、構成サーバーの IP アドレスが 192.168.3.121、セキュリティで保護されたネットワーク ファイル共有が \\\ContosoSecureFS\MobilityServiceInstallers であることを前提としています。

### <a name="prepare-for-deployment"></a>デプロイの準備をする
1. ネットワーク共有にフォルダーを作成し、**MobSvcWindows** という名前を付けます。
2. 構成サーバーにサインインし、管理者権限のコマンド プロンプトを開きます。
3. 次のコマンドを実行して、パスフレーズ ファイルを生成します。

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. **MobSvc.passphrase** ファイルをネットワーク共有上の **MobSvcWindows** フォルダーにコピーします。
5. 次のコマンドを実行して、構成サーバーでインストーラーのリポジトリを参照します。

   `cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

6. **Microsoft-ASR\_UA\_*バージョン*\_Windows\_GA\_*日付*\_Release.exe** をネットワーク共有上の **MobSvcWindows** フォルダーにコピーします。
7. 次のコードをコピーして、**install.bat** という名前で **MobSvcWindows** フォルダーに保存します。

   > [!NOTE]
   > このスクリプトの [CSIP] プレース ホルダーを、お使いの構成サーバーの IP アドレスの実際の値に置き換えてください。

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

### <a name="create-a-package"></a>パッケージを作成する

1. Configuration Manager コンソールにサインインします。
2. **[ソフトウェア ライブラリ]** > 、**[アプリケーション管理]**、 > **[パッケージ]** と移動します。
3. **[パッケージ]** を右クリックして **[パッケージの作成]** を選択します。
4. 名前、説明、製造元、言語、バージョンの値を指定します。
5. **[このパッケージにソース ファイルを含める]** チェック ボックスをオンします。
6. **[参照]** ボタンをクリックして、インストーラーが格納されているネットワーク共有 (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcWindows) を選択します。

   ![パッケージとプログラムの作成ウィザードのスクリーンショット](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package.png)

7. **[作成するプログラムの種類の選択]** ページで、**[標準プログラム]** を選択して **[次へ]** をクリックします。

   ![パッケージとプログラムの作成ウィザードのスクリーンショット](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. **[この標準プログラムに関する情報の指定]** ページで、次の入力を指定して **[次へ]** をクリックします  (その他の入力は既定値を使用できます)。

   | **パラメーター名** | **値** |
   |--|--|
   | Name | Microsoft Azure Mobility Service のインストール (Windows) |
   | コマンド ライン | install.bat |
   | プログラムの実行条件 | ユーザーがログオンしているかどうか |

   ![パッケージとプログラムの作成ウィザードのスクリーンショット](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties.png)

9. 次のページで、ターゲットのオペレーティング システムを選択します。 Mobility Service は、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2 にのみインストールできます。

   ![パッケージとプログラムの作成ウィザードのスクリーンショット](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-page2.png)

10. **[次へ]** を 2 回クリックしてウィザードを終了します。


> [!NOTE]
> スクリプトでは、Mobility Service エージェントの新規インストールとインストール済みエージェントへの更新の両方をサポートします。

### <a name="deploy-the-package"></a>パッケージをデプロイする
1. Configuration Manager コンソールで、パッケージを右クリックして **[コンテンツの配布]** を選択します。
   ![Configuration Manager コンソールのスクリーンショット](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. パッケージのコピー先とする**[配布ポイント](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** を選択します。
3. ウィザードを終了します。 指定した配布ポイントへのパッケージのレプリケートが開始されます。
4. パッケージの配布が完了したら、パッケージを右クリックして **[展開]** を選択します。
   ![Configuration Manager コンソールのスクリーンショット](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. 展開のターゲット コレクションとして前提条件のセクションで作成した Windows Server デバイスのコレクションを選択します。

   ![ソフトウェアの展開ウィザードのスクリーンショット](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection.png)

6. **[コンテンツの配布先の指定]** ページで**配布ポイント**を選択します。
7. **[このソフトウェアの展開を制御する設定の指定]** ページで、目的が **[必須]** になっていることを確認します。

   ![ソフトウェアの展開ウィザードのスクリーンショット](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. **[この展開のスケジュールを指定します]** でスケジュールを指定します。 詳しくは、[パッケージのスケジュール設定](https://technet.microsoft.com/library/gg682178.aspx)に関する記事をご覧ください。
9. **[配布ポイント]** ページで、データ センターのニーズに応じてプロパティを構成します。 その後ウィザードを終了します。

> [!TIP]
> 不要な再起動を避けるためには、月次のメンテナンス期間中またはソフトウェアの更新期間中にパッケージのインストールをスケジュールします。

Configuration Manager コンソールを使用して、展開の進行状況を監視できます。 **[監視]**、 > **[展開]**、 > *[対象のパッケージ名]* と移動します。

  ![展開を監視する Configuration Manager のオプションのスクリーン ショット](./media/vmware-azure-mobility-install-configuration-mgr/report.PNG)

## <a name="deploy-on-linux-machines"></a>Linux マシンでデプロイする
> [!NOTE]
> この記事は、構成サーバーの IP アドレスが 192.168.3.121、セキュリティで保護されたネットワーク ファイル共有が \\\ContosoSecureFS\MobilityServiceInstallers であることを前提としています。

### <a name="prepare-for-deployment"></a>デプロイの準備をする
1. ネットワーク共有にフォルダーを作成し、**MobSvcLinux** という名前を付けます。
2. 構成サーバーにサインインし、管理者権限のコマンド プロンプトを開きます。
3. 次のコマンドを実行して、パスフレーズ ファイルを生成します。

    `cd %ProgramData%\ASR\home\svsystems\bin`

    `genpassphrase.exe -v > MobSvc.passphrase`
4. **MobSvc.passphrase** ファイルをネットワーク共有上の **MobSvcLinux** フォルダーにコピーします。
5. 次のコマンドを実行して構成サーバーでインストーラーのリポジトリに移動します。

   `cd %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository`

6. 次のファイルをネットワーク共有上の **MobSvcLinux** フォルダーにコピーします。
   * Microsoft-ASR\_UA\*RHEL6-64*release.tar.gz
   * Microsoft-ASR\_UA\*RHEL7-64\*release.tar.gz
   * Microsoft-ASR\_UA\*SLES11-SP3-64\*release.tar.gz
   * Microsoft-ASR\_UA\*SLES11-SP4-64\*release.tar.gz
   * Microsoft-ASR\_UA\*OL6-64\*release.tar.gz
   * icrosoft-ASR\_UA\*UBUNTU-14.04-64\*release.tar.gz


7. 次のコードをコピーして、**install_linux.sh** という名前で **MobSvcLinux** フォルダーに保存します。
   > [!NOTE]
   > このスクリプトの [CSIP] プレース ホルダーを、お使いの構成サーバーの IP アドレスの実際の値に置き換えてください。

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

1. Configuration Manager コンソールにサインインします。
2. **[ソフトウェア ライブラリ]** > 、**[アプリケーション管理]**、 > **[パッケージ]** と移動します。
3. **[パッケージ]** を右クリックして **[パッケージの作成]** を選択します。
4. 名前、説明、製造元、言語、バージョンの値を指定します。
5. **[このパッケージにソース ファイルを含める]** チェック ボックスをオンします。
6. **[参照]** ボタンをクリックして、インストーラーが格納されているネットワーク共有 (\\\ContosoSecureFS\MobilityServiceInstaller\MobSvcLinux) を選択します。

   ![パッケージとプログラムの作成ウィザードのスクリーンショット](./media/vmware-azure-mobility-install-configuration-mgr/create_sccm_package-linux.png)

7. **[作成するプログラムの種類の選択]** ページで、**[標準プログラム]** を選択して **[次へ]** をクリックします。

   ![パッケージとプログラムの作成ウィザードのスクリーンショット](./media/vmware-azure-mobility-install-configuration-mgr/sccm-standard-program.png)

8. **[この標準プログラムに関する情報の指定]** ページで、次の入力を指定して **[次へ]** をクリックします  (その他の入力は既定値を使用できます)。

    | **パラメーター名** | **値** |
   |--|--|
   | Name | Microsoft Azure Mobility Service のインストール (Linux) |
   | コマンド ライン | ./install_linux.sh |
   | プログラムの実行条件 | ユーザーがログオンしているかどうか |

   ![パッケージとプログラムの作成ウィザードのスクリーンショット](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-linux.png)

9. 次のページで、**[任意のプラットフォームで実行可能]** を選択します。
   ![パッケージとプログラムの作成ウィザードのスクリーンショット](./media/vmware-azure-mobility-install-configuration-mgr/sccm-program-properties-page2-linux.png)

10. **[次へ]** を 2 回クリックしてウィザードを終了します。

> [!NOTE]
> スクリプトでは、Mobility Service エージェントの新規インストールとインストール済みエージェントへの更新の両方をサポートします。

### <a name="deploy-the-package"></a>パッケージをデプロイする
1. Configuration Manager コンソールで、パッケージを右クリックして **[コンテンツの配布]** を選択します。
   ![Configuration Manager コンソールのスクリーンショット](./media/vmware-azure-mobility-install-configuration-mgr/sccm_distribute.png)
2. パッケージのコピー先とする**[配布ポイント](https://technet.microsoft.com/library/gg712321.aspx#BKMK_PlanForDistributionPoints)** を選択します。
3. ウィザードを終了します。 指定した配布ポイントへのパッケージのレプリケートが開始されます。
4. パッケージの配布が完了したら、パッケージを右クリックして **[展開]** を選択します。
   ![Configuration Manager コンソールのスクリーンショット](./media/vmware-azure-mobility-install-configuration-mgr/sccm_deploy.png)
5. 展開のターゲット コレクションとして前提条件のセクションで作成した Linux サーバー デバイスのコレクションを選択します。

   ![ソフトウェアの展開ウィザードのスクリーンショット](./media/vmware-azure-mobility-install-configuration-mgr/sccm-select-target-collection-linux.png)

6. **[コンテンツの配布先の指定]** ページで**配布ポイント**を選択します。
7. **[このソフトウェアの展開を制御する設定の指定]** ページで、目的が **[必須]** になっていることを確認します。

   ![ソフトウェアの展開ウィザードのスクリーンショット](./media/vmware-azure-mobility-install-configuration-mgr/sccm-deploy-select-purpose.png)

8. **[この展開のスケジュールを指定します]** でスケジュールを指定します。 詳しくは、[パッケージのスケジュール設定](https://technet.microsoft.com/library/gg682178.aspx)に関する記事をご覧ください。
9. **[配布ポイント]** ページで、データ センターのニーズに応じてプロパティを構成します。 その後ウィザードを終了します。

構成したスケジュールに従って、Linux サーバーのデバイス コレクションに Mobility Service がインストールされます。


## <a name="uninstall-the-mobility-service"></a>Mobility Service をアンインストールする
Configuration Manager パッケージを作成して Mobility Service をアンインストールできます。 これを行うには、次のスクリプトを実行します。

```
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

## <a name="next-steps"></a>次の手順
仮想マシンの[保護を有効にする](vmware-azure-enable-replication.md)準備をします。
