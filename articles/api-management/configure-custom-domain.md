---
title: Azure API Management インスタンスのカスタム ドメイン名を構成する
titleSuffix: Azure API Management
description: このトピックでは、Azure API Management インスタンスのカスタム ドメイン名を構成する方法について説明します。
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: a7032c64efa486c65830e013373239647a368540
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92311133"
---
# <a name="configure-a-custom-domain-name-for-your-azure-api-management-instance"></a>Azure API Management インスタンスのカスタム ドメイン名を構成する

Azure API Management サービス インスタンスを作成すると、Azure によって `azure-api.net` サブドメイン (例: `apim-service-name.azure-api.net`) に割り当てられます。 ただし、独自のカスタム ドメイン名 (**contoso.com** など) を使用する API Management エンドポイントを公開できます。 このチュートリアルでは、既存のカスタム DNS 名を、API Management インスタンスによって公開されるエンドポイントにマップする方法を説明します。

> [!IMPORTANT]
> API Management は、既定のドメイン名または構成されているいずれかのカスタム ドメイン名と一致する[ホスト ヘッダー](https://tools.ietf.org/html/rfc2616#section-14.23)値を持つ要求のみを受け入れます。

> [!WARNING]
> アプリケーションのセキュリティを向上させるために証明書のピン留めを使用するお客様は、カスタム ドメイン名と、(既定の証明書ではない) お客様が管理する証明書を使用する必要があります。 代わりに、既定の証明書をピン留めするお客様は、制御しない証明書のプロパティでハードの依存関係を取得することになります。この方法はお勧めしません。

## <a name="prerequisites"></a>前提条件

この記事で説明されている手順を実行するには、以下が必要です。

-   有効な Azure サブスクリプション

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   API Management インスタンス。 詳細については、[Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関する記事を参照してください。
-   自分または自分の所属する組織が所有しているカスタム ドメイン名。 このトピックでは、カスタム ドメイン名を取得する手順は説明しません。
-   DNS サーバーでホストしている CNAME レコード。カスタム ドメイン名を API Management インスタンスの既定のドメイン名にマップするものです。 このトピックでは、CNAME レコードをホストする手順は説明しません。
-   パブリックおよびプライベート キー (.PFX) 付きの有効な証明書。 サブジェクトやサブジェクトの別名 (SAN) は、ドメイン名と一致している必要があります (これによって API Management インスタンスでは、TLS を通して URL を安全に公開することができます)。

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Azure ポータルを使用してカスタム ドメイン名を設定する

1. [Azure portal](https://portal.azure.com/) で API Management インスタンスに移動します。
1. **[カスタム ドメイン]** をクリックします。

    カスタム ドメイン名を割り当てることができるエンドポイントは多数あります。 現時点では、次のエンドポイントを利用できます。

    - **ゲートウェイ** (既定値: `<apim-service-name>.azure-api.net`)
    - **開発者ポータル (レガシ)** (既定値: `<apim-service-name>.portal.azure-api.net`)
    - **開発者ポータル** (既定値: `<apim-service-name>.developer.azure-api.net`)
    - **Management** (既定値: `<apim-service-name>.management.azure-api.net`)
    - **SCM** (既定値: `<apim-service-name>.scm.azure-api.net`)

    > [!NOTE]
    > 従量課金レベルの構成で利用できるのは、 **[ゲートウェイ]** エンドポイントだけです。
    > すべてのエンドポイントまたは一部を更新できます。 通常、お客様は、**ゲートウェイ** (この URL を使用して API Management を通じて公開される API が呼び出されます) と **ポータル** (開発者ポータルの URL) を更新します。
    > **Management** エンドポイントと **SCM** エンドポイントは、API Management インスタンス所有者だけが内部的に使用するため、カスタム ドメイン名が割り当てられることはほとんどありません。
    > **Premium** レベルでは、**ゲートウェイ** エンドポイントに対する複数のホスト名の設定がサポートされます。

1. 更新するエンドポイントを選択します。
1. 右側のウィンドウで **[カスタム]** をクリックします。

    - **[カスタム ドメイン名]** に、使用する名前を指定します。 たとえば、「 `api.contoso.com` 」のように入力します。
    - **[証明書]** で、Key Vault から証明書を選択します。 証明書がパスワードで保護されている場合は、有効な .PFX ファイルをアップロードし、その **パスワード** を指定することもできます。

    > [!NOTE]
    > ワイルドカードを使用したドメイン名 (`*.contoso.com` など) は、従量課金レベル以外のあらゆるレベルでサポートされています。

    > [!TIP]
    > [Azure Key Vault を使用して証明書を管理](../key-vault/certificates/about-certificates.md)し、それらを自動更新するように設定することをおすすめします。
    > Azure Key Vault を使用してカスタム ドメインの TLS/SSL 証明書を管理する場合は、証明書が、_シークレット_ ではなく [_証明書_ として](/rest/api/keyvault/createcertificate/createcertificate) Key Vault に挿入されていることを確認してください。
    >
    > TLS/SSL 証明書を取得するには、証明書が格納されている Azure Key Vault に対するシークレットの一覧表示および取得のアクセス許可が、API Management に付与されている必要があります。 Azure portal を使用している場合、必要なすべての構成手順が自動的に完了します。 コマンド ライン ツールまたは管理 API を使用している場合は、これらのアクセス許可を手動で付与する必要があります。 この処理は 2 段階の手順で実行されます。 まず、API Management インスタンスのマネージド ID ページを使用して、マネージド ID が有効になっていることを確認し、そのページに表示されているプリンシパル ID を書き留めます。 次に、このプリンシパル ID にアクセス許可リストを提供し、証明書が格納されている Azure Key Vault に対するシークレットの取得アクセス許可を付与します。
    >
    > 証明書が自動更新されるように設定されている場合、API Management はサービスのダウンタイムなしに最新バージョンを自動的に取得します (お使いの API Management のレベルに SLA が含まれている場合 (つまり、Developer レベルを除くすべてのレベル))。

1. [適用] をクリックします。

    > [!NOTE]
    > 証明書を割り当てる処理は、デプロイのサイズによっては、15 分以上かかる場合があります。 Developer SKU にはダウンタイムがありますが、Basic 以上の SKU にはダウンタイムがありません。

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="dns-configuration"></a>DNS の構成

カスタム ドメイン名の DNS を構成するときは、次の 2 つのオプションがあります。

-   構成したカスタム ドメイン名のエンドポイントを指す CNAME レコードを構成する。
-   API Management ゲートウェイの IP アドレスを指す A レコードを構成する。

> [!NOTE]
> API Management インスタンスの IP アドレスは静的ですが、いくつかのシナリオで変更される可能性があります。 このため、カスタム ドメインを構成するときは CNAME を使用することをお勧めします。 DNS 構成方法を選択するときは、このことを考慮してください。 詳細については、[IP に関するドキュメント記事](api-management-howto-ip-addresses.md#changes-to-the-ip-addresses)と [API Management の FAQ](api-management-faq.md#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services) を参照してください。

## <a name="next-steps"></a>次のステップ

[サービスのアップグレードとスケーリングを行う](upgrade-and-scale.md)
