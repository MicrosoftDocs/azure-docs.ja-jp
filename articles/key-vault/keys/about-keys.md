---
title: Azure Key Vault のキーについて - Azure Key Vault
description: キーに関する Azure Key Vault の REST インターフェイスと開発者の詳細の概要です。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: b9803726bf3a54eb31d3c2ebaddce11fb96472be
ms.sourcegitcommit: fdaad48994bdb9e35cdd445c31b4bac0dd006294
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/26/2020
ms.locfileid: "85413725"
---
# <a name="about-azure-key-vault-keys"></a>Azure Key Vault のキーについて

Azure Key Vault は複数のキーの種類とアルゴリズムをサポートし、価値の高いキーにハードウェア セキュリティ モジュールを使用できるようにします。

Key Vault の暗号化キーは、JSON Web Key (JWK) オブジェクトとして表されます。 JavaScript Object Notation (JSON) および JavaScript Object Signing and Encryption (JOSE) の仕様は、次のとおりです。

-   [JSON Web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JSON Web Encryption (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JSON Web Algorithms (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON Web Signature (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature) 

基本の JWK/JWA の仕様は、Key Vault の実装に固有のキーの種類も有効にするように拡張されます。 たとえば、HSM ベンダー固有のパッケージを使用してキーをインポートすると、Key Vault HSM でのみ使用できるキーの安全なトランスポートが可能です。 

Azure Key Vault では、ソフトウェアで保護するキーと HSM で保護するキーの両方がサポートされています。

- **ソフトウェアで保護するキー**:Key Vault によってソフトウェアで処理されるが、HSM 内のシステム キーを使って保存時に暗号化されるキー。 クライアントは、既存の RSA または EC (Elliptic Curve) キーをインポートするか、または Key Vault による生成を要求できます。
- **HSM で保護するキー**:HSM (ハードウェア セキュリティ モジュール) で処理されるキー。 これらのキーは、Key Vault HSM セキュリティ ワールドのいずれかで保護されます (分離を維持するために場所ごとに 1 つのセキュリティ ワールドがあります)。 クライアントは、RSA または EC キーをインポートできます (ソフトウェア保護形式で、または互換性のある HSM デバイスからエクスポートすることにより)。 クライアントは、Key Vault にキーの生成を要求することもできます。 このキーの種類では、HSM キー マテリアルを取得するために、key_hsm 属性が JWK に追加されます。

地理的境界について詳しくは、[Microsoft Azure トラスト センター](https://azure.microsoft.com/support/trust-center/privacy/) をご覧ください。  

## <a name="cryptographic-protection"></a>暗号化による保護

Key Vault では、RSA キーと楕円曲線キーのみがサポートされます。 

-   **EC**:ソフトウェアで保護される楕円曲線キー。
-   **EC-HSM**:"ハード" 楕円曲線キー。
-   **RSA**:ソフトウェアで保護される RSA キー。
-   **RSA-HSM**:"ハード" RSA キー。

Key Vault では、サイズが 2048、3072、4096 の RSA キーがサポートされています。 サポートされている楕円曲線キーの種類は P-256、P-384、P-521、P-256K (SECP256K1) です。

Key Vault が使う暗号化モジュールは、HSM でもソフトウェアでも、FIPS (Federal Information Processing Standards) で検証されます。 FIPS モードで実行するために特別なことを行う必要はありません。 HSM で保護されて**作成**または**インポート**されたキーは、HSM 内で処理され、FIPS 140-2 レベル 2 について検証されます。 ソフトウェアで保護されて**作成**または**インポート**されたキーは、暗号化モジュール内で処理され、FIPS 140-2 レベル 1 について検証されます。

###  <a name="ec-algorithms"></a>EC アルゴリズム
 Key Vault の EC および EC-HSM キーでは、次のアルゴリズム識別子がサポートされます。 

#### <a name="curve-types"></a>曲線の種類

-   **P-256** - NIST 曲線 P-256。[DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf) で定義されています。
-   **P-256K** - SEC 曲線 SECP256K1。「[SEC 2:Recommended Elliptic Curve Domain Parameters (SEC 2: 推奨される楕円曲線ドメイン パラメーター)](https://www.secg.org/sec2-v2.pdf)」で定義されています。
-   **P-384** - NIST 曲線 P-384。[DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf) で定義されています。
-   **P-521** - NIST 曲線 P-521。[DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf) で定義されています。

#### <a name="signverify"></a>SIGN/VERIFY

-   **ES256** - SHA-256 ダイジェストおよび P-256 曲線を使用して作成されたキーのための ECDSA。 このアルゴリズムは、[RFC7518](https://tools.ietf.org/html/rfc7518) で説明されます。
-   **ES256SHA-256** - SHA-256 ダイジェストおよび P-256K 曲線を使用して作成されたキーのための ECDSA。 このアルゴリズムは、標準としての承認待ちです。
-   **ES384** - SHA-384 ダイジェストおよび P-384 曲線を使用して作成されたキーのための ECDSA。 このアルゴリズムは、[RFC7518](https://tools.ietf.org/html/rfc7518) で説明されます。
-   **ES512** - SHA-512 ダイジェストおよび P-521 曲線を使用して作成されたキーのための ECDSA。 このアルゴリズムは、[RFC7518](https://tools.ietf.org/html/rfc7518) で説明されます。

###  <a name="rsa-algorithms"></a>RSA アルゴリズム  
 Key Vault の RSA および RSA-HSM キーでは、次のアルゴリズム識別子がサポートされます。  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY、ENCRYPT/DECRYPT

-   **RSA1_5** - RSAES-PKCS1-V1_5 [RFC3447] キー暗号化  
-   **RSA-OAEP** - Optimal Asymmetric Encryption Padding (OAEP) [RFC3447] を使用する RSAES。既定のパラメーターは RFC 3447 の Section A.2.1 で指定されています。 これらの既定パラメーターでは、SHA-1 のハッシュ関数と、SHA-1 による MGF1 のマスク生成関数が使用されます。  

#### <a name="signverify"></a>SIGN/VERIFY

-   **PS256** - [RFC7518](https://tools.ietf.org/html/rfc7518) に記載されている、SHA-256 を使用する RSASSA-PSS と SHA-256 を使用する MGF1。
-   **PS384** - [RFC7518](https://tools.ietf.org/html/rfc7518) に記載されている、SHA-384 を使用する RSASSA-PSS と SHA-384 を使用する MGF1。
-   **PS512** - [RFC7518](https://tools.ietf.org/html/rfc7518) に記載されている SHA-512 を使用する RSASSA-PSS と SHA-512 を使用する MGF1。
-   **RS256** - SHA-256 を使用する RSASSA-PKCS-v1_5。 アプリケーション提供のダイジェスト値は SHA-256 を使用して計算され、長さは 32 バイトである必要があります。  
-   **RS384** - SHA-384 を使用する RSASSA-PKCS-v1_5。 アプリケーション提供のダイジェスト値は SHA-384 を使用して計算され、長さは 48 バイトである必要があります。  
-   **RS512** - SHA-512 を使用する RSASSA-PKCS-v1_5。 アプリケーション提供のダイジェスト値は SHA-512 を使用して計算され、長さは 64 バイトである必要があります。  
-   **RSNULL** - RFC2437 を参照。特定の TLS シナリオを有効にする特殊なユース ケース。  

##  <a name="key-operations"></a>キーの操作

Key Vault では、キー オブジェクトに対する次の操作がサポートされます。  

-   **作成**:クライアントは、Key Vault にキーを作成できます。 キーの値は、Key Vault によって生成されて格納され、クライアントにはリリースされません。 Key Vault では非対称キーを作成できます。  
-   **Import**:クライアントは、既存のキーを Key Vault にインポートできます。 JWK コンストラクト内の複数のパッケージング方法を使用して、非対称キーを Key Vault にインポートできます。 
-   **更新**:十分なアクセス許可を持つクライアントは、以前に Key Vault に格納されたキーに関連付けられているメタデータ (キー属性) を変更できます。  
-   **[削除]** :十分なアクセス許可を持つクライアントは、Key Vault からキーを削除できます。  
-   **一覧表示**:クライアントは、特定の Key Vault 内のすべてのキーを一覧表示できます。  
-   **バージョンの一覧表示**:クライアントは、特定の Key Vault 内の特定のキーのすべてのバージョンを一覧表示できます。  
-   **取得**:クライアントは、Key Vault 内の特定のキーの公開部分を取得できます。  
-   **バックアップ**:保護された形式でキーをエクスポートします。  
-   **復元**:以前にバックアップしたキーをインポートします。  

詳しくは、[Key Vault REST API リファレンスのキー操作](/rest/api/keyvault)をご覧ください。  

Key Vault にキーが作成されたら、キーを使って次の暗号化操作を実行できます。  

-   **署名と検証**:Key Vault は署名作成の一部として内容のハッシュをサポートしていないため、厳密には、この操作は "ハッシュの署名" または "ハッシュの検証" です。 アプリケーションは、署名されるデータをローカルにハッシュした後、Key Vault にハッシュの署名を要求する必要があります。 "公開" キー マテリアルにアクセスできないアプリケーション用の便利な操作として、署名されたハッシュの検証がサポートされています。 アプリケーションのパフォーマンスを最高にするため、VERIFY 操作はローカルに実行する必要があります。  
-   **キーの暗号化/ラッピング**:Key Vault に格納されているキーを使って、別のキー (通常は、対称コンテンツ暗号化キー (CEK)) を保護できます。 Key Vault 内のキーが非対称の場合、キーの暗号化が使用されます。 たとえば、RSA-OAEP 操作と WRAPKEY/UNWRAPKEY 操作は、ENCRYPT/DECRYPT と同等です。 Key Vault 内のキーが対称の場合は、キー ラッピング キーが使用されます。 たとえば、AES-KW などです。 "公開" キー マテリアルにアクセスできないアプリケーション用の便利な操作として、WRAPKEY 操作がサポートされています。 アプリケーションのパフォーマンスを最高にするため、WRAPKEY 操作はローカルに実行する必要があります。  
-   **暗号化と復号化**:Key Vault に格納されているキーを使って、データの単一のブロックを暗号化または復号化できます。 ブロックのサイズは、キーの種類および選択されている暗号化アルゴリズムによって決まります。 "公開" キー マテリアルにアクセスできないアプリケーション用の便利な操作として、暗号化操作が提供されています。 アプリケーションのパフォーマンスを最高にするため、ENCRYPT 操作はローカルに実行する必要があります。  

非対称キーを使用する WRAPKEY/UNWRAPKEY は (ENCRYPT/DECRYPT と同等の操作なので) 余分に見えますが、個別の操作を使用することが重要です。 区別することで、これらの操作のセマンティックと承認の分離、および他のキーの種類がサービスによってサポートされている場合の整合性が提供されます。  

Key Vault では EXPORT 操作はサポートされていません。 キーがシステム内でプロビジョニングされた後では、キーを抽出したり、キー マテリアルを変更したりすることはできません。 ただし、削除後などの他のユース ケースで、Key Vault のユーザーがキーを必要とする場合があります。 その場合、ユーザーは、BACKUP および RESTORE 操作を使って、保護された形式でキーをエクスポート/インポートできます。 BACKUP 操作によって作成されたキーは、Key Vault の外部では使用できません。 代わりに、複数の Key Vault インスタンスに対して、IMPORT 操作を使用できます。  

ユーザーは、JWK オブジェクトの key_ops プロパティを使って、キー単位で、Key Vault がサポートする暗号化操作を制限できます。  

JWK オブジェクトについて詳しくは、「[JSON Web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)」をご覧ください。  

## <a name="key-attributes"></a>キーの属性

キー マテリアルに加えて、次の属性を指定できます。 JSON 要求では、属性を指定しない場合であっても、attributes キーワードとかっこ { } は必要です。  

- *enabled*: boolean、省略可能、既定値は **true**。 キーが有効になっていて、暗号化操作に使用できるかどうかを指定します。 *enabled* 属性は、*nbf* および *exp* と組み合わせて使います。*nbf* と *exp* の間で操作が発生する場合、*enabled* が **true** に設定されている場合にのみ許可されます。 *nbf* / *exp* ウィンドウの外部での操作は、[特定の条件](#date-time-controlled-operations)の下での特定の操作の種類を除き、自動的に禁止されます。
- *nbf*:IntDate、省略可能、既定値は現在。 *nbf* (not before: 開始日時) 属性は、[特定の条件](#date-time-controlled-operations)の下での特定の操作の種類を除き、それより前にはキーを暗号化操作に使用してはならない日時を示します。 *nbf* 属性の処理には、現在の日時が、*nbf* 属性で指定されている開始日時以降でなければなりません。 Key Vault では、クロックのスキューを考慮して、短い猶予 (通常は数分以下) を提供できます。 その値は、IntDate 値を含む数値でなければなりません。  
- *exp*:IntDate、省略可能、既定値は "無期限"。 *exp* (expiration time: 有効期限) 属性は、[特定の条件](#date-time-controlled-operations)の下での特定の操作の種類を除き、それを過ぎたらキーを暗号化操作に使用してはならない日時を示します。 *exp* 属性の処理には、現在の日時が、*exp* 属性で指定されている有効期限日時以前でなければなりません。 Key Vault では、クロックのスキューを考慮して、短い猶予 (通常は数分以下) を提供できます。 その値は、IntDate 値を含む数値でなければなりません。  

キー属性を含むすべての応答に含まれる追加の読み取り専用属性があります。  

- *created*:IntDate、省略可能。 *created* 属性は、このバージョンのキーが作成された日時を示します。 この属性が追加される前に作成されたキーについては、値は null です。 その値は、IntDate 値を含む数値でなければなりません。  
- *updated*:IntDate、省略可能。 *updated* 属性は、このバージョンのキーが更新された日時を示します。 この属性が追加される前に最後に更新されたキーについては、値は null です。 その値は、IntDate 値を含む数値でなければなりません。  

IntDate および他のデータ型について詳しくは、「キー、シークレット、証明書について」を参照してください。[データ型](../general/about-keys-secrets-certificates.md#data-types)。

### <a name="date-time-controlled-operations"></a>日付と時刻で制御される操作

*nbf* / *exp* ウィンドウの範囲外である、有効期間前および有効期間後のキーは、**復号化**、**ラップ解除**、**検証**操作には使用できます (403 Forbidden を返しません)。 有効期間前状態を使用する理由は、運用環境で使用する前にキーをテストできるようにすることです。 期限切れ状態を使用する理由は、キーが有効であったときに作成されたデータを回復できるようにすることです。 また、Key Vault のポリシーを使って、または *enabled* キー属性を **false** に更新することで、キーへのアクセスを無効にすることもできます。

データ型について詳しくは、「[データ型](../general/about-keys-secrets-certificates.md#data-types)」をご覧ください。

他の使用可能な属性について詳しくは、「[JSON Web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)」をご覧ください。

## <a name="key-tags"></a>キーのタグ

タグの形式で、アプリケーション固有の追加メタデータを指定できます。 Key Vault は最大 15 個のタグをサポートし、それぞれが 256 文字の名前と 256 文字の値を持つことができます。  

>[!Note]
>呼び出し元は、そのオブジェクトの種類 (キー、シークレット、証明書) に対して *list* または *get* アクセス許可を持っている場合、タグを読み取ることができます。

##  <a name="key-access-control"></a>キーのアクセス制御

Key Vault によって管理されているキーのアクセス制御は、キーのコンテナーとして機能する Key Vault のレベルで提供されます。 キーのアクセス制御ポリシーは、同じキー コンテナー内のシークレットに対するアクセス制御ポリシーとは別です。 ユーザーは、1 つまたは複数のコンテナーを作成してキーを保持することができ、キーのセグメント化と管理に適切なシナリオを維持する必要があります。 キーのアクセス制御は、シークレットのアクセス制御に依存しません。  

コンテナーのキー アクセス制御エントリでは、ユーザー/サービス プリンシパルごとに、次のアクセス許可を付与できます。 これらのアクセス許可は、キー オブジェクトに対して許可される操作を厳密に反映しています。  キー コンテナーのサービス プリンシパルへのアクセス許可は 1 回限りの操作であり、すべての Azure サブスクリプションで同じです。 これを使用して、必要な数だけ証明書をデプロイできます。 

- キー管理操作に対するアクセス許可
  - *get*:キーの公開部分とその属性を読み取ります
  - *list*:キー コンテナーに格納されているキーまたはキーのバージョンを一覧表示します
  - *update*:キーの属性を更新します
  - *create*:新しいキーを作成します
  - *import*:キーをキー コンテナーにインポートします
  - *delete*:キー オブジェクトを削除します
  - *recover*:削除されたキーを復旧します
  - *backup*:キー コンテナー内のキーをバックアップします
  - *restore*:バックアップしたキーをキー コンテナーに復元します

- 暗号化操作に対するアクセス許可
  - *decrypt*:キーを使ってバイト シーケンスの保護を解除します
  - *encrypt*:キーを使って任意のバイト シーケンスを保護します
  - *unwrapKey*:キーを使ってラップされた対称キーの保護を解除します
  - *wrapKey*:キーを使って対称キーを保護します
  - *verify*:キーを使ってダイジェストを検証します  
  - *sign*:キーを使ってダイジェストに署名します
    
- 特権操作に対するアクセス許可
  - *purge*:削除されたキーをパージ (完全に削除) します

キーの処理について詳しくは、[Key Vault REST API リファレンスのキーの操作](/rest/api/keyvault)に関するページをご覧ください。 アクセス許可の設定については、「[Vaults - Create or Update](/rest/api/keyvault/vaults/createorupdate)」(コンテナー - 作成または更新) および「[Vaults - Update Access Policy](/rest/api/keyvault/vaults/updateaccesspolicy)」(コンテナー -アクセス ポリシーの更新) をご覧ください。 

## <a name="next-steps"></a>次のステップ

- [Key Vault について](../general/overview.md)
- [キー、シークレット、証明書について](../general/about-keys-secrets-certificates.md)
- [シークレットについて](../secrets/about-secrets.md)
- [証明書について](../certificates/about-certificates.md)
- [認証、要求、応答](../general/authentication-requests-and-responses.md)
- [Key Vault 開発者ガイド](../general/developers-guide.md)
