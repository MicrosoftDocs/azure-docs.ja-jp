---
title: Azure Stack Edge Pro GPU デバイスの Azure Resource Manager に接続する
description: Azure PowerShell を使用して、Azure Stack Edge Pro GPU 上で実行されている Azure Resource Manager に接続する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: 27c0c211b9844a34b0cb43dcd05037ba4a548f3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102637084"
---
# <a name="connect-to-azure-resource-manager-on-your-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro デバイスの Azure Resource Manager に接続する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Resource Manager には、Azure サブスクリプションのリソースを作成、更新、削除できる管理レイヤーが用意されています。 Azure Stack Edge Pro デバイスにより、ローカル サブスクリプションで VM を作成、更新、削除する同じ Azure Resource Manager API がサポートされています。 このサポートにより、クラウドと一貫性のある方法でデバイスを管理できるようになります。 

このチュートリアルでは、Azure PowerShell を使用して Azure Resource Manager を介して Azure Stack Edge Pro デバイスのローカル API に接続する方法について説明します。

## <a name="about-azure-resource-manager"></a>Azure Resource Manager について

Azure Resource Manager には、Azure Stack Edge Pro デバイス API を呼び出したり、VM の作成、更新、削除などの操作を実行したりするための一貫した管理レイヤーが用意されています。 Azure Resource Manager のアーキテクチャの詳細を次の図に示します。

![Azure Resource Manager の図](media/azure-stack-edge-gpu-connect-resource-manager/edge-device-flow.svg)


## <a name="endpoints-on-azure-stack-edge-pro-device"></a>Azure Stack Edge Pro デバイスのエンドポイント

次の表は、デバイスで公開されているさまざまなエンドポイント、サポートされているプロトコル、およびそれらのエンドポイントにアクセスするためのポートをまとめたものです。 この記事全体を通して、これらのエンドポイントへの参照が見つかります。

| # | エンドポイント | サポートされるプロトコル | 使用されるポート | 使用目的 |
| --- | --- | --- | --- | --- |
| 1. | Azure Resource Manager | https | 443 | オートメーションのために Azure Resource Manager に接続する |
| 2. | セキュリティ トークン サービス | https | 443 | アクセス トークンと更新トークンを使用して認証する |
| 3. | BLOB | https | 443 | REST を使用して BLOB ストレージに接続する |


## <a name="connecting-to-azure-resource-manager-workflow"></a>Azure Resource Manager ワークフローへの接続

Azure Resource Manager を使用してデバイスのローカル API に接続するプロセスでは、次の手順を行う必要があります。

| 手順番号 | 実行する手順 | .. 実行する場所 |
| --- | --- | --- |
| 1. | [Azure Stack Edge Pro デバイスを構成する](#step-1-configure-azure-stack-edge-pro-device) | ローカル Web UI |
| 2. | [証明書を作成してインストールする](#step-2-create-and-install-certificates) | Windows クライアント/ローカル Web UI |
| 3. | [前提条件を確認して構成する](#step-3-install-powershell-on-the-client) | Windows クライアント |
| 4. | [クライアントで Azure PowerShell を設定する](#step-4-set-up-azure-powershell-on-the-client) | Windows クライアント |
| 5. | [エンドポイント名の解決のためにホスト ファイルを変更する](#step-5-modify-host-file-for-endpoint-name-resolution) | Windows クライアントまたは DNS サーバー |
| 6. | [エンドポイント名が解決されていることを確認する](#step-6-verify-endpoint-name-resolution-on-the-client) | Windows クライアント |
| 7. | [Azure PowerShell のコマンドレットを使用して、Azure Resource Manager への接続を確認する](#step-7-set-azure-resource-manager-environment) | Windows クライアント |

以降のセクションでは、Azure Resource Manager への接続での上記の各手順について詳しく説明します。

## <a name="prerequisites"></a>前提条件

開始する前に、Azure Resource Manager 経由でデバイスに接続するために使用されるクライアントで TLS 1.2 が使用されていることを確認してください。 詳細については、「[Azure Stack Edge Pro デバイスにアクセスする Windows クライアントで TLS 1.2 を構成する](azure-stack-edge-gpu-configure-tls-settings.md)」をご覧ください。

## <a name="step-1-configure-azure-stack-edge-pro-device"></a>手順 1:Azure Stack Edge Pro デバイスを構成する 

Azure Stack Edge Pro デバイスのローカル Web UI で、次の手順を行います。

1. Azure Stack Edge Pro デバイスのネットワーク設定を完了します。 

    ![ローカル Web UI の [ネットワーク設定] ページ](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png)


    デバイスの IP アドレスをメモしておきます。 この IP は後で使用します。

2. **[デバイス]** ページでデバイス名と DNS ドメインを構成します。 後で使用するため、デバイス名と DNS ドメインをメモしておきます。

    ![ローカル Web UI の [デバイス] ページ](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-2.png)

    > [!IMPORTANT]
    > デバイス名、DNS ドメインは、公開されるエンドポイントを形成するために使用されます。
    > ローカル Web UI の **[デバイス]** ページの Azure Resource Manager と BLOB エンドポイントを使用します。


## <a name="step-2-create-and-install-certificates"></a>手順 2:証明書を作成してインストールする

証明書により、通信が信頼されていることが保証されます。 Azure Stack Edge Pro デバイスによって、自己署名アプライアンス、BLOB、Azure Resource Manager の証明書が自動的に生成されます。 必要に応じて、独自の署名済み BLOB と Azure Resource Manager の証明書を取り込むこともできます。

独自の署名入り証明書を取り込む場合は、証明書の対応する署名チェーンも必要です。 署名チェーン、Azure Resource Manager、およびデバイスの BLOB 証明書については、デバイスを認証して通信するために、クライアント マシンにも対応する証明書が必要になります。

Azure Resource Manager に接続するには、署名チェーンとエンドポイント証明書を作成または取得し、これらの証明書を Windows クライアントにインポートして、最後にこれらの証明書をデバイスにアップロードする必要があります。

### <a name="create-certificates-optional"></a>証明書を作成する (省略可能)

テストと開発に使用する場合にのみ、Windows PowerShell を使用して、ローカル システムで証明書を作成できます。 クライアントの証明書を作成するときは、次のガイドラインに従ってください。

1. 最初に、署名チェーンのルート証明書を作成する必要があります。 詳細については、「[署名チェーン証明書を作成する](azure-stack-edge-gpu-manage-certificates.md#create-signing-chain-certificate)」の手順を参照してください。

2. 次に、BLOB と Azure Resource Manager のエンドポイント証明書を作成できます。 これらのエンドポイントは、ローカル Web UI の **[デバイス]** ページから取得できます。 [エンドポイント証明書の作成](azure-stack-edge-gpu-manage-certificates.md#create-signed-endpoint-certificates)手順を参照してください。

3. これらのすべての証明書について、サブジェクト名とサブジェクトの別名が、次のガイドラインに準拠していることを確認します。

    |Type |サブジェクト名 (SN)  |サブジェクトの別名 (SAN)  |サブジェクト名の例 |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |BLOB ストレージ|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |両方のエンドポイントに対するマルチ SAN の単一の証明書|`<Device name>.<dnsdomain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |

証明書の詳細については、[証明書の管理](azure-stack-edge-gpu-manage-certificates.md)方法に関するページを参照してください。

### <a name="upload-certificates-on-the-device"></a>デバイスに証明書をアップロードする

前の手順で作成した証明書は、クライアントの個人用ストアに保存されます。 これらの証明書は、デバイスにアップロードできるように、クライアントで適切なフォーマット ファイルにエクスポートする必要があります。

1. ルート証明書は、 *.cer* ファイル拡張子を持つ DER フォーマット ファイルとしてエクスポートする必要があります。 詳細な手順については、[証明書を .cer フォーマット ファイルとしてエクスポート](azure-stack-edge-gpu-manage-certificates.md#export-certificates-as-der-format)に関するセクションを参照してください。

2. エンドポイント証明書は、秘密キーを使用して *.pfx* ファイルとしてエクスポートする必要があります。 詳細な手順については、[秘密キーを使用して証明書を .pfx ファイルとしてエクスポート](azure-stack-edge-gpu-manage-certificates.md#export-certificates-as-pfx-format-with-private-key)に関するセクションを参照してください。

3. 次に、ローカル Web UI の **[証明書]** ページの **[+ 証明書の追加]** オプションを使用して、ルート証明書とエンドポイント証明書をデバイスにアップロードします。 証明書をアップロードするには、「[証明書のアップロード](azure-stack-edge-gpu-manage-certificates.md#upload-certificates)」の手順に従います。


### <a name="import-certificates-on-the-client-running-azure-powershell"></a>Azure PowerShell を実行しているクライアントで証明書をインポートする

Azure Resource Manager API を呼び出す Windows クライアントで、デバイスとの信頼関係を確立する必要があります。 このためには、前の手順で作成した証明書を、Windows クライアントの適切な証明書ストアにインポートする必要があります。

1. *.cer* 拡張子を持つ DER 形式としてエクスポートしたルート証明書は、クライアント システムの信頼されたルート証明機関にインポートされるはずです。 詳細な手順については、[信頼されたルート証明機関ストアへの証明書のインポート](azure-stack-edge-gpu-manage-certificates.md#import-certificates-as-der-format)に関するページを参照してください。

2. *.pfx* としてエクスポートしたエンドポイント証明書は、 *.cer* としてエクスポートする必要があります。 この *.cer* は、お使いのシステム上の **個人用** 証明書ストアにインポートされます。 詳細な手順については、[個人用ストアへの証明書のインポート](azure-stack-edge-gpu-manage-certificates.md#import-certificates-as-der-format)に関するページを参照してください。

## <a name="step-3-install-powershell-on-the-client"></a>手順 3:クライアントに PowerShell をインストールする 

Windows クライアントが、次の前提条件を満たしている必要があります。

1. PowerShell バージョン 5.0 を実行している。 PowerShell バージョン 5.0 が必要です。 PowerShell Core はサポートされていません。 お使いのシステムの PowerShell のバージョンを確認するには、次のコマンドレットを実行します。

    ```powershell
    $PSVersionTable.PSVersion
    ```

    **メジャー** バージョンを比較し、それが確実に 5.0 以降であるようにします。

    バージョンが古い場合は、「[既存の Windows PowerShell をアップグレードする](/powershell/scripting/install/installing-windows-powershell?view=powershell-6&preserve-view=true#upgrading-existing-windows-powershell)」をご覧ください。

    PowerShell 5.0 をお持ちでない場合は、「[Windows PowerShell のインストール](/powershell/scripting/install/installing-windows-powershell?view=powershell-6&preserve-view=true)」に従います。

    サンプル出力を次に示します。

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved. 
    Try the new cross-platform PowerShell https://aka.ms/pscore6
    PS C:\windows\system32> $PSVersionTable.PSVersion
    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      18362  145
    ```
    
2. PowerShell ギャラリーにアクセスできる。

    PowerShell を管理者として実行します。 `PSGallery` がリポジトリとして登録されているかどうかを確認します。

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop
    Get-PSRepository -Name "PSGallery"
    ```
    
    サンプル出力を次に示します。
    
    ```powershell
    PS C:\windows\system32> Import-Module -Name PowerShellGet -ErrorAction Stop
    PS C:\windows\system32> Import-Module -Name PackageManagement -ErrorAction Stop
    PS C:\windows\system32> Get-PSRepository -Name "PSGallery"
    Name                      InstallationPolicy   SourceLocation
    ----                      ------------------   --------------
    PSGallery                 Trusted              https://www.powershellgallery.com/api/v2
    ```
    
リポジトリが信頼されていない場合、または詳細情報が必要な場合は、「[PowerShell ギャラリーにアクセスできるか検証する](/azure-stack/operator/azure-stack-powershell-install?view=azs-1908&preserve-view=true&preserve-view=true#2-validate-the-powershell-gallery-accessibility)」を参照してください。

## <a name="step-4-set-up-azure-powershell-on-the-client"></a>手順 4:クライアントで Azure PowerShell を設定する 

<!--1. Verify the API profile of the client and identify which version of the Azure PowerShell modules and libraries to include on your client. In this example, the client system will be running Azure Stack 1904 or later. For more information, see [Azure Resource Manager API profiles](/azure-stack/user/azure-stack-version-profiles?view=azs-1908#azure-resource-manager-api-profiles).-->

1. デバイスで動作する Azure PowerShell モジュールをクライアントにインストールします。

    a. PowerShell を管理者として実行します。 PowerShell ギャラリーへのアクセスが必要になります。 


    b. PowerShell ギャラリーから必要な Azure PowerShell モジュールをインストールするには、次のコマンドを実行します。

    ```powershell
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet.
    
    Install-Module -Name AzureRM.BootStrapper
    
   # Install and import the API Version Profile into the current PowerShell session.
    
    Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    
    # Confirm the installation of PowerShell
    Get-Module -Name "Azure*" -ListAvailable
    ```
    
    インストールの最後に、AzureRM モジュール バージョン 2.5.0 が実行されていることを確認します。 
    必要なバージョンと一致しない既存のバージョンの AzureRM モジュールがある場合は、次のコマンドを使用してアンインストールします。

    `Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose`

    ここで、必要なバージョンを再インストールする必要があります。
   

AzureRM バージョン 2.5.0 モジュールが正常にインストールされたことを示すサンプル出力を以下に示します。

```powershell
PS C:\windows\system32> Install-Module -Name AzureRM.BootStrapper
PS C:\windows\system32> Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
Loading Profile 2019-03-01-hybrid
PS C:\windows\system32> Get-Module -Name "Azure*" -ListAvailable
 
    Directory: C:\Program Files\WindowsPowerShell\Modules
 
ModuleType Version    Name                          ExportedCommands
---------- -------    ----                          ----------------
Script     4.5.0      Azure.Storage                       {Get-AzureStorageTable, New-AzureStorageTableSASToken, New...
Script     2.5.0      AzureRM
Script     0.5.0      AzureRM.BootStrapper                {Update-AzureRmProfile, Uninstall-AzureRmProfile, Install-...
Script     4.6.1      AzureRM.Compute                     {Remove-AzureRmAvailabilitySet, Get-AzureRmAvailabilitySet...
Script     3.5.1      AzureRM.Dns                         {Get-AzureRmDnsRecordSet, New-AzureRmDnsRecordConfig, Remo...
Script     5.1.5      AzureRM.Insights                    {Get-AzureRmMetricDefinition, Get-AzureRmMetric, Remove-Az...
Script     4.2.0      AzureRM.KeyVault                    {Add-AzureKeyVaultCertificate, Set-AzureKeyVaultCertificat...
Script     5.0.1      AzureRM.Network                     {Add-AzureRmApplicationGatewayAuthenticationCertificate, G...
Script     5.8.3      AzureRM.profile                     {Disable-AzureRmDataCollection, Disable-AzureRmContextAuto...
Script     6.4.3      AzureRM.Resources                   {Get-AzureRmProviderOperation, Remove-AzureRmRoleAssignmen...
Script     5.0.4      AzureRM.Storage                     {Get-AzureRmStorageAccount, Get-AzureRmStorageAccountKey, ...
Script     4.0.2      AzureRM.Tags                        {Remove-AzureRmTag, Get-AzureRmTag, New-AzureRmTag}
Script     4.0.3      AzureRM.UsageAggregates             Get-UsageAggregates
Script     5.0.1      AzureRM.Websites                    {Get-AzureRmAppServicePlan, Set-AzureRmAppServicePlan, New...

 
    Directory: C:\Program Files (x86)\Microsoft Azure Information Protection\Powershell
 
ModuleType Version    Name                            ExportedCommands
---------- -------    ----                           ----------------
Binary     1.48.204.0 AzureInformationProtection          {Clear-RMSAuthentication, Get-RMSFileStatus, Get-RMSServer...
```


## <a name="step-5-modify-host-file-for-endpoint-name-resolution"></a>手順 5:エンドポイント名の解決のためにホスト ファイルを変更する 

次に、デバイスのローカル Web UI で定義した Azure の整合 VIP を次に追加します。

- クライアント上のホスト ファイル、または
- DNS サーバーの構成

> [!IMPORTANT]
> エンドポイントの名前解決には、DNS サーバーの構成を変更することをお勧めします。

デバイスへの接続に使用している Windows クライアントで、次の手順を行います。

1. 管理者として **メモ帳** を起動し、C:\Windows\System32\Drivers\etc にある **hosts** ファイルを開きます。

    ![エクスプローラーの hosts ファイル](media/azure-stack-edge-gpu-connect-resource-manager/hosts-file.png)

2. **hosts** ファイルに次のエントリを追加します。値は、実際のデバイスに合わせて置き換えてください。 

    ```
    <Device IP> login.<appliance name>.<DNS domain>
    <Device IP> management.<appliance name>.<DNS domain>
    <Device IP> <storage name>.blob.<appliance name>.<DNS domain>
    ```

    > [!IMPORTANT]
    > hosts ファイル内のエントリは、後の手順で Azure Resource Manager に接続するために提供されるものと完全に一致している必要があります。 ここの DNS ドメイン エントリがすべて小文字になっていることを確認してください。

    前の手順でローカル Web UI からデバイス IP を保存しました。

    login.\<appliance name\>.\<DNS domain\> エントリは、セキュリティ トークン サービス (STS) のエンドポイントです。 STS は、セキュリティ トークンの作成、検証、更新、および取り消しを担当します。 セキュリティ トークン サービスは、デバイスとクライアントの間の継続的な通信に使用されるアクセス トークンと更新トークンを作成するために使用されます。

3. 次の図を参考にしてください。 **hosts** ファイルを保存します。

    ![メモ帳で開いた hosts ファイル](media/azure-stack-edge-gpu-connect-resource-manager/hosts-file-notepad.png)

## <a name="step-6-verify-endpoint-name-resolution-on-the-client"></a>手順 6:クライアントでエンドポイントの名前解決を確認する

Azure の整合 VIP に接続するために使用しているクライアントで、エンドポイント名が解決されているかどうかを確認します。

1. ping.exe コマンドライン ユーティリティを使用して、エンドポイント名が解決されていることを確認できます。 IP アドレスを指定すると、ping コマンドによって、トレースしているコンピューターの TCP/IP ホスト名が返されます。

    次の例に示すように、`-a` スイッチをコマンド ラインに追加します。 ホスト名を返すことができる場合は、この潜在的に貴重な情報も応答で返される可能性があります。

    ![コマンドプロンプトでの ping](media/azure-stack-edge-gpu-connect-resource-manager/ping-command-prompt.png)



## <a name="step-7-set-azure-resource-manager-environment"></a>手順 7:Azure Resource Manager 環境を設定する

Azure Resource Manager 環境を設定し、Azure Resource Manager を使用したデバイスとクライアントの通信が正常に機能していることを確認します。 この検証を行うには、次の手順を行います。


1. `Add-AzureRmEnvironment` コマンドレットを使用して、Azure Resource Manager 経由の通信が正常に機能していること、API 呼び出しが Azure Resource Manager 専用のポート 443 を通過していることをより確実にします。

    `Add-AzureRmEnvironment` コマンドレットにより、エンドポイントとメタデータが追加され、Azure Resource Manager のコマンドレットが Azure Resource Manager の新しいインスタンスに接続できるようになります。 


    > [!IMPORTANT]
    > 次のコマンドレットで指定する Azure Resource Manager エンドポイント URL では、大文字と小文字が区別されます。 エンドポイント URL がすべて小文字になっていて、hosts ファイルで指定したものと一致していることを確認してください。 大文字と小文字が一致しない場合は、エラーが表示されます。

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>/"
    ```

    サンプル出力を次に示します。
    
    ```powershell
    PS C:\windows\system32> Add-AzureRmEnvironment -Name AzDBE -ARMEndpoint https://management.dbe-n6hugc2ra.microsoftdatabox.com/
    
    Name  Resource Manager Url                    ActiveDirectory Authority
    ----  --------------------                   -------------------------
    AzDBE https://management.dbe-n6hugc2ra.microsoftdatabox.com https://login.dbe-n6hugc2ra.microsoftdatabox.com/adfs/
    ```

2. 環境を Azure Stack Edge Pro として設定し、Azure Resource Manager の呼び出しに使用するポートを 443 として設定します。 環境を定義するには、次の 2 つの方法があります。

    - 環境を設定します。 次のコマンドを入力します。

    ```powershell
    Set-AzureRMEnvironment -Name <Environment Name>
    ```
    
    詳細については 「[Set-AzureRMEnvironment](/powershell/module/azurerm.profile/set-azurermenvironment?view=azurermps-6.13.0&preserve-view=true)」を参照してください。

    - 実行するすべてのコマンドレットに対して、環境をインラインで定義します。 これにより、すべての API 呼び出しが確実に正しい環境を経由するようになります。 既定では、呼び出しは Azure パブリックを経由しますが、Azure Stack Edge Pro デバイス用に設定した環境を経由する必要があります。

    - AzureRM 環境を切り替える方法ついては、[こちら](#switch-environments)を参照してください。

2. ローカル デバイス API を呼び出して、Azure Resource Manager への接続を認証します。 

    1. これらの資格情報は、ローカル コンピューター アカウント用であり、API アクセスにのみ使用されます。

    2. `login-AzureRMAccount` または `Connect-AzureRMAccount` コマンドを使用して接続できます。 

        1. サインインするには、次のコマンドを入力します。 このインスタンスのテナント ID はハードコーディングされています - c0257de7-538f-415c-993a-1b87a031879d。 次のユーザー名とパスワードを使用します。

            - **ユーザー名** - *EdgeArmUser*

            - **パスワード** - [Azure Resource Manager にパスワードを設定](azure-stack-edge-gpu-set-azure-resource-manager-password.md)し、このパスワードを使用してサインインします。 

            ```powershell
            PS C:\windows\system32> $pass = ConvertTo-SecureString "<Your password>" -AsPlainText -Force;
            PS C:\windows\system32> $cred = New-Object System.Management.Automation.PSCredential("EdgeArmUser", $pass)
            PS C:\windows\system32> Connect-AzureRmAccount -EnvironmentName AzDBE -TenantId c0257de7-538f-415c-993a-1b87a031879d -credential $cred
            
            Account       SubscriptionName   TenantId            Environment
            -------       ----------------   --------            -----------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
            
            PS C:\windows\system32>
            ```
                   
        
            ログインする別の方法は、`login-AzureRmAccount` コマンドレットを使用することです。 
            
            `login-AzureRMAccount -EnvironmentName <Environment Name>` -TenantId c0257de7-538f-415c-993a-1b87a031879d 

            コマンドの出力例を次に示します。 
         
            ```powershell
            PS C:\Users\Administrator> login-AzureRMAccount -EnvironmentName AzDBE -TenantId c0257de7-538f-415c-993a-1b87a031879d
            
            Account         SubscriptionName  TenantId              Environment
            -------         ----------------  --------              -------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
            PS C:\Users\Administrator>
            ```



> [!IMPORTANT]
> Azure Stack Edge Pro デバイスが再起動されると、Azure Resource Manager への接続は 1.5 時間ごとに有効期限が切れます。 この場合、実行するすべてのコマンドレットで、Azure に接続されていないことを示すエラー メッセージが返されます。 再度サインインする必要があります。

## <a name="switch-environments"></a>環境を切り替える

`Disconnect-AzureRmAccount` コマンドを実行して、別の `AzureRmEnvironment` に切り替えます。 

`Disconnect-AzureRmAccount` を使用せずに `Set-AzureRmEnvironment` と `Login-AzureRmAccount` を使用する場合、環境は実際には切り替えられません。  

次の例は、`AzDBE1` と `AzDBE2` の 2 つの環境を切り替える方法を示しています。

まず、クライアント上の既存のすべての環境を一覧表示します。


```azurepowershell
PS C:\WINDOWS\system32> Get-AzureRmEnvironment
Name    Resource Manager Url     ActiveDirectory Authority
----    --------------------      -------------------------
AzureChinaCloud   https://management.chinacloudapi.cn/                 https://login.chinacloudapi.cn/
AzureCloud        https://management.azure.com/                        https://login.microsoftonline.com/
AzureGermanCloud  https://management.microsoftazure.de/                https://login.microsoftonline.de/
AzDBE1            https://management.HVTG1T2-Test.microsoftdatabox.com https://login.hvtg1t2-test.microsoftdatabox.com/adfs/
AzureUSGovernment https://management.usgovcloudapi.net/                https://login.microsoftonline.us/
AzDBE2            https://management.CVV4PX2-Test.microsoftdatabox.com https://login.cvv4px2-test.microsoftdatabox.com/adfs/
```

次に、Azure Resource Manager 経由で現在接続している環境を取得します。

```azurepowershell
PS C:\WINDOWS\system32> Get-AzureRmContext |fl *

Name               : Default Provider Subscription (A4257FDE-B946-4E01-ADE7-674760B8D1A3) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE2
Subscription       : a4257fde-b946-4e01-ade7-674760b8d1a3
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```

現在の環境から切断してから、他の環境に切り替える必要があります。


```azurepowershell
PS C:\WINDOWS\system32> Disconnect-AzureRmAccount

Id                    : EdgeArmUser@localhost
Type                  : User
Tenants               : {c0257de7-538f-415c-993a-1b87a031879d}
AccessToken           :
Credential            :
TenantMap             : {}
CertificateThumbprint :
ExtendedProperties    : {[Subscriptions, A4257FDE-B946-4E01-ADE7-674760B8D1A3], [Tenants, c0257de7-538f-415c-993a-1b87a031879d]}
```

他の環境にログインします。 サンプル出力を次に示します。

```azurepowershell
PS C:\WINDOWS\system32> Login-AzureRmAccount -Environment "AzDBE1" -TenantId $ArmTenantId

Account     SubscriptionName   TenantId        Environment
-------     ----------------   --------        -----------
EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE1
```

このコマンドレットを実行して、接続している環境を確認します。

```azurepowershell
PS C:\WINDOWS\system32> Get-AzureRmContext |fl *

Name               : Default Provider Subscription (A4257FDE-B946-4E01-ADE7-674760B8D1A3) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE1
Subscription       : a4257fde-b946-4e01-ade7-674760b8d1a3
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```
これで、目的の環境に切り替えられました。

## <a name="next-steps"></a>次のステップ

[Azure Stack Edge Pro デバイスに VM をデプロイする](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)。
