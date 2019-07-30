---
title: Project Acoustics のベイク精度
titlesuffix: Azure Cognitive Services
description: この概念的概要では、音響のベイク操作をする際の粗分解能と細分解能の違いについて説明します。
services: cognitive-services
author: KyleStorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: kylsto
ms.openlocfilehash: ba7c71c666ae8f90d499bfe52303537aeb112a77
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68296712"
---
# <a name="project-acoustics-bake-resolution"></a>Project Acoustics のベイク精度
この概念的概要では、音響のベイク操作をする際の粗分解能と細分解能の違いについて説明します。 この設定は、ベイク ワークフローのプローブ手順で選択します。

## <a name="Coarse-vs-Fine-Resolution"></a> 粗分解能と細分解能の比較

分解能設定の粗と細の唯一の違いは、シミュレーションが実行される周波数です。 細では、粗の 2 倍の周波数が使用されます。 これは、音響シミュレーションにさまざまな影響を及ぼします。

* 粗の波長は細の 2 倍であるため、ボクセルが 2 倍の大きさになります。
* シミュレーション時間はボクセル サイズに直接関係します。粗でのベイクは細でのベイクよりも約 16 倍高速化されます。
* ボクセル サイズより小さいポータル (ドアや窓など) はシミュレートできません。 粗設定では、これらのより小さなポータルがシミュレートされないことがあります。そのため、実行時に音が通過しなくなります。 ボクセルを表示することによって、この状況が発生しているかどうかを確認できます。
* シミュレーションの周波数が低いほど、角やエッジ付近での回折が少なくなります。
* "中身の詰まった" ボクセル (つまり、ジオメトリを含むボクセル) 内に音源を配置することはできません。 これを行うと音がなくなります。 粗設定のサイズの大きいボクセル内には音源は配置されないため、細設定を使用する場合よりも音源の配置が難しくなります。
* 次に示すように、ボクセルのサイズが大きくなると、ポータルへの侵入の度合いが大きくなります。 最初の画像は、粗を使用して作成されたものであり、2 番目は、細分解能を使用した同じ出入り口を示しています。 赤のマーキングで示すように、細設定を使用すると、出入り口への侵入が大幅に減っています。 青色の線はジオメトリによって定義された出入り口であり、赤色の線はボクセル サイズによって定義された有効な音響ポータルです。 特定の状況でこの侵入がどの程度になるかは、ボクセルとポータルのジオメトリの適合度合いに完全に左右されます。この度合いは、シーン内のオブジェクトのサイズと位置によって決まります。

![Unreal で出入り口を埋める粗いボクセルのスクリーンショット](media/unreal-coarse-bake.png)

![Unreal で出入り口に配置された細かなボクセルのスクリーンショット](media/unreal-fine-bake.png)

## <a name="next-steps"></a>次の手順

[Unreal](unreal-baking.md) または [Unity](unity-baking.md) プラグインを使用して、粗分解能と細分解能の設定を試します。
