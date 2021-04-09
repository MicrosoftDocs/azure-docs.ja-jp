---
title: Azure でのカスタム メトリックを使用した自動スケール
description: Azure でカスタム メトリックを使用してリソースをスケールする方法について説明します。
ms.topic: conceptual
ms.date: 05/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 8e744e6a91eb6fbe23a6b45f95c39b1acfdcb61f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100603450"
---
# <a name="get-started-with-auto-scale-by-custom-metric-in-azure"></a>Azure でのカスタム メトリックによる自動スケールの概要
この記事では、Azure Portal でカスタム メトリックを使用してリソースをスケールする方法について説明します。

Azure Monitor の自動スケーリングは、[Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/)、[Cloud Services](https://azure.microsoft.com/services/cloud-services/)、[App Service - Web Apps](https://azure.microsoft.com/services/app-service/web/)、[Azure Data Explorer クラスター](https://azure.microsoft.com/services/data-explorer/)、   
統合サービス環境および [API Management サービス](../../api-management/api-management-key-concepts.md)のみに適用されます。

## <a name="lets-get-started"></a>作業の開始
この記事では、Application Insights が構成されている Web アプリがあることを前提としています。 Web アプリをまだ所有していない場合は、[ASP.NET Web サイト向けに Application Insights を設定する][1]ことができます。

- [Azure portal][2] を開きます。
- 左側のナビゲーション ウィンドウで、Azure Monitor のアイコンをクリックします。
  ![Azure Monitor の起動][3]
- [自動スケール] 設定をクリックすると、自動スケールを適用できるすべてのリソースと、現在の自動スケールの状態が表示されます。![Azure Monitor での自動スケールの検出][4]
- Azure Monitor で [自動スケール] ブレードを開き、スケールするリソースを選択します。
  > 注:以下の手順では、App Insights が構成されている Web アプリに関連付けられた App Service プランを使用します。
- リソースのスケール設定ブレードで、現在のインスタンス数が 1 であることを確認します。 [Enable autoscale]\(自動スケールを有効にする\) をクリックします。
  ![新しい Web アプリのスケール設定][5]
- スケール設定の名前を指定し、[ルールの追加] をクリックします。 右側にコンテキスト ペインとして表示されるスケール ルールのオプションに注目します。 既定では、リソースの CPU の割合が 70% を超えた場合にインスタンス数を 1 つずつ増やしてスケールするようにオプションが設定されています。 上部にある [Metric source]\(メトリック ソース\) を [Application Insights] に変更し、[リソース] ボックスの一覧で Application Insights リソースを選択して、スケールする際に基づくカスタム メトリックを選択します。
  ![カスタム メトリックによるスケール][6]
- 上記の手順と同様に、カスタム メトリックがしきい値を下回る場合にスケールインし、スケール カウントを 1 ずつ小さくするスケール ルールを追加します。
  ![CPU に基づくスケール][7]
- インスタンスの限界を設定します。 たとえば、カスタム メトリックの変動に応じて 2～5 個のインスタンスの間でスケールする場合は、[最小] を「2」、[最大] を「5」、[既定] を「2」に設定します。
  > 注:リソース メトリックの読み取りで問題が発生し、現在の容量が既定の容量を下回る場合は、リソースの可用性を確保するために、自動スケールによって既定値にスケールアウトされます。 現在の容量が既に既定の容量を上回る場合、自動スケールではスケールインされません。
- [保存] をクリックします。

おめでとうございます。 カスタム メトリックに基づいて Web アプリを自動スケールするスケール設定が正常に作成されました。

> 注:VMSS またはクラウド サービス ロールを使用する場合も同じ手順が適用されます。

<!--Reference-->
[1]: ../app/asp-net.md
[2]: https://portal.azure.com
[3]: ./media/autoscale-custom-metric/azure-monitor-launch.png
[4]: ./media/autoscale-custom-metric/discover-autoscale-azure-monitor.png
[5]: ./media/autoscale-custom-metric/scale-setting-new-web-app.png
[6]: ./media/autoscale-custom-metric/scale-by-custom-metric.png
[7]: ./media/autoscale-custom-metric/autoscale-setting-custom-metrics-ai.png
