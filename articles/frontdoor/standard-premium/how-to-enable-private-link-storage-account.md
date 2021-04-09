---
title: Private Link を使用して Azure Front Door Premium をストレージ アカウントの配信元に接続する
titleSuffix: Azure Private Link
description: Azure Front Door Premium をストレージ アカウントにプライベートに接続する方法について説明します。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 03/04/2021
ms.author: tyao
ms.openlocfilehash: 885b4d132208ab6f8b470d147438e26a5fd4bab7
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102201670"
---
# <a name="connect-azure-front-door-premium-to-a-storage-account-origin-with-private-link"></a>Private Link を使用して Azure Front Door Premium をストレージ アカウントの配信元に接続する

この記事では、Azure Private Link サービスを使用してストレージ アカウントの配信元にプライベートに接続するように Azure Front Door Premium SKU を構成する方法について説明します。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="enable-private-link-to-a-storage-account"></a>ストレージ アカウントへの Private Link を有効にする
 
このセクションでは、Private Link サービスを、Azure Front Door のプライベート ネットワークに作成されたプライベート エンドポイントにマップします。 

1. Azure Front Door Premium プロファイル内の *[設定]* で、 **[配信元グループ]** を選択します。

1. Private Link を有効にするストレージ アカウントの配信元が含まれている配信元グループを選択します。

1. **[+ Add an origin]\(+ 配信元の追加\)** を選択して新しいストレージ アカウントの配信元を追加するか、以前に作成したストレージ アカウントの配信元を一覧から選択します。

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-storage-account.png" alt-text="ストレージ アカウントへのプライベート リンクを有効にするスクリーンショット。":::

1. **[Azure リソースの選択]** には、 **[In my directory]\(マイ ディレクトリ内\)** を選択します。 次の設定を選択または入力して、Azure Front Door Premium でプライベートに接続するサイトを構成します。

    | 設定 | 値 |
    | ------- | ----- |
    | リージョン | 配信元と同じまたは最も近いリージョンを選択します。 |
    | リソースの種類 | **Microsoft.Storage/storageAccounts** を選択します。 |
    | リソース | 使うストレージ アカウントを選びます。 |
    | 対象サブ リソース | *blob* または *web* を選択できます。 |
    | 要求メッセージ | メッセージをカスタマイズするか、既定値を選択します。 |

1. 次に、 **[追加]** を選択して構成を保存します。

## <a name="approve-private-endpoint-connection-from-the-storage-account"></a>ストレージ アカウントからプライベート エンドポイント接続を承認する

1. 前のセクションで Private Link を構成したストレージ アカウントにアクセスします。 **[設定]** で、 **[ネットワーク]** を選択します。

1. **[ネットワーク]** で、 **[プライベート エンドポイント接続]** を選択します。 

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/storage-account-configure-endpoint.png" alt-text="Web アプリのネットワーク設定のスクリーンショット。":::

1. Azure Front Door Premium から "*保留中*" のプライベート エンドポイント要求を選択し、 **[承認]** を選択します。

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-pending-approval.png" alt-text="保留中のストレージ プライベート エンドポイント要求のスクリーンショット。":::

1. 承認されると、次のスクリーンショットのようになります。 接続が完全に確立されるまで数分かかります。 これで、Azure Front Door Premium からストレージ アカウントにアクセスできるようになりました。

    :::image type="content" source="../media/how-to-enable-private-link-storage-account/private-endpoint-approved.png" alt-text="承認されたストレージ エンドポイント要求のスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

[ストレージ アカウントでの Private Link サービス](../../storage/common/storage-private-endpoints.md)について学習します。
