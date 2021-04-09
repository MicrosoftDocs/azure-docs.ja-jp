---
title: Desired State Configuration を使用して Azure に資格情報を渡す
description: PowerShell Desired State Configuration (DSC) を使用して Azure 仮想マシンに資格情報を安全に渡す方法を説明します。
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: bobbytreed
ms.author: robreed
ms.collection: windows
ms.date: 05/02/2018
ms.openlocfilehash: 6817dd6baacd835b7d433177ff18af1238ee44a6
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560080"
---
# <a name="pass-credentials-to-the-azure-dscextension-handler"></a>資格情報を Azure DSC 拡張機能ハンドラーに渡す

この記事では、Azure の Desired State Configuration (DSC) 拡張機能について説明します。 DSC 拡張機能ハンドラーの概要については、「[Azure Desired State Configuration 拡張機能ハンドラーの概要](dsc-overview.md)」を参照してください。

 

## <a name="pass-in-credentials"></a>資格情報を渡す

構成プロセスの一環で、ユーザー アカウントの設定、サービスへのアクセス、ユーザー コンテキストでのプログラムのインストールが必要になることがあります。 このような操作を行うには、資格情報を指定する必要があります。

DSC を使用して、パラメーター化された構成を設定できます。 パラメーター化された構成では、資格情報が構成に渡され、.mof ファイルに安全に格納されます。 Azure 拡張機能ハンドラーには証明書の自動管理機能があるので、資格情報管理が簡易になります。

次の DSC 構成スクリプトにより、指定したパスワードでローカル ユーザー アカウントが作成されます。

```powershell
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

構成に **node localhost** を含めることが重要です。 拡張機能ハンドラーは、特に **node localhost** ステートメントを探します。 このステートメントがない場合、その後のステップは実行されません。 型キャスト **[PsCredential]** を含めることも重要です。 この特定の型が、資格情報を暗号化するように拡張機能をトリガーします。

このスクリプトを Azure Blob Storage に発行するには、次のコマンドを使用します。

`Publish-AzVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Azure DSC 拡張機能を設定して資格情報を指定するには、次を使用します。

```powershell
$configurationName = 'Main'
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = 'user_configuration.ps1.zip'
$vm = Get-AzVM -Name 'example-1'

$vm = Set-AzVMDscExtension -VMName $vm -ConfigurationArchive $configurationArchive -ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzVM
```

## <a name="how-a-credential-is-secured"></a>資格情報を保護する方法

このコードを実行すると、資格情報の入力を求められます。 提供された資格情報は、短期間メモリに格納されます。 **Set-AzVMDscExtension** コマンドレットを使用して資格情報が公開されると、資格情報は HTTPS 経由で VM に送信されます。 VM では、Azure がローカル VM 証明書を使用して、暗号化された資格情報をディスクに格納します。 資格情報はメモリ内で一時的に復号化された後、再暗号化されてから DSC に渡されます。

このプロセスは、[拡張機能ハンドラーがない、セキュリティで保護された構成を使用する場合](/powershell/scripting/dsc/pull-server/securemof)とは異なります。 Azure 環境には、証明書を使用して構成データを安全に送信する機能が用意されています。 そのため、DSC 拡張機能ハンドラーを使用する場合、**ConfigurationData** に **$CertificatePath** または **$CertificateID**/ **$Thumbprint** エントリを指定する必要はありません。

## <a name="next-steps"></a>次のステップ

- [Azure DSC 拡張機能ハンドラーの概要](dsc-overview.md)を確認します。
- [DSC 拡張機能用の Azure Resource Manager テンプレート](dsc-template.md)に関するページをご覧ください。
- PowerShell DSC の詳細については、[PowerShell ドキュメント センター](/powershell/scripting/dsc/overview/overview)を参照してください。
- PowerShell DSC を使用して管理できる機能とその他の DSC のリソースについては、[PowerShell ギャラリー](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0)を参照してください。