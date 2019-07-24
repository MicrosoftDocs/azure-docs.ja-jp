---
title: Azure API Management ポリシーのサンプル - X-CSRF パターンの実装 | Microsoft Docs
description: Azure API Management ポリシーのサンプル - 多くの API で使用される X CSRF パターンを実装する方法を示します。 この例は SAP ゲートウェイに固有のものです。
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
ms.openlocfilehash: 2f4d26702443ef3113dad98cde1d13b292fe657a
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52870012"
---
# <a name="implement-x-csrf-pattern"></a>X CSRF パターンの実装

この記事では、多くの API で使用される X CSRF パターンを実装する方法を示す Azure API Management ポリシーのサンプルを示します。 この例は SAP ゲートウェイに固有です。 ポリシー コードを設定または編集するには、[ポリシーの設定または編集](../set-edit-policies.md)に関するページで説明されている手順に従います。 他の例については、[ポリシーのサンプル](../policy-samples.md)に関するページをご覧ください。

## <a name="policy"></a>ポリシー

コードを **inbound** ブロックに貼り付けます。

[!code-xml[Main](../../../api-management-policy-samples/examples/Get X-CSRF token from SAP gateway using send request.policy.xml)]

## <a name="next-steps"></a>次の手順

APIM ポリシーの詳細については、以下をご覧ください。

+ [変換ポリシー](../api-management-transformation-policies.md)
+ [ポリシーのサンプル](../policy-samples.md)

