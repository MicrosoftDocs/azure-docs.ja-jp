---
title: "既定の VM イメージの Azure Stack Marketplace への追加 | Microsoft Docs"
description: "Windows Server 2016 VM の既定のイメージを Azure Stack Marketplace に追加します。"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/10/2017
ms.author: sngun
ms.openlocfilehash: 43781cb025865df1d228376f57412f3d482d3ad0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="add-the-windows-server-2016-vm-image-to-the-azure-stack-marketplace"></a>Windows Server 2016 VM イメージの Azure Stack Marketplace への追加

既定では、Azure Stack Marketplace には使用可能な仮想マシン イメージはありません。 ユーザーがイメージを使用できるようにするには、Azure Stack オペレーターがイメージをマーケットプレースに追加する必要があります。 Windows Server 2016 イメージを Azure Stack Marketplace に追加するには、次の 2 つの方法の 1 つを使用します。

* [Azure Marketplace からイメージをダウンロードして追加する](#add-the-image-by-downloading-it-from-the-Azure-marketplace) - 接続されたシナリオで操作しており、Azure Stack インスタンスを Azure に登録している場合は、このオプションを使用します。

* [PowerShell を使用してイメージを追加する](#add-the-image-by-using-powershell) - 接続が切断されたシナリオで、または接続が制限されたシナリオで Azure Stack をデプロイした場合は、このオプションを使用します。

## <a name="add-the-image-by-downloading-it-from-the-azure-marketplace"></a>Azure Marketplace からイメージをダウンロードして追加

1. Azure Stack のデプロイ後に、Azure Stack Development Kit にサインインします。

2. **[More services]** (その他のサービス) > **[Marketplace Management]** (Marketplace の管理) > **[Add from Azure]** (Azure から追加) をクリックします 

3. **Windows Server 2016 Datacenter – Eval** イメージを検索し、**[Download]** (ダウンロード) をクリックします

   ![Azure からのイメージのダウンロード](media/azure-stack-add-default-image/download-image.png)

ダウンロードの完了後、イメージは **[Marketplace Management]** (Marketplace の管理) ブレードに追加され、**[Virtual Machines]** (仮想マシン) ブレードからも使用できるようになります。

## <a name="add-the-image-by-using-powershell"></a>PowerShell を使用したイメージの追加

### <a name="prerequisites"></a>前提条件 

[開発キット](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop)から、または [VPN 経由で接続](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)している場合は Windows ベースの外部クライアントから、次の前提条件を実行します。

* [Azure Stack と互換性のある Azure PowerShell モジュール](azure-stack-powershell-install.md)をインストールします。  

* [Azure Stack を操作するために必要なツール](azure-stack-powershell-download.md)をダウンロードします。  

* https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2016 に移動し、Windows Server 2016 評価版をダウンロードします。 メッセージが表示されたら、ダウンロードの **ISO** バージョンを選択します。 この手順の後の方で使用できるよう、ダウンロード先のパスを記録します。 この手順では、インターネット接続が必要です。  

ここで、以下の手順を実行して、Azure Stack Marketplace にイメージを追加します。
   
1. 次のコマンドを使用して、Azure Stack Connect および ComputeAdmin モジュールをインポートします。

   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # import the Connect and ComputeAdmin modules   
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   ```

2. Azure Stack 環境にサインインします。 Azure Stack 環境が AAD と AD FS のどちらを使用してデプロイされているかに応じて、次のスクリプトを実行します (環境の構成に従って、必ず AAD tenantName、GraphAudience エンドポイント、および ArmEndpoint の値を置き換えてください)。  

   a. **Azure Active Directory** の場合は、次のコマンドレットを使用します。

   ```PowerShell
   # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
   $ArmEndpoint = "<Resource Manager endpoint for your environment>"

   # For Azure Stack development kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
   $GraphAudience = "<GraphAuidence endpoint for your environment>"
   
   # Create the Azure Stack operator's AzureRM environment by using the following cmdlet:
   Add-AzureRMEnvironment `
     -Name "AzureStackAdmin" `
     -ArmEndpoint $ArmEndpoint

   Set-AzureRmEnvironment `
    -Name "AzureStackAdmin" `
    -GraphAudience $GraphAudience

   $TenantID = Get-AzsDirectoryTenantId `
     -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
     -EnvironmentName AzureStackAdmin

   Login-AzureRmAccount `
     -EnvironmentName "AzureStackAdmin" `
     -TenantId $TenantID 
   ```

   b. **Active Directory フェデレーション サービス (AD FS)** の場合は、次のコマンドレットを使用します。
    
   ```PowerShell
   # For Azure Stack development kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
   $ArmEndpoint = "<Resource Manager endpoint for your environment>"

   # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
   $GraphAudience = "<GraphAuidence endpoint for your environment>"

   # Create the Azure Stack operator's AzureRM environment by using the following cmdlet:
   Add-AzureRMEnvironment `
     -Name "AzureStackAdmin" `
     -ArmEndpoint $ArmEndpoint

   Set-AzureRmEnvironment `
     -Name "AzureStackAdmin" `
     -GraphAudience $GraphAudience `
     -EnableAdfsAuthentication:$true

   $TenantID = Get-AzsDirectoryTenantId `
     -ADFS 
     -EnvironmentName AzureStackAdmin 

   Login-AzureRmAccount `
     -EnvironmentName "AzureStackAdmin" `
     -TenantId $TenantID 
   ```
   
3. Windows Server 2016 イメージを Azure Stack Marketplace に追加します (必ず *Path_to_ISO* を、ダウンロードした WS2016 ISO のパスに置き換えてください)。

   ```PowerShell
   $ISOPath = "<Fully_Qualified_Path_to_ISO>"

   # Add a Windows Server 2016 Evaluation VM Image.
   New-AzsServer2016VMImage `
     -ISOPath $ISOPath

   ```

Windows Server 2016 VM イメージに最新の累積的更新プログラムを確実に適用するには、`New-AzsServer2016VMImage` コマンドレットの実行時に `IncludeLatestCU` パラメーターを含めます。 `New-AzsServer2016VMImage` コマンドレットで許可されるパラメーターの詳細については、「[パラメーター](#parameters)」セクションを参照してください。 イメージを Azure Stack Marketplace に公開するには、1 時間ほどかかります。 

## <a name="parameters"></a>parameters

|New-AzsServer2016VMImage パラメーター|必須|Description|
|-----|-----|------|
|ISOPath|あり|ダウンロードした Windows Server 2016 ISO の完全修飾パス。|
|Net35|いいえ|このパラメーターを使用すると、Windows Server 2016 イメージに .NET 3.5 ランタイムをインストールできます。 既定では、この値は true に設定されます。|
|バージョン|いいえ|このパラメーターを使用すると、**[Core]** (コア)、**[Full]** (完全)、**[Both]** (両方) の Windows Server 2016 イメージを追加するかどうかを選択できます。 既定では、この値は [Full] (完全) に設定されます。|
|VHDSizeInMB|いいえ|Azure Stack 環境に追加する VHD イメージのサイズ (MB 単位) を設定します。 既定では、この値は 40960 MB に設定されます。|
|CreateGalleryItem|いいえ|Windows Server 2016 イメージの Marketplace 項目を作成するかどうかを指定します。 既定では、この値は true に設定されます。|
|location |いいえ |Windows Server 2016 イメージを公開する場所を指定します。|
|IncludeLatestCU|いいえ|このスイッチを設定すると、最新の Windows Server 2016 累積的更新プログラムが新しい VHD に適用されます。|
|CUUri |いいえ |この値を設定すると、特定の URI から Windows Server 2016 累積的更新プログラムを選択できます。 |
|CUPath |いいえ |この値を設定すると、ローカル パスから Windows Server 2016 累積的更新プログラムを選択できます。 このオプションは、接続が切断された環境で Azure Stack インスタンスをデプロイしている場合に便利です。|

## <a name="next-steps"></a>次のステップ

[仮想マシンのプロビジョニング](azure-stack-provision-vm.md)
