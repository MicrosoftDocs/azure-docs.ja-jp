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
ms.date: 07/27/2020
ms.author: damendo
ms.openlocfilehash: 97349071fee6a95623e5b5efdc0c9818cfe7b811
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87388338"
---
# <a name="moving-azure-network-watcher-resources-across-regions"></a>Azure Network Watcher リソースのリージョン間での移動

## <a name="moving-the-network-watcher-resource"></a>Network Watcher リソースの移動
Network Watcher リソースは、Network Watcher のバックエンドサービスを表しており、Azure によって完全に管理されています。 お客様はその管理を行う必要はありません。 このリソースに対する移動操作はサポートされていません。

## <a name="moving-child-resources-of-network-watcher"></a>Network Watcher の子リソースの移動
現在、リージョン間でのリソースの移動は、`*networkWatcher*` リソース タイプの子リソースではサポートされていません。

## <a name="next-steps"></a>次の手順
* [Network Watcher の概要](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)に関するページを読む
* [Network Watcher のよくある質問](https://docs.microsoft.com/azure/network-watcher/frequently-asked-questions)に関するページを確認する
