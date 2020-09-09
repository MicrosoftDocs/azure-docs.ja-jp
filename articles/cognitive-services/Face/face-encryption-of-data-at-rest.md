---
title: Face サービスによる保存データの暗号化
titleSuffix: Azure Cognitive Services
description: Face サービスによる保存データの暗号化。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 33495dd5b092cb51b3421e7204f3b529077d63b3
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/03/2020
ms.locfileid: "84309035"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Face サービスによる保存データの暗号化

Face サービスでは、クラウドに永続化されるときにデータが自動的に暗号化されます。 Face サービスの暗号化によってデータは保護され、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> カスタマー マネージド キーは、E0 価格レベルでのみ利用できます。 カスタマー マネージド キーを使用できるようにするには、[Face サービス カスタマー マネージド キー要求フォーム](https://aka.ms/cogsvc-cmk)に記入して送信します。 要求の状態について連絡を差し上げるまで、約 3 から 5 営業日かかります。 要求によっては、お客様は待ち行列に登録され、スペースが利用できるようになってから承認される場合があります。 Face サービスでの CMK の使用が承認されたら、新しい Face リソースを作成し、価格レベルとして E0 を選択する必要があります。 E0 価格レベルで Face リソースを作成したら、Azure Key Vault を使用してマネージド ID を設定できます。

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
