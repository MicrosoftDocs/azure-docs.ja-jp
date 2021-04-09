---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7151c110fd50f7485aa0b130832aace4f3143ad9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101750272"
---
- 現在、この機能は Premium SSD でのみサポートされています。
- ディスクのレベルを変更する前に、VM の割り当てを解除するか、実行中の VM からディスクを切断する必要があります。
- P60、P70、P80 のパフォーマンス レベルは、4,096 GiB より大きなディスクでのみ利用できます。
- ディスクのパフォーマンス レベルは、12 時間ごとに 1 回だけダウングレードできます。

## <a name="change-performance-tier-without-downtime-preview"></a>ダウンタイムなしでパフォーマンス レベルを変更する (プレビュー)

通常、パフォーマンス レベルを変更するには、VM の割り当てを解除するか、ディスクをデタッチする必要があります。 しかし、このプレビュー機能を有効にすれば、レベルを変更するために VM の割り当てを解除したり、ディスクをデタッチしたりする必要はありません。 プレビューには、[こちら](https://aka.ms/liveperftiersignup)からサインアップできます。

このプレビューには、次の制限事項があります。
- 提供されるリージョンは EastUS2EUAP のみです。
- 現在、共有ディスクでは使用できません。
- ダウンタイムなしでパフォーマンス レベルを変更するには、`2020-12-01` API を含む Azure Resource Manager テンプレートを使用する必要があります。