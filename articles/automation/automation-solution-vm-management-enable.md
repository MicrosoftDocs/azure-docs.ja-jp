---
title: Azure Automation の Start/Stop VMs during off-hours を有効にする
description: この記事では、Azure VM の Start/Stop VMs during off-hours 機能を有効にする方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 36f885416c5e9cb656d01a65b9c503f8897d2f9f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100593894"
---
# <a name="enable-startstop-vms-during-off-hours"></a>Start/Stop VMs during off-hours を有効にする

このトピックの手順を順に行って、新規または既存の Automation アカウントおよびリンクされた Log Analytics ワークスペースを使用して、VM の Start/Stop VMs during off-hours 機能を有効にします。 セットアップ プロセスが完了したら、機能をカスタマイズする変数を構成します。

>[!NOTE]
>クラシック VM でこの機能を使用するには、既定では作成されないクラシック実行アカウントが必要です。 「[クラシック実行アカウントを作成する](automation-create-standalone-account.md#create-a-classic-run-as-account)」を参照してください。
>

## <a name="create-resources-for-the-feature"></a>機能のリソースを作成する

1. Azure [Portal](https://portal.azure.com) にサインインします。
2. **Automation アカウント** を検索して選択します。
3. [Automation アカウント] ページで、一覧から Automation アカウントを選択します。
4. Automation アカウントから、 **[関連リソース]** の下にある **[VM の開始/停止]** を選択します。 この画面では、 **[Learn more about and enable the solution]\(ソリューションの詳細と有効化\)** をクリックして確認できます。 この機能が既にデプロイされている場合は、 **[ソリューションの管理]** をクリックし、それを一覧から見つけることができます。

   ![Automation アカウントから有効にする](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Azure portal のどこからでも、 **[リソースの作成]** をクリックしてリソースを作成できます。 [Marketplace] ページで、「**Start**」、「**Start/Stop**」などのキーワードを入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 または、機能のフルネームから 1 つ以上のキーワードを入力し、**Enter** キーを押すこともできます。 検索結果から **Start/Stop VMs during off-hours** を選択します。

5. 選択したデプロイの [Start/Stop VMs during off-hours] ページで概要を確認し、 **[作成]** をクリックします。

   ![Azure portal](media/automation-solution-vm-management/azure-portal-01.png)

## <a name="configure-the-feature"></a>機能を構成する

リソースが作成されると、[ソリューションの追加] ページが表示されます。 Automation サブスクリプションにインポートする前に、機能を構成するように求められます。 「[Start/Stop VMs during off-hours を構成する](automation-solution-vm-management-config.md)」を参照してください。

   ![VM 管理の [ソリューションの追加] ページ](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

## <a name="select-a-log-analytics-workspace"></a>Log Analytics ワークスペースを選択する

1. [ソリューションの追加] ページで、 **[ワークスペース]** を選択します。 Automation アカウントによって使用されている Azure サブスクリプションにリンクされている Log Analytics ワークスペースを選択します。 

2. ワークスペースがない場合は、 **[新しいワークスペースの作成]** を選択します。 [Log Analytics ワークスペース] ページで、次の手順を行います。

   - 新しい Log Analytics ワークスペースの名前 (**ContosoLAWorkspace** など) を指定します。
   - 既定値が適切でない場合は、リンクする **サブスクリプション** をドロップダウン リストから選択します。
   - **[リソース グループ]** では、新しいリソース グループを作成するか、既存のリソース グループを選択できます。
   - **[場所]** を選択します。
   - **[価格レベル]** を選択します。 **[GB ごと (スタンドアロン)]** オプションを選択します。 Azure Monitor ログは[価格](https://azure.microsoft.com/pricing/details/log-analytics/)が更新され、GB あたりのレベルが唯一のオプションです。

   > [!NOTE]
   > 機能を有効にする際、Log Analytics ワークスペースと Automation アカウントのリンクは特定のリージョンでのみサポートされています。 サポートされているマッピング ペアの一覧については、[Automation アカウントと Log Analytics ワークスペースのリージョン マッピング](how-to/region-mappings.md)に関する記事をご覧ください。

3. [Log Analytics ワークスペース] ページで必要な情報を入力した後、 **[作成]** をクリックします。 その進行状況は、メニューの **[通知]** で追跡できます。完了したら、[ソリューションの追加] ページに戻ります。

## <a name="add-automation-account"></a>Automation アカウントの追加

[ソリューションの追加] ページに再びアクセスし、 **[Automation アカウント]** を選択します。 Log Analytics ワークスペースにまだリンクされていない既存の Automation アカウントを選択できます。 新しい Log Analytics ワークスペースを作成する場合は、それに関連付ける新しい Automation アカウントを作成できます。 既存の Automation アカウントを選択するか、 **[Automation アカウントの作成]** をクリックし、[Automation アカウントの追加] ページで、Automation アカウントの名前を **[名前]** フィールドに入力します。

それ以外のオプションはすべて、選択した Log Analytics ワークスペースに基づいて自動的に入力されます。 これらのオプションは変更できません。 この機能に含まれている Runbook は、Azure 実行アカウントが既定の認証方法になります。 

**[OK]** をクリックすると、構成オプションが検証され、Automation アカウントが作成されます。 進行状況は、メニューの **[通知]** で追跡できます。

## <a name="define-feature-parameters"></a>機能パラメーターを定義する

1. [ソリューションの追加] ページで、 **[構成]** を選択します。 [パラメーター] ページが表示されます。

    ![ソリューションの [パラメーター] ページ](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

2. **[Target ResourceGroup Names]\(ターゲット リソース グループ名\)** フィールドの値を指定します。 このフィールドは、管理する機能の VM を含むグループ名を定義します。 複数の名前を入力する場合は、それぞれをコンマで区切ってください (値の大文字と小文字は区別されません)。 ワイルドカードを使用して、サブスクリプション内の全リソース グループの VM を対象にすることもできます。 値は `External_Start_ResourceGroupNames` および `External_Stop_ResourceGroupNames` 変数に格納されます。

    > [!IMPORTANT]
    > **[Target ResourceGroup Names]\(ターゲット リソース グループ名\)** の既定値は **&ast;** です。 この設定は、サブスクリプション内のすべての VM が対象です。 サブスクリプション内のすべての VM を対象にする機能を使用しない場合は、スケジュールを選択する前に、リソース グループ名の一覧を指定する必要があります。
  
3. **[VM 除外リスト (文字列)]** フィールドの値を指定します。 この値は、ターゲット リソース グループの 1 つ以上の仮想マシンの名前です。 複数の名前を入力する場合は、それぞれをコンマで区切ってください (値の大文字と小文字は区別されません)。 ワイルドカードの使用がサポートされています。 この値は、`External_ExcludeVMNames` 変数に格納されます。
  
4. **[スケジュール]** フィールドを使用して、機能による VM 管理のスケジュールを選択します。 スケジュールの開始日時を選択して、選択した時刻に開始する定期的な日単位のスケジュールを作成します。 別のリージョンを選択することはできません。 機能の構成後、スケジュールを特定のタイム ゾーンに構成するには、「[起動および停止スケジュールの変更](automation-solution-vm-management-config.md#modify-the-startup-and-shutdown-schedules)」を参照してください。

5. [アクション グループ](../azure-monitor/alerts/action-groups.md)から電子メール通知を受信するには、 **[メール通知]** フィールドで既定値の **[はい]** を使用し、有効なメール アドレスを指定します。 **[いいえ]** を選択したものの、後日、電子メール通知を受信することにした場合は、コンマで区切られた有効なメール アドレスで作成されたアクション グループを更新することができます。 

6. 次のアラート ルールを有効にします。

   - `AutoStop_VM_Child`
   - `Scheduled_StartStop_Parent`
   - `Sequenced_StartStop_Parent`

## <a name="create-alerts"></a>アラートを作成する

Start/Stop VMs during off-hours には、事前に定義された一連のアラートは含まれていません。 [Azure Monitor を使用したログ アラートの作成](../azure-monitor/alerts/alerts-log.md)に関するページを参照して、ジョブ失敗アラートを作成し、DevOps や運用プロセスおよび手順をサポートする方法について学習してください。

## <a name="deploy-the-feature"></a>機能をデプロイする

1. 機能に必要な初期設定を構成した後、 **[OK]** をクリックして [パラメーター] ページを閉じます。

2. **Create** をクリックしてください。 すべての設定が検証された後、機能はご利用のサブスクリプションにデプロイされます。 このプロセスは、完了までに数秒かかる場合があります。進行状況は、メニューの **[通知]** で確認してください。

    > [!NOTE]
    > Azure クラウド ソリューション プロバイダー (Azure CSP) サブスクリプションがある場合は、デプロイ完了後に、Automation アカウントで、 **[共有リソース]** の **[変数]** に移動し、[External_EnableClassicVMs](automation-solution-vm-management.md#variables) 変数を **False** に設定します。 これにより、ソリューションによるクラシック VM リソースの検索が停止します。

## <a name="next-steps"></a>次のステップ

* 機能を設定するには、「[Start/Stop VMs during off-hours を構成する](automation-solution-vm-management-config.md)」を参照してください。
* 機能のエラーを解決するには、「[Start/Stop VMs during off-hours の問題のトラブルシューティング](troubleshoot/start-stop-vm.md)」を参照してください。