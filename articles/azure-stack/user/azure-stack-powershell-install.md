---
title: "PowerShell for Azure Stack のインストール | Microsoft Docs"
description: "PowerShell for Azure Stack をインストールする方法について説明します。"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2017
ms.author: sngun
ms.openlocfilehash: a3b25dd6d7b9ba7a3040dee3196ec25463fe85ef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="install-powershell-for-azure-stack"></a>PowerShell for Azure Stack のインストール  

Azure Stack を使用するには、Azure Stack と互換性のある Azure PowerShell モジュールが必要です。 このガイドでは、PowerShell for Azure Stack のインストールに必要な手順について説明します。 この記事で説明している手順は、Azure Stack Development Kit で、または VPN 経由で接続している場合は Windows ベースの外部クライアントで使用できます。

この記事では、PowerShell for Azure Stack をインストールする手順について詳しく説明しています。 ただし、PowerShell をすばやくインストールして構成する場合は、「Get up and running with PowerShell」(PowerShell の起動と実行) のトピックにあるスクリプトを使用できます。 

> [!NOTE]
> 次の手順では、PowerShell 5.0 が必要です。 バージョンを確認するには、$PSVersionTable.PSVersion を実行して、[Major] (メジャー) のバージョンを比較します。

Azure Stack 用の PowerShell コマンドは、PowerShell ギャラリーを介してインストールされます。 PSGallery レポジトリを登録するには、開発キットから、または VPN 経由で接続している場合は Windows ベースの外部クライアントから、管理者特権の PowerShell セッションを開いて、次のコマンドを実行します。

```powershell
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted
```

## <a name="uninstall-existing-versions-of-powershell"></a>既存のバージョンの PowerShell のアンインストール

必要なバージョンをインストールする前に、必ず既存の Azure PowerShell モジュールをアンインストールしてください。 アンインストールには、次の 2 つの方法の 1 つを使用できます。

* 既存の PowerShell モジュールをアンインストールするには、開発キットにサインインするか、または VPN 接続を確立する予定の場合は Windows ベースの外部クライアントにサインインします。 アクティブな PowerShell セッションをすべて終了し、次のコマンドを実行します。 

   ```powershell
   Get-Module -ListAvailable | where-Object {$_.Name -like “Azure*”} | Uninstall-Module
   ```

* 開発キットにサインインするか、または VPN 接続を確立する予定の場合は Windows ベースの外部クライアントにサインインします。 `C:\Program Files (x86)\WindowsPowerShell\Modules` および `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules` フォルダーから、「Azure」で始まるすべてのフォルダーを削除します。 これらのフォルダーを削除すると、「AzureStackAdmin」および「global」ユーザー スコープから既存の PowerShell モジュールがすべて削除されます。 

以下のセクションでは、PowerShell for Azure Stack のインストールに必要な手順について説明します。 PowerShell は、接続されているシナリオ、部分的に接続されているシナリオ、または接続が切断されたシナリオで運用される Azure Stack にインストールできます。 

## <a name="install-powershell-in-a-connected-scenario"></a>接続されているシナリオでの PowerShell のインストール 

Azure Stack と互換性のある AzureRM モジュールは、API バージョン プロファイルを使用してインストールされます。 Azure Stack には **2017-03-09-profile** API バージョン プロファイルが必要です。このプロファイルは、AzureRM.Bootstrapper モジュールをインストールすることで利用できます。 API バージョン プロファイル、およびそれらのプロファイルによって提供されるコマンドレットについては、 「[manage API version profiles](azure-stack-version-profiles.md)」(API バージョン プロファイルの管理) を参照してください。 AzureRM モジュールだけでなく、Azure Stack 固有の PowerShell モジュールもインストールする必要があります。 次の PowerShell スクリプトを実行して、これらのモジュールを開発用ワークステーションにインストールします。

  ```powershell
  # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
  Install-Module `
    -Name AzureRm.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile `
    -Profile 2017-03-09-profile -Force

  Install-Module `
    -Name AzureStack `
    -RequiredVersion 1.2.10
  ```

インストールを確認するには、次のコマンドを実行します。

  ```powershell
  Get-Module `
    -ListAvailable | where-Object {$_.Name -like “Azure*”}
  ```
  インストールに成功した場合、出力に AzureRM および AzureStack モジュールが表示されます。

## <a name="install-powershell-in-a-disconnected-or-in-a-partially-connected-scenario"></a>接続が切断されたシナリオまたは部分的に接続されているシナリオでの PowerShell のインストール

接続が切断されたシナリオでは、まずインターネット接続が確立されたコンピューターに PowerShell モジュールをダウンロードしてから、モジュールを Azure Stack Development Kit に転送してインストールします。

1. インターネット接続が確立されたコンピューターにサインインし、次のスクリプトを使用して、AzureRM および AzureStack パッケージをローカル コンピューターにダウンロードします。

   ```powershell
   $Path = "<Path that is used to save the packages>"

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureRM `
     -Path $Path `
     -Force `
     -RequiredVersion 1.2.10

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureStack `
     -Path $Path `
     -Force `
     -RequiredVersion 1.2.10 
   ```

2. ダウンロードしたパッケージを USB デバイスにコピーします。

3. 開発キットにサインインし、パッケージを USB デバイスから開発キット上の場所にコピーします。 

4. ここで、その場所を既定のレポジトリとして登録し、そのレポジトリから AzureRM および AzureStack モジュールをインストールする必要があります。

   ```powershell
   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository `
     -Name $RepoName `
     -SourceLocation $SourceLocation `
     -InstallationPolicy Trusted

   ```powershell
   Install-Module AzureRM `
     -Repository $RepoName

   Install-Module AzureStack `
     -Repository $RepoName 
   ```

## <a name="next-steps"></a>次のステップ

* [GitHub からの Azure Stack ツールのダウンロード](azure-stack-powershell-download.md)
* [Azure Stack ユーザーの PowerShell 環境の構成](azure-stack-powershell-configure-user.md)  
* [Azure Stack での API バージョン プロファイルの管理](azure-stack-version-profiles.md)  
