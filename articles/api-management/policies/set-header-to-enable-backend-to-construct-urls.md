---
title: Azure API Management ポリシーのサンプル - 転送ヘッダーを追加する | Microsoft Docs
description: Azure API Management ポリシーのサンプル - 受信要求に転送ヘッダーを追加して、バックエンド API が適切な URL を構築できるようにする方法を説明します。
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
ms.openlocfilehash: b857d1780e9734ce891ce2c0ce4bedf50dfe13e9
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52871457"
---
# <a name="add-a-forwarded-header"></a>転送ヘッダーの追加

この記事では、受信要求に転送ヘッダーを追加して、バックエンド API が適切な URL を構築できるようにする方法を示す Azure API Management ポリシーのサンプルを紹介します。 ポリシー コードを設定または編集するには、[ポリシーの設定または編集](../set-edit-policies.md)に関するページで説明されている手順に従います。 他の例については、[ポリシーのサンプル](../policy-samples.md)に関するページをご覧ください。

## <a name="code"></a>コード

コードを **inbound** ブロックに貼り付けます。

[!code-xml[Main](../../../api-management-policy-samples/examples/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>次の手順

APIM ポリシーの詳細については、以下をご覧ください。

+ [変換ポリシー](../api-management-transformation-policies.md)
+ [ポリシーのサンプル](../policy-samples.md)
