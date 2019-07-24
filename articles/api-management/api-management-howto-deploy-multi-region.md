---
title: 複数の Azure リージョンへの Azure API Management サービスのデプロイ | Microsoft Docs
description: 複数の Azure リージョンに Azure API Management サービス インスタンスをデプロイする方法について説明します。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: apimpm
ms.openlocfilehash: d22da92355616c208c7616b4b0e8c26b7f9e7006
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59793625"
---
# <a name="how-to-deploy-an-azure-api-management-service-instance-to-multiple-azure-regions"></a>複数の Azure リージョンに Azure API Management サービス インスタンスをデプロイする方法

Azure API Management では複数リージョンのデプロイメントがサポートされており、API パブリッシャーは 1 つの Azure API Management サービスを任意の数の Azure リージョンに分散できます。 これにより、地理的に分散した API コンシューマーによって認識される要求待ち時間が短くなり、1 つのリージョンがオフラインになった場合でもサービスの可用性を向上できます。

新しい Azure API Management サービスには、最初は単一の Azure リージョン (プライマリ リージョン) 内に 1 つの[ユニット][unit]のみが含まれています。 リージョンは Azure Portal で簡単に追加できます。 各リージョンには API Management のゲートウェイ サーバーがデプロイされており、呼び出しのトラフィックは、待ち時間の観点から最も近いゲートウェイにルーティングされます。 リージョンがオフラインになった場合、トラフィックは自動的に次に最も近いゲートウェイにリダイレクトされます。

> [!NOTE]
> Azure API Management は、リージョン間で API ゲートウェイ コンポーネントのみをレプリケートします。 サービス管理コンポーネントは、プライマリ リージョンでのみホストされます。 プライマリ リージョンでシステム停止が発生した場合、設定またはポリシーの更新プログラムを含む構成の変更を Azure API Management サービス インスタンスに適用することはできません。

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="add-region"> </a>新しいリージョンに API Management サービス インスタンスをデプロイする

> [!NOTE]
> API Management サービス インスタンスをまだ作成していない場合は、[API Management サービス インスタンスの作成][Create an API Management service instance]に関するページを参照してください。

Azure Portal で API Management サービス インスタンスの **[スケールと料金]** ページに移動します。 

![[スケール] タブ][api-management-scale-service]

新しいリージョンにデプロイするには、ツールバーの **[+ 地域の追加]** をクリックします。

![リージョンの追加][api-management-add-region]

ドロップダウン リストから場所を選択し、スライダーでユニット数を設定します。

![ユニットの指定][api-management-select-location-units]

**[追加]** をクリックすると、選択した内容が場所のテーブルに記載されます。 

すべての場所が構成されるまでこのプロセスを繰り返したら、ツールバーの **[保存]** をクリックして、デプロイ プロセスを開始します。

## <a name="remove-region"> </a>場所から API Management サービス インスタンスを削除する

Azure Portal で API Management サービス インスタンスの **[スケールと料金]** ページに移動します。 

![[スケール] タブ][api-management-scale-service]

テーブルの右端にある **[...]** ボタンを使用して、削除する場所のコンテキスト メニューを開きます。 **[削除]** を選択します。

削除されたことを確認したら、**[保存]** をクリックして変更を適用します。

## <a name="route-backend"> </a>リージョンのバックエンド サービスに API 呼び出しをルーティングする

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

## <a name="custom-routing"> </a>API Management リージョン ゲートウェイへのカスタム ルーティングを使用する

API Management は、[最短の待ち時間](../traffic-manager/traffic-manager-routing-methods.md#performance)に基づいてリージョン "*ゲートウェイ*" に要求をルーティングします。 API Management でこの設定をオーバーライドすることはできませんが、カスタム ルーティング規則を持った独自の Traffic Manager を使用することはできます。

1. 独自の [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) を作成します。
1. カスタム ドメインを使用している場合、API Management サービスではなく、[Traffic Manager と共に使用](../traffic-manager/traffic-manager-point-internet-domain.md)します。
1. [Traffic Manager に API Management のリージョン エンドポイントを構成](../traffic-manager/traffic-manager-manage-endpoints.md)します。 リージョン エンドポイントは、`https://<service-name>-<region>-01.regional.azure-api.net` という URL パターンに従います (例: `https://contoso-westus2-01.regional.azure-api.net`)。
1. [Traffic Manager に API Management のリージョン状態エンドポイントを構成](../traffic-manager/traffic-manager-monitoring.md)します。 リージョン状態エンドポイントは、`https://<service-name>-<region>-01.regional.azure-api.net/status-0123456789abcdef` という URL パターンに従います (例: `https://contoso-westus2-01.regional.azure-api.net/status-0123456789abcdef`)。
1. Traffic Manager の[ルーティング方法](../traffic-manager/traffic-manager-routing-methods.md)を指定します。


[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-location-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-location-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Create an API Management service instance]: get-started-create-service-instance.md
[Get started with Azure API Management]: get-started-create-service-instance.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unit]: https://azure.microsoft.com/pricing/details/api-management/
[Premium]: https://azure.microsoft.com/pricing/details/api-management/
