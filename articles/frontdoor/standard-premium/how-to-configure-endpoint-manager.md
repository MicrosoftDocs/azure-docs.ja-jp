---
title: エンドポイント マネージャーを使用して Azure Front Door Standard/Premium エンドポイントを構成する
description: この記事では、エンドポイント マネージャーを使用してエンドポイントを構成する方法について説明します。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 241f4a61e8d8c8de7a5573e8de534cb927a71b30
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "101097907"
---
# <a name="configure-an-azure-front-door-standardpremium-preview-endpoint-with-endpoint-manager"></a>エンドポイント マネージャーを使用して Azure Front Door Standard/Premium (プレビュー) エンドポイントを構成する

> [!NOTE]
> このドキュメントは、Azure Front Door Standard/Premium (プレビュー) を対象としています。 Azure Front Door については、 **[Azure Front Door ドキュメント](../front-door-overview.md)** を参照してください。

この記事では、エンドポイント マネージャーを使用して、既存の Azure Front Door Standard/Premium プロファイルのエンドポイントを作成する方法について説明します。

> [!IMPORTANT]
> Azure Front Door Standard/Premium (プレビュー) は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

エンドポイント マネージャーを使用して Azure Front Door Standard/Premium エンドポイントを作成するには、事前に少なくとも 1 つの Azure Front Door プロファイルを作成しておく必要があります。 そのプロファイルには、少なくとも 1 つ以上の Azure Front Door Standard/Premium エンドポイントが必要です。 インターネット ドメイン、Web アプリケーション、またはその他の一部の基準別に Azure Front Door Standard/Premium エンドポイントを整理する場合、複数のプロファイルを使用できます。 

Azure Front Door プロファイルを作成するには、[新しい Azure Front Door Standard/Premium プロファイルの作成](create-front-door-portal.md)に関するページを参照してください。

## <a name="create-a-new-azure-front-door-standardpremium-endpoint"></a>新しい Azure Front Door Standard/Premium エンドポイントを作成する

1. [Azure portal](https://portal.azure.com) サインインし、Azure Front Door Standard/Premium のプロファイルに移動します。

1. **[エンドポイント マネージャー]** を選択します。 次に、 **[エンドポイントの追加]** を選択して新しいエンドポイントを作成します。
   
    :::image type="content" source="../media/how-to-configure-endpoint-manager/select-create-endpoint.png" alt-text="エンドポイント マネージャーを使用したエンドポイントの追加のスクリーンショット。":::

1. **[エンドポイントの追加]** ページで、次の設定を入力および選択します。
    
    :::image type="content" source="../media/how-to-configure-endpoint-manager/create-endpoint-page.png" alt-text="[エンドポイントの追加] ページのスクリーンショット。":::

    | 設定 | 値 |
    | -------- | ----- |
    | 名前 | 新しい Azure Front Door Standard/Premium エンドポイントの一意の名前を入力します。 この名前は、ドメイン `<endpointname>.az01.azurefd.net` でキャッシュされたリソースにアクセスする際に使用します。 |
    | Origin Response timeout (secs) (配信元の応答のタイムアウト (秒)) | 配信元との接続がタイムアウトになったと見なされるまでの間、Azure Front Door が待機するタイムアウト値を秒数で入力します。 |
    | Status | このエンドポイントを有効にするには、このチェックボックスをオンにします。 |

## <a name="add-domains-origin-group-routes-and-security"></a>ドメイン、配信元グループ、ルート、セキュリティを追加する

1. エンドポイントで **[エンドポイントの編集]** を選択して、ルートを構成します。

1. **[エンドポイントの編集]** ページの [ドメイン] で **[+ 追加]** を選択します。
   
    :::image type="content" source="../media/how-to-configure-endpoint-manager/select-add-domain.png" alt-text="[エンドポイントの編集] ページのドメインの選択のスクリーンショット。":::

### <a name="add-domain"></a>ドメインの追加

1. **[ドメインの追加]** ページで、"*Azure Front Door プロファイルから*" ドメインを関連付けるか、"*新しいドメインを追加*" するかを選択します。 新しいドメインを作成する方法については、[新しい Azure Front Door Standard/Premium カスタム ドメインの作成](how-to-add-custom-domain.md)に関するページを参照してください。

    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-domain-page.png" alt-text="[ドメインの追加] ページのスクリーンショット。":::

1. **[追加]** を選択して、現在のエンドポイントにドメインを追加します。 選択したドメインが [ドメイン] パネル内に表示されます。

    :::image type="content" source="../media/how-to-configure-endpoint-manager/domain-in-domainview.png" alt-text="[ドメイン] ビューのドメインのスクリーンショット。":::

### <a name="add-origin-group"></a>配信元グループを追加する

1. [配信元グループ] ビューで **[追加]** を選択します。 **[配信元グループの追加]** ページが表示されます。 

    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-origin-group-view.png" alt-text="[配信元グループの追加] ページのスクリーンショット。":::

1. **[名前]** に、新しい配信元グループの一意の名前を入力します。

1. **[Add an Origin]\(配信元の追加\)** を選択して、現在のグループに新しい配信元を追加します。
 
#### <a name="health-probes"></a>正常性プローブ
Front Door は、各配信元に定期的な HTTP または HTTPS プローブ要求を送信します。 プローブ要求は、エンドユーザーの要求の負荷を分散するために、各配信元の近接性と正常性を判別します。 配信元グループの正常性プローブの設定では、アプリ配信元の正常性状態をポーリングする方法を定義します。 次の設定は、負荷分散構成で使用できます。

> [!WARNING]
> Front Door には世界中に多くのエッジ環境があるので、配信元に対する正常性プローブの量が極めて多くなる可能性があります。構成されている正常性プローブの頻度にもよりますが、毎分 25 回要求されることもあれば、多いときは毎分 1200 回要求されることもあります。 既定のプローブ頻度は 30 秒であり、配信元のプローブ量は毎分 200 要求程度になるはずです。

* **状態**: 正常性プローブをオンにするかどうかを指定します。 配信元グループにある配信元が 1 つである場合は、正常性プローブを無効にして、アプリケーション バックエンドの負荷を減らすことができます。 グループに複数の配信元があるが、そのうちの 1 つだけが有効な状態にある場合でも、正常性プローブを無効にできます。
   
* **パス**: この配信元グループ内のすべての配信元に対するプローブ要求に使用される URL。 たとえば、配信元の 1 つが contoso-westus.azurewebsites.net で、そのパスが /probe/test.aspx に設定されている場合、Front Door 環境では、プロトコルが HTTP に設定されていると想定して、正常性プローブ要求が `http://contoso-westus.azurewebsites.net/probe/test.aspx` に送信されます。 
   
* **プロトコル**: Front Door から配信元への正常性プローブ要求を HTTP と HTTPS プロトコルのどちらを経由して送信するかを定義します。
   
* **プローブ メソッド**: 正常性プローブの送信に使用される HTTP メソッド。 オプションには、GET または HEAD (既定値) があります。

    > [!NOTE]
    > 配信元の負荷とコストを抑えるため、Front Door では正常性プローブに HEAD 要求を使用することが推奨されています。

* **間隔 (秒)** : 配信元に対する正常性プローブの頻度、つまり各 Front Door 環境がプローブを送信する間隔を定義します。
   
    >[!NOTE]
    >フェールオーバーを高速化するには、間隔をより小さい値に設定してください。 値が小さくなると、配信元が受信する正常性プローブのボリュームは大きくなります。 たとえば、間隔が 30 秒に設定されていて、100 個の Front Door POP が世界中に存在する場合、各バックエンドは 1 分あたり約 200 件のプローブ要求を受信します。

#### <a name="load-balancing"></a>負荷分散
配信元グループの負荷分散の設定では、正常性プローブの評価方法を定義します。 これらの設定により、バックエンドが正常か異常かが判断されます。 また、配信元グループ内の異なる配信元間でのトラフィックの負荷分散方法も確認されます。 次の設定は、負荷分散構成で使用できます。

- **サンプル サイズ**。 配信元正常性評価のために検討が必要な正常性プローブのサンプル数を特定します。

- **成功サンプル サイズ**。 前述のとおり、サンプル サイズ、つまり配信元が正常であると見なすために必要な成功サンプルの数を定義します。 たとえば、Front Door の正常性プローブの間隔が 30 秒、サンプル サイズが 5、成功サンプル サイズが 3 だとします。 配信元の正常性プローブを評価するたびに、150 秒間 (5 x 30) で最新の 5 つのサンプルを確認します。 バックエンドが正常であると宣言するには、少なくとも 3 つのプローブが成功している必要があります。

- **Latency sensitivity (extra latency) (待機時間感度 (追加の待機時間))** . Front Door で待機時間の測定感度の範囲内にある配信元に要求を送信するか、最も近いバックエンドに要求を転送するかを定義します。

**[追加]** を選択して、配信元グループを現在のエンドポイントに追加します。 配信元グループは、[配信元グループ] パネル内に表示されます。

:::image type="content" source="../media/how-to-configure-endpoint-manager/origin-in-origin-group.png" alt-text="配信元グループ内の配信元のスクリーンショット。":::

### <a name="add-route"></a>ルートの追加

[ルート] ビューで **[追加]** を選択すると、 **[ルートの追加]** ページが表示されます。 ドメインと配信元グループを関連付ける方法については、[新しい Azure Front Door ルートの作成](how-to-configure-route.md)に関するページを参照してください。

### <a name="add-security"></a>セキュリティの追加

1. [セキュリティ] ビューで **[追加]** を選択すると、 **[WAF ポリシーの追加]** ページが表示されます。
 
    :::image type="content" source="../media/how-to-configure-endpoint-manager/add-waf-policy-page.png" alt-text="[WAF ポリシーの追加] ページのスクリーンショット。":::

1. **WAF ポリシー**: このエンドポイント内で選択したドメインに適用する WAF ポリシーを選択します。 
  
   新しい WAF ポリシーを作成するには、 **[新規作成]** を選択します。

    :::image type="content" source="../media/how-to-configure-endpoint-manager/create-new-waf-policy.png" alt-text="新しい WAF ポリシーの作成のスクリーンショット。":::
   
    **[名前]** : 新しい WAF ポリシーの一意の名前を入力します。 このポリシーは、Web Application Firewall ページから追加の構成を指定して編集することができます。

    **ドメイン**: WAF ポリシーを適用するドメインを選択します。

1. **[追加]** ボタンを選択します。 WAF ポリシーが [セキュリティ] パネル内に表示されます。

    :::image type="content" source="../media/how-to-configure-endpoint-manager/waf-in-security-view.png" alt-text="[セキュリティ] ビューの WAF ポリシーのスクリーンショット。":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったエンドポイントを削除するには、エンドポイント行の最後にある **[エンドポイントの削除]** を選択します。 

:::image type="content" source="../media/how-to-configure-endpoint-manager/delete-endpoint.png" alt-text="エンドポイントを削除する方法のスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

カスタム ドメインについて学習するには、[カスタム ドメインの追加](how-to-add-custom-domain.md)に関するページに進んでください。
