---
title: Forcepoint DLP を Azure Sentinel に接続する | Microsoft Docs
description: Forcepoint DLP を Azure Sentinel に接続する方法について学習します。
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: 62ed3915dcaf596d144a2f59817626cdf8ec47e5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100092776"
---
# <a name="connect-your-forcepoint-dlp-to-azure-sentinel"></a>Forcepoint DLP を Azure Sentinel に接続する

> [!IMPORTANT]
> Azure Sentinel の Forcepoint Data Loss Prevention (DLP) データ コネクタは、現在パブリック プレビューです。 この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。



Forcepoint DLP コネクタを使用すると、DLP インシデント データを Azure Sentinel に自動的にエクスポートできます。 これを使用すると、ユーザー アクティビティやデータ損失のインシデントを可視化できます。 また、Azure ワークロードやその他のフィードのデータとの相関関係も有効になり、Azure Sentinel 内の Workbooks での監視機能が向上します。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="configure-and-connect-forcepoint-dlp"></a>Forcepoint DLP を構成して接続する

[Forcepoint DLP 統合ガイド](https://frcpnt.com/dlp-sentinel)で説明されているように REST API 経由で、JSON 形式のインシデント データをご利用の Azure ワークスペースに転送するように Forcepoint DLP を構成します。


## <a name="find-your-data"></a>データの検索

Forcepoint DLP コネクタが設定されると、Log Analytics の CustomLogs **ForcepointDLPEvents_CL** の下にデータが表示されます。


Forcepoint DLP の Log Analytics で関連するスキーマを使用するには、**ForcepointDLPEvents_CL** を検索します。


## <a name="validate-connectivity"></a>接続の検証

ログが Log Analytics に表示され始めるまで、最大 20 分かかることがあります。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Forcepoint DLP を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。