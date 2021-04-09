---
title: Azure Key Vault Managed HSM の HSM 保護キーを生成し、転送する方法 - Azure Key Vault | Microsoft Docs
description: この記事は、Managed HSM と共に使用する独自の HSM で保護されたキーを計画、生成、転送する場合に役立ちます。 これは、BYOK (Bring Your Own Key) とも呼ばれます。
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: ambapat
ms.openlocfilehash: dd5b38a858ceba12f5d48f1782da5b85228c4b06
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102212112"
---
# <a name="import-hsm-protected-keys-to-managed-hsm-byok"></a>HSM で保護されたキーを Managed HSM にインポートする (BYOK)

 Azure Key Vault Managed HSM は、オンプレミスのハードウェア セキュリティ モジュール (HSM) で生成されたキーのインポートをサポートしています。このキーが HSM による保護の境界の外に出ることは決してありません。 多くの場合、このシナリオは *Bring Your Own Key* (BYOK) と呼ばれます。 Managed HSM によるキーの保護は、Marvell LiquidSecurity HSM アダプター (FIPS 140-2 レベル 3 で検証済み) を使用して行われます。

この記事の情報は、Managed HSM と共に使用する独自の HSM で保護されたキーを計画、生成、転送する場合に役立ちます。

> [!NOTE]
> この機能は Azure China 21Vianet では使用できません。 このインポート方法は、[サポートされている HSM](#supported-hsms) でのみ使用できます。 

詳細および Managed HSM の使用を開始するためのチュートリアルについては、「[Managed HSM とは](overview.md)」を参照してください。

## <a name="overview"></a>概要

次にプロセスの概要を示します。 具体的な手順については、この記事の後半で説明します。

* Managed HSM で、キー (*キー交換キー* (KEK) と呼ばれる) を生成します。 KEK は、キー操作が `import` のみの RSA-HSM キーである必要があります。 
* KEK 公開キーを .pem ファイルとしてダウンロードします。
* オンプレミスの HSM に接続されているオフライン コンピューターに KEK 公開キーを転送します。
* オフライン コンピューターで、HSM ベンダーから提供されている BYOK ツールを使用して BYOK ファイルを作成します。 
* ターゲット キーは KEK で暗号化され、Managed HSM に転送されるまで暗号化が維持されます。 暗号化されたキーだけが、オンプレミスの HSM から外に出て行きます。
* Managed HSM 内で生成された KEK のエクスポートはできません。 HSM によって、Managed HSM の外部に平文の KEK は存在しないというルールが適用されます。
* KEK は、ターゲット キーがインポートされるのと同じマネージド HSM 内に存在する必要があります。
* BYOK ファイルが Managed HSM にアップロードされると、Managed HSM により、KEK 秘密キーを使用してターゲット キー マテリアルが解読され、HSM キーとしてインポートされます。 この操作は、完全に HSM 内で行われます。 ターゲット キーは常に、HSM 保護境界内に留まります。


## <a name="prerequisites"></a>前提条件

この記事にある Azure CLI コマンドを使用するには、以下を用意する必要があります。

* Microsoft Azure サブスクリプション。 サブスクリプションがない場合でも、[無料試用版](https://azure.microsoft.com/pricing/free-trial)にサインアップできます。
* Azure CLI バージョン 2.12.0 以降。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。
* サブスクリプションで[サポートされている HSM の一覧](#supported-hsms)のマネージド HSM。 「[クイック スタート:Azure CLI を使用してマネージド HSM をプロビジョニングしてアクティブにする](quick-create-cli.md)」を参照して、マネージド HSM をプロビジョニングしてアクティブにします。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI を使用して Azure にサインインするには、次のように入力します。

```azurecli
az login
```

CLI を使用したログイン オプションの詳細については、「[Azure CLI を使用してサインインする](/cli/azure/authenticate-azure-cli)」を参照してください

## <a name="supported-hsms"></a>サポートされている HSM

|ベンダー名|ベンダーの種類|サポートされている HSM モデル|詳細情報|
|---|---|---|---|
|nCipher|製造元、<br/>サービスとしての HSM|<ul><li>HSM の nShield ファミリ</li><li>サービスとしての nShield</ul>|[nCipher の新しい BYOK ツールとドキュメント](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|
|Thales|Manufacturer|<ul><li>ファームウェア バージョンが 7.3 以降の Luna HSM 7 ファミリ</li></ul>| [Luna の BYOK ツールとドキュメント](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanix|製造元、<br/>サービスとしての HSM|<ul><li>Self-Defending Key Management Service (SDKMS)</li><li>Equinix SmartKey</li></ul>|[SDKMS キーを BYOK 用クラウド プロバイダーにエクスポートする - Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|
|Marvell|Manufacturer|以下を含む LiquidSecurity のすべての HSM<ul><li>ファームウェア バージョン 2.0.4 以降</li><li>ファームウェア バージョン 3.2 以降</li></ul>|[Marvell の BYOK ツールとドキュメント](https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/exporting-marvell-hsm-keys-to-cloud-azure-key-vault.html)|
|Cryptomathic|ISV (エンタープライズ キー管理システム)|以下を含む複数の HSM ブランドおよびモデル<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>詳細については、[Cryptomathic のサイト](https://www.cryptomathic.com/azurebyok)を参照してください|[Cryptomathic の BYOK ツールとドキュメント](https://www.cryptomathic.com/azurebyok)|
|Securosys SA|製造元、サービスとしての HSM|Primus HSM ファミリ、Securosys Clouds HSM|[Primus の BYOK ツールとドキュメント](https://www.securosys.com/primus-azure-byok)|
|StorMagic|ISV (エンタープライズ キー管理システム)|以下を含む複数の HSM ブランドおよびモデル<ul><li>Utimaco</li><li>Thales</li><li>nCipher</li></ul>[詳細については、StorMagic のサイト](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)を参照してください|[SvKMS と Azure Key Vault の BYOK](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|
|IBM|製造元|IBM 476x、CryptoExpress|[IBM Enterprise Key Management Foundation](https://www.ibm.com/security/key-management/ekmf-bring-your-own-key-azure)|
|Utimaco|製造元、<br/>サービスとしての HSM|u.trust Anchor、CryptoServer|[Utimaco BYOK ツールと統合ガイド](https://support.hsm.utimaco.com/support/downloads/byok)|
||||


## <a name="supported-key-types"></a>サポートされているキーの種類

|キー名|キーの種類|キー サイズまたは曲線|出発地|説明|
|---|---|---|---|---|
|キー交換キー (KEK)|RSA| 2,048 ビット<br />3,072 ビット<br />4,096 ビット|Managed HSM|Managed HSM で生成される、HSM で保護された RSA キー ペア|
|ターゲット キー|
||RSA|2,048 ビット<br />3,072 ビット<br />4,096 ビット|ベンダー HSM|Managed HSM に転送されるキー|
||EC|P-256<br />P-384<br />P-521|ベンダー HSM|Managed HSM に転送されるキー|
||対称キー (oct-HSM)|128 ビット<br />192 ビット<br />256 ビット|ベンダー HSM|Managed HSM に転送されるキー|
||||
## <a name="generate-and-transfer-your-key-to-the-managed-hsm"></a>キーを生成して Managed HSM に転送する

キーを生成して Managed HSM に転送するには、次の操作を行います。

* [ステップ 1:KEK を生成する](#step-1-generate-a-kek)
* [手順 2:KEK 公開キーをダウンロードする](#step-2-download-the-kek-public-key)
* [ステップ 3:キーを生成して転送用に準備をする](#step-3-generate-and-prepare-your-key-for-transfer)
* [手順 4:キーを Managed HSM に転送する](#step-4-transfer-your-key-to-managed-hsm)

### <a name="step-1-generate-a-kek"></a>手順 1:KEK を生成する

KEK は、Managed HSM で生成される RSA キーです。 KEK は、インポートするキー (*ターゲット* キー) を暗号化するために使用されます。

KEK には次の条件があります。
- RSA-HSM キー (2,048 ビット、3,072 ビット、4,096 ビット) であること
- ターゲット キーをインポートするのと同じマネージド HSM で生成されていること
- `import` に設定されている、許可されたキー操作で作成されていること

> [!NOTE]
> KEK には、許可されている唯一のキー操作として「インポート」が必要です。 「インポート」は、他のすべてのキー操作と同時には使用できません。

キー操作が `import` に設定されている KEK を作成するには、[az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create) コマンドを使用します。 次のコマンドから返されるキー識別子 (`kid`) を記録しておきます。 (この `kid` 値は[手順 3](#step-3-generate-and-prepare-your-key-for-transfer) で使用します。)

```azurecli-interactive
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --hsm-name ContosoKeyVaultHSM
```
---


### <a name="step-2-download-the-kek-public-key"></a>手順 2:KEK 公開キーをダウンロードする

KEK 公開キーを .pem ファイルにダウンロードするには、[az keyvault key download](/cli/azure/keyvault/key#az-keyvault-key-download) を使用します。 インポートするターゲット キーを暗号化するには、KEK 公開キーを使用します。

```azurecli-interactive
az keyvault key download --name KEKforBYOK --hsm-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```
---

KEKforBYOK.publickey.pem ファイルをオフライン コンピューターに転送します。 このファイルは、次の手順で必要になります。

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>手順 3:キーを生成して転送用に準備をする

BYOK ツールをダウンロードしてインストールする方法については、HSM ベンダーのドキュメントをご覧ください。 HSM ベンダーの指示に従って、ターゲット キーを生成した後、キー転送パッケージ (BYOK ファイル) を作成します。 BYOK ツールでは、[手順 1](#step-1-generate-a-kek) の `kid` と、[手順 2](#step-2-download-the-kek-public-key) でダウンロードした KEKforBYOK.publickey.pem ファイルを使用して、暗号化されたターゲット キーを BYOK ファイルに生成します。

接続されているコンピューターに BYOK ファイルを転送します。

> [!NOTE] 
> 1,024 ビットの RSA キーのインポートはサポートされていません。 現時点では、楕円曲線 (EC) キーのインポートはサポートされていません。
>
> **既知の問題**:Luna HSM からの RSA 4K ターゲット キーのインポートは、ファームウェア 7.4.0 以降でのみサポートされています。

### <a name="step-4-transfer-your-key-to-managed-hsm"></a>手順 4:キーを Managed HSM に転送する

キーのインポートを完了するには、切断されたコンピューターからインターネットに接続されているコンピューターにキー転送パッケージ (BYOK ファイル) を転送します。 [az keyvault key import](/cli/azure/keyvault/key#az-keyvault-key-import) コマンドを使用して、BYOK ファイルを Managed HSM にアップロードします。

```azurecli-interactive
az keyvault key import --hsm-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

アップロードが正常に行われると、インポートされたキーのプロパティが Azure CLI に表示されます。

## <a name="next-steps"></a>次のステップ

これでこの HSM 保護キーを Managed HSM で使用できます。 詳細については、[この価格と機能の比較](https://azure.microsoft.com/pricing/details/key-vault/)に関するページをご覧ください。



