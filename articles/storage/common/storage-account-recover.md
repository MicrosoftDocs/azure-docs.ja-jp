---
title: 削除されたストレージ アカウントを復旧します
titleSuffix: Azure Storage
description: 削除されたストレージ アカウントを Azure portal で復旧する方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: f57cd3361d7888d9d7f747955257d96282274fd6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97357348"
---
# <a name="recover-a-deleted-storage-account"></a>削除されたストレージ アカウントを復旧します

削除されたストレージ アカウントは、Azure portal 内から復旧できる場合があります。 ストレージ アカウントを復旧するには、次の条件が満たされている必要があります。

- ストレージ アカウントが過去 14 日以内に削除された。
- ストレージ アカウントは、Azure Resource Manager デプロイ モデルを使用して作成されている。
- 元のアカウントが削除されて以来、同じ名前の新しいストレージ アカウントが作成されていない。

削除されたストレージ アカウントの復旧を試みる前に、そのアカウントのリソース グループが存在することを確認してください。 リソース グループが削除されている場合は、再作成する必要があります。 リソース グループを復旧することはできません。 詳細については、[リソース グループの管理](../../azure-resource-manager/management/manage-resource-groups-portal.md)に関する記事を参照してください。

削除されたストレージ アカウントによって Azure Key Vault でカスタマー マネージド キーが使用しされており、キー コンテナーも削除されている場合は、ストレージ アカウントを復元する前に、キー コンテナーを復元する必要があります。 詳細については、[Azure Key Vault の復旧の概要](../../key-vault/general/key-vault-recovery.md)に関する記事を参照してください。

> [!IMPORTANT]
> 削除されたストレージ アカウントが復旧できるかどうかの保証はありません。 復旧はベストエフォートで試行されます。 Microsoft では、アカウントを誤って削除しないように、リソースをロックすることを推奨しています。 リソースのロックの詳細については、[リソースのロックによる変更の防止](../../azure-resource-manager/management/lock-resources.md)に関する記事を参照してください。
>
> アカウントを誤って削除しないようにするもう 1 つのベスト プラクティスは、ロールベースのアクセス制御 (Azure RBAC) を使用してアカウントを削除できるアクセス許可を持つユーザーの数を制限することです。 詳細については、「[Azure RBAC のベスト プラクティス](../../role-based-access-control/best-practices.md)」を参照してください。

## <a name="recover-a-deleted-account-from-the-azure-portal"></a>削除されたアカウントを Azure portal から復旧する

削除されたストレージ アカウントを別のストレージ アカウント内から復旧するには、次の手順を実行します。

1. Azure portal の既存のストレージ アカウントの [概要] ページに移動します。
1. **[サポート + トラブルシューティング]** セクションで、 **[Recover deleted account]\(削除されたアカウントの復旧\)** を選択します。
1. 次の画像に示すように、ドロップダウンから復旧するアカウントを選択します。 復旧するストレージ アカウントがドロップダウンにない場合は、復旧することはできません。

    :::image type="content" source="media/storage-account-recover/recover-account-portal.png" alt-text="Azure portal でストレージ アカウントを復旧する方法を示すスクリーンショット":::

1. **[復旧]** ボタンを選択して、アカウントを復元します。 ポータルに、復旧が進行中であることを示す通知が表示されます。

## <a name="recover-a-deleted-account-via-a-support-ticket"></a>サポート チケットを使用して削除されたアカウントを復旧する

1. Azure portal で **[ヘルプとサポート]** に移動します。
1. **[新しいサポート リクエスト]** を選択します。
1. **[基本]** タブの **[問題の種類]** フィールドで、 **[技術]** を選択します。
1. **[サブスクリプション]** フィールドで、削除されたストレージ アカウントが含まれているサブスクリプションを選択します。
1. **[サービス]** フィールドで、 **[ストレージ アカウントの管理]** を選択します。
1. **[リソース]** フィールドで、任意のストレージ アカウント リソースを選択します。 削除されたストレージ アカウントは、一覧に表示されません。
1. 問題の簡単な概要を追加します。
1. **[問題の種類]** フィールドで、 **[Deletion and Recovery]\(削除と回復\)** を選択します。
1. **[問題のサブタイプ]** フィールドで、 **[削除されたストレージ アカウントを復旧する]** を選択します。 次の画像は、記入済みの **[基本]** タブの入力例を示しています。

    :::image type="content" source="media/storage-account-recover/recover-account-support-basics.png" alt-text="サポート チケットの [基本] タブでストレージ アカウントを復旧する方法を示すスクリーンショット":::

1. 次に、次の画像に示すように、 **[ソリューション]** タブに移動し、 **[お客様が管理するストレージ アカウントの復旧]** を選択します。

    :::image type="content" source="media/storage-account-recover/recover-account-support-solutions.png" alt-text="サポート チケットの [ソリューション] タブでストレージ アカウントを復旧する方法を示すスクリーンショット":::

1. 次の画像に示すように、ドロップダウンから復旧するアカウントを選択します。 復旧するストレージ アカウントがドロップダウンにない場合は、復旧することはできません。

    :::image type="content" source="media/storage-account-recover/recover-account-support.png" alt-text="サポート チケットを使用してストレージ アカウントを復旧する方法を示すスクリーンショット":::

1. **[復旧]** ボタンを選択して、アカウントを復元します。 ポータルに、復旧が進行中であることを示す通知が表示されます。

## <a name="next-steps"></a>次のステップ

- [ストレージ アカウントの概要](storage-account-overview.md)
- [ストレージ アカウントの作成](storage-account-create.md)
- [汎用 v2 ストレージ アカウントにアップグレードする](storage-account-upgrade.md)
- [Azure ストレージ アカウントを別のリージョンに移動する](storage-account-move.md)