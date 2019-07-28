---
author: kumudd
ms.service: load-balancer
ms.topic: include
ms.date: 11/09/2018
ms.author: kumud
ms.openlocfilehash: bf3aee30460e052db23cf9090f13e2af3b22628b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181598"
---
このシナリオでは、次のタスクを実行します。

* ポート 80 でネットワーク トラフィックを受信するロード バランサーを作成し、負荷分散されたトラフィックを仮想マシン "web1" と "web2" に送信する
* ロード バランサーの背後にある仮想マシンのリモート デスクトップ アクセス/ SSH 用の NAT 規則を作成する
* 正常性プローブを作成する

![ロード バランサーのシナリオ](./media/load-balancer-get-started-internet-scenario-include/scenario-classic.png)
