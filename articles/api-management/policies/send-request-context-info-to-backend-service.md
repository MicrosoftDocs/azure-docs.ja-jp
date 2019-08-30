---
title: Azure API Management ポリシーのサンプル - バックエンド サービスに要求コンテキスト情報を送信する | Microsoft Docs
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
ms.openlocfilehash: cc770ff700155f8ab32bbbd6737c9dad7bc6e664
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067525"
---
# <a name="send-request-context-information-to-the-backend-service"></a>バックエンド サービスに要求コンテキスト情報を送信する

この記事では、Azure API Management ポリシーのサンプルを示し、バックエンド サービスに要求コンテキスト情報を送信する方法を説明します。 ポリシー コードを設定または編集するには、[ポリシーの設定または編集](../set-edit-policies.md)に関するページで説明されている手順に従います。 他の例については、[ポリシーのサンプル](../policy-samples.md)に関するページをご覧ください。

## <a name="policy"></a>ポリシー

コードを **inbound** ブロックに貼り付けます。

[!code-xml[Main](../../../api-management-policy-samples/examples/Send request context information to the backend service.policy.xml)]

## <a name="next-steps"></a>次の手順

APIM ポリシーの詳細については、以下をご覧ください。

+ [変換ポリシー](../api-management-transformation-policies.md)
+ [ポリシーのサンプル](../policy-samples.md)

