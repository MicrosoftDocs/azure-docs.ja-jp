---
services: virtual-machines
title: "PowerShell のセットアップ"
author: JoeDavies-MSFT
solutions: 
manager: timlt
editor: tysonn
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 05/12/2015
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: b3fd172d8dc468780d483821d7067c053e39968e
ms.openlocfilehash: 19c704d965ff3e2fc9ac8c5b623aeb386cb0b974


---
## <a name="setting-up-powershell"></a>PowerShell のセットアップ
Azure PowerShell を使用する前に次の手順を実行します。

### <a name="verify-powershell-versions"></a>PowerShell のバージョンを確認する
Windows PowerShell を使用するには、Windows PowerShell Version 3.0 か 4.0 が必要です。 Windows PowerShell のバージョンを確認するには、Windows PowerShell コマンド プロンプトで次のコマンドを入力します。

    $PSVersionTable

次のような結果が表示されます。

    Name                           Value
    ----                           -----
    PSVersion                      3.0
    WSManStackVersion              3.0
    SerializationVersion           1.1.0.1
    CLRVersion                     4.0.30319.18444
    BuildVersion                   6.2.9200.16481
    PSCompatibleVersions           {1.0, 2.0, 3.0}
    PSRemotingProtocolVersion      2.2

**PSVersion** の値が 3.0 または 4.0 であることを確認します。 互換バージョンをインストールするには、「[Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595)」または「[Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)」を参照してください。

また、Azure PowerShell Version 0.8.0 以降も必要です。 インストールした Azure PowerShell のバージョンは、Azure PowerShell コマンド プロンプトで次のコマンドを使用して確認できます。

    Get-Module azure | format-table version

次のような結果が表示されます。

    Version
    -------
    0.8.16.1

最新バージョンの説明とダウンロード用リンクについては、[Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)に関するページを参照してください。

### <a name="set-your-azure-account-and-subscription"></a>Azure アカウントとサブスクリプションを設定する
Azure サブスクリプションを持っていない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を有効にするか、[無料試用版](https://azure.microsoft.com/pricing/free-trial/)にサインアップしてください。

Azure PowerShell コマンド プロンプトを開き、次のコマンドで Azure にログオンします。

    Add-AzureAccount

Azure サブスクリプションが複数ある場合は、次のコマンドで、Azure サブスクリプションの一覧を表示できます。

    Get-AzureSubscription

次の種類の情報が表示されます。

    SubscriptionId            : fd22919d-eaca-4f2b-841a-e4ac6770g92e
    SubscriptionName          : Visual Studio Ultimate with MSDN
    Environment               : AzureCloud
    SupportedModes            : AzureServiceManagement,AzureResourceManager
    DefaultAccount            : johndoe@contoso.com
    Accounts                  : {johndoe@contoso.com}
    IsDefault                 : True
    IsCurrent                 : True
    CurrentStorageAccountName : 
    TenantId                  : 32fa88b4-86f1-419f-93ab-2d7ce016dba7

Azure PowerShell コマンド プロンプトで次のコマンドを実行して、現在の Azure サブスクリプションを設定します。 引用符内のすべての文字 (< および > を含む) を、正しい名前に置き換えます。

    $subscr="<SubscriptionName from the display of Get-AzureSubscription>"
    Select-AzureSubscription -SubscriptionName $subscr -Current    

Azure サブスクリプションとアカウントの詳細については、[サブスクリプションへの接続方法](/powershell/azureps-cmdlets-docs#Connect)に関するトピックを参照してください。




<!--HONumber=Jan17_HO3-->


