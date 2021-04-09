---
title: Azure Automation の変更履歴とインベントリ機能を削除する
description: この記事では、変更履歴とインベントリの使用を停止し、Log Analytics ワークスペースから Automation アカウントをリンク解除する方法について説明します。
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 2e3e5abdfbb2bf2e9d7a12a677422adc67336775
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92209240"
---
# <a name="remove-change-tracking-and-inventory-from-automation-account"></a>Automation アカウントから変更履歴とインベントリを削除する

Azure Automation の変更履歴とインベントリを使用して仮想マシンの管理を有効にした後は、その使用を停止し、アカウントおよびリンクされた Log Analytics ワークスペースからその構成を削除することができます。 この記事では、マネージド VM、ご自分の Automation アカウント、Log Analytics ワークスペースから変更履歴とインベントリを完全に削除する方法について説明します。

## <a name="sign-into-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="remove-management-of-vms"></a>VM の管理を削除する

変更履歴とインベントリを削除する前に、まず VM の管理を停止する必要があります。 機能からそれらの登録を解除するには、[変更履歴からの VM の削除](remove-vms-from-change-tracking.md)に関する記事を参照してください。

## <a name="remove-changetracking-solution"></a>ChangeTracking ソリューションを削除する

ワークスペースから Automation アカウントのリンクを解除する前に、次の手順に従って、変更履歴とインベントリを完全に削除する必要があります。 **ChangeTracking** ソリューションをワークスペースから削除します。

1. Azure Portal で **[すべてのサービス]** を選択します。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて一覧から候補が絞り込まれます。 **[Log Analytics]** を選択します。

2. Log Analytics ワークスペースの一覧で、変更履歴とインベントリを有効化するときに選択したワークスペースを選択します。

3. 左側で、 **[ソリューション]** を選択します。  

4. ソリューションの一覧から、**ChangeTracking(<ワークスペース名>)** を選択します。 ソリューションの **[概要]** ページで、 **[削除]** を選択します。 確認メッセージが表示されたら、 **[はい]** を選択します。

## <a name="unlink-workspace-from-automation-account"></a>Automation アカウントからワークスペースのリンクを解除する

1. Azure portal で、 **[Automation アカウント]** を選択します。

2. 自分の Automation アカウントを開いて、左側にある **[関連リソース]** の **[リンクされたワークスペース]** を選択します。

3. **[ワークスペースのリンクを解除]** ページで **[ワークスペースのリンクを解除]** を選択して、プロンプトに応答します。

   ![[ワークスペースのリンクを解除] ページ](media/remove-feature/automation-unlink-workspace-blade.png)

Log Analytics ワークスペースとのリンク解除が試行されている間、メニューの **[通知]** で進行状況を追跡できます。

または、Automation アカウントと Log Analytics ワークスペースのリンクをワークスペース内から解除することもできます。

1. Azure Portal で **[Log Analytics]** を選択します。

2. ワークスペースから、 **[関連リソース]** の **[Automation アカウント]** を選択します。

3. [Automation アカウント] ページで **[アカウントのリンク解除]** を選択します。

Automation アカウントとのリンク解除が試行されている間、メニューの **[通知]** で進行状況を追跡できます。

## <a name="next-steps"></a>次の手順

この機能を再度有効にするには、「[Automation アカウントから変更履歴とインベントリを有効にする](enable-from-automation-account.md)」、[Azure portal の参照による変更履歴とインベントリの有効化](enable-from-portal.md)に関する記事、「[Runbook から変更履歴とインベントリを有効にする](enable-from-runbook.md)」、または「[Azure VM から変更履歴とインベントリを有効にする](enable-from-vm.md)」を参照してください。
