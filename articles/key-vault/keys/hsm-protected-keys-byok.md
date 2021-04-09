---
title: HSM 保護キーを生成し、転送する方法 – BYOK – Azure Key Vault
description: この記事は、Azure Key Vault と共に使用する独自の HSM で保護されたキーを計画、生成、転送する場合に役立ちます。 これは、BYOK (Bring Your Own Key) とも呼ばれます。
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: ambapat
ms.openlocfilehash: 4a3eaddd160acb8d4d2ae9f0da43ce6cb0236055
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "102198151"
---
# <a name="import-hsm-protected-keys-to-key-vault-byok"></a>HSM で保護されたキーを Key Vault にインポートする (BYOK)

Azure Key Vault を使用するときの安心感をさらに高めるために、ハードウェア セキュリティ モジュール (HSM) でキーをインポートしたり、生成したりできます。キーが HSM の境界内から出ることはありません。 多くの場合、このシナリオは *Bring Your Own Key* (BYOK) と呼ばれます。 Azure Key Vault では、HSM の nCipher nShield ファミリ (FIPS 140-2 レベル 2 で検証済み) を使用してキーが保護されます。

この記事の情報は、Azure Key Vault と共に使用する独自の HSM で保護されたキーを計画、生成、転送する場合に役立ちます。

> [!NOTE]
> この機能は Azure China 21Vianet では使用できません。 
> 
> このインポート方法は、[サポートされている HSM](#supported-hsms) でのみ使用できます。 

Azure Key Vault の詳細と、Key Vault の使用を開始するチュートリアル (HSM で保護されたキーのキー コンテナーの作成方法を含む) については、[「Azure Key Vault とは」](../general/overview.md)をご覧ください。

## <a name="overview"></a>概要

次にプロセスの概要を示します。 具体的な手順については、この記事の後半で説明します。

* キー コンテナーで、キー (*キー交換キー* (KEK) と呼ばれる) を生成します。 KEK は、キー操作が `import` のみの RSA-HSM キーである必要があります。 RSA-HSM キーは、Key Vault Premium SKU でのみサポートされます。
* KEK 公開キーを .pem ファイルとしてダウンロードします。
* オンプレミスの HSM に接続されているオフライン コンピューターに KEK 公開キーを転送します。
* オフライン コンピューターで、HSM ベンダーから提供されている BYOK ツールを使用して BYOK ファイルを作成します。 
* ターゲット キーは KEK で暗号化され、Key Vault HSM に転送されるまで暗号化が維持されます。 暗号化されたキーだけが、オンプレミスの HSM から外に出て行きます。
* Key Vault HSM 内で生成された KEK のエクスポートはできません。 HSM によって、Key Vault HSM の外部に平文の KEK は存在しないというルールが適用されます。
* KEK は、ターゲット キーがインポートされるのと同じキー コンテナー内に存在する必要があります。
* BYOK ファイルが Key Vault にアップロードされると、Key Vault HSM により、KEK 秘密キーを使用してターゲット キー マテリアルが解読され、HSM キーとしてインポートされます。 この操作は、完全に Key Vault HSM 内で行われます。 ターゲット キーは常に、HSM 保護境界内に留まります。

## <a name="prerequisites"></a>前提条件

次の表に、Azure Key Vault で BYOK を使用するための前提条件を示します。

| 要件 | 詳細情報 |
| --- | --- |
| Azure サブスクリプション |Azure Key Vault でキー コンテナーを作成するには、Azure サブスクリプションが必要です。 [無料試用版にサインアップ](https://azure.microsoft.com/pricing/free-trial/)してください。 |
| HSM で保護されたキーをインポートするための Key Vault Premium SKU |Azure Key Vault のサービス レベルと機能の詳細については、「[Key Vault 価格](https://azure.microsoft.com/pricing/details/key-vault/)」をご覧ください。 |
| サポートされている HSM の一覧に含まれる HSM と、HSM ベンダーから提供されている BYOK ツールおよび説明 | HSM のアクセス許可と HSM の使用方法に関する基本的な知識が必要です。 「[サポートされている HSM](#supported-hsms)」を参照してください。 |
| Azure CLI バージョン 2.1.0 以降 | 「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。|

## <a name="supported-hsms"></a>サポートされている HSM

|ベンダー名|ベンダーの種類|サポートされている HSM モデル|詳細情報|
|---|---|---|---|
|nCipher|製造元、<br/>サービスとしての HSM|<ul><li>HSM の nShield ファミリ</li><li>サービスとしての nShield</ul>|[nCipher の新しい BYOK ツールとドキュメント](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|
|Thales|Manufacturer|<ul><li>ファームウェア バージョンが 7.3 以降の Luna HSM 7 ファミリ</li></ul>| [Luna の BYOK ツールとドキュメント](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanix|製造元、<br/>サービスとしての HSM|<ul><li>Self-Defending Key Management Service (SDKMS)</li><li>Equinix SmartKey</li></ul>|[SDKMS キーを BYOK 用クラウド プロバイダーにエクスポートする - Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|
|Marvell|Manufacturer|以下を含む LiquidSecurity のすべての HSM<ul><li>ファームウェア バージョン 2.0.4 以降</li><li>ファームウェア バージョン 3.2 以降</li></ul>|[Marvell の BYOK ツールとドキュメント](https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/exporting-marvell-hsm-keys-to-cloud-azure-key-vault.html)|
|Cryptomathic|ISV (エンタープライズ キー管理システム)|以下を含む複数の HSM ブランドおよびモデル<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>詳細については、[Cryptomathic のサイト](https://www.cryptomathic.com/azurebyok)を参照してください|[Cryptomathic の BYOK ツールとドキュメント](https://www.cryptomathic.com/azurebyok)|
|Securosys SA|製造元、<br/>サービスとしての HSM|Primus HSM ファミリ、Securosys Clouds HSM|[Primus の BYOK ツールとドキュメント](https://www.securosys.com/primus-azure-byok)|
|StorMagic|ISV (エンタープライズ キー管理システム)|以下を含む複数の HSM ブランドおよびモデル<ul><li>Utimaco</li><li>Thales</li><li>nCipher</li></ul>[詳細については、StorMagic のサイト](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)を参照してください|[SvKMS と Azure Key Vault の BYOK](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|
|IBM|製造元|IBM 476x、CryptoExpress|[IBM Enterprise Key Management Foundation](https://www.ibm.com/security/key-management/ekmf-bring-your-own-key-azure)|
|Utimaco|製造元、<br/>サービスとしての HSM|u.trust Anchor、CryptoServer|[Utimaco BYOK ツールと統合ガイド](https://support.hsm.utimaco.com/support/downloads/byok)|
||||


## <a name="supported-key-types"></a>サポートされているキーの種類

|キー名|キーの種類|キー サイズまたは曲線|出発地|説明|
|---|---|---|---|---|
|キー交換キー (KEK)|RSA| 2,048 ビット<br />3,072 ビット<br />4,096 ビット|Azure Key Vault HSM|Azure Key Vault で生成される、HSM で保護された RSA キー ペア|
|ターゲット キー|
||RSA|2,048 ビット<br />3,072 ビット<br />4,096 ビット|ベンダー HSM|Azure Key Vault HSM に転送されるキー|
||EC|P-256<br />P-384<br />P-521|ベンダー HSM|Azure Key Vault HSM に転送されるキー|
||||

## <a name="generate-and-transfer-your-key-to-the-key-vault-hsm"></a>キーを生成して Key Vault HSM に転送する

キーを生成して Key Vault HSM に転送するには、次の操作を行います。

* [ステップ 1:KEK を生成する](#step-1-generate-a-kek)
* [手順 2:KEK 公開キーをダウンロードする](#step-2-download-the-kek-public-key)
* [ステップ 3:キーを生成して転送用に準備をする](#step-3-generate-and-prepare-your-key-for-transfer)
* [手順 4:キーを Azure Key Vault に転送する](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>手順 1:KEK を生成する

KEK は、Key Vault HSM で生成される RSA キーです。 KEK は、インポートするキー (*ターゲット* キー) を暗号化するために使用されます。

KEK には次の条件があります。
- RSA-HSM キー (2,048 ビット、3,072 ビット、4,096 ビット) であること
- ターゲット キーをインポートするのと同じキー コンテナーで生成されていること
- `import` に設定されている、許可されたキー操作で作成されていること

> [!NOTE]
> KEK には、許可されている唯一のキー操作として「インポート」が必要です。 「インポート」は、他のすべてのキー操作と同時には使用できません。

キー操作が `import` に設定されている KEK を作成するには、[az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create) コマンドを使用します。 次のコマンドから返されるキー識別子 (`kid`) を記録しておきます。 (この `kid` 値は[手順 3](#step-3-generate-and-prepare-your-key-for-transfer) で使用します。)

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-the-kek-public-key"></a>手順 2:KEK 公開キーをダウンロードする

KEK 公開キーを .pem ファイルにダウンロードするには、[az keyvault key download](/cli/azure/keyvault/key#az-keyvault-key-download) を使用します。 インポートするターゲット キーを暗号化するには、KEK 公開キーを使用します。

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

KEKforBYOK.publickey.pem ファイルをオフライン コンピューターに転送します。 このファイルは、次の手順で必要になります。

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>手順 3:キーを生成して転送用に準備をする

BYOK ツールをダウンロードしてインストールする方法については、HSM ベンダーのドキュメントをご覧ください。 HSM ベンダーの指示に従って、ターゲット キーを生成した後、キー転送パッケージ (BYOK ファイル) を作成します。 BYOK ツールでは、[手順 1](#step-1-generate-a-kek) の `kid` と、[手順 2](#step-2-download-the-kek-public-key) でダウンロードした KEKforBYOK.publickey.pem ファイルを使用して、暗号化されたターゲット キーを BYOK ファイルに生成します。

接続されているコンピューターに BYOK ファイルを転送します。

> [!NOTE] 
> 1,024 ビットの RSA キーのインポートはサポートされていません。 楕円曲線キー (P-256K 曲線) のインポートはサポートされていません。
> 
> **既知の問題**:Luna HSM からの RSA 4K ターゲット キーのインポートは、ファームウェア 7.4.0 以降でのみサポートされています。

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>手順 4:キーを Azure Key Vault に転送する

キーのインポートを完了するには、切断されたコンピューターからインターネットに接続されているコンピューターにキー転送パッケージ (BYOK ファイル) を転送します。 [az keyvault key import](/cli/azure/keyvault/key#az-keyvault-key-import) コマンドを使用して、BYOK ファイルを Key Vault HSM にアップロードします。

RSA キーをインポートするには、次のコマンドを使用します。 パラメーター - kty は省略可能で、既定値は "RSA-HSM" です。
```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

EC キーをインポートするには、キーの種類と曲線名を指定する必要があります。

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file --kty EC-HSM --curve-name "P-256" KeyTransferPackage-ContosoFirstHSMkey.byok
```

アップロードが正常に行われると、インポートされたキーのプロパティが Azure CLI に表示されます。

## <a name="next-steps"></a>次のステップ

これでこの HSM 保護キーを Key Vault で使用できます。 詳細については、[この価格と機能の比較](https://azure.microsoft.com/pricing/details/key-vault/)に関するページをご覧ください。



