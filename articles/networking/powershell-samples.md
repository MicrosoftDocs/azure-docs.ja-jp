---
title: Azure PowerShell のサンプル - ネットワーク
description: 多層アプリケーション用に仮想ネットワークを作成するサンプルを含む、ネットワーク用の Azure PowerShell のサンプルについて説明します。
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/24/2017
ms.author: gwallace
ms.openlocfilehash: aa3304cbabb183368f0e47415e64120854f43535
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87282077"
---
# <a name="azure-powershell-samples-for-networking"></a>ネットワークに関する Azure PowerShell のサンプル

次の表には、Azure PowerShell を使って構築されたスクリプトへのリンクが含まれています。

| スクリプト | 説明 |
|-|-|
|**Azure リソースの間の接続**||
| [多層アプリケーション用の仮想ネットワークの作成](./scripts/virtual-network-powershell-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | フロントエンド サブネットとバックエンド サブネットを含む仮想ネットワークを作成します。 フロントエンド サブネットへのトラフィックは HTTP に制限され、バックエンド サブネットへのトラフィックは SQL、ポート 1433 に制限されます。 |
| [2 つの仮想ネットワークのピアリング](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | 同じリージョンに 2 つの仮想ネットワークを作成し、接続します。 |
| [ネットワーク仮想アプライアンス経由のトラフィックのルーティング](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | フロントエンド サブネットとバックエンド サブネットを含む仮想ネットワークと、2 つのサブネット間でトラフィックをルーティングできる VM を作成します。 |
| [VM ネットワークの着信トラフィックおよび送信トラフィックのフィルター処理](./scripts/virtual-network-powershell-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | フロントエンド サブネットとバックエンド サブネットを含む仮想ネットワークを作成します。 フロントエンド サブネットへの受信ネットワーク トラフィックは、HTTP および HTTPS に制限されます。 バックエンド サブネットからインターネットへの送信トラフィックは許可されません。 |
|**負荷分散とトラフィックの方向**||
| [高可用性を確保するための、VM へのトラフィックの負荷分散](./scripts/load-balancer-windows-powershell-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | 可用性が高く、負荷分散がされた構成で仮想マシンを複数作成します。 |
| [アプリケーションの高可用性を確保するための、複数リージョン間でのトラフィック転送](./scripts/traffic-manager-powershell-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  2 つの App Service プラン、2 つの Web アプリ、1 つの Traffic Manager プロファイル、および 2 つの Traffic Manager エンドポイントを作成します。 |
| | |
