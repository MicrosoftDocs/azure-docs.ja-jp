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
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: e525029ae8eab086d11126a4e18958423e207aa1
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067499"
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
