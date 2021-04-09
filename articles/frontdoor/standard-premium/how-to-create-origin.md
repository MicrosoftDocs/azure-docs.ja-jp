---
title: Azure Front Door Standard/Premium (プレビュー) の配信元を設定する
description: この記事では、エンドポイント マネージャーを使用して配信元を構成する方法について説明します。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: ebc71ea2d354caf0c8f31b1231ecc1487237dd29
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101741890"
---
# <a name="set-up-an-azure-front-door-standardpremium-preview-origin"></a>Azure Front Door Standard/Premium (プレビュー) の配信元を設定する

> [!Note]
> このドキュメントは Azure Front Door Standard/Premium (プレビュー) 向けです。 Azure Front Door については、 [こちら](../front-door-overview.md)をご覧ください。

この記事では、既存の配信元グループに Azure Front Door Standard/Premium の配信元を作成する方法について説明します。 

> [!IMPORTANT]
> Azure Front Door Standard/Premium (プレビュー) は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

Azure Front Door Standard/Premium の配信元を作成する前に、少なくとも 1 つの配信元グループを作成しておく必要があります。

## <a name="create-a-new-azure-front-door-standardpremium-origin"></a>Azure Front Door Standard/Premium の配信元を新しく作成する

1. [Azure portal](https://portal.azure.com) サインインし、Azure Front Door Standard/Premium のプロファイルに移動します。

1. **[配信元グループ]** を選択します。 **[+ 追加]** を選択して、新しい配信元グループを作成します。
   
    :::image type="content" source="../media/how-to-create-origin/select-add-origin.png" alt-text="配信元グループのランディング ページのスクリーンショット。":::

1. **[配信元グループの追加]** ページで、新しい配信元グループの一意な **名前** を入力します。

1. 次に、 **[+ Add an Origin]** \(+ 配信元の追加\) を選択して、この配信元グループに新しい配信元を追加します。 

    :::image type="content" source="../media/how-to-create-origin/add-origin-view.png" alt-text="[Add an origin]\(配信元の追加\) ページのスクリーンショット。":::
  
    | 設定 | 値 |
    | --- | --- |
    | 名前 | 新しい Azure Front Door の配信元の一意の名前を入力します。 |   
    | 配信元の種類 | 追加するリソースの種類。 Azure Front Door Standard/Premium では、アプリ サービス、クラウド サービス、またはストレージからのアプリ配信元の自動検出がサポートされています。 Azure または Azure 以外のバックエンド内の別のリソースが必要な場合は、 **[カスタム ホスト]** 選択します。 |
    | ホスト名  | 配信元のホストの種類で **[カスタム ホスト]** を選択しなかった場合は、ドロップダウンで配信元のホスト名を選択して、バックエンドを選択します。 |
    | 配信元のホスト ヘッダー | 各要求でバックエンドに送信されるホスト ヘッダーの値を入力します。 詳細については、「[配信元のホスト ヘッダー](concept-origin.md#hostheader)」を参照してください。 |
    | HTTP ポート | 配信元で HTTP プロトコルに対してサポートされるポートの値を入力します。 |
    | HTTPS Port | 配信元で HTTPS プロトコルに対してサポートされるポートの値を入力します。 |
    | Priority | すべてのトラフィックにプライマリ サービスの配信元を使用する場合は、さまざまな配信元に優先順位を割り当てます。 さらに、プライマリまたはバックアップの配信元が使用できない場合は、バックアップを用意します。 詳細については、[優先順位](concept-origin.md#priority)に関するセクションを参照してください。 |
    | Weight | 均等にまたは重み係数に従って一連の配信元間でトラフィックを分散するには、さまざまな配信元に重みを割り当てます。 詳細については、[重み](concept-origin.md#weighted)に関するセクションを参照してください。 |
    | Status | 配信元を有効にするには、このオプションを選択します。 |
    | ルール | このルートに適用されるルール セットを選択します。 ルールを構成する方法の詳細については、[Azure Front Door のルールセットの構成](how-to-configure-rule-set.md)に関する記事を参照してください。 | 

    > [!IMPORTANT]
    > 構成中、API では、Front Door 環境から配信元にアクセスできないかどうかは検証されません。 Front Door が配信元にアクセスできることを確認してください。

1. **[追加]** を選択して、新しい配信元を作成します。 作成された配信元は、グループと共に配信元の一覧に表示されます。
  
    :::image type="content" source="../media/how-to-create-origin/origin-list-view.png" alt-text="リスト ビューの配信元のスクリーンショット。":::

1. **[追加]** を選択して、配信元グループを現在のエンドポイントに追加します。 配信元グループは、[配信元グループ] パネル内に表示されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする
必要なくなったときに配信元グループを削除するには、 **[...]** をクリックし、ドロップダウンから **[削除]** を選択します。

:::image type="content" source="../media/how-to-create-origin/delete-origin-group.png" alt-text="配信元グループを削除する方法のスクリーンショット。":::

必要なくなったときに配信元を削除するには、 **[...]** をクリックし、ドロップダウンから **[削除]** を選択します。 

:::image type="content" source="../media/how-to-create-origin/delete-origin-view.png" alt-text="配信元を削除する方法のスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

カスタム ドメインの詳細については、Azure Front Door Standard/Premium エンドポイントへの[カスタム ドメインの追加](how-to-add-custom-domain.md)に関するページを参照してください。
