---
title: HSM で保護されたキーを生成し、転送する方法 – Azure Key Vault
description: Azure Key Vault と共に使用する、HSM で保護された独自のキーを計画、生成、転送する方法について説明します。 これは、BYOK (Bring Your Own Key) とも呼ばれます。
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 02/24/2021
ms.author: ambapat
ms.openlocfilehash: a7e709ba9a4de5ff77524a2d2b1b64a5933131a2
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "102489414"
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
|[nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|製造元、<br/>サービスとしての HSM|<ul><li>HSM の nShield ファミリ</li><li>サービスとしての nShield</ul>|**方法 1:** [nCipher BYOK](hsm-protected-keys-ncipher.md) (非推奨)<br/>**方法 2:** [新しい BYOK の方法を使用する](hsm-protected-keys-byok.md) (推奨)|
|Thales|Manufacturer|<ul><li>ファームウェア バージョンが 7.3 以降の Luna HSM 7 ファミリ</li></ul>| [新しい BYOK の方法を使用する](hsm-protected-keys-byok.md)|
|Fortanix|製造元、<br/>サービスとしての HSM|<ul><li>Self-Defending Key Management Service (SDKMS)</li><li>Equinix SmartKey</li></ul>|[新しい BYOK の方法を使用する](hsm-protected-keys-byok.md)|
|Marvell|Manufacturer|以下を含む LiquidSecurity のすべての HSM<ul><li>ファームウェア バージョン 2.0.4 以降</li><li>ファームウェア バージョン 3.2 以降</li></ul>|[新しい BYOK の方法を使用する](hsm-protected-keys-byok.md)|
|Cryptomathic|ISV (エンタープライズ キー管理システム)|以下を含む複数の HSM ブランドおよびモデル<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>詳細については、[Cryptomathic のサイト](https://www.cryptomathic.com/azurebyok)を参照してください|[新しい BYOK の方法を使用する](hsm-protected-keys-byok.md)|
|Securosys SA|製造元、<br/>サービスとしての HSM|Primus HSM ファミリ、Securosys Clouds HSM|[新しい BYOK の方法を使用する](hsm-protected-keys-byok.md)|
|StorMagic|ISV (エンタープライズ キー管理システム)|以下を含む複数の HSM ブランドおよびモデル<ul><li>Utimaco</li><li>Thales</li><li>nCipher</li></ul>[詳細については、StorMagic のサイト](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)を参照してください|[新しい BYOK の方法を使用する](hsm-protected-keys-byok.md)|
|IBM|製造元|IBM 476x、CryptoExpress|[新しい BYOK の方法を使用する](hsm-protected-keys-byok.md)|
|Utimaco|製造元、<br/>サービスとしての HSM|u.trust Anchor、CryptoServer|[新しい BYOK の方法を使用する](hsm-protected-keys-byok.md)|
|||||

## <a name="next-steps"></a>次のステップ

* [Key Vault のセキュリティの概要](../general/security-overview.md)を読んで、キーのセキュリティ、持続性、監視を徹底します。
* 新しい BYOK の方法の詳細については、[BYOK の仕様](./byok-specification.md)に関するページを参照してください