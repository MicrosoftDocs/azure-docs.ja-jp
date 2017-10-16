---
title: "Azure Stack への接続 | Microsoft Docs"
description: "Azure Stack に接続する方法について説明します"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: sngun
ms.openlocfilehash: 09c626e97832821009ce2da360ceea2b54273ffa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-azure-stack"></a>Azure Stack への接続

*適用対象: Azure Stack 開発キット*

リソースを管理するには、Azure Stack Development Kit に接続する必要があります。 ここでは、Development Kit に接続するために必要な手順について詳しく説明します。 次の接続方法のうちどちらかを使用できます。

* [リモート デスクトップ](#connect-with-remote-desktop): Development Kit から、1 人の同時実行ユーザーのみがすばやく接続できます。
* [仮想プライベート ネットワーク (VPN)](#connect-with-vpn): Azure Stack インフラストラクチャ以外のクライアントから、複数の同時実行ユーザーが接続できます (構成が必要です)。

## <a name="connect-to-azure-stack-with-remote-desktop"></a>リモート デスクトップを使用して Azure Stack に接続する
リモート デスクトップ接続では、1 人の同時実行ユーザーがポータルを使用してリソースを管理できます。

1. リモート デスクトップ接続を開き、Development Kit に接続します。 「**AzureStack\AzureStackAdmin**」というユーザー名と、Azure Stack セットアップ時に指定したオペレーターのパスワードを入力します。  

2. Development Kit コンピューターからサーバー マネージャーを開き、**[ローカル サーバー]** をクリックし、Internet Explorer セキュリティ強化を無効にして、サーバー マネージャーを閉じます。

3. ユーザーの[ポータル](azure-stack-key-features.md#portal)を開くには、(https://portal.local.azurestack.external/) に移動し、ユーザーの資格情報を使用してサインインします。 Azure Stack オペレーターの[ポータル](azure-stack-key-features.md#portal)を開くには、(https://adminportal.local.azurestack.external/) に移動し、インストール時に指定した Azure Active Directory の資格情報を使用してサインインします。

## <a name="connect-to-azure-stack-with-vpn"></a>VPN を使用して Azure Stack に接続する

Azure Stack Development Kit に対して、分割トンネルの仮想プライベート ネットワーク (VPN) 接続を確立することができます。 VPN 接続を介して、Azure Stack オペレーターのポータル、ユーザー ポータル、およびローカルにインストールされているツール (Visual Studio、PowerShell など) にアクセスして、Azure Stack リソースを管理できます。 VPN 接続は、Azure Active Directory (AAD) および Active Directory フェデレーション サービス (AD FS) ベースのデプロイの両方でサポートされています。 VPN 接続は、同時に複数のクライアントが Azure Stack に接続できます。 

> [!NOTE] 
> この VPN 接続では、Azure Stack インフラストラクチャ VM には接続できません。 

### <a name="prerequisites"></a>前提条件

* [Azure Stack 互換の Azure PowerShell](azure-stack-powershell-install.md) をローカル コンピューターにインストールします。  
* [Azure Stack を操作するために必要なツール](azure-stack-powershell-download.md)をダウンロードします。 

### <a name="configure-vpn-connectivity"></a>VPN 接続の構成

Development Kit に対する VPN 接続を作成するには、ローカル Windows ベースのコンピューターから管理者特権の PowerShell セッションを開き、次のスクリプトを実行します (実際の環境に合わせて IP アドレスとパスワードは変更してください)。

```PowerShell 
# Configure winrm if it's not already configured
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module
Import-Module .\Connect\AzureStack.Connect.psm1 

# Add the development kit computer’s host IP address & certificate authority (CA) to the list of trusted hosts. Make sure to update the the IP address and password values for your environment. 

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<operator's password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

セットアップが成功すると、VPN 接続の一覧に **azurestack** と表示されます。

![ネットワーク接続](media/azure-stack-connect-azure-stack/image3.png)  

### <a name="connect-to-azure-stack"></a>Azure Stack への接続

次の 2 つの方法のいずれかを使用して、Azure Stack インスタンスに接続します。  

* `Connect-AzsVpn ` コマンドを使用する: 
    
  ```PowerShell
  Connect-AzsVpn `
    -Password $Password
  ```

  プロンプトに従って Azure Stack ホストを信頼し、**AzureStackCertificateAuthority** の証明書をローカル コンピューターの証明書ストアにインストールします (プロンプトは、PowerShell セッション ウィンドウの背後に表示される可能性があります)。 

* ローカル コンピューターの **[ネットワーク設定]** > **[VPN]** を開き、**[azurestack]** > **[接続]** をクリックします。 サインイン プロンプトで、ユーザー名 (AzureStack\AzureStackAdmin) とパスワードを入力します。

### <a name="test-the-vpn-connectivity"></a>VPN 接続のテスト

ポータル接続をテストするには、インターネット ブラウザーを開き、ユーザー ポータル (https://portal.local.azurestack.external/) またはオペレーター ポータル (https://adminportal.local.azurestack.external/) にアクセスし、サインインしてリソースを作成します。  

## <a name="next-steps"></a>次のステップ

[Azure Stack ユーザーが仮想マシンを使用できるようにする](azure-stack-tutorial-tenant-vm.md)

