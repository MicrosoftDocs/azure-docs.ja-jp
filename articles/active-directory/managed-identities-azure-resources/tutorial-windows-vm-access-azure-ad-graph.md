---
title: Windows VM のシステム割り当てマネージド ID を使用して Azure AD Graph API にアクセスする
description: Windows VM のシステム割り当てマネージド ID を使用して Azure AD Graph API にアクセスするプロセスについて説明するチュートリアルです。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/20/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: b402fa754105b734bfc7abbd2790a2a12afc6ff4
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59523360"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-ad-graph-api"></a>チュートリアル:Windows VM のシステム割り当てマネージド ID を使用して Azure AD Graph API にアクセスする

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice.md)]

このチュートリアルでは、Windows 仮想マシン (VM) のシステム割り当てマネージド ID を使用して Microsoft Graph API にアクセスし、そのグループ メンバーシップを取得する方法について説明します。 Azure リソースのマネージド ID は Azure によって自動的に管理され、資格情報をコードに挿入しなくても、Azure AD 認証をサポートするサービスへの認証を有効にします。  このチュートリアルでは、Azure AD グループ内での VM ID のメンバーシップをクエリします。 たとえば、グループの情報は承認の判断によく使用されます。 実際には、VM のマネージド ID は Azure AD 内の**サービス プリンシパル**によって表されます。 グループ クエリを実行する前に、VM の ID を表すサービス プリンシパルを Azure AD 内のグループに追加します。 これは Azure PowerShell、Azure AD PowerShell、または Azure CLI を使用して実行できます。

> [!div class="checklist"]
> * Azure への接続
> * Azure AD 内のグループに VM ID を追加する 
> * VM の ID アクセスを Azure AD Graph に許可する 
> * VM ID を使用してアクセス トークンを取得し、それを使用して Azure AD Graph を呼び出す

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- Azure AD Graph に VM ID アクセスを許可するには、Azure AD で**全体管理者**ロールがアカウントに割り当てられている必要があります。
- 最新の [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2) をインストールします (まだインストールしていない場合)。 

## <a name="connect-to-azure-ad"></a>Azure への接続

Azure AD に接続して、VM をグループに割り当てることに加えて、そのグループ メンバーシップを取得するためのアクセス許可を VM に付与する必要があります。 Connect-AzureAD コマンドレットは直接使用できるほか、複数のテナントがある場合には TenantId パラメータを指定することもできます。

```powershell
Connect-AzureAD
```
または
```powershell
Connect-AzureAD -TenantId "Object Id of the tenant"
```

## <a name="add-your-vm-identity-to-a-group-in-azure-ad"></a>Azure AD 内のグループに VM ID を追加する

Windows VM でシステム割り当てマネージド ID を有効にした時点で、Azure AD にサービス プリンシパルが作成されました。  ここで、VM をグループに追加する必要があります。  次の例では、Azure AD に新しいグループを作成し、VM のサービス プリンシパルをそのグループに追加します:

```powershell
New-AzureADGroup -DisplayName "myGroup" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
$AzureADGroup = Get-AzureADGroup -Filter "displayName eq 'myGroup'"
$ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq 'myVM'"
Add-AzureADGroupMember -ObjectId $AzureADGroup.ObjectID -RefObjectId $ManagedIdentitiesServicePrincipal.ObjectId
```
## <a name="grant-your-vm-access-to-the-azure-ad-graph-api"></a>Azure AD Graph API へのアクセスを VM に許可する

Azure リソースのマネージド ID を使用すると、Azure AD 認証をサポートするリソースに対して認証するためのアクセス トークンをコードで取得できます。 Microsoft Azure AD Graph API は Azure AD 認証をサポートします。 この手順では、VM ID のサービス プリンシパルに Azure AD Graph へのアクセスを許可して、グループ メンバーシップをクエリできるようにします。 サービス プリンシパルは、**アプリケーションのアクセス許可**を通じて Microsoft または Azure AD Graph へのアクセスを許可されます。 付与する必要があるアプリケーションのアクセス許可の種類は、MS または Azure AD Graph でアクセスするエンティティによって異なります。

このチュートリアルでは、VM ID で ```Directory.Read.All``` のアプリケーションのアクセス許可を使用してグループ メンバーシップをクエリできるようにします。 このアクセス許可を付与するには、Azure AD で全体管理者のロールが割り当てられているユーザー アカウントが必要です。 通常は、Azure portal でアプリケーションの登録にアクセスし、そこでアクセス許可を追加することによって、アプリケーションのアクセス許可を付与します。 ただし、Azure リソースのマネージド ID は Azure AD にアプリケーション オブジェクトを登録せず、サービス プリンシパルを登録するだけです。 アプリケーションのアクセス許可を登録するには、Azure AD PowerShell コマンド ライン ツールを使用します。 

Azure AD Graph:
- サービス プリンシパル appId (アプリのアクセス許可の付与時に使用): 00000002-0000-0000-c000-000000000000
- リソース ID (Azure リソースのマネージド ID からアクセス トークンを要求するときに使用): https://graph.windows.net
- アクセス許可のスコープに関するリファレンス: [Azure AD Graph のアクセス許可に関するリファレンス](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)

### <a name="grant-application-permissions-using-azure-ad-powershell"></a>Azure AD PowerShell を使用してアプリケーションのアクセス許可を付与する

このオプションを使用するには Azure AD PowerShell が必要です。 インストールしていない場合は、先に進む前に、[最新バージョンをダウンロード](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2)してください。

1. PowerShell ウィンドウを開き、Azure AD に接続します:

   ```powershell
   Connect-AzureAD
   ```
   特定の Azure Active Directory に接続するには、次のように _TenantId_ パラメーターを使用します。

   ```powershell
   Connect-AzureAD -TenantId "Object Id of the tenant"
   ```

   
2. 次の PowerShell コマンドを実行して、VM の ID を表すサービス プリンシパルに ``Directory.Read.All`` のアプリケーションのアクセス許可を割り当てます。

   ```powershell
   $ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq 'myVM'"
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   $PermissionName = "Directory.Read.All"
   $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}
   New-AzureAdServiceAppRoleAssignment -ObjectId $ManagedIdentitiesServicePrincipal.ObjectId -PrincipalId $ManagedIdentitiesServicePrincipal.ObjectId -ResourceId $GraphServicePrincipal.ObjectId -Id $AppRole.Id
   ``` 

   最後のコマンドからの出力は次のようになり、割り当ての ID を返します:
        
   `ObjectId`:`gzR5KyLAiUOTiqFhNeWZWBtK7ZKqNJxAiWYXYVHlgMs`

   `ResourceDisplayName`:`Windows Azure Active Directory`

   `PrincipalDisplayName`:`myVM` 

   `New-AzureAdServiceAppRoleAssignment` の呼び出しがエラー `bad request, one or more properties are invalid` で失敗した場合、アプリのアクセス許可が VM ID のサービス プリンシパルに既に割り当てられている可能性があります。 次の PowerShell コマンドを使用して、VM の ID と Azure AD Graph の間にアプリケーションのアクセス許可が既に存在しているかどうかを確認できます:

   ```powershell
   $ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq '<VM-NAME>'"
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   $PermissionName = "Directory.Read.All"
   $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}
   Get-AzureADServiceAppRoleAssignment -ObjectId $GraphServicePrincipal.ObjectId | Where-Object {$_.Id -eq $AppRole.Id -and $_.PrincipalId -eq $ManagedIdentitiesServicePrincipal.ObjectId}
   ```

   次の PowerShell コマンドを使用して、Azure AD Graph に付与済みであるアプリのアクセス許可をすべて一覧表示できます:

   ```powershell
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   Get-AzureADServiceAppRoleAssignment -ObjectId $GraphServicePrincipal.ObjectId
   ``` 

   次の PowerShell コマンドを使用して、Azure AD Graph の VM ID に付与済みであるアプリのアクセス許可を削除できます:

   ```powershell
   $ManagedIdentitiesServicePrincipal = Get-AzureADServicePrincipal -Filter "displayName eq '<VM-NAME>'"
   $GraphAppId = "00000002-0000-0000-c000-000000000000"
   $GraphServicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$GraphAppId'"
   $PermissionName = "Directory.Read.All"
   $AppRole = $GraphServicePrincipal.AppRoles | Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}   
   $ServiceAppRoleAssignment = Get-AzureADServiceAppRoleAssignment -ObjectId $GraphServicePrincipal.ObjectId | Where-Object {$_.Id -eq $AppRole.Id -and $_.PrincipalId -eq $ManagedIdentitiesServicePrincipal.ObjectId}
   Remove-AzureADServiceAppRoleAssignment -AppRoleAssignmentId $ServiceAppRoleAssignment.ObjectId -ObjectId $ManagedIdentitiesServicePrincipal.ObjectId
   ```
 
## <a name="get-an-access-token-using-the-vms-identity-to-call-azure-ad-graph"></a>VM ID を使用してアクセス トークンを取得し、 Azure AD Graph を呼び出す 

VM のシステム割り当てマネージド ID を Azure AD Graph への認証で使用するには、VM から要求を行う必要があります。

1. ポータルで **[仮想マシン]** を開いて Windows VM に移動し、**[概要]** ブレードの **[接続]** をクリックします。  
2. Windows VM を作成したときに使用した**ユーザー名**と**パスワード**を入力します。
3. これで、仮想マシンを使用するリモート デスクトップ接続が作成されました。リモート セッションで PowerShell を開きます。  
4. PowerShell の Invoke-WebRequest を使用して、Azure リソース エンドポイントのローカル マネージド ID に、Azure AD Graph のアクセス トークンを取得するよう要求します。

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://graph.windows.net' -Method GET -Headers @{Metadata="true"}
   ```

   応答を JSON オブジェクトから PowerShell オブジェクトに変換します。

   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   応答からアクセス トークンを抽出します。

   ```powershell
   $AccessToken = $content.access_token
   ```

5. VM ID のサービス プリンシパルのオブジェクト ID (この値は以前の手順で宣言した変数から取得できます: ``$ManagedIdentitiesServicePrincipal.ObjectId``) を使用して Azure AD Graph API をクエリし、そのグループ メンバーシップを取得できます。 `<OBJECT ID>` は以前の手順で取得したオブジェクト ID に、<`ACCESS-TOKEN>` は以前に取得したアクセス トークンにそれぞれ置き換えます。

   ```powershell
   Invoke-WebRequest 'https://graph.windows.net/<Tenant ID>/servicePrincipals/<VM Object ID>/getMemberGroups?api-version=1.6' -Method POST -Body '{"securityEnabledOnly":"false"}' -Headers @{Authorization="Bearer $AccessToken"} -ContentType "application/json"
   ```
   
   応答には、以前の手順で VM のサービス プリンシパルを追加したグループの `Object ID` が含まれています。

   応答:

   ```powershell   
   Content : {"odata.metadata":"https://graph.windows.net/<Tenant ID>/$metadata#Collection(Edm.String)","value":["<ObjectID of VM's group membership>"]}
   ```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Windows VM のシステム割り当てマネージド ID を使用して Azure AD Graph にアクセスする方法について説明しました。  Azure AD Graph の詳細については、次を参照してください:

>[!div class="nextstepaction"]
>[Azure AD Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api)
