---
title: API Management ポリシーのサンプル - Shared Access Signature を生成する
titleSuffix: Azure API Management
description: Azure API Management ポリシーのサンプル - 式を使用して Shared Access Signature を生成し、rewrite-uri ポリシーを使用して要求を Azure ストレージに転送する方法を示します。
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
ms.openlocfilehash: 62a6e8c9fdec3b30bd193e9887d7e0cb7926e73e
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2020
ms.locfileid: "86243003"
---
# <a name="generate-shared-access-signature"></a>Shared Access Signature の生成

この記事では、式を使用して [Shared Access Signature](../../storage/common/storage-sas-overview.md) を生成し、rewrite-uri ポリシーを使用して Azure ストレージに要求を転送する方法を示す Azure API Management ポリシーのサンプルを示します。 ポリシー コードを設定または編集するには、[ポリシーの設定または編集](../set-edit-policies.md)に関するページで説明されている手順に従います。 他の例については、[ポリシーのサンプル](../policy-samples.md)に関するページをご覧ください。

## <a name="policy"></a>ポリシー

コードを **inbound** ブロックに貼り付けます。

[!code-xml[Main](../../../api-management-policy-samples/examples/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>次のステップ

APIM ポリシーの詳細については、以下をご覧ください。

+ [変換ポリシー](../api-management-transformation-policies.md)
+ [ポリシーのサンプル](../policy-samples.md)
