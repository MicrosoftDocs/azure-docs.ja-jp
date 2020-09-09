---
title: Azure Attestation ポリシーを作成して署名する方法
description: 構成証明ポリシーを作成して署名する方法についての説明。
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: fe901960c08c83484d9446c9030a273fa185b456
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89237053"
---
# <a name="how-to-author-and-sign-an-attestation-policy"></a>構成証明ポリシーを作成して署名する方法

構成証明ポリシーは、Microsoft Azure Attestation にアップロードされるファイルです。 Azure Attestation により、構成証明固有の形式でポリシーをアップロードする柔軟性が得られます。 また、JSON Web Signature でエンコードしたバージョンのポリシーをアップロードすることもできます。 構成証明ポリシーの作成を担うのはポリシー管理者です。 構成証明のほとんどのシナリオでは、証明書利用者がポリシー管理者としての役割を果たします。 構成証明の呼び出し側のクライアントが構成証明の証拠を送信し、その証拠をサービスが解析して入力要求 (一連のプロパティ、値) に変換します。 その要求が、ポリシーに定義されている内容に応じてサービスによって処理され、計算結果が返されます。

ポリシーには、構成証明トークンの内容、プロパティ、承認の基準を決めるルールが含まれています。 ポリシー ファイルのサンプルを次に示します。

```
version=1.0;
authorizationrules
{
   c:[type="secureBootEnables", issuer=="AttestationService"]=> permit()
};

issuancerules
{
  c:[type="secureBootEnables", issuer=="AttestationService"]=> issue(claim=c)
  c:[type="notSafeMode", issuer=="AttestationService"]=> issue(claim=c)
};
```
 
ご覧のように、ポリシー ファイルには次の 3 つのセグメントがあります。

- **version**: 準拠している文法のバージョン番号です。 

    ```
    version=MajorVersion.MinorVersion   
    ```

    現在サポートされているのはバージョン 1.0 のみです。

- **authorizationrules**: Azure Attestation が **issuancerules** に進むべきかどうかを判断するために最初にチェックされる要求規則のコレクションです。 要求規則は、定義されている順に適用されます。

- **issuancerules**: ポリシーに定義されている構成証明の結果に他の情報を追加する目的で評価される要求規則のコレクションです。 要求規則は、定義されている順に適用されます。省略することもできます。

詳細については、「[要求と要求規則](claim-rule-grammar.md)」を参照してください。
   
## <a name="drafting-the-policy-file"></a>ポリシー ファイルの下書きを作成する

1. 新しいファイルを作成します。
1. ファイルにバージョンを追加します。
1. **authorizationrules** と **issuancerules** のセクションを追加します。

  ```
  version=1.0;
  authorizationrules={
  =>deny();
  };
  
  issuancerules={
  };
  ```

  この承認規則には、条件のない deny() アクションが含まれているため、発行規則は処理されません。 または、承認規則に permit() アクションを追加して、発行規則の処理を許可することもできます。
  
4. 承認規則に要求規則を追加します。

  ```
  version=1.0;
  authorizationrules={
  [type=="secureBootEnabled", value==true, issuer=="AttestationService"]=>permit();
  };
  
  issuancerules={
  };
  ```

  入力要求セットに、type、value、issuer に一致する要求が含まれていた場合、permit() アクションによって、**issuancerules** を処理するようポリシー エンジンに伝えられます。
  
5. **issuancerules** に要求規則を追加します。

  ```
  version=1.0;
  authorizationrules={
  [type=="secureBootEnabled", value==true, issuer=="AttestationService"]=>permit();
  };
  
  issuancerules={
  => issue(type="SecurityLevelValue", value=100);
  };
  ```
  
  出力要求セットには、次の内容の要求が含まれることになります。

  ```
  [type="SecurityLevelValue", value=100, valueType="Integer", issuer="AttestationPolicy"]
  ```

  複雑なポリシーも同様の方法で作成できます。 詳細については、[構成証明ポリシーの例](policy-examples.md)を参照してください。
  
6. ファイルを保存します。

## <a name="creating-the-policy-file-in-json-web-signature-format"></a>JSON Web Signature 形式でポリシー ファイルを作成する

ポリシー ファイルを作成した後、JWS 形式でポリシーをアップロードするには、次の手順に従います。

1. UTF-8 でエンコードされたポリシーをペイロードとして含んだ JWS (RFC 7515) を生成します。
     - Base64Url でエンコードされたポリシーのペイロード ID は、"AttestationPolicy" である必要があります。
     
     サンプル JWT:
     ```
     Header: {"alg":"none"}
     Payload: {"AttestationPolicy":" Base64Url (policy)"}
     Signature: {}

     JWS format: eyJhbGciOiJub25lIn0.XXXXXXXXX.
     ```

2. (省略可) ポリシーに署名します。 Azure Attestation では、次のアルゴリズムがサポートされます。
     - **None**:ポリシーのペイロードは署名されません。
     - **RS256**: ポリシーのペイロードに署名する際にサポートされるアルゴリズムです。

3. JWS をアップロードして、ポリシーを検証します。
     - ポリシー ファイルに構文エラーがない場合、サービスによってポリシー ファイルが受理されます。
     - ポリシー ファイルに構文エラーがある場合、サービスによってポリシー ファイルが拒否されます。

## <a name="signing-the-policy"></a>ポリシーに署名する

次に示したのは、ポリシーの署名操作を実行する方法についてのサンプル Python スクリプトです。

```python
from OpenSSL import crypto
import jwt
import getpass
       
def cert_to_b64(cert):
              cert_pem = crypto.dump_certificate(crypto.FILETYPE_PEM, cert)
              cert_pem_str = cert_pem.decode('utf-8')
              return ''.join(cert_pem_str.split('\n')[1:-2])
       
print("Provide the path to the PKCS12 file:")
pkcs12_path = str(input())
pkcs12_password = getpass.getpass("\nProvide the password for the PKCS12 file:\n")
pkcs12_bin = open(pkcs12_path, "rb").read()
pkcs12 = crypto.load_pkcs12(pkcs12_bin, pkcs12_password.encode('utf8'))
ca_chain = pkcs12.get_ca_certificates()
ca_chain_b64 = []
for chain_cert in ca_chain:
   ca_chain_b64.append(cert_to_b64(chain_cert))
   signing_cert_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM, pkcs12.get_privatekey())
signing_cert_b64 = cert_to_b64(pkcs12.get_certificate())
ca_chain_b64.insert(0, signing_cert_b64)

print("Provide the path to the policy text file:")
policy_path = str(input())
policy_text = open(policy_path, "r").read()
encoded = jwt.encode({'text': policy_text }, signing_cert_pkey, algorithm='RS256', headers={'x5c' : ca_chain_b64})
print("\nAttestation Policy JWS:")
print(encoded.decode('utf-8'))
```

## <a name="next-steps"></a>次のステップ
- [PowerShell を使用して Azure Attestation を設定する](quickstart-powershell.md)
- [コード サンプルを使用して SGX エンクレーブの構成証明を行う](https://docs.microsoft.com/samples/browse/?expanded=azure&terms=attestation)
