---
title: インクルード ファイル
description: インクルード ファイル
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 4700573d3f5319599a6437d092e20d8013d2f7fb
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632782"
---
| Resource | 既定の制限 | 上限 |
| --- | --- | --- |
| 自動スケールの設定 |サブスクリプションあたりのリージョンごとに 100。 | 既定値と同じ。 |
| メトリック アラート (クラシック) |サブスクリプションごとに 100 個のアクティブなアラート ルール。 | サポートに問い合わせます。 |
| メトリック アラート |サブスクリプションごとに 100 個のアクティブなアラート ルール。 | サポートに問い合わせます。 |
| アクション グループ |サブスクリプションごとに 2,000 のアクション グループ。 | サポートに問い合わせます。 |

**アクション グループ固有の制限**

| Resource | 既定の制限 | 上限 |
| --- | --- | --- |
| Azure アプリのプッシュ | アクション グループあたり 10 個のアプリ アクション。 | サポートに問い合わせます。 |
| 電子メール | アクション グループの 1,000 個のメール アクション。 [レート制限情報](../articles/azure-monitor/platform/alerts-rate-limiting.md) もご覧ください。 | サポートに問い合わせます。 |
| ITSM | アクション グループの 10 個の ITSM アクション。 | サポートに問い合わせます。 | 
| ロジック アプリ | アクション グループの 10 個のロジック アプリ アクション。 | サポートに問い合わせます。 |
| Runbook | アクション グループの 10 個の Runbook アクション。 | サポートに問い合わせます。 |
| sms | アクション グループの 10 個の SMS アクション。 [レート制限情報](../articles/azure-monitor/platform/alerts-rate-limiting.md) もご覧ください。 | サポートに問い合わせます。 |
| 音声 | アクション グループの 10 個の音声アクション。 [レート制限情報](../articles/azure-monitor/platform/alerts-rate-limiting.md) もご覧ください。 | サポートに問い合わせます。 |
| Webhook | アクション グループの 10 個の Webhook アクション。  Webhook の最大呼び出し数は、サブスクリプションごとに毎分 1500 個です。 その他の制限は[アクション固有の情報](../articles/azure-monitor/platform/action-groups.md#action-specific-information)でご覧いただけます。  | サポートに問い合わせます。 |
