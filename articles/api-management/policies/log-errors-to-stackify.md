---
title: Azure API Management ポリシーのサンプル - ログ記録のために Stackify にエラーを送信する | Microsoft Docs
description: Azure API Management ポリシーのサンプル - エラー ログ ポリシーを追加して、ログ記録のために Stackify にエラーを送信する　方法を示します。
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
ms.openlocfilehash: 07cc83830fe2d467c611622bb66dfbb8c9429c2d
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52864878"
---
# <a name="send-errors-to-stackify-for-logging"></a>ログ記録のために Stackify にエラーを送信する

この記事では、Azure API Management ポリシーのサンプルを示し、エラー ログ ポリシーを追加して、ログ記録のために Stackify にエラーを送信する　方法を説明します。 ポリシー コードを設定または編集するには、[ポリシーの設定または編集](../set-edit-policies.md)に関するページで説明されている手順に従います。 他の例については、[ポリシーのサンプル](../policy-samples.md)に関するページをご覧ください。

## <a name="policy"></a>ポリシー

コードを **on-error** ブロックに貼り付けます。

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>次の手順

APIM ポリシーの詳細については、以下をご覧ください。

+ [変換ポリシー](../api-management-transformation-policies.md)
+ [ポリシーのサンプル](../policy-samples.md)

