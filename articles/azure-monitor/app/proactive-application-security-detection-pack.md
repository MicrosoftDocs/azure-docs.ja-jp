---
title: スマート検出 - Azure Application Insights とセキュリティ検出パック | Microsoft Docs
description: Azure Application Insights を使用して、潜在的なセキュリティの問題を監視します。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: mbullwin
ms.openlocfilehash: 16dd381301bdc650022ba5580f96a1733aeb32b0
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/03/2019
ms.locfileid: "54004712"
---
# <a name="application-security-detection-pack-preview"></a>アプリケーション セキュリティ検出パック (プレビュー)

Application Insights は、アプリケーションによって生成されたテレメトリを自動的に分析し、潜在的なセキュリティの問題を検出します。 この機能を使用して、潜在的なセキュリティの問題を識別し、アプリケーションを修正するか必要なセキュリティ手段が講じることで、それらの問題を処理できます。

この機能を使用するには、[テレメトリを送信するようにアプリを構成する](https://docs.microsoft.com/azure/application-insights/app-insights-usage-overview)こと以外は、特別な設定は必要ありません。

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>この種類のスマート検出通知はどのような場合に取得されますか。
検出されるセキュリティの問題には、次の 3 つの種類があります。
1. 安全でない URL アクセス: アプリケーションの URL が、HTTP と HTTPS の両方を介してアクセスされている。 通常、HTTPS 要求を受け入れる URL が HTTP 要求を受け入れることはありません。 これは、アプリケーションにバグやセキュリティの問題があることを示唆している可能性があります。
2. 安全でないフォーム: アプリケーションのフォーム (またはその他の "POST" 要求) で、HTTPS ではなく HTTP が使用されている。 HTTP を使用すると、フォームによって送信されるユーザー データが侵害される可能性があります。
3. 不審なユーザー アクティビティ: アプリケーションが、ほぼ同時刻に同じユーザーによって複数の国からアクセスされている。 たとえば、同じユーザーが、1 時間以内にスペインと米国からアプリケーションにアクセスしています。 この検出は、アプリケーションに対して潜在的に悪意のあるアクセスが試行されてることを示唆しています。

## <a name="does-my-app-definitely-have-a-security-issue"></a>アプリにセキュリティの問題があるのは確かですか。
いいえ。通知は、アプリに間違いなくセキュリティの問題があることを示すものではありません。 上記のシナリオでの検出は、多くの場合、セキュリティの問題を示唆しています。 ただし、検出されるのは業務上の正当な理由がある可能性があるため、検出を無視することができます。

## <a name="how-do-i-fix-the-insecure-url-access-detection"></a>"安全でない URL アクセス" 検出を修正する方法
1. **トリアージ**。 通知には、安全でない URL にアクセスしたユーザーの数と、安全でないアクセスによって最も影響を受けた URL が示されます。 この情報を基に、問題に優先順位を割り当てることができます。
2. **範囲**。 安全でない URL にアクセスしたユーザーの割合は?  影響を受けた URL の数は?  この情報を通知から取得できます。
3. **診断**。 検出では、安全でない要求の一覧と、影響を受けた URL とユーザーの一覧が提供されます。これらを使用して問題を詳しく診断できます。

## <a name="how-do-i-fix-the-insecure-form-detection"></a>"安全でないフォーム" 検出を修正する方法
1. **トリアージ**。 通知には、安全でないフォームの数と、データが侵害された可能性のあるユーザーの数が示されます。 この情報を基に、問題に優先順位を割り当てることができます。
2. **範囲**。 最大数の安全でない転送に関係しているのはどのフォームであり、特定の期間の安全でない転送の分布状況はどうなっているのか?  この情報を通知から取得できます。
3. **診断**。 検出では、安全でないフォームの一覧と、各フォームの安全でない転送数の内訳が提供されます。これらの情報を使用して問題を詳しく診断できます。

## <a name="how-do-i-fix-the-suspicious-user-activity-detection"></a>"不審なユーザー アクティビティ" 検出を修正する方法
1. **トリアージ**。 通知には、疑わしい動作を行ったユーザーの数が示されます。 この情報を基に、問題に優先順位を割り当てることができます。
2. **範囲**。 どこの国から疑わしい要求が送信されているのか?  最も疑わしいのはどのユーザーか?  この情報を通知から取得できます。
3. **診断**。 検出では、疑わしいユーザーの一覧と、各ユーザーの国の一覧が提供されます。これらを使用して、問題を詳しく診断できます。
