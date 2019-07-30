---
title: Azure Digital Twins のロールベースのアクセス制御を理解する | Microsoft Docs
description: Digital Twins でのロールベースのアクセス制御による認証について説明します。
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: lyhughes
ms.openlocfilehash: 33e09ad52722665e6162b18159012d69ec1463bd
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849268"
---
# <a name="role-based-access-control-in-azure-digital-twins"></a>Azure Digital Twins でのロール ベースのアクセス制御

Azure Digital Twins では、空間グラフ内の特定のデータ、リソース、アクションへのアクセスを正確に制御できます。 これは、"ロールベースのアクセス制御" (RBAC) と呼ばれるきめ細かいロールとアクセス許可の管理によって行われます。 RBAC は _ロール_ と _ロール割り当て_ により構成されます。 ロールによってアクセス許可のレベルが特定されます。 ロール割り当てによってロールがユーザーまたはデバイスに関連付けられます。

RBAC を使用し、次にアクセス許可を付与できます。

- ユーザー。
- デバイス。
- サービス プリンシパル。
- ユーザー定義関数。
- あるドメインに属するすべてのユーザー。
- テナント。

アクセスの程度を微調整することもできます。

RBAC は、アクセス許可が空間グラフの下方に継承されるという点で独特です。

## <a name="what-can-i-do-with-rbac"></a>RBAC でできること

開発者は次の目的で RBAC を使用することがあります。

- ビル全体のデバイスまたは特定の部屋やフロアだけのデバイスを管理する権限をユーザーに付与します。
- グラフ全体のすべての空間グラフ ノードまたはグラフのセクションのノードだけに対するグローバル アクセス権を、管理者に付与します。
- サポート スペシャ リストに、アクセス キーを除く、グラフへの読み取りアクセス権を付与します。
- ドメインのすべてのメンバーに、すべてのグラフ オブジェクトへの読み取りアクセス権を付与します。

## <a name="rbac-best-practices"></a>RBAC ベスト プラクティス

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>ロール

### <a name="role-definitions"></a>ロールの定義

ロールの定義は、アクセス許可と、ロールを構成するその他の属性のコレクションです。 ロールの定義では、そのロールを持つすべてのオブジェクトで実行される可能性のある*作成*、*読み取り*、*更新*、*削除*など、許可される操作がリストされます。 また、アクセス許可が適用されるオブジェクトの型も指定されます。

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

>[!NOTE]
> 前のロールの完全定義を取得するには、システム/ロール API にお問い合わせください。
> 詳細については、[ロール割り当ての作成と管理](./security-create-manage-role-assignments.md#all)に関するページを参照してください。

### <a name="object-identifier-types"></a>オブジェクト識別子の型

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

>[!TIP]
> サービス プリンシパルにアクセス許可を付与する方法については、[ロール割り当ての作成と管理](./security-create-manage-role-assignments.md#grant)に関するページを参照してください。

次のリファレンス ドキュメント記事では、以下について説明されています。

- [ユーザー用オブジェクト ID のクエリの実行または取得](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0)方法。
- [サービス プリンシパル用オブジェクト ID の取得](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal)方法。
- [Azure AD テナント用オブジェクト ID の取得](../active-directory/develop/quickstart-create-new-tenant.md)方法。

## <a name="role-assignments"></a>ロールの割り当て

Azure Digital Twins のロールの割り当てでは、ユーザー、Azure AD テナントなどのオブジェクトにロールや空間が関連付けられます。 その空間に属するすべてのオブジェクトにアクセス許可が付与されます。 空間には、その下にある空間グラフ全体が含まれます。

たとえば、建物を表す空間グラフのルート ノードに関して、あるユーザーにロール `DeviceInstaller` のロールの割り当てが与えられるとします。 この場合、このユーザーは、そのノードだけでなく、建物内の他のすべての子空間で、デバイスの読み取りと更新を行うことができます。

受信者にアクセス許可を付与するには、ロールの割り当てを作成します。 アクセス許可を取り消すには、ロールの割り当てを削除します。

>[!IMPORTANT]
> ロールの割り当ての詳細については、[ロール割り当ての作成と管理](./security-create-manage-role-assignments.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

- Azure Digital Twins ロールの割り当ての作成と管理の詳細については、[ロール割り当ての作成と管理](./security-create-manage-role-assignments.md)に関するページを参照してください。
