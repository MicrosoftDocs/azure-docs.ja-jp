---
title: サンプルの API 管理ポリシー - ログ記録のために Stackify にエラーを送信する
titleSuffix: Azure API Management
description: Azure API Management ポリシーのサンプル - エラー ログ ポリシーを追加して、ログ記録のために Stackify にエラーを送信する　方法を示します。
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 89428e9a64c6d4ae78d333c0cf597531588b1638
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92072069"
---
# <a name="send-errors-to-stackify-for-logging"></a>ログ記録のために Stackify にエラーを送信する

この記事では、Azure API Management ポリシーのサンプルを示し、エラー ログ ポリシーを追加して、ログ記録のために Stackify にエラーを送信する　方法を説明します。 ポリシー コードを設定または編集するには、[ポリシーの設定または編集](../set-edit-policies.md)に関するページで説明されている手順に従います。 他の例については、[ポリシーのサンプル](../policy-reference.md)に関するページをご覧ください。

## <a name="policy"></a>ポリシー

コードを **on-error** ブロックに貼り付けます。

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>次のステップ

APIM ポリシーの詳細については、以下をご覧ください。

+ [変換ポリシー](../api-management-transformation-policies.md)
+ [ポリシーのサンプル](../policy-reference.md)