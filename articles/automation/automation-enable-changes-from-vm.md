---
title: Azure VM で Azure Automation の変更履歴とインベントリを有効にする
description: この記事では、Azure VM で変更履歴とインベントリを有効にする方法について説明します。
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 5ff6e0ffd4040393a040dc67a511aab47887f6e1
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86186267"
---
# <a name="enable-change-tracking-and-inventory-from-an-azure-vm"></a>Azure VM で変更履歴とインベントリを有効にする

この記事では、Azure VM を使用して、他のコンピューターで[変更履歴とインベントリ](change-tracking.md)機能を有効にする方法について説明します。 Azure VM を大規模に有効にするには、変更履歴とインベントリを使用して既存の VM を有効にする必要があります。 

> [!NOTE]
> 変更履歴とインベントリを有効にする際、Log Analytics ワークスペースと Automation アカウントのリンクは特定のリージョンでのみサポートされています。 サポートされているマッピング ペアの一覧については、[Automation アカウントと Log Analytics ワークスペースのリージョン マッピング](how-to/region-mappings.md)に関する記事をご覧ください。

## <a name="prerequisites"></a>前提条件

* Azure のサブスクリプション。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)にサインアップしてください。
* マシンを管理する [Automation アカウント](./index.yml)。
* [仮想マシン](../virtual-machines/windows/quick-create-portal.md)。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="enable-change-tracking-and-inventory"></a>Change Tracking と Inventory の有効化

1. [[Azure portal]](https://portal.azure.com) で **[仮想マシン]** を選択するか、[ホーム] ページから **[仮想マシン]** を検索して選択します。

2. 変更履歴とインベントリを有効にする VM を選択します。 VM は、ご使用の Automation アカウントの場所に関係なく任意のリージョンに存在できます。

3. VM ページで、 **[構成管理]** で、 **[インベントリ]** または **[Change tracking]\(変更履歴\)** を選択します。

4. ワークスペースに VM を有効にするかどうかを判断するには、`Microsoft.OperationalInsights/workspaces/read` アクセス許可が必要です。 必要な追加のアクセス許可については、[機能セットアップのアクセス許可](automation-role-based-access-control.md#feature-setup-permissions)に関するページをご覧ください。 一度に複数のコンピューターを有効にする方法については、「[Automation アカウントで変更履歴とインベントリを有効にする](automation-enable-changes-from-auto-acct.md)」をご覧ください。

5. Log Analytics ワークスペースおよび Automation アカウントを選択し、 **[有効にする]** をクリックして、VM で変更履歴とインベントリを有効にします。 セットアップが完了するまでに最高 15 分かかります。 

## <a name="next-steps"></a>次のステップ

* この機能の操作の詳細については、[変更履歴とインベントリの管理](change-tracking-file-contents.md)に関するページをご覧ください。
* この機能に関する一般的な問題をトラブルシューティングするには、[変更履歴とインベントリでの問題のトラブルシューティング](troubleshoot/change-tracking.md)に関するページをご覧ください。
