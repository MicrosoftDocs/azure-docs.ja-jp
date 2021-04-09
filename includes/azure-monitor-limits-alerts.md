---
title: インクルード ファイル
description: インクルード ファイル
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 10/01/2020
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 071f2849a877f4ea1e8a84eff6ccfb8343be3ec7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101734066"
---
| リソース | 既定の制限 | 上限 |
| --- | --- | --- |
| メトリック アラート (クラシック) |サブスクリプションごとに 100 個のアクティブなアラート ルール。 | サポートに問い合わせます |
| メトリック アラート |Azure パブリック、Azure China 21Vianet、および Azure Government クラウド内でサブスクリプションごとに 5,000 個のアクティブな警告ルール。 この制限に達した場合は、[同じ種類のマルチリソース アラート](../articles/azure-monitor/alerts/alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor)を使用できるかどうかを調べます。<br/>アラート ルールあたり 5,000 個のメトリック時系列。 | サポートに問い合わせます。 |
| アクティビティ ログ アラート | サブスクリプションごとに 100 個のアクティブなアラート ルール (増やすことはできません)。 | 既定値と同じ |
| ログ アラート | サブスクリプションごとに 512 個のアクティブなアラート ルール。 リソースごとに 200 個のアクティブなアラート ルール。 | サポートに問い合わせます |
| アラート ルールとアクション ルールの説明の長さ| ログ検索アラートは 4096 文字<br/>その他はすべて 2048 文字 | 既定値と同じ |