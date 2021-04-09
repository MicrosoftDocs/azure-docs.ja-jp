---
title: Azure での自動スケールの使用
description: Azure でリソースの Web アプリ、クラウド サービス、仮想マシン、仮想マシン スケール セットをスケーリングする方法について説明します。
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 081e06fbd6506c76a068d7207e29efb66502a9dd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101726055"
---
# <a name="get-started-with-autoscale-in-azure"></a>Azure での自動スケールの使用
この記事では、Microsoft Azure Portal でリソースの自動スケール設定をセットアップする方法について説明します。

Azure Monitor の自動スケーリングは、[Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/)、[Cloud Services](https://azure.microsoft.com/services/cloud-services/)、[App Service - Web Apps](https://azure.microsoft.com/services/app-service/web/)、および [API Management サービス](../../api-management/api-management-key-concepts.md)にのみ適用されます。

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>サブスクリプションの自動スケール設定の表示

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4u7ts]

Azure Monitor で自動スケールを適用できるすべてのリソースを確認できます。 詳しい手順については、次の手順を参照してください。

1. [Azure portal][1] を開きます。
1. 左側のウィンドウにある Azure Monitor アイコンをクリックします。
  ![Azure Monitor][2] を開きます。
1. **自動スケール** をクリックすると、現在の自動スケールの状態と共に、自動スケールが適用できるすべてのリソースが表示されます。
  ![Azure Monitor][3] での自動スケールの表示

上部のフィルター ウィンドウを使用すると、特定のリソース グループ、特定のリソースの種類、または特定のリソースに絞り込んでリソースを選択できます。

各リソースについて、現在のインスタンス数と自動スケールの状態を確認できます。 自動スケールの状態は次のいずれかになります。

- **未構成**: このリソースの自動スケール設定はまだ有効になっていません。
- **有効**: このリソースの自動スケール設定は有効になっています。
- **Disabled**: このリソースの自動スケール設定は無効になっています。

## <a name="create-your-first-autoscale-setting"></a>最初の自動スケール設定を作成する

簡単な手順に従って、最初の自動スケール設定を作成します。

1. Azure Monitor で **[自動スケール]** ブレードを開き、スケールするリソースを選択します (以下の手順では、Web アプリに関連付けられている App Service プランを使用します。 [初めての ASP.NET Web アプリを Azure に 5 分で作成][4]できます)。
1. 現在のインスタンス数は 1 です。 **[Enable autoscale]\(自動スケールを有効にする\)** をクリックします。
  ![新しい Web アプリのスケール設定][5]
1. スケール設定の名前を指定し、 **[ルールの追加]** をクリックします。 右側にコンテキスト ペインとして表示されるスケール ルールのオプションに確認してください。 既定では、リソースの CPU 使用率が 70% を超えた場合にインスタンス数を 1 つずつ増やしてスケールするようにオプションが設定されています。 このオプションを既定値のままにし、 **[追加]** をクリックします。
  ![Web アプリのスケール設定の作成][6]
1. これで最初のスケール ルールが作成されました。 UX では、ベスト プラクティスとして、ルール内に少なくとも 1 つのスケールを含めることが推奨されています。 そのためには次を行います。

    a. **[ルールの追加]** をクリックします。

    b. **[演算子]** に **[より小さい]** を設定します。

    c. **[しきい値]** を **20** に設定します。

    d. **[操作]** の **[Decrease count by]\(カウント数減少幅\)** を設定します。

   これで、CPU 使用率に基づいてスケールアウト/スケールインするスケール設定が作成されます。
   ![CPU に基づくスケール][8]
1. **[保存]** をクリックします。

お疲れさまでした。 これで、CPU 使用率に基づいて Web アプリを自動スケールする最初のスケール設定が作成されました。

> [!NOTE]
> 仮想マシン スケール セットまたはクラウド サービス ロールを使用する場合も同じ手順が適用されます。

## <a name="other-considerations"></a>その他の考慮事項
### <a name="scale-based-on-a-schedule"></a>スケジュールに基づくスケール変更
CPU 使用率に基づいてスケールするだけでなく、特定の曜日に異なる方法でスケールを設定することもできます。

1. **[Add a scale condition]\(スケール条件の追加\)** をクリックします。
1. スケール モードとルールの設定は既定の条件と同じです。
1. スケジュール、 **[Repeat specific days]\(特定の曜日に繰り返す\)** を選択します。
1. 曜日、スケール条件を適用する開始時刻と終了時刻を選択します。

![スケジュールに基づくスケール条件][9]
### <a name="scale-differently-on-specific-dates"></a>特定の日に異なる方法でスケールする
CPU 使用率に基づいてだけでなく、特定の曜日に異なる方法でスケールを設定することもできます。

1. **[Add a scale condition]\(スケール条件の追加\)** をクリックします。
1. スケール モードとルールの設定は既定の条件と同じです。
1. スケジュールで、 **[Specify start/end dates]\(開始日/終了日を指定する\)** を選択します。
1. スケール条件を適用する開始時刻と終了時刻を選択します。

![日付に基づくスケール条件][10]

### <a name="view-the-scale-history-of-your-resource"></a>リソースのスケール履歴を表示する
リソースをスケールアップ/スケールダウンするたびに、アクティビティ ログにイベントが記録されます。 **[実行履歴]** タブに切り替えることで、過去 24 時間のリソースのスケール履歴を表示できます。

![実行履歴][11]

すべてのスケール履歴 (最大 90 日間) を表示する場合は、 **[Click here to see more details]\(詳細を表示するには、ここをクリックしてください\)** をクリックします。 リソースやカテゴリに自動スケールがあらかじめ選択されたアクティビティ ログが表示されます。

### <a name="view-the-scale-definition-of-your-resource"></a>リソースのスケール定義を表示する
自動スケールは、Azure Resource Manager のリソースです。 **[JSON]** タブに切り替えると、JSON でスケール定義を表示できます。

![スケール定義][12]

必要に応じて、JSON に直接変更を加えることができます。 これらの変更は保存後反映されます。

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>自動スケールを無効にしてインスタンスを手動でスケールする
現在のスケール設定を無効にし、リソースを手動でスケールすることが必要な場合があります。

上部にある **[自動スケールの無効化]** ボタンをクリックします。
![自動スケールの無効化][13]

> [!NOTE]
> このオプションでは、構成が無効にます。 ただし、自動スケールをもう一度有効にした後元に戻せます。

自動スケールを無効にすると、手動でスケールするインスタンス数を設定できるようになります。

![手動スケールの設定][14]

**[Enable autoscale]\(自動スケールを有効にする\)** をクリックし、 **[保存]** をクリックすることで、いつでも自動スケールに戻すことができます。

## <a name="route-traffic-to-healthy-instances-app-service"></a>正常なインスタンスにトラフィックをルーティングする (App Service)

<a id="health-check-path"></a>

Azure Web アプリを複数のインスタンスにスケールアウトする場合、App Service を使用すれば、ご利用のインスタンスに対して正常性チェックを実行して、正常なインスタンスにのみトラフィックをルーティングすることができます。 詳細については、[この記事の Azure App Service Health チェック](../../app-service/monitor-instances-health-check.md)に関する項を参照してください。

## <a name="moving-autoscale-to-a-different-region"></a>自動スケーリングの別のリージョンへの移動
このセクションでは、Azure の自動スケーリングを同じサブスクリプションおよびリソース グループの別のリージョンに移動する方法について説明します。 REST API を使って自動スケーリング設定を移動できます。
### <a name="prerequisite"></a>前提条件
1. サブスクリプションとリソース グループが使用可能であり、移動元と移動先のリージョンの両方で詳細が同じであることを確認します。
1. [移動先の Azure リージョン](https://azure.microsoft.com/global-infrastructure/services/?products=monitor&regions=all)で Azure の自動スケーリングが使用可能であることを確認します。

### <a name="move"></a>詳細ビュー
新しい環境で自動スケーリング設定を作成するには、[REST API](/rest/api/monitor/autoscalesettings/createorupdate) を使います。 移動先のリージョンで作成される自動スケーリング設定は、移動元リージョンの自動スケーリング設定のコピーになります。

移動元リージョンの自動スケーリング設定と関連付けて作成された[診断設定](../essentials/diagnostic-settings.md)は移動できません。 自動スケーリング設定の作成が完了した後で、移行先リージョンで診断設定を再作成する必要があります。 

### <a name="learn-more-about-moving-resources-across-azure-regions"></a>Azure リージョン間でのリソースの移動に関する詳細情報
リージョン間でのリソースの移動と Azure でのディザスター リカバリーの詳細については、「[リソースを新しいリソース グループまたはサブスクリプションに移動する](../../azure-resource-manager/management/move-resource-group-and-subscription.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
- [アクティビティ ログ アラートを作成して、サブスクリプションで自動スケールのエンジン操作をすべて監視する](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [アクティビティ ログ アラートを作成して、サブスクリプションで失敗した自動スケールのスケールイン/スケールアウト操作をすべて監視する](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/autoscale-get-started/azure-monitor-launch.png
[3]: ./media/autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: ../../app-service/quickstart-dotnetcore.md
[5]: ./media/autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/autoscale-get-started/scale-in-recommendation.png
[8]: ./media/autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/autoscale-get-started/scale-condition-schedule.png
[10]: ./media/autoscale-get-started/scale-condition-dates.png
[11]: ./media/autoscale-get-started/scale-history.png
[12]: ./media/autoscale-get-started/scale-definition-json.png
[13]: ./media/autoscale-get-started/disable-autoscale.png
[14]: ./media/autoscale-get-started/set-manualscale.png