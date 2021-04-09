---
title: Azure Network Watcher リソースを移動する |Microsoft Docs
description: Azure Network Watcher リソースをリージョン間で移動する
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
ms.openlocfilehash: 4853f485e4424c3c3263a18d27834d0f9ae94918
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019654"
---
# <a name="moving-azure-network-watcher-resources-across-regions"></a>Azure Network Watcher リソースのリージョン間での移動

## <a name="moving-the-network-watcher-resource"></a>Network Watcher リソースの移動
Network Watcher リソースは、Network Watcher のバックエンドサービスを表しており、Azure によって完全に管理されています。 お客様はその管理を行う必要はありません。 このリソースに対する移動操作はサポートされていません。

## <a name="moving-child-resources-of-network-watcher"></a>Network Watcher の子リソースの移動
現在、リージョン間でのリソースの移動は、`*networkWatcher*` リソース タイプの子リソースではサポートされていません。

## <a name="next-steps"></a>次の手順
* [Network Watcher の概要](./network-watcher-monitoring-overview.md)に関するページを読む
* [Network Watcher のよくある質問](./frequently-asked-questions.md)に関するページを確認する