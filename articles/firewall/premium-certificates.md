---
title: Azure Firewall Premium プレビューの証明書
description: Azure Firewall Premium プレビューで TLS 検査を適切に構成するには、中間 CA 証明書を構成してインストールする必要があります。
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: victorh
ms.openlocfilehash: 47ebc752dedd72bbdedc02908911f1686584acda
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102615501"
---
# <a name="azure-firewall-premium-preview-certificates"></a>Azure Firewall Premium プレビューの証明書 

> [!IMPORTANT]
> 現在、Azure Firewall Premium はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

 Azure Firewall Premium プレビューの TLS 検査を適切に構成するには、有効な中間 CA 証明書を用意し、それを Azure Key Vault に格納する必要があります。

## <a name="certificates-used-by-azure-firewall-premium-preview"></a>Azure Firewall Premium プレビューによって使用される証明書

通常のデプロイで使用される証明書には 3 つの種類があります。

- **中間 CA 証明書 (CA 証明書)**

   証明機関 (CA) は、デジタル証明書に署名する、信頼された組織です。 CA は、証明書を要求する会社または個人の身元と正当性を検証します。 検証に合格すると、CA は署名済み証明書を発行します。 サーバーが SSL/TLS ハンドシェイク中にクライアント (たとえば、Web ブラウザー) に証明書を提示すると、クライアントは "*既知の正当な*" 署名者の一覧と照合して署名を検証しようとします。 通常 Web ブラウザーには、ホストを識別するために暗黙的に信頼されている CA の一覧が用意されています。 独自の証明書に署名する一部のサイトのように、機関がその一覧に含まれていない場合、ブラウザーは、認識されている機関によって証明書が署名されていないことをユーザーに警告し、未検証のサイトとの通信を続行するかどうかをユーザーに尋ねます。

- **サーバー証明書 (Web サイト証明書)**

   特定のドメイン名に関連付けられている証明書。 Web サイトに有効な証明書がある場合、それは、Web アドレスが実際にその組織に属していることを検証する手順が証明機関によって行われたことを意味します。 安全な Web サイトへの URL を入力するかリンクをたどると、ブラウザーは、証明書に次の特性があるかどうかを確認します。
   - Web サイトのアドレスが証明書のアドレスと一致している。
   - "*信頼された*" 機関としてブラウザーによって認識されている証明機関によって証明書が署名されている。
   
   ときにより、信頼されていない証明書を持つサーバーにユーザーが接続する場合があります。 Azure Firewall は、サーバーによって接続が終了されたかのように接続を切断します。

- **ルート CA 証明書 (ルート証明書)**

   証明機関は、ツリー構造の形式で複数の証明書を発行することができます。 ルート証明書は、ツリーの最上位にある証明書です。

Azure Firewall Premium プレビューは、送信 HTTP/S トラフィックをインターセプトし、`www.website.com` のサーバー証明書を自動生成できます。 この証明書は、ユーザーが提供する中間 CA 証明書を使用して生成されます。 この手順を正しく行うには、エンドユーザーのブラウザーとクライアント アプリケーションによって組織のルート CA 証明書または中間 CA 証明書が信頼される必要があります。 

:::image type="content" source="media/premium-certificates/certificate-process.png" alt-text="証明書のプロセス":::

## <a name="intermediate-ca-certificate-requirements"></a>中間 CA 証明書の要件

CA 証明書が次の要件に従っていることを確認してください。

- Key Vault シークレットとしてデプロイする場合、証明書と秘密キーを使用するパスワードレス PFX (Pkcs12) を使用する必要があります。

- 単一の証明書でなければならず、証明書チェーン全体を含めることはできません。  

- 今後 1 年間有効でなければなりません。  

- 最小サイズが 4096 バイトである RSA 秘密キーでなければなりません。  

- `KeyCertSign` フラグによって Critical とマーク付けされた `KeyUsage` 拡張が必要です (RFC 5280、4.2.1.3 Key Usage)。

- Critical とマーク付けされた `BasicContraints` 拡張が必要です (RFC 5280、4.2.1.9 Basic Constraints)。  

- `CA` フラグが TRUE に設定されている必要があります。

- パスの長さは 1 以上でなければなりません。

## <a name="azure-key-vault"></a>Azure Key Vault

[Azure Key Vault](../key-vault/general/overview.md) はプラットフォームマネージド シークレット ストアです。シークレット、キー、TLS または SSL 証明書を保護するために使用できます。 Azure Firewall Premium では、ファイアウォール ポリシーにアタッチされているサーバー証明書用の Key Vault との統合がサポートされます。
 
キー コンテナーを構成するには:

- キーの組を含む既存の証明書をキー コンテナーにインポートする必要があります。 
- または Base-64 でエンコードされたパスワードレスの PFX ファイルとして格納されているキー コンテナー シークレットを使用することもできます。  PFX ファイルは、秘密キーと公開キーの両方を含むデジタル証明書です。
- 証明書の有効期限に基づくアラートを構成できるため、CA 証明書のインポートを使用することをお勧めします。
- 証明書またはシークレットをインポートしたら、キー コンテナーでアクセス ポリシーを定義して、その ID に証明書またはシークレットの取得アクセス権が付与されるようにする必要があります。
- 提供された CA 証明書は、Azure ワークロードによって信頼される必要があります。 これらが正しくデプロイされていることを確認します。

Azure Firewall がユーザーに代わって Key Vault から証明書を取得するために使用する、ユーザー割り当てマネージド ID を作成することも、既存のものを再利用することもできます。 詳細については、「[Azure リソースのマネージド ID とは](../active-directory/managed-identities-azure-resources/overview.md)」を参照してください。 

## <a name="configure-a-certificate-in-your-policy"></a>ポリシー内で証明書を構成する

Firewall Premium ポリシー内で CA 証明書を構成するには、ポリシーを選択し、 **[TLS 検査 (プレビュー)]** を選択します。 **[TLS 検査]** ページで **[有効化済み]** を選択します。 次に、次の図に示すように、Azure Key Vault で CA 証明書を選択します。

:::image type="content" source="media/premium-certificates/tls-inspection.png" alt-text="Azure Firewall Premium の概要を示す図":::
 
> [!IMPORTANT]
> Azure portal から証明書を表示して構成するには、Key Vault アクセス ポリシーに Azure ユーザー アカウントを追加する必要があります。 **[シークレットのアクセス許可]** で、ユーザー アカウントに **[取得]** と **[一覧表示]** を付与します。
   :::image type="content" source="media/premium-certificates/secret-permissions.png" alt-text="Azure Key Vault のアクセス ポリシー":::


## <a name="create-your-own-self-signed-ca-certificate"></a>独自の自己署名 CA 証明書を作成する

TLS 検査をテストして検証するために、次のスクリプトを使用して、独自の自己署名ルート CA と中間 CA を作成することができます。

> [!IMPORTANT]
> 運用時には、企業の PKI を使用して中間 CA 証明書を作成する必要があります。 企業の PKI では、既存のインフラストラクチャを活用して、すべてのエンドポイント マシンへのルート CA の配布を処理します。 詳細については、「[Azure Firewall プレビュー用にエンタープライズ CA 証明書をデプロイおよび構成する](premium-deploy-certificates-enterprise-ca.md)」を参照してください。

このスクリプトには、次の 2 つのバージョンがあります。
- Bash スクリプト `cert.sh` 
- PowerShell スクリプト `cert.ps1` 

 また、両方のスクリプトで `openssl.cnf` 構成ファイルが使用されています。 スクリプトを使用するには、`openssl.cnf`、`cert.sh`、または`cert.ps1` の内容をローカル コンピューターにコピーします。

スクリプトでは、次のファイルが生成されます。
- rootCA.crt/rootCA.key - ルート CA の公開証明書と秘密キー
- interCA.crt/interCA.key - 中間 CA の公開証明書と秘密キー
- interCA.pfx - ファイアウォールで使用される中間 CA pkcs12 パッケージ

> [!IMPORTANT]
> rootCA.key は、セキュリティで保護されたオフラインの場所に格納する必要があります。 このスクリプトでは、有効期間が 1024 日である証明書が生成されます。
> スクリプトには、ローカル コンピューターにインストールされた openssl バイナリが必要です。 詳しくは、https://www.openssl.org/ をご覧ください。
> 
証明書を作成したら、次の場所に配置します。
- rootCA.crt - エンドポイント マシンに配置します (公開証明書のみ)。
- interCA.pfx - 証明書としてキー コンテナーにインポートし、ファイアウォール ポリシーに割り当てます。

### <a name="opensslcnf"></a>**openssl.cnf**
```
[ req ]
default_bits        = 4096
distinguished_name  = req_distinguished_name
string_mask         = utf8only
default_md          = sha512

[ req_distinguished_name ]
countryName                     = Country Name (2 letter code)
stateOrProvinceName             = State or Province Name
localityName                    = Locality Name
0.organizationName              = Organization Name
organizationalUnitName          = Organizational Unit Name
commonName                      = Common Name
emailAddress                    = Email Address

[ rootCA_ext ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:true
keyUsage = critical, digitalSignature, cRLSign, keyCertSign

[ interCA_ext ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:true, pathlen:1
keyUsage = critical, digitalSignature, cRLSign, keyCertSign

[ server_ext ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:false
keyUsage = critical, digitalSignature
extendedKeyUsage = serverAuth
```

###  <a name="bash-script---certsh"></a>Bash スクリプト - cert.sh 
```bash
#!/bin/bash

# Create root CA
openssl req -x509 -new -nodes -newkey rsa:4096 -keyout rootCA.key -sha256 -days 1024 -out rootCA.crt -subj "/C=US/ST=US/O=Self Signed/CN=Self Signed Root CA" -config openssl.cnf -extensions rootCA_ext

# Create intermediate CA request
openssl req -new -nodes -newkey rsa:4096 -keyout interCA.key -sha256 -out interCA.csr -subj "/C=US/ST=US/O=Self Signed/CN=Self Signed Intermediate CA"

# Sign on the intermediate CA
openssl x509 -req -in interCA.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out interCA.crt -days 1024 -sha256 -extfile openssl.cnf -extensions interCA_ext

# Export the intermediate CA into PFX
openssl pkcs12 -export -out interCA.pfx -inkey interCA.key -in interCA.crt -password "pass:"

echo ""
echo "================"
echo "Successfully generated root and intermediate CA certificates"
echo "   - rootCA.crt/rootCA.key - Root CA public certificate and private key"
echo "   - interCA.crt/interCA.key - Intermediate CA public certificate and private key"
echo "   - interCA.pfx - Intermediate CA pkcs12 package which could be uploaded to Key Vault"
echo "================"
```

### <a name="powershell---certps1"></a>PowerShell - cert.ps1
```powershell
# Create root CA
openssl req -x509 -new -nodes -newkey rsa:4096 -keyout rootCA.key -sha256 -days 3650 -out rootCA.crt -subj '/C=US/ST=US/O=Self Signed/CN=Self Signed Root CA' -config openssl.cnf -extensions rootCA_ext

# Create intermediate CA request
openssl req -new -nodes -newkey rsa:4096 -keyout interCA.key -sha256 -out interCA.csr -subj '/C=US/ST=US/O=Self Signed/CN=Self Signed Intermediate CA'

# Sign on the intermediate CA
openssl x509 -req -in interCA.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out interCA.crt -days 3650 -sha256 -extfile openssl.cnf -extensions interCA_ext

# Export the intermediate CA into PFX
openssl pkcs12 -export -out interCA.pfx -inkey interCA.key -in interCA.crt -password 'pass:'

Write-Host ""
Write-Host "================"
Write-Host "Successfully generated root and intermediate CA certificates"
Write-Host "   - rootCA.crt/rootCA.key - Root CA public certificate and private key"
Write-Host "   - interCA.crt/interCA.key - Intermediate CA public certificate and private key"
Write-Host "   - interCA.pfx - Intermediate CA pkcs12 package which could be uploaded to Key Vault"
Write-Host "================"

```

## <a name="troubleshooting"></a>トラブルシューティング

CA 証明書が有効であっても、TLS 検査で FQDN や URL にアクセスできない場合は、次の項目を確認してください。

- Web サーバー証明書が有効であることを確認します。  

- ルート CA 証明書がクライアント オペレーティング システムにインストールされていることを確認します。  

- ブラウザーまたは HTTPS クライアントに有効なルート証明書が含まれていることを確認します。 Firefox や、その他いくつかのブラウザーには、特殊な証明書ポリシーが含まれる場合があります。  

- アプリケーション規則内の URL の宛先の種類で、正しいパスと、宛先 HTML ページに組み込まれたその他のハイパーリンクが対象に含まれることを確認します。 ワイルドカードを使用すると、必要な URL パス全体を簡単に対象範囲に含めることができます。  


## <a name="next-steps"></a>次のステップ

- [Azure Firewall Premium の機能の詳細を確認する](premium-features.md)
