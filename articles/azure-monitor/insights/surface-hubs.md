---
title: Azure Monitor で Surface Hub を監視する | Microsoft Docs
description: Surface Hub ソリューションを使用して、Surface Hub の正常性を追跡し、Surface Hub がどのように使用されているかを理解します。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2018
ms.openlocfilehash: 9d81af064b18ca8113599b460f2b9524c7288439
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711078"
---
# <a name="monitor-surface-hubs-with-azure-monitor-to-track-their-health"></a>Azure Monitor で Surface Hub を監視して正常性を追跡する

![Surface Hub シンボル](./media/surface-hubs/surface-hub-symbol.png)

この記事では、Azure Monitor の Surface Hub ソリューションを使用して、Microsoft Surface Hub デバイスを監視する方法について説明します。 そのソリューションは、Surface Hub の正常性を追跡し、それらがどのように使用されているかを理解するのに役立ちます。

各 Surface Hub には、Microsoft Monitoring Agent がインストールされます。 そのエージェントを通して、Surface Hub から Azure Monitor 内の Log Analytics ワークスペースにデータを送信できます。 ログ ファイルは、Surface Hub から読み取られた後、Azure Monitor に送信されます。 オフラインになっているサーバー、同期していない予定表、デバイス アカウントで Skype にログインできないなどの問題が、Azure Monitor の Surface Hub ダッシュボードに表示されます。 ダッシュボードに表示されるデータを利用して、実行されていないデバイスや問題が発生しているデバイスを識別し、検出された問題に対して修正プログラムを適用できる可能性があります。

## <a name="install-and-configure-the-solution"></a>ソリューションのインストールと構成
次の情報を使用して、ソリューションをインストールおよび構成します。 Azure Monitor で Surface Hub を管理するには、次のものが必要です。

* 監視する台数のデバイスをサポートする [Log Analytics サブスクリプション](https://azure.microsoft.com/pricing/details/log-analytics/) レベル。 Log Analytics の価格は、登録されるデバイスの台数とデータの処理量によって決まります。 Surface Hub の展開を計画する際は、この点を考慮してください。

次に、既存の Log Analytics ワークスペースを追加するか、新しいワークスペースを作成します。 いずれの方法の使用に関する詳細も、「[Create a Log Analytics workspace in the Azure portal](../logs/quick-create-workspace.md)」(Azure portal で Log Analytics ワークスペースを作成する) に記載されています。 Log Analytics ワークスペースを構成した後、Surface Hub デバイスを登録するには 2 つの方法があります。

* Intune を通して自動で
* Surface Hub デバイスの **[設定]** を通して手動で

## <a name="set-up-monitoring"></a>監視を設定する
Azure Monitor を使用して、Surface Hub の正常性とアクティビティを監視できます。 Surface Hub の登録は、Intune を使うか、Surface Hub で **[設定]** を使ってローカルに実行できます。

## <a name="connect-surface-hubs-to-azure-monitor-through-intune"></a>Intune を使用して Surface Hub を Azure Monitor に接続する
Surface Hub を管理する Log Analytics ワークスペースのワークスペース ID とワークスペース キーが必要です。 これらは、Azure Portal でのワークスペースの設定から取得できます。

Intune は、1 つまたは複数のデバイスに適用される Log Analytics ワークスペースの構成設定を一元管理できる Microsoft 製品です。 Intune を通してデバイスを構成するには、次の手順に従います。

1. Intune にサインインします。
2. **[設定]**  >  **[接続されたソース]** の順に移動します。
3. Surface Hub テンプレートに基づいてポリシーを作成するか編集します。
4. ポリシーの [Azure Operational Insights] セクションに移動し、Log Analytics の "*ワークスペース ID*" と "*ワークスペース キー*" をポリシーに追加します。
5. ポリシーを保存します。
6. ポリシーを適切なデバイス グループに関連付けます。

   ![Intune ポリシー](./media/surface-hubs/intune.png)

その後、Intune によって、Log Analytics の設定がターゲット グループ内のデバイスと同期され、デバイスが Log Analytics ワークスペースに登録されます。

## <a name="connect-surface-hubs-to-azure-monitor-using-the-settings-app"></a>設定アプリを使用して Surface Hub を Azure Monitor に接続する
Surface Hub を管理する Log Analytics ワークスペースのワークスペース ID とワークスペース キーが必要です。 これらは、Azure Portal での Log Analytics ワークスペースに対する設定から取得できます。

環境の管理に Intune を使用しない場合は、各 Surface Hub の **[設定]** を通して手動でデバイスを登録できます。

1. Surface Hub から **[設定]** を開きます。
2. 情報を求めるメッセージが表示されたら、デバイス管理者の資格情報を入力します。
3. **[This device (このデバイス)]** をクリックし、 **[監視]** の下の **[Log Analytics 設定の構成]** をクリックします。
4. **[監視を有効にする]** を選択します。
5. [Log Analytics の設定] ダイアログで、Log Analytics の **[ワークスペース ID]** と **[ワークスペース キー]** を入力します。  
   ![Microsoft Operations Manager Suite 設定のスクリーンショット。[監視を有効にする] が選択されています。[ワークスペース ID] と [ワークスペース キー] にテキスト ボックスがあります。](./media/surface-hubs/settings.png)
6. **[OK]** をクリックして、構成を完了します。

デバイスに構成が正常に適用されたかどうかを示す確認メッセージが表示されます。 成功した場合は、エージェントが Azure Monitor に正常に接続されたことを示すメッセージが表示されます。 その後、デバイスでは Azure Monitor へのデータ送信が開始されます。Azure Monitor でデータを表示し、対応することができます。

## <a name="monitor-surface-hubs"></a>Surface Hub を監視する
Azure Monitor を使用した Surface Hub の監視は、その他の登録済みデバイスの監視と似ています。

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Surface Hub のタイルをクリックすると、デバイスの正常性が表示されます。

   ![Surface Hub ダッシュボード](./media/surface-hubs/surface-hub-dashboard.png)

既存またはカスタムのログ検索に基づく[アラート](../alerts/alerts-overview.md)を作成できます。 Azure Monitor によって Surface Hub から収集されたデータを使用して、ユーザーがデバイスに対して定義した条件に該当する問題とアラートを検索できます。

## <a name="next-steps"></a>次のステップ
* [Azure Monitor でログ クエリ](../logs/log-query-overview.md)を使用して、Surface Hub の詳細なデータを表示します。
* Surface Hub で問題が発生した場合に通知する[アラート](../alerts/alerts-overview.md)を作成します。
