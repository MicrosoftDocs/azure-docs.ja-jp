---
title: "既存の可用性セットへの Azure VM の追加のサポート | Microsoft Docs"
description: "既存の可用性セットへの Azure VM の追加のサポートについて説明します。"
services: virtual-machines-linux
documentationcenter: 
author: Deland-Han
manager: cshepard
editor: 
ms.assetid: 
ms.service: virtual-machines
ms.workload: virtual-machines
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/15/2017
ms.author: delhan
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: f07108f5d7a792dcc9aae8a4fe889878db168bc6
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

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
