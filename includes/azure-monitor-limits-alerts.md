---
title: インクルード ファイル
description: インクルード ファイル
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 08/11/2020
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 332841d4ee4791cb8da1620aa292b05bbde74955
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/12/2020
ms.locfileid: "88162713"
---
| リソース | 既定の制限 | 上限 |
| --- | --- | --- |
| メトリック アラート (クラシック) |サブスクリプションごとに 100 個のアクティブなアラート ルール。 | サポートに問い合わせます |
| メトリック アラート |Azure パブリック、Azure China 21Vianet、および Azure Government クラウド内でサブスクリプションごとに 5,000 個のアクティブな警告ルール。 この制限に達した場合は、[同じ種類のマルチリソース アラート](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor)を使用できるかどうかを調べます。   | サポートに問い合わせます。 |
| アクティビティ ログ アラート | サブスクリプションごとに 100 個のアクティブなアラート ルール。 | 既定値と同じ |
| ログ アラート | サブスクリプションごとに 512 個のアクティブなアラート ルール。 リソースごとに 200 個のアクティブなアラート ルール。 | サポートに問い合わせます |
| アクション グループ |サブスクリプションごとに 2,000 のアクション グループ。 | サポートに問い合わせます |
| アラート ルールとアクション ルールの説明の長さ| ログ検索アラートは 4096 文字<br/>その他はすべて 2048 文字 | 既定値と同じ |
| 自動スケールの設定 |サブスクリプションあたりのリージョンごとに 100。 | 既定値と同じ |
| 自動スケール プロファイル |自動スケーリング設定ごとに 20 プロファイル。 | 既定値と同じ |


