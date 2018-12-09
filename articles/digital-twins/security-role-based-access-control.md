---
title: Azure Digital Twins のロールベースのアクセス制御を理解する | Microsoft Docs
description: Digital Twins でのロールベースのアクセス制御による認証について説明します。
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: lyrana
ms.openlocfilehash: b9ccdb9030a24520be8f24f757c279241f3a07e1
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2018
ms.locfileid: "51014790"
---
# <a name="role-based-access-control"></a>ロールベースのアクセス制御

Azure Digital Twins では、空間グラフ内の特定のデータ、リソース、アクションへのアクセスを正確に制御できます。 これは、"ロールベースのアクセス制御" (RBAC) と呼ばれるきめ細かいロールとアクセス許可の管理によって行われます。 RBAC は _ロール_ と _ロール割り当て_ により構成されます。 ロールによってアクセス許可のレベルが特定されます。 ロール割り当てによってロールがユーザーまたはデバイスに関連付けられます。

RBAC を使用し、次にアクセス許可を付与できます。

- ユーザー。
- デバイス。
- サービス プリンシパル。
- ユーザー定義関数。 
- あるドメインに属するすべてのユーザー。 
- テナント。
 
アクセスの程度は微調整することもできます。

RBAC は、アクセス許可が空間グラフの下方に継承されるという点で独特です。

## <a name="what-can-i-do-with-rbac"></a>RBAC でできること

開発者は次の目的で RBAC を使用することがあります。

* ビル全体のデバイスまたは特定の部屋やフロアだけのデバイスを管理する権限をユーザーに付与します。
* グラフ全体のすべての空間グラフ ノードまたはグラフのセクションのノードだけに対するグローバル アクセス権を、管理者に付与します。
* サポート スペシャ リストに、アクセス キーを除く、グラフへの読み取りアクセス権を付与します。
* ドメインのすべてのメンバーに、すべてのグラフ オブジェクトへの読み取りアクセス権を付与します。

## <a name="rbac-best-practices"></a>RBAC ベスト プラクティス

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>ロール

### <a name="role-definitions"></a>ロールの定義

ロールの定義はアクセス許可の集合であり、ロールと呼ばれることもあります。 ロールの定義には、作成、読み取り、更新、削除など、許可される操作が列記されます。 また、これらのアクセス許可が適用されるオブジェクトの型も指定されます。

Azure Digital Twins では次のロールを使用できます。

* **スペース管理者**: 指定のスペースとその下にあるすべてのノードに対する作成、読み取り、更新、削除が許可されます。 グローバル アクセス許可。
* **ユーザー管理者**: ユーザーとユーザー関連オブジェクトに対する作成、読み取り、更新、削除が許可されます。 スペースに対する読み取りアクセス許可。
* **デバイス管理者**: デバイスとデバイス関連オブジェクトに対する作成、読み取り、更新、削除が許可されます。 スペースに対する読み取りアクセス許可。
* **キー管理者**: アクセス キーに対する作成、読み取り、更新、削除が許可されます。 スペースに対する読み取りアクセス許可。
* **トークン管理者**: アクセス キーに対する読み取りと更新が許可されます。 スペースに対する読み取りアクセス許可。
* **ユーザー**: スペース、センサー、ユーザー、それに対応する関連オブジェクトに対する読み取りが許可されます。
* **サポート スペシャ リスト**: アクセス キーを除くすべてのものの読み取りアクセス許可。
* **デバイス インストーラー**: デバイス、センサー、それに対応する関連オブジェクトに対する読み取りと更新が許可されます。 スペースに対する読み取りアクセス許可。
* **ゲートウェイ デバイス**: センサーの作成アクセス許可。 デバイス、センサー、それに対応する関連オブジェクトに対する読み取りが許可されます。

>[!NOTE]
> 前のロールの完全定義を取得するには、システム/ロール API にお問い合わせください。

### <a name="object-types"></a>オブジェクトの型

`ObjectIdType` は、ロールが与えられる ID の種類を示します。 `DeviceId` および `UserDefinedFunctionId` 型を除き、型は Azure Active Directory (Azure AD) オブジェクトのプロパティに対応します。
  
* `UserId` 型は、ユーザーにロールを割り当てます。
* `DeviceId` 型は、デバイスにロールを割り当てます。
* `DomainName` 型は、ドメイン名にロールを割り当てます。 指定されたドメイン名を持つ各ユーザーには、対応するロールのアクセス権が与えられます。
* `TenantId` 型は、テナントにロールを割り当てます。 指定された Azure AD テナント ID に属する各ユーザーには、対応するロールのアクセス権が与えられます。
* `ServicePrincipalId` 型は、サービス プリンシパルのオブジェクト ID にロールを割り当てます。
* `UserDefinedFunctionId` 型は、ユーザー定義関数 (UDF) にロールを割り当てます。

> [!div class="nextstepaction"]
> [ユーザーに対するクエリまたはオブジェクト ID](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0)

> [!div class="nextstepaction"]
> [サービス プリンシパルのオブジェクト ID を取得する](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.8.1)

> [!div class="nextstepaction"]
> [Azure AD テナントのオブジェクト ID を取得する](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)

## <a name="role-assignments"></a>ロールの割り当て

受信者にアクセス許可を付与するには、ロールの割り当てを作成します。 アクセス許可を取り消すには、ロールの割り当てを削除します。 Azure Digital Twins のロールの割り当てでは、ユーザー、Azure AD テナントなどのオブジェクトにロールや空間が関連付けられます。 その空間に属するすべてのオブジェクトにアクセス許可が付与されます。 空間には、その下にある空間グラフ全体が含まれます。

たとえば、建物を表す空間グラフのルート ノードに関して、あるユーザーにロール `DeviceInstaller` のロールの割り当てが与えられるとします。 この場合、このユーザーは、そのノードだけでなく、建物内の他のすべての子空間で、デバイスの読み取りと更新を行うことができます。

## <a name="next-steps"></a>次の手順

Azure Digital Twins のセキュリティについては、「[ロールの割り当てを作成および管理する](./security-create-manage-role-assignments.md)」をご覧ください。
