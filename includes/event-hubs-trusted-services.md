---
title: インクルード ファイル
description: インクルード ファイル
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 08/07/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b7c3719b1539aa585dbc030bb8dfe732e73c81ac
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185072"
---
## <a name="trusted-microsoft-services"></a>信頼できる Microsoft サービス
**[Allow trusted Microsoft services to bypass this firewall]\(信頼された Microsoft サービスがこのファイアウォールをバイパスすることを許可する\)** 設定を有効にした場合、次のサービスに Event Hubs リソースへのアクセス権が与えられます。

| 信頼できるサービス | サポートされる使用シナリオ | 
| --------------- | ------------------------- | 
| Azure Event Grid | Event Hubs 名前空間のイベント ハブにイベントを送信することを Azure Event Grid に許可します。 |
| Azure Monitor (診断設定) | Event Hubs 名前空間のイベント ハブに診断情報を送信することを Azure Monitor に許可します。 |