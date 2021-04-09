---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/10/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 194b4ad507a48f1010dc884326c16b95d6513081
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102608660"
---
|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Data Box ジョブは、デバイス上の保存データに対する二重暗号化を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc349d81b-9985-44ae-a8da-ff98d108ede8) |デバイス上の保存データに対し、ソフトウェアベースの暗号化の第 2 のレイヤーを有効にしてください。 デバイスの保存データはあらかじめ、Advanced Encryption Standard 256 ビット暗号化で保護されています。 このオプションは、第 2 のデータ暗号化レイヤーを追加するものです。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_DoubleEncryption_Audit.json) |
|[Azure Data Box ジョブは、カスタマー マネージド キーを使用してデバイスのロック解除パスワードを暗号化する必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86efb160-8de7-451d-bc08-5d475b0aadae) |Azure Data Box に使用するデバイスのロック解除パスワードの暗号化は、カスタマー マネージド キーを使用して管理してください。 また、デバイスの準備とデータのコピーを自動化する目的で、デバイスのロック解除パスワードに対する Data Box サービスのアクセスを管理する際にも、カスタマー マネージド キーが役立ちます。 デバイス上の保存データ自体は、あらかじめ Advanced Encryption Standard 256 ビット暗号化を使用して暗号化されており、また、デバイスのロック解除パスワードも既定で Microsoft マネージド キーを使用して暗号化されています。 |Audit、Deny、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_CMK_Audit.json) |
