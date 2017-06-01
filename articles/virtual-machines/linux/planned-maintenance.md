---
title: "Azure で Linux VM の計画メンテナンスのスケジュールを設定する方法 | Microsoft Docs"
description: "Azure の計画メンテナンスの概要と、それが Azure で実行されている Linux 仮想マシンに及ぼす影響について説明します。"
services: virtual-machines-linux
documentationcenter: 
author: zivr
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/27/2017
ms.author: zivr
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 28c2fb5a67eca0c5ab2f0299bba7c11375e10558
ms.contentlocale: ja-jp
ms.lasthandoff: 04/03/2017


---

# <a name="planned-maintenance-for-linux-virtual-machines"></a>Linux 仮想マシンの計画メンテナンス 

Microsoft Azure は、世界各地で定期的に更新を行い、仮想マシンの基盤となるホスト インフラストラクチャの信頼性、パフォーマンス、セキュリティの向上に努めています。 そうした更新は、ホスティング環境のソフトウェア コンポーネント (ホストに展開されている OS、ハイパーバイザー、各種エージェント) の修正から、ネットワーク コンポーネントのアップグレード、ハードウェアの使用停止にいたるまで、さまざまな範囲に及んでいます。

これらの更新のほとんどは、ホストされている仮想マシンやクラウド サービスに影響を及ぼすことなく実行されます。

ただし、更新がホストされた仮想マシンに影響を与える場合があります。

-   インプレース VM 移行を使用する VM 保持メンテナンスでは、メンテナンス中に仮想マシンを再起動しない更新クラスについて説明しています。

-   VM 再起動メンテナンスでは、ホストされた仮想マシンの再起動または再配置が必要です。

このページでは、Microsoft Azure による計画メンテナンスの実行について説明しています。 計画外のイベント (停止) の詳細については、「 [仮想マシンの可用性管理](../linux/manage-availability.md)」をご覧ください。
