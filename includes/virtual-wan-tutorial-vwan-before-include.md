---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 66e21aefa5648262ca2fcc61501905f725ac0e4b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181079"
---
構成を開始する前に、以下の条件を満たしていることを確認します。

* 接続する仮想ネットワークが既にある場合は、オンプレミス ネットワークのどのサブネットも接続先の仮想ネットワークと重複していないことを確認します。 仮想ネットワークではゲートウェイ サブネットは必要ありません。仮想ネットワークに仮想ネットワーク ゲートウェイを配置することはできません。 仮想ネットワークがない場合は、この記事の手順を使用して仮想ネットワークを作成できます。
* ハブ リージョンの IP アドレス範囲を取得します。 ハブは仮想ネットワークであり、ハブ リージョンに指定するアドレス範囲が接続先の既存の仮想ネットワークと重複することはできません。 さらに、オンプレミスで接続するアドレス範囲と重複することもできません。 オンプレミス ネットワーク構成の IP アドレス範囲を把握していない場合は、詳細な情報を把握している担当者と協力して作業を行ってください。
* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。