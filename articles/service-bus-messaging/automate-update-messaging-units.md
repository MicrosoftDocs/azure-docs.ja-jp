---
title: Azure Service Bus - メッセージング ユニットを自動的に更新する
description: この記事では、Service Bus 名前空間のメッセージング ユニットを自動的に更新する方法について説明します。
ms.topic: how-to
ms.date: 03/03/2021
ms.openlocfilehash: 7fc3aca82b8f01d70dec4fc2dac7842895417ec9
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102177957"
---
# <a name="automatically-update-messaging-units-of-an-azure-service-bus-namespace"></a>Azure Service Bus 名前空間のメッセージング ユニットを自動的に更新する 
自動スケールを使用すると、適切な量のリソースを実行して、アプリケーションに対する負荷を処理することができます。 リソースを追加して負荷の増加に対処したり、アイドル状態のリソースを削除して経費を節約したりできます。 Azure Monitor の自動スケーリング機能の詳細については、「[Microsoft Azure の自動スケーリングの概要](../azure-monitor/autoscale/autoscale-overview.md)」を参照してください。 

Service Bus Premium メッセージングでは、各顧客のワークロードが分離した状態で実行されるように、CPU とメモリのレベルでリソースが分離されます。 このリソースのコンテナーを、**メッセージング ユニット** と呼びます。 メッセージング ユニットの詳細については、[Service Bus Premium メッセージング](service-bus-premium-messaging.md)に関するページを参照してください。 

Service Bus Premium 名前空間の自動スケーリング機能を使用して、[メッセージング ユニット](service-bus-premium-messaging.md)の最小および最大数を指定し、一連のルールに基づいてメッセージング ユニットを自動的に追加または削除することができます。 

たとえば、自動スケーリング機能を使用して、Service Bus 名前空間の次のスケーリング シナリオを実装できます。 

- 名前空間の CPU 使用率が 75% を超えたときに Service Bus 名前空間のメッセージング ユニットを増やします。 
- 名前空間の CPU 使用率が 25% 未満になったときに Service Bus 名前空間のメッセージング ユニットを減らします。 
- 営業時間中により多くのメッセージング ユニットを使用し、業務時間外は少なくします。 

この記事では、Azure portal で Service Bus 名前空間を自動的にスケーリングする ([メッセージング ユニット](service-bus-premium-messaging.md)を更新する) 方法について説明します。 

> [!IMPORTANT]
> この記事は、Azure Service Bus の **Premium** レベルのみを対象としています。 

## <a name="autoscale-setting-page"></a>自動スケーリング設定ページ
まず、次の手順に従って、Service Bus 名前空間の **[Autoscale settings]\(自動スケーリング設定\)** ページに移動します。

1. [Azure Portal](https://portal.azure.com) にサインインします。 
2. 検索バーに「**Service Bus**」と入力し、ドロップダウン リストから **[Service Bus]** を選択して、**ENTER** キーを押します。 
1. 名前空間の一覧から **Premium 名前空間** を選択します。 
1. **[Scale]\(スケール\)** ページに移動します。 

    :::image type="content" source="./media/automate-update-messaging-units/scale-page.png" alt-text="Service Bus 名前空間 - [Scale]\(スケール\) ページ":::

## <a name="manual-scale"></a>手動でのスケーリング 
この設定を使用すると、名前空間に固定された数のメッセージング ユニットを設定できます。 

1. **[Autoscale setting]\(自動スケーリング設定\)** ページで、 **[Manual scale]\(手動スケール\)** を選択します (まだ選択されていない場合)。 
1. **[Messaging units]\(メッセージング ユニット\)** 設定で、ドロップダウン リストからメッセージ ユニット数を選択します。
1. ツール バーの **[保存]** を選択して設定を保存します。 

    :::image type="content" source="./media/automate-update-messaging-units/manual-scale.png" alt-text="メッセージング ユニットの手動スケール":::       


## <a name="custom-autoscale---default-condition"></a>カスタム自動スケーリング - 既定の条件
メッセージング ユニットの自動スケーリングは、条件を使用して構成できます。 このスケーリング条件は、その他のスケーリング条件のいずれも一致しないときに実行されます。 既定の条件は、次のいずれかの方法で設定できます。

- メトリック (CPU やメモリ使用量など) に基づいてスケーリングする
- メッセージング ユニットの特定の数に合わせてスケーリングする

既定の条件について、特定の曜日または日付範囲に自動スケーリングするようスケジュールを設定することはできません。 このスケーリング条件は、スケジュールが設定されているその他のスケーリング条件のいずれも一致しないときに実行されます。 

### <a name="scale-based-on-a-metric"></a>メトリックに基づいてスケーリングする
次の手順では、CPU 使用率が 75% を超えた場合にメッセージング ユニットを自動的に増加させ (スケールアウト)、CPU 使用率が 25% 未満の場合にメッセージング ユニットを減少させる (スケールイン) 条件を追加する方法を示します。 増分は、1 から 2、2 から 4、4 から 8、および 8 から 16 になるように行われます。 同様に、減分は 16 から 8、8 から 4、4 から 2、および 2 から 1 になるように行われます。 

1. **[Autoscale setting]\(自動スケーリング設定\)** ページで、 **[Choose how to scale your resource]\(リソースをスケールする方法を選択します\)** オプションに **[Custom auto scale]\(カスタム自動スケーリング\)** を選択します。 
1. ページの **[Default]\(既定\)** セクションで、既定の条件として **[名前]** を指定します。 **鉛筆** アイコンをクリックしてテキストを編集します。 
1. **[スケール モード]** には、 **[メトリックに基づいてスケーリングする]** を選択します。 
1. **[+ ルールの追加]** を選択します。 

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-metric-add-rule-link.png" alt-text="既定 - メトリックに基づいてスケーリングする":::    
1. **[スケール ルール]** ページで、次の手順を行います。
    1. **[メトリック名]** ドロップダウン リストからメトリックを選択します。 この例では **[CPU]** です。 
    1. 演算子としきい値を選択します。 この例では、これらは **[より大きい]** および **[Metric threshold to trigger scale action]\(スケール操作をトリガーするメトリックのしきい値\)** の **[75]** です。 
    1. **[アクション]** セクションで **操作** を選択します。 この例では、 **[Increase]\(増加\)** に設定されています。 
    1. 次に、 **[Add]\(追加\)** を選択します
    
        :::image type="content" source="./media/automate-update-messaging-units/scale-rule-cpu-75.png" alt-text="既定 - CPU 使用率が 75% を超えた場合にスケールアウト":::       

        > [!NOTE]
        > この例では、全体的な CPU 使用率が 75% を超えた場合、自動スケーリング機能によって名前空間のメッセージング ユニットが増加します。 増分は、1 から 2、2 から 4、4 から 8、および 8 から 16 になるように行われます。 
1. **[+ Add a rule]\(+ ルールの追加\)** を再度選択し、 **[スケール ルール]** ページで次の手順を実行します。
    1. **[メトリック名]** ドロップダウン リストからメトリックを選択します。 この例では **[CPU]** です。 
    1. 演算子としきい値を選択します。 この例では、これらは **[より小さい]** および **[Metric threshold to trigger scale action]\(スケール操作をトリガーするメトリックのしきい値\)** の **[25]** です。 
    1. **[アクション]** セクションで **操作** を選択します。 この例では、 **[Decrease]\(減少\)** に設定されています。 
    1. 次に、 **[Add]\(追加\)** を選択します 

        :::image type="content" source="./media/automate-update-messaging-units/scale-rule-cpu-25.png" alt-text="既定 - CPU 使用率が 25% 未満の場合にスケールイン":::       

        > [!NOTE]
        > この例では、全体的な CPU 使用率が 25% 未満の場合、自動スケーリング機能によって名前空間のメッセージング ユニットが減少します。 減分は、16 から 8、8 から 4、4 から 2、および 2 から 1 になるように行われます。 
1. **[minimum]\(最小\)** 、 **[maximum]\(最大\)** 、 **[default]\(既定\)** のメッセージング ユニット数を設定します。

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-metric-based.png" alt-text="メトリックに基づく既定のルール":::
1. ツール バーの **[保存]** を選択して自動スケーリング設定を保存します。 
        
### <a name="scale-to-specific-number-of-messaging-units"></a>メッセージング ユニットの特定の数に合わせてスケーリングする
特定の数のメッセージング ユニットを使用するように名前空間をスケーリングするルールを構成するには、次の手順に従います。 ここでも、既定の条件は、その他のスケーリング条件のいずれも一致しないときに適用されます。 

1. **[Autoscale setting]\(自動スケーリング設定\)** ページで、 **[Choose how to scale your resource]\(リソースをスケールする方法を選択します\)** オプションに **[Custom auto scale]\(カスタム自動スケーリング\)** を選択します。 
1. ページの **[Default]\(既定\)** セクションで、既定の条件として **[名前]** を指定します。 
1. **[スケール モード]** で、 **[Scale to specific messaging units]\(特定のメッセージング ユニットに合わせてスケーリングする\)** を選択します。 
1. **[メッセージング ユニット]** については、既定のメッセージング ユニットの数を選択します。 

    :::image type="content" source="./media/automate-update-messaging-units/default-scale-messaging-units.png" alt-text="既定 - 特定のメッセージング ユニットに合わせてスケーリングする":::       

## <a name="custom-autoscale---additional-conditions"></a>カスタム自動スケーリング - 追加条件
前のセクションでは、自動スケーリング設定のための既定の条件を追加する方法について説明しました。 このセクションでは、自動スケーリング設定に条件を追加する方法について説明します。 これらの既定以外の追加条件では、特定の曜日または日付範囲に基づいてスケジュールを設定できます。 

### <a name="scale-based-on-a-metric"></a>メトリックに基づいてスケーリングする
1. **[Autoscale setting]\(自動スケーリング設定\)** ページで、 **[Choose how to scale your resource]\(リソースをスケールする方法を選択します\)** オプションに **[Custom auto scale]\(カスタム自動スケーリング\)** を選択します。 
1. **[Default]\(既定\)** ブロックの下で **[Add a scale condition]\(スケーリング条件を追加する\)** を選択します。 

    :::image type="content" source="./media/automate-update-messaging-units/add-scale-condition-link.png" alt-text="カスタム - [スケーリング条件を追加する] リンク":::    
1. 条件の **名前** を指定します。 
1. **[メトリックに基づいてスケーリングする]** オプションが選択されていることを確認します。 
1. **[+ Add a rule]\(+ ルールの追加\)** を選択して、CPU の全体的な使用率が 75% を超えたときにメッセージング ユニットを増やすルールを追加します。 [既定の条件](#custom-autoscale---default-condition)に関するセクションの手順に従います。 
5. **[minimum]\(最小\)** 、 **[maximum]\(最大\)** 、 **[default]\(既定\)** のメッセージング ユニット数を設定します。
6. また、カスタム条件に対して **スケジュール** を設定することもできます (既定の条件は対象外)。 条件の開始日と終了日を指定するか、または週の特定の曜日 (月曜日、火曜日など) を選択することができます。 
    1. **[Specify start/end dates]\(開始日/終了日の指定\)** を選択した場合、条件が有効になる **[タイムゾーン]** 、 **[Start date]\(開始日\) と時刻**、 **[End date]\(終了日\) と時刻** を (以下のイメージに表示されているように) 選択します。 

       :::image type="content" source="./media/automate-update-messaging-units/custom-min-max-default.png" alt-text="メッセージング ユニットの数の最小値、最大値、既定値":::
    1. **[Repeat specific days]\(特定の曜日に繰り返す\)** を選択した場合、条件を適用する曜日、タイムゾーン、開始時刻、終了時刻を選択します。 

        :::image type="content" source="./media/automate-update-messaging-units/repeat-specific-days.png" alt-text="特定の曜日に繰り返す":::
  
### <a name="scale-to-specific-number-of-messaging-units"></a>メッセージング ユニットの特定の数に合わせてスケーリングする
1. **[Autoscale setting]\(自動スケーリング設定\)** ページで、 **[Choose how to scale your resource]\(リソースをスケールする方法を選択します\)** オプションに **[Custom auto scale]\(カスタム自動スケーリング\)** を選択します。 
1. **[Default]\(既定\)** ブロックの下で **[Add a scale condition]\(スケーリング条件を追加する\)** を選択します。 

    :::image type="content" source="./media/automate-update-messaging-units/add-scale-condition-link.png" alt-text="カスタム - [スケーリング条件を追加する] リンク":::    
1. 条件の **名前** を指定します。 
2. **[スケール モード]** で、 **[Scale to specific messaging units]\(特定のメッセージング ユニットに合わせてスケーリングする\)** オプションを選択します。 
1. ドロップダウン リストから **メッセージ ユニット** 数を選択します。 
6. **スケジュール** については、条件の開始日と終了日を指定するか、または週の特定の曜日 (月曜日、火曜日など) および時刻を選択することができます。 
    1. **[Specify start/end dates]\(開始日/終了日の指定\)** を選択した場合、条件が有効になる **[Timezone]\(タイムゾーン\)** 、 **[Start date]\(開始日\) と時刻**、 **[End date]\(終了日\) と時刻** を選択します。 
    
    :::image type="content" source="./media/automate-update-messaging-units/scale-specific-messaging-units-start-end-dates.png" alt-text="特定のメッセージング ユニットに合わせてスケーリングする - 開始日および終了日":::        
    1. **[Repeat specific days]\(特定の曜日に繰り返す\)** を選択した場合、条件を適用する曜日、タイムゾーン、開始時刻、終了時刻を選択します。
    
    :::image type="content" source="./media/automate-update-messaging-units/repeat-specific-days-2.png" alt-text="特定のメッセージング ユニットに合わせてスケーリングする - 特定の曜日に繰り返す":::

    
    自動スケーリング設定がどのように機能するか、特にプロファイルまたは条件を選択して複数のルールを評価する方法の詳細については、「[自動スケーリング設定について](../azure-monitor/autoscale/autoscale-understanding-settings.md)」を参照してください。          

    > [!NOTE]
    > - 自動スケーリングに関する意思決定を行う際に確認するメトリックは、5 から 10 分前のものである可能性があります。 激しいワークロードを扱う場合、激しいワークロードを処理するために十分なメッセージング ユニットを確保するため、スケール アップの継続時間は短く、スケール ダウンの継続時間は長く (10 分以上) 設定します。 
    > 
    > - それでも容量不足 (使用可能なメッセージング ユニットの不足) が原因でエラーが発生する場合は、サポート チケットを発行してください。  


## <a name="next-steps"></a>次のステップ
メッセージング ユニットの詳細については、[Premium メッセージング](service-bus-premium-messaging.md)に関するページを参照してください

