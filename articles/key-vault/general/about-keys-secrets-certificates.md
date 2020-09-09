---
title: Azure Key Vault のキー、シークレット、証明書について - Azure Key Vault
description: キー、シークレット、証明書に関する Azure Key Vault の REST インターフェイスと開発者の詳細の概要です。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: overview
ms.date: 04/17/2020
ms.author: mbaldwin
ms.openlocfilehash: cb8a29c5d2eff46eecb2cf977bfb492f28731e68
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87043623"
---
# <a name="about-keys-secrets-and-certificates"></a>キー、シークレット、証明書について

Azure Key Vault では、Microsoft Azure アプリケーションとユーザーはいくつかの種類のシークレット/キー データを保存して使用できます。

- 暗号化キー:複数のキーの種類とアルゴリズムをサポートし、価値の高いキーにハードウェア セキュリティ モジュールを使用できるようにします。 詳細については、[キーについて](../keys/about-keys.md)のページを参照してください。
- シークレット:パスワードやデータベース接続文字列などのシークレットのセキュリティで保護されたストレージを提供します。 詳細については、[シークレットについて](../secrets/about-secrets.md)のページを参照してください。
- Certificates:キーとシークレットを基に構築され、自動更新機能を追加します。 詳細については、[証明書について](../certificates/about-certificates.md)のページを参照してください。
- Azure Storage:Azure Storage アカウントのキーを自動的に管理できます。 内部的には、Key Vault は Azure ストレージ アカウントのキーを一覧表示し (同期)、定期的にキーを再生成 (ローテーション) できます。 詳細については、[Key Vault を使用してストレージ アカウント キーを管理する方法](../secrets/overview-storage-keys.md)に関するページを参照してください。

Key Vault の一般的な情報については、「[Azure Key Vault について](overview.md)」を参照してください。

## <a name="data-types"></a>データ型

キー、暗号化、および署名に関連するデータ型については、JOSE の仕様をご覧ください。  

-   **algorithm** - キーの操作に対してサポートされるアルゴリズムです (RSA1_5 など)  
-   **ciphertext-value** - Base64URL を使用してエンコードされた暗号テキスト オクテットです  
-   **digest-value** - Base64URL を使用してエンコードされたハッシュ アルゴリズムの出力です  
-   **key-type** - サポートされているキーの種類の 1 つです (RSA (Rivest-Shamir-Adleman) など)。  
-   **plaintext-value** - Base64URL を使用してエンコードされたプレーンテキスト オクテットです  
-   **signature-value** - Base64URL を使用してエンコードされた署名アルゴリズムの出力です  
-   **base64URL** - Base64URL [RFC4648] でエンコードされたバイナリ値です  
-   **boolean** - true または false です  
-   **Identity** - Azure Active Directory (AAD) からの ID です。  
-   **IntDate** - 1970-01-01T0:0:0Z UTC から指定された UTC 日時までの秒数を表す JSON 10 進値です。 一般的および UTC 固有の日付/時刻に関する詳細については、RFC3339 をご覧ください。  

## <a name="objects-identifiers-and-versioning"></a>オブジェクト、識別子、バージョン管理

Key Vault に格納されるオブジェクトは、オブジェクトの新しいインスタンスが作成されるたびにバージョン管理されます。 各バージョンには、一意の識別子と URL が割り当てられます。 オブジェクトが最初に作成されるときに、オブジェクトに一意のバージョン識別子が指定され、オブジェクトの現在のバージョンとしてマークされます。 同じオブジェクト名の新しいインスタンスが作成されると、新しいオブジェクトに一意のバージョン識別子が与えられ、現在のバージョンになります。  

Key Vault 内のオブジェクトをアドレス指定する場合、バージョンを指定することも、オブジェクトの現在のバージョンに対する操作でバージョンを省略することもできます。 たとえば、キーの名前を `MasterKey` とした場合、バージョンを指定せずに操作を実行すると、システムは使用可能な最新のバージョンを使用します。 バージョン固有の識別子を指定して操作を実行すると、システムはオブジェクトの特定のバージョンを使用します。  

Key Vault 内のオブジェクトは、URL を使用して一意に識別されます。 地理的場所に関係なく、システム内の複数のオブジェクトが同じ URL を持つことはありません。 オブジェクトの完全な URL は、オブジェクト識別子と呼ばれます。 URL は、Key Vault を示すプレフィックス、オブジェクトの種類、ユーザー指定のオブジェクト名、およびオブジェクトのバージョンで構成されます。 オブジェクト名は大文字と小文字が区別されず、変更できません。 オブジェクトのバージョンを含まない識別子は、ベース識別子と呼ばれます。  

詳しくは、「[Authentication, requests, and responses](authentication-requests-and-responses.md)」(認証、要求、応答) をご覧ください。

オブジェクト識別子の一般的な形式は次のとおりです。  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

各値の説明:  

| 要素 | 説明 |  
|-|-|  
|`keyvault-name`|Microsoft Azure Key Vault サービスでのキー コンテナーの名前。<br /><br /> キー コンテナーの名前はユーザーが選択し、グローバルに一意です。<br /><br /> Key Vault の名前は、0 ～ 9、a ～ z、A ～ Z、- のみを使った 3 ～ 24 文字の文字列である必要があります。|  
|`object-type`|オブジェクトの種類で、"keys"、"secrets"、"certificates" のいずれかになります。|  
|`object-name`|`object-name` は、ユーザーが指定する名前で、キー コンテナー内で一意である必要があります。 名前は、文字で始まり、0 から 9、a から z、A から Z、および - のみを使った 1 から 127 文字の文字列である必要があります。|  
|`object-version`|`object-version` はシステムが生成し、オブジェクトの一意のバージョンに対応するために必要に応じて使用される 32 文字の文字列識別子です。|  

## <a name="next-steps"></a>次のステップ

- [キーについて](../keys/about-keys.md)
- [シークレットについて](../secrets/about-secrets.md)
- [証明書について](../certificates/about-certificates.md)
- [認証、要求、応答](../general/authentication-requests-and-responses.md)
- [Key Vault 開発者ガイド](../general/developers-guide.md)
