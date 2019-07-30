---
title: Microsoft Azure FXT Edge Filer ユニットをシャットダウンする方法
description: Azure FXT Edge Filer ノードの起動と安全なシャットダウンの手順
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: v-erkell
ms.openlocfilehash: 6921e7a52e43a63055b59242c02cc6ca3b8c5313
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620096"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>Azure FXT Edge Filer ハードウェアの電源を安全に切る方法

個々のノードの電源をオンにするために物理的な電源ボタンを使用することはできますが、通常の状況では、電源ボタンを使用してユニットをシャットダウンしてはいけません。

Azure FXT Edge Filer ノードがクラスターの一部として使用された後は、クラスターのコントロール パネル ソフトウェアを使用して、ハードウェアをシャットダウンする必要があります。 

> [!NOTE] 
> データの損失や破損を避けるには、必ずコントロール パネル ソフトウェアを使用して、Azure FXT Edge Filer をシャットダウンしてください。 Microsoft カスタマー サービスおよびサポートから指示されない限り、物理的な電源ボタンを使用してシャットダウンしないでください。
> 
> 電気的非常時には、電源コードを外すか、データ センターの電気切断メカニズムを使用します。

## <a name="shut-down-a-node-from-the-control-panel"></a>コントロール パネルからノードをシャットダウンする

Azure FXT Edge Filer ノードの電源を安全に切るには、次の手順に従います。

1. クラスターのコントロール パネルにサインインします。 (「[[設定] ページを開く](fxt-cluster-create.md#open-the-settings-pages)」の手順)
1. **[設定]** タブをクリックし、 **[クラスター]**  >  **[FXT Nodes]\(FXT ノード\)** ページを読み込みます。
1. クラスター ノードの一覧で、シャットダウンするノードを見つけます。 **[アクション]** 列の **[Power down]\(電源切断\)** ボタンをクリックします。 
1. 数分待ちます。 ノードがシャットダウンし、電源が切れます。

## <a name="next-steps"></a>次の手順

* LED およびその他のインジケーターについては、「[Monitor Azure FXT Edge Filer hardware status (Azure FXT Edge Filer ハードウェアの状態の監視)](fxt-monitor.md)」を参照してください。
* Azure FXT Edge Filer の電源の詳細については、「[電源ケーブルを接続する](fxt-network-power.md#connect-power-cables)」を参照してください。
