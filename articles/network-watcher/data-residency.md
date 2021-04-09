---
title: Azure Network Watcher のデータ所在地 | Microsoft Docs
description: この記事では、Azure Network Watcher サービスのデータ所在地について説明します。
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: b5aa8167031c3b871c6a6a4d84159c3c284bf241
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018430"
---
# <a name="data-residency-for-azure-network-watcher"></a>Azure Network Watcher のデータ所在地
Azure Network Watcher には、接続モニター (プレビュー) サービスを除き、顧客データが格納されません。


## <a name="connection-monitor-preview-data-residency"></a>接続モニター (プレビュー) のデータ所在地
接続モニター (プレビュー) サービスを使用すると、顧客データが格納されます。 このデータは、Network Watcher によって 1 つのリージョンに自動的に格納されます。 そのため、接続モニター (プレビュー) は、[トラスト センター](https://azuredatacentermap.azurewebsites.net/)で指定された要件など、リージョン内のデータ所在地の要件を自動的に満たします。

## <a name="data-residency"></a>データの保存場所
Azure では、顧客データを 1 つのリージョンに格納できるようにする機能は現在、アジア太平洋地域の東南アジア リージョン (シンガポール) と、ブラジル地域のブラジル南部リージョン (サンパウロ州) でのみ使用できます。 その他のすべてのリージョンでは、顧客データは geo 内に格納されます。 詳細については、[トラスト センター](https://azuredatacentermap.azurewebsites.net/)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

* [Network Watcher](./network-watcher-monitoring-overview.md) の概要をお読みください。