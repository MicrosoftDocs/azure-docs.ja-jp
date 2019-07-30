---
title: Azure Resource Graph の概要
description: Azure Resource Graph サービスによってリソースの複雑なクエリの大規模な実行がどのように実現されるかについて理解します。
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/06/2019
ms.topic: overview
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: d78c640f4269c799d3d371e6dd9db477faf96694
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807404"
---
# <a name="overview-of-the-azure-resource-graph-service"></a>Azure Resource Graph サービスの概要

Azure Resource Graph は Azure 内のサービスであり、Azure Resource Management を拡張するよう設計されています。環境を効果的に管理できるように、特定のセットのサブスクリプションにわたって大規模にクエリを実行する機能を備えた、高効率および高性能のリソース探索を提供します。 これらのクエリでは、次の機能を提供します。

- 複雑なフィルター処理、グループ化、およびリソースのプロパティでの並べ替えによりリソースのクエリを行う機能。
- ガバナンスの要件に基づいてリソースを繰り返し探索する機能。
- 広大なクラウド環境にポリシーを適用することの影響を評価する機能。
- [リソースのプロパティに加えられた変更について詳しく説明する](./how-to/get-resource-changes.md)機能 (プレビュー)。

このドキュメントでは、それぞれの機能について詳しく見ていきます。

> [!NOTE]
> Azure Resource Graph は、Azure portal の検索バー、新しい "すべてのリソース" 参照エクスペリエンス、Azure Policy の[変更履歴](../policy/how-to/determine-non-compliance.md#change-history-preview)
> の "_差分表示_" を強化します。大規模な環境を管理するお客様をサポートするために設計されています。

[!INCLUDE [service-provider-management-toolkit](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-does-resource-graph-complement-azure-resource-manager"></a>Resource Graph が Azure Resource Manager をどのように補完するか

Azure Resource Manager では現在、基本的なリソース フィールドに対するクエリがサポートされています。具体的には、リソース名、ID、種類、リソース グループ、サブスクリプション、および場所です。 Resource Manager には、一度に 1 つのリソースについて詳細なプロパティを得るために個別のリソース プロバイダーを呼び出す機能も用意されています。

Azure Resource Graph を使用することにより、各リソースプロバイダーへの個別の呼び出しを行う必要なく、リソースプロバイダーが返すこれらのプロパティにアクセスすることができます。 サポートされるリソースの種類については、[完全モード デプロイでのリソース](../../azure-resource-manager/complete-mode-deletion.md)に関する表で "**はい**" を探してください。

Azure Resource Graph では、次のことができます。

- 各リソース プロバイダーへの個別の呼び出しを行う必要なく、リソース プロバイダーから返されるプロパティにアクセスする。
- リソースに加えられた変更の過去 14 日間分の履歴を表示して、どのプロパティがいつ変更されたかを確認する。 (プレビュー)

## <a name="how-resource-graph-is-kept-current"></a>Resource Graph が最新の状態に保たれるしくみ

Azure リソースが更新されると、Resource Manager から Resource Graph に変更の通知が届きます。
その後、Resource Graph によってそのデータベースが更新されます。 Resource Graph では、定期的な "_フル スキャン_" も行われます。 このスキャンにより、通知が届かなかった場合、またはリソースが Resource Manager の外部で更新されたときにも、Resource Graph が最新の状態に維持されます。

## <a name="the-query-language"></a>クエリ言語

これで Azure Resource Graph についてはよく理解したので、クエリを作成する方法に進みましょう。

Azure Resource Graph のクエリ言語が Azure Data Explorer で使用される [Kusto クエリ言語](../../data-explorer/data-explorer-overview.md)に基づくことを理解することが重要です。

最初に、Azure Resource Graph と共に使用することができる操作および機能の詳細については、[Resource Graph のクエリ言語](./concepts/query-language.md)を参照してください。
リソースをブラウズするためには、[リソースを探索する](./concepts/explore-resources.md)を参照してください。

## <a name="permissions-in-azure-resource-graph"></a>Azure Resource Graph でのアクセス許可

Resource Graph を使用するためには、最低限、照会したいリソースに読み取りアクセスできる適切な権限が、[ロール ベースのアクセス制御](../../role-based-access-control/overview.md) (RBAC) を通じて付与される必要があります。 Azure のオブジェクトまたはオブジェクト グループに対する `read` 以上のアクセス許可がないと、結果は返されません。

> [!NOTE]
> Resource Graph では、プリンシパルがログイン中に利用できるサブスクリプションが使用されます。 アクティブなセッション中に追加された新しいサブスクリプションのリソースを表示するには、プリンシパルがコンテキストを更新する必要があります。 ログアウトしてから再度ログインすると、このアクションが自動的に実行されます。

## <a name="throttling"></a>Throttling

無料サービスとして、すべてのお客様に最適なエクスペリエンスと応答時間が提供されるよう、Resource Graph へのクエリはスロットルされます。 お客様の組織が大規模かつ頻繁なクエリに Resource Graph API を使用したい場合、[Resource Graph ポータル ページ](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/ResourceGraph)からポータルの "フィードバック" を使用してください。
ビジネス ケースを明記し、チームが連絡できるように [Microsoft からフィードバックについてメールをお送りする場合があります] チェック ボックスをオンにしてください。

Resource Graph では、ユーザー レベルでクエリのスロットルが行われます。 サービスの応答には、次の HTTP ヘッダーが含まれています。

- `x-ms-user-quota-remaining` (int):ユーザーの残りリソース クォータ。 この値はクエリ カウントにマップされます。
- `x-ms-user-quota-resets-after` (hh:mm:ss):ユーザーのクォータ消費量がリセットされるまでの期間

詳細については、[調整された要求に関するガイダンス](./concepts/guidance-for-throttled-requests.md)のページを参照してください。

## <a name="running-your-first-query"></a>最初のクエリを送信する

Resource Graph は、Azure CLI、Azure PowerShell、および Azure SDK for .NET をサポートします。 どの言語も、クエリの構造は同じです。 [Azure CLI ](first-query-azurecli.md#add-the-resource-graph-extension)および[ Azure PowerShell ](first-query-powershell.md#add-the-resource-graph-module)において、Resource Graph を有効にする方法について説明します。

## <a name="next-steps"></a>次の手順

- [Azure CLI](first-query-azurecli.md) を使用して最初のクエリを実行します。
- [Azure PowerShell](first-query-powershell.md) を使用して最初のクエリを実行します。
- [初歩的なクエリ](./samples/starter.md)から開始します。
- [高度なクエリ](./samples/advanced.md)について理解を深めます。