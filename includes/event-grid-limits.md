---
title: インクルード ファイル
description: インクルード ファイル
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 02/17/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2f52e8a89ec9dd78a1951836053cb2c698310bbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100645451"
---
次の制限は、Azure Event Grid の **トピック** (システム、カスタム、パートナーのトピック) に適用されます。 

| リソース | 制限 |
| --- | --- |
| Azure サブスクリプションあたりのカスタム トピック数 | 100 |
| トピックあたりのイベント サブスクリプション数 | 500 |
| カスタムまたはパートナーのトピックの発行率 (イングレス) | 5,000 イベント/秒または 5 MB/秒 (いずれか先に達した方) |
| イベント サイズ | 1 MB  |
| トピックあたりのプライベート エンドポイント接続数  | 64 | 
| トピックあたりの IP ファイアウォール規則数 | 16 | 

次の制限は、Azure Event Grid **ドメイン** に適用されます。 

| リソース | 制限 |
| --- | --- |
| イベント ドメインあたりのトピック数 | 100,000 |
| ドメイン内のトピックあたりのイベント サブスクリプション数 | 500 |
| ドメイン スコープ イベント サブスクリプション数 | 50 |
| イベント ドメインの発行率 (イングレス) | 5,000 イベント/秒または 5 MB/秒 (いずれか先に達した方) |
| Azure サブスクリプションあたりのイベント ドメイン数 | 100 |
| ドメインあたりのプライベート エンドポイント接続数 | 64 | 
| ドメインあたりの IP ファイアウォール規則数 | 16 | 


