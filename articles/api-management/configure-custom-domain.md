---
title: Azure API Management インスタンスのカスタム ドメイン名を構成する | Microsoft Docs
description: このトピックでは、Azure API Management インスタンスのカスタム ドメイン名を構成する方法について説明します。
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/12/2019
ms.author: apimpm
ms.openlocfilehash: ba2288fa4add6df6b7861b5f8a99bcd3ee8b4c12
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472190"
---
# <a name="configure-a-custom-domain-name"></a>カスタム ドメイン名の構成

Azure API Management サービス インスタンスを作成すると、Azure によって azure-api.net サブドメイン (例: `apim-service-name.azure-api.net`) に割り当てられます。 ただし、独自のカスタム ドメイン名 (**contoso.com** など) を使用する API Management エンドポイントを公開できます。 このチュートリアルでは、既存のカスタム DNS 名を、API Management インスタンスによって公開されるエンドポイントにマップする方法を説明します。

> [!WARNING]
> アプリケーションのセキュリティを許可するために証明書のピン留めを使用するお客様は、カスタム ドメイン名と、既定の証明書ではなく、お客様が管理する証明書を使用する必要があります。 代わりに、既定の証明書をピン留めするお客様は、制御しない証明書のプロパティでハードの依存関係を取得することになります。この方法はお勧めしません。

## <a name="prerequisites"></a>前提条件

この記事で説明されている手順を実行するには、以下が必要です。

-   有効な Azure サブスクリプション

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   API Management インスタンス。 詳細については、[Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関する記事を参照してください。
-   自分または自分の所属する組織が所有しているカスタム ドメイン名。 このトピックでは、カスタム ドメイン名を取得する手順は説明しません。
-   DNS サーバーでホストしている CNAME レコード。カスタム ドメイン名を API Management インスタンスの既定のドメイン名にマップするものです。 このトピックでは、CNAME レコードをホストする手順は説明しません。
-   パブリックおよびプライベート キー (.PFX) 付きの有効な証明書。 サブジェクトまたはサブジェクト代替名 (SAN) はドメイン名と一致している必要があります (これにより、API Management インスタンスは SSL 経由で URL を安全に公開できます)。

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Azure ポータルを使用してカスタム ドメイン名を設定する

1. [Azure portal](https://portal.azure.com/) で API Management インスタンスに移動します。
1. **[カスタム ドメイン]** をクリックします。

    カスタム ドメイン名を割り当てることができるエンドポイントは多数あります。 現時点では、次のエンドポイントを利用できます。

    - **ゲートウェイ** (既定値: `<apim-service-name>.azure-api.net`)
    - **ポータル** (既定値: `<apim-service-name>.portal.azure-api.net`)
    - **Management** (既定値: `<apim-service-name>.management.azure-api.net`)
    - **SCM** (既定値: `<apim-service-name>.scm.azure-api.net`)

    > [!NOTE]
    > 従量課金レベルの構成で利用できるのは、 **[ゲートウェイ]** エンドポイントだけです。
    > すべてのエンドポイントまたは一部を更新できます。 通常、お客様は、**ゲートウェイ** (この URL を使用して API Management を通じて公開される API が呼び出されます) と**ポータル** (開発者ポータルの URL) を更新します。
    > **Management** エンドポイントと **SCM** エンドポイントは、API Management インスタンス所有者だけが内部的に使用するため、カスタム ドメイン名が割り当てられることはほとんどありません。
    > **Premium** レベルでは、**ゲートウェイ** エンドポイントに対する複数のホスト名の設定がサポートされます。

1. 更新するエンドポイントを選択します。
1. 右側のウィンドウで **[カスタム]** をクリックします。

    - **[カスタム ドメイン名]** に、使用する名前を指定します。 たとえば、「 `api.contoso.com` 」のように入力します。
    - **[証明書]** で、Key Vault から証明書を選択します。 証明書がパスワードで保護されている場合は、有効な .PFX ファイルをアップロードし、その**パスワード**を指定することもできます。

    > [!NOTE]
    > ワイルドカードを使用したドメイン名 (`*.contoso.com` など) は、従量課金レベル以外のあらゆるレベルでサポートされています。

    > [!TIP]
    > 証明書を管理し、それらをオートローテーションに設定する場合は、Azure Key Vault を使用することをお勧めします。
    > Azure Key Vault を使用してカスタム ドメインの SSL 証明書を管理する場合は、証明書が _シークレット_ ではなく、[_証明書_として](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate) Key Vault に挿入されていることを確認してください。
    >
    > SSL 証明書を取得するには、API Management に、証明書が格納されている Azure Key Vault に対するシークレットの取得アクセス許可のリストが必要です。 Azure portal を使用している場合、必要なすべての構成手順が自動的に完了します。 コマンド ライン ツールまたは管理 API を使用している場合は、これらのアクセス許可を手動で付与する必要があります。 この処理は 2 段階の手順で実行されます。 まず、API Management インスタンスのマネージド ID ページを使用して、マネージド ID が有効になっていることを確認し、そのページに表示されているプリンシパル ID を書き留めます。 次に、このプリンシパル ID にアクセス許可リストを提供し、証明書が格納されている Azure Key Vault に対するシークレットの取得アクセス許可を付与します。
    >
    > 証明書がオートローテーションに設定されている場合、API Management はサービスのダウンタイムなしに最新バージョンを自動的に取得します (お使いの API Management のレベルに SLA が含まれている場合 (つまり、Developer レベルを除くすべてのレベル))。

1. [適用] をクリックします。

    > [!NOTE]
    > 証明書を割り当てる処理は、デプロイのサイズによっては、15 分以上かかる場合があります。 Developer SKU にはダウンタイムがありますが、Basic 以上の SKU にはダウンタイムがありません。

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="dns-configuration"></a>DNS の構成

カスタム ドメイン名の DNS を構成するときは、次の 2 つのオプションがあります。

-   構成したカスタム ドメイン名のエンドポイントを指す CNAME レコードを構成する。
-   API Management ゲートウェイの IP アドレスを指す A レコードを構成する。

> [!NOTE]
> API Management インスタンスの IP アドレスは静的ですが、いくつかのシナリオで変更される可能性があります。 このため、カスタム ドメインを構成するときは CNAME を使用することをお勧めします。 DNS 構成方法を選択するときは、このことを考慮してください。 詳細については、[API Management の FAQ](api-management-faq.md#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services) に関するページを参照してください。

## <a name="next-steps"></a>次の手順

[サービスのアップグレードとスケーリングを行う](upgrade-and-scale.md)
