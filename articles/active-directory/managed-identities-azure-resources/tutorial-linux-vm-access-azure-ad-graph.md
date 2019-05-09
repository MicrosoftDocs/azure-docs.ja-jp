---
title: Linux VM のシステム割り当てマネージド ID を使用して Azure AD Graph API にアクセスする
description: Linux VM のシステム割り当てマネージド ID を使用して Azure AD Graph API にアクセスするプロセスについて説明するチュートリアルです。
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
ms.openlocfilehash: 481cb560daa26e59de2c78cc64bab9fb168eed58
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445409"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-ad-graph-api"></a>チュートリアル:Linux VM のシステム割り当てマネージド ID を使用して Azure AD Graph API にアクセスする

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice.md)]

このチュートリアルでは、Linux 仮想マシン (VM) 向けのシステム割り当てマネージド ID を使用して、Azure AD Graph API にアクセスし、そのグループ メンバーシップを取得する方法について説明します。 Azure リソースのマネージド ID は Azure によって自動的に管理され、資格情報をコードに挿入しなくても、Azure AD 認証をサポートするサービスへの認証を有効にします。  

このチュートリアルでは、Azure AD グループ内での VM ID のメンバーシップをクエリします。 グループの情報は承認の判断によく使用されます。 実際には、VM のマネージド ID は Azure AD 内の**サービス プリンシパル**によって表されます。 

> [!div class="checklist"]
> * Azure への接続
> * Azure AD 内のグループに VM ID を追加する 
> * VM の ID アクセスを Azure AD Graph に許可する 
> * VM ID を使用してアクセス トークンを取得し、それを使用して Azure AD Graph を呼び出す

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [最新バージョンの Azure CLI をインストールする](https://docs.microsoft.com/cli/azure/install-azure-cli)

- Azure AD Graph に VM ID アクセスを許可するには、Azure AD で**全体管理者**ロールがアカウントに割り当てられている必要があります。

## <a name="connect-to-azure-ad"></a>Azure への接続

Azure AD に接続して、VM をグループに割り当てることに加えて、そのグループ メンバーシップを取得するためのアクセス許可を VM に付与する必要があります。

```cli
az login
```

## <a name="add-your-vms-identity-to-a-group-in-azure-ad"></a>VM の ID を Azure AD 内のグループに追加する

Linux VM でシステム割り当てマネージド ID を有効にした時点で、Azure AD にサービス プリンシパルが作成されました。  VM をグループに追加する必要があります。 VM を Azure AD 内のグループに追加する方法については、次の記事を参照してください:

- [Add group members](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add)\(グループ メンバーの追加\)

## <a name="grant-your-vm-access-to-the-azure-ad-graph-api"></a>Azure AD Graph API へのアクセスを VM に許可する

Azure リソースのマネージド ID を使用すると、Azure AD 認証をサポートするリソースに対して認証するためのアクセス トークンをコードで取得できます。 Microsoft Azure AD Graph API は Azure AD 認証をサポートします。 この手順では、VM ID のサービス プリンシパルに Azure AD Graph へのアクセスを許可して、グループ メンバーシップをクエリできるようにします。 サービス プリンシパルは、**アプリケーションのアクセス許可**を通じて Microsoft または Azure AD Graph へのアクセスを許可されます。 付与する必要があるアプリケーションのアクセス許可の種類は、MS または Azure AD Graph でアクセスするエンティティによって異なります。

このチュートリアルでは、VM ID で `Directory.Read.All` のアプリケーションのアクセス許可を使用してグループ メンバーシップをクエリできるようにします。 このアクセス許可を付与するには、Azure AD で全体管理者のロールが割り当てられているユーザー アカウントが必要です。 通常は、Azure portal でアプリケーションの登録にアクセスし、そこでアクセス許可を追加することによって、アプリケーションのアクセス許可を付与します。 ただし、Azure リソースのマネージド ID は Azure AD にアプリケーション オブジェクトを登録せず、サービス プリンシパルを登録するだけです。 アプリケーションのアクセス許可を登録するには、Azure AD PowerShell コマンド ライン ツールを使用します。 

Azure AD Graph:
- サービス プリンシパル appId (アプリのアクセス許可の付与時に使用): 00000002-0000-0000-c000-000000000000
- リソース ID (Azure リソースのマネージド ID からアクセス トークンを要求するときに使用): https://graph.windows.net
- アクセス許可のスコープに関するリファレンス: [Azure AD Graph のアクセス許可に関するリファレンス](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)

### <a name="grant-application-permissions-using-curl"></a>CURL を使用してアプリケーションのアクセス許可を付与する

1. CURL 要求を行うためのトークンを取得します:

   ```cli
   az account get-access-token --resource "https://graph.windows.net/"
   ```

2. VM の `objectId` を取得してメモする必要があります。 後の手順で、そのグループ メンバーシップを読み取るためのアクセス許可を VM に付与するために使用します。 `<ACCESS TOKEN>` を、前の手順で取得したアクセス トークンに置き換えます。

   ```bash
   curl 'https://graph.windows.net/myorganization/servicePrincipals?$filter=startswith%28displayName%2C%27myVM%27%29&api-version=1.6' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

3. Azure AD Graph appID 00000002-0000-0000-c000-000000000000 を使用して、`odata.type: Microsoft.DirectoryServices.ServicePrincipal` の `objectId` と、`Directory.Read.All` のアプリ ロール アクセス許可の `id` を取得します。  `<ACCESS TOKEN>` は、以前に取得したアクセス トークンに置き換えます。

   ```bash
   curl "https://graph.windows.net/myorganization/servicePrincipals?api-version=1.6&%24filter=appId%20eq%20'00000002-0000-0000-c000-000000000000'" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   応答:

   ```json
   "odata.metadata":"https://graph.windows.net/myorganization/$metadata#directoryObjects",
   "value":[
      {
         "odata.type":"Microsoft.DirectoryServices.ServicePrincipal",
         "objectType":"ServicePrincipal",
         "objectId":"81789304-ff96-402b-ae73-07ec0db26721",
         "deletionTimestamp":null,
         "accountEnabled":true,
         "addIns":[
         ],
         "alternativeNames":[
         ],
         "appDisplayName":"Windows Azure Active Directory",
         "appId":"00000002-0000-0000-c000-000000000000",
         "appOwnerTenantId":"f8cdef31-a31e-4b4a-93e4-5f571e91255a",
         "appRoleAssignmentRequired":false,
         "appRoles":[
            {
               "allowedMemberTypes":[
                  "Application"
               ],
               "description":"Allows the app to read data in your company or school directory, such as users, groups, and apps.",
               "displayName":"Read directory data",
               "id":"5778995a-e1bf-45b8-affa-663a9f3f4d04",
               "isEnabled":true,
               "value":"Directory.Read.All"
            },
            {
               //other appRoles values
            }
   ``` 

4. ここで、VM のサービス プリンシパルに、Azure AD Graph API を使用して Azure AD ディレクトリ オブジェクトを読み取るアクセス権を付与します。  `id` の値は `Directory.Read.All` のアプリ ロール アクセス許可の値であり、`resourceId` はサービス プリンシパル `odata.type:Microsoft.DirectoryServices.ServicePrincipal` の `objectId` です (前の手順でメモした値)。

   ```bash
   curl "https://graph.windows.net/myorganization/servicePrincipals/<VM Object ID>/appRoleAssignments?api-version=1.6" -X POST -d '{"id":"5778995a-e1bf-45b8-affa-663a9f3f4d04","principalId":"<VM Object ID>","resourceId":"81789304-ff96-402b-ae73-07ec0db26721"}'-H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ``` 
 
## <a name="get-an-access-token-using-the-vms-identity-to-call-azure-ad-graph"></a>VM ID を使用してアクセス トークンを取得し、 Azure AD Graph を呼び出す 

これらの手順を完了するには、SSH クライアントが必要です。 Windows を使用している場合は、[Windows Subsystem for Linux](https://msdn.microsoft.com/commandline/wsl/about) で SSH クライアントを使用することができます。 SSH クライアント キーの構成について支援が必要な場合は、「[Azure 上の Windows で SSH キーを使用する方法](../../virtual-machines/linux/ssh-from-windows.md)」または「[Azure に Linux VM 用の SSH 公開キーと秘密キーのペアを作成して使用する方法](../../virtual-machines/linux/mac-create-ssh-keys.md)」をご覧ください。

1. ポータルで Linux VM に移動し、**[概要]** の **[接続]** をクリックします。  
2. 任意の SSH クライアントを使用して、VM に**接続**します。 
3. ターミナル ウィンドウで、CURL を使用して、Azure リソース エンドポイントのローカル マネージド ID に、Azure AD Graph のアクセス トークンを取得するよう要求します。  
    
   ```bash
   curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://graph.windows.net' -H Metadata:true
   ```    
   応答には、Azure AD Graph へのアクセスに必要なアクセス トークンが含まれています。

   応答:

   ```bash
   {
       "access_token":"eyJ0eXAiOiJKV...",
       "expires_in":"3599",
       "expires_on":"1519622535",
       "not_before":"1519618635",
       "resource":"https://graph.windows.net",
       "token_type":"Bearer"
   }
   ```

4. VM のサービス プリンシパルのオブジェクト ID (以前の手順で取得した値) を使用して、Azure AD Graph API をクエリし、そのグループ メンバーシップを取得することができます。 `<OBJECT-ID>` は VM のサービス プリンシパルのオブジェクト ID に、`<ACCESS-TOKEN>` は以前に取得したアクセス トークンにそれぞれ置き換えます。

   ```bash
   curl 'https://graph.windows.net/myorganization/servicePrincipals/<OBJECT-ID>/getMemberGroups?api-version=1.6' -X POST -d "{\"securityEnabledOnly\": false}" -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS-TOKEN>"
   ```

   応答:

   ```bash   
   Content : {"odata.metadata":"https://graph.windows.net/myorganization/$metadata#Collection(Edm.String)","value":["<ObjectID of VM's group membership>"]}
   ```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Linux VM のシステム割り当てマネージド ID を使用して Azure AD Graph にアクセスする方法について説明しました。  Azure AD Graph の詳細については、次を参照してください:

>[!div class="nextstepaction"]
>[Azure AD Graph](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api)
