---
title: Bring Your Own Key の仕様 - Azure Key Vault | Microsoft Docs
description: このドキュメントでは、Bring Your Own Key の仕様について説明します。
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: ambapat
ms.openlocfilehash: 141abea0c0946c98b6dfe627f32f01682a18be44
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99581025"
---
# <a name="bring-your-own-key-specification"></a>Bring Your Own Key の仕様

このドキュメントでは、HSM で保護されたキーを顧客のオンプレミスの HSM から Key Vault にインポートするための仕様について説明します。

## <a name="scenario"></a>シナリオ

Key Vault の顧客は、Azure の外部にあるオンプレミスの HSM から HSM で保護された Azure キー コンテナーにキーを安全に転送することができます。 Key Vault の外部で生成されたキーをインポートするプロセスは、一般に、Bring Your Own Key (BYOK) と呼ばれます。

次の要件があります。
* 転送されるキーが HSM の外部でプレーン テキスト形式で存在することは絶対にない。
* HSM の外部では、転送されるキーは、常に Azure Key Vault HSM に保持されているキーによって保護される。

## <a name="terminology"></a>用語

|キーの名前|キーの種類|Origin (配信元)|説明|
|---|---|---|---|
|キー交換キー (KEK)|RSA|Azure Key Vault HSM|Azure Key Vault で生成される、HSM で保護された RSA キー ペア
ラッピング キー|AES|ベンダー HSM|オンプレミスの HSM によって生成される [一時的な] AES キー
ターゲット キー|RSA、EC、AES (Managed HSM のみ)|ベンダー HSM|Azure Key Vault HSM に転送されるキー

**キー交換キー**:BYOK キーのインポート先であるキー コンテナー内に顧客が生成する HSM で保護されたキー。 この KEK には、次のプロパティが必要です。

* RSA-HSM キーであること (4096 ビット、3072 ビット、または 2048 ビット)
* 固定された key_ops ("import" のみ) があること。これにより、BYOK でのみ使用可能になります
* ターゲット キーのインポート先と同じキー コンテナー内に存在する必要があること

## <a name="user-steps"></a>ユーザーの手順

ユーザーは、キーの転送を実行するために次の手順を実行します。

1. KEK を生成します。
2. KEK の公開キーを取得します。
3. HSM ベンダーから提供される BYOK ツールの使用 - KEK をターゲット HSM にインポートし、KEK によって保護されたターゲット キーをエクスポートします。
4. 保護されたターゲット キーを Azure Key Vault にインポートします。

顧客は、HSM ベンダーから提供された BYOK ツールとドキュメントを使用して手順 3 を完了します。 キー転送 BLOB (".byok" ファイル) が生成されます。


## <a name="hsm-constraints"></a>HSM の制約

既存の HSM では、管理するキーに次のような制約が適用される場合があります。
* キーのラップベースのエクスポートを許可するように HSM を構成しなければならない場合がある。
* HSM で制御されたエクスポートを可能にするために、ターゲット キーを CKA_EXTRACTABLE としてマークしなければならない場合がある。
* 場合によっては、KEK とラッピング キーを CKA_TRUSTED としてマークしなければならない。 これにより、HSM でキーをラップするために使用できます。

ソース HSM の構成は、通常、この仕様の範囲外です。 Microsoft では、このような構成手順を含む BYOK ツールに付随するドキュメントを HSM ベンダーが作成することを想定しています。

> [!NOTE]
> 次に示す手順 1、2、および 4 は、Azure PowerShell や Azure Portal などの他のインターフェイスを使用して実行できます。 Key Vault SDK の同等の関数を使用してプログラムで実行することもできます。

### <a name="step-1-generate-kek"></a>手順 1:KEK を生成する

インポートのためのキー操作セットで KEK を作成するには、**az keyvault key create** コマンドを使用します。 次のコマンドから返されるキー識別子 "kid" を記録しておきます。

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-retrieve-the-public-key-of-the-kek"></a>手順 2:KEK の公開キーを取得する

KEK の公開キー部分をダウンロードして PEM ファイルに格納します。

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

### <a name="steps-3-generate-key-transfer-blob-using-hsm-vendor-provided-byok-tool"></a>手順 3:HSM ベンダーから提供される BYOK ツールを使用してキー転送 BLOB を生成する

顧客は、HSM ベンダーから提供される BYOK ツールを使用して、キー転送 BLOB を作成します (".byok" ファイルとして格納されます)。 (.pem ファイルとしての) KEK 公開キーは、このツールへの入力の 1 つになります。

#### <a name="key-transfer-blob"></a>キー転送 BLOB
長期的には、Microsoft は、PKCS#11 CKM_RSA_AES_KEY_WRAP メカニズムを使用してターゲット キーを Azure Key Vault に転送します。これは、このメカニズムでは単一の BLOB が生成されるためですが、さらに重要なのは、中間 AES キーが 2 台の HSM によって処理され、一時的であることが保証されるためです。 このメカニズムは、一部の HSM では現在使用できませんが、AES キーを使用する CKM_AES_KEY_WRAP_PAD によるターゲット キーの保護と、CKM_RSA_PKCS_OAEP による AES キーの保護を組み合わせることで、同等の BLOB が生成されます。

ターゲット キーのプレーンテキストは、キーの種類によって異なります。 
* RSA キーでは、PKCS#8 [RFC5208] でラップされた秘密キー ASN.1 DER エンコード [RFC3447] 
* EC キーでは、PKCS#8 [RFC5208] でラップされた秘密キー ASN.1 DER エンコード [RFC5915]
* オクテット キーでは、キーの未処理バイト

次に、プレーンテキスト キーのバイトが CKM_RSA_AES_KEY_WRAP メカニズムを使用して変換されます。
* 一時 AES キーが生成され、RSA-OAEP と SHA1 を使用して、ラッピング RSA キーで暗号化されます。
* エンコードされたプレーンテキスト キーが、AES キーのラップとパディングを使用する AES キーを使用して暗号化されます。
* 暗号化された AES キーと暗号化されたプレーンテキスト キーが連結されて、最終的な暗号化テキスト BLOB が生成されます。

転送 BLOB の形式では、適切な暗号化解除のために必要なメタデータをサービスに配信する手段として、主に JSON Web Encryption コンパクト シリアル化 (RFC7516) が使用されます。

CKM_RSA_AES_KEY_WRAP_PAD が使用される場合、転送 BLOB の JSON シリアル化は次のようになります。

```json
{
  "schema_version": "1.0.0",
  "header":
  {
    "kid": "<key identifier of the KEK>",
    "alg": "dir",
    "enc": "CKM_RSA_AES_KEY_WRAP"
  },
  "ciphertext":"BASE64URL(<ciphertext contents>)",
  "generator": "BYOK tool name and version; source HSM name and firmware version"
}

```

* kid = KEK のキー識別子。 Key Vault キーの場合は、次のようになります。 https://ContosoKeyVaultHSM.vault.azure.net/keys/mykek/eba63d27e4e34e028839b53fac905621
* alg = アルゴリズム。 
* dir = Direct モード。つまり、参照されている kid を使用して、CKM_RSA_AES_KEY_WRAP の正確な表現である暗号化テキストが直接保護されます。
* generator = BYOK ツールの名前とバージョン、およびソース HSM の製造元とモデルを示す情報フィールド。 この情報は、トラブルシューティングとサポートで使用することを目的としています。

'Add-AzKeyVaultKey' (PSH) または 'az keyvault key import' (CLI) コマンドの使用時に Azure PowerShell/CLI クライアントによって適切に処理されるように、JSON BLOB はファイルに格納され、拡張子が ".byok" になります。

### <a name="step-4-upload-key-transfer-blob-to-import-hsm-key"></a>手順 4:HSM キーをインポートするためにキー転送 BLOB をアップロードする

顧客は、キー転送 BLOB (".byok" ファイル) をオンライン ワークステーションに転送した後、**az keyvault key import** コマンドを実行して、この BLOB を新しい HSM でサポートされるキーとして Key Vault にインポートします。 

RSA キーをインポートするには、このコマンドを使用します。
```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok --ops encrypt decrypt
```
EC キーをインポートするには、キーの種類と曲線名を指定する必要があります。

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file --kty EC-HSM --curve-name "P-256" KeyTransferPackage-ContosoFirstHSMkey.byok --ops sign verify
```


上記のコマンドを実行すると、次のような REST API 要求が送信されます。

```
PUT https://contosokeyvaulthsm.vault.azure.net/keys/ContosoFirstHSMKey?api-version=7.0
```

RSA キーをインポートするときの要求本文:
```json
{
  "key": {
    "kty": "RSA-HSM",
    "key_ops": [
      "decrypt",
      "encrypt"
    ],
    "key_hsm": "<Base64 encoded BYOK_BLOB>"
  },
  "attributes": {
    "enabled": true
  }
}
```

EC キーをインポートするときの要求本文:
```json
{
  "key": {
    "kty": "EC-HSM",
    "crv": "P-256",
    "key_ops": [
      "sign",
      "verify"
    ],
    "key_hsm": "<Base64 encoded BYOK_BLOB>"
  },
  "attributes": {
    "enabled": true
  }
}
```

"key_hsm" 値は、Base64 形式でエンコードされた KeyTransferPackage-ContosoFirstHSMkey.byok の内容全体です。

## <a name="references"></a>References
- [Key Vault 開発者ガイド](../general/developers-guide.md)

## <a name="next-steps"></a>次のステップ
* 詳細な BYOK の手順:[HSM で保護されたキーを Key Vault にインポートする (BYOK)](hsm-protected-keys-byok.md)

