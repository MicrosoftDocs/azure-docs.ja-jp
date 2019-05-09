---
title: インクルード ファイル
description: インクルード ファイル
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 04/30/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 443db1b4609e62fb7c57de417e42a2b4d0737ada
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554031"
---
次の制限は、Azure Event Grid システムのトピックとカスタム トピックに適用されます。イベント ドメインには適用*されません*。

| リソース | 制限 |
| --- | --- |
| Azure サブスクリプションあたりのカスタム トピック数 | 100 |
| トピックあたりのイベント サブスクリプション数 | 500 |
| カスタム トピックの発行レート (イングレス) | 5,000 イベント/秒/トピック |

次の制限は、イベント ドメインにのみ適用されます。

| リソース | 制限 |
| --- | --- |
| イベント ドメインあたりのトピック数 | パブリック プレビュー時に 1,000 件 |
| ドメイン内のトピックあたりのイベント サブスクリプション数 | パブリック プレビュー時に 50 件 |
| ドメイン スコープ イベント サブスクリプション数 | パブリック プレビュー時に 50 件 |
| イベント ドメインの発行率 (イングレス) | パブリック プレビュー時に 1 秒あたり 5,000 イベント |