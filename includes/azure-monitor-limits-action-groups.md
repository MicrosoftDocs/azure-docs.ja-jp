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
ms.openlocfilehash: 6f9405e1b402b029b628821824a1727dd825a031
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101734046"
---
| リソース | 既定の制限 | 上限 |
| --- | --- | --- |
| Azure アプリのプッシュ | アクション グループあたり 10 個のアプリ アクション。 | 既定値と同じ |
| Email | アクション グループの 1,000 個のメール アクション。<br>1 時間で 100 件以下の電子メール。<br>[レート制限情報](../articles/azure-monitor/alerts/alerts-rate-limiting.md) もご覧ください。 | 既定値と同じ |
| ITSM | アクション グループの 10 個の ITSM アクション。 | 既定値と同じ | 
| ロジック アプリ | アクション グループの 10 個のロジック アプリ アクション。 | 既定値と同じ |
| Runbook | アクション グループの 10 個の Runbook アクション。 | 既定値と同じ |
| sms | アクション グループの 10 個の SMS アクション。<br>5 分ごとに SMS メッセージ 1 件以下。<br>[レート制限情報](../articles/azure-monitor/alerts/alerts-rate-limiting.md) もご覧ください。 | 既定値と同じ |
| 音声 | アクション グループの 10 個の音声アクション。<br>5 分ごとに 1 件以下の音声通話。<br>[レート制限情報](../articles/azure-monitor/alerts/alerts-rate-limiting.md) もご覧ください。 | 既定値と同じ |
| Webhook | アクション グループの 10 個の Webhook アクション。  Webhook の最大呼び出し数は、サブスクリプションごとに毎分 1500 個です。 その他の制限は[アクション固有の情報](../articles/azure-monitor/alerts/action-groups.md#action-specific-information)でご覧いただけます。  | 既定値と同じ |