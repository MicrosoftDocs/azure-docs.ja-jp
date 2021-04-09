---
title: 一般的なエラーのトラブルシューティング
description: Azure Resource Graph を使用して Azure リソースのクエリを実行する際に発生するさまざまな SDK に関する問題をトラブルシューティングする方法について説明します。
ms.date: 01/27/2021
ms.topic: troubleshooting
ms.openlocfilehash: 0d783b9cb4fa30e3c3e0ff82536b4878c29c7a0d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98916704"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>Azure Resource Graph 使用時のエラーのトラブルシューティング

Azure Resource Graph を使用して Azure リソースをクエリすると、エラーが発生することがあります。 この記事では、発生する可能性があるさまざまなエラーと、その解決方法について説明します。

## <a name="finding-error-details"></a>エラー詳細の検索

ほとんどのエラーは、Azure Resource Graph を使用したクエリ実行中の問題の結果です。 クエリが失敗すると、SDK は失敗したクエリの詳細を提供します。 この情報から問題が示されるので、問題を解決すれば、次回以降のクエリは成功します。

## <a name="general-errors"></a>一般エラー

### <a name="scenario-throttled-requests"></a><a name="throttled"></a>シナリオ:スロットルされた要求

#### <a name="issue"></a>問題

大量に、または頻繁にリソース クエリを実行するお客様により、要求がスロットルされています。

#### <a name="cause"></a>原因

Azure Resource Graph では、タイム ウィンドウに基づいて各ユーザーにクォータ数が割り当てられます。 たとえば、ユーザーは、5 秒間のウィンドウごとに最大 15 のクエリをスロットルなしで送信できます。 クォータ値は多数の要因によって決定され、変更される可能性があります。 詳細については、[Azure Resource Graph のスロットル](../overview.md#throttling)に関する記事をご覧ください。

#### <a name="resolution"></a>解決方法

スロットルされた要求を処理する方法はいくつかあります。

- [クエリのグループ化](../concepts/guidance-for-throttled-requests.md#grouping-queries)
- [クエリの時間差処理](../concepts/guidance-for-throttled-requests.md#staggering-queries)
- [並列クエリ](../concepts/guidance-for-throttled-requests.md#query-in-parallel)
- [改ページ位置の自動修正](../concepts/guidance-for-throttled-requests.md#pagination)

### <a name="scenario-too-many-subscriptions"></a><a name="toomanysubscription"></a>シナリオ:サブスクリプションが多すぎる

#### <a name="issue"></a>問題

[Azure Lighthouse](../../../lighthouse/overview.md) によるクロステナント サブスクリプションを含め、1,000 を超えるサブスクリプションにアクセスできるお客様は、Azure Resource Graph の 1 回の呼び出しですべてのサブスクリプションにわたってデータをフェッチできません。

#### <a name="cause"></a>原因

Azure CLI と PowerShell は、最初の 1,000 サブスクリプションのみを Azure Resource Graph に転送します。 Azure Resource Graph 用の REST API は、クエリを実行するために最大数のサブスクリプションを受け付けます。

#### <a name="resolution"></a>解決方法

1,000 サブスクリプションの制限を超えないよう、サブスクリプションのサブセットを使用してクエリ要求をバッチ化します。 ソリューションでは PowerShell の **Subscription** パラメーターを使用しています。

```azurepowershell-interactive
# Replace this query with your own
$query = 'Resources | project type'

# Fetch the full array of subscription IDs
$subscriptions = Get-AzSubscription
$subscriptionIds = $subscriptions.Id

# Create a counter, set the batch size, and prepare a variable for the results
$counter = [PSCustomObject] @{ Value = 0 }
$batchSize = 1000
$response = @()

# Group the subscriptions into batches
$subscriptionsBatch = $subscriptionIds | Group -Property { [math]::Floor($counter.Value++ / $batchSize) }

# Run the query for each batch
foreach ($batch in $subscriptionsBatch){ $response += Search-AzGraph -Query $query -Subscription $batch.Group }

# View the completed results of the query on all subscriptions
$response
```

### <a name="scenario-unsupported-content-type-rest-header"></a><a name="rest-contenttype"></a>シナリオ:サポートされていない Content-Type REST ヘッダー

#### <a name="issue"></a>問題

Azure Resource Graph REST API クエリを実行すると、_500_ (内部サーバー エラー) 応答が返される。

#### <a name="cause"></a>原因

Azure Resource Graph REST API では、**application/json** の `Content-Type` のみがサポートされます。 一部の REST ツールまたはエージェントは、既定で **text/plain** に設定されています。これは、REST API ではサポートされていません。

#### <a name="resolution"></a>解決方法

Azure Resource Graph のクエリに使用しているツールまたはエージェントの REST API ヘッダー `Content-Type` が **application/json** 用に構成されていることを検証します。

### <a name="scenario-no-read-permission-to-all-subscriptions-in-list"></a><a name="rest-403"></a>シナリオ:リスト内のすべてのサブスクリプションに対する読み取りアクセス許可がない

#### <a name="issue"></a>問題

Azure Resource Graph クエリを使用してサブスクリプションの一覧を明示的に渡すと、_403_ (禁止) の応答が返される。

#### <a name="cause"></a>原因

指定されたすべてのサブスクリプションに対する読み取りアクセス許可を持っていない場合は、ユーザーに適切なセキュリティ権限がないため、要求は拒否されます。

#### <a name="resolution"></a>解決方法

そのクエリを実行するユーザーが、少なくとも読み取りアクセス権を持っている 1 つ以上のサブスクリプションを、サブスクリプション一覧に含めます。 詳細については、「[Azure Resource Graph でのアクセス許可](../overview.md#permissions-in-azure-resource-graph)」を参照してください。

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

- [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
- [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる – Microsoft Azure 公式アカウントです。Azure コミュニティを適切なリソース (回答、サポート、エキスパート) に結び付けることで、カスタマー エクスペリエンスを向上します。
- さらにヘルプが必要であれば、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。