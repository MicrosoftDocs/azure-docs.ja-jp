---
title: インクルード ファイル
description: インクルード ファイル
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 05/22/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 3f94481e6a8550479788d92c744327e1dc3b58c4
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "66376936"
---
次の制限は、Azure Event Grid システムのトピックとカスタム トピックに適用されます。イベント ドメインには適用*されません*。

| リソース | 制限 |
| --- | --- |
| Azure サブスクリプションあたりのカスタム トピック数 | 100 |
| トピックあたりのイベント サブスクリプション数 | 500 |
| カスタム トピックの発行レート (イングレス) | 5,000 イベント/秒/トピック |
| 発行要求数 | 1 秒あたり 250 |
| イベント サイズ | 一般提供 (GA) では 64 KB をサポート。 現在のプレビューでは 1 MB をサポート。 |

次の制限は、イベント ドメインにのみ適用されます。

| リソース | 制限 |
| --- | --- |
| イベント ドメインあたりのトピック数 | パブリック プレビュー時に 1,000 件 |
| ドメイン内のトピックあたりのイベント サブスクリプション数 | パブリック プレビュー時に 50 件 |
| ドメイン スコープ イベント サブスクリプション数 | パブリック プレビュー時に 50 件 |
| イベント ドメインの発行率 (イングレス) | パブリック プレビュー時に 1 秒あたり 5,000 イベント |
| 発行要求数 | 1 秒あたり 250 |