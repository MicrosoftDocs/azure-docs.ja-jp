---
title: アプリケーション変更分析のトラブルシューティング - Azure Monitor
description: アプリケーション変更分析で問題をトラブルシューティングする方法について学習します。
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 02/11/2021
ms.openlocfilehash: 7200978ce31a47f7bd0d023cecf359b10a1c96de
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100520827"
---
# <a name="troubleshoot-application-change-analysis-preview"></a>アプリケーション変更分析のトラブルシューティング (プレビュー)

## <a name="having-trouble-registering-microsoft-change-analysis-resource-provider-from-change-history-tab"></a>[変更履歴] タブからの Microsoft. Change Analysis リソース プロバイダーの登録で問題が発生している

アプリケーション変更分析との統合後、変更履歴を初めて表示すると、リソース プロバイダー **Microsoft.ChangeAnalysis** が自動的に登録されることがわかります。 まれに、次のような理由でこれが失敗する場合があります。

- **Microsoft.ChangeAnalysis リソース プロバイダーを登録するための十分なアクセス許可がありません**。 このエラー メッセージは、現在のサブスクリプションのロールに、関連付けられている **Microsoft.Support/register/action** スコープがないことを意味します。 これは、サブスクリプションの所有者ではなく、同僚を通じて共有アクセス許可 (つまり、リソース グループへのビュー アクセス) を取得した場合に発生する可能性があります。 これを解決するには、サブスクリプションの所有者に問い合わせて、**Microsoft.ChangeAnalysis** リソース プロバイダーを登録します。 これは、Azure portal の **サブスクリプション | リソース プロバイダー** から行うことができ、```Microsoft.ChangeAnalysis``` を検索し、UI で、または Azure PowerShell や Azure CLI を使用して登録します。

    PowerShell を使用してリソース プロバイダーを登録します。
    ```PowerShell
    # Register resource provider
    Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
    ```

- **Microsoft.ChangeAnalysis リソース プロバイダーを登録できませんでした**。 このメッセージは、UI によってリソース プロバイダーの登録の要求が送信された直後に何かが失敗し、それがアクセス許可の問題に関係していないことを示しています。 一時的なインターネット接続の問題である可能性があります。 ページを更新し、インターネット接続を確認してみてください。 エラーが解決しない場合は、changeanalysishelp@microsoft.com にお問い合わせください

- **予想以上に時間がかかっています**。 このメッセージは、登録に 2 分以上かかっていることを示しています。 これはめったにありませんが、必ずしも問題が発生したことを示しているわけではありません。 **サブスクリプション | リソース プロバイダー** に移動して、**Microsoft.ChangeAnalysis** リソースプロバイダーの登録状態を確認できます。 UI を使用して登録を解除し、再登録または更新してみて、それが役立つかどうかを確認できます。 問題が解決しない場合は、changeanalysishelp@microsoft.com にサポートについてお問い合わせください。
    ![時間がかかりすぎる RP 登録のトラブルシューティング](./media/change-analysis/troubleshoot-registration-taking-too-long.png)

![[トラブルシューティング ツール] が選択されている、仮想マシンの [問題の診断と解決] ツールのスクリーンショット。](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![仮想マシンの [最近の変更の分析] トラブルシューティング ツールのタイルのスクリーンショット。](./media/change-analysis/analyze-recent-changes.png)

## <a name="azure-lighthouse-subscription-is-not-supported"></a>Azure Lighthouse サブスクリプションはサポートされていません

- **Microsoft ChangeAnalysis リソース プロバイダーの照会に失敗し**、「*Azure lighthouse サブスクリプションはサポートされていません。変更はサブスクリプションのホーム テナントでのみ行えます*」というようなメッセージを受け取ります。 変更分析リソース プロバイダーの登録に現時点で制限があり、ホーム テナントにないユーザーの Azure Lighthouse サブスクリプションを介して行うことができません。 この制限については、解決に取り組んでいる最中です。 これが障害となっている問題である場合、サービス プリンシパルを作成し、アクセスを許可するためのロールを明示的に割り当てることによって回避できます。  詳細については、changeanalysishelp@microsoft.com にお問い合わせください。

## <a name="an-error-occurred-while-getting-changes-please-refresh-this-page-or-come-back-later-to-view-changes"></a>変更の取得中にエラーが発生しました。 このページを最新の情報に更新するか、後で戻って変更内容を表示してください

これは、変更を読み込めなかったときにアプリケーション変更分析サービスによって表示される一般的なエラー メッセージです。 いくつかの既知の原因は次のとおりです。

- クライアント デバイスからのインターネット接続エラー
- 変更分析サービスが一時的に利用できないため、通常は、数分後にページを最新の情報に更新すると、この問題は解決されます。 エラーが解決しない場合は、changeanalysishelp@micorosoft.com にお問い合わせください

## <a name="you-dont-have-enough-permissions-to-view-some-changes-contact-your-azure-subscription-administrator"></a>一部の変更を表示するのに十分なアクセス許可がありません。 Azure サブスクリプション管理者に連絡してください

これは、現在のユーザーが変更を表示するのに十分なアクセス許可を持っていないことを示す一般的な未承認エラー メッセージです。 Azure Resource Graph と Azure Resource Manager によって返されるインフラストラクチャの変更を表示するには、少なくともリソースに対する閲覧者アクセス権が必要です。 Web アプリのゲスト内のファイルの変更と構成の変更については、少なくとも共同作成者ロールが必要です。

## <a name="failed-to-register-microsoftchangeanalysis-resource-provider"></a>Microsoft.ChangeAnalysis リソース プロバイダーを登録できませんでした

このメッセージは、UI によってリソース プロバイダーの登録の要求が送信された直後に何かが失敗し、それがアクセス許可の問題に関係していないことを示しています。 一時的なインターネット接続の問題である可能性があります。 ページを更新し、インターネット接続を確認してみてください。 エラーが解決しない場合は、changeanalysishelp@microsoft.com にお問い合わせください

## <a name="you-dont-have-enough-permissions-to-register-microsoftchangeanalysis-resource-provider-contact-your-azure-subscription-administrator"></a>Microsoft.ChangeAnalysis リソース プロバイダーを登録するための十分なアクセス許可がありません。 Azure サブスクリプション管理者に連絡してください

このエラー メッセージは、現在のサブスクリプションのロールに、関連付けられている **Microsoft.Support/register/action** スコープがないことを意味します。 これは、サブスクリプションの所有者ではなく、同僚を通じて共有アクセス許可 (つまり、リソース グループへのビュー アクセス) を取得した場合に発生する可能性があります。 これを解決するには、サブスクリプションの所有者に問い合わせて、**Microsoft.ChangeAnalysis** リソース プロバイダーを登録します。 これは、Azure portal の **サブスクリプション | リソース プロバイダー** から行うことができ、```Microsoft.ChangeAnalysis``` を検索し、UI で、または Azure PowerShell や Azure CLI を使用して登録します。

PowerShell を使用してリソース プロバイダーを登録します。

```PowerShell
# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
```

## <a name="next-steps"></a>次のステップ

- [Azure Resource Graph](../../governance/resource-graph/overview.md) の詳細を参照してください。変更分析の強化に役立ちます。