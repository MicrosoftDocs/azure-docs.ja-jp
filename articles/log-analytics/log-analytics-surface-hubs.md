---
title: "Azure Log Analytics を使用した Surface Hub の監視 | Microsoft Azure"
description: "Surface Hub ソリューションを使用して、Surface Hub の正常性を追跡し、Surface Hub がどのように使用されているかを理解します。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 8b4e56bc-2d4f-4648-a236-16e9e732ebef
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a0c8af30fbed064001c3fd393bf0440aa1cb2835
ms.openlocfilehash: d568c52a7cbbe593658fb95203bfa98af13a1554
ms.lasthandoff: 02/28/2017


---
# <a name="monitor-surface-hubs-with-log-analytics-to-track-their-health"></a>Log Analytics を使用して Surface Hub を監視し、その正常性を追跡する

この記事では、Log Analytics の Surface Hub ソリューションを使用して、Microsoft Operations Management Suite (OMS) で Microsoft Surface Hub デバイスを監視する方法について説明します。 Log Analytics は、Surface Hub の正常性を追跡するとともに、それらがどのように使用されているかを理解するために役立ちます。

各 Surface Hub には、Microsoft Monitoring Agent がインストールされます。 そのエージェントを通して、Surface Hub から OMS にデータが送信されます。 Surface Hub からログ ファイルが読み込まれた後、OMS サービスに送信されます。 サーバーのオフライン状態、予定表の非同期、デバイス アカウントで Skype にログインできないなどの問題が、OMS の Surface Hub ダッシュボードに表示されます。 ダッシュボードに表示されるデータを利用して、実行されていないデバイスや問題が発生しているデバイスを識別し、検出された問題に対して修正プログラムを適用できる可能性があります。

## <a name="installing-and-configuring-the-solution"></a>ソリューションのインストールと構成
次の情報を使用して、ソリューションをインストールおよび構成します。 Surface Hub を Microsoft Operations Management Suite (OMS) から管理するためには、次のものが必要です。

* [OMS](http://www.microsoft.com/oms) の有効なサブスクリプション。
* 監視する台数のデバイスをサポートする [OMS サブスクリプション](https://azure.microsoft.com/pricing/details/log-analytics/) レベル。 OMS の価格は、登録されるデバイスの台数とデータの処理量によって決まります。 Surface Hub の展開を計画する際は、この点を考慮してください。

次に、OMS サブスクリプションを既存の Microsoft Azure サブスクリプションに追加するか、OMS ポータルから新しいワークスペースを直接作成します。 これらの手順の詳細については、「[Log Analytics の起動と開始](log-analytics-get-started.md)」を参照してください。 OMS サブスクリプションを設定した後、2 つの方法で Surface Hub デバイスを登録できます。

* InTune を通して自動で
* Surface Hub デバイスの **[設定]** を通して手動で

## <a name="set-up-monitoring"></a>監視を設定する
OMS で Log Analytics を使用して、Surface Hub の正常性とアクティビティを監視できます。 Surface Hub の OMS への登録は、InTune を使用するか、Surface Hub で **[設定]** を使用してローカルに実行できます。

## <a name="connect-surface-hubs-to-oms-through-intune"></a>InTune 経由で Surface Hub を OMS に接続する
Surface Hub を管理する OMS ワークスペースのワークスペース ID とワークスペース キーが必要です。 これらは、OMS ポータルから取得できます。

InTune は、1 つまたは複数のデバイスに適用される OMS 構成設定の一元管理を可能にする Microsoft 製品です。 InTune を通してデバイスを構成するには、次の手順に従います。

1. Azure にサインインします。
2. **[設定]** > **[接続されたソース]** の順に移動します。
3. Surface Hub テンプレートに基づいてポリシーを作成するか編集します。
4. ポリシーの OMS (Azure Operational Insights) セクションに移動し、"*ワークスペース ID*" と "*ワークスペース キー*" をポリシーに追加します。
5. ポリシーを保存します。
6. ポリシーを適切なデバイス グループに関連付けます。

   ![InTune ポリシー](./media/log-analytics-surface-hubs/intune.png)

その後、InTune によって、OMS の設定がターゲット グループ内のデバイスと同期され、デバイスが OMS ワークスペースに登録されます。

## <a name="connect-surface-hubs-to-oms-using-the-settings-app"></a>設定を通して Surface Hub を OMS に接続する
Surface Hub を管理する OMS ワークスペースのワークスペース ID とワークスペース キーが必要です。 これらは、OMS ポータルから取得できます。

環境の管理に InTune を使用しない場合は、各 Surface Hub の **[設定]** を通して手動でデバイスを登録できます。

1. Surface Hub から **[設定]** を開きます。
2. 情報を求めるメッセージが表示されたら、デバイス管理者の資格情報を入力します。
3. **[This device (このデバイス)]** をクリックし、**[監視]** の下の **[OMS 設定の構成]** をクリックします。
4. **[監視を有効にする]** を選択します。
5. [OMS の設定] ダイアログで、**[ワークスペース ID]** と **[ワークスペース キー]** を入力します。  
   ![設定](./media/log-analytics-surface-hubs/settings.png)
6. **[OK]** をクリックして、構成を完了します。

デバイスに OMS 構成が正常に適用されたかどうかを示す確認メッセージが表示されます。 成功した場合は、エージェントが OMS サービスに正常に接続されたことを示すメッセージが表示されます。 その後、デバイスが OMS へのデータの送信を開始します。OMS に表示されるデータを確認し、それに基づいてアクションをとることができます。

## <a name="monitor-surface-hubs"></a>Surface Hub を監視する
OMS を使用した Surface Hub の監視は、その他の登録済みデバイスの監視と似ています。

1. OMS ポータルにサインインします。
2. Surface Hub ソリューション パック ダッシュボードに移動します。
3. デバイスの正常性が表示されます。

   ![Surface Hub ダッシュボード](./media/log-analytics-surface-hubs/surface-hub-dashboard.png)

既存またはカスタムのログ検索に基づく[アラート](log-analytics-alerts.md)を作成できます。 OMS が Surface Hub から収集するデータを使用して、デバイス用に定義した条件に該当する問題とアラートを検索できます。

## <a name="next-steps"></a>次のステップ
* [Log Analytics のログ検索](log-analytics-log-searches.md)を使用して、Surface Hub の詳細データを表示します。
* Surface Hub で問題が発生した場合に通知する[アラート](log-analytics-alerts.md)を作成します。

