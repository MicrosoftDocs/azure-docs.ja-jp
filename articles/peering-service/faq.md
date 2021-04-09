---
title: Azure Peering Service に関する FAQ
description: Microsoft Azure Peering Service に関する FAQ の詳細
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 95ce90dbbf47ffe527fe6f25704d9cd28b834ea9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026697"
---
# <a name="peering-service-faq"></a>Peering Service に関する FAQ

この記事では、Azure Peering Service についてよく寄せられる質問について説明します。


**Q.ターゲットとなる顧客はどのような顧客ですか?**

A. ターゲットとなるお客様は、トランスポートとしてインターネットを使用して Microsoft クラウドに接続する企業です。

**Q.複数のプロバイダーで Peering Service にサインアップできますか?** 

A. はい。お客様は、同じリージョンまたは異なるリージョンにある複数のプロバイダーで Peering Service にサインアップできます。ただし、同じプレフィックスの場合はできません。

**Q.顧客は地理的リージョンごとにサイトに対して固有の ISP を選択できますか?**

A. はい、できます。 お客様のビジネスや運用上のニーズに合ったパートナー ISP を選択してください。

**Q.Microsoft Edge PoP とは何ですか?**

A. これは、Microsoft が他のネットワークと相互接続している物理的な場所です。 Microsoft Edge PoP の場所では、Azure Front Door や Azure CDN などのサービスがホストされます。 詳細については、[Azure CDN](../cdn/cdn-features.md) に関するページを参照してください。

## <a name="peering-service-unique-characteristics"></a>Peering Service: 固有の特性

**Q.Peering Service と通常のインターネット アクセスの違いは何ですか?**

A. Microsoft Peering Service に登録されているパートナーは、Microsoft と協力して、Microsoft サービスへの最適化されたルーティングと信頼性の高い接続を提供します。  

**Q.Peering Service と ExpressRoute の違いは何ですか?**

A. Azure ExpressRoute は、1 つまたは複数のお客様の場所からのプライベートの専用接続です。 Peering Service は最適化されたパブリック接続を提供し、プライベート接続をサポートしていませんが、ローカルのインターネット ブレークアウトに対して最適化された接続も提供します。

## <a name="next-steps"></a>次のステップ

- Peering Service の詳細については、[Peering Service の概要](about.md)に関するページを参照してください。
- サービス プロバイダーを検索するには、[Peering Service のパートナーと場所](location-partners.md)に関するページを参照してください。
- Peering Service 接続をオンボードする場合は、[Peering Service のオンボード](onboarding-model.md)に関するページを参照してください。
- 接続を登録する方法については、[Azure portal を使用した Peering Service 接続の登録](azure-portal.md)に関するページを参照してください。
- テレメトリを測定する方法については、[接続のテレメトリの測定](measure-connection-telemetry.md)に関するページを参照してください。