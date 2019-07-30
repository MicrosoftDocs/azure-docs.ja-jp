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
ms.openlocfilehash: 2922a1fb92f3df07429d3463b8bf639f336776af
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305301"
---
| Resource | 既定の制限 | 上限 |
| --- | --- | --- |
| Azure アプリのプッシュ | アクション グループあたり 10 個のアプリ アクション。 | サポートに問い合わせます。 |
| Email | アクション グループの 1,000 個のメール アクション。<br>1 時間で 100 件以下の電子メール。<br>[レート制限情報](../articles/azure-monitor/platform/alerts-rate-limiting.md) もご覧ください。 | サポートに問い合わせます。 |
| ITSM | アクション グループの 10 個の ITSM アクション。 | サポートに問い合わせます。 | 
| ロジック アプリ | アクション グループの 10 個のロジック アプリ アクション。 | サポートに問い合わせます。 |
| Runbook | アクション グループの 10 個の Runbook アクション。 | サポートに問い合わせます。 |
| sms | アクション グループの 10 個の SMS アクション。<br>5 分ごとに SMS メッセージ 1 件以下。<br>[レート制限情報](../articles/azure-monitor/platform/alerts-rate-limiting.md) もご覧ください。 | サポートに問い合わせます。 |
| 音声 | アクション グループの 10 個の音声アクション。<br>5 分ごとに 1 件以下の音声通話。<br>[レート制限情報](../articles/azure-monitor/platform/alerts-rate-limiting.md) もご覧ください。 | サポートに問い合わせます。 |
| Webhook | アクション グループの 10 個の Webhook アクション。  Webhook の最大呼び出し数は、サブスクリプションごとに毎分 1500 個です。 その他の制限は[アクション固有の情報](../articles/azure-monitor/platform/action-groups.md#action-specific-information)でご覧いただけます。  | サポートに問い合わせます。 |
