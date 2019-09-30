---
title: Azure シリアル コンソールの電源オプション | Microsoft Docs
description: Azure シリアル コンソール内で使用可能な VM 電源オプション
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/6/2019
ms.author: alsin
ms.openlocfilehash: f17b96ad880742cf1232b074e4398f3b1d15e5ba
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129443"
---
# <a name="power-options-available-from-the-azure-serial-console"></a>Azure シリアル コンソールから使用可能な電源オプション

Azure シリアル コンソールには、VM または仮想マシン スケール セットでの電源管理用の強力なツールがいくつか用意されています。 これらの電源管理オプションは混乱を招く可能性があるため、このドキュメントでは、各ツールの概要とその用途について説明します。

シリアル コンソール機能 | 説明 | ユース ケース
:----------------------|:------------|:---------
VM の再起動 | VM または仮想マシン スケール セット インスタンスの正常な再起動。 この操作は、[概要] ページで使用できる再起動機能の呼び出しと同じです。 | ほとんどの場合、このオプションは、VM を再起動するための最初のツールであるはずです。 シリアル コンソール接続に短時間の中断が発生し、VM が再起動されるとすぐに自動的に再開されます。
VM をリセットする | Azure プラットフォームによる VM または仮想マシン スケール セットの強制的な電源の入れ直し。 | このオプションは、その現在の状態に関係なく、オペレーティング システムを直ちに再起動するために使用されます。 この操作は正常なものではないため、データが失われたり破損したりするリスクがあります。 シリアル コンソール接続が中断されることはないため、起動時の早い段階でコマンドを送信する場合に便利なことがあります (たとえば、Linux VM では GRUB、Windows VM ではセーフ モードを開始する場合)。
SysRq - 再起動 (b) | ゲストの再起動を強制するシステム要求。 | この機能は Linux オペレーティング システムにのみ適用され、オペレーティング システムで [SysRq が有効になっている](./serial-console-nmi-sysrq.md#system-request-sysrq)必要があります。 オペレーティング システムが SysRq 用に適切に構成されている場合、このコマンドを実行すると OS が再起動します。
NMI (マスク不可能割り込み) | オペレーティング システムに配信される割り込みコマンド。 | この操作は [Windows](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) VM と [Linux](./serial-console-nmi-sysrq.md#non-maskable-interrupt-nmi) VM の両方で使用でき、NMI が有効になっている必要があります。 NMI を送信すると、通常はオペレーティング システムがクラッシュします。 ダンプ ファイルを作成し、NMI の受信時に再起動するようにオペレーティング システムを構成できます。これは低レベルのデバッグに役立つことがあります。

## <a name="next-steps"></a>次の手順
* [Linux VM 用 Azure シリアル コンソール](./serial-console-linux.md)についての詳細を参照する
* [Windows VM 用 Azure シリアル コンソール](./serial-console-windows.md)についての詳細を参照する