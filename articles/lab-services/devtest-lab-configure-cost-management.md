---
title: Azure DevTest Labs で、ラボの月間推定コスト傾向を表示する | Microsoft Docs
description: Azure DevTest Labs の月間推定コスト傾向グラフについて説明します。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1f46fdc5-d917-46e3-a1ea-f6dd41212ba4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2019
ms.author: spelluru
ms.openlocfilehash: 9180c29b807ef26c6426aab75fe74870fef9669a
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68318165"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs でラボに関連付けられているコストを追跡する
この記事では、ラボのコストを追跡する方法について説明します。 ラボの現在のカレンダー月の推定コスト傾向を表示する方法を紹介します。 また、ラボのリソースごとの過去 1 か月間のコストを表示する方法も紹介します。

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>ラボの月間推定コスト傾向を表示する 
このセクションでは、**月間推定コスト傾向**グラフを使用して、現在のカレンダー月の現時点までの推定コストおよび現在のカレンダー月の月末の予測コストを表示する方法について説明します。 また、支出の目標としきい値を設定し、それに達した場合に DevTest Labs をトリガーして結果をレポートすることで、ラボのコストを管理する方法についても説明します。

月間推定コスト傾向グラフを表示するには、次の手順に従います: 

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
3. ラボの一覧でラボを選択します。  
4. 左側のメニューで **[構成とポリシー]** を選択します。  
4. 左側のメニューの **[コスト管理]** セクションで、 **[コスト傾向]** を選択します。 次のスクリーン ショットはコスト グラフの例を示しています。 
   
    ![コスト グラフ](./media/devtest-lab-configure-cost-management/graph.png)

    **[推定コスト]** 値は現在のカレンダー月のこれまでの推定コストです。 **[予測コスト]** は現在のカレンダー月全体の推定コストで、過去 5 日間のラボのコストを使用して計算されます。

    コストの金額の端数は切り上げられます。 例: 

   * 5.01 は切り上げて 6 になります。 
   * 5.50 は切り上げて 6 になります。
   * 5.99 は切り上げて 6 になります。

     グラフの上に示されているとおり、グラフに既定で表示されるコストは、[従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/)プラン料金を使用した*推定*コストです。 [ラボのコスト目標を管理する](#managing-cost-targets-for-your-lab)ことで、グラフに表示する独自の支出目標を設定することもできます。

     コスト計算には次のものは含まれて*いません*。

   * CSP サブスクリプションと Dreamspark サブスクリプションは現在サポートされません。Azure DevTest Labs は、[Azure 課金 API](../billing/billing-usage-rate-card-overview.md) を使用してラボ コストを計算しますが、CSP サブスクリプションも Dreamspark サブスクリプションもサポートしていないためです。
   * プラン料金。 現時点では、Microsoft または Microsoft パートナーと交渉したオファー料金 (サブスクリプションの下に表示される金額) は使用できません。 従量課金制の料金のみが使用されます。
   * 税金
   * 割引
   * 請求通貨。 現時点では、ラボのコストは USD 通貨でのみ表示されます。

### <a name="managing-cost-targets-for-your-lab"></a>ラボのコスト目標を管理する
DevTest Labs を使用して支出目標を設定することで、コストの管理を改善できます。設定した目標は、月間推定コスト傾向グラフに表示されます。 また、DevTest Labs では、指定した目標の支出やしきい値に達したときに通知を送信することもできます。 

1. **[コスト傾向]** ページで **[ターゲットの管理]** を選択します。

    ![[ターゲットの管理] ボタン](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. **[ターゲットの管理]** ウィンドウで、支出目標としきい値を指定します。 選択した各しきい値を、コスト傾向グラフや webhook 通知でレポートするかどうかも設定できます。

    ![[ターゲットの管理] ウィンドウ](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - コスト目標を追跡する期間を選択します。
      - **毎月**: コスト目標は月 1 回追跡されます。
      - **固定**: コスト目標は、開始日と終了日で指定した日付範囲の間追跡されます。 通常、これらの値はプロジェクトの実行予定期間を表します。
   - **目標コスト**を指定します。 たとえば、定義した期間にこのラボで支出が予定されている金額を指定します。
   - レポートされるしきい値を有効にするか無効にするかを選択します。指定した**目標コスト**の最大 125% まで、25% 刻みで増やして設定できます。
      - **通知**: しきい値に達すると、指定した webhook URL により通知されます。
      - **グラフ上のプロット**: しきい値に達すると、「月間推定コスト傾向グラフを表示する」での説明のとおり、結果がコスト傾向グラフ上にプロットされ、確認できるようになります。
   - しきい値に達したときに**通知**されるように選択した場合、webhook の URL を指定する必要があります。 [コストの統合] 領域で、 **[ここをクリックして統合を追加します]** をクリックします。 [通知の構成] ウィンドウで **webhook の URL** を入力し、 **[OK]** を選択します。

       ![[通知の構成] ウィンドウ](./media/devtest-lab-configure-cost-management/configure-notification.png)

     - **[通知]** を指定した場合、webhook の URL を設定する必要があります。
     - 同様に、webhook の URL を設定した場合は、[コストのしきい値] ウィンドウで **[通知]** を **[オン]** に設定する必要があります。
     - このウィンドウで入力する前に、webhook を作成しておく必要があります。  

       webhook の詳細については、「[webhook または API Azure Function を作成する](../azure-functions/functions-create-a-web-hook-or-api-function.md)」を参照してください。 

## <a name="view-cost-by-resource"></a>リソースごとのコストを表示する 
ラボの月間コスト傾向機能では、現在のカレンダー月に費やした金額を表示できます。 また、過去 7 日間の支出に基づいて、月末までの支出の予測も表示されます。 早い段階でラボでの支出がしきい値に達する理由を理解するには、**リソースごとの**過去 1 か月間のコストをテーブルに表示する、**リソースごとのコスト**機能を使用します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
3. ラボの一覧で目的のラボを選択します。  
4. 左側のメニューで **[構成とポリシー]** を選択します。
5. 左側のメニューの **[コスト管理]** セクションで、 **[リソースごとのコスト]** を選択します。 ラボに関連付けられている各リソースに関連するコストが表示されます。 

    ![リソースごとのコスト](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

この機能では、ラボの支出を削減する措置を講じるために、最もコストのかかるリソースを簡単に特定することができます。 たとえば、VM のコストは VM のサイズに基づいています。 VM のサイズが大きいほど、コストが大きくなります。 VM のサイズと所有者を簡単に確認できるので、VM 所有者に問い合わせて、このような VM サイズが必要な理由と、サイズを小さくできるかどうかを確認することができます。

[自動シャットダウン ポリシー](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy)は、特定の時間帯にラボの VM をシャットダウンしてコストを削減するのに役立ちます。 しかし、ラボ ユーザーはシャットダウン ポリシーを適用外にしていることがあり、この場合、VM の実行コストが増加します。 テーブルで VM を選択すると、自動シャットダウン ポリシーの適用外になっているかどうかを確認できます。 適用外になっている場合は、VM 所有者に問い合わせて、VM がポリシーの適用外になっている理由を確認することができます。
 
## <a name="next-steps"></a>次の手順
次に試す操作をいくつか以下に示します。

* [ラボ ポリシーを定義する](devtest-lab-set-lab-policy.md) - ラボとその VM の使用方法を制御するために使用されるさまざまなポリシーを設定する方法を確認します。 
* [カスタム イメージを作成する](devtest-lab-create-template.md) - VM を作成する場合は、ベースとしてカスタム イメージまたは Marketplace イメージを指定します。 この記事では、VHD ファイルからカスタム イメージを作成する方法について説明します。
* [Marketplace イメージを構成する](devtest-lab-configure-marketplace-images.md) - DevTest Labs では、Azure Marketplace イメージを基にした VM を作成することができます。 この記事では、ラボで VM を作成する際に使用できるようにする Azure Marketplace イメージ (該当するものがある場合) を指定する方法について説明します。
* [ラボで VM を作成する](devtest-lab-add-vm.md) - 基本イメージ (カスタムまたは Marketplace) から VM を作成する方法と、VM でアーティファクトを操作する方法について説明します。

