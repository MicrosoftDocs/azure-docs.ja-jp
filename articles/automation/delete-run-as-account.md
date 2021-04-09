---
title: Azure Automation の実行アカウントを削除する
description: この記事では、PowerShell または Azure portal を使用して、実行アカウントを削除する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 01/06/2021
ms.topic: conceptual
ms.openlocfilehash: 6924a9a9394d237b08db878ef910de6767ca9b39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99056836"
---
# <a name="delete-an-azure-automation-run-as-account"></a>Azure Automation の実行アカウントを削除する

Azure Automation の実行アカウントでは、Automation の Runbook やその他の自動化機能を使用し、Azure Resource Manager または Azure クラシック デプロイ モデルのリソースを管理する認証を提供します。 この記事では、実行またはクラシック実行アカウントを削除する方法について説明します。 この操作を実行するとき、Automation アカウントが保持されます。 実行アカウントを削除した場合、Azure portal または提供されている PowerShell スクリプトを使用してそれを再作成できます。

## <a name="delete-a-run-as-or-classic-run-as-account"></a>実行アカウントまたはクラシック実行アカウントの削除

1. Azure Portal で Automation アカウントを開きます。

2. 左ペインで、アカウントの設定セクションの **[実行アカウント]** を選択します。

3. 実行アカウントのプロパティ ページで、削除する実行アカウントまたはクラシック実行アカウントを選択します。

4. 選択したアカウントの [プロパティ] ペインで、 **[削除]** をクリックします。

   ![Azure 実行アカウントを削除する](media/delete-run-as-account/automation-account-delete-run-as.png)

5. アカウントが削除されている間、メニューの **[通知]** で進行状況を追跡できます。

## <a name="next-steps"></a>次のステップ

実行アカウントまたはクラシック実行アカウントを再作成する方法については、[実行アカウントの作成](create-run-as-account.md)に関するページを参照してください。