---
title: "Azure 仮想マシン内の変更を追跡する | Microsoft Docs"
description: "変更の追跡を使用して、仮想マシン上のファイルとレジストリの変更を追跡します"
services: automation
documentationcenter: automation
author: eslesar
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: automation
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: eslesar
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ba92390b684573818ec9d69fd8c7be6d064316c7
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

# <a name="track-changes-in-your-azure-virtual-machines"></a>Azure 仮想マシン内の変更を追跡する

変更の追跡を有効にすることで、仮想マシン上のファイルと Windows レジストリ キーの変更を追跡できます。 . 構成の変更を識別することで、運用上の問題を特定できるようになります。

変更の追跡は、Azure 仮想マシンから直接有効にすることができます。

Azure 仮想マシンがない場合は、[Windows クイック スタート](../virtual-machines/windows/quick-create-portal.md)または [Linux のクイック スタート](../virtual-machines/linux/quick-create-portal.md)を使用して作成できます。

## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインする

[Azure ポータル](https://portal.azure.com/)にサインインします。

## <a name="enable-change-tracking-for-an-azure-virtual-machine"></a>Azure 仮想マシンに対する変更の追跡を有効にする

1. 画面左側の **[仮想マシン]** を選択します。
1. 一覧から仮想マシンを選択します。
1. [仮想マシン] 画面で、**[操作]** セクションの **[変更の追跡]** をクリックします。 **[更新管理の有効化]** 画面が開きます。

   ![仮想マシンでの変更の追跡の開始](./media/automation-vm-change-tracking/change-onboard-vm-blade.png)

この仮想マシンで追跡の変更が有効になっているかどうかを確認する検証が行われます。 追跡の変更が有効になっていない場合は、ソリューションを有効にするためのオプションを示すバナーが表示されます。

   ![変更の追跡の開始構成バナー](./media/automation-vm-change-tracking/change-onboard-banner.png)

ソリューションを有効にするには、バナーをクリックします。 以下のものがない場合は、自動的に追加されます。

* [Log Analytics](../log-analytics/log-analytics-overview.md) ワークスペース
* [Automation アカウント](../automation/automation-offering-get-started.md)

変更の追跡からのデータ ログを格納する Log Analytics ワークスペースと、変更を追跡するための Automation アカウントを選択し、**[有効化]** をクリックします。

ステータス バーに、ソリューションが有効になっていることが通知されます。 このプロセスには最大 15 分かかる可能性があります。

## <a name="configure-change-tracking"></a>変更の追跡を構成する

変更の追跡が有効になると、**[変更の追跡]** 画面が表示されます。 **[設定の編集]** をクリックして、追跡するファイルとレジストリ キーを選択します。

![変更の追跡の設定の編集](./media/automation-vm-change-tracking/change-edit-settings.png)

**[ワークスペースの構成]** 画面が開きます。 適切なタブの **[追加]** をクリックし、追跡する Windows レジストリ キー、Windows ファイル、または Linux を追加します。

## <a name="add-a-windows-registry-key"></a>Windows レジストリ キーを追加する

1. **[ワークスペースの構成]** 画面の **[Windows レジストリ]** タブで、**[追加]** をクリックします。 **[変更履歴用の Windows レジストリを追加する]** 画面が開きます。

   ![変更の追跡へのレジストリの追加](./media/automation-vm-change-tracking/change-add-registry.png)

1. **[有効化]** で **[True]** を選択します。
1. **[項目名]** フィールドにフレンドリ名を追加します。
1. **[グループ]** フィールドに、グループ名を入力します (省略可能)。
1. **[Windows レジストリ キー]** フィールドに、追跡するレジストリ キーの名前を追加します。
1. **[保存]** をクリックします。

## <a name="add-a-windows-file"></a>Windows ファイルを追加する

1. **[ワークスペースの構成]** 画面の **[Windows ファイル]** タブで、**[追加]** をクリックします。**[変更履歴用の Windows ファイルを追加する]** 画面が開きます。

   ![変更の追跡への Windows ファイルの追加](./media/automation-vm-change-tracking/change-add-win-file.png)

1. **[有効化]** で **[True]** を選択します。
1. **[項目名]** フィールドにフレンドリ名を追加します。
1. **[グループ]** フィールドに、グループ名を入力します (省略可能)。
1. **[パスの入力]** フィールドに、追跡するファイルの完全なパスとファイル名を追加します。
1. **[保存]** をクリックします。

## <a name="add-a-linux-file"></a>Linux ファイルを追加する

1. **[ワークスペースの構成]** 画面の **[Linux ファイル]** タブで、**[追加]** をクリックします。**[変更履歴用の Linux ファイルを追加する]** 画面が開きます。

   ![変更の追跡への Linux ファイルの追加](./media/automation-vm-change-tracking/change-add-linux-file.png)

1. **[有効化]** で **[True]** を選択します。
1. **[項目名]** フィールドにフレンドリ名を追加します。
1. **[グループ]** フィールドに、グループ名を入力します (省略可能)。
1. **[パスの入力]** フィールドに、追跡するファイルの完全なパスとファイル名を追加します。
1. **[パスの種類]** フィールドで、**[ファイル]** または **[ディレクトリ]** を選択します。
1. 指定したパスとその下にあるすべてのファイルとパスの変更を追跡するには、**[再帰]** で、**[オン]** を選択します。 選択したパスまたはファイルのみを追跡するには、**[オフ]** を選択します。
1. `sudo` コマンドを使用してアクセスする必要があるファイルを追跡するには、**[Sudo]** で、**[オン]** を選択します。 それ以外の場合は、**[オフ]** を選択します。
1. **[保存]** をクリックします。

## <a name="view-changes"></a>変更を表示する

**[変更の追跡]** 画面で、仮想マシンで一定期間中に発生したさまざまなカテゴリの変更を確認できます。

   ![変更の追跡での変更の表示](./media/automation-vm-change-tracking/change-view-changes.png)

表示する変更のカテゴリと時間の範囲を選択できます。 画面の上部に、一定期間の変更のグラフィック表示が表示されます。
画面の下部に、最近の変更の一覧が表示されます。

## <a name="view-change-tracking-log-data"></a>変更の追跡のログ データを表示する

変更の追跡は、Log Analytics に送信されるログ データを生成します。 クエリを実行してログを検索するには、**[変更の追跡]** 画面の上部にある **[Log Analytics]** をクリックします。

   ![変更の追跡の Log Analytics](./media/automation-vm-change-tracking/change-log-analytics.png)

Log Analytics でのログ ファイルの検索の実行については、「[Log Analytics とは](../log-analytics/log-analytics-overview.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* 変更の追跡の詳細については、[変更の追跡](../log-analytics/log-analytics-change-tracking.md)に関する記事を参照してください。
* 仮想マシンの更新の詳細については、[更新管理](../operations-management-suite/oms-solution-update-management.md)に関する記事を参照してください。

