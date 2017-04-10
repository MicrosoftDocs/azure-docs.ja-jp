---
title: "DSC を使用して資格情報を Azure に渡す | Microsoft Docs"
description: "PowerShell Desired State Configuration を使用して Azure Virtual Machines に資格情報を安全に渡す方法の概要"
services: virtual-machines-windows
documentationcenter: 
author: zjalexander
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
keywords: 
ms.assetid: ea76b7e8-b576-445a-8107-88ea2f3876b9
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 09/15/2016
ms.author: zachal
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 19f3f67c7e8a09b38751365ad87785d687c2209f


---
# <a name="passing-credentials-to-the-azure-dsc-extension-handler"></a>資格情報を Azure DSC 拡張機能ハンドラーに渡す
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

この記事では、Azure の Desired State Configuration 拡張機能について説明します。 DSC 拡張機能ハンドラーの概要については、「[Azure Desired State Configuration 拡張機能ハンドラーの概要](virtual-machines-windows-extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。 

## <a name="passing-in-credentials"></a>資格情報を渡す
構成プロセスの一環で、ユーザー アカウントの設定、サービスへのアクセス、ユーザー コンテキストでのプログラムのインストールが必要になることがあります。 このような操作を行うには、資格情報を指定する必要があります。 

DSC では、資格情報を構成に渡し、MOF ファイルに安全に保存するパラメーター化された構成を使用できます。 Azure 拡張機能ハンドラーには証明書の自動管理機能があるので、資格情報管理が簡易になります。 

指定したパスワードでローカル ユーザー アカウントを作成するには、次のような DSC 構成スクリプトを使用します。

*user_configuration.ps1*

```
configuration Main
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PSCredential]
        $Credential
    )    
    Node localhost {       
        User LocalUserAccount
        {
            Username = $Credential.UserName
            Password = $Credential
            Disabled = $false
            Ensure = "Present"
            FullName = "Local User Account"
            Description = "Local User Account"
            PasswordNeverExpires = $true
        } 
    }  
} 
```

構成に *node localhost* を含めることが重要です。 拡張機能ハンドラーは特に node localhost ステートメントを探すため、このステートメントなしでは以下の手順は動作しません。 また、型キャスト *[PsCredential]* を含めることも重要です。この型を含めると、拡張機能によって資格情報が暗号化されます。 

次のスクリプトを Blob Storage に発行します。

`Publish-AzureVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Azure DSC 拡張機能を設定し、資格情報を指定します。

```
$configurationName = "Main"
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = "user_configuration.ps1.zip"
$vm = Get-AzureVM "example-1"

$vm = Set-AzureVMDSCExtension -VM $vm -ConfigurationArchive $configurationArchive 
-ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzureVM
```
## <a name="how-credentials-are-secured"></a>資格情報を保護する方法
このコードを実行すると、資格情報の入力を求められます。 入力すると、一時的にメモリに格納されます。 `Set-AzureVmDscExtension` コマンドレットを使用して発行すると、HTTPS 経由で VM に送信されます。VM では、Azure によってローカルの VM 証明書を使用してディスクで暗号化されます。 次に、メモリ内に一時的に復号化され、再暗号化されてから DSC に渡されます。

この動作は、 [拡張機能ハンドラーがない、セキュリティで保護された構成を使用する場合](https://msdn.microsoft.com/powershell/dsc/securemof)とは異なります。 Azure 環境には、証明書を使用して構成データを安全に送信する機能が用意されています。 そのため、DSC 拡張機能ハンドラーを使用する場合、ConfigurationData に $CertificatePath または $CertificateID / $Thumbprint エントリを指定する必要はありません。

## <a name="next-steps"></a>次のステップ
Azure DSC 拡張機能ハンドラーの詳細については、「 [Azure Desired State Configuration 拡張機能ハンドラーの概要](virtual-machines-windows-extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。 

[DSC 拡張機能用の Azure Resource Manager テンプレート](virtual-machines-windows-extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページをご覧ください。

PowerShell DSC の詳細については、 [PowerShell ドキュメント センター](https://msdn.microsoft.com/powershell/dsc/overview)を参照してください。 

PowerShell DSC で管理できる追加機能については、 [PowerShell ギャラリー](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) で DSC リソースを検索してください。




<!--HONumber=Nov16_HO3-->


