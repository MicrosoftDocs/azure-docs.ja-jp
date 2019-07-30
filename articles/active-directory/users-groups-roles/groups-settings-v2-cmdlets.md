---
title: グループを管理するための PowerShell の例とオンプレミスへのグループの書き戻し (プレビュー) - Azure Active Directory | Microsoft Docs
description: このページでは、Azure Active Directory でグループを管理するための PowerShell の例を示します。
keywords: Azure AD、Azure Active Directory、PowerShell、グループ、グループ管理
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 06/14/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e22baabda901a34f624cf27c25037ff3ba94e90
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381845"
---
# <a name="azure-active-directory-version-2-cmdlets-for-group-management"></a>グループ管理用の Azure Active Directory バージョン 2 のコマンドレット

> [!div class="op_single_selector"]
> * [Azure Portal](../fundamentals/active-directory-groups-create-azure-portal.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
> * [PowerShell](groups-settings-v2-cmdlets.md)
>
>

この記事では、PowerShell を使用して、Azure Active Directory (Azure AD) でグループを管理する方法の例を掲載しています。  また、Azure AD PowerShell のモジュールを使用して設定する方法についても説明します。 最初に、 [Azure AD PowerShell モジュールをダウンロード](https://www.powershellgallery.com/packages/AzureAD/)する必要があります。

## <a name="install-the-azure-ad-powershell-module"></a>Azure AD PowerShell モジュールをインストールする

Azure AD PowerShell モジュールをインストールするには、次のコマンドを使用します。

```powershell
    PS C:\Windows\system32> install-module azuread
    PS C:\Windows\system32> import-module azuread
```

モジュールを使用する準備ができているかどうかを確認するには、次のコマンドを使用します。

```powershell
    PS C:\Windows\system32> get-module azuread

    ModuleType Version      Name                                ExportedCommands
    ---------- ---------    ----                                ----------------
    Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

これで、モジュールのコマンドレットの使用を開始できます。 Azure AD モジュールのコマンドレットについて詳しくは、[Azure Active Directory PowerShell バージョン 2](/powershell/azure/install-adv2?view=azureadps-2.0) に関するオンライン リファレンス ドキュメントをご覧ください。

## <a name="connect-to-the-directory"></a>ディレクトリに接続する

Azure AD PowerShell コマンドレットを使用してグループの管理を開始するには、管理するディレクトリに PowerShell セッションを接続する必要があります。 次のコマンドを使用します。

```powershell
    PS C:\Windows\system32> Connect-AzureAD
```

このコマンドレットでは、ディレクトリへのアクセスに使用する資格情報の入力を求められます。 この例では、 karen@drumkit.onmicrosoft.com を使用してデモンストレーション ディレクトリにアクセスします。 コマンドレットは、セッションがディレクトリに正常に接続されたことを表示する確認メッセージを返します。

```powershell
    Account                       Environment Tenant
    -------                       ----------- ------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…
```

これで、Azure AD コマンドレットの使用を開始して、ディレクトリ内のグループを管理できます。

## <a name="retrieve-groups"></a>グループを取得する

既存のグループをディレクトリから取得するには、Get-AzureADGroups コマンドレットを使用します。 

ディレクトリ内のすべてのグループを取得するには、パラメーターなしでコマンドレットを使用します。

```powershell
    PS C:\Windows\system32> get-azureadgroup
```

このコマンドレットは、接続されたディレクトリ内のすべてのグループを返します。

-objectID パラメーターを使用すると、グループの objectID を指定する特定のグループを取得できます。

```powershell
    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b
```

コマンドレットは次のように、入力したパラメーターの値に一致する objectID のグループを返します。

```powershell
    DeletionTimeStamp            :
    ObjectId                     : e29bae11-4ac0-450c-bc37-6dae8f3da61b
    ObjectType                   : Group
    Description                  :
    DirSyncEnabled               :
    DisplayName                  : Pacific NW Support
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 9bb4139b-60a1-434a-8c0d-7c1f8eee2df9
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

-filter パラメーターを使用して、特定のグループを検索できます。 次の例に示すように、このパラメーターは ODATA フィルター句を使用し、フィルターに一致するすべてのグループを返します。

```powershell
    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

> [!NOTE]
> Azure AD PowerShell コマンドレットは標準の OData クエリを実装しています。 詳しくは、「[OData エンドポイントを使用する OData システム クエリ オプション](https://msdn.microsoft.com/library/gg309461.aspx#BKMK_filter)」の「 **$filter**」を参照してください。

## <a name="create-groups"></a>グループを作成する

新しいグループをディレクトリに作成するには、New-AzureADGroup コマンドレットを使用します。 このコマンドレットでは、"Marketing" という新しいセキュリティ グループを作成します。

```powershell
    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"
```

## <a name="update-groups"></a>グループを更新する

既存のグループを更新するには、Set-AzureADGroup コマンドレットを使用します。 この例では、"Intune Administrators" グループの DisplayName プロパティを変更します。 最初に、Get-AzureADGroup コマンドレットを使用してグループを検索し、DisplayName 属性を使用してフィルターを検索します。

```powershell
    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

次に、Description プロパティを新しい値 "Intune Device Administrators" に変更します。

```powershell
    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"
```

ここで、もう一度グループを検索する場合は、Description プロパティが更新され、新しい値が反映されていることを確認します。

```powershell
    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"

    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Device Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

## <a name="delete-groups"></a>グループを削除する

ディレクトリからグループを削除するには、次のように、Remove-AzureADGroup コマンドレットを使用します。

```powershell
    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b
```

## <a name="manage-group-membership"></a>グループ メンバーシップを管理する

### <a name="add-members"></a>メンバーを追加する

新しいメンバーをグループに追加するには、Add-AzureADGroupMember コマンドレットを使用します。 このコマンドは、前の例で使用した Intune Administrators グループにメンバーを追加します。

```powershell
    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

-ObjectId パラメーターはメンバーを追加するグループの ObjectID であり、-RefObjectId はグループにメンバーとして追加するユーザーの ObjectID です。

### <a name="get-members"></a>メンバーを取得する

グループの既存のメンバーを取得するには、この例のように、Get-AzureADGroupMember コマンドレットを使用します。

```powershell
    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                          8120cc36-64b4-4080-a9e8-23aa98e8b34f User
```

### <a name="remove-members"></a>メンバーを削除する

グループに以前追加したメンバーを削除するには、ここに示したように、Remove-AzureADGroupMember コマンドレットを使用します。

```powershell
    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

### <a name="verify-members"></a>メンバーを確認する

ユーザーのグループ メンバーシップを確認するには、Select-AzureADGroupIdsUserIsMemberOf コマンドレットを使用します。 このコマンドレットは、グループ メンバーシップを確認する対象のユーザーの ObjectId、およびメンバーシップを確認する対象のグループの一覧を、パラメーターとして使用します。 グループの一覧は “Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck” 型の複合変数の形式に指定する必要があるため、最初にその型で変数を作成する必要があります。

```powershell
    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck
```

次に、groupIds の値を入力し、この複合変数の "GroupIds" 属性で確認します。

```powershell
    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"
```

ここで、$g のグループに対して ObjectID 72cd4bbd-2594-40a2-935c-016f3cfeeeea を持つユーザーのグループ メンバーシップを確認する場合は、次を使用する必要があります。

```powershell
    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                                 Value
    -------------                                                                                                  -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}
```

返される値は、このユーザーがメンバーであるグループの一覧です。 また、このメソッドを適用し、Select-AzureADGroupIdsContactIsMemberOf、Select-AzureADGroupIdsGroupIsMemberOf または Select-AzureADGroupIdsServicePrincipalIsMemberOf を使用して、グループの指定された一覧に対する連絡先、グループ、またはサービス プリンシパルのメンバーシップを確認することもできます。

## <a name="disable-group-creation-by-your-users"></a>ユーザーによるグループの作成を無効にする

管理者以外のユーザーがセキュリティ グループを作成できないようにできます。 MS Online Directory Service (MSODS) の既定の動作では、管理者以外のユーザーによるグループの作成は許可されます。これはセルフサービス グループ管理 (SSGM) が有効であるかどうかとは無関係です。 SSGM の設定で制御できるのは、[マイ アプリ] アクセス パネルでの動作だけです。

管理者以外のユーザーによるグループの作成を無効にするには、次のようにします。

1. 管理者以外のユーザーにグループの作成が許可されていることを確認します。
   
   ```powershell
   PS C:\> Get-MsolCompanyInformation | fl UsersPermissionToCreateGroupsEnabled
   ```
  
2. `UsersPermissionToCreateGroupsEnabled : True` が返された場合は、管理者以外のユーザーはグループを作成できます。 この機能を無効にするには、次のコマンドを実行します。
  
   ```powershell 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```
  
## <a name="manage-owners-of-groups"></a>グループの所有者を管理する

グループに所有者を追加するには、Add-AzureADGroupOwner コマンドレットを使用します。

```powershell
    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

-ObjectId パラメーターは所有者を追加するグループの ObjectID であり、-RefObjectId はグループに所有者として追加するユーザーまたはサービス プリンシパルの ObjectID です。

グループの所有者を取得するには、Get-AzureADGroupOwner コマンドレットを使用します。

```powershell
    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
```

コマンドレットは、指定したグループの所有者 (ユーザーおよびサービス プリンシパル) の一覧を返します。

```powershell
    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          e831b3fd-77c9-49c7-9fca-de43e109ef67 User
```

グループから所有者を削除する場合は、Remove-AzureADGroupOwner コマンドレットを使用します。

```powershell
    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67
```

## <a name="reserved-aliases"></a>予約済みのエイリアス

グループを作成すると、特定のエンドポイントで、グループの電子メール アドレスの一部として使用する mailNickname またはエイリアスをエンド ユーザーが指定できるようになります。 次のような高い権限を持つ電子メール エイリアスを使用するグループを作成できるのは、Azure AD グローバル管理者だけです。 
  
* abuse
* admin
* 管理者
* hostmaster
* majordomo
* postmaster
* root
* secure
* security
* ssl-admin
* webmaster

## <a name="group-writeback-to-on-premises-preview"></a>オンプレミスへのグループの書き戻し (プレビュー)

現在でも、多くのグループがオンプレミスの Active Directory で管理されています。 クラウド グループをオンプレミスに逆同期するという要望に応えるために、Azure AD の Office 365 グループ書き戻し機能がプレビューで利用できるようになりました。

Office 365 グループはクラウドで作成および管理されます。 書き戻し機能を使用すると、Exchange がインストールされている Active Directory フォレストに Office 365 グループを配布グループとして書き戻すことができます。 オンプレミスの Exchange メールボックスを持つユーザーは、これらのグループとの間で電子メールを送受信できます。 グループの書き戻し機能は、Azure AD のセキュリティ グループまたは配布グループをサポートしていません。

詳細については、[Azure AD Connect の同期サービス](../hybrid/how-to-connect-syncservice-features.md)に関するドキュメントを参照してください。

Office 365 のグループの書き戻しは Azure Active Directory (Azure AD) のパブリック プレビュー機能であり、すべての有料 Azure AD ライセンス プランに付属します。 プレビューに関する法的情報については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="next-steps"></a>次の手順

Azure Active Directory PowerShell のその他のドキュメントについては、 [Azure Active Directory コマンドレット](/powershell/azure/install-adv2?view=azureadps-2.0)を参照してください。

* [Azure Active Directory グループによるリソースへのアクセス管理](../fundamentals/active-directory-manage-groups.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [オンプレミス ID と Azure Active Directory の統合](../hybrid/whatis-hybrid-identity.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
