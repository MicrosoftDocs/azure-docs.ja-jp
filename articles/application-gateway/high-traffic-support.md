---
title: Application Gateway の高トラフィック量のサポート
description: この記事では、ネットワーク トラフィック量の多いシナリオをサポートするために Azure Application Gateway を構成するためのガイダンスを提供します。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: b96720ead2c7b7bc942efca32a8510f57c2dbcad
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85250250"
---
# <a name="application-gateway-high-traffic-support"></a>Application Gateway の高トラフィックのサポート

>[!NOTE]
> この記事では、COVID-19 危機が原因で発生する可能性がある大量のトラフィックに起因する追加のトラフィックを処理するように Application Gateway を設定するために役立つ、いくつかの推奨ガイドラインについて説明します。

Application Gateway と Web アプリケーション ファイアウォール (WAF) を使用すると、Web アプリケーションへのトラフィックをスケーラブルで安全な方法で管理できます。

以下の推奨事項は、追加のトラフィックを処理するために Application Gateway と WAF を設定する場合に役立ちます。

## <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>自動スケール機能を備えパフォーマンスに優れた v2 SKU を v1 より優先的に使用する
v2 SKU では自動スケールが提供され、トラフィックの増加に合わせて Application Gateway がスケールアップできることが保証されています。 また、v1 と比較した場合、TLS オフロードのパフォーマンスは 5 倍で、デプロイと更新が高速化され、ゾーン冗長性を備えるなど、パフォーマンス面での重要な利点は他にもあります。 詳細については、[v2 のドキュメント](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)を参照してください。 

## <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>最大インスタンス数を許容上限 (125) に設定する
 
Application Gateway v2 SKU のご利用を想定した場合、最大インスタンス数を許容上限の 125 に設定すると、必要に応じて Application Gateway をスケールアウトできます。 これによって、アプリケーションへのトラフィックが増加しても対応できるようになります。 ご利用になった容量ユニット (CU) のみが請求の対象となります。  

## <a name="set-your-minimum-instance-count-based-on-your-average-cu-usage"></a>平均 CU 使用量に基づいて最小インスタンス数を設定する

Application Gateway v2 SKU のご利用を想定した場合、自動スケールによるスケールアウトには 6 ～ 7 分かかります。最小インスタンス数が大きくしておくと、トラフィックのスパイクが発生しても自動スケール操作が不要なため、負荷上昇時に Application Gateway はより効率的にトラフィックを処理できます。  

## <a name="alert-if-a-certain-metric-surpasses-75-of-average-cu-utilization"></a>特定のメトリックが平均 CU 使用率の 75% を超えた場合にアラートを出す 
メトリックの詳しい説明やその他のチュートリアルについては、[Application Gateway のメトリックに関するドキュメント](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-visualization)を参照してください。 

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>例:平均 CU 使用量の 75% にアラートを設定する

この例では、Azure portal を使用して、平均 CU 使用量の 75% に達したときのアラートを設定する方法を示しています。 
1. **Application Gateway** に移動します。
2. 左側のパネルで、 **[Monitoring]\(監視\)** タブの **[Metrics]\(メトリック\)** を選択します。 
3. **[Average Current Compute Units]\(現在のコンピューティング ユニット数の平均\)** のメトリックを追加します。 
![WAF メトリックの設定](./media/application-gateway-covid-guidelines/waf-setup-metrics.png)
4. 最小インスタンス数を平均 CU 使用量に設定している場合は、最小インスタンス数の 75% が使用されたらアラートを出す積極的な設定にしてください。 たとえば、平均使用量が 10 CU の場合、7.5 CU にアラートを設定します。 こうすれば、使用量が増加している場合にアラートが出るので、対応に必要に時間を確保できます。 このトラフィックが持続すると考えられ、トラフィックの増加傾向が警戒される場合、最小値を引き上げることができます。 
![WAF アラートの設定](./media/application-gateway-covid-guidelines/waf-setup-monitoring-alert.png)

> [!NOTE]
> 潜在的なトラフィック スパイクへの警戒度に応じて、より低い (または高い) CU 使用率でアラートが発生するように設定できます。

## <a name="set-up-waf-with-geofiltering-and-bot-protection-to-stop-attacks"></a>geo フィルタリングとボット保護を WAF に設定して攻撃を阻止する
アプリケーションの手前に追加のセキュリティ層が必要な場合、WAF 機能のために Application Gateway WAF_v2 SKU を使用します。 特定の国/地域からのみアプリケーションへのアクセスを許可するように v2 SKU を構成できます。 WAF カスタム ルールを設定し、位置情報に基づいてトラフィックを明示的に許可またはブロックします。 詳細については、[geo フィルタリングのカスタム ルール](https://docs.microsoft.com/azure/web-application-firewall/ag/geomatch-custom-rules)および [PowerShell を使用して Application Gateway WAF_v2 SKU でカスタム ルールを構成する方法](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules)に関するページを参照してください。

ボット保護を有効にして既知の悪意あるボットをブロックします。 これによって、アプリケーションに到達するトラフィック量を減少させます。 詳細については、[ボット保護と設定手順](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules)に関するページを参照してください。

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>Application Gateway と WAF で診断を有効にする

診断ログでは、ファイアウォール ログ、パフォーマンス ログ、およびアクセス ログを確認できます。 Azure のこれらのログを使用して、Application Gateway の管理とトラブルシューティングを行うことができます。 詳細については、[診断に関するドキュメント](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#diagnostic-logging)を参照してください。 

## <a name="set-up-an-tls-policy-for-extra-security"></a>セキュリティ強化のための TLS ポリシーを設定する
最新の TLS ポリシー バージョン ([AppGwSslPolicy20170401S](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview#appgwsslpolicy20170401s)) を使用していることを確認してください。 これにより、TLS 1.2 と、より強力な暗号が適用されます。 詳細については、[PowerShell を使用した TLS ポリシー バージョンと暗号スイートの構成](https://docs.microsoft.com/azure/application-gateway/application-gateway-configure-ssl-policy-powershell)に関するページを参照してください。
