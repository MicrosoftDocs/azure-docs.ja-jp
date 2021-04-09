---
title: Azure AD Connect クラウド プロビジョニング エージェント gMSA PowerShell コマンドレット
description: Azure AD Connect クラウド プロビジョニング エージェント gMSA PowerShell コマンドレットの使用方法について説明します。
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
ms.openlocfilehash: 411a8e46151a762bcd270fb676f78a91f760ac4f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101653798"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-gmsa-powershell-cmdlets"></a>Azure AD Connect クラウド プロビジョニング エージェント gMSA PowerShell コマンドレット

このドキュメントの目的は、Azure AD Connect クラウド プロビジョニング エージェント gMSA PowerShell コマンドレットの使用方法について説明することです。 これらのコマンドレットを使用すると、サービス アカウント (gmsa) に対して適用されるアクセス許可をより細かく設定できます。 既定では、Azure AD Connect クラウド同期によって Azure AD Connect に似たすべてのアクセス許可が既定の gmsa またはカスタム gmsa に対して適用されます。 

このドキュメントでは、次のコマンドレットについて説明します。  

`Set-AADCloudSyncRestrictedPermissions`

`Ste-AADCloudSyncPermissions` 

## <a name="how-to-use-the-cmdlets"></a>コマンドレットの使用方法:  

これらのコマンドレットを使用するには、次の前提条件があります。

1. プロビジョニング エージェントをインストールします。 
2. プロビジョニング エージェント PS モジュールを PowerShell セッションにインポートします。 

 ```PowerShell
 Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Microsoft.CloudSync.Powershell.dll"  
 ```
3. 既存のアクセス許可を削除します。  サービス アカウントの既存のアクセス許可を、自己使用を除いてすべて削除するには、`Set-AADCloudSyncRestrictedPermission` を使用します。  

    このコマンドレットには `Credential` というパラメーターを指定して、渡せるようにする必要があります。これを指定せずに呼び出した場合は、プロンプトが表示されます。

    変数を作成するには、以下を使用します  

   `$credential = Get-Credential` 

   これにより、ユーザー名とパスワードを入力するよう求められます。 サインイン情報は少なくとも (エージェントがインストールされているドメインの) ドメイン管理者でなければなりません。エンタープライズ管理者でもかまいません。 

4.  次に、コマンドレットを呼び出して余分なアクセス許可を削除できます。 
   ```PowerShell
   Set-AADCloudSyncRestrictedPermissions -Credential $credential 
   ```
5. あるいは、以下を呼び出します。 

   `Set-AADCloudSyncRestrictedPermissions` (これによりサインイン情報が求められます) 

 6.  特定のアクセス許可の種類を追加します。  追加するアクセス許可は Azure AD Connect と同じです。  アクセス許可の設定例については、下の「[Set-AADCloudSyncPermissions を使用する](#using-set-aadcloudsyncpermissions)」を参照してください。

## <a name="using-set-aadcloudsyncpermissions"></a>Set-AADCloudSyncPermissions を使用する 
`Set-AADCloudSyncPermissions` でサポートされている次のアクセス許可の種類は、Azure AD Connect で使用されるアクセス許可と同じです。 次のアクセス許可の種類がサポートされています。 

|アクセス許可の種類|説明|
|-----|-----|
|BasicRead| Azure AD Connect の [BasicRead](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#configure-basic-read-only-permissions) アクセス許可を参照してください|
|PasswordHashSync|Azure AD Connect の [PasswordHashSync](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-password-hash-synchronization) アクセス許可を参照してください|
|PasswordWriteBack|Azure AD Connect の [PasswordWriteBack](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-password-writeback) アクセス許可を参照してください|
|HybridExchangePermissions|Azure AD Connect の [HybridExchangePermissions](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-exchange-hybrid-deployment) アクセス許可を参照してください| 
|ExchangeMailPublicFolderPermissions| Azure AD Connect の [ExchangeMailPublicFolderPermissions](../../active-directory/hybrid/how-to-connect-configure-ad-ds-connector-account.md#permissions-for-exchange-mail-public-folders-preview) アクセス許可を参照してください| 
|CloudHR| [Descendant User objects]\(ユーザーの子孫オブジェクト\) に対して [フル コントロール] を適用し、[このオブジェクトとすべての子オブジェクト] に対して [Create/delete User objects]\(ユーザー オブジェクトの作成または削除\) を適用します| 
|すべて|上のすべてのアクセス許可が追加されます。| 



AADCloudSyncPermissions を次のいずれかの方法で使用できます。
- [構成されているすべてのドメインに特定のアクセス許可を付与する](#grant-a-certain-permission-to-all-configured-domains) 
- [特定のドメインに特定のアクセス許可を付与する](#grant-a-certain-permission-to-a-specific-domain) 
## <a name="grant-a-certain-permission-to-all-configured-domains"></a>構成されているすべてのドメインに特定のアクセス許可を付与する 
構成されているすべてのドメインに特定のアクセス許可を付与する場合は、エンタープライズ管理者アカウントを使用する必要があります。


 ```PowerShell
Set-AADCloudSyncPermissions -PermissionType “Any mentioned above” -EACredential $credential (prepopulated same as above [$credential = Get-Credential]) 
```

## <a name="grant-a-certain-permission-to-a-specific-domain"></a>特定のドメインに特定のアクセス許可を付与する 
特定のドメインに特定のアクセス許可を付与する場合は、最低でも、追加しようとしているドメインのドメイン管理者アカウントを使用する必要があります。


 ```PowerShell
Set-AADCloidSyncPermissions -PermissionType “Any mentioned above” -TargetDomain “FQDN of domain” (has to be already configured through wizard) -TargetDomaincredential $credential(same as above) 
```
 

注: 1 の場合、 資格情報は最低でも、エンタープライズ管理者のものでなければなりません。 

[2.  資格情報は、ドメイン管理者とエンタープライズ管理者のどちらでもかまいません。 

  

