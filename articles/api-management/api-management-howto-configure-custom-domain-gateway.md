---
title: Azure API Management セルフホステッド ゲートウェイのカスタム ドメイン名を構成する | Microsoft Docs
description: このトピックでは、Azure API Management セルフホステッド ゲートウェイのカスタム ドメイン名を構成する手順について説明します。
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 03/31/2020
ms.author: apimpm
ms.openlocfilehash: 0894203be4867e305c8e15467a2a867b9bfdc727
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86506810"
---
# <a name="configure-a-custom-domain-name"></a>カスタム ドメイン名の構成

[Azure API Management セルフホステッド ゲートウェイ](self-hosted-gateway-overview.md)をプロビジョニングする場合、ホスト名が割り当てられていないため、その IP アドレスで参照する必要があります。 この記事では、既存のカスタム DNS 名 (ホスト名とも呼ばれます) をセルフホステッド ゲートウェイにマップする方法について説明します。

## <a name="prerequisites"></a>前提条件

この記事で説明されている手順を実行するには、以下が必要です。

-   有効な Azure サブスクリプション

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

-   API Management インスタンス。 詳細については、[Azure API Management インスタンスの作成](get-started-create-service-instance.md)に関する記事を参照してください。
- セルフホステッド ゲートウェイ。 詳細については、[セルフホステッド ゲートウェイのプロビジョニング方法](api-management-howto-provision-self-hosted-gateway.md)に関する記事を参照してください
-   自分または自分の所属する組織が所有しているカスタム ドメイン名。 このトピックでは、カスタム ドメイン名を取得する手順は説明しません。
-   カスタム ドメイン名をセルフホステッド ゲートウェイの IP アドレスにマップする DNS サーバーでホストされている DNS レコード。 このトピックでは、DNS レコードをホストする手順は説明しません。
-   パブリックおよびプライベート キー (.PFX) 付きの有効な証明書。 サブジェクトまたはサブジェクト代替名 (SAN) はドメイン名と一致している必要があります (これにより、API Management インスタンスは TLS 経由で URL を安全に公開できます)。

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-custom-domain-certificate-to-your-api-management-service"></a>API Management サービスにカスタム ドメイン証明書を追加する

1. **[セキュリティ]** の下から **[証明書]** を選択します。
2. **[+ 追加]** を選択します。
3. 証明書のリソース名を **[ID]** フィールドに入力します。
4. **[証明書]** フィールドまたはその隣にあるフォルダー アイコンを選択することで、証明書 (.PFX) を含むファイルを選択します。
5. 証明書のパスワードを **[パスワード]** フィールドに入力します。
6. **[作成]** を選択して、API Management サービスに証明書を追加します。

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
