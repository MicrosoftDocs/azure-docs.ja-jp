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
ms.openlocfilehash: d14c18ee77662f3803281413f9a844d10d245933
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "88011034"
---
Azure には、Virtual Machines とディスクの両方でディスク記憶域の IOPS と帯域幅 (MB/秒) パフォーマンスを高めるバーストと呼ばれる機能が用意されています。 バーストは、予期しないディスク トラフィックの処理やバッチ ジョブの処理などさまざまなシナリオで役立ちます。 VM とディスク レベルのバーストを効果的に活用すれば、高いベースライン パフォーマンスとバースト パフォーマンスを VM とディスクの両方で実現できます。 そうすることで、高いベースライン パフォーマンスとバースト パフォーマンスを VM とディスクの両方で実現することができます。 

ディスクと VM でのバーストは、互いに独立していることに注意してください。 バーストするディスクがある場合に、バーストする VM においてディスクのバーストを許可する必要はありません。 バーストする VM がある場合に、バーストするディスクにおいて VM のバーストを許可する必要はありません。 
