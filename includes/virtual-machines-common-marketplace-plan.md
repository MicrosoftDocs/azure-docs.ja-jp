---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 8e0e549f88caf4a541642bab77faf54b5e536b29
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71174966"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Marketplace の契約条件でイメージを展開する

Azure Marketplace の一部の VM イメージには、プログラムでデプロイする前に同意する必要がある追加のライセンスと購入契約条件があります。  

このようなイメージから VM をデプロイするには、イメージの契約条件に同意し、なおかつプログラムによるデプロイを有効にする必要があります。 これを行う必要があるのはサブスクリプションごとに 1 回だけです。 その後、イメージからプログラムを使って VM をデプロイするたびに、*購入プラン*のパラメーターを指定する必要があります。

以下のセクションでは、その方法について説明します。

* Marketplace イメージに追加のライセンス条件があるかどうかを調べる 
* プログラムで契約条件に同意する
* プログラムを使って VM を展開するときに、購入プランのパラメーターを入力する

