---
title: Custom Vision による保存データの暗号化
titleSuffix: Azure Cognitive Services
description: Custom Vision による保存データの暗号化。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: egeaney
ms.openlocfilehash: 85cf251db69d33f02e928eaea6a848f20ae7d923
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310293"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>Custom Vision による保存データの暗号化

Azure Custom Vision では、クラウドに永続化されるときにデータが自動的に暗号化されます。 Azure Custom Vision 暗号化によってデータは保護され、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> カスタマー マネージド キーは、2020年 5 月 11 日より後に作成されたリソースのみに使用できます。 Custom Vision と共に CMK を使用するには、新しい Custom Vision リソースを作成する必要があります。 リソースが作成されたら、Azure Key Vault を使用してマネージド ID を設定できます。

## <a name="regional-availability"></a>リージョン別の提供状況

カスタマー マネージド キーは、現在、次のリージョンで利用できます。

* 米国中南部
* 米国西部 2
* 米国東部
* US Gov バージニア州

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>次のステップ

* CMK がサポートされているサービスの完全な一覧については、[Cognitive Services 用のカスタマー マネージド キー](../encryption/cognitive-services-encryption-keys-portal.md)に関するページを参照してください
* [Azure Key Vault とは](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Cognitive Services カスタマー マネージド キー要求フォーム](https://aka.ms/cogsvc-cmk)
