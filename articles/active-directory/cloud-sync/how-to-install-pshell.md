---
title: PowerShell を使用した Azure AD Connect クラウド プロビジョニング エージェントのインストール
description: PowerShell コマンドレットを使用して Azure AD Connect クラウド プロビジョニング エージェントをインストールする方法について説明します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c20cfb96b5cd6e1d05e332fa7157fe6e0cde8656
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98612770"
---
# <a name="install-the-azure-ad-connect-provisioning-agent-using-powershell-cmdlets"></a>PowerShell コマンドレットを使用した Azure AD Connect プロビジョニング エージェントのインストール 
次のドキュメントでは、PowerShell コマンドレットを使用して Azure AD Connect プロビジョニング エージェントをインストールする方法について説明します。
 

## <a name="prerequisite"></a>前提条件: 


>[!IMPORTANT]
>次のインストール手順は、[前提条件](how-to-prerequisites.md)がすべて満たされていることを前提としています。
>
> Powershell コマンドレットを使用して Azure AD Connect プロビジョニング エージェントをインストールするには、Windows Server で TLS 1.2 を有効にする必要があります。 TLS 1.2 を有効にするには、[こちら](how-to-prerequisites.md#tls-requirements)の手順を使用します。

 

## <a name="install-the-azure-ad-connect-provisioning-agent-using-powershell-cmdlets"></a>PowerShell コマンドレットを使用した Azure AD Connect プロビジョニング エージェントのインストール 


 1. Azure portal にサインインし、 **[Azure Active Directory]** に移動します。
 2. 左側のメニューで、 **[Azure AD Connect]** を選択します。
 3. **[プロビジョニングの管理 (プレビュー)]**  >  **[すべてのエージェントの確認]** を選択します。
 4. Azure portal から Azure AD Connect プロビジョニング エージェントをローカルにダウンロードします。  

   ![オンプレミスのエージェントをダウンロードします](media/how-to-install/install-9.png)</br>
 5. この手順では、エージェントは  “C:\ProvisioningSetup” フォルダーにダウンロードされました。 
 6. プロビジョニング エージェントを quiet モードでインストールします

   ```
   $installerProcess = Start-Process c:\temp\AADConnectProvisioningAgent.Installer.exe /quiet -NoNewWindow -PassThru 
   $installerProcess.WaitForExit()  
   ```
 7. プロビジョニング エージェント PS モジュールをインポートします 

   ```
   Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Microsoft.CloudSync.Powershell.dll" 
   ```
 8. グローバル管理者の資格情報を使用して AzureAD に接続します。このセクションをカスタマイズして、セキュリティで保護されたストアからパスワードを取得することができます。 

   ```
   $globalAdminPassword = ConvertTo-SecureString -String "Global admin password" -AsPlainText -Force 

   $globalAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("GlobalAdmin@contoso.onmicrosoft.com", $globalAdminPassword) 
   ```

   Connect-AADCloudSyncAzureAD -Credential $globalAdminCreds 

 9. gMSA アカウントを追加し、ドメイン管理者の資格情報を指定して、既定の gMSA アカウントを作成します 
 
   ```
   $domainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 

   $domainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $domainAdminPassword) 

   Add-AADCloudSyncGMSA -Credential $domainAdminCreds 
   ```
 10. または、次のように上記のコマンドレットを使用して、事前に作成した gMSA アカウントを指定します。 

 
   ```
   Add-AADCloudSyncGMSA -CustomGMSAName preCreatedGMSAName$ 
   ```
 11. ドメインの追加 

   ```
   $contosoDomainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 

   $contosoDomainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $contosoDomainAdminPassword) 

   Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds 
   ```
 12. または、次のように上記のコマンドレットを使用して、優先ドメイン コントローラーを構成します 

   ```
   $preferredDCs = @("PreferredDC1", "PreferredDC2", "PreferredDC3") 

   Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds -PreferredDomainControllers $preferredDCs 
   ```
 13. 前の手順を繰り返してドメインをさらに追加します。各ドメインのアカウント名とドメイン名を指定してください 
 14. サービスを再起動します。 
   ```
   Restart-Service -Name AADConnectProvisioningAgent  
   ```
 15.  Azure portal にアクセスして、クラウド同期構成を作成します。

## <a name="provisioning-agent-gmsa-powershell-cmdlets"></a>プロビジョニング エージェント gMSA PowerShell コマンドレット
エージェントをインストールしたので、さらに詳細なアクセス許可を gMSA に適用できます。  アクセス許可の構成の詳細情報と詳細な手順については、「[Azure AD Connect クラウド プロビジョニング エージェント gMSA PowerShell コマンドレット](how-to-gmsa-cmdlets.md)」を参照してください。

## <a name="next-steps"></a>次のステップ 

- [プロビジョニングとは](what-is-provisioning.md)
- [Azure AD Connect クラウド プロビジョニング エージェント gMSA PowerShell コマンドレット](how-to-gmsa-cmdlets.md)
- [Azure AD Connect クラウド同期とは](what-is-cloud-sync.md)