---
title: "Azure Automation の証明書資産 | Microsoft Docs"
description: "証明書を Azure Automation に安全に保存し、Runbook または DSC 構成でアクセスして Azure およびサードパーティのリソースで認証できます。  この記事では、証明書の詳細およびテキスト作成とグラフィカル作成の両方で証明書を使用する方法について説明します。"
services: automation
documentationcenter: 
author: mgoedtel
manager: stevenka
editor: tysonn
ms.assetid: ac9c22ae-501f-42b9-9543-ac841cf2cc36
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/19/2016
ms.author: magoedte;bwren
translationtype: Human Translation
ms.sourcegitcommit: 89e5486f3302098f3a1d49e4390ec5b21617d778
ms.openlocfilehash: fd1737a420c132dace9307436bfea98a9bde94a0

---

# <a name="certificate-assets-in-azure-automation"></a>Azure Automation の証明書資産

証明書を Azure Automation に安全に保存し、Azure Resource Manager リソースの **Get-AzureRmAutomationRmCertificate** アクティビティを使用して、Runbook または DSC 構成でアクセスできます。 そのため、認証に証明書を使用する Runbook または DSC 構成を作成したり、それらを Azure またはサードパーティのリソースに追加したりすることができます。

> [!NOTE] 
> Azure Automation でセキュリティ保護される資産としては、資格情報、証明書、接続、暗号化された変数などがあります。 これらの資産は、各 Automation アカウント用に生成された一意のキーを使用して暗号化され、Azure Automation に保存されます。 このキーはマスター証明書によって暗号化され、Azure Automation に保存されます。 セキュリティで保護された資産を格納する前に、Automation アカウントのキーがマスター証明書を使用して復号化され、資産の暗号化に使用されます。
> 

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell コマンドレット

Windows PowerShell で Automation 証明書資産を作成および管理するには、次の表のコマンドレットを使用します。 これらのコマンドレットは、Automation Runbook と DSC 構成に使用できる [Azure PowerShell モジュール](../powershell-install-configure.md) に付属しています。

|コマンドレット|Description|
|:---|:---|
|[Get-AzureRmAutomationCertificate](https://msdn.microsoft.com/library/mt603765.aspx)|Runbook または DSC 構成で使用する証明書についての情報を取得します。 Get-AutomationCertificate アクティビティから取得できるのは、証明書自体のみです。|
|[New-AzureRmAutomationCertificate](https://msdn.microsoft.com/library/mt603604.aspx)|新しい証明書を Azure Automation に作成します。|
[Remove-AzureRmAutomationCertificate](https://msdn.microsoft.com/library/mt603529.aspx)|証明書を Azure Automation から削除します。|新しい証明書を Azure Automation に作成します。
|[Set-AzureRmAutomationCertificate](https://msdn.microsoft.com/library/mt603760.aspx)|証明書ファイルのアップロードや .pfx のパスワードの設定など、既存の証明書のプロパティを設定します。|
|[Add-AzureCertificate](https://msdn.microsoft.com/library/azure/dn495214.aspx)|指定されたクラウド サービスのサービス証明書をアップロードします。|


## <a name="creating-a-new-certificate"></a>新しい証明書の作成

新しい証明書を作成するときは、cer または pfx ファイルを Azure Automation にアップロードします。 証明書をエクスポート可能とマークすると、Azure Automation の証明書ストアから転送できます。 エクスポート可能ではない場合は、Runbook または DSC 構成内での署名にのみ使用できます。


### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Azure ポータルで新しい証明書を作成するには

1. Automation アカウントから、**[資産]** タイルをクリックして **[資産]** ブレードを開きます。
1. **[証明書]** タイルをクリックして **[証明書]** ブレードを開きます。
1. ブレード上部の **[証明書の追加]** をクリックします。
2. Type a name for the certificate in the **Name** box.
2. Click **Select a file** under **Upload a certificate file** to browse for a .cer or .pfx file.  .pfx ファイルを選択する場合は、パスワードおよびエクスポートを許可するかどうかを指定します。
1. **[作成]** をクリックして、新しい証明書資産を保存します。


### <a name="to-create-a-new-certificate-with-windows-powershell"></a>Windows PowerShell で新しい証明書を作成するには

次の例では、新しい Automation 証明書を作成してエクスポート可能に指定する方法について説明します。 これは既存の .pfx ファイルをインポートします。

    $certName = 'MyCertificate'
    $certPath = '.\MyCert.pfx'
    $certPwd = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
    $ResourceGroup = "ResourceGroup01"
    
    New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certName -Path $certPath –Password $certPwd -Exportable -ResourceGroupName $ResourceGroup

## <a name="using-a-certificate"></a>証明書の使用

証明書を使用するには、 **Get-AutomationCertificate** アクティビティを使用する必要があります。 [Get-AzureRmAutomationCertificate](https://msdn.microsoft.com/library/mt603765.aspx) コマンドレットは、証明書資産に関する情報は返しますが証明書自体を返さないので使用できません。

### <a name="textual-runbook-sample"></a>テキストの Runbook のサンプル

次のサンプル コードでは、Runbook でクラウド サービスに証明書を追加する方法を示します。 このサンプルでは、パスワードは暗号化された Automation 変数から取得されます。

    $serviceName = 'MyCloudService'
    $cert = Get-AutomationCertificate -Name 'MyCertificate'
    $certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResouceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
    Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert

### <a name="graphical-runbook-sample"></a>グラフィカルな Runbook のサンプル

グラフィカル エディターの [ライブラリ] ウィンドウで証明書を右クリックして **[キャンバスに追加]** を選択することにより、**Get-AutomationCertificate** をグラフィカルな Runbook に追加します。

![証明書をキャンバスに追加します](media/automation-certificates/automation-certificate-add-to-canvas.png)

次の図は、グラフィカルな Runbook で証明書を使用する例を示したものです。  これは、テキストの Runbook からクラウド サービスに証明書を追加する前述の例と同じです。

![グラフィカル作成の例 ](media/automation-certificates/graphical-runbook-add-certificate.png)


## <a name="next-steps"></a>次のステップ

- リンクを使用して、Runbook で実行するように設計されているアクティビティの論理フローを制御する方法については、「[グラフィカル作成でのリンク](automation-graphical-authoring-intro.md#links-and-workflow)」をご覧ください。 



<!--HONumber=Feb17_HO2-->


