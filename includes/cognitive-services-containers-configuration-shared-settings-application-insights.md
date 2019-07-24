---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: 2f5b03dd0170da9a9869183d7a412688509525ef
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2019
ms.locfileid: "56741056"
---
`ApplicationInsights` 設定を使用すると、[Azure Application Insights](https://docs.microsoft.com/azure/application-insights) テレメトリのサポートをお客様のコンテナーに追加できます。 Application Insights によってお客様のコンテナーを詳細に監視できます。 コンテナーの可用性、パフォーマンス、利用状況を簡単に監視できます。 さらに、お客様のコンテナーのエラーを迅速に特定して診断することもできます。

次の表に、`ApplicationInsights` セクションでサポートされている構成設定について説明します。

|必須| Name | データ型 | 説明 |
|--|------|-----------|-------------|
|いいえ | `InstrumentationKey` | String | コンテナーのテレメトリ データの送信先の Application Insights インスタンスのインストルメンテーション キー。 詳細については、「[Application Insights for ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core)」を参照してください。 <br><br>例:<br>`InstrumentationKey=123456789`|

