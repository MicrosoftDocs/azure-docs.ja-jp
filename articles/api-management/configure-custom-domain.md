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
ms.date: 12/14/2017
ms.author: apimpm
ms.openlocfilehash: a771b437258046f937b97a9e37ffedbe0a17c1c1
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58079795"
---
# <a name="configure-a-custom-domain-name"></a>カスタム ドメイン名の構成 

API Management (APIM) インスタンスを作成すると、Azure によって azure-api.net サブドメイン (例: `apim-service-name.azure-api.net`) に割り当てられます。 ただし、独自のドメイン名 (**contoso.com** など) を使用する APIM エンドポイントを公開できます。 このチュートリアルでは、既存のカスタム DNS 名を Azure API Management インスタンスによって公開されるエンドポイントにマップする方法を示します。

> [!WARNING]
> アプリケーションのセキュリティを許可するために証明書のピン留めを使用するお客様は、カスタム ドメイン名と、既定の証明書ではなく、お客様が管理する証明書を使用する必要があります。 代わりに、既定の証明書をピン留めするお客様は、制御しない証明書のプロパティでハードの依存関係を取得することになります。この方法はお勧めしません。

## <a name="prerequisites"></a>前提条件

この記事で説明されている手順を実行するには、以下が必要です。

+ 有効な Azure サブスクリプション

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ APIM インスタンス。 詳細については、[Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関する記事を参照してください。
+ 自己所有しているカスタム ドメイン名。 使用するカスタム ドメイン名は別途入手して DNS サーバーでホストしている必要があります。 このトピックでは、カスタム ドメイン名をホストする方法の手順は説明しません。
+ パブリックおよびプライベート キー (.PFX) 付きの有効な証明書。 サブジェクトまたはサブジェクト代替名 (SAN) はドメイン名と一致している必要があります (これにより、APIM は SSL 経由で URL を安全に公開できます)。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Azure ポータルを使用してカスタム ドメイン名を設定する

1. [Azure ポータル](https://portal.azure.com/)で、APIM インスタンスに移動します。
1. **[カスタム ドメインと SSL]** をクリックします。
    
    カスタム ドメイン名を割り当てることができるエンドポイントは多数あります。 現時点では、次のエンドポイントを利用できます。 
   + **プロキシ** (既定値: `<apim-service-name>.azure-api.net`) 
   + **ポータル** (既定値: `<apim-service-name>.portal.azure-api.net`)     
   + **Management** (既定値: `<apim-service-name>.management.azure-api.net`) 
   + **SCM** (既定値: `<apim-service-name>.scm.azure-api.net`)

     >[!NOTE]
     > すべてのエンドポイントまたは一部を更新できます。 一般的には、**プロキシ**(この URL は API Management を通じて公開される API を呼び出すために使用されます) と**ポータル**(開発者ポータルの URL) を更新します。 **Management** エンドポイントと **SCM** エンドポイントは、APIM ユーザーによって内部的に使用されるため、カスタム ドメイン名が割り当てられることはほぼありません。

1. 更新するエンドポイントを選択します。 
1. 右側のウィンドウで **[カスタム]** をクリックします。

   + **[カスタム ドメイン名]** に、使用する名前を指定します。 たとえば、「 `api.contoso.com` 」のように入力します。 ワイルドカード ドメイン名 (たとえば、*. domain.com) もサポートされています。
   + **[証明書]** で、Key Vault から証明書を選択します。 証明書がパスワードで保護されている場合は、有効な .PFX ファイルをアップロードし、その**パスワード**を指定することもできます。

     > [!TIP]
     > Azure Key Vault を使用してカスタム ドメインの SSL 証明書を管理する場合は、証明書が*シークレット*ではなく、[*証明書*として](https://docs.microsoft.com/rest/api/keyvault/CreateCertificate/CreateCertificate) Key Vault に挿入されていることを確認してください。 証明書がオートローテーションに設定されている場合は、API Management によって最新のバージョンが自動的に選択されます。

1. [適用] をクリックします。

    >[!NOTE]
    >証明書を割り当てる処理は、デプロイのサイズによっては、15 分以上かかる場合があります。 Developer SKU にはダウンタイムがありますが、Basic SKU と上位の SKU にはダウンタイムはありません。

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="next-steps"></a>次の手順

[サービスのアップグレードとスケーリングを行う](upgrade-and-scale.md)
