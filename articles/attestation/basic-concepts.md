---
title: Azure Attestation の基本的な概念
description: Azure Attestation の基本的な概念。
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.custom: references_regions
ms.openlocfilehash: 3cd7d2541cb980fc5ca6a1a9c42a430eac1ecb1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99429281"
---
# <a name="basic-concepts"></a>基本的な概念

以下では、Microsoft Azure Attestation に関する基本的な概念をいくつか紹介します。

## <a name="json-web-token-jwt"></a>JSON Web トークン (JWT)

[JSON Web トークン](https://jwt.io/) (JWT) は、当事者間で情報を JavaScript Object Notation (JSON) オブジェクトとして安全に転送するためのオープン標準 [RFC7519](https://tools.ietf.org/html/rfc7519) の手法です。 この情報はデジタル署名されているため、検証して信頼することができます。 JWT の署名には、シークレットを使用するか、公開/秘密キーの組を使用します。

## <a name="json-web-key-jwk"></a>JSON Web Key (JWK)

[JSON Web Key](https://tools.ietf.org/html/rfc7517) (JWK) は、暗号化キーを表す JSON データ構造です。 この仕様には、JWK のセットを表す JWK Set JSON データ構造も定義されています。

## <a name="attestation-provider"></a>構成証明プロバイダー

構成証明プロバイダーは、Microsoft.Attestation という名前の Azure リソース プロバイダーに属しています。 リソース プロバイダーは、Azure Attestation の REST コントラクトを提供するサービス エンドポイントであり、[Azure Resource Manager](../azure-resource-manager/management/overview.md) を使用してデプロイされます。 構成証明プロバイダーはそれぞれ、検出可能な特定のポリシーに従います。 構成証明プロバイダーは、構成証明タイプごとに既定のポリシーを使用して作成されます (VBS エンクレーブには既定のポリシーがないことに注意してください)。 SGX の既定のポリシーについて詳しくは、「[構成証明ポリシーの例](policy-examples.md)」を参照してください。

### <a name="regional-shared-provider"></a>リージョンの共有プロバイダー

Azure Attestation には、利用可能なリージョンごとにその共有プロバイダーが用意されています。 構成証明にリージョンの共有プロバイダーを使用するか、カスタム ポリシーを使用した独自のプロバイダーを作成するかは、お客様が選択できます。 共有プロバイダーには、すべての Azure AD ユーザーがアクセスでき、そこに関連付けられているポリシーは変更できません。

| リージョン | 構成証明の URI | 
|--|--|
| 米国東部 | `https://sharedeus.eus.attest.azure.net` | 
| 米国西部 | `https://sharedwus.wus.attest.azure.net` | 
| 英国南部 | `https://shareduks.uks.attest.azure.net` | 
| 英国西部| `https://sharedukw.ukw.attest.azure.net  ` | 
| カナダ東部 | `https://sharedcae.cae.attest.azure.net` | 
| カナダ中部 | `https://sharedcac.cac.attest.azure.net` | 
| 北ヨーロッパ | `https://sharedneu.neu.attest.azure.net` | 
| 西ヨーロッパ| `https://sharedweu.weu.attest.azure.net` | 
| 米国東部 2 | `https://sharedeus2.eus2.attest.azure.net` | 
| 米国中部 | `https://sharedcus.cus.attest.azure.net` | 

## <a name="attestation-request"></a>構成証明要求

構成証明要求は、クライアント アプリケーションから構成証明プロバイダーに送信されるシリアル化された JSON オブジェクトです。 SGX エンクレーブ用の要求オブジェクトには、2 つのプロパティがあります。 
- "Quote" - "Quote" プロパティの値は、構成証明クォートの Base64URL エンコード表現を含む文字列です。
- "EnclaveHeldData" - "EnclaveHeldData" プロパティの値は、エンクレーブ保持データの Base64URL エンコード表現を含む文字列です。

Azure Attestation により、提供された "Quote" が検証され、そのクォートの reportData フィールドの先頭から 32 バイトの中で、与えられたエンクレーブ保持データの SHA256 ハッシュが確実に表現されます。 

## <a name="attestation-policy"></a>構成証明ポリシー

構成証明ポリシーは、構成証明の証拠を処理する際に使用されるもので、お客様が構成できます。 Azure Attestation の核となるのはポリシー エンジンです。これによって、証拠の構成要素となる要求が処理されます。 Azure Attestation が証拠に基づいて構成証明トークンを発行すべきかどうか、ひいては構成証明者を承認するかどうかが、ポリシーを使用して判断されます。 結果的に、すべてのポリシーにパスしなければ、JWT トークンは発行されません。

構成証明プロバイダーの既定のポリシーで要件を満たすことができない場合、お客様は、Azure Attestation でサポートされるリージョンであれば、どこにでもカスタム ポリシーを作成できます。 ポリシー管理は、Azure Attestation がお客様に提供する重要な機能です。 ポリシーは構成証明タイプ固有であり、エンクレーブを識別したり、出力トークンに要求を追加したり、出力トークン内の要求に変更を加えたりするときに使用できます。 

ポリシーのサンプルについては、[構成証明ポリシーの例](policy-examples.md)を参照してください。

## <a name="benefits-of-policy-signing"></a>ポリシーに署名することの利点

Azure Attestation から構成証明トークンが発行されるかどうかは、結局のところ構成証明ポリシーによって決まります。 また、構成証明トークンに生成される要求もポリシーによって決まります。 したがって、このサービスによって評価されるポリシーが、本当に管理者によって作成されたポリシーであり、外部のエンティティによって改ざんも改変もされていないということがきわめて重要となります。 

ポリシーを定義したり更新したりするための構成証明プロバイダーの承認モデルは、信頼モデルによって定義されます。  サポートされるモデルは 2 つです。1 つは Azure AD の承認に基づくもので、もう 1 つは、お客様が管理する暗号化キーの所有に基づくものです ("分離モデル" と呼ばれます)。  分離モデルを使用すると、お客様によって提出されたポリシーが改ざんされていないことを Azure Attestation が保証できます。

分離モデルでは、管理者が構成証明プロバイダーを作成し、信頼済みの一連の X.509 署名証明書をファイルに指定します。 その構成証明プロバイダーに対し、管理者は署名されたポリシーを追加することができます。 Azure Attestation は、構成証明要求を処理する間、ヘッダー内の "jwk" または "x5c" パラメーターによって表される公開キーを使用してポリシーの署名を検証します。  また、要求ヘッダーに含まれる公開キーが、構成証明プロバイダーに関連付けられている信頼済み署名証明書のリストに存在するかどうかも検証されます。 このようにして、証明書利用者 (Azure Attestation) は、自己が把握している X.509 証明書を使用して署名されたポリシーを信頼することができます。 

サンプルについては、[ポリシー署名者証明書の例](policy-signer-examples.md)を参照してください。

## <a name="attestation-token"></a>構成証明トークン

Azure Attestation の応答は、JWT を値として含んでいる JSON 文字列になります。 Azure Attestation は、要求をパッケージ化して、署名済みの JWT を生成します。 署名操作は、サブジェクト名が構成証明プロバイダーの AttestUri 要素と一致する自己署名証明書を使用して実行されます。

Get OpenID Metadata API からは、[OpenID Connect Discovery プロトコル](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig)によって規定された OpenID 構成応答が返されます。 この API では、Azure Attestation によって使用されている署名証明書に関するメタデータを取得します。

SGX エンクレーブに対して生成された JWT の例:

```
{
  "alg": "RS256",
  "jku": "https://tradewinds.us.attest.azure.net/certs",
  "kid": <self signed certificate reference to perform signature verification of attestation token,
  "typ": "JWT"
}.{
  "aas-ehd": <input enclave held data>,
  "exp": 1568187398,
  "iat": 1568158598,
  "is-debuggable": false,
  "iss": "https://tradewinds.us.attest.azure.net",
  "maa-attestationcollateral": 
    {
      "qeidcertshash": <SHA256 value of QE Identity issuing certs>,
      "qeidcrlhash": <SHA256 value of QE Identity issuing certs CRL list>,
      "qeidhash": <SHA256 value of the QE Identity collateral>,
      "quotehash": <SHA256 value of the evaluated quote>, 
      "tcbinfocertshash": <SHA256 value of the TCB Info issuing certs>, 
      "tcbinfocrlhash": <SHA256 value of the TCB Info issuing certs CRL list>, 
      "tcbinfohash": <SHA256 value of the TCB Info collateral>
     },
  "maa-ehd": <input enclave held data>,
  "nbf": 1568158598,
  "product-id": 4639,
  "sgx-mrenclave": <SGX enclave mrenclave value>,
  "sgx-mrsigner": <SGX enclave msrigner value>,
  "svn": 0,
  "tee": "sgx"
  "x-ms-attestation-type": "sgx", 
  "x-ms-policy-hash": <>,
  "x-ms-sgx-collateral": 
    {
      "qeidcertshash": <SHA256 value of QE Identity issuing certs>,
      "qeidcrlhash": <SHA256 value of QE Identity issuing certs CRL list>,
      "qeidhash": <SHA256 value of the QE Identity collateral>,
      "quotehash": <SHA256 value of the evaluated quote>, 
      "tcbinfocertshash": <SHA256 value of the TCB Info issuing certs>, 
      "tcbinfocrlhash": <SHA256 value of the TCB Info issuing certs CRL list>, 
      "tcbinfohash": <SHA256 value of the TCB Info collateral>
     },
  "x-ms-sgx-ehd": <>, 
  "x-ms-sgx-is-debuggable": true,
  "x-ms-sgx-mrenclave": <SGX enclave mrenclave value>,
  "x-ms-sgx-mrsigner": <SGX enclave msrigner value>, 
  "x-ms-sgx-product-id": 1, 
  "x-ms-sgx-svn": 1,
  "x-ms-ver": "1.0"
}.[Signature]
```
上で使用されている要求のいくつかは、非推奨と見なされてはいますが、完全にサポートされています。  今後作成するすべてのコードおよびツールには、非推奨となっていない要求名の使用をお勧めします。 詳細については、[Azure Attestation によって発行される要求](claim-sets.md)に関するページを参照してください。

## <a name="encryption-of-data-at-rest"></a>保存データの暗号化

顧客データを保護するために、Azure Attestation では Azure Storage にデータが保持されます。 Azure ストレージでは、データ センターへの書き込みの際に保存データが暗号化され、顧客がアクセスできるように暗号化が解除されます。 この暗号化は、Microsoft が管理する暗号化キーを使用して行われます。 

Azure Attestation では、Azure ストレージ内のデータを保護するだけでなく、Azure Disk Encryption (ADE) を利用してサービス VM も暗号化します。 Azure コンフィデンシャル コンピューティング環境のエンクレーブで実行されている Azure Attestation の場合、ADE 拡張機能は現時点ではサポートされていません。 このようなシナリオでは、データがメモリに格納されないようにするために、ページ ファイルは無効になっています。 

Azure Attestation インスタンスのローカル ハード ディスク ドライブに顧客データが保存されることはありません。


## <a name="next-steps"></a>次のステップ

- [構成証明ポリシーを作成して署名する方法](author-sign-policy.md)
- [PowerShell を使用して Azure Attestation を設定する](quickstart-powershell.md)
