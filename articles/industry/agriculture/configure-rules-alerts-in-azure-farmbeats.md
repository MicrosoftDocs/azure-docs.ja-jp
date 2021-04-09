---
title: ルールの構成とアラートの管理
description: FarmBeats でルールを構成したりアラートを管理したりする方法について説明します。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: a04f973cbfa3a68016065f50e9e2ff4f7566da94
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182929"
---
# <a name="configure-rules-and-manage-alerts"></a>ルールの構成とアラートの管理

Azure FarmBeats では、ファーム内に配置されたセンサーおよびデバイスから配信されるセンサー データに加え、ビジネス ロジックに基づいてルールを作成することができます。 センサーの値がしきい値を超えるとすぐに、それらのルールによってシステム内のアラートがトリガーされます。 しきい値に基づいて作成されたアラートを見て分析することで、あらゆる問題に迅速に対応し、必要な解決策を得ることができます。

## <a name="create-rule"></a>ルールを作成する

1. ホーム ページで、 **[Rules]\(ルール\)** に移動します。
2. **[New Rule]\(新しいルール\)** を選択します。 [New Rule]\(新しいルール\) ウィンドウが表示されます。

    ![[New Rule]\(新しいルール\) ボタンと [New Rule]\(新しいルール\) セクションのスクリーンショット。](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-1.png)

3. **[Rule Name]\(ルール名\)** と **[Rule Description]\(ルールの説明\)** を入力し、 **[Select Farm]\(ファームの選択\)** ボックスの一覧からいずれかのファームを選択します。
4. 該当するファームの名前を入力してそのファームを選択すると、同じウィンドウに **[Conditions]\(条件\)** セクションが表示されます。  

    ![[Conditions]\(条件\) セクションのスクリーンショット。](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-condition-1.png)

5. **[Conditions]\(条件\)** の **[Measure]\(メジャー\)** 、 **[Operator]\(演算子\)** 、 **[Value]\(値\)** にそれぞれ値を入力します。
6. **[Measure]\(メジャー\)** ドロップダウン メニューにメジャーの名前を入力します。
7. ルールに条件を追加するには、 **[+Add Condition]\(+条件の追加\)** を選択します。
8. **[Severity level]\(重大度レベル\)** を選択します。
9. メール アラートを有効にするには、 **[Action]\(アクション\)** の **[Email enabled]\(メールを有効にする\)** トグル ボタンをオンにします。

    ![[Email enabled]\(メールを有効にする\) オプションのスクリーンショット。](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-email-1.png)

10. メール アラートの送信先となる **[Email addresses]\(メール アドレス\)** を入力し、 **[Email Subject]\(メールの件名\)** と **[Additional Notes]\(追加のメモ\)** を入力します。  
11. ルールの有効と無効は、 **[Rule Status]\(ルールの状態\)** の **[Enabled]\(有効\)** トグル ボタンで切り替えます。
    このルールの影響を受けるデバイスの数を確認できます。
12. **[Apply]\(適用\)** を選択してルールを作成します。

## <a name="view-rule"></a>ルールを表示する

**[Farm]\(ファーム\)** ページには、使用可能なルールが一覧表示されます。 **ルール名** を選択します。 選択したルールについて次の情報がウィンドウに表示されます。
 - 規則の名前
 - ルールが関連付けられているファームへのリンク
 - 作成日
 - 最終更新日
 - 重大度レベル
 - ルールの状態
 - 条件の一覧  
 - ルールの影響を受けるデバイスの数

    ![ルールの詳細画面のスクリーンショット。](./media/configure-rules-and-alerts-in-azure-farmbeats/view-rule-1.png)

## <a name="edit-rule"></a>ルールの編集

ルールを編集するには、次の手順に従います。

1. ホーム ページの左側のナビゲーション メニューから **[Rules]\(ルール\)** を選択します。
   ルール ウィンドウが表示されます。
2. 編集するルールを選択します。

    ![ルールが選択されている画面のスクリーンショット。](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-action-bar-1.png)

3. アクション バーから **[Edit]\(編集\)** を選択して、 **[Edit Rule]\(ルールの編集\)** ウィンドウを表示します。

    ![[Edit Rule]\(ルールの編集\) のスクリーンショット。](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-one-1.png)

4. **[Rule Name]\(ルール名\)** と **[Rule Description]\(ルールの説明\)** に変更を加え、 **[Select Farm]\(ファームの選択\)** ボックスの一覧からいずれかのファームを選択します。
5. 該当するファームの名前を入力してそのファームを選択すると、同じウィンドウに **[Conditions]\(条件\)** が表示されます。  
6. **[Conditions]\(条件\)** で、 **[Measure]\(メジャー\)** 、 **[Operator]\(演算子\)** 、 **[Value]\(値\)** を編集します。
7. **[Measure]\(メジャー\)** ドロップダウン メニューにメジャーの名前を入力します。
8. ルールに条件を追加したり条件を編集したりするには、 **[+Add Condition]\(+条件の追加\)** を選択します。

    ![[Add Condition]\(条件の追加\) ボタンのスクリーンショット。](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-two-1.png)

9.  **[Severity level]\(重大度レベル\)** を選択します。  
10. メール アラートを有効にするには、 **[Action]\(アクション\)** の **[Email enabled]\(メールを有効にする\)** トグル ボタンをオンにします。
11. メール アラートの送信先となる **[Email addresses]\(メール アドレス\)** を編集し、 **[Email Subject]\(メールの件名\)** と **[Additional Notes]\(追加のメモ\)** を編集します。  
12. ルールの有効と無効は、 **[Rule Status]\(ルールの状態\)** の **[Enabled]\(有効\)** トグル ボタンで切り替えます。
このルールの影響を受けるデバイスの数を確認できます。
13. **[Apply]\(適用\)** を選択してルールを編集します。

## <a name="change-rule-status"></a>ルールの状態を変更する

ルールの状態を変更するには、次の手順を実行します。

1. ホーム ページの左側のナビゲーション メニューから **[Rules]\(ルール\)** を選択します。 ルール ウィンドウが表示されます。
2. 状態を変更するルールを選択します。

    ![[Change Status]\(状態の変更\) ボタンのスクリーンショット。](./media/configure-rules-and-alerts-in-azure-farmbeats/change-status-rule-action-bar-1.png)

3. アクション バーから **[Change Status]\(状態の変更\)** を選択します。 **[Change Status]\(状態の変更\)** ウィンドウが表示されます。

    ![[Change Status]\(状態の変更\) 画面のスクリーンショット。](./media/configure-rules-and-alerts-in-azure-farmbeats/rule-change-status-1.png)

3. **[Change Status]\(状態の変更\)** トグル ボタンを使用してルールの状態を変更します。
   このルールの影響を受けるデバイスの数を確認できます。
4. **[Apply]\(適用\)** を選択すると、ルールの状態が変更されます。

## <a name="delete-rule"></a>規則の削除

ルールを削除するには、次の手順に従います。

1. ホーム ページの左側のナビゲーション メニューから **[Rules]\(ルール\)** を選択します。 ルール ウィンドウが表示されます。
2. 削除するルールを選択します。

    ![[Delete]\(削除\) ボタンのスクリーンショット。](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-action-bar-1.png)

3. アクション バーから **[Delete]\(削除\)** を選択します。

    ![プロジェクト (Farm Beats)](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-1.png)

4. **[Delete Rule]\(ルールの削除\)** ダイアログ ボックスが表示されます。 **[削除]** を選択します。
