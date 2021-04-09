---
title: Azure API Management セルフホステッド ゲートウェイのカスタム ドメイン名を構成する | Microsoft Docs
description: このトピックでは、Azure API Management セルフホステッド ゲートウェイのカスタム ドメイン名を構成する手順について説明します。
services: api-management
documentationcenter: ''
author: vladvino
ms.service: api-management
ms.topic: article
ms.date: 03/31/2020
ms.author: apimpm
ms.openlocfilehash: d52bf87b74ae9b1770ed5092738fd05eb9f54fde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99491032"
---
# <a name="configure-a-custom-domain-name-for-a-self-hosted-gateway"></a>セルフホステッド ゲートウェイのカスタム ドメイン名を構成する

[Azure API Management セルフホステッド ゲートウェイ](self-hosted-gateway-overview.md)をプロビジョニングする場合、ホスト名が割り当てられていないため、その IP アドレスで参照する必要があります。 この記事では、既存のカスタム DNS 名 (ホスト名とも呼ばれます) をセルフホステッド ゲートウェイにマップする方法について説明します。

## <a name="prerequisites"></a>前提条件

この記事で説明されている手順を実行するには、以下が必要です。

-   有効な Azure サブスクリプション

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   API Management インスタンス。 詳細については、[Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関する記事を参照してください。
- セルフホステッド ゲートウェイ。 詳細については、[セルフホステッド ゲートウェイのプロビジョニング方法](api-management-howto-provision-self-hosted-gateway.md)に関する記事を参照してください
-   自分または自分の所属する組織が所有しているカスタム ドメイン名。 このトピックでは、カスタム ドメイン名を取得する手順は説明しません。
-   カスタム ドメイン名をセルフホステッド ゲートウェイの IP アドレスにマップする DNS サーバーでホストされている DNS レコード。 このトピックでは、DNS レコードをホストする手順は説明しません。
-   パブリックおよびプライベート キー (.PFX) 付きの有効な証明書。 サブジェクトやサブジェクトの別名 (SAN) は、ドメイン名と一致している必要があります (これによって API Management インスタンスでは、TLS 経由で URL を安全に公開することができます)。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-custom-domain-certificate-to-your-api-management-service"></a>API Management サービスにカスタム ドメイン証明書を追加する

カスタム ドメイン証明書 (.PFX) ファイルを API Management インスタンスに追加するか、Azure Key Vault に保存されている証明書を参照します。 「[Azure API Management でクライアント証明書認証を使用してバックエンド サービスを保護する](api-management-howto-mutual-certificates.md)」の手順に従います。

> [!NOTE]
> セルフホステッド ゲートウェイ ドメインには、キー コンテナー証明書を使用することをお勧めします。

## <a name="use-the-azure-portal-to-set-a-custom-domain-name-for-your-self-hosted-gateway"></a>Azure portal を使用してセルフホステッド ゲートウェイのカスタム ドメイン名を設定する

1. **[Deployment and infrastructure]\(デプロイとインフラストラクチャ\)** の下から **[ゲートウェイ]** を選択します。
2. ドメイン名を構成するセルフホステッド ゲートウェイを選択します。
3. **[設定]** の下で **[ホスト名]** を選択します。
4. **[+ 追加]** を選択します。
5. ホスト名のリソース名を **[名前]** フィールドに入力します。
6. **[ホスト名]** フィールドにドメイン名を入力します。
7. **[証明書]** ドロップダウンから証明書を選択します。
8. このゲートウェイ経由で公開されているいずれかの API でクライアント証明書認証を使用する場合は、 **[クライアント証明書のネゴシエート]** チェックボックスをオンにします。
    > [!WARNING]
    > この設定は、ゲートウェイに対して構成されたすべてのドメイン名で共有されます。
9. **[追加]** を選択し、選択したセルフホステッド ゲートウェイにカスタム ドメイン名を割り当てます。

## <a name="next-steps"></a>次のステップ

[サービスのアップグレードとスケーリングを行う](upgrade-and-scale.md)
