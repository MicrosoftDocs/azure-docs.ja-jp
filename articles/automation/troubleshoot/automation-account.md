---
title: Azure Automation アカウントに関する問題のトラブルシューティング
description: この記事では、Azure アカウントに関する問題のトラブルシューティングと解決方法について説明します。
services: automation
ms.subservice: ''
ms.date: 03/24/2020
ms.topic: troubleshooting
ms.openlocfilehash: 06c15136e9d2fabdf50031c8b4be455cf2f7bbca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896581"
---
# <a name="troubleshoot-azure-automation-account-issues"></a>Azure Automation アカウントに関する問題のトラブルシューティング

この記事では、Azure Automation アカウントを使用する際に発生する可能性がある問題の解決方法について説明します。 Automation アカウントに関する一般的な情報については、[Azure Automation アカウントの認証の概要](../automation-security-overview.md)に関する記事をご覧ください。

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>シナリオ:サブスクリプションで Automation リソース プロバイダーを登録できない

### <a name="issue"></a>問題

Automation アカウントで Update Management などの管理機能を使用すると、次のエラーが発生します。

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>原因

Automation リソース プロバイダーがサブスクリプションに登録されていません。

### <a name="resolution"></a>解像度

Automation リソース プロバイダーを登録するには、Azure portal で次の手順に従います。

1. お使いのブラウザーで [Azure portal](https://portal.azure.com) に移動します。

2. **[サブスクリプション]** に移動し、ご使用のサブスクリプションを選択します。   

3. **[設定]** で、 **[リソース プロバイダー]** を選択します。

4. リソース プロバイダーの一覧で、**Microsoft.Automation** リソース プロバイダーが登録されていることを確認します。

5. プロバイダーが一覧に表示されていない場合は、「[リソース プロバイダーの登録エラーの解決](../../azure-resource-manager/templates/error-register-resource-provider.md)」に記載の手順に従ってそれを登録します。

## <a name="next-steps"></a>次のステップ

この記事で問題を解決できない場合は、追加のサポートを得るために、次のいずれかのチャネルをお試しください。

* [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる。 これは、Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐための、Microsoft Azure の公式アカウントです。
* Azure サポート インシデントを送信する。 [Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、 **[サポートを受ける]** を選択します。
