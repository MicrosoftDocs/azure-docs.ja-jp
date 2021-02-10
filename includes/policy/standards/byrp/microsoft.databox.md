---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/29/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 3bb3dfcf77f0bd22af0a62a07a0e72846f1eb91f
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2021
ms.locfileid: "99179451"
---
## <a name="cmmc-level-3"></a>CMMC レベル 3

すべての Azure サービスで使用可能な Azure Policy 組み込みがこのコンプライアンス標準にどのように対応するのかを確認するには、[Azure Policy の規制コンプライアンス - CMMC レベル 3](../../../../articles/governance/policy/samples/cmmc-l3.md) に関する記事をご覧ください。
このコンプライアンス標準の詳細については、[サイバーセキュリティ成熟度モデル認定 (CMMC)](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf) に関するドキュメントをご覧ください。

|Domain |コントロール ID |コントロールのタイトル |ポリシー<br /><sub>(Azure portal)</sub> |ポリシーのバージョン<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|システムと通信の保護 |SC.3.177 |CUI の機密性を保護するために使用する場合は、FIPS 検証済みの暗号化を採用する。 |[Azure Data Box ジョブは、デバイス上の保存データに対する二重暗号化を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc349d81b-9985-44ae-a8da-ff98d108ede8) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_DoubleEncryption_Audit.json) |
|システムと通信の保護 |SC.3.191 |保存時の CUI の機密性を保護する。 |[Azure Data Box ジョブは、デバイス上の保存データに対する二重暗号化を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc349d81b-9985-44ae-a8da-ff98d108ede8) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_DoubleEncryption_Audit.json) |

