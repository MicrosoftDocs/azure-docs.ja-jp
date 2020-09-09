---
title: HSM で保護されたキーを生成し、転送する方法 – Azure Key Vault
description: この記事は Azure Key Vault と共に使用する独自の HSM 保護キーを計画、生成、転送する際に役立ちます。 これは、BYOK (Bring Your Own Key) とも呼ばれます。
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 05/29/2020
ms.author: ambapat
ms.openlocfilehash: 76b10dbd9b6d801d93cd5d9704531eb1a6de36a5
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585494"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>HSM で保護されたキーを Key Vault にインポートする

Azure Key Vault の使用時にさらに安心感を高める場合、ハードウェア セキュリティ モジュール (HSM) でキーをインポートしたり、生成したりできます。キーは HSM の境界内から出ることはありません。 このシナリオは、多くの場合、*Bring Your Own Key* または BYOK と呼ばれています。 Azure Key Vault では、HSM の nCipher nShield ファミリ (FIPS 140-2 レベル 2 で検証済み) を使用してキーが保護されます。

この機能は Azure China 21Vianet では使用できません。

> [!NOTE]
> Azure Key Vault の詳細については、「 [What is Azure Key Vault? (Azure Key Vault とは)](../general/overview.md)  
> HSM で保護されたキーの Key Vault 作成を含む入門チュートリアルについては、「[Azure Key Vault とは](../general/overview.md)」を参照してください。

## <a name="supported-hsms"></a>サポートされている HSM

HSM で保護されたキーの Key Vault への転送は、使用する HSM に応じて 2 つの異なる方法でサポートされます。 以下の表を使用して、HSM で、独自の HSM で保護されたキーを生成してから、Azure Key Vault で使用するためにそれらを転送する場合に使用する必要がある方法を判別します。 

|ベンダー名|ベンダーの種類|サポートされている HSM モデル|サポートされている HSM キーの転送方法|
|---|---|---|---|
|[nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|製造元、<br/>サービスとしての HSM|<ul><li>HSM の nShield ファミリ</li><li>サービスとしての nShield</ul>|**方法 1:** [nCipher BYOK](hsm-protected-keys-ncipher.md) (キー インポートおよび HSM 検証用の強力な構成証明を使用)<br/>**方法 2:** [新しい BYOK の方法を使用する](hsm-protected-keys-byok.md) |
|Thales|Manufacturer|<ul><li>ファームウェア バージョンが 7.3 以降の Luna HSM 7 ファミリ</li></ul>| [新しい BYOK の方法を使用する](hsm-protected-keys-byok.md)|
|Fortanix|製造元、<br/>サービスとしての HSM|<ul><li>Self-Defending Key Management Service (SDKMS)</li><li>Equinix SmartKey</li></ul>|[新しい BYOK の方法を使用する](hsm-protected-keys-byok.md)|
|Marvell|Manufacturer|以下を含む LiquidSecurity のすべての HSM<ul><li>ファームウェア バージョン 2.0.4 以降</li><li>ファームウェア バージョン 3.2 以降</li></ul>|[新しい BYOK の方法を使用する](hsm-protected-keys-byok.md)|
|Cryptomathic|ISV (エンタープライズ キー管理システム)|以下を含む複数の HSM ブランドおよびモデル<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>詳細については、[Cryptomathic のサイト](https://www.cryptomathic.com/azurebyok)を参照してください|[新しい BYOK の方法を使用する](hsm-protected-keys-byok.md)|


## <a name="next-steps"></a>次のステップ

* [Key Vault のベスト プラクティス](../general/best-practices.md)に従って、キーのセキュリティ、持続性および監視を確実にします。
* 新しい BYOK の方法の詳細については、[BYOK の仕様](https://docs.microsoft.com/azure/key-vault/keys/byok-specification)に関するページを参照してください
