---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: a6f399e19cadf3d6ce9edfaecb3d904e62c498aa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96006877"
---
このコンテナーには、次の構成設定があります。

|必須|設定|目的|
|--|--|--|
|はい|[ApiKey](#apikey-configuration-setting)|課金情報を追跡します。|
|いいえ|[ApplicationInsights](#applicationinsights-setting)|お客様のコンテナーに対する [Azure Application Insights](/azure/application-insights) テレメトリ サポートの追加を有効にします。|
|はい|[Billing](#billing-configuration-setting)|Azure 上のサービス リソースのエンドポイント URI を指定します。|
|はい|[Eula](#eula-setting)| コンテナーのライセンスに同意していることを示します。|
|いいえ|[Fluentd](#fluentd-settings)|ログと (必要に応じて) メトリック データを Fluentd サーバーに書き込みます。|
|いいえ|HTTP Proxy|送信要求を行うために、HTTP プロキシを構成します。|
|いいえ|[Logging](#logging-settings)|ASP.NET Core のログ サポートをお客様のコンテナーに提供します。 |
|いいえ|[Mounts](#mount-settings)|ホスト コンピューターからコンテナーに、またコンテナーからホスト コンピューターにデータを読み取ったり書き込んだりします。|