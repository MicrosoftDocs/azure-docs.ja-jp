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
ms.openlocfilehash: 945d701a2a7dffc259c601b4dab9fa1333ccc066
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896601"
---
構成を開始する前に、次の条件を満たしていることを確認してください。

* 接続先の仮想ネットワークが既にある場合、オンプレミス ネットワークのいずれのサブネットとも重複しないことを確認します。 仮想ネットワークにゲートウェイ サブネットは不要であり、また仮想ネットワーク ゲートウェイを割り当てることはできません。 仮想ネットワークがない場合は、この記事の手順を使用して作成できます。
* ハブ リージョンの IP アドレス範囲を取得します。 ハブは仮想ネットワークであり、ハブ リージョンに指定するアドレス範囲が接続先の既存の仮想ネットワークと重複することはできません。 さらに、オンプレミスで接続するアドレス範囲と重複することもできません。 オンプレミス ネットワーク構成に含まれている IP アドレス範囲になじみがない場合は、それらの詳細を提供できるだれかと調整してください。
* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。