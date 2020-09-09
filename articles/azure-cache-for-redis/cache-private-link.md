---
title: Azure Private Link を使用した Azure Cache for Redis (プレビュー)
description: Azure プライベート エンドポイントは、Azure Private Link を使用した Azure Cache for Redis にプライベートかつ安全に接続するネットワーク インターフェイスです。 この記事では、Azure portal を使って Azure Cache、Azure 仮想ネットワーク、プライベート エンドポイントを作成する方法について説明します。
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: 5db756b60330cdac4e43e13bfe29d9397f87af50
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87421656"
---
# <a name="azure-cache-for-redis-with-azure-private-link-preview"></a>Azure Private Link を使用した Azure Cache for Redis (プレビュー)
Azure プライベート エンドポイントは、Azure Private Link を使用した Azure Cache for Redis にプライベートかつ安全に接続するネットワーク インターフェイスです。 

この記事では、Azure portal を使って Azure Cache、Azure 仮想ネットワーク、プライベート エンドポイントを作成する方法について説明します。  

> [!IMPORTANT]
> このプレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。 
> 

## <a name="prerequisites"></a>前提条件
* Azure サブスクリプション -  [無料アカウントを作成します](https://azure.microsoft.com/free/)

> [!NOTE]
> この機能は現在プレビュー段階です。ご興味がある場合は[お問い合わせください](mailto:azurecache@microsoft.com)。
>

## <a name="create-a-cache"></a>キャッシュの作成
1. キャッシュを作成するには、[Azure portal](https://portal.azure.com) にサインインし、 **[リソースの作成]** を選択します。 

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="[リソースの作成] を選択します。":::
   
1. **[新規]** ページで、 **[データベース]** を選択し、 **[Azure Cache for Redis]** を選択します。

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="[Azure Cache for Redis] を選択します。":::
   
1. **[新規 Redis Cache]** ページで、新しいキャッシュの設定を構成します。
   
   | 設定      | 推奨値  | 説明 |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS 名** | グローバルに一意の名前を入力します。 | キャッシュ名は 1 から 63 文字の文字列で、数字、英字、ハイフンのみを使用する必要があります。 名前の先頭と末尾には数字または文字を使用する必要があり、連続するハイフンを含めることはできません。 キャッシュ インスタンスの "*ホスト名*" は、 *\<DNS name>.redis.cache.windows.net* になります。 | 
   | **サブスクリプション** | ドロップダウンで、ご自身のサブスクリプションを選択します。 | この新しい Azure Cache for Redis インスタンスが作成されるサブスクリプション。 | 
   | **リソース グループ** | ドロップ ダウンでリソース グループを選択するか、 **[新規作成]** を選択し、新しいリソース グループの名前を入力します。 | その中にキャッシュやその他のリソースを作成するリソース グループの名前。 すべてのアプリ リソースを 1 つのリソース グループに配置することで、それらをまとめて簡単に管理または削除できます。 | 
   | **場所** | ドロップ ダウンで場所を選択します。 | キャッシュを使用する他のサービスの近くの[リージョン](https://azure.microsoft.com/regions/)を選択します。 |
   | **価格レベル** | ドロップ ダウンで[価格レベル](https://azure.microsoft.com/pricing/details/cache/)を選択します。 |  価格レベルによって、キャッシュに使用できるのサイズ、パフォーマンス、および機能が決まります。 詳細については、[Azure Cache for Redis の概要](cache-overview.md)に関するページを参照してください。 |
   
1. **［作成］** を選択します 
   
    :::image type="content" source="media/cache-private-link/3-new-cache.png" alt-text="Azure Cache for Redis を作成します。":::
   
   キャッシュが作成されるまで、しばらく時間がかかります。 Azure Cache for Redis の **[概要]** ページで進行状況を監視できます。 **[状態]** に "**実行中**" と表示されている場合は、キャッシュを使用する準備ができています。
    
    :::image type="content" source="media/cache-private-link/4-status.png" alt-text="Azure Cache for Redis が作成されました。":::

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

このセクションでは、仮想ネットワークとサブネットを作成します。

1. **[リソースの作成]** を選択します。

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="[リソースの作成] を選択します。":::

2. **[新規]** ページで、 **[ネットワーク]** を選択してから、 **[仮想ネットワーク]** を選択します。

    :::image type="content" source="media/cache-private-link/5-select-vnet.png" alt-text="仮想ネットを作成します。":::

3. **[仮想ネットワークの作成]** の **[基本]** タブで次の情報を入力または選択します。

    | **設定**          | **Value**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **プロジェクトの詳細**  |                                                                 |
    | サブスクリプション     | ドロップダウンで、ご自身のサブスクリプションを選択します。                                  |
    | リソース グループ   | ドロップ ダウンで、リソース グループを選択します。 |
    | **インスタンスの詳細** |                                                                 |
    | 名前             | 「 **\<virtual-network-name>** 」と入力します。                                    |
    | リージョン           | [ **] \<region-name> [** ] を選択します。 |

4. **[IP アドレス]** タブを選択するか、ページの下部にある **[Next: IP Addresses]\(次へ: IP アドレス\)** ボタンを選択します。

5. **[IP アドレス]** タブで、次の情報を入力します。

    | 設定            | 値                      |
    |--------------------|----------------------------|
    | IPv4 アドレス空間 | 「 **\<IPv4-address-space>** 」と入力します。 |

6. **[サブネット名]** で、 **[default]\(既定\)** という単語を選択します。

7. **[サブネットの編集]** に次の情報を入力します。

    | 設定            | 値                      |
    |--------------------|----------------------------|
    | サブネット名 | 「 **\<subnet-name>** 」と入力します。 |
    | サブネットのアドレス範囲 | 「 **\<subnet-address-range>** 」と入力します。

8. **[保存]** を選択します。

9. **[確認と作成]** タブを選択するか、 **[確認と作成]** ボタンを選択します。

10. **［作成］** を選択します


## <a name="create-a-private-endpoint"></a>プライベート エンドポイントの作成 

このセクションでは、プライベート エンドポイントを作成し、先ほど作成したキャッシュに接続します。

1. **Private Link** を検索して Enter キーを押すか、検索候補から選択します。

    :::image type="content" source="media/cache-private-link/7-create-private-link.png" alt-text="Private Link を検索します。":::

2. 画面の左側で、 **[プライベート エンドポイント]** を選択します。

    :::image type="content" source="media/cache-private-link/8-select-private-endpoint.png" alt-text="プライベート リンクを選択します。":::

3. **[+ 追加]** ボタンを選択して、プライベート エンドポイントを作成します。 

    :::image type="content" source="media/cache-private-link/9-add-private-endpoint.png" alt-text="プライベート リンクを追加します。":::

4. **[プライベート エンドポイントの作成] ページ**で、プライベート エンドポイントの設定を構成します。

    | 設定 | 値 |
    | ------- | ----- |
    | **プロジェクトの詳細** | |
    | サブスクリプション | ドロップダウンで、ご自身のサブスクリプションを選択します。 |
    | Resource group | ドロップ ダウンで、リソース グループを選択します。 |
    | **インスタンスの詳細** |  |
    | 名前 |プライベート エンドポイントの名前を入力します。  |
    | リージョン |ドロップ ダウンで場所を選択します。 |
    |||

5. ページの下部にある **[次へ: リソース]** ボタンを選択します。

6. **[リソース]** タブで、サブスクリプションを選択し、[リソースの種類] として [Microsoft.Cache/Redis] を選択し、前のセクションで作成したキャッシュを選択します。

    :::image type="content" source="media/cache-private-link/10-resource-private-endpoint.png" alt-text="プライベート リンクの [リソース]。":::

7. ページの下部にある **[次へ: 構成]** ボタンを選択します。

8. **[構成]** タブで、前のセクションで作成した仮想ネットワークとサブネットを選択します。

    :::image type="content" source="media/cache-private-link/11-configuration-private-endpoint.png" alt-text="プライベート リンクの [構成]。":::

9. ページの下部にある **[次へ: タグ]** ボタンを選択します。

10. リソースを分類する場合は、 **[タグ]** タブに名前と値を入力します。 この手順は省略可能です。

    :::image type="content" source="media/cache-private-link/12-tags-private-endpoint.png" alt-text="プライベート リンクの [タグ]。":::

11.  **[確認および作成]** を選択します。  **[確認および作成]**   タブが表示され、Azure によって構成が検証されます。

12. 緑色の**検証に成功**のメッセージが表示されたら、 **[作成]** を選択します。


## <a name="next-steps"></a>次の手順

Private Link の詳細については、[Azure Private Link](https://docs.microsoft.com/azure/private-link/private-link-overview) に関するドキュメントを参照してください。 

