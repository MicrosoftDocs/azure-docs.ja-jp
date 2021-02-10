---
title: Azure Cosmos DB のコンプライアンス
description: この記事では、Azure Cosmos DB のコンプライアンス認証の範囲について説明します。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 512accd274da35c1f747f2ef185f0b3eefa4756d
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2020
ms.locfileid: "94636794"
---
# <a name="compliance-in-azure-cosmos-db"></a>Azure Cosmos DB のコンプライアンス 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB は、すべての Azure リージョンで利用できます。 Microsoft では、5 つの異なる Azure クラウド環境をお客様が利用できるようにしています。

* **Azure パブリック** クラウドは、グローバルに利用できます。

* **Azure China 21Vianet** は、Microsoft とその国/地域の最大のインターネット プロバイダーの 1 つである 21Vianet との間の独自のパートナーシップを通して利用できます。

* **Azure Germany** では、データ保護受託者モデルに沿ってサービスが提供され、お客様のデータは、ドイツ国内でデータ保護受託者の役目を務める T-Systems International GmbH (Deutsche Telekom の子会社) の管理下で、ドイツ国外に移転されないことが保証されます。

* **Azure Government** は、米国内の 4 つのリージョンで米国政府機関とそのパートナーが利用できます。 

* **米国国防総省 (DoD) 向け Azure Government** は、米国内の 2 つのリージョンで米国国防総省が利用できます。

規制の対象となる業界や世界中の市場におけるお客様各自の遵守義務を満たすことを支援するために、Azure では、その幅の広さ (認証の総数) と深さ (評価対象である顧客向けサービスの数) の両方に関して、業界最大のコンプライアンス ポートフォリオを保持しています。  Azure のコンプライアンス認証は、4 つのセグメント (グローバルに適用可能、米国政府、業界に固有、および国や地域に固有) にグループ化されています。  コンプライアンス認証は、独立したサードパーティの監査企業による正式な認証、証明、検証、承認、および評価の他に、Microsoft による契約の修正、自己評価、顧客向けのガイダンス ドキュメントを含むさまざまな種類の保証に基づいています。

## <a name="azure-cosmos-db-certifications"></a>Azure Cosmos DB の認証  

Azure Cosmos DB は、その認証範囲を拡大し続けています。 現時点では、Azure Cosmos DB は、以下の認証を受けています。

| **グローバルに適用可能** | **米国政府** | **業界に固有** | **地域/国に固有** |
| --- | --- | --- | --- |
| CSA STAR 認証 | DoD SRG Level 2 | HIPAA BAA | オーストラリアの IRAP |
| CSA STAR 証明 | FedRAMP Moderate | HITRUST | ドイツの C5|
| ISO 20000-1:2011 | GxP (FDA 21 CFR Part 11) | PCI DSS | シンガポールの MTCS Level 3 |
| ISO 22301:2012 | | | スペインの ENS High|
| ISO 27001:2013| | | |
| ISO 27017:2015 | | | |
| ISO 27018:2014 | | | |
| ISO 9001:2015 | | | |
| ISO 27701:2019| | | |
| SOC 1、2、3 | | | |

これらのコンプライアンス認証とそれらのメリットの詳細については、[Microsoft Azure のコンプライアンス認証の概要](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/)に関するページを参照してください。

次の表に、Azure Government 上で Azure Cosmos DB によってサポートされている認証を示します。

| **グローバルに適用可能** | **米国政府** | **業界に固有** | 
| --- | --- | --- |
| CSA STAR 認証 | CJIS| HIPAA BAA | 
| CSA STAR 証明| DoD SRG Level 2| HITRUST  | 
| ISO 20000-1:2011 | DoD SRG Level 4 | PCI DSS | 
| ISO 9001:2012 | DoD SRG Level 5 | | 
| ISO 27001:2013 | FedRAMP High | |
| ISO 9001:2015 | IRS 1075|  |
| ISO 27017:2014 | NIST CSF | |
| ISO 27018:2015 | NIST SP 800-171 | |
| SOC 1、2、3 | | |


## <a name="next-steps"></a>次のステップ

Azure のコンプライアンス認証の詳細については、以下の記事を参照してください。

* Azure Cosmos DB の最新のコンプライアンス認証を確認するには、[Microsoft Azure のコンプライアンス認証の概要](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/)に関するページの付録 A と B を参照してください。  

* Azure Cosmos DB のセキュリティと最新の機能強化の概要については、[Azure Cosmos データベースのセキュリティ](database-security.md)に関する記事を参照してください。

* Azure Cosmos DB のデプロイのセキュリティ対策を向上させるための推奨事項については、[Azure Cosmos DB セキュリティ ベースライン](security-baseline.md)に関する記事を参照してください。 

* マイクロソフトが取得している認証の詳細については、[Azure トラスト センター](https://azure.microsoft.com/support/trust-center/)に関するページを参照してください。

* FedRAMP コンプライアンスの情報については、「[FedRAMP および DoD CC SRG 監査スコープ別の Azure サービス](../azure-government/compliance/azure-services-in-fedramp-auditscope.md)」を参照してください。

* DoD コンプライアンスの情報については、[DoD コンプライアンス認証](/microsoft-365/compliance/offering-dod-disa-l2-l4-l5)に関する記事を参照してください。
