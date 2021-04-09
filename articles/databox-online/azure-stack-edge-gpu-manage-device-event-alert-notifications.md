---
title: Azure Stack Edge Pro リソースのデバイス イベント アラート通知を管理する | Microsoft Docs
description: Azure portal を使用して、Azure Stack Edge Pro リソースのデバイス イベントのアラートを管理する方法について説明します。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 585343137a4a8fd8a1fb591c640e1183d71c0fd3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443099"
---
# <a name="manage-device-event-alert-notifications-on-azure-stack-edge-pro-resources"></a>Azure Stack Edge Pro リソースのデバイス イベント アラート通知を管理する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Azure portal でアクション ルールを作成し、リソース グループ、Azure サブスクリプション、または個々の Azure Stack Edge リソース内で発生するデバイス イベントのアラート通知をトリガーまたは非表示にする方法について説明します。 この記事は、Azure Stack Edge のすべてのモデルに適用されます。  

## <a name="about-action-rules"></a>アクション ルールについて

アクション ルールを使用して、アラート通知をトリガーまたは非表示にすることができます。 アクション ルールは "*アクション グループ*" に追加されます。これは、リソースまたはリソースのセットに対して異なるコンテキストでトリガーされたアラートに対処する必要があるユーザーに通知するために使用される通知の基本設定のセットです。

アクション ルールの詳細については、「[アクション ルールの構成](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule)」を参照してください。 アクション グループの詳細については、「[Azure portal でのアクション グループの作成および管理](../azure-monitor/alerts/action-groups.md)」を参照してください。

> [!NOTE]
> アクション ルール機能はプレビュー段階です。 一部の画面と手順は、プロセスの調整に応じて変わる可能性があります。


## <a name="create-an-action-rule"></a>アクション ルールを作成する

Azure Stack Edge デバイスのアクション ルールを作成するには、Azure portal で次の手順を行います。

> [!NOTE]
> これらの手順では、アクション グループに通知を送信するアクション ルールを作成します。 通知を非表示にするアクション ルールの作成の詳細については、「[アクション ルールの構成](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule)」を参照してください。

1. Azure portal で Azure Stack Edge デバイスに移動してから、 **[監視] > [アラート]** の順に移動します。 **[アクションの管理]** を選択します。

   ![[監視]、[アラート]、[アクションの管理] ビュー](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/action-rules-open-view-01.png)

2. **[アクション ルール (プレビュー)]** を選択してから、 **[+ 新しいアクション ルール]** を選びます。

   ![[アクションの管理]、アクション ルール オプション](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/action-rules-open-view-02.png)

3. **[アクション ルールを作成]** 画面で、 **[スコープ]** を使用して、Azure サブスクリプション、リソース グループ、またはターゲット リソースを選択します。 このアクション ルールにより、そのスコープ内で生成されるすべてのアラートに対処します。

   1. **[スコープ]** で **[選択]** を選びます。

      ![新しいアクション ルールのスコープを選択する](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-01.png)

   2. アクション ルールの **[サブスクリプション]** を選択し、必要に応じて、 **[リソース]** の種類でフィルター処理します。 Azure Stack Edge リソースにフィルターを適用するには、 **[Data Box Edge devices (dataBoxEdge)]\(Data Box Edge のデバイス (dataBoxEdge)\)** を選択します。

      **[リソース]** 領域には、選択内容に基づいて使用可能なリソースが一覧表示されます。
  
      ![[スコープの選択] 画面の使用可能なリソース](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-02.png)

   3. ルールを適用する各リソースのチェック ボックスをオンにします。 サブスクリプション、リソース グループ、または個々のリソースを選択できます。 **[完了]** を選択します。

      ![アクション ルール スコープのサンプル設定](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-03.png)

      **[アクション ルールを作成]** 画面には、選択されたスコープが表示されます。

      ![Azure Stack Edge アクション ルールの完了したスコープ](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-scope-04.png)

4. **[フィルター]** オプションを使用して、選択されたスコープ内のアラートのサブセットにルールの適用を絞り込みます。

   1. **[追加]** を選択して **[フィルターの追加]** ペインを開きます。

      ![アクション ルールにフィルターを追加するためのオプション](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-filter-01.png)

   2. **[フィルター]** で、適用する各フィルターを追加します。 各フィルターについて、フィルターの種類、**演算子**、および **値** を選択します。
   
      フィルター オプションの一覧については、「[フィルター条件](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#filter-criteria)」を参照してください。

      以下のサンプル フィルターは、監視サービスによって Azure Stack Edge リソースに対して発生する重大度レベル 2、3、および 4 のすべてのアラートに適用されます。

      フィルターの追加が終了したら、 **[完了]** を選択します。
   
      ![アクション ルールのサンプル フィルター](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-filter-02.png)

5. **[アクション ルールを作成]** 画面で、 **[アクション グループ]** を選択して通知を送信するルールを作成します。 その後、 **[アクション]** で **[選択]** を選びます。

   ![通知を送信するアクション ルールを作成するためのアクション グループ オプション](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-action-group-01.png)

   > [!NOTE]
   > 通知を非表示にするルールを作成するには、 **[抑制]** を選択します。 詳細については、「[アクション ルールの構成](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule)」を参照してください。

6. このアクション ルールで使用するアクション グループを選択します。 次に **[選択]** を選択します。 新しいアクション ルールは、選択されたアクション グループの通知の基本設定に追加されます。

   新しいアクション グループを作成する必要がある場合は、 **[+ アクション グループの作成]** を選択し、「[Azure Portal を使用したアクション グループの作成](../azure-monitor/alerts/action-groups.md#create-an-action-group-by-using-the-azure-portal)」の手順に従います。

   ![ルールで使用するアクション グループを選択してから、[選択] を選びます。](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-action-group-02.png)

7. 新しいアクション ルールに **名前** と **説明** を指定し、そのルールをリソース グループに割り当てます。

9. 新しいルールが既定で有効になります。 ルールの使用をすぐに開始しない場合は、 **[Enable rule update creation]\(ルールの更新の作成を有効にする\)** で **[いいえ]** を選択します。

10. 設定が終了したら、 **[作成]** を選択します。

    ![アラート通知を送信するアクション ルールの完了した設定](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-completed-settings.png)

    **[アクション ルール (プレビュー)]** 画面が開きますが、新しいアクション ルールはすぐに表示されない場合があります。 フォーカスは **すべて** のリソース グループにあります。

11. 新しいアクション ルールを表示するには、そのルールのリソース グループを選択します。

    ![新しいルールが表示されたアクション ルールの画面](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/new-action-rule-displayed.png)


## <a name="view-notifications"></a>通知の表示

アクション ルールのスコープ内にあるリソースに対して、新しいイベントによってアラートがトリガーされると、通知が送信されます。

ルールのアクション グループにより、通知を受信するユーザーと送信される通知の種類 (電子メールまたはショート メッセージサービス (SMS) メッセージ、あるいはその両方) が設定されます。

アラートがトリガーされた後、通知を受信するまで数分かかる場合があります。

電子メール通知はこのようになります。

![アクション ルールの電子メール通知のサンプル](media/azure-stack-edge-gpu-manage-device-event-alert-notifications/sample-action-rule-email-notification.png)


## <a name="next-steps"></a>次のステップ

<!-- - See [Create and manage action groups in the Azure portal](../azure-monitor/alerts/action-groups.md) for guidance on creating a new action group.
- See [Configure an action rule](../azure-monitor/alerts/alerts-action-rules.md?tabs=portal#configuring-an-action-rule) for more info about creating action rules that send or suppress alert notifications. -2 bullets referenced above. Making room for local tasks in "Next Steps." --> 
- デバイス イベント、ハードウェアの状態、およびメトリック グラフの確認に関する情報については、「[Azure Stack Edge Pro を監視する](azure-stack-edge-monitor.md)」を参照してください。 
- Azure Stack Edge Pro GPU デバイス用の Azure Monitor の最適化に関する情報については、[Azure Monitor の使用](azure-stack-edge-gpu-enable-azure-monitor.md)に関するページを参照してください。
- 個々のアラートの管理に関する情報については、「[Azure Monitor を使用してメトリック アラートを作成、表示、管理する](../azure-monitor/alerts/alerts-metric.md)」を参照してください。