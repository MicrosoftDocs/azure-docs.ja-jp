---
title: "Azure での Windows VM の計画的なメンテナンス | Microsoft Docs"
description: "Azure の計画的なメンテナンスの概要と、それが Azure で実行されている Windows 仮想マシンに及ぼす影響について説明します。"
services: virtual-machines-windows
documentationcenter: 
author: zivr
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: eb4b92d8-be0f-44f6-a6c3-f8f7efab09fe
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: zivr
ms.translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 198c1804b342b8faf406a08eff7bc42994b9dd0d
ms.contentlocale: ja-jp
ms.lasthandoff: 03/31/2017


---
# <a name="planned-maintenance-for-windows-virtual-machines"></a>Windows 仮想マシンの計画的なメンテナンス 

Microsoft Azure は、世界各地で定期的に更新を行い、仮想マシンの基盤となるホスト インフラストラクチャの信頼性、パフォーマンス、セキュリティの向上に努めています。 そうした更新は、ホスティング環境のソフトウェア コンポーネント (ホストに展開されている OS、ハイパーバイザー、各種エージェント) の修正から、ネットワーク コンポーネントのアップグレード、ハードウェアの使用停止にいたるまで、さまざまな範囲に及んでいます。

これらの更新のほとんどは、ホストされている仮想マシンやクラウド サービスに影響を及ぼすことなく実行されます。

ただし、更新がホストされた仮想マシンに影響を与える場合があります。

-   インプレース VM 移行を使用する VM 保持メンテナンスでは、メンテナンス中に仮想マシンを再起動しない更新クラスについて説明しています。

-   再起動メンテナンスでは、ホストされた仮想マシンの再起動または再配置が必要です。

このページでは、Microsoft Azure による計画的なメンテナンスの実行について説明しています。 計画外のイベント (停止) の詳細については、「[Azure での Windows 仮想マシンの可用性の管理](manage-availability.md)」を参照してください。
