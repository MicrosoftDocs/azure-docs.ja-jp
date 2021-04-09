---
title: API management ポリシーのサンプル - 応答のキャッシュ期間を設定する
titleSuffix: Azure API Management
description: Azure API Management ポリシーのサンプル - バックエンドによって送信された Cache-Control ヘッダーの maxAge の値を使用して、応答のキャッシュ期間を設定する方法を示します。
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
ms.openlocfilehash: 088dfbcbfe00357c47de4662ac31b52e83d1d8ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92070981"
---
# <a name="set-response-cache-duration"></a>応答のキャッシュ期間を設定する

この記事では、Azure API Management ポリシーのサンプルを示します。このサンプルでは、バックエンドによって送信された Cache-Control ヘッダーの maxAge の値を使用して、応答のキャッシュ期間を設定する方法が示されます。 ポリシー コードを設定または編集するには、[ポリシーの設定または編集](../set-edit-policies.md)に関するページで説明されている手順に従います。 他の例については、[ポリシーのサンプル](../policy-reference.md)に関するページをご覧ください。

## <a name="policy"></a>ポリシー

コードを **inbound** ブロックに貼り付けます。

[!code-xml[Main](../../../api-management-policy-samples/examples/Set cache duration using response cache control header.policy.xml)]

## <a name="next-steps"></a>次のステップ

APIM ポリシーの詳細については、以下をご覧ください。

+ [変換ポリシー](../api-management-transformation-policies.md)
+ [ポリシーのサンプル](../policy-reference.md)