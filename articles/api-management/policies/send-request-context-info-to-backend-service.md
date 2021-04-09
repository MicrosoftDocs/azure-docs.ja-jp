---
title: サンプルの API Management ポリシー - バックエンド サービスに要求コンテキスト情報を送信する
titleSuffix: Azure API Management
description: Azure API Management ポリシーのサンプル - バックエンド サービスに要求コンテキスト情報を送信する方法を示します。
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
ms.openlocfilehash: 7782af3c8a533ceb3a6d2bd3b412c21469f9a021
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92078818"
---
# <a name="send-request-context-information-to-the-backend-service"></a>バックエンド サービスに要求コンテキスト情報を送信する

この記事では、Azure API Management ポリシーのサンプルを示し、バックエンド サービスに要求コンテキスト情報を送信する方法を説明します。 ポリシー コードを設定または編集するには、[ポリシーの設定または編集](../set-edit-policies.md)に関するページで説明されている手順に従います。 他の例については、[ポリシーのサンプル](../policy-reference.md)に関するページをご覧ください。

## <a name="policy"></a>ポリシー

コードを **inbound** ブロックに貼り付けます。

[!code-xml[Main](../../../api-management-policy-samples/examples/Send request context information to the backend service.policy.xml)]

## <a name="next-steps"></a>次のステップ

APIM ポリシーの詳細については、以下をご覧ください。

+ [変換ポリシー](../api-management-transformation-policies.md)
+ [ポリシーのサンプル](../policy-reference.md)