---
title: 複数の Azure リージョンへの Azure API Management サービスのデプロイ
titleSuffix: Azure API Management
description: 複数の Azure リージョンに Azure API Management サービス インスタンスをデプロイする方法について説明します。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/20/2020
ms.author: apimpm
ms.openlocfilehash: 427ebfe865002612be2f9aeb9db416f5c2f41e52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88065456"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>複数の Azure リージョンに Azure API Management サービス インスタンスをデプロイする方法

Azure API Management では複数リージョンのデプロイがサポートされています。これにより、API パブリッシャーは 1 つの Azure API Management サービスを任意の数のサポートされる Azure リージョンに分散できます。 複数リージョン機能により、地理的に分散した API コンシューマーによって認識される要求待ち時間が短くなり、1 つのリージョンがオフラインになった場合でもサービスの可用性を向上できます。

新しい Azure API Management サービスには、最初は単一の Azure リージョン (プライマリ リージョン) 内に 1 つの[ユニット][unit]のみが含まれています。 追加のユニットは、プライマリ リージョンまたはセカンダリ リージョンに追加できます。 API Management ゲートウェイ コンポーネントは、選択されたすべてのプライマリ リージョンとセカンダリ リージョンにデプロイされます。 受信 API 要求は、最も近いリージョンに自動的に送られます。 リージョンがオフラインになった場合、API 要求は、障害が発生したリージョンを迂回して、次に最も近いゲートウェイに自動的にルーティングされます。

> [!NOTE]
> すべてのリージョンにデプロイされるのは、API Management のゲートウェイ コンポーネントのみです。 サービス管理コンポーネントと開発者ポータルは、プライマリ リージョンでのみホストされます。 このため、プライマリ リージョンの停止時には、プライマリ リージョンがオンラインに戻るまで、開発者ポータルへのアクセス、および構成を変更する機能 (API の追加、ポリシーの適用など) が損なわれます。 プライマリ リージョンがオフラインの間、使用可能なセカンダリ リージョンは、使用可能な最新の構成を使用して引き続き API トラフィックを処理します。

>[!IMPORTANT]
> 顧客データを 1 つのリージョンに格納できるようにする機能は、現在のところ、アジア太平洋地域の東南アジア リージョン (シンガポール) でのみ使用できます。 その他のすべてのリージョンでは、顧客データは地域内に格納されます。

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="deploy-api-management-service-to-a-new-region"></a><a name="add-region"> </a>新しいリージョンに API Management サービスをデプロイする

> [!NOTE]
> API Management サービス インスタンスをまだ作成していない場合は、[API Management サービス インスタンスの作成][create an api management service instance]に関するページを参照してください。

1. Azure portal で、API Management サービスに移動し、メニューの **[場所]** エントリをクリックします。
2. 上部バーの **[+ 追加]** をクリックします。
3. ドロップダウン リストから場所を選択し、スライダーでユニット数を設定します。
4. **[追加]** ボタンをクリックして確認します。
5. すべての場所を構成するまで、この手順を繰り返します。
6. 上部バーにある **[保存]** をクリックして、デプロイ プロセスを開始します。

## <a name="delete-an-api-management-service-location"></a><a name="remove-region"> </a>API Management サービスの場所を削除する

1. Azure portal で、API Management サービスに移動し、メニューの **[場所]** エントリをクリックします。
2. テーブルの右端にある **[...]** ボタンを使用して、削除する場所のコンテキスト メニューを開きます。 **[削除]** を選択します。
3. 削除されたことを確認したら、 **[保存]** をクリックして変更を適用します。

## <a name="route-api-calls-to-regional-backend-services"></a><a name="route-backend"> </a>リージョンのバックエンド サービスに API 呼び出しをルーティングする

Azure API Management は、バックエンド サービスの URL が 1 つだけであることを特徴としています。 複数のリージョンに Azure API Management インスタンスがある場合でも、API ゲートウェイは、1 つのリージョンのみにデプロイされる同じバックエンド サービスに要求を転送します。 この場合、要求に固有のリージョンで Azure API Management 内にキャッシュされた応答でのみパフォーマンスが向上し、グローバルなバックエンドへの接続では引き続き長い待ち時間が発生します。

システムの地理的な分散を十分に活用するには、Azure API Management インスタンスと同じリージョンにバックエンド サービスをデプロイする必要があります。 その後、ポリシーと `@(context.Deployment.Region)` プロパティを使用して、バックエンドのローカル インスタンスにトラフィックをルーティングできます。

1. Azure API Management インスタンスに移動し、左側のメニューから **[API]** をクリックします。
2. 目的の API を選択します。
3. **[受信処理]** で、矢印のドロップダウンから **[コード エディター]** をクリックします。

    ![API コード エディター](./media/api-management-howto-deploy-multi-region/api-management-api-code-editor.png)

4. `set-backend` と `choose` 条件ポリシーを組み合わせて使用して、ファイルの `<inbound> </inbound>` セクション内に適切なルーティング ポリシーを作成します。

    たとえば、次の XML ファイルは、米国西部リージョンと東アジア リージョンで機能します。

    ```xml
    <policies>
        <inbound>
            <base />
            <choose>
                <when condition="@("West US".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-us.com/" />
                </when>
                <when condition="@("East Asia".Equals(context.Deployment.Region, StringComparison.OrdinalIgnoreCase))">
                    <set-backend-service base-url="http://contoso-asia.com/" />
                </when>
                <otherwise>
                    <set-backend-service base-url="http://contoso-other.com/" />
                </otherwise>
            </choose>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>
    ```

> [!TIP]
> バックエンド サービスの正面に [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) を置き、API 呼び出しを Traffic Manager に誘導して、そこでルーティングを自動的に解決させることもできます。

## <a name="use-custom-routing-to-api-management-regional-gateways"></a><a name="custom-routing"> </a>API Management リージョン ゲートウェイへのカスタム ルーティングを使用する

API Management は、[最短の待ち時間](../traffic-manager/traffic-manager-routing-methods.md#performance)に基づいてリージョン "_ゲートウェイ_" に要求をルーティングします。 API Management でこの設定をオーバーライドすることはできませんが、カスタム ルーティング規則を持った独自の Traffic Manager を使用することはできます。

1. 独自の [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) を作成します。
1. カスタム ドメインを使用している場合、API Management サービスではなく、[Traffic Manager と共に使用](../traffic-manager/traffic-manager-point-internet-domain.md)します。
1. [Traffic Manager に API Management のリージョン エンドポイントを構成](../traffic-manager/traffic-manager-manage-endpoints.md)します。 リージョン エンドポイントは、`https://<service-name>-<region>-01.regional.azure-api.net` という URL パターンに従います (例: `https://contoso-westus2-01.regional.azure-api.net`)。
1. [Traffic Manager に API Management のリージョン状態エンドポイントを構成](../traffic-manager/traffic-manager-monitoring.md)します。 リージョン状態エンドポイントは、`https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef` という URL パターンに従います (例: `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef`)。
1. Traffic Manager の[ルーティング方法](../traffic-manager/traffic-manager-routing-methods.md)を指定します。

[create an api management service instance]: get-started-create-service-instance.md
[get started with azure api management]: get-started-create-service-instance.md
[deploy an api management service instance to a new region]: #add-region
[delete an api management service instance from a region]: #remove-region
[unit]: https://azure.microsoft.com/pricing/details/api-management/
[premium]: https://azure.microsoft.com/pricing/details/api-management/
