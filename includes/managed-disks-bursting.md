---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 04/27/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 0f85f2770b98507618d5cbfdec1b55f397c7f514
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101749890"
---
Azure には、ディスク記憶域の IOPS と帯域幅 (MB/秒) パフォーマンスを高めるバーストと呼ばれる機能が、仮想マシン (VM) とディスクの両方に用意されています。 VM とディスクのバーストを効果的に活用すれば、VM とディスクの両方でバースト パフォーマンスを高めることができます。

Azure VM のバーストとディスク リソースのバーストは互いに独立しています。 バースト対応の VM がなくても、アタッチされているバースト対応ディスクのバースト機能は利用できます。 同様に、バースト対応の VM にバースト対応のディスクがアタッチされていなくても、その VM のバースト機能は利用できます。