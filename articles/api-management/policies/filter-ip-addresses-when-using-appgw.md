---
title: API Management ポリシーのサンプル - Application Gateway を使用する際に IP アドレスでフィルター処理する
titleSuffix: Azure API Management
description: Azure API Management ポリシーのサンプル - アプリケーション ゲートウェイを使用する際に、要求 IP アドレスでフィルター処理する方法を示します。
services: api-management
documentationcenter: ''
author: jftl6y
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: joscot
ms.custom: fasttrack-new
ms.openlocfilehash: 8249cc543c6334841c8e5152d5d1ceb84d4097dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92076115"
---
# <a name="filter-on-request-ip-address-when-using-an-application-gateway"></a>アプリケーション ゲートウェイを使用する際に要求 IP アドレスでフィルター処理する

この記事では、Azure API Management ポリシーのサンプルを示します。これは、アプリケーション ゲートウェイまたはその他の仲介手段を通じて API Management インスタンスにアクセスする際に、要求 IP アドレスでフィルター処理する方法を示します。 ポリシー コードを設定または編集するには、[ポリシーの設定または編集](../set-edit-policies.md)に関するページで説明されている手順に従います。 他の例については、[ポリシーのサンプル](../policy-reference.md)に関するページをご覧ください。

## <a name="policy"></a>ポリシー

コードを **inbound** ブロックに貼り付けます。

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter on IP Address when using Application Gateway.policy.xml)]

## <a name="next-steps"></a>次のステップ

APIM ポリシーの詳細については、以下をご覧ください。

+ [アクセス制限ポリシー](../api-management-access-restriction-policies.md)
+ [ポリシーのサンプル](../policy-reference.md)