---
title: サンプルの Azure API Management ポリシー - ゲートウェイとバックエンド間の承認に OAuth2 を使用する
titleSuffix: Azure API Management
description: Azure API Management ポリシーのサンプル - ゲートウェイとバックエンド間の承認に OAuth2 を使用する方法を示します。 AAD からアクセス トークンを取得してバックエンドに転送する方法を示します。
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
ms.openlocfilehash: 0f5a10eb2ebc3b3a7c527dd718e37faf03c927bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92076098"
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>ゲートウェイとバックエンド間の承認に OAuth2 を使用する

この記事では、Azure API Management ポリシーのサンプルを示し、ゲートウェイとバックエンド間の承認に OAuth2 を使用する方法を説明します。 AAD からアクセス トークンを取得してバックエンドに転送する方法を示します。 

ポリシー コードを設定または編集するには、[ポリシーの設定または編集](../set-edit-policies.md)に関するページで説明されている手順に従います。 他の例については、[ポリシーのサンプル](../policy-reference.md)に関するページをご覧ください。

次のスクリプトでは、{{プロパティ}} で示されているプロパティを使います。 プロパティの詳細および API Management のポリシーでプロパティを使う方法については、[こちら](../api-management-howto-properties.md)のトピックをご覧ください。
 
## <a name="policy"></a>ポリシー

コードを **inbound** ブロックに貼り付けます。

[!code-xml[Main](../../../api-management-policy-samples/examples/Get OAuth2 access token from AAD and forward it to the backend.policy.xml)]
  
## <a name="next-steps"></a>次のステップ

APIM ポリシーの詳細については、以下をご覧ください。

+ [変換ポリシー](../api-management-transformation-policies.md)
+ [ポリシーのサンプル](../policy-reference.md)