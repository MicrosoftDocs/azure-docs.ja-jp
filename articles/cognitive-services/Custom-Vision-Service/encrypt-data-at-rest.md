---
title: Custom Vision による保存データの暗号化
titleSuffix: Azure Cognitive Services
description: Microsoft からは Microsoft が管理する暗号化キーが提供されます。また、カスタマー マネージド キー (CMK) と呼ばれている独自のキーで自分の Cognitive Services サブスクリプションを管理することをお客様に許可します。 この記事では、Custom Vision での保存データの暗号化と、CMK を有効化および管理する方法について説明します。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 822a4249b6ed054f36605d0367803da68bab090b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100652260"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>Custom Vision による保存データの暗号化

Azure Custom Vision では、クラウドに永続化されるときにデータが自動的に暗号化されます。 Azure Custom Vision 暗号化によってデータは保護され、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> カスタマー マネージド キーは、2020年 5 月 11 日より後に作成されたリソースのみに使用できます。 Custom Vision と共に CMK を使用するには、新しい Custom Vision リソースを作成する必要があります。 リソースが作成されたら、Azure Key Vault を使用してマネージド ID を設定できます。

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>次のステップ

* CMK がサポートされているサービスの完全な一覧については、[Cognitive Services 用のカスタマー マネージド キー](../encryption/cognitive-services-encryption-keys-portal.md)に関するページを参照してください
* [Azure Key Vault とは](../../key-vault/general/overview.md)
* [Cognitive Services カスタマー マネージド キー要求フォーム](https://aka.ms/cogsvc-cmk)