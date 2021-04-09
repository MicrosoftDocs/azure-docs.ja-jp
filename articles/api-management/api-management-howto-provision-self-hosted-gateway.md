---
title: Azure API Management でセルフホステッド ゲートウェイをプロビジョニングする | Microsoft Docs
description: Azure API Management でセルフホステッド ゲートウェイをプロビジョニングする方法について説明します。
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/31/2020
ms.author: apimpm
ms.openlocfilehash: e79248e16ca21ae84022f8ac7f280d93f489c6a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87050340"
---
# <a name="provision-a-self-hosted-gateway-in-azure-api-management"></a>Azure API Management でセルフホステッド ゲートウェイをプロビジョニングする

Azure API Management インスタンスでのゲートウェイ リソースのプロビジョニングは、セルフホステッド ゲートウェイをデプロイするための前提条件です。 この記事では、API Management でゲートウェイ リソースをプロビジョニングする手順について説明します。

## <a name="prerequisites"></a>前提条件

[Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関するクイックスタートを完了します

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="provision-a-self-hosted-gateway"></a>セルフホステッド ゲートウェイをプロビジョニングする

1. **[Deployment and infrastructure]\(デプロイとインフラストラクチャ\)** の下から **[ゲートウェイ]** を選択します。
2. **[+ 追加]** をクリックします。
3. ゲートウェイの **[名前]** と **[リージョン]** を入力します。
> [!TIP]
> **[リージョン]** には、このゲートウェイ リソースに関連付けられるゲートウェイ ノードの目的の場所を指定します。 これは、Azure リソースに関連付けられた同様のプロパティと意味的には同じですが、任意の文字列値を割り当てることができます。

4. 必要に応じて、ゲートウェイ リソースの **[説明]** を入力します。
5. 必要に応じて、 **[API]** の下にある **[+]** を選択して、1 つ以上の API をこのゲートウェイ リソースに関連付けます。
> [!IMPORTANT]
> 既定では、既存の API はいずれも新しいゲートウェイ リソースに関連付けられません。 そのため、新しいゲートウェイを使用してそれらを呼び出そうとすると、`404 Resource Not Found` 応答が返されます。

6. **[追加]** をクリックします。

これで、API Management インスタンスにゲートウェイ リソースがプロビジョニングされました。 ゲートウェイのデプロイに進むことができます。

## <a name="next-steps"></a>次のステップ

* セルフホステッド ゲートウェイの詳細については、[Azure API Management のセルフホステッド ゲートウェイの概要](self-hosted-gateway-overview.md)に関する記事を参照してください
* [Kubernetes にセルフホステッド ゲートウェイをデプロイする](how-to-deploy-self-hosted-gateway-kubernetes.md)方法について確認します
* [Docker にセルフホステッド ゲートウェイをデプロイする](how-to-deploy-self-hosted-gateway-docker.md)方法について確認します
