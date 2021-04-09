---
title: Azure VM で Azure Automation の変更履歴とインベントリを有効にする
description: この記事では、Azure VM で変更履歴とインベントリを有効にする方法について説明します。
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 61b45d6f6414b1e8e1f48f6d46957b21b9b8c58b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99052500"
---
# <a name="enable-change-tracking-and-inventory-from-an-azure-vm"></a>Azure VM で変更履歴とインベントリを有効にする

この記事では、Azure VM を使用して他のコンピューターで[変更履歴とインベントリ](overview.md)を有効にする方法について説明します。 Azure VM を大規模に有効にするには、変更履歴とインベントリを使用して既存の VM を有効にする必要があります。

> [!NOTE]
> 変更履歴とインベントリを有効にする際、Log Analytics ワークスペースと Automation アカウントのリンクは特定のリージョンでのみサポートされています。 サポートされているマッピング ペアの一覧については、[Automation アカウントと Log Analytics ワークスペースのリージョン マッピング](../how-to/region-mappings.md)に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件

* Azure のサブスクリプション。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)にサインアップしてください。
* マシンを管理する [Automation アカウント](../automation-security-overview.md)。
* [仮想マシン](../../virtual-machines/windows/quick-create-portal.md)。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="enable-change-tracking-and-inventory"></a>Change Tracking と Inventory の有効化

1. [[Azure portal]](https://portal.azure.com) で **[仮想マシン]** を選択するか、[ホーム] ページから **[仮想マシン]** を検索して選択します。

2. 変更履歴とインベントリを有効にする VM を選択します。 VM は、ご使用の Automation アカウントの場所に関係なく任意のリージョンに存在できます。

3. VM ページで、 **[構成管理]** で、 **[インベントリ]** または **[Change tracking]\(変更履歴\)** を選択します。

4. ワークスペースに VM を有効にするかどうかを判断するには、`Microsoft.OperationalInsights/workspaces/read` アクセス許可が必要です。 必要な追加のアクセス許可については、[機能セットアップのアクセス許可](../automation-role-based-access-control.md#feature-setup-permissions)に関するページをご覧ください。 一度に複数のコンピューターを有効にする方法については、「[Automation アカウントで変更履歴とインベントリを有効にする](enable-from-automation-account.md)」をご覧ください。

5. Log Analytics ワークスペースおよび Automation アカウントを選択し、 **[有効にする]** をクリックして、VM で変更履歴とインベントリを有効にします。 セットアップが完了するまでに最高 15 分かかります。

## <a name="next-steps"></a>次のステップ

* この機能の操作の詳細については、[変更履歴の管理](manage-change-tracking.md)と[インベントリの管理](manage-inventory-vms.md)に関するページをご覧ください。

* この機能に関する一般的な問題のトラブルシューティングについては、「[Change Tracking と Inventory に関する問題のトラブルシューティング](../troubleshoot/change-tracking.md)」を参照してください。