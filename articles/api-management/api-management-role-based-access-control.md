---
title: "Azure API Management におけるロールベースのアクセス制御の方法 | Microsoft Docs"
description: "Azure API Management で組み込みロールを使用して、カスタム ロールを作成する方法について説明します。"
services: api-management
documentationcenter: 
author: miaojiang
manager: erikre
editor: 
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: b048a1db135d217b319541b92cf3c30b345d1a66
ms.openlocfilehash: cedab98452ad336748d805f0efbf50633badf8ab


---

# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Azure API Management におけるロールベースのアクセス制御の方法
Azure API Management では Azure Role-Based Access Control (RBAC) を使用して、API Management のサービスとエンティティ (API、ポリシーなど) に関するきめ細かいアクセス管理を可能にしています。 この記事では、API Management における組み込みロールとカスタム ロールについて説明します。 Azure Portal でのアクセス管理の詳細については、「[Azure Portal でのアクセス管理の概要](https://azure.microsoft.com/en-us/documentation/articles/role-based-access-control-what-is/)」を参照してください。

## <a name="built-in-roles"></a>組み込みの役割
API Management では現在、組み込みロールが 3 つ提供されており、近い将来、さらに 2 つのロールが追加されます。 これらのロールは、サブスクリプション、リソース グループ、および個々の API Management インスタンスなどのさまざまなスコープで割り当てることができます。 たとえば、"Azure API Management サービスのリーダー" のロールがリソース グループ レベルのユーザーに割り当てられている場合、そのユーザーはリソース グループ内のすべての API Management インスタンスに読み取りアクセスができるようになります。 

次の表に、組み込みのロールについての簡単な説明を示します。 これらのロールは Azure Portal や、Azure [PowerShell](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-manage-access-powershell)、Azure [コマンド ライン インターフェイス](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-manage-access-azure-cli)、[REST API](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-manage-access-rest) などの他のツールを使用して割り当てることができます。 組み込みロールを割り当てる方法の詳細については、[ロール割り当てを使用した Azure サブスクリプション リソースへのアクセス管理](https://azure.microsoft.com/en-us/documentation/articles/role-based-access-control-what-is/)に関するページを参照してください。

| 役割          | 読み取りアクセス<sup>[1]</sup> | 書き込みアクセス<sup>[2]</sup> | サービスの作成、削除、スケーリング、VPN、カスタム ドメインの構成 | 以前のパブリッシャー ポータルへのアクセス | Description
| ------------- | ---- | ---- | ---- | ---- | ---- | ---- |
| Azure API Management サービスの共同作業者 | ✓ | ✓  | ✓  | ✓ | スーパー ユーザー。 API Management のサービスとエンティティ (API、ポリシーなど) への完全な CRUD アクセス権がある。 以前のパブリッシャー ポータルにアクセスできる。 |
| Azure API Management サービスのリーダー | ✓ | | || API Management のサービスとエンティティへの読み取り専用アクセスがある。 |
| Azure API Management サービスのオペレーター | ✓ | | ✓ | | API Management サービスは管理できるが、エンティティの管理はできない。|
| Azure API Management サービスのエディター<sup>*</sup> | ✓ | ✓ | |  | API Management エンティティは管理できるが、サービスは管理できない。|
| Azure API Management コンテンツ マネージャー<sup>*</sup> | ✓ | | | ✓ | 開発者ポータルを管理できる。 サービスおよびエンティティへの読み取り専用アクセス。|

<sup>[1] API Management サービスとエンティティ (API、ポリシーなど) への読み取りアクセス</sup>

<sup>[2] 次の操作以外の API Management サービスとエンティティへの書き込みアクセス: 1) インスタンスの作成、削除、スケーリング、2) VPN 構成、3) カスタム ドメイン名のセットアップ</sup>

<sup>\*既存のパブリッシャー ポータルから Azure Portal へすべての管理者 UI を移行すると、サービス エディターのロールが利用可能になります。コンテンツ マネージャー ロールは、パブリッシャー ポータルが開発者ポータルの管理に関連する特定の機能のみを含むようリファクタリングされた後、利用可能になります。</sup>  


## <a name="custom-roles"></a>カスタム ロール
特定のニーズを満たす組み込みロールがない場合は、カスタム ロールを作成して、API Management エンティティのアクセス管理をより詳細にすることができます。 たとえば、API Management サービスへの読み取り専用アクセス権はあるが、特定の&1; つの API にしか書き込みアクセス権がないカスタム ロールを作成できます。 カスタム ロールの詳細については、「[Azure RBAC のカスタム ロール](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-custom-roles)」を参照してください。 

カスタム ロールを作成するときは、いずれかの組み込みロールを土台にすると簡単です。 その属性を編集して Actions、NotActions、またはAssignableScopes を追加し、変更内容を新しいロールとして保存します。 次の例では、はじめに "Azure API Managment Service Reader" ロールがあり、"Calculator API Editor" と呼ばれるカスタム ロールを作成します。 カスタム ロールは特定の&1; つの API にしか割り当てられないため、その API にのみアクセス権があります。 

```
$role = Get-AzureRmRoleDefinition "API Management Service Reader Role"
$role.Id = $null
$role.Name = 'Calculator API Contributor'
$role.Description = 'Has read access to Contoso APIM instance and write access to the Calculator API.'
$role.Actions.Add('Microsoft.ApiManagement/service/apis/write')
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add('/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>')
New-AzureRmRoleDefinition -Role $role
New-AzureRmRoleAssignment -ObjectId <object ID of the user account> -RoleDefinitionName 'Calculator API Contributor' -Scope '/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>'
```

## <a name="watch-a-video-overview"></a>ビデオの概要を見る

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
> 
> 

## <a name="next-steps"></a>次のステップ

* Azure のロールベースのアクセス制御の詳細について学習する
  * [Azure Portal でのアクセス管理の概要](https://azure.microsoft.com/en-us/documentation/articles/role-based-access-control-what-is/)
  * [Azure サブスクリプション リソースへのアクセスをロールの割り当てによって管理する](https://azure.microsoft.com/en-us/documentation/articles/role-based-access-control-what-is/)
  * [Azure RBAC のカスタム ロール](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-custom-roles)



<!--HONumber=Feb17_HO1-->


