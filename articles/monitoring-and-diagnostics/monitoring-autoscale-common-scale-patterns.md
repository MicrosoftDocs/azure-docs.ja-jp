---
title: "自動スケールの一般的なパターンの概要 | Microsoft Docs"
description: "Azure におけるリソースの自動スケールの一般的なパターンについて説明します。"
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d37d3fda-8ef1-477c-a360-a855b418de84
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2017
ms.author: ancav
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c697b49769ffbbb4aee7a482bb067990b893490c
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---
# <a name="overview-of-common-autoscale-patterns"></a>自動スケールの一般的なパターンの概要
この記事では、Azure におけるリソースのスケールの一般的なパターンについて説明します。

Azure Monitor の自動スケールは、仮想マシン スケール セット (VMSS)、Cloud Services、App Service プラン、App Service Environment にのみ適用されます。 

# <a name="lets-get-started"></a>作業の開始

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
- ただし、休暇シーズン (またはビジネスにとって重要な特定の日) には、既定値を上書きし、容量を自由に増やす。

![休日は異なる方法でスケールする][4]

## <a name="scale-based-on-custom-metric"></a>カスタム メトリックに基づいてスケールする

Web フロントエンド、およびバックエンドと通信する API 層があり、次の要件がある場合は、このパターンを使用します。 

- フロントエンドのカスタム イベントに基づいて API 層をスケールする (例: ショッピング カート内のアイテムの数に基づいて精算プロセスをスケールする)。

![カスタム メトリックに基づいてスケールする][5]

<!--Reference-->
[1]: ./monitoring-autoscale-get-started.md
[2]: ./media/monitoring-autoscale-common-scale-patterns/scale-based-on-cpu.png
[3]: ./media/monitoring-autoscale-common-scale-patterns/weekday-weekend-scale.png
[4]: ./media/monitoring-autoscale-common-scale-patterns/holidays-scale.png
[5]: ./media/monitoring-autoscale-common-scale-patterns/custom-metric-scale.png
