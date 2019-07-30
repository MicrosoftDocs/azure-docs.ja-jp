---
title: Azure Application Gateway の Resource Health の概要
description: この記事は、Azure Application Gateway のリソース正常性機能の概要です
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/9/2019
ms.author: victorh
ms.openlocfilehash: db29551a8150b70e797d45fe659482470c8aca2a
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659425"
---
# <a name="azure-application-gateway-resource-health-overview"></a>Azure Application Gateway の Resource Health の概要

[Azure Resource Health](../service-health/resource-health-overview.md) は、Azure サービスの問題によってリソースが影響を受けているときに、診断を行ったり、サポートを受けたりするのに役立ちます。 Azure Resource Health では、リソースの現在と過去の正常性に関する情報が通知されます。 また、問題を軽減するのに役立つテクニカル サポートが提供されます。

Application Gateway では、Resource Health は、ゲートウェイが送信するシグナルを基にそれが正常であるかどうかを評価します。 ゲートウェイが正常でない場合、Resource Health は追加の情報を分析して問題の原因を特定します。 また、マイクロソフトが行っている措置や、問題を修正するためにユーザーが実施できることも識別します。

正常性を評価する方法に関する追加情報については、[Azure Resource Health](../service-health/resource-health-checks-resource-types.md#microsoftnetworkapplicationgateways) に関する記事でリソースの種類と正常性チェックの全一覧を確認してください。


Application Gateway の正常性状態は、次のいずれかの状態として表示されます。

## <a name="available"></a>使用可能

**[使用可能]** 状態は、サービスにおいて、リソースの正常性に影響するイベントが一切検出されていないことを意味します。 ゲートウェイが過去 24 時間以内に予定外のダウンタイムから回復した場合は、**最近解決された**ことを示す通知が表示されます。

![[使用可能] 正常性状態](media/resource-health-overview/available-full.png)

## <a name="unavailable"></a>使用不可

**[使用不可]** 状態は、サービスにおいて、ゲートウェイの正常性に影響を与える継続中のイベント (プラットフォームまたはプラットフォーム以外のイベント) が検出されたことを意味します。

### <a name="platform-events"></a>プラットフォームのイベント

プラットフォームのイベントは、Azure インフラストラクチャの複数のコンポーネントによってトリガーされます。 これには、スケジュールされたアクション (計画的なメンテナンスなど) と、予期しないインシデント (計画されていないホストの再起動など) の両方が含まれます。

Resource Health は、イベントと復旧プロセスについてさらに詳細な情報を提供します。 また、ユーザーは、有効な Microsoft サポート契約がない場合でも、サポートに問い合わせることができます。

![[使用不可] 状態](media/resource-health-overview/unavailable.png)

## <a name="unknown"></a>Unknown

**[不明]** 正常性状態は、Resource Health がそのゲートウェイに関する情報を 10 分以上受け取っていないことを示します。 この状態は、ゲートウェイの状態を明確に示すものではありません。 しかし、トラブルシューティングのプロセスにおいて重要なデータ ポイントです。

ゲートウェイが想定したとおりに実行されている場合、状態は数分後に **[使用可能]** に変わります。

問題が発生している場合、 **[不明]** 正常性状態は、プラットフォーム内のイベントによってゲートウェイが影響を受けていることを示唆している可能性があります。

![[不明] 状態](media/resource-health-overview/unknown.png)

## <a name="degraded"></a>低下しています

**[低下]** 正常性状態は、ゲートウェイでパフォーマンス低下が検出されたものの、まだ使用可能であることを示します。

![[低下] 状態](media/resource-health-overview/degraded.png)

## <a name="next-steps"></a>次の手順

Application Gateway の Web アプリケーション ファイアウォール (WAF) のトラブルシューティングについては、「[Azure Application Gateway の Web アプリケーション ファイアウォール (WAF) のトラブルシューティング](web-application-firewall-troubleshoot.md)」を参照してください。