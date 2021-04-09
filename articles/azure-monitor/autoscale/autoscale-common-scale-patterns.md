---
title: 自動スケールの一般的なパターンの概要
description: Azure におけるリソースの自動スケールの一般的なパターンについて説明します。
ms.topic: conceptual
ms.date: 05/07/2017
ms.subservice: autoscale
ms.openlocfilehash: adbfecd05bfd4330e7c7c972cfdb9fe7021eec31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100603458"
---
# <a name="overview-of-common-autoscale-patterns"></a>自動スケールの一般的なパターンの概要
この記事では、Azure におけるリソースのスケールの一般的なパターンについて説明します。

Azure Monitor の自動スケーリングは、[Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/)、[Cloud Services](https://azure.microsoft.com/services/cloud-services/)、[App Service - Web Apps](https://azure.microsoft.com/services/app-service/web/)、および [API Management サービス](../../api-management/api-management-key-concepts.md)にのみ適用されます。

## <a name="lets-get-started"></a>作業の開始

この記事は、自動スケールを熟知していることを前提としています。 リソースをスケールする方法については、[こちら][1]をご覧ください。 スケールの一般的なパターンを以下に示します。

## <a name="scale-based-on-cpu"></a>CPU に基づいてスケールする

Web アプリ (/VMSS/クラウド サービス ロール) があり、次の要件がある場合は、このパターンを使用します。

- CPU に基づいてスケールアウト/スケールインする。
- さらに、最小数のインスタンスを確保する。
- また、スケール可能なインスタンス数に上限を設定する。

![CPU に基づいてスケールする][2]

## <a name="scale-differently-on-weekdays-vs-weekends"></a>平日と週末に異なる方法でスケールする

Web アプリ (/VMSS/クラウド サービス ロール) があり、次の要件がある場合は、このパターンを使用します。

- 既定で 3 つのインスタンスを使用する (平日)。
- 週末のトラフィックは想定していないので、週末には 1 インスタンスにスケールダウンする。

![平日と週末に異なる方法でスケールする][3]

## <a name="scale-differently-during-holidays"></a>休暇中は異なる方法でスケールする

Web アプリ (/VMSS/クラウド サービス ロール) があり、次の要件がある場合は、このパターンを使用します。

- 既定で CPU 使用率に基づいてスケールアップ/スケールダウンする。
- ただし、休暇シーズン (またはビジネスにとって重要な特定の日) には、既定値をオーバーライドし、容量を自由に増やす。

![休日は異なる方法でスケールする][4]

## <a name="scale-based-on-custom-metric"></a>カスタム メトリックに基づいてスケールする

Web フロントエンド、およびバックエンドと通信する API 層があり、次の要件がある場合は、このパターンを使用します。

- フロントエンドのカスタム イベントに基づいて API 層をスケーリングする (例: ショッピング カート内のアイテムの数に基づいて精算プロセスをスケーリングする)。

![カスタム メトリックに基づいてスケールする][5]

<!--Reference-->
[1]: ./autoscale-get-started.md
[2]: ./media/autoscale-common-scale-patterns/scale-based-on-cpu.png
[3]: ./media/autoscale-common-scale-patterns/weekday-weekend-scale.png
[4]: ./media/autoscale-common-scale-patterns/holidays-scale.png
[5]: ./media/autoscale-common-scale-patterns/custom-metric-scale.png
