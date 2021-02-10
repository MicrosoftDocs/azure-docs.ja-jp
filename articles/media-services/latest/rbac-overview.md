---
title: Media Services アカウント用のロールベースのアクセス制御
description: この記事では、Azure Media Services アカウント用の Azure ロールベースのアクセス制御 (RBAC) について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 7c9edce71e0d0565de80153bfd5b3d5f1edb2e8f
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955158"
---
# <a name="azure-role-based-access-control-azure-rbac-for-media-services-accounts"></a>Media Services アカウント用の Azure ロールベースのアクセス制御 (Azure RBAC)

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

現在、Azure Media Services では、サービスに固有のカスタム ロールは定義されていません。 Media Services アカウントへのフル アクセスを取得する場合、お客様は、組み込みロールである **所有者** または **共同作成者** を使用します。 これらのロールの主な違いとして、**所有者** はリソースにアクセスできるユーザーを制御できますが、**共同作成者** はできません。 組み込みの **閲覧者** ロールも使用できますが、ユーザーまたはアプリケーションには Media Services API への読み取りアクセス許可が与えられるだけです。 

## <a name="design-principles"></a>設計原則

v3 API の主要な設計原則の 1 つは、API の安全性の向上です。 v3 API シリーズは、**Get** または **List** 操作でシークレットまたは資格情報を返しません。 キーは常に、null または空であるか、応答から削除されます。 ユーザーがシークレットまたは資格情報を取得するには、別のアクション メソッドを呼び出す必要があります。 **閲覧者** ロールでは、Asset.ListContainerSas、StreamingLocator.ListContentKeys、ContentKeyPolicies.GetPolicyPropertiesWithSecrets などの操作を呼び出すことはできません。 別々のアクションを使用することで、必要に応じてカスタム ロールにより詳細な Azure RBAC セキュリティ アクセス許可を設定できます。

Media Services でサポートされている操作を一覧表示するには、次を実行します。

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

[組み込みロールの定義](../../role-based-access-control/built-in-roles.md)に関する記事では、ロールで許可されている内容が詳しく説明されています。 

詳細については、次の記事を参照してください。

- [従来のサブスクリプション管理者ロール、Azure ロール、および Azure AD ロール](../../role-based-access-control/rbac-and-directory-admin-roles.md)
- [Azure ロールベースのアクセス制御 (Azure RBAC) とは](../../role-based-access-control/overview.md)
- [REST API を使用して Azure ロールの割り当てを追加または削除する](../../role-based-access-control/role-assignments-rest.md)
- [Media Services リソース プロバイダー操作](../../role-based-access-control/resource-provider-operations.md#microsoftmedia)

## <a name="next-steps"></a>次のステップ

- [Media Services v3 API シリーズを使用した開発](media-services-apis-overview.md)
- [Media Services .NET を使用してコンテンツ キー ポリシーを取得する](get-content-key-policy-dotnet-howto.md)
