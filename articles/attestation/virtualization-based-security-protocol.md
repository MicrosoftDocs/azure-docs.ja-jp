---
title: Azure Attestation の仮想化ベースのセキュリティ (VBS) プロトコル
description: VBS 構成証明プロトコル
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 38012c5b4bb9338c1200d9583256193ee8402c98
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99507915"
---
# <a name="trusted-platform-module-tpm-and-virtualization-based-securityvbs-enclave-attestation-protocol"></a>トラステッド プラットフォーム モジュール (TPM) と仮想化ベースのセキュリティ (VBS) エンクレーブ構成証明プロトコル 

強力なセキュリティ保証を提供するための Microsoft Azure Attestation は、信頼のチェーンが信頼のルート (TPM) からハイパーバイザーおよびセキュア カーネルの起動まで保持されることを検証することに依存します。 このためには、セキュリティで保護されたエンクレーブ内で信頼を確立する前に、Azure Attestation でマシンのブート状態を証明する必要があります。 オペレーティング システム、ハイパーバイザー、およびセキュア カーネル バイナリは、正しい公式の Microsoft 機関によって署名され、安全な方法で構成されている必要があります。 トラステッド プラットフォーム モジュール (TPM) とハイパーバイザーの正常性との間で信頼をバインドしたら、測定されたブート ログで提供される仮想化ベースのセキュリティ (VBS) エンクレーブ IDK を信頼できます。これにより、キーの組がエンクレーブによって生成されたことを確認し、構成証明レポートを作成できます。このレポートは、そのキーの信頼をバインドし、セキュリティ レベルやブート構成証明のプロパティなどの他の要求を含みます。 

VBS エンクレーブでは、セキュリティ基盤を確認する測定値を提供するために TPM が必要です。 VBS エンクレーブは、プロトコルの要求オブジェクトに追加された TPM エンドポイントによって証明されます。 

## <a name="protocol-messages"></a>プロトコル メッセージ

### <a name="init-message"></a>初期化メッセージ

#### <a name="direction"></a>Direction

クライアント -> Azure Attestation

#### <a name="payload"></a>ペイロード

```
{ 
  "type": "aikcert" 
} 
```

"type" (ASCII 文字列): 要求された構成証明の種類を表します。 現時点でサポートされているのは、"aikcert" のみです。

### <a name="challenge-message"></a>チャレンジ メッセージ

#### <a name="direction"></a>Direction

Azure Attestation -> クライアント

#### <a name="payload"></a>ペイロード

```
{ 
  "challenge": "<BASE64URL(CHALLENGE)>", 
  "service_context": "<BASE64URL(SERVICECONTEXT)>" 
} 
```

**challenge** (BASE64URL(オクテット)):サービスによって発行されたランダムな値です。

**service_context** (BASE64URL(オクテット)):サービスによって作成されたあいまいなコンテキスト。


### <a name="request-message"></a>要求メッセージ

#### <a name="direction"></a>Direction

クライアント -> Azure Attestation 

#### <a name="payload"></a>ペイロード

```
{
  "request": "<JWS>"
}
```

**request** (JWS):要求は、JSON Web Signature (JWS) 構造で構成されます。 JWS Protected Header と JWS Payload を次に示します。 すべての JWS 構造と同様に、最終的な値は次のように構成されます。

BASE64URL(UTF8(JWS Protected Header)) || '.' ||

BASE64URL(JWS Payload) || '.' ||

BASE64URL(JWS Signature)

##### <a name="jws-protected-header"></a>JWS Protected Header

```
{
  "alg": "PS256",
  "typ": "attReq"
  // no "kid" parameter as the key specified by attest_key MUST sign this JWS to prove possession.
}
```

##### <a name="jws-payload"></a>JWS Payload

JWS ペイロードの種類には、basic または VBS を指定できます。 basic は、構成証明の証拠に VBS データが含まれていない場合に使用されます。 

TPM のみのサンプル: 

``` 
{ 
  "att_type": "basic", 
  "att_data": { 
    "rp_id": "<URL>", 
    "rp_data": "<BASE64URL(RPCUSTOMDATA)>", 
    "challenge": "<BASE64URL(CHALLENGE)>", 

    "tpm_att_data": { 
      "srtm_boot_log": "<BASE64URL(SRTMBOOTLOG)>", 
      "srtm_resume_log": "<BASE64URL(SRTMRESUMELOG)>", 
      "drtm_boot_log": "<BASE64URL(DRTMBOOTLOG)>", 
      "drtm_resume_log": "<BASE64URL(DRTMRESUMELOG)>", 
      "aik_cert": "<BASE64URL(AIKCERTIFICATE)>", 

      // aik_pub is represented as a JSON Web Key (JWK) object (RFC 7517). 

      "aik_pub": { 
        "kty": "RSA", 
        "n": "<Base64urlUInt(MODULUS)>", 
        "e": "<Base64urlUInt(EXPONENT)>" 
      }, 
      "current_claim": "<BASE64URL(CURRENTCLAIM)>", 
      "boot_claim": "<BASE64URL(BOOTCLAIM)>" 
    }, 

    // attest_key is represented as a JSON Web Key (JWK) object (RFC 7517). 

    "attest_key": { 
      "kty": "RSA", 
      "n": "<Base64urlUInt(MODULUS)>", 
      "e": "<Base64urlUInt(EXPONENT)>" 
    }, 
    "custom_claims": [ 
      { 
        "name": "<name>", 
        "value": "<value>", 
        "value_type": "<value_type>" 
      }, 
      { 
        "name": "<name>", 
        "value": "<value>", 
        "value_type": "<value_type>" 
      } 
    ], 
    "service_context": "<BASE64URL(SERVICECONTEXT)>" 
  } 
} 
```

TPM と VBS エンクレーブのサンプル: 

``` 
{ 
  "att_type": "vbs", 
  "att_data": { 
    "report_signed": { 
      "rp_id": "<URL>", 
      "rp_data": "<BASE64URL(RPCUSTOMDATA)>", 
      "challenge": "<BASE64URL(CHALLENGE)>", 
      "tpm_att_data": { 
        "srtm_boot_log": "<BASE64URL(SRTMBOOTLOG)>", 
        "srtm_resume_log": "<BASE64URL(SRTMRESUMELOG)>", 
        "drtm_boot_log": "<BASE64URL(DRTMBOOTLOG)>", 
        "drtm_resume_log": "<BASE64URL(DRTMRESUMELOG)>", 
        "aik_cert": "<BASE64URL(AIKCERTIFICATE)>", 

        // aik_pub is represented as a JSON Web Key (JWK) object (RFC 7517). 

        "aik_pub": { 
          "kty": "RSA", 
          "n": "<Base64urlUInt(MODULUS)>", 
          "e": "<Base64urlUInt(EXPONENT)>" 
        }, 
        "current_claim": "<BASE64URL(CURRENTCLAIM)>", 
        "boot_claim": "<BASE64URL(BOOTCLAIM)>" 
      }, 

      // attest_key is represented as a JSON Web Key (JWK) object (RFC 7517). 

      "attest_key": { 
        "kty": "RSA", 
        "n": "<Base64urlUInt(MODULUS)>", 
        "e": "<Base64urlUInt(EXPONENT)>" 
      }, 
      "custom_claims": [ 
        { 
          "name": "<name>", 
          "value": "<value>", 
          "value_type": "<value_type>" 
        }, 
        { 
          "name": "<name>", 
          "value": "<value>", 
          "value_type": "<value_type>" 
        } 
      ], 
      "service_context": "<BASE64URL(SERVICECONTEXT)>" 
    }, 
    "vsm_report": "<BASE64URL(REPORT)>" 
  } 
} 
``` 

**rp_id** (文字列または URI):証明書利用者の識別子です。 マシン ID 要求の評価においてサービスによって使用されます

**rp_data** (BASE64URL(オクテット)):証明書利用者によって渡される不透明データです。 これは通常、レポートの鮮度を保証するために、証明書利用者によって nonce として使用されます

**challenge** (BASE64URL(オクテット)):サービスによって発行されたランダムな値

**tpm_att_data**:TPM 関連の構成証明データ

- **srtm_boot_log (BASE64URL(オクテット))** :ログの種類 = TBS_TCGLOG_SRTM_BOOT を指定した関数 Tbsi_Get_TCG_Log_Ex によって取得された SRTM ブート ログ

- **srtm_resume_log (BASE64URL(オクテット))** :ログの種類 = TBS_TCGLOG_SRTM_RESUME を指定した関数 Tbsi_Get_TCG_Log_Ex によって取得された SRTM 再開ログ

- **drtm_boot_log (BASE64URL(オクテット))** :ログの種類 = TBS_TCGLOG_DRTM_BOOT を指定した関数 Tbsi_Get_TCG_Log_Ex によって取得された DRTM ブート ログ

- **drtm_resume_log (BASE64URL(オクテット))** :ログの種類 = TBS_TCGLOG_DRTM_RESUME を指定した関数 Tbsi_Get_TCG_Log_Ex によって取得された DRTM 再開ログ

- **aik_cert** (BASE64URL(オクテット)):プロパティ = NCRYPT_CERTIFICATE_PROPERTY を指定した関数 NCryptGetProperty から返される、AIK 用の x.509 証明書

- **aik_pub**:JSON Web Key (JWK) オブジェクト (RFC 7517) として表される AIK の公開部分

- **current_claim (BASE64URL(オクテット))** :関数 NCryptCreateClaim (dwClaimType = NCRYPT_CLAIM_PLATFORM と、すべての PCR を含むように設定したパラメーター NCRYPTBUFFER_TPM_PLATFORM_CLAIM_PCR_MASK を指定) から返される、現在の PCR 状態についての構成証明要求。 サービスによって送信されるチャレンジは、この要求の評価にも使用される必要があります

- **boot_claim (BASE64URL(オクテット))** :関数 NCryptCreateClaim (dwClaimType = NCRYPT_CLAIM_PLATFORM と、すべての PCR を含むように設定したパラメーター NCRYPTBUFFER_TPM_PLATFORM_CLAIM_PCR_MASK を指定) から返される、ブート時の PCR 状態についての構成証明要求

**vsm_report** (BASE64URL(オクテット)):関数 EnclaveGetAttestationReport から返される VBS エンクレーブ構成証明レポート。 EnclaveData パラメーターは、report_signed の値 (左中かっこと右中かっこを含む) の SHA-512 ハッシュである必要があります。 ハッシュ関数の入力は UTF8(report_signed) です

**attest_key**:JSON Web Key (JWK) オブジェクト(RFC 7517) として表されるエンクレーブ キーの公開部分

**custom_claims**:ポリシーによって評価できる、サービスに送信されるカスタム エンクレーブ要求の配列。 要求について次のように指定します。

- **name (文字列)** :要求の名前です。 この名前は、構成証明サービスによって決定された URL に追加され (競合を回避するため)、連結された文字列はポリシーで使用できる要求の種類になります

- **value (文字列)** :要求の値

- **value_type (文字列)** :要求の値のデータ型

**service_context** (BASE64URL(オクテット)):サービスによって作成されたあいまいなコンテキスト。

### <a name="report-message"></a>レポート メッセージ

#### <a name="direction"></a>Direction

Azure Attestation -> クライアント

#### <a name="payload"></a>ペイロード

```
{
  "report": "<JWT>"
}
```

**report** (JWT):JSON Web Token (JWT) 形式 (RFC 7519) の構成証明レポート。

## <a name="next-steps"></a>次のステップ

- [Azure Attestation ワークフロー](workflow.md)
