---
title: Azure API Management でロールベースのアクセス制御を使用する方法 | Microsoft Docs
description: Azure API Management で組み込みロールを使用して、カスタム ロールを作成する方法について説明します。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: fc41d2904d5a6676f1e9e1e2d178985bef0a9aa3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101095541"
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Azure API Management でロールベースのアクセス制御を使用する方法

Azure API Management では、Azure のロールベースのアクセス制御 (RBAC) の使用により、API Management のサービスとエンティティ (API やポリシーなど) に関するきめ細かいアクセス管理が可能になります。 この記事では、API Management における組み込みロールとカスタム ロールについて説明します。 Azure ポータルでのアクセス管理の詳細については、「[Azure ポータルでのアクセス管理の概要](../role-based-access-control/overview.md)」を参照してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="built-in-roles"></a>組み込みのロール

API Management には、現在、組み込みロールが 3 つ提供されており、近い将来、さらに 2 つのロールが追加されます。 これらのロールは、サブスクリプション、リソース グループ、および個々の API Management インスタンスなどのさまざまなスコープで割り当てることができます。 たとえば、"API Management サービスのリーダー" ロールをリソース グループ レベルのユーザーに割り当てた場合、そのユーザーはリソース グループ内のすべての API Management インスタンスに読み取りアクセスを行うことができます。 

次の表に、組み込みのロールについての簡単な説明を示します。 これらのロールは Azure ポータルや、Azure [PowerShell](../role-based-access-control/role-assignments-powershell.md)、[Azure CLI](../role-based-access-control/role-assignments-cli.md)、[REST API](../role-based-access-control/role-assignments-rest.md) などのツールを使用して割り当てることができます。 組み込みロールを割り当てる方法の詳細については、[Azure サブスクリプション リソースへのアクセスを管理するための Azure ロールの割り当て](../role-based-access-control/role-assignments-portal.md)に関するページを参照してください。

| Role          | 読み取りアクセス<sup>[1]</sup> | 書き込みアクセス<sup>[2]</sup> | サービスの作成、削除、スケーリング、VPN、カスタム ドメインの構成 | 以前のパブリッシャー ポータルへのアクセス | 説明
| ------------- | ---- | ---- | ---- | ---- | ---- 
| API Management Service Contributor | ✓ | ✓ | ✓ | ✓ | スーパー ユーザー。 API Management のサービスとエンティティ (API やポリシーなど) への完全な CRUD アクセス権がある。 以前のパブリッシャー ポータルにアクセスできる。 |
| API Management サービス リーダー | ✓ | | || API Management のサービスとエンティティへの読み取り専用アクセスがある。 |
| API Management サービス オペレーター | ✓ | | ✓ | | API Management サービスは管理できるが、エンティティの管理はできない。|
| API Management サービス エディター<sup>*</sup> | ✓ | ✓ | |  | API Management エンティティは管理できるが、サービスは管理できない。|
| API Management コンテンツ マネージャー<sup>*</sup> | ✓ | | | ✓ | 開発者ポータルを管理できる。 サービスおよびエンティティへの読み取り専用アクセス。|

<sup>[1] API Management サービスとエンティティ (API やポリシーなど) への読み取りアクセス</sup>

<sup>[2] 次の操作以外の API Management サービスとエンティティへの書き込みアクセス: インスタンスの作成、削除、およびスケーリング、VPN 構成、カスタム ドメインのセットアップ</sup>

<sup>\* 既存の発行者ポータルから Azure ポータルへすべての管理者 UI を移行すると、サービス エディターのロールが利用可能になります。コンテンツ マネージャー ロールは、発行者ポータルが開発者ポータルの管理に関連する特定の機能のみを含むようリファクタリングされた後、利用可能になります。</sup>  

## <a name="custom-roles"></a>カスタム ロール

特定のニーズを満たす組み込みロールがない場合は、カスタム ロールを作成して、API Management エンティティのアクセス管理をより詳細にすることができます。 たとえば、API Management サービスへの読み取り専用アクセス権はあるが、特定の 1 つの API にしか書き込みアクセス権がないカスタム ロールを作成できます。 カスタム ロールの詳細については、「[Azure RBAC のカスタム ロール](../role-based-access-control/custom-roles.md)」を参照してください。 

> [!NOTE]
> Azure portal で API Management インスタンスを表示できるようにするには、カスタム ロールに ```Microsoft.ApiManagement/service/read``` アクションを含める必要があります。

カスタム ロールを作成するときは、いずれかの組み込みロールを土台にすると簡単です。 **Actions**、**NotActions**、または **AssignableScopes** を追加して属性を編集した後、変更内容を新しいロールとして保存します。 次の例では、"API Management Service Reader (API Management サービスのリーダー)" ロールから、"Calculator API Editor (Calculator API の編集者)" という名前のカスタム ロールを作成します。 このカスタム ロールを、特定の API に割り当てることができます。 その結果、このロールは、その API にのみアクセスできます。 

```powershell
$role = Get-AzRoleDefinition "API Management Service Reader Role"
$role.Id = $null
$role.Name = 'Calculator API Contributor'
$role.Description = 'Has read access to Contoso APIM instance and write access to the Calculator API.'
$role.Actions.Add('Microsoft.ApiManagement/service/apis/write')
$role.Actions.Add('Microsoft.ApiManagement/service/apis/*/write')
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add('/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>')
New-AzRoleDefinition -Role $role
New-AzRoleAssignment -ObjectId <object ID of the user account> -RoleDefinitionName 'Calculator API Contributor' -Scope '/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>'
```

記事「[Azure Resource Manager のリソース プロバイダー操作](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement)」に、API Management レベルで許可できる権限の一覧が含まれています。

## <a name="video"></a>ビデオ


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
>
>

## <a name="next-steps"></a>次のステップ

Azure でのロールベースのアクセス制御の詳細については、次の記事を参照してください。
  * [Azure Portal でのアクセス管理の概要](../role-based-access-control/overview.md)
  * [Azure サブスクリプション リソースへのアクセスを管理するための Azure ロールの割り当て](../role-based-access-control/role-assignments-portal.md)
  * [Azure RBAC のカスタム ロール](../role-based-access-control/custom-roles.md)
  * [Azure Resource Manager のリソース プロバイダー操作](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement)
