---
title: Content Moderator による保存データの暗号化
titleSuffix: Azure Cognitive Services
description: Content Moderator による保存データの暗号化。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 1401108a594e30790e842ec379724603f11d493f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100524085"
---
# <a name="content-moderator-encryption-of-data-at-rest"></a>Content Moderator による保存データの暗号化

Content Moderator を使うと、クラウドに永続化されるデータが自動的に暗号化されるので、組織のセキュリティとコンプライアンスの目標を達成するのに役立ちます。

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> カスタマー マネージド キーは、E0 価格レベルでのみ利用できます。 カスタマー マネージド キーを使用できるようにするには、[Content Moderator カスタマー マネージド キー要求フォーム](https://aka.ms/cogsvc-cmk)に記入して送信します。 要求の状態について連絡を差し上げるまで、約 3 から 5 営業日かかります。 要求によっては、お客様は待ち行列に登録され、スペースが利用できるようになってから承認される場合があります。 Content Moderator サービスでの CMK の使用が承認されたら、新しい Content Moderator リソースを作成し、価格レベルとして E0 を選択する必要があります。 E0 価格レベルで Content Moderator リソースを作成したら、Azure Key Vault を使用してマネージド ID を設定できます。

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="enable-data-encryption-for-your-content-moderator-team"></a>Content Moderator チーム用にデータの暗号化を有効にする

Content Moderator レビュー チーム用にデータの暗号化を有効にするには、「[クイック スタート: Web 上で Content Moderator を試す](quick-start.md#create-a-review-team)」をご覧ください。  

> [!NOTE]
> Content Moderator E0 価格レベルの "_リソース ID_" を指定する必要があります。

## <a name="next-steps"></a>次のステップ

* CMK がサポートされているサービスの完全な一覧については、[Cognitive Services 用のカスタマー マネージド キー](../encryption/cognitive-services-encryption-keys-portal.md)に関するページを参照してください
* [Azure Key Vault とは](../../key-vault/general/overview.md)
* [Cognitive Services カスタマー マネージド キー要求フォーム](https://aka.ms/cogsvc-cmk)