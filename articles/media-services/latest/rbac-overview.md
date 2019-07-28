---
title: Media Services アカウント用のロールベースのアクセス制御 - Azure | Microsoft Docs
description: この記事では、Azure Media Services アカウント用のロールベースのアクセス制御 (RBAC) について説明します。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/23/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 93b2cd3a2565b14ea07d6db6b14dd146e4223528
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66236915"
---
# <a name="role-based-access-control-rbac-for-media-services-accounts"></a>Media Services アカウント用のロールベースのアクセス制御 (RBAC)

現在、Azure Media Services では、サービスに固有のカスタム ロールは定義されていません。 Media Services アカウントへのフル アクセスを取得する場合、お客様は、組み込みロールである**所有者**または**共同作成者**を使用します。 これらのロールの主な違いとして、**所有者**はリソースにアクセスできるユーザーを制御できますが、**共同作成者**はできません。 組み込みの**閲覧者**ロールも使用できますが、ユーザーまたはアプリケーションには Media Services API への読み取りアクセス許可が与えられるだけです。 

## <a name="design-principles"></a>設計原則

v3 API の主要な設計原則の 1 つは、API の安全性の向上です。 v3 API シリーズは、**Get** または **List** 操作でシークレットまたは資格情報を返しません。 キーは常に、null または空であるか、応答から削除されます。 ユーザーがシークレットまたは資格情報を取得するには、別のアクション メソッドを呼び出す必要があります。 **閲覧者**ロールでは、Asset.ListContainerSas、StreamingLocator.ListContentKeys、ContentKeyPolicies.GetPolicyPropertiesWithSecrets などの操作を呼び出すことはできません。 別々のアクションを使用することで、必要に応じてカスタム ロールに、よりきめ細かな RBAC セキュリティ アクセス許可を設定できます。

Media Services でサポートされている操作を一覧表示するには、次を実行します。

```csharp
foreach (Microsoft.Azure.Management.Media.Models.Operation a in client.Operations.List())
{
    Console.WriteLine($"{a.Name} - {a.Display.Operation} - {a.Display.Description}");
}
```

[組み込みロールの定義](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)に関する記事では、ロールで許可されている内容が詳しく説明されています。 

詳細については、次の記事を参照してください。

- [従来のサブスクリプション管理者ロール、Azure RBAC ロール、および Azure AD 管理者ロール](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)
- [Azure リソース用の RBAC とは?](https://docs.microsoft.com/azure/role-based-access-control/overview)
- [RBAC を使用してアクセスを管理する](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest)
- [Media Services リソース プロバイダー操作](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftmedia)

## <a name="next-steps"></a>次の手順

- [Media Services v3 API シリーズを使用した開発](media-services-apis-overview.md)
- [Media Services .NET を使用してコンテンツ キー ポリシーを取得する](get-content-key-policy-dotnet-howto.md)
