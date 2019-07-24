---
title: Azure API Management ポリシーのサンプル - 関連付け ID を含むヘッダーを追加する | Microsoft Docs
description: Azure API Management ポリシーのサンプル - 関連付け ID を含むヘッダーを受信要求に追加する方法を示します。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 126701674b6dc529404f5a7854cda9b31c336170
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52868431"
---
# <a name="add-a-header-containing-a-correlation-id"></a>関連付け ID を含むヘッダーの追加

この記事では、関連付け ID を含むヘッダーを受信要求に追加する方法を示す Azure API Management ポリシーのサンプルを示します。 ポリシー コードを設定または編集するには、[ポリシーの設定または編集](../set-edit-policies.md)に関するページで説明されている手順に従います。 他の例については、[ポリシーのサンプル](../policy-samples.md)に関するページをご覧ください。

## <a name="policy"></a>ポリシー

コードを **inbound** ブロックに貼り付けます。

[!code-xml[Main](../../../api-management-policy-samples/examples/Add correlation id to inbound request.policy.xml)]

## <a name="next-steps"></a>次の手順

APIM ポリシーの詳細については、以下をご覧ください。

+ [変換ポリシー](../api-management-transformation-policies.md)
+ [ポリシーのサンプル](../policy-samples.md)

