---
title: App Service インスタンスの正常性の監視
description: 正常性チェックを使用して App Service インスタンスの正常性を監視する方法について説明します。
keywords: Azure App Service, Web アプリ, 正常性チェック, トラフィックのルーティング, 正常なインスタンス, パス, 監視,
author: msangapu-msft
ms.topic: article
ms.date: 12/03/2020
ms.author: msangapu
ms.openlocfilehash: 60a210c6c336c1b820015304e8ab53bc894c17bf
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/26/2021
ms.locfileid: "98792466"
---
# <a name="monitor-app-service-instances-using-health-check"></a>正常性チェックを使用して App Service インスタンスを監視する

![正常性チェックのエラー][2]

この記事では、Azure portal の正常性チェックを使用して App Service インスタンスを監視します。 正常性チェックにより、問題のあるインスタンスが削除され、アプリケーションの可用性が向上します。 正常性チェックを使用するには、[App Service プラン](/overview-hosting-plans)を 2 つ以上のインスタンスにスケーリングする必要があります。 正常性チェック パスによって、ご利用のアプリケーションの重要なコンポーネントが確認されます。 たとえば、ご利用のアプリケーションがデータベースとメッセージング システムに依存している場合、正常性チェックのエンドポイントはそれらのコンポーネントに接続する必要があります。 アプリケーションが重要なコンポーネントに接続できない場合は、アプリケーションが異常であることを示す 500 レベルの応答コードがパスから返されます。

## <a name="what-app-service-does-with-health-checks"></a>App Service で正常性チェックを使用した処理

- アプリでパスを指定すると、正常性チェックによって、App Service アプリのすべてのインスタンスで 1 分間隔でこのパスに対する ping が実行されます。
- インスタンスが 2 つ以上の要求の後に 200 ～ 299 (200 と 299 を含む) の状態コードで応答しない場合、または ping への応答に失敗した場合は、システムによって異常があると判断され、削除されます。
- 削除後、正常性チェックによる異常なインスタンスへの ping が継続されます。 引き続き正常に応答しない場合、インスタンスを正常な状態に戻すために、基になる VM が App Service によって再起動されます。
- インスタンスが 1 時間、異常のままである場合、それは新しいインスタンスに置き換えられます。
- さらに、スケール アップまたはスケール アウトする場合は、新しいインスタンスの準備ができていることを保証するために、App Service によって正常性チェック パスに対して ping が実行されます。

> [!NOTE]
> 正常性チェックは 302 リダイレクトには従いません。 App Service プランによれば、1 時間あたり最大で 1 つのインスタンス、1 日あたり最大で 3 つのインスタンスが置き換えられます。
>

## <a name="enable-health-check"></a>正常性チェックを有効にする

![Azure portal での正常性チェックのナビゲーション][3]

- 正常性チェックを有効にするには、Azure portal にアクセスし、App Service アプリを選択します。
- **[監視]** で **[正常性チェック]** を選択します。
- **[有効]** を選択し、`/health` や `/api/health` など、ご利用のアプリケーションの有効な URL パスを指定します。
- **[保存]** をクリックします。

> [!CAUTION]
> 正常性チェックの構成変更によって、アプリが再起動します。 運用環境のアプリへの影響を最小限に抑えるには、[ステージング スロットを構成し](deploy-staging-slots.md)、運用環境にスワップすることをお勧めします。
>

### <a name="configuration"></a>構成

正常性チェックのオプションを構成するだけでなく、次の[アプリ設定](configure-common.md)を構成することもできます。

| アプリ設定の名前 | 使用できる値 | 説明 |
|-|-|-|
|`WEBSITE_HEALTHCHECK_MAXPINGFAILURES` | 2 ～ 10 | ping エラーの最大数。 たとえば、`2` に設定すると、ping に `2` 回失敗した後にインスタンスが削除されます。 さらに、スケール アップまたはスケール アウトする場合は、新しいインスタンスの準備ができていることを保証するために、App Service によって正常性チェック パスに対して ping が実行されます。 |
|`WEBSITE_HEALTHCHECK_MAXUNHEALTYWORKERPERCENT` | 0 ～ 100 | 正常なインスタンスが過負荷にならないように、ご利用のインスタンスの半分以下が除外されます。 たとえば、App Service プランが 4 つのインスタンスにスケーリングされ、3 つに異常が発生した場合、最大 2 つが除外されます。 他の 2 つのインスタンス (1 つは正常、1 つは異常) は、引き続き要求を受信することになります。 すべてのインスタンスが異常であるという最悪のシナリオでは、何も除外されません。 この動作をオーバーライドするには、アプリ設定を `0` ～ `100` の値に設定します。 これを大きな値に設定すると、異常なインスタンスがさらに多く削除されます (既定は 50)。 |

#### <a name="authentication-and-security"></a>認証とセキュリティ

正常性チェックは App Service の認証および認可機能と統合されます。 これらのセキュリティ機能が有効になっている場合、追加の設定は必要ありません。 ただし、独自の認証システムを使用する場合は、正常性チェック パスによって匿名アクセスが許可される必要があります。 サイトで HTTP **S** のみが有効になっている場合、正常性チェック要求が HTTP **S** 経由で送信されます。

大企業の開発チームは、多くの場合、公開されている API のセキュリティ要件に従う必要があります。 正常性チェック エンドポイントをセキュリティで保護するには、まず [IP 制限](app-service-ip-restrictions.md#set-an-ip-address-based-rule)、[クライアント証明書](app-service-ip-restrictions.md#set-an-ip-address-based-rule)、Virtual Network などの機能を使用して、アプリケーション アクセスを制限する必要があります。 着信要求の `User-Agent` が `ReadyForRequest/1.0` と一致するように要求することで、正常性チェック エンドポイントをセキュリティで保護できます。 以前のセキュリティ機能によって要求が既にセキュリティで保護されているため、User-Agent になりすますことはできません。

## <a name="monitoring"></a>監視

アプリケーションの正常性チェック パスを指定したら、Azure Monitor を使用してご利用のサイトの正常性を監視できます。 ポータルの **[正常性チェック]** ブレードで、上部のツールバーにある **[メトリック]** をクリックします。 これにより新しいブレードが開き、サイトの過去の正常性状態を確認したり、新しいアラート ルールを作成したりできるようになります。 サイトの監視方法の詳細については、[Azure Monitor に関するガイドを参照してください](web-sites-monitor.md)。

## <a name="next-steps"></a>次のステップ
- [アクティビティ ログ アラートを作成して、サブスクリプションで自動スケールのエンジン操作をすべて監視する](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [アクティビティ ログ アラートを作成して、サブスクリプションで失敗した自動スケールのスケールイン/スケールアウト操作をすべて監視する](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

[1]: ./media/app-service-monitor-instances-health-check/health-check-success-diagram.png
[2]: ./media/app-service-monitor-instances-health-check/health-check-failure-diagram.png
[3]: ./media/app-service-monitor-instances-health-check/azure-portal-navigation-health-check.png
