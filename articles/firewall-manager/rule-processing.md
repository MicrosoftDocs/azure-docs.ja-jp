---
title: Azure Firewall Manager ルール処理ロジック
description: Azure Firewall ルール処理ロジックの詳細
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 9184bf7baa85420e067edb4c0aafccb7e6711225
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86512182"
---
# <a name="azure-firewall-rule-processing-logic"></a>Azure Firewall ルール処理ロジック

Azure Firewall には、NAT ルール、ネットワーク ルール、およびアプリケーション ルールがあります。 これらのルールは、ルールの種類に応じて処理されます。

## <a name="network-rules-and-applications-rules"></a>ネットワーク ルールとアプリケーション ルール

ネットワーク ルールが先に適用され、次にアプリケーション ルールが適用されます。 その後、これらのルールが終了します。 そのため、ネットワーク ルールで一致が見つかると、アプリケーション ルールは処理されません。  どのネットワーク ルールも一致せず、パケットのプロトコルが HTTP/HTTPS の場合、パケットはアプリケーション ルールによって評価されます。 一致が見つからない場合、パケットはインフラストラクチャ ルール コレクションに対して評価されます。 それでも一致するものがない場合、パケットは既定で拒否されます。

## <a name="nat-rules"></a>NAT 規則

受信接続を有効にするには、宛先ネットワーク アドレス変換 (DNAT) を「[チュートリアル:Azure portal で Azure Firewall DNAT を使用して受信トラフィックをフィルター処理する](../firewall/tutorial-firewall-dnat.md)」の説明に従って構成します。 DNAT ルールが最初に適用されます。 一致が見つかると、変換されたトラフィックを許可する暗黙的な対応するネットワーク ルールが追加されます。 この動作は、変換されたトラフィックに一致する拒否ルールを使用してネットワーク ルール コレクションを明示的に追加することで、オーバーライドすることができます。 これらの接続には、アプリケーション ルールは適用されません。

## <a name="inherited-rules"></a>継承されたルール

親ポリシーから継承されたネットワーク ルール コレクションは、新しいポリシーの一部として定義されているネットワーク ルール コレクションより常に優先されます。 この同じロジックがアプリケーション ルール コレクションにも適用されます。 ただし、ネットワーク ルール コレクションは、継承に関係なく、常にアプリケーション ルール コレクションより先に処理されます。

既定では、ポリシーは親ポリシーの脅威インテリジェンス モードを継承します。 これをオーバーライドするには、ポリシー設定ページで脅威インテリジェンス モードを別の値に設定します。 より厳密な値でオーバーライドすることのみできます。 たとえば、親ポリシーが *[警告のみ]* に設定されている場合、このローカル ポリシーを *[警告して拒否]* に構成することはできますが、オフにすることはできません。

## <a name="next-steps"></a>次のステップ

- [Azure Firewall Manager の詳細を確認する](overview.md)