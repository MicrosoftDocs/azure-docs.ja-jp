---
title: Azure の請求およびコスト管理の予算シナリオ | Microsoft Docs
description: Azure Automation を使用して特定の予算しきい値に基づいて VM をシャットダウンする方法を説明します。
services: billing
documentationcenter: ''
author: bandersmsft
manager: dougeby
editor: ''
tags: billing
ms.assetid: db93f546-6b56-4b51-960d-1a5bf0274fc8
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 03/13/2019
ms.author: banders
ms.openlocfilehash: 37f129526cb184a2eeee9e36028e8f00b5bbc247
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443470"
---
# <a name="manage-costs-with-azure-budgets"></a>Azure Budgets でのコストの管理

コスト管理は、クラウドへの投資の価値を最大化するために不可欠な要素の 1 つです。 さまざまなシナリオにおいて、コストの可視性、レポート、およびコスト ベースのオーケストレーションが、継続的な事業運営に不可欠となります。 [Azure Cost Management API](https://docs.microsoft.com/rest/api/consumption/) の一連の API は、このようなシナリオのそれぞれをサポートします。 これらの API から使用状況の詳細がわかるので、インスタンス レベルのコストを細分化して調べることができます。

予算はコスト管理の一部として一般的に使用されます。 Azure では、範囲を限定して予算を管理することができます。 たとえば、予算のビューをサブスクリプション、リソース グループ、またはリソースのコレクションに基づいて絞り込むことができます。 予算 API を使用して、予算のしきい値に達したときに電子メールで通知を受け取ることに加えて、[Azure Monitor のアクション グループ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)を使用して、予算に関するイベントの発生時に一連のアクションをトリガーし、オーケストレーションの下で実行することもできます。

予算に関する一般的なシナリオの例を考えてみます。この企業は重要度の低いワークロードを実行しており、所定の予算を超えないように管理することと、毎月受け取る請求書の金額を予測可能な範囲で維持することを必要としています。 このシナリオでは、Azure 環境の一部であるリソースを、コストに基づいてオーケストレーションすることが必要になります。 このシナリオでは、サブスクリプションの毎月の予算を 1,000 ドルと設定します。 また、いくつかのオーケストレーションをトリガーするように通知のしきい値を設定します。 まず、コストしきい値を 80% に設定し、これに達したときにリソース グループ **Optional** ("必須ではない") 内のすべての VM を停止します。 次に、コストしきい値 100% に達したときに、すべての VM インスタンスを停止します。
このシナリオのとおりに構成するには、次に示すアクションを実行します。これらの手順を、このチュートリアルの各セクションで説明します。

このチュートリアルで説明するアクションでは、次のことを行います。

- Azure Automation Runbook を作成します。この Runbook で、Webhook を使用して VM を停止します。
- Azure ロジック アプリを作成します。このアプリは予算のしきい値に基づいてトリガーされ、適切なパラメーター付きで Runbook を呼び出します。
- Azure Monitor アクション グループを作成します。このグループは、予算のしきい値に達したときに Azure ロジック アプリをトリガーするように構成されます。
- Azure 予算を作成し、希望のしきい値を指定して、アクション グループに関連付けます。

## <a name="create-an-azure-automation-runbook"></a>Azure Automation Runbook を作成する

[Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) は、リソース管理タスクのほとんどをスクリプトで処理できるサービスです。このタスクはスケジュールに従って実行することも、オンデマンドで実行することもできます。 このシナリオでは、[Azure Automation Runbook](https://docs.microsoft.com/azure/automation/automation-runbook-types) を作成し、これを使用して VM を停止します。 このシナリオの構築には、[ギャラリー](https://docs.microsoft.com/azure/automation/automation-runbook-gallery)にある [Stop Azure V2 VMs](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) というグラフィカル Runbook を使用します。 この Runbook を Azure アカウントにインポートして発行すると、予算のしきい値に達したときに VM を停止できるようになります。

### <a name="create-an-azure-automation-account"></a>Azure Automation アカウントを作成する

1. Azure アカウントの資格情報で [Azure Portal](https://portal.azure.com/) にサインインします。
2. Azure の左上にある **[リソースの作成]** ボタンをクリックします。
3. **[管理ツール]**  >  **[オートメーション]** を選択します。
   > [!NOTE]
   > Azure アカウントがない場合は、[無料のアカウント](https://azure.microsoft.com/free/)を作成できます。
4. アカウント情報を入力します。 **[Azure 実行アカウントの作成]** で **[はい]** を選択します。これで、Azure に対する認証を単純化するための設定が自動的に有効になります。
5. 完了したら、 **[作成]** をクリックして、Automation アカウントの展開を開始します。

### <a name="import-the-stop-azure-v2-vms-runbook"></a>Stop Azure V2 VMs Runbook をインポートする

[Azure Automation Runbook](https://docs.microsoft.com/azure/automation/automation-runbook-types) を使用して、[Stop Azure V2 VMs](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) グラフィカル Runbook をギャラリーからインポートします。

1.  Azure アカウントの資格情報で [Azure Portal](https://portal.azure.com/) にサインインします。
2.  Automation アカウントを開くために、 **[すべてのサービス]**  >  **[Automation アカウント]** を選択します。 Automation アカウントを選択します。
3.  **[プロセス オートメーション]** セクションの **[Runbook ギャラリー]** をクリックします。
4.  **[ギャラリーのソース]** を **[スクリプト センター]** に設定し、 **[OK]** を選択します。
5.  Azure portal 内で [Stop Azure V2 VMs](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) ギャラリー アイテムを見つけて選択します。
6.  **[インポート]** ボタンをクリックして **[インポート]** ブレードを表示し、 **[OK]** を選択します。 Runbook の概要ブレードが表示されます。
7.  Runbook のインポート処理が完了したら、 **[編集]** を選択します。グラフィカル Runbook エディターと発行オプションが表示されます。

    ![Azure - グラフィカル Runbook の編集](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-01.png)
8.  **[発行]** ボタンをクリックして Runbook を発行し、確認を要求されたら **[はい]** をクリックします。 Runbook を発行すると、既存の発行済みバージョンよりもドラフト バージョンがオーバーライドされるようになります。 この例では、Runbook を作成したばかりであるため、発行済みバージョンはまだありません。

    Runbook の発行の詳細については、[グラフィカル Runbook の作成](https://docs.microsoft.com/azure/automation/automation-first-runbook-graphical)に関するページを参照してください。

## <a name="create-webhooks-for-the-runbook"></a>Runbook に対する Webhook を作成する

[Stop Azure V2 VMs](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) グラフィカル Runbook を使用して、2 つの Webhook を作成し、この Runbook を 1 つの HTTP 要求だけで Azure Automation の中で起動できるように設定します。 最初の Webhook は、80% の予算しきい値に達したときに Runbook を起動します。このときに、リソース グループ名をパラメーターとして指定します。これで、必須ではない VM を停止することができます。 2 つ目の Webhook は、Runbook をパラメーターなしで (100% のときに) 起動します。これで、残りの VM インスタンスをすべて停止します。

1. [Azure portal](https://portal.azure.com/) の **[Runbooks]** ページで、**StopAzureV2Vm** Runbook をクリックします。この Runbook の概要ブレードが表示されます。
2. ページの最上部にある **[Webhook]** をクリックして **[Webhook の追加]** ブレードを開きます。
3. **[新しい Webhook の作成]** をクリックして **[新しい Webhook の作成]** ブレードを開きます。
4. Webhook の **[名前]** を「**Optional**」に設定します。 **[有効]** プロパティは **[はい]** に設定する必要があります。 **[有効期限]** の値は変更する必要はありません。 Webhook のプロパティの詳細については、「[Webhook の詳細](https://docs.microsoft.com/azure/automation/automation-webhooks#details-of-a-webhook)」を参照してください。
5. URL の値の横にあるコピー アイコンをクリックして Webhook の URL をコピーします。
   > [!IMPORTANT]
   > **Optional** という名前の Webhook の URL を、安全な場所に保存します。 この URL は、このチュートリアルで後ほど使用します。 セキュリティ上の理由から、Webhook の作成後にその URL をもう一度表示または取得することはできないようになっています。
6. **[OK]** をクリックして新しい Webhook を作成します。
7. **[パラメータと実行設定を構成する]** をクリックします。Runbook のパラメーター値が表示されます。
   > [!NOTE]
   > Runbook に必須のパラメーターがある場合、値を指定しない限り webhook を作成できません。
8. Webhook パラメーターの値はそのままにして **[OK]** をクリックします。
9. **[作成]** をクリックして Webhook を作成します。
10. 次に、これまでと同じ手順に従って 2 つ目の Webhook を **Complete** という名前で作成します。
    > [!IMPORTANT]
    > 必ず、両方の Webhook URL を保存してください。これらは、このチュートリアルで後ほど使用します。 セキュリティ上の理由から、Webhook の作成後にその URL をもう一度表示または取得することはできないようになっています。

これで、2 つの Webhook が構成済みとなり、保存した URL を使用してそれぞれを使用できるようになりました。

![Webhook - Optional と Complete](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-02.png)

Azure Automation のセットアップが完了しました。 単純な Postman テストを使用して、Webhook が機能することを検証できます。 次に、オーケストレーションのためのロジック アプリを作成する必要があります。

## <a name="create-an-azure-logic-app-for-orchestration"></a>オーケストレーションのための Azure ロジック アプリを作成する

ロジック アプリは、プロセスをワークフローとして構築、スケジューリング、自動化するのに役立ちます。これを利用すると、複数の企業または組織にわたってアプリ、データ、システム、サービスを統合することができます。 このシナリオで作成する[ロジック アプリ](https://docs.microsoft.com/azure/logic-apps/)は、作成したオートメーション Webhook を呼び出すだけでなく、その他の処理も行います。

指定したしきい値に達したときに通知をトリガーするように、予算を設定することができます。 複数のしきい値を指定して、それぞれに達したときに通知を受け取るようにしたい場合に、ロジック アプリを利用すると、到達したしきい値に基づいて異なるアクションを実行することができます。 この例でのシナリオでは、複数の種類の通知を受け取ります。最初の通知は予算の 80% に達したとき、2 つ目の通知は予算の 100% に達したときに受け取ります。 ロジック アプリを使用して、リソース グループ内のすべての VM をシャットダウンします。 まず、**Optional** しきい値である 80% に達します。その後で、2 つ目のしきい値に達すると、サブスクリプション内のすべての VM がシャットダウンされます。

ロジック アプリでは、HTTP トリガーのためのサンプル スキーマを指定できますが、**Content-Type** ヘッダーを設定する必要があります。 アクション グループには Webhook のためのカスタム ヘッダーがないため、ペイロードの解析を別のステップで行う必要があります。 **解析**アクションを使用し、サンプル ペイロードを渡します。

### <a name="create-the-logic-app"></a>ロジック アプリの作成

このロジック アプリは、さまざまなアクションを実行します。 次のリストは、このロジック アプリが実行するアクションの概要です。
- HTTP 要求を受信したことを認識します。
- 渡された JSON データを解析し、到達したしきい値を特定します。
- 条件付きステートメントを使用して、しきい値と比較する値が予算の範囲の 80% 以上に到達し、かつ 100% 未満かどうかを調べます。
    - このしきい値に達している場合は、**Optional** という名前の Webhook を使用して HTTP POST を送信します。 このアクションは、"Optional" グループの VM をシャットダウンします。
- 条件付きステートメントを使用して、しきい値と比較する値が予算値の 100% 以上に達しているかどうかを調べます。
    - このしきい値に達している場合は、**Complete** という名前の Webhook を使用して HTTP POST を送信します。 このアクションは残りのすべての VM をシャットダウンします。

上記の手順を実行するロジック アプリを作成するには、次の手順が必要です。

1.  [Azure portal](https://portal.azure.com/) で、 **[リソースの作成]**  >  **[統合]**  >  **[Logic App]** を選択します。

    ![Azure - ロジック アプリ リソースを選択する](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-03.png)
2.  **[ロジック アプリの作成]** ブレードで、ロジック アプリを作成するための詳細情報を入力し、 **[ダッシュ ボードにピン留めする]** をクリックし、 **[作成]** をクリックします。

    ![Azure - ロジック アプリを作成する](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-03a.png)

ロジック アプリがデプロイされると、**Logic Apps デザイナー**が開き、紹介ビデオやよく使用されるトリガーがブレードに表示されます。

### <a name="add-a-trigger"></a>トリガーの追加

すべてのロジック アプリは必ずトリガーから起動され、トリガーは、特定のイベントが発生するか特定の条件が満たされたときに起動されます。 トリガーが起動するたびに、Logic Apps エンジンによって、ワークフローを開始および実行するロジック アプリ インスタンスが作成されます。 アクションとは、トリガーの後に発生するステップすべてを指します。

1.  **[Logic Apps デザイナー]** ブレードの **[テンプレート]** で、 **[空のロジック アプリ]** を選択します。
2.  [トリガー](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts)を追加するために、**Logic Apps デザイナー**の検索ボックスに「http 要求」と入力して、 **[要求 - HTTP 要求の受信時]** という名前のトリガーを見つけて選択します。

    ![Azure - ロジック アプリ - Http トリガー](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-04.png)
3.  **[新しいステップ]**  >  **[アクションの追加]** の順に選択します。

    ![Azure - 新しいステップ - アクションの追加](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-05.png)
4.  **Logic Apps デザイナー**の検索ボックスに「JSON の解析」と入力して検索し、 **[データ操作] - [JSON の解析]** [アクション](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts)を見つけて選択します。

    ![Azure - ロジック アプリ -JSON の解析アクションの追加](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-06.png)
5.  「JSON の解析」ペイロードの**コンテンツ**名として「Payload」と入力とするか、動的なコンテンツから "Body" タグを使用します。
6.  **[JSON の解析]** ボックスで **[サンプルのペイロードを使用してスキーマを生成する]** オプションを選択します。

    ![Azure - ロジック アプリ - サンプル JSON データを使用してスキーマを生成する](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-07.png)
7.  次の JSON サンプル ペイロードをテキスト ボックスに貼り付けます。`{"schemaId":"AIP Budget Notification","data":{"SubscriptionName":"CCM - Microsoft Azure Enterprise - 1","SubscriptionId":"<GUID>","SpendingAmount":"100","BudgetStartDate":"6/1/2018","Budget":"50","Unit":"USD","BudgetCreator":"email@contoso.com","BudgetName":"BudgetName","BudgetType":"Cost","ResourceGroup":"","NotificationThresholdAmount":"0.8"}}`

    テキスト ボックスは次のようになります。

    ![Azure - ロジック アプリ - JSON ペイロードのサンプル](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-08.png)
8.  **[Done]** をクリックします。

### <a name="add-the-first-conditional-action"></a>最初の条件付きアクションを追加する

条件付きステートメントを使用して、しきい値と比較する値が予算の範囲の 80% 以上に到達し、かつ 100% 未満かどうかを調べます。 このしきい値に達している場合は、**Optional** という名前の Webhook を使用して HTTP POST を送信します。 このアクションは、**Optional** グループの VM をシャットダウンします。

1.  **[新しいステップ]**  >  **[アクションの追加]** の順に選択します。

    ![Azure - ロジック アプリ - 条件を追加する](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-09.png)
2.  **[条件]** ボックスの中の、「**値の選択**」と表示されているテキスト ボックスをクリックします。選択できる値の一覧が表示されます。

    ![Azure - ロジック アプリ - [条件] ボックス](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-10.png)

3.  一覧の最上部にある **[式]** をクリックし、式エディターに `float()` という式を入力します。

    ![Azure - ロジック アプリ - 浮動小数点式](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-11.png)

4.  **[動的なコンテンツ]** を選択し、カーソルをかっこ () 内に置いて、一覧から **[NotificationThresholdAmount]** を選択して式全体を入力します。

    式は、次のようになります。<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`

5.  **[OK]** を選択して式を設定します。
6.  **[条件]** のドロップダウン ボックスで **[次の値より大きいか等しい]** を選択します。
7.  条件の **[値の選択]** ボックスに `.8` と入力します。

    ![Azure - ロジック アプリ - 浮動小数点式と値](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-12.png)

8.  [条件] ボックスの **[追加]**  >  **[行の追加]** をクリックし、追加の追加部分を追加します。
9.  **[条件]** ボックスで、 **[次の値を含む]** が含まれるテキスト ボックスをクリックします。
10. 一覧の最上部にある **[式]** をクリックし、式エディターに `float()` という式を入力します。
11. **[動的なコンテンツ]** を選択し、カーソルをかっこ () 内に置いて、一覧から **[NotificationThresholdAmount]** を選択して式全体を入力します。
12. **[OK]** を選択して式を設定します。
13. **[条件]** のドロップダウン ボックスで **[次の値より小さい]** を選択します。
14. 条件の **[値の選択]** ボックスに `1` と入力します。

    ![Azure - ロジック アプリ - 浮動小数点式と値](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-13.png)

15. **[true の場合]** ボックスで、 **[アクションの追加]** を選択します。 ここで追加するのは、必須ではない VM をシャットダウンする HTTP POST アクションです。

    ![Azure - ロジック アプリ - アクションを追加する](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-14.png)

16. HTTP アクションを検索するために「**HTTP**」と入力し、 **[HTTP - HTTP]** アクションを選択します。

    ![Azure - ロジック アプリ - HTTP アクションを追加する](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-15.png)

17. **[メソッド]** の値として **[Post]** を選択します。
18. このチュートリアルで作成した **Optional** という名前の Webhook の URL を **[URI]** の値として入力します。

    ![Azure - ロジック アプリ - HTTP アクションの URI](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-16.png)

19. **[true の場合]** ボックスで、 **[アクションの追加]** を選択します。 ここで追加するのは、必須ではない VM がシャットダウンされたことを通知する電子メールを送信する電子メール アクションです。
20. 「メールの送信」と入力して検索し、使用している電子メール サービスに基づいて *[電子メールの送信]* アクションを選択します。

    ![Azure - ロジック アプリ - メールの送信アクション](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-17.png)

    個人用 Microsoft アカウントには、**Outlook.com** を選択します。 Azure の職場または学校アカウントには、**Office 365 Outlook** を選択します。 まだ接続していない場合、お使いのメール アカウントにサインインするように求められます。 Logic Apps によって電子メール アカウントへの接続が作成されます。

    ロジック アプリに電子メール情報へのアクセスを許可する必要があります。

    ![Azure - ロジック アプリ - アクセスの注意事項](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-18.png)

21. 必須ではない VM がシャットダウンされたことを通知する電子メールの **[宛先]** 、 **[件名]** 、および **[本文]** を追加します。 動的コンテンツ **BudgetName** と **NotificationThresholdAmount** を使用して件名と本文のフィールドの内容を入力します。

    ![Azure - ロジック アプリ - 電子メールの詳細](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-19.png)

### <a name="add-the-second-conditional-action"></a>2 つ目の条件付きアクションを追加する

条件付きステートメントを使用して、しきい値と比較する値が予算値の 100% 以上に達しているかどうかを調べます。 このしきい値に達している場合は、**Complete** という名前の Webhook を使用して HTTP POST を送信します。 このアクションは残りのすべての VM をシャットダウンします。

1.  **[新しいステップ]**  >  **[条件の追加]** の順に選択します。

    ![Azure - ロジック アプリ - アクションを追加する](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-20.png)

2.  **[条件]** ボックスの中の、「**値の選択**」と表示されているテキスト ボックスをクリックします。選択できる値の一覧が表示されます。
3.  一覧の最上部にある **[式]** をクリックし、式エディターに `float()` という式を入力します。
4.  **[動的なコンテンツ]** を選択し、カーソルをかっこ () 内に置いて、一覧から **[NotificationThresholdAmount]** を選択して式全体を入力します。

    式は、次のようになります。<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`

5.  **[OK]** を選択して式を設定します。
6.  **[条件]** のドロップダウン ボックスで **[次の値より大きいか等しい]** を選択します。
7.  条件の **[値の選択]** ボックスに `1` と入力します。

    ![Azure - ロジック アプリ - 条件の値の設定](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-21.png)

8.  **[true の場合]** ボックスで、 **[アクションの追加]** を選択します。 ここで追加するのは、残りのすべての VM をシャットダウンする HTTP POST アクションです。

    ![Azure - ロジック アプリ - アクションを追加する](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-22.png)

9.  HTTP アクションを検索するために「**HTTP**」と入力し、 **[HTTP - HTTP]** アクションを選択します。
10. **[メソッド]** の値として **[Post]** を選択します。
11. このチュートリアルで作成した **Complete** という名前の Webhook の URL を **[URI]** の値として入力します。

    ![Azure - ロジック アプリ - アクションを追加する](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-23.png)

12. **[true の場合]** ボックスで、 **[アクションの追加]** を選択します。 ここで追加するのは、残りの VM がシャットダウンされたことを通知する電子メールを送信する電子メール アクションです。
13. 「メールの送信」と入力して検索し、使用している電子メール サービスに基づいて *[電子メールの送信]* アクションを選択します。
14. 必須ではない VM がシャットダウンされたことを通知する電子メールの **[宛先]** 、 **[件名]** 、および **[本文]** を追加します。 動的コンテンツ **BudgetName** と **NotificationThresholdAmount** を使用して件名と本文のフィールドの内容を入力します。

    ![Azure - ロジック アプリ - メールの送信の詳細](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-24.png)

15. **Logic Apps デザイナー** ブレードの最上部にある **[保存]** をクリックします。

### <a name="logic-app-summary"></a>ロジック アプリ - まとめ

完成したロジック アプリは次のようになります。 ごく基本的なシナリオで、しきい値に基づくオーケストレーションが不要な場合は、**Monitor** からオートメーション スクリプトを直接呼び出して、**ロジック アプリ**のステップを省略することができます。

   ![Azure - ロジック アプリ - 完成した状態](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-25.png)

ロジック アプリを保存したときに URL が生成されましたが、この URL を呼び出すことができます。 このチュートリアルの次のセクションで、この URL を使用します。

## <a name="create-an-azure-monitor-action-group"></a>Azure Monitor アクション グループを作成する

アクション グループとは、自分で定義する通知設定の集合です。 アラートがトリガーされたときに、特定のアクション グループに通知してそのアラートを受信させることができます。 Azure のアラートは、先を見越して特定の条件に基づく通知を生成するので、これを利用するとアクションを実行する機会が得られます。 1 つのアラートで複数のソースからのデータを使用でき、これにはメトリックやログも含まれます。

アクション グループは、予算との統合が可能な唯一のエンドポイントです。 さまざまなチャネルでの通知を設定できますが、このシナリオでは、このチュートリアルで先ほど作成したロジック アプリに重点を置きます。

### <a name="create-an-action-group-in-azure-monitor"></a>Azure Monitor でアクション グループを作成する

アクション グループを作成するときに、このチュートリアルで作成したロジック アプリを指定します。

1.  [Azure portal](https://portal.azure.com/) にまだサインインしていない場合はサインインして、 **[すべてのサービス]**  >  **[Monitor]** を選択します。
2.  **[設定]** セクションの **[アクション グループ]** を選択します。
3.  **[アクション グループ]** ブレードの **[アクション グループを追加する]** を選択します。
4.  次の項目を追加して確認します。
    - アクション グループ名
    - 短い名前
    - Subscription
    - Resource group

    ![Azure - ロジック アプリ - アクション グループを追加する](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-26.png)

5.  **[アクション グループの追加]** ウィンドウで、ロジック アプリ アクションを追加します。 アクションに **Budget-BudgetLA** という名前を付けます。 **[ロジック アプリ]** ウィンドウで **[サブスクリプション]** と **[リソース グループ]** を選択します。 次に、このチュートリアルで作成した**ロジック アプリ**を選択します。
6.  **[OK]** をクリックしてロジック アプリを設定します。 **[アクション グループの追加]** ウィンドウで **[OK]** を選択してアクション グループを作成します。

これで、予算を効果的にオーケストレーションするために必要なすべてのサポート コンポーネントがそろいました。 次に必要な作業は、予算を作成して、作成済みのアクション グループを使用するようにこの予算を構成することだけです。

## <a name="create-the-azure-budget"></a>Azure の予算を作成する

Cost Management の[予算機能](../cost-management/tutorial-acm-create-budgets.md)を使用して、Azure portal で予算を作成できます。 また、REST API シリーズ、PowerShell コマンドレット、または CLI を使用して予算を作成することもできます。 次に説明する手順では、REST API を使用します。 REST API を呼び出す前に、認証トークンが必要になります。 認証トークンを作成するには、[ARMClient](https://github.com/projectkudu/ARMClient) プロジェクトを使用できます。 **ARMClient** を使用して Azure Resource Manager に対する認証を行い、受け取ったトークンで API を呼び出します。

### <a name="create-an-authentication-token"></a>認証トークンを作成する

1.  GitHub の [ARMClient](https://github.com/projectkudu/ARMClient) プロジェクトに移動します。
2.  リポジトリを複製してローカル コピーを作成します。
3.  このプロジェクトを Visual Studio で開いてビルドします。
4.  ビルドが成功すると、実行可能ファイルが *\bin\debug* フォルダーに出力されます。
5.  ARMClient を実行します。 コマンド プロンプトを開き、プロジェクトのルートから *\bin\debug* フォルダーに移動します。
6.  ログインして認証するために、次のコマンドをコマンド プロンプトで入力します。<br>
    `ARMClient login prod`
7.  **サブスクリプション GUID** を出力からコピーします。
8.  認証トークンをクリップボードにコピーするには、次のコマンドをコマンド プロンプトで入力しますが、必ず前のステップでコピーしたサブスクリプション ID を使用してください。 <br>
    `ARMClient token <subscription GUID from previous step>`

    このステップを完了すると、次のメッセージが表示されます。<br>
    **Token copied to clipboard successfully.**
9.  このチュートリアルの次のセクションの手順で使用するためにトークンを保存します。

### <a name="create-the-budget"></a>予算を作成する

次に、Azure Consumption REST API を呼び出して予算を作成するように **Postman** を構成します。 Postman とは、API 開発環境の 1 つです。 環境とコレクション ファイルを Postman にインポートします。 このコレクションの内容は、Azure Consumption REST API を呼び出す HTTP 要求の定義をグループ化したものです。 環境ファイルには、コレクションによって使用される変数が含まれています。

1.  REST API を実行するための [Postman REST クライアント](https://www.getpostman.com/)をダウンロードして開きます。
2.  Postman で、新しい要求を作成します。

    ![Postman - 新しい要求を作成する](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-27.png)

3.  新しい要求はコレクションとして保存します。したがって、新しい要求には何も含まれていません。

    ![Postman - 新しい要求の保存](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-28.png)

4.  要求のアクションを `Get` から `Put` に変更します。
5.  次の URL に変更を加えます。`{subscriptionId}` を、このチュートリアルの前のセクションで使用した**サブスクリプション ID** で置き換えてください。 また、URL の `{budgetName}` の値として "SampleBudget" を追加してください。`https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2018-03-31`
6.  Postman の **[Headers]** タブを選択します。
7.  新しい **Key** を "Authorization" という名前で追加します。
8.  **[Value]** には、前のセクションの最後に ArmClient を使用して作成したトークンを指定します。
9.  Postman の **[Body]** タブを選択します。
10. **[raw]** オプション ボタンを選択します。
11. テキスト ボックスに、下記のサンプル予算定義を貼り付けます。ただし、**subscriptionid**、**budgetname**、および **actiongroupname** の各パラメーターを、自分のサブスクリプション ID、予算の一意の名前、および作成したアクション グループ名で置き換えてください (URL と要求の本文の両方)。

    ```
        {
            "properties": {
                "category": "Cost",
                "amount": 100.00,
                "timeGrain": "Monthly",
                "timePeriod": {
                "startDate": "2018-06-01T00:00:00Z",
                "endDate": "2018-10-31T00:00:00Z"
                },
                "filters": {
                },
            "notifications": {
                "Actual_GreaterThan_80_Percent": {
                    "enabled": true,
                    "operator": "GreaterThan",
                    "threshold": 80,
                    "contactEmails": [
                    ],
                    "contactRoles": [
                    ],
                    "contactGroups": [
                    "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}
                    ]
                },
            "Actual_EqualTo_100_Percent": {
                    "operator": "EqualTo",
                    "threshold": 100,
                    "contactGroups": [
                "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}"
                    ]
                }
            }
        }
    ```
12. **[Send]** をクリックして要求を送信します。

これで、[budgets API](https://docs.microsoft.com/rest/api/consumption/budgets) を呼び出すために必要なものはすべてそろいました。 budgets API リファレンスには、特定の要求に関する次のような追加の詳細情報が記載されています。
    - **budgetName** - 複数の予算がサポートされています。  予算名は一意である必要があります。
    - **category** - **Cost** と **Usage** のいずれかであることが必要です。 API では、コストと使用の両方の予算がサポートされています。
    - **timeGrain** - 予算が月次か、四半期か、年次かを表します。 金額は期間の最後にリセットされます。
    - **filters** - 選択したスコープ内の特定のリソース セットだけを対象とするように、予算を絞り込むことができます。 たとえば、サブスクリプション レベルの予算に対して、リソース グループのコレクションをフィルターとして指定します。
    - **notifications** – 通知の詳細としきい値を指定します。 複数のしきい値を設定し、通知を受信する電子メール アドレスまたはアクション グループを指定できます。

## <a name="summary"></a>まとめ

このチュートリアルで学習した内容は次のとおりです。
- VM を停止するための Azure Automation Runbook を作成する方法。
- 予算のしきい値に基づいてトリガーされ、適切なパラメーター付きで関連する Runbook を呼び出す Azure ロジック アプリを作成する方法。
- 予算のしきい値に到達したときに Azure ロジック アプリをトリガーするように構成された Azure Monitor アクション グループを作成する方法。
- Azure 予算を作成して希望のしきい値を指定し、アクション グループに関連付ける方法。

これで、サブスクリプションの予算が完成し、構成した予算のしきい値に達したときに VM がシャットダウンされるようになりました。

## <a name="next-steps"></a>次の手順

- Azure の請求シナリオの詳細については、「[請求およびコスト管理の自動化シナリオ](billing-cost-management-automation-scenarios.md)」を参照してください。
