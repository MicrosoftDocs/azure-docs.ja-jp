---
title: Application Gateway の高トラフィック量のサポート
description: この記事では、ネットワーク トラフィック量の多いシナリオをサポートするために Azure Application Gateway を構成するためのガイダンスを提供します。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: d8940d791920daca6ef0af186a4bb5e17009637b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100586106"
---
# <a name="application-gateway-high-traffic-support"></a>Application Gateway の高トラフィックのサポート

>[!NOTE]
> この記事では、大量トラフィックが発生した場合に備えて追加トラフィックを処理するように Application Gateway を設定する際に役立ついくつかの推奨ガイドラインについて説明します。 アラートのしきい値は、単なる提案であり、本質的には汎用です。 ユーザーは、ワークロードと使用率の予測に基づいてアラートのしきい値を決定できます。

Application Gateway と Web アプリケーション ファイアウォール (WAF) を使用すると、Web アプリケーションへのトラフィックをスケーラブルで安全な方法で管理できます。

実際のトラフィックに従い、若干のバッファーを追加して Application Gateway をスケーリングすることが重要です。それによって、トラフィックの急増やスパイクに備え、QoS に対する影響を最小限に抑えることができます。 以下の推奨事項は、追加のトラフィックを処理するために Application Gateway と WAF を設定する場合に役立ちます。

Application Gateway で提供されているメトリックの一覧については、[メトリックに関するドキュメント](./application-gateway-metrics.md)を確認してください。 メトリックのアラートを設定する方法については、Azure portal での[メトリックの視覚化](./application-gateway-metrics.md#metrics-visualization)に関する記事と、[Azure Monitor に関するドキュメント](../azure-monitor/alerts/alerts-metric.md)を参照してください。

## <a name="scaling-for-application-gateway-v1-sku-standardwaf-sku"></a>Application Gateway v1 SKU (Standard/WAF SKU) のスケーリング

### <a name="set-your-instance-count-based-on-your-peak-cpu-usage"></a>ピーク時の CPU 使用率に基づいてインスタンス数を設定する
v1 SKU ゲートウェイを使用している場合は、Application Gateway をスケーリングのために最大 32 インスタンスに設定できます。 過去 1 か月間の Application Gateway の CPU 使用率で 80% を超えるスパイクがないか確認します。これを監視対象メトリックとして使用できます。 インスタンス数は、ピーク時の使用率に応じて設定し、トラフィックの急増に対応するために 10% から 20% のバッファーを追加することをお勧めします。

:::image type="content" source="./media/application-gateway-covid-guidelines/v1-cpu-utilization-inline.png" alt-text="V1 CPU 使用率メトリック" lightbox="./media/application-gateway-covid-guidelines/v1-cpu-utilization-exp.png":::

### <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>自動スケール機能を備えパフォーマンスに優れた v2 SKU を v1 より優先的に使用する
v2 SKU では自動スケールが提供され、トラフィックの増加に合わせて Application Gateway がスケールアップできることが保証されています。 また、v1 と比較した場合、TLS オフロードのパフォーマンスは 5 倍で、デプロイと更新が高速化され、ゾーン冗長性を備えるなど、パフォーマンス面での重要な利点は他にもあります。 詳細については、[v2 のドキュメント](./application-gateway-autoscaling-zone-redundant.md)を参照してください。また、既存の v1 SKU ゲートウェイを v2 SKU に移行する方法については、v1 から v2 への[移行に関するドキュメント](./migrate-v1-v2.md)を参照してください。 

## <a name="autoscaling-for-application-gateway-v2-sku-standard_v2waf_v2-sku"></a>Application Gateway v2 SKU (Standard_v2/WAF_v2 SKU) の自動スケーリング

### <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>最大インスタンス数を許容上限 (125) に設定する
 
Application Gateway v2 SKU では、最大インスタンス数を許容上限の 125 に設定すると、必要に応じて Application Gateway をスケールアウトできます。 これによって、アプリケーションへのトラフィックが増加しても対応できるようになります。 ご利用になった容量ユニット (CU) のみが請求の対象となります。 

必ずサブネットのサイズとサブネットで使用可能な IP アドレス数を確認し、それに基づいて最大インスタンス数を設定するようにしてください。 対応するのに十分な領域がサブネットにない場合は、十分な容量がある、同じまたは別のサブネットにゲートウェイを再作成する必要があります。 

:::image type="content" source="./media/application-gateway-covid-guidelines/v2-autoscaling-max-instances-inline.png" alt-text="V2 自動スケーリングの構成" lightbox="./media/application-gateway-covid-guidelines/v2-autoscaling-max-instances-exp.png":::

### <a name="set-your-minimum-instance-count-based-on-your-average-compute-unit-usage"></a>コンピューティング ユニットの平均使用量に基づいて最小インスタンス数を設定する

Application Gateway v2 SKU の自動スケーリングでは、スケールアウトし、トラフィックの受け入れ準備ができた追加のインスタンス セットをプロビジョニングするまでに 6 分から 7 分かかります。 それまでは、短期的にトラフィックが急増すると、既存のゲートウェイ インスタンスがストレス条件下に置かれ、それによって予期しない遅延やトラフィックの損失が発生する可能性があります。 

最小インスタンス数は最適なレベルに設定することをお勧めします。 たとえば、ピーク負荷時のトラフィックを処理するために 50 インスタンスが必要な場合は、最小数は 10 未満ではなく、25 から 30 に設定することをお勧めします。これにより、短期的にトラフィックが急増しても、Application Gateway でそれを処理し、自動スケーリングが対応して有効になるまでに十分な時間を確保できます。

過去 1 か月間のコンピューティング ユニットのメトリックを確認します。 コンピューティング ユニット メトリックは、ゲートウェイの CPU 使用率を表し、ピーク時の使用量を 10 で割った値に基づいています。必要な最小インスタンス数を設定できます。 1 つのアプリケーション ゲートウェイ インスタンスで最小 10 個のコンピューティング ユニットを処理できることに注意してください。

:::image type="content" source="./media/application-gateway-covid-guidelines/compute-unit-metrics-inline.png" alt-text="V2 コンピューティング ユニット メトリック" lightbox="./media/application-gateway-covid-guidelines/compute-unit-metrics-exp.png":::

## <a name="manual-scaling-for-application-gateway-v2-sku-standard_v2waf_v2"></a>Application Gateway v2 SKU (Standard_v2/WAF_v2) の手動スケーリング

### <a name="set-your-instance-count-based-on-your-peak-compute-unit-usage"></a>ピーク時のコンピューティング ユニット使用量に基づいてインスタンス数を設定する 

自動スケーリングと異なり、手動スケーリングでは、トラフィックの要件に基づいて、アプリケーション ゲートウェイのインスタンス数を手動で設定する必要があります。 インスタンス数は、ピーク時の使用量に応じて設定し、トラフィックの急増に対応するために 10% から 20% のバッファーを追加することをお勧めします。 たとえば、ピーク時のトラフィックで 50 インスタンスが必要な場合は、予期しないトラフィック急増の発生に備えて 55 から 60 のインスタンスをプロビジョニングします。

過去 1 か月間のコンピューティング ユニット メトリックを確認します。 コンピューティング ユニット メトリックは、ゲートウェイの CPU 使用率を表し、ピーク時の使用量を 10 で割った値に基づいています。1 つのアプリケーション ゲートウェイで最小 10 個のコンピューティング ユニットを処理できるため、必要なインスタンス数を設定できます。

## <a name="monitoring-and-alerting"></a>監視とアラート

トラフィックまたは使用率の異常が通知されるようにするために、特定のメトリックに対してアラートを設定できます。 Application Gateway で提供されているメトリックの一覧については、[メトリックに関するドキュメント](./application-gateway-metrics.md)を参照してください。 メトリックのアラートを設定する方法については、Azure portal での[メトリックの視覚化](./application-gateway-metrics.md#metrics-visualization)に関する記事と、[Azure Monitor に関するドキュメント](../azure-monitor/alerts/alerts-metric.md)を参照してください。

## <a name="alerts-for-application-gateway-v1-sku-standardwaf"></a>Application Gateway v1 SKU (Standard/WAF) のアラート

### <a name="alert-if-average-cpu-utilization-crosses-80"></a>平均 CPU 使用率が 80% を超えたらアラートを出す

通常の状況では、CPU 使用率が 90% を超えることは望ましくありません。これは Application Gateway の背後でホストされている Web サイトで待機時間が発生し、クライアント エクスペリエンスを乱す可能性があるためです。 インスタンス数を増やすか、SKU のサイズを大きくするか、またはその両方を行うことによって、Application Gateway の構成を変更し、CPU 使用率を間接的に制御または向上させることができます。 CPU 使用率メトリックが平均で 80% を超える場合、アラートを設定します。

### <a name="alert-if-unhealthy-host-count-crosses-threshold"></a>異常なホストの数がしきい値を超えたらアラートを出す

このメトリックは、アプリケーション ゲートウェイで正常にプローブできないバックエンド サーバーの数を示します。 これにより、アプリケーション ゲートウェイのインスタンスをバックエンドに接続できない問題が検出されます。 この数値がバックエンド容量の 20% を超えたらアラートを出します。 例: 現在、バックエンド プールに 30 台のバックエンド サーバーがある場合は、異常なホストの数が 6 を超えたらアラートを出すように設定します。

### <a name="alert-if-response-status-4xx-5xx-crosses-threshold"></a>応答の状態 (4xx、5xx) がしきい値を超えたらアラートを出す 

Application Gateway の応答の状態が 4xx または 5xx のときにアラートを作成します。 一時的な問題が原因で 4xx または 5xx の応答が時々発生することがあります。 運用環境を観察して静的しきい値を判別するか、アラートに動的しきい値を使用してください。

### <a name="alert-if-failed-requests-crosses-threshold"></a>失敗した要求の数がしきい値を超えたらアラートを出す 

失敗した要求の数メトリックがしきい値を超えたらアラートを作成します。 運用環境を観察して静的しきい値を判別するか、アラートに動的しきい値を使用してください。

### <a name="example-setting-up-an-alert-for-more-than-100-failed-requests-in-the-last-5-minutes"></a>例:過去 5 分間に 100 件を超える要求が失敗した場合のアラートを設定する

この例では、Azure portal を使用して、過去 5 分間に失敗した要求の数が 100 を超えた場合にアラートを設定する方法を示します。
1. **Application Gateway** に移動します。
2. 左側のパネルで、 **[Monitoring]\(監視\)** タブの **[Metrics]\(メトリック\)** を選択します。 
3. **[失敗した要求の数]** のメトリックを追加します。
4. **[新しいアラート ルール]** をクリックし、条件とアクションを定義します。
5. **[アラート ルールの作成]** をクリックして、アラートを作成して有効にします。

:::image type="content" source="./media/application-gateway-covid-guidelines/create-alerts-inline.png" alt-text="V2 アラートの作成" lightbox="./media/application-gateway-covid-guidelines/create-alerts-exp.png":::

## <a name="alerts-for-application-gateway-v2-sku-standard_v2waf_v2"></a>Application Gateway v2 SKU (Standard_v2/WAF_v2) のアラート

### <a name="alert-if-compute-unit-utilization-crosses-75-of-average-usage"></a>コンピューティング ユニット使用率が平均使用量の 75% を超えたらアラートを出す 

コンピューティング ユニットは、Application Gateway のコンピューティング使用率の測定単位です。 過去 1 か月間の平均コンピューティング ユニット使用率を確認し、その 75% を超えた場合にアラートを設定します。 たとえば、平均使用量が 10 コンピューティング ユニットの場合は、7.5 CU にアラートを設定します。 こうすれば、使用量が増加している場合にアラートが出るので、対応に必要に時間を確保できます。 このトラフィックが持続すると考えられ、トラフィックの増加傾向が警戒される場合、最小値を引き上げることができます。 上記のスケーリングの提案に従って、必要に応じてスケールアウトします。

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>例:平均 CU 使用量の 75% にアラートを設定する

この例では、Azure portal を使用して、平均 CU 使用量の 75% に達したときのアラートを設定する方法を示しています。 
1. **Application Gateway** に移動します。
2. 左側のパネルで、 **[Monitoring]\(監視\)** タブの **[Metrics]\(メトリック\)** を選択します。 
3. **[Average Current Compute Units]\(現在のコンピューティング ユニット数の平均\)** のメトリックを追加します。 
4. 最小インスタンス数を平均 CU 使用量に設定している場合は、最小インスタンス数の 75% が使用されたらアラートを出す積極的な設定にしてください。 たとえば、平均使用量が 10 CU の場合、7.5 CU にアラートを設定します。 こうすれば、使用量が増加している場合にアラートが出るので、対応に必要に時間を確保できます。 このトラフィックが持続すると考えられ、トラフィックの増加傾向が警戒される場合、最小値を引き上げることができます。 

:::image type="content" source="./media/application-gateway-covid-guidelines/compute-unit-alert-inline.png" alt-text="V2 コンピューティング ユニットのアラート" lightbox="./media/application-gateway-covid-guidelines/compute-unit-alert-exp.png":::

> [!NOTE]
> 潜在的なトラフィック スパイクへの警戒度に応じて、より低い (または高い) CU 使用率でアラートが発生するように設定できます。

### <a name="alert-if-capacity-unit-utilization-crosses-75-of-peak-usage"></a>容量ユニットの使用率がピーク時の使用量の 75% を超えたらアラートを出す 

容量ユニットは、スループット、コンピューティング、接続数の観点から、全体的なゲートウェイ使用率を表します。 過去 1 か月間の最大容量ユニット使用量を確認し、その 75% を超えた場合にアラートを設定します。 たとえば、最大使用量が 100 容量ユニットの場合は、75 CU にアラートを設定します。 上記の 2 つの提案に従って、必要に応じてスケールアウトします。

### <a name="alert-if-unhealthy-host-count-crosses-threshold"></a>異常なホストの数がしきい値を超えたらアラートを出す 

このメトリックは、アプリケーション ゲートウェイで正常にプローブできないバックエンド サーバーの数を示します。 これにより、アプリケーション ゲートウェイのインスタンスをバックエンドに接続できない問題が検出されます。 この数値がバックエンド容量の 20% を超えたらアラートを出します。 例: 現在、バックエンド プールに 30 台のバックエンド サーバーがある場合は、異常なホストの数が 6 を超えたらアラートを出すように設定します。

### <a name="alert-if-response-status-4xx-5xx-crosses-threshold"></a>応答の状態 (4xx、5xx) がしきい値を超えたらアラートを出す 

Application Gateway の応答の状態が 4xx または 5xx のときにアラートを作成します。 一時的な問題が原因で 4xx または 5xx の応答が時々発生することがあります。 運用環境を観察して静的しきい値を判別するか、アラートに動的しきい値を使用してください。

### <a name="alert-if-failed-requests-crosses-threshold"></a>失敗した要求の数がしきい値を超えたらアラートを出す 

失敗した要求の数メトリックがしきい値を超えたらアラートを作成します。 運用環境を観察して静的しきい値を判別するか、アラートに動的しきい値を使用してください。

### <a name="alert-if-backend-last-byte-response-time-crosses-threshold"></a>バックエンドの最後のバイトの応答時間がしきい値を超えたらアラートを出す 

このメトリックは、バックエンド サーバーへの接続の確立を開始してから応答本文の最後のバイトを受信するまでの時間間隔を示します。 バックエンドの応答待機時間が、通常の特定のしきい値を超えた場合にアラートを作成します。 たとえば、バックエンドの応答待機時間が、通常の値から 30% を超えて増加している場合にアラートを出すように設定します。

### <a name="alert-if-application-gateway-total-time-crosses-threshold"></a>Application Gateway の合計時間がしきい値を超えたらアラートを出す

これは、Application Gateway が HTTP 要求の最初のバイトを受信してから、最後の応答バイトがクライアントに送信されるまでの間隔です。 バックエンドの応答待機時間が、通常の特定のしきい値を超えた場合にアラートを作成します。 たとえば、合計時間の待機時間が、通常の値から 30% を超えて増加している場合にアラートを出すように設定できます。

## <a name="set-up-waf-with-geo-filtering-and-bot-protection-to-stop-attacks"></a>geo フィルタリングとボット保護を使って WAF を設定して攻撃を阻止する
アプリケーションの手前に追加のセキュリティ層が必要な場合、WAF 機能のために Application Gateway WAF_v2 SKU を使用します。 特定の国/地域からのみアプリケーションへのアクセスを許可するように v2 SKU を構成できます。 地理的な場所に基づいてトラフィックを明示的に許可したりブロックしたりする WAF カスタム ルールを設定します。 詳細については、[geo フィルタリングのカスタム ルール](../web-application-firewall/ag/geomatch-custom-rules.md)および [PowerShell を使用して Application Gateway WAF_v2 SKU でカスタム ルールを構成する方法](../web-application-firewall/ag/configure-waf-custom-rules.md)に関するページを参照してください。

ボット保護を有効にして既知の悪意あるボットをブロックします。 これによって、アプリケーションに到達するトラフィック量を減少させます。 詳細については、[ボット保護と設定手順](../web-application-firewall/ag/configure-waf-custom-rules.md)に関するページを参照してください。

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>Application Gateway と WAF で診断を有効にする

診断ログでは、ファイアウォール ログ、パフォーマンス ログ、およびアクセス ログを確認できます。 Azure のこれらのログを使用して、Application Gateway の管理とトラブルシューティングを行うことができます。 詳細については、[診断に関するドキュメント](./application-gateway-diagnostics.md#diagnostic-logging)を参照してください。 

## <a name="set-up-an-tls-policy-for-extra-security"></a>セキュリティ強化のための TLS ポリシーを設定する
最新の TLS ポリシー バージョン ([AppGwSslPolicy20170401S](./application-gateway-ssl-policy-overview.md#appgwsslpolicy20170401s)) を使用していることを確認してください。 これにより、TLS 1.2 と、より強力な暗号が適用されます。 詳細については、[PowerShell を使用した TLS ポリシー バージョンと暗号スイートの構成](./application-gateway-configure-ssl-policy-powershell.md)に関するページを参照してください。