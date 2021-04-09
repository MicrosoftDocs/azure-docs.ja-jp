---
title: リソースの正常性を理解する
titleSuffix: Azure Digital Twins
description: Azure Resource Health を使用して Azure Digital Twins インスタンスの状態を確認する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 10/6/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: b64ee4d1538276bbbcab3fe09054f399ad20c162
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "94616551"
---
# <a name="troubleshooting-azure-digital-twins-resource-health"></a>Azure Digital Twins のトラブルシューティング: リソース ヘルス

[Azure Resource Health](../service-health/resource-health-overview.md) は、Azure のリソースに影響を及ぼしているサービスの問題を診断したり、サポートを受けたりするために役立ちます。 リソースの現在と過去の正常性について報告します。

この記事では、Azure Digital Twins インスタンスの **リソースの正常性** 情報を取得する方法について説明します。

## <a name="use-azure-resource-health"></a>Azure Resource Health の使用

Azure Resource Health は、Azure Digital Twins インスタンスが稼働しているかどうかを監視するために役立ちます。 また、リージョンで発生した停電がインスタンスの正常性に影響を与えているかどうかを確認するためにも使用できます。

インスタンスの正常性を確認するには、次の手順を行います。

1. [Azure portal](https://portal.azure.com) にサインインし、Azure Digital Twins インスタンスに移動します。 その名前をポータルの検索バーに入力して、検索することができます。 

2. インスタンスのメニューから、 *[サポートとトラブルシューティング]* の下にある _**[リソース正常性]**_ を選択します。 リソース正常性の履歴を確認できるページが表示されます。 

    :::image type="content" source="media/troubleshoot-resource-health/resource-health.png" alt-text="[リソース正常性] ページを示すスクリーンショット。過去 9 日間の日時レポートが表示された [正常性の履歴] セクションがあります。毎日、&quot;使用可能&quot; の状態が示されています。":::

上の図では、このインスタンスは "*使用可能*" と示されており、過去 9 日間分あります。 "*使用可能*" 状態と、表示される可能性のあるその他の状態の種類の詳細については、[*Azure リソースの正常性の概要*](../service-health/resource-health-overview.md)に関するページを参照してください。

さまざまな種類の Azure リソースにおけるリソースの正常性の詳細については、「[*Azure Resource Health で利用できるリソースの種類と正常性チェック*](../service-health/resource-health-checks-resource-types.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

Azure Digital Twins インスタンスを監視するその他の方法については、次の記事を参照してください。
* [*トラブルシューティング:Azure Monitor でメトリックを表示する*](troubleshoot-metrics.md)" に関するページを参照してください
* [*トラブルシューティング:診断の設定*](troubleshoot-diagnostics.md)に関するページを参照してください。
* [*トラブルシューティング:アラートを設定する*](troubleshoot-alerts.md)
