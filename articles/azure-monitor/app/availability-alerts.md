---
title: Azure Application Insights を使用して可用性アラートを設定する | Microsoft Docs
description: Application Insights で Web テストを設定する方法について説明します。 Web サイトが使用できなくなったり、応答速度が低下したりした場合に、アラートを受け取ります。
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: cbafa7997d203cf06a3e91965355258f0088d77e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100589881"
---
# <a name="availability-alerts"></a>可用性のアラート

[ Application Insights](./app-insights-overview.md) は、世界各地の複数のポイントから定期的にアプリケーションに Web 要求を送信します。 お使いのアプリケーションが応答していない場合、または応答が遅すぎる場合は、アラートを受信できます。

## <a name="enable-alerts"></a>アラートを有効にする

現在、アラートは既定で有効になっていますが、アラートを完全に構成するには、可用性テストを先に作成する必要があります。

![エクスペリエンスを作成する](./media/availability-alerts/create-test.png)

> [!NOTE]
>  [新しい統合アラート](../alerts/alerts-overview.md)の場合、アラート ルールの重大度と通知の基本設定 [をアクション グループ](../alerts/action-groups.md)と一緒に、アラート エクスペリエンスで構成する **必要があります**。 次の手順を行わないと、ポータル内通知を受け取るだけとなります。

1. 可用性テストを保存した後、詳細タブで、作成したテストの省略記号をクリックします。 [アラートの編集] をクリックします。

   ![メニューで [アラートの編集] が選択されていることを示すスクリーンショット。](./media/availability-alerts/edit-alert.png)

2. 目的の重大度レベルとルールの説明を設定し、次に、最も重要なものとして、このアラート ルールに使用する、通知の基本設定が含まれているアクション グループを設定します。

   ![[ルールの管理] ページを示すスクリーンショット。ここでは、ルールを編集できます。](./media/availability-alerts/set-action-group.png)

> [!NOTE]
> このエクスペリエンスによって作成される可用性アラートは、状態に基づきます。 つまり、アラートの条件が満たされると、サイトが使用不可として検出された場合に 1 つのアラートが生成されます。 次にアラートの条件が評価されたときに、サイトがまだ停止している場合、新しいアラートは生成されません。 そのため、サイトが 1 時間前から停止しており、電子メール アラートを設定済みの場合は、サイトが停止した時点で 1 つの電子メールを、また、サイトがバックアップされた時点でそれに続くもう 1 つの電子メールを受信するだけになります。 サイトがまだ使用不可であることを知らせる継続的なアラートは受信しません。

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>Y 箇所中 X 箇所に関するアラートでエラーが報告されている

新しい可用性テストを作成する場合、"Y 箇所中 X 箇所" アラート ルールは既定では[新しい統合アラート エクスペリエンス](../alerts/alerts-overview.md)で有効にされます。 オプトアウトするには、"クラシック" オプションを選択するか、またはアラート ルールを無効にすることを選択します。

> [!NOTE]
> 上記の手順に従って、アラートがトリガーされたときに通知を受信するようにアクション グループを構成します。 この手順を行わないと、ルールがトリガーされたとき、ポータル内通知を受け取るだけとなります。
>

### <a name="alert-on-availability-metrics"></a>可用性メトリックに関するアラート

[新しい統合アラート](../alerts/alerts-overview.md)を使用すると、セグメント化された可用性の集計メトリックおよびテスト継続期間メトリックに関するアラートを生成することもできます。

1. メトリック エクスペリエンス内で Application Insights リソースを選択し、可用性メトリックを選択します。

    ![可用性メトリックの選択](./media/availability-alerts/select-metric.png)

2. メニューからアラート オプションを選択すると、新しいエクスペリエンスが表示されます。そこでは、アラート ルールの設定対象とする特定のテストまたは場所を選択することができます。 ここでは、このアラート ルールに対してアクション グループを構成することもできます。

### <a name="alert-on-custom-analytics-queries"></a>カスタム分析クエリに関するアラート

[新しい統合アラート](../alerts/alerts-overview.md)を使用すると、[カスタム ログ クエリ](../alerts/alerts-unified-log.md)に関するアラートを生成することができます。 カスタム クエリを使用すると、可用性の問題を示す最も確かなシグナルを取得するのに役立つ任意の条件に基づいてアラートを生成することができます。 また、これは TrackAvailability SDK を使用してカスタムの可用性の結果を送信する場合にも当てはまります。

> [!Tip]
> 可用性データに関するメトリックにはカスタムの可用性の結果が含まれます。これは、Microsoft の TrackAvailability SDK を呼び出すことによって送信することができます。 メトリックに関するアラートのサポートを使用することにより、カスタムの可用性の結果に関するアラートを生成することができます。
>

## <a name="automate-alerts"></a>アラートを自動化する

Azure Resource Manager テンプレートを使用してこのプロセスを自動化する方法については、[Resource Manager テンプレートを使用したメトリック アラートの作成](../alerts/alerts-metric-create-templates.md#template-for-an-availability-test-along-with-a-metric-alert)に関するドキュメントを参照してください。

## <a name="troubleshooting"></a>トラブルシューティング

専用の[トラブルシューティングに関する記事](troubleshoot-availability.md)をご覧ください。

## <a name="next-steps"></a>次のステップ

* [複数ステップ Web テスト](availability-multistep.md)
* [URL ping Web テスト](monitor-web-app-availability.md)

