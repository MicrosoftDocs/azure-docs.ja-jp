---
title: Azure API Management ポリシーのサンプル - Google OAuth トークンを使用してアクセスを承認する | Microsoft Docs
description: Azure API Management ポリシーのサンプル - Google を OAuth トークン プロバイダーとして使用して、エンドポイントへのアクセスを承認する方法を示します。
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
ms.openlocfilehash: 7acc9071008937cd85c628878b385f1f53707e53
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70071926"
---
# <a name="authorize-access-using-google-oauth-token"></a>Google OAuth トークンを使用してアクセスを承認する

この記事では、Google を OAuth トークン プロバイダーとして使用して、エンドポイントへのアクセスを承認する方法を示す、Azure API Management ポリシーのサンプルを紹介します。 ポリシー コードを設定または編集するには、[ポリシーの設定または編集](../set-edit-policies.md)に関するページで説明されている手順に従います。 他の例については、[ポリシーのサンプル](../policy-samples.md)に関するページをご覧ください。

## <a name="policy"></a>ポリシー

コードを **inbound** ブロックに貼り付けます。

[!code-xml[Main](../../../api-management-policy-samples/examples/Simple Google OAuth validate-jwt.policy.xml)]

## <a name="next-steps"></a>次の手順

APIM ポリシーの詳細については、以下をご覧ください。

+ [変換ポリシー](../api-management-transformation-policies.md)
+ [ポリシーのサンプル](../policy-samples.md)

