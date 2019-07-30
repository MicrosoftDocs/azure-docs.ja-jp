---
title: Azure Advisor でのアクセス許可
description: Advisor のアクセス許可と、それを利用してサブスクリプションを構成する機能、推奨事項を延期または無視する機能をブロックする方法。
services: advisor
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 04/03/2019
ms.author: kasparks
ms.openlocfilehash: cbd2e456c96dbf8ca01387f0c7c17a1541dbfe55
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60467614"
---
# <a name="permissions-in-azure-advisor"></a>Azure Advisor でのアクセス許可

Azure Advisor は、Azure のリソースとサブスクリプションの使用状況と構成に基づいて推奨事項を提供します。 Advisor は[ロールベースのアクセス制御](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) が提供する[組み込みロール](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)を使用して、推奨事項および Advisor 機能へのアクセスを管理します。 

## <a name="roles-and-their-access"></a>ロールとそのアクセス権

次の表は、Advisor 内のロールとそのアクセス権の定義です。

| **ロール** | **推奨事項を表示する** | **ルールを編集する** | **サブスクリプションの構成を編集する** | **リソース グループの構成を編集する**| **推奨事項を無視して延期する**|
|---|:---:|:---:|:---:|:---:|:---:|
|サブスクリプションの所有者|**X**|**X**|**X**|**X**|**X**|
|サブスクリプションの共同作成者|**X**|**X**|**X**|**X**|**X**|
|サブスクリプション閲覧者|**X**|--|--|--|--|
|リソース グループの所有者|**X**|--|--|**X**|**X**|
|リソース グループの共同作成者|**X**|--|--|**X**|**X**|
|リソース グループ閲覧者|**X**|--|--|--|--|
|リソースの所有者|**X**|--|--|--|**X**|
|リソースの共同作成者|**X**|--|--|--|**X**|
|リソース閲覧者|**X**|--|--|--|--|

> [!NOTE]
> 推奨事項を表示するアクセス権は、推奨事項の影響を受けるリソースへのアクセス権によって変わります。

## <a name="permissions-and-unavailable-actions"></a>アクセス許可と使用できないアクション

適切なアクセス許可がない場合、Advisor でアクションを実行する機能がブロックされます。 一般的な問題の一部を次に示します。

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>サブスクリプションまたはリソース グループを構成できない

Advisor でサブスクリプションまたはリソース グループを構成しようとすると、含めるオプションまたは除外するオプションが無効な場合があります。 この状態は、そのリソース グループまたはサブスクリプションに対して十分なレベルのアクセス許可を持っていないことを示します。 この問題を解決するには、[ユーザーにアクセス権を付与する](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)方法を参照してください。

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>推奨事項を延期または無視できない

推奨事項を延期または無視しようとしてエラーが発生する場合は、十分なアクセス許可を持っていない可能性があります。 延期または無視している推奨事項の影響を受けるリソースに対して、少なくとも共同作成者アクセス権を持っていることを確認してください。 この問題を解決するには、[ユーザーにアクセス権を付与する](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)方法を参照してください。

## <a name="next-steps"></a>次の手順

この記事では、Advisor が RBAC を使用してユーザー アクセス許可を制御する方法と一般的な問題を解決する方法の概要を説明しました。 Advisor の詳細については、次を参照してください。

- [Azure Advisor とは](https://docs.microsoft.com/azure/advisor/advisor-overview)
- [Azure Advisor の使用を開始する](https://docs.microsoft.com/azure/advisor/advisor-get-started)
