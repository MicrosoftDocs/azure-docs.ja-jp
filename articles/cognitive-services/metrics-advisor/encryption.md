---
title: Metrics Advisor サービスの暗号化
titleSuffix: Azure Cognitive Services
description: Metrics Advisor サービスによる保存データの暗号化。
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 5d41500a9c53e38cd36f0feba602e0e1baa5da2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "92909745"
---
# <a name="metrics-advisor-service-encryption-of-data-at-rest"></a>Metrics Advisor サービスによる保存データの暗号化

Metrics Advisor サービスでは、クラウドに永続化されるときに、データが自動的に暗号化されます。 Metrics Advisor サービスの暗号化によってデータは保護され、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> カスタマー マネージド キーは、E0 価格レベルでのみ利用できます。 カスタマー マネージド キーを使用できるようにするには、[Metrics Advisor サービス カスタマー マネージド キー要求フォーム](https://aka.ms/cogsvc-cmk)に記入して送信します。 要求の状態について連絡を差し上げるまで、約 3 から 5 営業日かかります。 要求によっては、お客様は待ち行列に登録され、スペースが利用できるようになってから承認される場合があります。 Metrics Advisor サービスでの CMK の使用が承認されたら、新しい Metrics Advisor リソースを作成し、価格レベルとして E0 を選択する必要があります。 E0 価格レベルで Metrics Advisor リソースを作成したら、Azure Key Vault を使用してマネージド ID を設定できます。

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>次のステップ

* [Metrics Advisor サービス カスタマー マネージド キー要求フォーム](https://aka.ms/cogsvc-cmk)
* [Azure Key Vault の詳細を確認する](../../key-vault/general/overview.md)