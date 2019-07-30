---
title: Azure Firewall ルール処理ロジック
description: Azure Firewall ルール処理ロジックの詳細
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/27/2018
ms.author: victorh
ms.openlocfilehash: 12d86793c0d75413559aad77c558c4adb7ac91af
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "64681584"
---
# <a name="azure-firewall-rule-processing-logic"></a>Azure Firewall ルール処理ロジック
Azure Firewall には、NAT ルール、ネットワーク ルール、およびアプリケーション ルールがあります。 これらのルールは、ルールの種類に応じて処理されます。


## <a name="network-rules-and-applications-rules"></a>ネットワーク ルールとアプリケーション ルール 
ネットワーク ルールが先に適用され、次にアプリケーション ルールが適用されます。 その後、これらのルールが終了します。 そのため、ネットワーク ルールで一致が見つかると、アプリケーション ルールは処理されません。  一致するネットワーク ルールが存在せず、パケットのプロトコルが HTTP/HTTPS の場合、パケットはアプリケーション ルールによって評価されます。 一致が見つからない場合、パケットはインフラストラクチャ ルール コレクションに対して評価されます。 それでも一致するものがない場合、パケットは既定では拒否されます。

## <a name="nat-rules"></a>NAT 規則
受信接続を有効にするには、宛先ネットワーク アドレス変換 (DNAT) を「[チュートリアル:Azure portal で Azure Firewall DNAT を使用して受信トラフィックをフィルター処理する](tutorial-firewall-dnat.md)」の説明に従って構成します。 DNAT ルールが最初に適用されます。 一致が見つかると、変換されたトラフィックを許可する暗黙的な対応するネットワーク ルールが追加されます。 この動作は、変換されたトラフィックに一致する拒否ルールを使用してネットワーク ルール コレクションを明示的に追加することで、オーバーライドすることができます。 これらの接続には、アプリケーション ルールは適用されません。


## <a name="next-steps"></a>次の手順

- [Azure Firewall のデプロイおよび構成](tutorial-firewall-deploy-portal.md)方法について説明します。