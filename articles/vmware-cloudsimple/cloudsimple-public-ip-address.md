---
title: Azure VMware Solution by CloudSimple - パブリック IP アドレス
description: Azure VMware Solution by CloudSimple のパブリック IP アドレスとその利点について説明します
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 147ab6b5ace4493e1a0b303c320cb2e81d829b29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "77024978"
---
# <a name="cloudsimple-public-ip-address-overview"></a>CloudSimple のパブリック IP アドレスの概要

パブリック IP アドレスによって、インターネット リソースで、プライベート クラウド リソースのプライベート IP アドレスへの受信方向の通信を行うことができます。 プライベート IP アドレスは、プライベート クラウド vCenter 上の仮想マシンまたはソフトウェア ロード バランサーです。 パブリック IP アドレスを使用して、プライベート クラウドで実行されているサービスをインターネットに公開できます。

パブリック IP アドレスは、割り当てを解除するまで、そのプライベート IP アドレス専用になります。 1 つのパブリック IP アドレスは、1 つのプライベート IP アドレスにのみ割り当てることができます。

パブリック IP アドレスに関連付けられたリソースでは、常に、インターネット アクセスに対してパブリック IP アドレスが使われます。 既定では、パブリック IP アドレスではアウトバウンドのインターネット アクセスのみが許可されます。  パブリック IP アドレスでの着信トラフィックは拒否されます。  インバウンド トラフィックを許可するには、特定のポートに対してそのパブリック IP アドレスのファイアウォール規則を作成します。

## <a name="benefits"></a>メリット

パブリック IP アドレスを使ってインバウンドの通信を行うと、以下のものが提供されます。

* 分散型サービス拒否 (DDoS) 攻撃に対する防御。 この保護は、パブリック IP アドレスに対して自動的に有効になります。
* トラフィックの常時監視と一般的なネットワーク レベル攻撃のリアルタイム軽減。 これらの防御は、Microsoft Online Services によって使われるものと同じ防御です。
* Azure グローバル ネットワークのスケール全体。 ネットワークを使って、リージョン間で攻撃トラフィックを分散および軽減できます。  

## <a name="next-steps"></a>次のステップ

* [パブリック IP アドレスを割り当てる](public-ips.md)方法を確認する