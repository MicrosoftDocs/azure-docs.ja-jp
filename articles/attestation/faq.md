---
title: よく寄せられる質問
description: Microsoft Azure Attestation に関してよく寄せられる質問への回答
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 39f628845bdc9d54b48b1c8037f4a506a9d5c00a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89236549"
---
# <a name="frequently-asked-questions-for-microsoft-azure-attestation"></a>Microsoft Azure Attestation に関してよく寄せられる質問

この記事では、[Azure Attestation](overview.md) に関してよく寄せられる質問への回答を提供します。

この記事で Azure の問題に対処できない場合は、[Azure サポート ページ](https://azure.microsoft.com/support/options/)で Azure サポート リクエストを送信することもできます。

## <a name="what-is-azure-pck-caching-service-and-its-role-in-enclave-attestation"></a>エンクレーブ構成証明における Azure PCK キャッシュ サービスとその役割は何ですか

Azure PCK キャッシュ サービスでは、Intel の [Azure Confidential Computing (ACC)](../confidential-computing/overview.md) ノードの Azure セキュリティ ベースラインを定義し、データをキャッシュします。 キャッシュされた情報は、信頼できる実行環境 (TEE) の検証時に Azure Attestation によってさらに使用されます。  

Azure PCK キャッシュ サービスでは、次が可能となります。
   - 高可用性を提供します 
   - 外部でホストされるサービスとインターネット接続への依存を軽減します。
   - 最新バージョンの Intel 証明書、CRL、信頼済みのコンピューティング ベース (TCB) 情報、および Intel からの ACC ノードの Quoting Enclave ID を取得します。 したがってこのサービスでは、TEE の検証中に Azure のセキュリティ ベースラインが Azure Attestation によって参照可能であることを確認し、無効または失効した Intel 証明書による構成証明の失敗を大幅に削減します。  

## <a name="is-sgx-attestation-supported-by-azure-attestation-in-non-azure-environments"></a>SGX 構成証明は、Azure 以外の環境の Azure Attestation でサポートされていますか

Azure Attestation は、TEE を検証するために、Azure PCK キャッシュ サービスによって示されるセキュリティ ベースラインに依存します。 Azure PCK キャッシュ サービスは現在、Azure Confidential Computing ノードのみをサポートするように設計されています。 

## <a name="what-validations-does-azure-attestation-perform-for-attesting-sgx-enclaves"></a>SGX エンクレーブを証明するために Azure Attestation によってどのような検証が実行されますか

Azure Attestation は、さまざまな種類の TEE をリモートで証明する統合フレームワークです。 Azure Attestation では次のことが可能です。

   - 署名されたエンクレーブ クォートの信頼されたルートが Intel に属しているかどうかを検証します。
   - エンクレーブ クォートが、Azure PCK キャッシュ サービスで定義されている Azure セキュリティ ベースラインを満たしているかどうかを検証します。
   - 構成証明要求オブジェクトの Enclave Held Data (EHD) の SHA256 ハッシュが、エンクレーブ クォートの reportData フィールドの最初の 32 バイトと一致するかどうかを検証します。
   - ユーザーが構成証明プロバイダーを作成し、カスタム ポリシーを構成できるようにします。 上記の検証に加えて、Azure Attestation はポリシーに対してエンクレーブ クォートを評価します。 ポリシーは、エンクレーブの承認規則を定義すると共に、構成証明トークンを生成するための発行規則を決定します。 エンクレーブで意図したソフトウェアが実行されているかどうかを確認するために、ユーザーは承認規則を追加して、エンクレーブ クォートの **mrsigner** および **mrenclave** フィールドが、ユーザーのバイナリの値と一致するかどうかを検証できます。

## <a name="how-can-a-verifier-obtain-the-collateral-for-sgx-attestation-supported-by-azure-attestation"></a>証明者は、Azure Attestation でサポートされている SGX 構成証明の関連資料をどのように取得できますか

一般に、Intel を信頼のルートとする構成証明モデルでは、エンクレーブの証拠を取得するため、構成証明クライアントと Enclave API 間で通信が行われます。 Enclave API は、内部的に Intel PCK キャッシュ サービスを呼び出して、証明するノードの Intel の証明書を取得します。 証明書は、エンクレーブの証拠に署名するために使用されます。これにより、リモートで構成証明可能な関連資料が生成されます。  

Azure Attestation に対しても同じプロセスを実装できます。 ただし、Azure PCK キャッシュ サービスによって提供される利点を活用するために、ACC 仮想マシンをインストールした後に [Azure DCAP ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.DCAP)をインストールすることをお勧めします。 Azure DCAP ライブラリをインストールすると、Intel との契約に基づいて、エンクレーブの証拠生成の要求が Intel PCK キャッシュ サービスから Azure PCK キャッシュ サービスにリダイレクトされます。 Azure DCAP ライブラリは、Windows および Linux ベースの環境でサポートされています。

## <a name="how-to-shift-to-azure-attestation-from-other-attestation-models"></a>他の構成証明モデルから Azure Attestation に移行する方法

- Azure Confidential Computing 仮想マシンをインストールした後、Azure PCK キャッシュ サービスで提供される利点を活用するために、Azure DCAP ライブラリ ([Windows/](https://www.nuget.org/packages/Microsoft.Azure.DCAP/) [Linux](https://packages.microsoft.com/ubuntu/18.04/prod/pool/main/a/az-dcap-client/)) をインストールします。
- エンクレーブの証拠を取得し、Azure Attestation に要求を送信できるリモート構成証明クライアントを作成する必要があります。 リファレンスについては、[コード例](/samples/browse/?expanded=azure&terms=attestation)を参照してください 
- 構成証明要求は、既定のプロバイダーまたはカスタム構成証明プロバイダーの REST API エンドポイントに送信できます。 
- Azure Attestation API は Azure AD 認証によって保護されています。 そのため、Attest API を呼び出すクライアントは、構成証明要求時に有効な Azure AD アクセス トークンを取得して渡すことができる必要があります。 

## <a name="how-can-the-relying-party-verify-the-integrity-of-attestation-token"></a>証明書利用者はどのように構成証明トークンの整合性を検証できますか

Azure Attestation によって生成された構成証明トークンは、自己署名証明書を使用して署名されます。 証明書は、[OpenID メタデータ エンドポイント](/rest/api/attestation/metadataconfiguration/get)経由で公開されます。 証明書利用者は、このエンドポイントから署名証明書を取得し、構成証明トークンの署名の検証を実行できます。 構成証明トークンの有効期間は 8 時間です。 

## <a name="how-to-identify-the-certificate-to-be-used-for-signature-verification-from-the-openid-metadata-endpoint"></a>OpenID メタデータ エンドポイントから、署名の検証に使用する証明書を識別する方法

OpenID メタデータ エンドポイントで公開されている複数の証明書は、Azure Attestation でサポートされるさまざまなユース ケース (たとえば、SGX 構成証明) に対応します。 [RFC 7515](https://tools.ietf.org/html/rfc7515) によって規定される標準に従って、構成証明トークン ヘッダーの *kid* パラメーターに一致するキー ID (kid) を持つ証明書が署名の検証に使用されます。 一致する **kid** が見つからない場合は、OpenID メタデータ エンドポイントによって公開されているすべての証明書を試すことが想定されます。

## <a name="is-it-possible-for-the-relying-party-to-share-secrets-with-the-validated-trusted-execution-environments-tees"></a>証明書利用者が、検証済みの信頼できる実行環境 (TEE) とシークレットを共有することはできますか

エンクレーブ内で生成される公開キーは、クライアントから Azure Attestation に送信される構成証明要求オブジェクトの Enclave Held Data (EHD) プロパティで表すことができます。 EHD の SHA256 ハッシュが、クォートの reportData フィールドに示されているかどうかを確認した後、Azure Attestation によって EHD が構成証明トークンに含められます。 証明書利用者は、検証済みの構成証明の応答からの EHD を使用してシークレットを暗号化し、エンクレーブと共有できます。 詳細については、「[Azure Attestation の基本的な概念](basic-concepts.md)」を参照してください。
