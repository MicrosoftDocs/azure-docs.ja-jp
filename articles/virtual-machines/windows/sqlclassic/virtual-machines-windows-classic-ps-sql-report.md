---
title: ネイティブ モードのレポート サーバーを実行する VM を PowerShell を使用して作成する | Microsoft Docs
description: 'このトピックでは、Azure 仮想マシンで SQL Server Reporting Services ネイティブ モードのレポート サーバーをデプロイおよび構成する手順について説明します。 '
services: virtual-machines-windows
documentationcenter: na
author: markingmyname
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: 553af55b-d02e-4e32-904c-682bfa20fa0f
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/11/2017
ms.author: maghan
ms.openlocfilehash: 6339b49d0bc9c635457f305dad7b1a075327a1dd
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58650276"
---
# <a name="use-powershell-to-create-an-azure-vm-with-a-native-mode-report-server"></a>ネイティブ モードのレポート サーバーを実行する Azure VM を PowerShell を使用して作成する
> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、2 種類のデプロイ モデルがあります。[Resource Manager とクラシック](../../../azure-resource-manager/resource-manager-deployment-model.md)です。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイメントでは、リソース マネージャー モデルを使用することをお勧めします。

このトピックでは、Azure 仮想マシンで SQL Server Reporting Services ネイティブ モードのレポート サーバーをデプロイおよび構成する手順について説明します。 このドキュメントの手順では、仮想マシンを手動で作成する手順と、VM で Reporting Services を構成する Windows PowerShell スクリプトを組み合わせて使用しています。 構成スクリプトでは、HTTP または HTTPS のファイアウォール ポートを開きます。

> [!NOTE]
> レポート サーバーで **HTTPS** を使用する必要がない場合は、**手順 2 をスキップ**してください。
> 
> 手順 1 で VM を作成したら、「スクリプトを使用してレポート サーバーと HTTP を構成する」に進みます。 スクリプトを実行すると、レポート サーバーの使用準備が完了します。

## <a name="prerequisites-and-assumptions"></a>前提条件
* **Azure サブスクリプション**: Azure サブスクリプションで使用できるコアの数を確認します。 推奨 VM サイズである **A3** で作成する場合は、使用可能なコアが **4 個**必要です。 VM サイズとして **A2** を使用する場合は、使用可能なコアが **2** 個必要です。
  
  * サブスクリプションのコアの上限を確認するには、Azure Portal の左側のウィンドウで [設定] をクリックし、上部のメニューの [使用状況] をクリックします。
  * コア クォータを増やすには、 [Azure サポート](https://azure.microsoft.com/support/options/)にお問い合わせください。 VM サイズについては、「 [Azure の仮想マシンのサイズ](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。
* **Windows PowerShell スクリプト**:このトピックは、Windows PowerShell の使用方法に関する基本的な知識があることを前提としています。 Windows PowerShell の使用方法の詳細については、次のトピックをご覧ください。
  
  * [Starting Windows PowerShell on Windows Server (Windows Server での Windows PowerShell の起動)](https://docs.microsoft.com/powershell/scripting/setup/starting-windows-powershell)
  * [Getting Started with Windows PowerShell (Windows PowerShell の概要)](https://technet.microsoft.com/library/hh857337.aspx)

## <a name="step-1-provision-an-azure-virtual-machine"></a>手順 1:Azure 仮想マシンをプロビジョニングする
1. Azure Portal にアクセスします。
2. 左側のウィンドウで、 **[Virtual Machines]** をクリックします。
   
    ![Microsoft Azure Virtual Machines](./media/virtual-machines-windows-classic-ps-sql-report/IC660124.gif)
3. **[新規]** をクリックします。
   
    ![[新規] ボタン](./media/virtual-machines-windows-classic-ps-sql-report/IC692019.gif)
4. **[ギャラリーから]** をクリックします。
   
    ![ギャラリーから新しい VM を作成](./media/virtual-machines-windows-classic-ps-sql-report/IC692020.gif)
5. **[SQL Server 2014 RTM Standard – Windows Server 2012 R2]** をクリックし、矢印をクリックして続行します。
   
    ![[次へ]](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
   
    Reporting Services のデータ ドリブン サブスクリプション機能が必要な場合は、 **[SQL Server 2014 RTM Enterprise – Windows Server 2012 R2]** を選択します。 SQL Server の各エディションと機能のサポートの詳細については、「 [SQL Server 2012 の各エディションがサポートする機能](https://msdn.microsoft.com/library/cc645993.aspx#Reporting)」をご覧ください。
6. **[仮想マシンの構成]** ページで、以下のフィールドを編集します。
   
   * **バージョンのリリース日**が複数存在する場合は、最新バージョンを選択します。
   * **[仮想マシン名]**: このマシン名は、次の構成ページで既定のクラウド サービス DNS 名としても使用されます。 DNS 名は、Azure サービス全体で一意であることが必要です。 VM の用途を示すコンピューター名で VM を構成するよう考慮してください  (例: ssrsnativecloud)。
   * **レベル**:Standard
   * **[サイズ]** : SQL Server のワークロードに推奨される VM サイズは A3 です。 VM をレポート サーバーとしてのみ使用する場合、レポート サーバーで大規模なワークロードが発生しないのであれば、VM サイズは A2 で十分です。 VM の価格については、「 [Virtual Machines の価格](https://azure.microsoft.com/pricing/details/virtual-machines/)」をご覧ください。
   * **[新しいユーザー名]**: 指定した名前が VM の管理者として作成されます。
   * **[新しいパスワード]** と **[確認]**: このパスワードは、新しい管理者アカウントに使用されるので、強力なパスワードを使用することをお勧めします。
   * **[次へ]** をクリックします。 ![next](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
7. 次のページでは、以下のフィールドを編集します。
   
   * **[クラウド サービス]**: **[新しいクラウド サービスの作成]** を選択します。
   * **クラウド サービス DNS 名**:これは、VM に関連付けられるクラウド サービスのパブリック DNS 名です。 既定の名前は、VM 名として入力した名前です。 このトピックの後の手順で信頼済み SSL 証明書を作成する場合、この DNS 名が証明書の **[発行先]** の値に使用されます。
   * **リージョン/アフィニティ グループ/仮想ネットワーク**:エンド ユーザーに最も近いリージョンを選択してください。
   * **[ストレージ アカウント]**: 自動的に生成されたストレージ アカウントを使用します。
   * **可用性セット**:なし。
   * **[エンドポイント]**: **[リモート デスクトップ]** エンドポイントと **[PowerShell]** エンドポイントを保持し、環境に応じて HTTP または HTTPS エンドポイントを追加します。
     
     * **HTTP**:既定のパブリック ポートとプライベート ポートは **80** です。 80 以外のプライベート ポートを使用する場合は、HTTP スクリプトの **$HTTPport = 80** を変更します。
     * **HTTPS**:既定のパブリック ポートとプライベート ポートは **443** です。 セキュリティのベスト プラクティスとして、プライベート ポートを変更し、そのプライベート ポートを使用するようにファイアウォールとレポート サーバーを構成することをお勧めします。 エンドポイントの詳細については、「 [仮想マシンに対してエンドポイントを設定する方法](../classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)」をご覧ください。 443 以外のポートを使用する場合は、HTTPS スクリプトの **$HTTPsport = 443** パラメーターを変更します。
   * [次へ] をクリックします。 ![[次へ]](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
8. ウィザードの最後のページでは、既定の **[VM エージェントのインストール]** が選択されている状態のままにしておきます。 このトピックの手順では、VM エージェントを使用しませんが、この VM を保持する場合は、VM エージェントと拡張機能により CM を機能強化することができます。  VM エージェントの詳細については、「[VM エージェントと拡張機能 - パート 1](https://azure.microsoft.com/blog/2014/04/11/vm-agent-and-extensions-part-1/)」をご覧ください。 インストールされ、実行されている既定の拡張機能の 1 つに "BGINFO" 拡張機能があります。この機能により、内部 IP やドライブの空き領域などのシステム情報が VM デスクトップに表示されます。
9. [完了] をクリックします。 ![Ok](./media/virtual-machines-windows-classic-ps-sql-report/IC660122.gif)
10. プロビジョニング処理の実行中、VM の **[状態]** に **[開始中 (プロビジョニング)]** と表示されます。VM がプロビジョニングされ、使用準備が完了すると、**[実行中]** と表示されます。

## <a name="step-2-create-a-server-certificate"></a>手順 2:サーバー証明書を作成する
> [!NOTE]
> レポート サーバーで HTTPS を使用する必要がない場合は、**手順 2 をスキップ**し、「**スクリプトを使用してレポート サーバーと HTTP を構成する**」に進んでください。 HTTP スクリプトを使用すると、レポート サーバーが迅速に構成され、レポート サーバーの使用準備が完了します。

VM で HTTPS を使用するには、信頼済み SSL 証明書が必要です。 シナリオに応じて、次の 2 つの方法のいずれかを使用できます。

* 証明機関 (CA) が発行し、Microsoft が信頼する有効な SSL 証明書。 CA ルート証明書が Microsoft ルート証明書プログラムによって配布されている必要があります。 このプログラムの詳細については、「[Windows and Windows Phone 8 SSL Root Certificate Program (Member CAs)](https://social.technet.microsoft.com/wiki/contents/articles/14215.windows-and-windows-phone-8-ssl-root-certificate-program-member-cas.aspx)」 (Windows および Windows Phone 8 SSL ルート証明書プログラム (メンバー CA))、および「[Introduction to The Microsoft Root Certificate Program](https://social.technet.microsoft.com/wiki/contents/articles/3281.introduction-to-the-microsoft-root-certificate-program.aspx)」 (Microsoft ルート証明書プログラムの概要) を参照してください。
* 自己署名証明書。 運用環境では、自己署名証明書を使用しないことをお勧めします。

### <a name="to-use-a-certificate-created-by-a-trusted-certificate-authority-ca"></a>信頼された証明機関 (CA) によって作成された証明書を使用するには
1. **証明機関に Web サイトのサーバー証明書を要求します**。 
   
    Web サーバー証明書ウィザードを使用して、証明機関に送信する証明書要求ファイル (Certreq.txt) を生成することも、オンライン証明機関  (Windows Server 2012 の Microsoft 証明書サービスなど) への要求を生成することもできます。 サーバー証明書が提供する ID 保証レベルに応じて、証明機関によって要求が承認され、証明書ファイルが送られてくるまで数日から数か月かかります。 
   
    サーバー証明書の要求の詳細については、以下をご覧ください。 
   
   * [Certreq](https://technet.microsoft.com/library/cc725793.aspx) の使用: [Certreq](https://technet.microsoft.com/library/cc725793.aspx)
   * Windows Server 2012 を管理するためのセキュリティ ツール: 
     
     [Security Tools to Administer Windows Server 2012 (Windows Server 2012 を管理するためのセキュリティ ツール)](https://technet.microsoft.com/library/jj730960.aspx)
     
     > [!NOTE]
     > 信頼済み SSL 証明書の **[発行先]** フィールドは、新しい VM に使用した **[クラウド サービス DNS 名]** と同じである必要があります。

2. **Web サーバーにサーバー証明書をインストールします**。 この例の Web サーバーは、レポート サーバーをホストする VM です。Web サイトは、後で Reporting Services を構成するときに作成されます。 証明書 MMC スナップインを使用して Web サーバーにサーバー証明書をインストールする方法の詳細については、「[Install a Server Certificate (サーバー証明書のインストール)](https://technet.microsoft.com/library/cc740068)」をご覧ください。
   
    このトピックに用意されているスクリプトを使用してレポート サーバーを構成する場合、スクリプトのパラメーターとして証明書の **[拇印]** の値が必要となります。 証明書の拇印を取得する方法の詳細については、次のセクションをご覧ください。
3. サーバー証明書をレポート サーバーに割り当てます。 この割り当ては、次のセクションでレポート サーバーを構成したときに完了します。

### <a name="to-use-the-virtual-machines-self-signed-certificate"></a>Virtual Machines の自己署名証明書を使用するには
VM をプロビジョニングしたときに、VM 上に自己署名証明書が作成されています。 この証明書には、VM の DNS 名と同じ名前が付けられています。 証明書エラーを防ぐためには、証明書が VM 上で信頼されているだけでなく、サイトのすべてのユーザーからも信頼されている必要があります。

1. ローカル VM 上の証明書のルート CA を信頼するには、証明書を **信頼されたルート証明機関**に追加する必要があります。 必要な手順の概要を次に示します。 CA を信頼する方法の詳しい手順については、「 [Install a Server Certificate (サーバー証明書のインストール)](https://technet.microsoft.com/library/cc740068)」をご覧ください。
   
   1. Azure Portal で VM を選択し、[接続] をクリックします。 ブラウザー構成によっては、VM に接続するために .rdp ファイルを保存するよう求められる場合があります。
      
       ![connect to azure virtual machine](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) VM の作成時に構成したユーザー VM 名、ユーザー名、パスワードを使用します。 
      
       たとえば、次の図では、VM 名が **ssrsnativecloud** で、ユーザー名が **testuser** です。
      
       ![ログインには VM 名が含まれる](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
   2. Mmc.exe を実行します。 詳細については、[方法:MMC スナップインを使用して証明書を参照する](https://msdn.microsoft.com/library/ms788967.aspx)」を参照してください。
   3. コンソール アプリケーションの **[ファイル]** メニューで、**[証明書]** スナップインを追加します。メッセージが表示されたら、**[コンピューター アカウント]** を選択し、**[次へ]** をクリックします。
   4. 管理対象として **[ローカル コンピューター]** を選択し、**[完了]** をクリックします。
   5. **[OK]** をクリックし、**[証明書 - 個人]** ノードを展開して、**[証明書]** をクリックします。 証明書の名前は VM の DNS 名に基づいており、 **cloudapp.net**で終わります。 証明書の名前を右クリックし、 **[コピー]** をクリックします。
   6. **[信頼されたルート証明機関]** ノードを展開し、**[証明書]** を右クリックして、**[貼り付け]** をクリックします。
   7. 検証するために、 **[信頼されたルート証明機関]** で証明書の名前をダブルクリックし、エラーがないことと、追加した証明書であることを確認します。 このトピックに用意されている HTTPS スクリプトを使用してレポート サーバーを構成する場合、スクリプトのパラメーターとして証明書の **[拇印]** の値が必要となります。 **拇印の値を取得するには**、次の手順を実行します。 「 [スクリプトを使用してレポート サーバーと HTTPS を構成する](#use-script-to-configure-the-report-server-and-https)」には、拇印を取得するための PowerShell サンプルも用意されています。
      
      1. 証明書の名前 (例: ssrsnativecloud.cloudapp.net) をダブルクリックします。
      2. **[詳細]** タブをクリックします。
      3. **[拇印]** をクリックします。 詳細フィールドに拇印の値が表示されます (例: ‎a6 08 3c df f9 0b f7 e3 7c 25 ed a4 ed 7e ac 91 9c 2c fb 2f)。
      4. 拇印をコピーし、後で編集できるように値を保存するか、スクリプトをすぐに編集します。
      5. (*) スクリプトを実行する前に、値の各ペアの間にあるスペースを削除します。 たとえば、上記の拇印は ‎a6083cdff90bf7e37c25eda4ed7eac919c2cfb2f になります。
      6. サーバー証明書をレポート サーバーに割り当てます。 この割り当ては、次のセクションでレポート サーバーを構成したときに完了します。

自己署名 SSL 証明書を使用している場合は、証明書の名前が VM のホスト名と既に一致しています。 したがって、マシンの DNS は既にグローバルに登録されており、どのクライアントからでもアクセスできます。

## <a name="step-3-configure-the-report-server"></a>手順 3:レポート サーバーを構成する
ここでは、Reporting Services ネイティブ モードのレポート サーバーとして VM を構成する手順を説明します。 レポート サーバーは、次のいずれかの方法を使用して構成できます。

* スクリプトを使用してレポート サーバーを構成する
* 構成マネージャーを使用してレポート サーバーを構成する

詳しい手順については、「 [仮想マシンへの接続と Reporting Services 構成マネージャーの起動](virtual-machines-windows-classic-ps-sql-bi.md#connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager)」をご覧ください。

**認証に関する注意:** Windows 認証が推奨される認証方法であり、Reporting Services の既定の認証となります。 VM 上で構成されているユーザーだけが Reporting Services にアクセスでき、Reporting Services ロールに割り当てられます。

### <a name="use-script-to-configure-the-report-server-and-http"></a>スクリプトを使用してレポート サーバーと HTTP を構成する
Windows PowerShell スクリプトを使用してレポート サーバーを構成するには、次の手順を実行します。 この構成には、HTTPS ではなく HTTP が含まれます。

1. Azure Portal で VM を選択し、[接続] をクリックします。 ブラウザー構成によっては、VM に接続するために .rdp ファイルを保存するよう求められる場合があります。
   
    ![connect to azure virtual machine](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) VM の作成時に構成したユーザー VM 名、ユーザー名、パスワードを使用します。 
   
    たとえば、次の図では、VM 名が **ssrsnativecloud** で、ユーザー名が **testuser** です。
   
    ![ログインには VM 名が含まれる](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
2. VM で、管理者特権を使用して **Windows PowerShell ISE** を開きます。 PowerShell ISE は、Windows Server 2012 に既定でインストールされています。 標準の Windows PowerShell ウィンドウではなく、ISE を使用することをお勧めします。ISE では、スクリプトを貼り付けて変更してから、スクリプトを実行できます。
3. Windows PowerShell ISE で、**[表示]** メニューをクリックし、**[スクリプト ウィンドウの表示]** をクリックします。
4. 次のスクリプトをコピーし、Windows PowerShell ISE のスクリプト ウィンドウに貼り付けます。
   
        ## This script configures a Native mode report server without HTTPS
        $ErrorActionPreference = "Stop"
   
        $server = $env:COMPUTERNAME
        $HTTPport = 80 # change the value if you used a different port for the private HTTP endpoint when the VM was created.
   
        ## Set PowerShell execution policy to be able to run scripts
        Set-ExecutionPolicy RemoteSigned -Force
   
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
   
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
   
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
   
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
   
        ## Report Server Configuration Steps
   
        ## Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
   
        ## ReserveURL for ReportServerWebService - port $HTTPport (for local usage)
            write-host "Calling ReserveURL port $HTTPport"
            $r = $RSObject.ReserveURL('ReportServerWebService',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportServer port $HTTPport" 
   
        ## Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
   
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS              ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
   
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
   
        ## Setting the Report Manager URL ##
   
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
   
        ## ReserveURL for ReportManager  - port $HTTPport
            write-host "Calling ReserveURL for ReportManager, port $HTTPport"
            $r = $RSObject.ReserveURL('ReportManager',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportManager port $HTTPport"
   
        write-host -foregroundcolor green "Open Firewall port for $HTTPport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## Open Firewall port for $HTTPport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $HTTPport)” -Direction Inbound –Protocol TCP –LocalPort $HTTPport
            write-host "Added rule Report Server (TCP on port $HTTPport) in Windows Firewall"
   
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
5. 80 以外の HTTP ポートを使用して VM を作成した場合は、$HTTPport = 80 パラメーターを変更します。
6. 現在、このスクリプトは Reporting Services 用に構成されています。 Reporting Services でこのスクリプトを実行する場合は、Get-WmiObject ステートメントで、名前空間のパスのバージョン部分を "v11" に変更します。
7. スクリプトを実行します。

**検証**:レポート サーバーの基本的な機能が動作していることを確認するには、このトピックで後述する「[構成を確認する](#verify-the-configuration)」セクションをご覧ください。

### <a name="use-script-to-configure-the-report-server-and-https"></a>スクリプトを使用してレポート サーバーと HTTPS を構成する
Windows PowerShell を使用してレポート サーバーを構成するには、次の手順を実行します。 この構成には、HTTP ではなく HTTPS が含まれます。

1. Azure Portal で VM を選択し、[接続] をクリックします。 ブラウザー構成によっては、VM に接続するために .rdp ファイルを保存するよう求められる場合があります。
   
    ![connect to azure virtual machine](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) VM の作成時に構成したユーザー VM 名、ユーザー名、パスワードを使用します。 
   
    たとえば、次の図では、VM 名が **ssrsnativecloud** で、ユーザー名が **testuser** です。
   
    ![ログインには VM 名が含まれる](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
2. VM で、管理者特権を使用して **Windows PowerShell ISE** を開きます。 PowerShell ISE は、Windows Server 2012 に既定でインストールされています。 標準の Windows PowerShell ウィンドウではなく、ISE を使用することをお勧めします。ISE では、スクリプトを貼り付けて変更してから、スクリプトを実行できます。
3. スクリプトの実行を有効にするには、次の Windows PowerShell コマンドを実行します。
   
        Set-ExecutionPolicy RemoteSigned
   
    次のコマンドを実行してポリシーを検証できます。
   
        Get-ExecutionPolicy
4. **Windows PowerShell ISE** で、**[表示]** メニューをクリックし、**[スクリプト ウィンドウの表示]** をクリックします。
5. 次のスクリプトをコピーし、Windows PowerShell ISE のスクリプト ウィンドウに貼り付けます。
   
        ## This script configures the report server, including HTTPS
        $ErrorActionPreference = "Stop"
        $httpsport=443 # modify if you used a different port number when the HTTPS endpoint was created.
   
        # You can run the following command to get (.cloudapp.net certificates) so you can copy the thumbprint / certificate hash
        #dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
        #
        # The certifacte hash is a REQUIRED parameter
        $certificatehash="" 
        # the certificate hash should not contain spaces
   
        if ($certificatehash.Length -lt 1) 
        {
            write-error "certificatehash is a required parameter"
        } 
        # Certificates should be all lower case
        $certificatehash=$certificatehash.ToLower()
        $server = $env:COMPUTERNAME
        # If the certificate is not a wildcard certificate, comment out the following line, and enable the full $DNSNAme reference.
        $DNSName="+"
        #$DNSName="$server.cloudapp.net"
        $DNSNameAndPort = $DNSName + ":$httpsport"
   
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
   
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
   
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
   
        write-host "The script will use $DNSNameAndPort as the DNS name and port" 
   
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
   
        ## Reporting Services Report Server Configuration Steps
   
        ## 1. Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
   
        ## ReserveURL for ReportServerWebService - port 80 (for local usage)
            write-host 'Calling ReserveURL port 80'
            $r = $RSObject.ReserveURL('ReportServerWebService','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportServer port 80" 
   
        ## ReserveURL for ReportServerWebService - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportServerWebService',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportServer port $httpsport" 
   
        ## CreateSSLCertificateBinding for ReportServerWebService port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport, with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportServerWebService',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportServer port $httpsport" 
   
        ## 2. Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
   
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS                    ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
   
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
   
        ## 3. Setting the Report Manager URL ##
   
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
   
        ## ReserveURL for ReportManager  - port 80
            write-host 'Calling ReserveURL for ReportManager, port 80'
            $r = $RSObject.ReserveURL('ReportManager','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportManager port 80"
   
        ## ReserveURL for ReportManager - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportManager',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportManager port $httpsport" 
   
        ## CreateSSLCertificateBinding for ReportManager port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportManager',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportManager port $httpsport" 
   
        write-host -foregroundcolor green "Open Firewall port for $httpsport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## Open Firewall port for $httpsport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $httpsport)” -Direction Inbound –Protocol TCP –LocalPort $httpsport
            write-host "Added rule Report Server (TCP on port $httpsport) in Windows Firewall"
   
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
6. スクリプトの **$certificatehash** パラメーターを変更します。
   
   * これは **必須** のパラメーターです。 以前の手順で証明書の値を保存しなかった場合は、次のいずれかの方法で、証明書の拇印から証明書のハッシュ値をコピーします。
     
       VM で Windows PowerShell ISE を開き、次のコマンドを実行します。
     
           dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
     
       出力は次のようになります。 スクリプトで空白行が返された場合は、たとえば VM で証明書が構成されていません。「[Virtual Machines の自己署名証明書を使用するには](#to-use-the-virtual-machines-self-signed-certificate)」を参照してください。
     
     または
   * VM で mmc.exe を実行し、 **証明書** スナップインを追加します。
   * **[信頼されたルート証明機関]** ノードで、証明書の名前をダブルクリックします。 VM の自己署名証明書を使用している場合、証明書の名前は VM の DNS 名に基づいており、 **cloudapp.net**で終わります。
   * **[詳細]** タブをクリックします。
   * **[拇印]** をクリックします。 詳細フィールドに拇印の値が表示されます (例: af 11 60 b6 4b 28 8d 89 0a 82 12 ff 6b a9 c3 66 4f 31 90 48)。
   * **スクリプトを実行する前に**、値の各ペアの間にあるスペースを削除します。 (例: af1160b64b288d890a8212ff6ba9c3664f319048)。
7. **$httpsport** パラメーターを変更します。 
   
   * HTTPS エンドポイントにポート 443 を使用した場合、スクリプトでこのパラメーターを更新する必要はありません。 それ以外の場合は、VM で HTTPS プライベート エンドポイントを構成したときに選択したポート値を使用します。
8. **$DNSName** パラメーターを変更します。 
   
   * このスクリプトは、ワイルドカード証明書 $DNSName="+" 用に構成されています。 ワイルドカード証明書バインド用に構成しない場合は、$DNSName="+" をコメント アウトし、次の行の完全な $DNSNAme 参照である ##$DNSName="$server.cloudapp.net" を有効にします。
     
       仮想マシンの DNS 名を Reporting Services に使用しない場合は、$DNSName 値を変更します。 このパラメーターを使用する場合は、証明書でも同じ名前を使用する必要があります。DNS サーバーでその名前をグローバルに登録します。
9. 現在、このスクリプトは Reporting Services 用に構成されています。 Reporting Services でこのスクリプトを実行する場合は、Get-WmiObject ステートメントで、名前空間のパスのバージョン部分を "v11" に変更します。
10. スクリプトを実行します。

**検証**:レポート サーバーの基本的な機能が動作していることを確認するには、このトピックで後述する「構成を確認する」セクションをご覧ください。 証明書バインドを確認するには、管理者特権でコマンド プロンプトを開き、次のコマンドを実行します。

    netsh http show sslcert

結果には次の行が含まれます。

    IP:port                      : 0.0.0.0:443

    Certificate Hash             : f98adf786994c1e4a153f53fe20f94210267d0e7

### <a name="use-configuration-manager-to-configure-the-report-server"></a>構成マネージャーを使用してレポート サーバーを構成する
レポート サーバーを構成する際に PowerShell スクリプトを実行しない場合は、このセクションの手順に従い、Reporting Services ネイティブ モード構成マネージャーを使用してレポート サーバーを構成します。

1. Azure Portal で VM を選択し、[接続] をクリックします。 VM の作成時に構成したユーザー名とパスワードを使用します。
   
    ![Azure 仮想マシンに接続](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif)
2. Windows Update を実行し、VM に更新プログラムをインストールします。 VM の再起動が必要な場合は、VM を再起動し、Azure Portal から VM に再接続します。
3. VM の [スタート] メニューで「**Reporting Services**」と入力し、**Reporting Services 構成マネージャー**を開きます。
4. **[サーバー名]** と **[レポート サーバー インスタンス]** は既定値のままにしておきます。 **[接続]** をクリックします。
5. 左側のウィンドウで、 **[Web サービス URL]** をクリックします。
6. 既定では、RS は HTTP ポート 80 で構成され、IP は "すべて割り当て" に設定されます。 HTTPS を追加するには、次の手順を実行します。
   
   1. **[SSL 証明書]** で、使用する証明書を選択します。たとえば、[VM 名].cloudapp.net を選択します。 証明書がリストされない場合は、「**手順 2:サーバー証明書を作成する**」セクションを参照し、VM に証明書をインストールして信頼する方法を確認してください。
   2. **[SSL ポート]** で 443 を選択します。 VM で別のプライベート ポートを使用して HTTPS プライベート エンドポイントを構成した場合は、ここでその値を使用します。
   3. **[適用]** をクリックし、処理が完了するまで待ちます。
7. 左側のウィンドウで、 **[データベース]** をクリックします。
   
   1. **[データベースの変更]** をクリックします。
   2. **[新しいレポート サーバー データベースを作成する]** をクリックし、**[次へ]** をクリックします。
   3. **[サーバー名]** では、既定の VM 名をそのまま使用します。**[認証の種類]** も、既定の **[現在のユーザー** - **統合セキュリティ]** のままにしておきます。 **[次へ]** をクリックします。
   4. **[データベース名]** を既定の **[ReportServer]** のままにし、**[次へ]** をクリックします。
   5. **[認証の種類]** を既定の **[サービス資格情報]** のままにし、**[次へ]** をクリックします。
   6. 左側のウィンドウで、 **[次へ]** on the **[次へ]** をクリックします。
   7. 構成が完了したら、 **[完了]** をクリックします。
8. 左側のウィンドウで、 **[レポート マネージャー URL]** をクリックします。 **[仮想ディレクトリ]** を既定の **[Reports]** のままにし、**[適用]** をクリックします。
9. **[終了]** をクリックして、Reporting Services 構成マネージャーを閉じます。

## <a name="step-4-open-windows-firewall-port"></a>手順 4:Windows ファイアウォール ポートを開く
> [!NOTE]
> いずれかのスクリプトを使用してレポート サーバーを構成した場合は、このセクションをスキップしてかまいません。 スクリプトにファイアウォール ポートを開く手順が含まれていました。 既定のポートは、HTTP では 80、HTTPS では 443 です。
> 
> 

仮想マシン上のレポート マネージャーまたはレポート サーバーにリモートから接続するには、VM に TCP エンドポイントが必要です。 また、VM のファイアウォールで同じポートを開く必要があります。 エンドポイントは、VM をプロビジョニングしたときに作成されています。

ここでは、ファイアウォール ポートを開く方法に関する基本情報を提供します。 詳細については、「 [レポート サーバー アクセスに対するファイアウォールの構成](https://technet.microsoft.com/library/bb934283.aspx)

> [!NOTE]
> スクリプトを使用してレポート サーバーを構成した場合は、このセクションをスキップしてかまいません。 スクリプトにファイアウォール ポートを開く手順が含まれていました。
> 
> 

HTTPS のプライベート ポートを 443 以外で構成した場合は、以下のスクリプトを適切に変更してください。 Windows ファイアウォールでポート **443** を開くには、次の手順を実行します。

1. 管理者特権で Windows PowerShell ウィンドウを開きます。
2. VM で HTTPS エンドポイントを構成したときに 443 以外のポートを使用した場合は、次のコマンド内のポートを更新してから、コマンドを実行します。
   
        New-NetFirewallRule -DisplayName “Report Server (TCP on port 443)” -Direction Inbound –Protocol TCP –LocalPort 443
3. コマンドが完了すると、コマンド プロンプトに " **Ok** " と表示されます。

ポートが開いていることを確認するには、Windows PowerShell ウィンドウを開き、次のコマンドを実行します。

    get-netfirewallrule | where {$_.displayname -like "*report*"} | select displayname,enabled,action

## <a name="verify-the-configuration"></a>構成を確認する
レポート サーバーの基本的な機能が動作していることを確認するには、管理者特権でブラウザーを開き、次のレポート サーバー URL とレポート マネージャー URL を参照します。

* VM で、レポート サーバー URL を参照します。
  
        http://localhost/reportserver
* VM で、レポート マネージャー URL を参照します。
  
        http://localhost/Reports
* ローカル コンピューターから、VM 上の**リモート** レポート マネージャーを参照します。 必要に応じて、次の例の DNS 名を更新します。 パスワードの入力を求められたら、VM をプロビジョニングしたときに作成した管理者資格情報を使用します。 ユーザー名は、[ドメイン]\[ユーザー名] の形式で指定します。ドメインは VM のコンピューター名です。たとえば、「ssrsnativecloud\testuser」のように指定します。 HTTP**S** を使用していない場合は、URL の **s** を削除します。 VM で追加のユーザーを作成する方法については、次のセクションをご覧ください。
  
        https://ssrsnativecloud.cloudapp.net/Reports
* ローカル コンピューターから、リモート レポート サーバー URL を参照します。 必要に応じて、次の例の DNS 名を更新します。 HTTPS を使用していない場合は、URL の "s" を削除します。
  
        https://ssrsnativecloud.cloudapp.net/ReportServer

## <a name="create-users-and-assign-roles"></a>ユーザーを作成してロールを割り当てる
レポート サーバーを構成して確認したら、一般的な管理タスクとして、1 人以上のユーザーを作成し、ユーザーを Reporting Services ロールに割り当てます。 詳細については、「

* [ローカル ユーザー アカウントを作成する](https://technet.microsoft.com/library/cc770642.aspx)
* [レポート サーバーへのユーザー アクセスを許可する (レポート マネージャー)](https://msdn.microsoft.com/library/ms156034.aspx)
* [ロールの割り当てを作成および管理する](https://msdn.microsoft.com/library/ms155843.aspx)

## <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>レポートを作成して Azure 仮想マシンに発行するには
Microsoft Azure 仮想マシンでホストされているレポート サーバーに、オンプレミスのコンピューターから既存のレポートを発行する際に使用できるオプションの一部を次に示します。

* **RS.exe スクリプト**:RS.exe スクリプトを使用して、既存のレポート サーバーから Microsoft Azure 仮想マシンにレポート アイテムをコピーします。 詳細については、「 [レポート サーバー間でコンテンツを移行するサンプル Reporting Services rs.exe スクリプト](https://msdn.microsoft.com/library/dn531017.aspx)」の「ネイティブ モードからネイティブ モード (Microsoft Azure Virtual Machine)」をご覧ください。
* **レポート ビルダー**:仮想マシンには、ClickOnce バージョンの Microsoft SQL Server レポート ビルダーが含まれています。 仮想マシンでレポート ビルダーを初めて起動するときは、次の手順を実行します。
  
  1. 管理者特権でブラウザーを起動します。
  2. 仮想マシン上のレポート マネージャーを参照し、リボンの **[レポート ビルダー]** をクリックします。
     
     詳細については、「 [レポート ビルダーのインストール、アンインストール、およびサポート](https://technet.microsoft.com/library/dd207038.aspx)」をご覧ください。
* **SQL Server Data Tools:VM**:SQL Server 2012 で VM を作成した場合、SQL Server Data Tools が仮想マシンにインストールされます。このツールを使用して、仮想マシンで**レポート サーバー プロジェクト**とレポートを作成できます。 SQL Server Data Tools では、仮想マシン上のレポート サーバーにレポートを発行できます。
  
    SQL Server 2014 で VM を作成した場合は、SQL Server Data Tools - BI for Visual Studio をインストールできます。 詳細については、「
  
  * [Microsoft SQL Server Data Tools - Business Intelligence for Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313)
  * [Microsoft SQL Server Data Tools - Business Intelligence for Visual Studio 2012](https://www.microsoft.com/download/details.aspx?id=36843)
  * [SQL Server Data Tools and SQL Server Business Intelligence (SSDT-BI)](https://docs.microsoft.com/sql/ssdt/previous-releases-of-sql-server-data-tools-ssdt-and-ssdt-bi)
* **SQL Server Data Tools:Remote**:ローカル コンピューター上の SQL Server Data Tools で、Reporting Services レポートを含む Reporting Services プロジェクトを作成します。 Web サービス URL に接続するようにプロジェクトを構成します。
  
    ![SSRS プロジェクトの SSDT プロジェクト プロパティ](./media/virtual-machines-windows-classic-ps-sql-report/IC650114.gif)
* **スクリプトの使用**:スクリプトを使用して、レポート サーバーのコンテンツをコピーします。 詳細については、「 [レポート サーバー間でコンテンツを移行するサンプル Reporting Services rs.exe スクリプト](https://msdn.microsoft.com/library/dn531017.aspx)」をご覧ください。

## <a name="minimize-cost-if-you-are-not-using-the-vm"></a>VM を使用していない場合にコストを最小限に抑える
> [!NOTE]
> Azure Virtual Machines を使用していないときに料金を最小限に抑えるには、Azure Portal から VM をシャットダウンします。 VM の内部で Windows の電源オプションを使用して VM をシャットダウンしても、VM に対して同じ金額が課金されます。 料金を削減するには、Azure Portal で VM をシャットダウンする必要があります。 VM が不要になった場合は、ストレージ料金がかからないように、VM および関連する .vhd ファイルを必ず削除してください。詳細については、「[Virtual Machines の価格](https://azure.microsoft.com/pricing/details/virtual-machines/)」の「FAQ」をご覧ください。

## <a name="more-information"></a>詳細情報
### <a name="resources"></a>リソース
* SQL Server Business Intelligence と SharePoint 2013 のシングル サーバー デプロイに関する類似コンテンツについては、「 [Use Windows PowerShell to Create an Azure VM With SQL Server BI and SharePoint 2013 (SQL Server BI と SharePoint 2013 を実行する Azure VM を Windows PowerShell を使用して作成する)](https://blogs.technet.microsoft.com/ptsblog/2013/10/24/use-powershell-to-create-a-windows-azure-vm-with-sql-server-bi-and-sharepoint-2013/)」をご覧ください。
* Azure Virtual Machines の SQL Server Business Intelligence のデプロイに関する一般情報については、「 [SQL Server Business Intelligence in Azure Virtual Machines (Azure Virtual Machines の SQL Server Business Intelligence)](virtual-machines-windows-classic-ps-sql-bi.md)」をご覧ください。
* Azure Compute のコストの詳細については、「 [Azure 価格計算ツール](https://azure.microsoft.com/pricing/calculator/?scenario=virtual-machines)」の [Virtual Machines] タブをご覧ください。

### <a name="community-content"></a>コミュニティ コンテンツ
* スクリプトを使用せずに Reporting Services ネイティブ モードのレポート サーバーを作成する方法の詳しい手順については、「 [Hosting SQL Reporting Service on Azure Virtual Machine (Azure Virtual Machine での SQL Reporting Services のホスト)](http://adititechnologiesblog.blogspot.in/2012/07/hosting-sql-reporting-service-on-azure.html)」をご覧ください。

### <a name="links-to-other-resources-for-sql-server-in-azure-vms"></a>Azure VM の SQL Server に関するその他のリソースへのリンク
[Azure Virtual Machines における SQL Server の概要](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

