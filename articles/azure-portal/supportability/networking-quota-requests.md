---
title: ネットワークの上限の引き上げ
description: ネットワークの上限の引き上げ
author: anavinahar
ms.author: anavin
ms.date: 01/23/2020
ms.topic: how-to
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: e945dec2ce8514c3e4f1edecdecd13c5c43f7c75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96745453"
---
# <a name="networking-limit-increase"></a>ネットワークの上限の引き上げ

ネットワーク クォータを増やすには、[Azure portal](https://portal.azure.com) を使用します。

Azure portal で現在のネットワークの使用量とクォータを表示するには、サブスクリプションを開き、 **[Usages + quotas]\(使用量 + クォータ\)** を選択します。 また、次のオプションを使用して、ネットワークの使用量と制限を表示することもできます。

* [使用量 CLI](/cli/azure/network#az-network-list-usages)
* [PowerShell](/powershell/module/azurerm.network/get-azurermnetworkusage)
* [ネットワーク使用量 API](/rest/api/virtualnetwork/virtualnetworks/listusage)

ポータルで **[ヘルプとサポート]** または **[Usages + quotas]\(使用量 + クォータ\)** を使用して、増加を要求できます。

> [!Note]
> **[パブリック IP プレフィックス]** の既定のサイズを変更するには、ドロップダウン リストから **[Min Public IP InterNetwork Prefix Length]\(最小パブリック IP InterNetwork プレフィックス長\)** を選択します。

## <a name="request-networking-quota-increase-at-subscription-level-using-help--support"></a>[ヘルプとサポート] を使用してサブスクリプション レベルでネットワーク クォータの引き上げを要求する

Azure portal で **[ヘルプとサポート]** を使用してサポート リクエストを作成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインし、Azure portal のメニューから **[ヘルプとサポート]** を選択するか、 **[ヘルプとサポート]** を検索して選択します。

    ![[ヘルプとサポート]](./media/networking-quota-request/help-plus-support.png)

1. **[新しいサポート リクエスト]** を選択します。

    ![新しいサポート要求](./media/networking-quota-request/new-support-request.png)

1. **[問題の種類]** については、 **[サービスとサブスクリプションの制限 (クォータ)]** を選択します。

    ![[問題の種類] ドロップダウンからサブスクリプションの制限を選択する](./media/networking-quota-request/select-quota-issue-type.png)

1. クォータの追加が必要なサブスクリプションを選択します。

    ![サブスクリプション newSR を選択する](./media/networking-quota-request/select-subscription-support-request.png)

1. **[クォータの種類]** で、 **[ネットワーク]** を選択します。 **「解決方法」** を参照してください。

    ![[クォータの種類] を選択する](./media/networking-quota-request/select-quota-type-network.png)

1. **[問題の詳細]** で、 **[詳細の指定]** をクリックして、要求の処理に役立つ追加情報を指定します。

    ![[詳細の指定]](./media/networking-quota-request/provide-details-link.png)

1. **[クォータの詳細]** パネルで、要求に含めるデプロイ モデル、場所、およびリソースを選択します。

    ![[クォータの詳細] の DM](./media/networking-quota-request/quota-details-network.png)

1. サブスクリプションに対して必要な新しい制限を入力します。 行を削除するには、 **[リソース]** メニューからリソースの選択を解除するか、破棄 ("x") アイコンを選択します。 各リソースのクォータを入力したら、 **[保存して続行]** を選択してサポート リクエストの作成を続行します。

    ![新しい制限](./media/networking-quota-request/network-new-limits.png)

## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quotas"></a>[Usages + quotas]\(使用量 + クォータ\) を使用してサブスクリプション レベルでネットワーク クォータの引き上げを要求する

Azure portal で **[Usage + quota]\(使用量 + クォータ\)** を使用してサポート リクエストを作成するには、次の手順に従います。

1. https://portal.azure.com から、 **[サブスクリプション]** を検索して選択します。

    ![サブスクリプション](./media/networking-quota-request/search-for-suscriptions.png)

1. クォータの追加が必要なサブスクリプションを選択します。

    ![サブスクリプションの選択](./media/networking-quota-request/select-subscription-change-quota.png)

1. **[使用量 + クォータ]** を選択します。

    ![使用量とクォータを選択します。](./media/networking-quota-request/select-usage-plus-quotas.png)

1. 右上の **[引き上げを依頼する]** を選択します。

    ![引き上げを依頼する](./media/networking-quota-request/request-increase-from-subscription.png)

1. 「[サブスクリプション レベルでネットワーク クォータの引き上げを要求する](#request-networking-quota-increase-at-subscription-level-using-help--support)」の手順 3 以降の手順に従ってください。

## <a name="about-networking-limits"></a>ネットワークの制限について

ネットワークの制限の詳細については、制限に関するページの[ネットワークのセクション](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)、またはネットワークの制限に関する FAQ を参照してください。
