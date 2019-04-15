---
title: PowerShell for Azure Stack のインストール | Microsoft Docs
description: PowerShell for Azure Stack をインストールする方法について説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 02/08/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 7e631281405b173405f28c134432e870c757b3da
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58648404"
---
# <a name="install-powershell-for-azure-stack"></a>PowerShell for Azure Stack をインストールする

*適用対象:Azure Stack 統合システムと Azure Stack Development Kit*

クラウドで動作させるには、Azure Stack と互換のある PowerShell モジュールをインストールする必要があります。 互換性は、*API プロファイル*と呼ばれる機能によって有効になります。

API のプロファイルは、Azure と Azure Stack の間のバージョンの違いを管理するための方法を提供します。 API バージョンのプロファイルは、特定の API バージョンを持つ一連の Azure Resource Manager PowerShell モジュールです。 各クラウド プラットフォームでは、一連の API バージョンのプロファイルがサポートされています。 たとえば、Azure Stack では **2018-03-01-hybrid** などの特定のプロファイル バージョンがサポートされます。 プロファイルをインストールすると、指定されたプロファイルに対応する Azure Resource Manager PowerShell モジュールがインストールされます。

Azure Stack と互換のある PowerShell モジュールのインストールは、インターネットに接続されたシナリオ、部分的に接続されたシナリオ、接続が切断されたシナリオのいずれでも可能です。 この記事では、各シナリオで PowerShell for Azure Stack をインストールするための詳しい手順を説明します。

## <a name="1-verify-your-prerequisites"></a>1.前提条件を確認する

Azure Stack と PowerShell を開始する前に、次の前提条件が必要です。

- **PowerShell Version 5.0** 使用しているバージョンを確認するには、**$PSVersionTable.PSVersion** を実行して、**メジャー** バージョンを比較します。 PowerShell 5.0 を使用していない場合は、[Installing Windows PowerShell](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell) (Windows PowerShell のインストール) に従ってください。

  > [!Note]
  > PowerShell 5.0 には、Windows コンピューターが必要です。

- **PowerShell の実行 (管理者特権でのコマンド プロンプト)**。
  PowerShell を管理特権で実行する必要があります。

- **PowerShell ギャラリーへのアクセス** [PowerShell ギャラリー](https://www.powershellgallery.com)にアクセスする必要があります。 ギャラリーとは、PowerShell コンテンツのための中央のリポジトリです。 **PowerShellGet** モジュールには、PowerShell ギャラリーやその他のプライベート リポジトリから PowerShell アーティファクト (モジュール、DSC リソース、ロール機能、スクリプトなど) を検出、インストール、更新、発行するためのコマンドレットが含まれています。 切断されたシナリオで PowerShell を使用している場合は、インターネットに接続しているマシンからリソースを取得し、切断されたマシンがアクセスできる場所に格納する必要があります。

## <a name="2-validate-the-powershell-gallery-accessibility"></a>2.PowerShell ギャラリーにアクセスできるか検証する

PSGallery がリポジトリとして登録されているかどうかを検証します。

> [!Note]  
> この手順では、インターネットへのアクセスが必要です。

管理者特権の PowerShell プロンプトを開き、次のコマンドレットを実行します。

```powershell
Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop
Get-PSRepository -Name "PSGallery"
```

PSGallery リポジトリが登録されていない場合は、管理者特権の PowerShell セッションを開き、次のコマンドを実行します。

```powershell
Register-PsRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

## <a name="3-uninstall-existing-versions-of-the-azure-stack-powershell-modules"></a>手順 3.既存のバージョンの Azure Stack PowerShell モジュールのアンインストール

必要なバージョンをインストールする前に、必ず以前にインストールした Azure Stack AzureRM PowerShell モジュールをアンインストールしてください。 アンインストールには、次の 2 つの方法の 1 つを使用できます。

1. 既存の AzureRM PowerShell モジュールをアンインストールするには、アクティブな PowerShell セッションをすべて終了し、次のコマンドレットを実行します。

    ```powershell
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
    Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose
    ```

    「モジュールが既に使用されています」などのエラーが発生した場合には、そのモジュールを使用している PowerShell セッションを終了してから、上のスクリプトをもう一度実行してください。

2. `C:\Program Files\WindowsPowerShell\Modules` フォルダーおよび `C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules` フォルダーから、`Azure` または `Azs.` で始まるすべてのフォルダーを削除します。 これらのフォルダーを削除すると、既存の PowerShell モジュールがすべて削除されます。

## <a name="4-connected-install-powershell-for-azure-stack-with-internet-connectivity"></a>4.接続済みの場合: インターネット接続を使用して PowerShell for Azure Stack をインストールする

Azure Stack では、Azure Stack バージョン 1808 以降の **2018-03-01-hybrid** API バージョン プロファイルが必要です。 このプロファイルは、**AzureRM.BootStrapper** モジュールをインストールすると使用できるようになります。 AzureRM モジュールだけでなく、Azure Stack 固有の PowerShell モジュールもインストールする必要があります。 必要な API バージョン プロファイルおよび Azure Stack PowerShell モジュールは、実行している Azure Stack のバージョンによって異なります。

インストールには 3 つの手順があります:

1. Azure Stack のバージョンに応じて Azure Stack PowerShell をインストールする
2. 追加のストレージ機能を有効にする
3. PowerShell のインストールを確認する

### <a name="install-azure-stack-powershell"></a>Azure Stack PowerShell のインストール

次の PowerShell スクリプトを実行して、これらのモジュールを開発用ワークステーションにインストールします。

- Azure Stack 1901 以降:

    ```powershell
    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.

    Install-Module AzureRM -RequiredVersion 2.4.0
    Install-Module -Name AzureStack -RequiredVersion 1.7.1
    ```

    > [!Note]  
    > Azure Stack モジュール バージョン 1.7.1 は破壊的変更を伴うリリースです。 Azure Stack 1.6.0 から移行するには、[移行ガイド](https://aka.ms/azspshmigration171)を参照してください。
    > AzureRm モジュール バージョン 2.4.0 には、コマンドレット Remove-AzureRmStorageAccount について破壊的変更が存在します。 このコマンドレットでは、確認なしでストレージ アカウントを削除する際に -Force パラメーターを指定する必要があります。

- Azure Stack 1811:

    ```powershell
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRM.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    Install-Module -Name AzureStack -RequiredVersion 1.6.0
    ```

- Azure Stack 1810 以前:

    ```powershell
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRM.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    Install-Module -Name AzureStack -RequiredVersion 1.5.0
    ```

> [!Note]  
> Azure PowerShell を **2017-03-09-profile** から **2018-03-01-hybrid** にアップグレードするには、[移行ガイド](https://github.com/azure/azure-powershell/blob/AzureRM/documentation/migration-guides/Stack/migration-guide.2.3.0.md)を参照してください。

### <a name="enable-additional-storage-features"></a>追加のストレージ機能を有効にする

「接続済みの場合」のセクションで触れた追加のストレージ機能を利用する場合は、次のパッケージもダウンロードしてインストールしてください。

```powershell
# Install the Azure.Storage module version 4.5.0
Install-Module -Name Azure.Storage -RequiredVersion 4.5.0 -Force -AllowClobber

# Install the AzureRm.Storage module version 5.0.4
Install-Module -Name AzureRM.Storage -RequiredVersion 5.0.4 -Force -AllowClobber

# Remove incompatible storage module installed by AzureRM.Storage
Uninstall-Module Azure.Storage -RequiredVersion 4.6.1 -Force

# Load the modules explicitly specifying the versions
Import-Module -Name Azure.Storage -RequiredVersion 4.5.0
Import-Module -Name AzureRM.Storage -RequiredVersion 5.0.4
```

### <a name="confirm-the-installation-of-powershell"></a>PowerShell のインストールを確認する

次のコマンドを実行してインストールを確認します。

```powershell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

インストールに成功した場合、出力に AzureRM および AzureStack モジュールが表示されます。

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5.切断状態の場合: インターネット接続なしで PowerShell をインストールする

接続が切断されたシナリオでは、まずインターネット接続が確立されたコンピューターに PowerShell モジュールをダウンロードしてから、モジュールを Azure Stack Development Kit に転送してインストールします。

インターネット接続が確立されたコンピューターにサインインし、Azure Stack のバージョンに応じて次のスクリプトを使用して、Azure Resource Manager および AzureStack パッケージをダウンロードします。

インストールには 4 つの手順があります:

1. 接続されているコンピューターに Azure Stack PowerShell をインストールする
2. 追加のストレージ機能を有効にする
3. 接続が切断されたワークステーションに PowerShell パッケージを転送する
4. PowerShell のインストールを確認する

### <a name="install-azure-stack-powershell"></a>Azure Stack PowerShell のインストール

- Azure Stack 1901 以降。

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.4.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.7.1
    ```

    > [!Note]  
    > Azure Stack モジュール バージョン 1.7.1 は破壊的変更です。 AzureStack 1.6.0 から移行するには、[移行ガイド](https://github.com/Azure/azure-powershell/tree/AzureRM/documentation/migration-guides/Stack)を参照してください。

  - Azure Stack 1811 以前。

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.6.0
    ```

  - Azure Stack 1809 以前: 

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
    ```

    > [!NOTE]
    > インターネット接続のないマシンでは、次のコマンドレットを実行して利用統計情報の収集を無効にすることをお勧めします。 利用統計情報の収集を無効にしないと、コマンドレットのパフォーマンスが低下することがあります。 これは、インターネット接続のないマシンのみ該当します。
    > ```powershell
    > Disable-AzureRmDataCollection
    > ```

### <a name="enable-additional-storage-features"></a>追加のストレージ機能を有効にする

「接続済みの場合」のセクションで触れた追加のストレージ機能を利用する場合は、次のパッケージもダウンロードしてインストールしてください。

```powershell
$Path = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Azure.Storage -Path $Path -Force -RequiredVersion 4.5.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRm.Storage -Path $Path -Force -RequiredVersion 5.0.4
```

### <a name="add-your-packages-to-your-workstation"></a>パッケージをワークステーションに追加する

1. ダウンロードしたパッケージを USB デバイスにコピーします。

2. 接続が切断されたワークステーションにサインインし、パッケージを USB デバイスからワークステーション上の場所にコピーします。

3. ここで、その場所を既定のレポジトリとして登録し、そのレポジトリから AzureRM および AzureStack モジュールをインストールします。

   ```powershell
   #requires -Version 5
   #requires -RunAsAdministrator
   #requires -Module PowerShellGet
   #requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation  -InstallationPolicy Trusted

   Install-Module -Name AzureRM -Repository $RepoName

   Install-Module -Name AzureStack -Repository $RepoName
   ```

### <a name="confirm-the-installation-of-powershell"></a>PowerShell のインストールを確認する

次のコマンドを実行してインストールを確認します。

```powershell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

## <a name="6-configure-powershell-to-use-a-proxy-server"></a>6.プロキシ サーバーを使用するように PowerShell を構成する

インターネットへのアクセスにプロキシ サーバーを必要とするシナリオでは、最初に既存のプロキシ サーバーを使用するように PowerShell を構成する必要があります。

1. 管理者特権の PowerShell プロンプトを開きます。
2. 次のコマンドを実行します。

   ```powershell
   #To use Windows credentials for proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

   #Alternatively, to prompt for separate credentials that can be used for #proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
   ```

## <a name="next-steps"></a>次の手順

- [GitHub からの Azure Stack ツールのダウンロード](azure-stack-powershell-download.md)
- [Azure Stack ユーザーの PowerShell 環境の構成](user/azure-stack-powershell-configure-user.md)
- [Azure Stack オペレーターの PowerShell 環境の構成](azure-stack-powershell-configure-admin.md)
- [Azure Stack での API バージョン プロファイルの管理](user/azure-stack-version-profiles.md)
