---
title: 既存の可用性セットへの Azure VM の追加のサポート | Microsoft Docs
description: 既存の可用性セットへの Azure VM の追加のサポートについて説明します。
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 3954df389516aa7199022d713dc63d62dda961ae
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155455"
---
# <a name="supportability-of-adding-azure-vms-to-an-existing-availability-set"></a>既存の可用性セットへの Azure VM の追加のサポート

既存の可用性セットに新しい仮想マシン (VM) を追加するときに、制限されることがあります。 次の表では、同じ可用性セットに混在させることができる VM シリーズについて詳しく説明します。

異なる種類の VM の混在に関するサポート マトリックスを次に示します。

シリーズと可用性セット|第 2 の VM|A|Av2|D|Dv2|Dv3|
|---|---|---|---|---|---|---|
|第 1 の VM|||||||
|A||OK|OK|OK|OK|OK|
|Av2||OK|OK|OK|OK|OK|
|D||OK|OK|OK|OK|OK|
|Dv2||OK|OK|OK|OK|OK|
|Dv3||OK|OK|OK|OK|OK|

他のすべてのシリーズは、特定のハードウェアが必要なため、同じ可用性セットに存在することはできません。

専用 RDMA バックエンド ネットワークに関する要件のため、A8/A9 サイズの VM を混在させることはできません。
