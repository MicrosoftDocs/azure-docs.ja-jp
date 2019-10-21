---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 09/18/2019
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: 0f528117b19a3f26b964dee7ae453fc184c8dddb
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123064"
---
Azure Compute では、特定のハードウェアの種類に分離される、単一顧客専用の仮想マシン サイズを提供します。  これらの仮想マシン サイズは、コンプライアンスや規制上の要件などの要素に関連するワークロードについて、他の顧客からの高いレベルの分離を必要とするワークロードに最適です。  お客様は、[入れ子になった仮想マシンの Azure サポート](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)を使用して、これらの分離された仮想マシンのリソースをさらに分割することもできます。

分離されたサイズを利用することで、お使いの仮想マシンがその特定のサーバー インスタンス上で実行されている唯一のマシンであることが保証されます。  現在の分離された仮想マシンのプランには、以下が含まれます。
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2
* Standard_D15_v2
* Standard_F72s_v2

利用可能な分離されたサイズごとの詳細については、[こちら](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)を参照してください。