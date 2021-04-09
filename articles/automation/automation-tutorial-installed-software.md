---
title: Azure Automation で VM にインストールされているソフトウェアを検出する | Microsoft Docs
description: この記事では、環境全体で VM にインストールされているソフトウェアについて説明します。
services: automation
keywords: インベントリ, オートメーション, Change Tracking
ms.date: 04/11/2018
ms.topic: tutorial
ms.subservice: change-inventory-management
ms.custom: mvc
ms.openlocfilehash: 8451067ae86e95269c0c2f22554e1654ac64c1e4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "100593779"
---
# <a name="discover-what-software-is-installed-on-your-vms"></a>VM にインストールされているソフトウェアを検出する

このチュートリアルでは、Azure Automation の Change Tracking およびインベントリ機能を使用して、環境にインストールされているソフトウェアを確認する方法について説明します。 コンピューター上のソフトウェア、ファイル、Linux デーモン、Windows サービス、Windows レジストリ キーのインベントリを収集し、表示することができます。 マシンの構成を追跡すると、環境全体の運用上の問題を特定し、マシンの状態をよりよく理解することができます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Change Tracking と Inventory の有効化
> * Azure VM を有効にする
> * Azure 以外の VM を有効にする
> * インストールされているソフトウェアを表示する
> * インストールされているソフトウェアのインベントリ ログを検索する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。

* Azure サブスクリプション。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)にサインアップしてください。
* 監視およびアクションの Runbook と監視タスクを保持する [Automation アカウント](./index.yml)。
* 機能に対して有効にする[仮想マシン](../virtual-machines/windows/quick-create-portal.md)。

## <a name="log-in-to-azure"></a>Azure にログインする

Azure Portal (https://portal.azure.com ) にログインします。

## <a name="enable-change-tracking-and-inventory"></a>Change Tracking と Inventory の有効化

このチュートリアルでは、まず変更履歴とインベントリを有効にする必要があります。 既に機能を有効にしてある場合は、この手順は不要です。

>[!NOTE]
>フィールドが淡色表示されている場合は、その VM で別の Automation 機能が有効になっているため、同じワークスペースと Automation アカウントを使用する必要があります。

1. Automation アカウントに移動し、 **[構成管理]** で、 **[インベントリ]** または **[Change tracking]** を選択します。

2. [Log Analytics](../azure-monitor/logs/log-query-overview.md) ワークスペースを選択します。 このワークスペースは、Change Tracking やインベントリなどの機能によって生成されるデータを収集します。 ワークスペースには、複数のソースからのデータを確認および分析する場所が 1 つ用意されています。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

3. 使用する Automation アカウントを選択します。

4. 展開する場所を構成します。

5. **[有効化]** をクリックして、VM の機能を展開します。 

    ![インベントリの構成バナー](./media/automation-tutorial-installed-software/enableinventory.png)

セットアップの過程で、Windows 用の Log Analytics エージェントと [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) と共に VM がプロビジョニングされます。 Change Tracking およびインベントリの有効化には、最大で 15 分かかることがあります。 この処理中はブラウザーのウィンドウは閉じないでください。

機能が有効になると、VM にインストールされているソフトウェアと変更に関する情報が Azure Monitor ログに送られます。 データの分析に使用できるようになるまでに、30 分から 6 時間かかる場合があります。

## <a name="add-an-azure-vm-to-change-tracking-and-inventory"></a>Azure VM を Change Tracking とインベントリに追加する

1. Automation アカウントで、 **[構成管理]** の下の **[インベントリ]** または **[Change Tracking]** に移動します。

2. **[+ Azure VM の追加]** を選択します。

3. VM の一覧で対象の VM を選択します。 

4. **[有効化]** をクリックして、その VM で Change Tracking とインベントリを有効にします。 Windows 用の Log Analytics エージェントが VM に展開され、Log Analytics ワークスペースと通信するように VM が構成されます。 セットアップ操作には数分かかることがあります。 

5. この時点で、必要に応じて、この機能に対して有効にする新しい VM を一覧から選択できます。

## <a name="add-a-non-azure-machine-to-change-tracking-and-inventory"></a>Azure 以外のマシンを Change Tracking とインベントリに追加する

機能に対して Azure 以外のマシンを有効にするには、次のようにします。

1. お使いのオペレーティング システムに応じて、[Windows 用の Log Analytics エージェント](../azure-monitor/agents/agent-windows.md)または [Linux 用の Log Analytics エージェント](automation-linux-hrw-install.md)をインストールします。 

2. Automation アカウントに移動し、 **[構成管理]** の下の **[インベントリ]** または **[Change Tracking]** に移動します。 

3. **[マシンの管理]** をクリックします。 お使いの Log Analytics ワークスペースをレポート先とするマシンのうち、Change Tracking とインベントリが有効になってないマシンの一覧が表示されます。 実際の環境に合った適切なオプションを以下から選択します。

    * **[使用可能なすべてのマシンで有効にします]** - このオプションを選択すると、その時点で Log Analytics ワークスペースをレポート先とするすべてのマシンで機能が有効になります。
    * **[使用可能なマシンと今後のマシンすべてで有効にします]** - このオプションを選択すると、Log Analytics ワークスペースをレポート先としているすべてのマシンおよび以後そのワークスペースに追加されるすべてのマシンで機能が有効になります。
    * **[選択したマシンで有効にします]** - このオプションを選択すると、選択したマシンでのみ機能が有効になります。

    ![マシンの管理](./media/automation-tutorial-installed-software/manage-machines.png)

## <a name="view-installed-software"></a>インストールされているソフトウェアを表示する

Change Tracking およびインベントリ機能が有効になると、[インベントリ] ページに結果を表示できます。

1. Automation アカウント内で、 **[構成管理]** の **[インベントリ]** を選択します。

2. [インベントリ] ページの **[ソフトウェア]** タブをクリックします。

3. 検出されたソフトウェアの一覧が表に示されます。 ソフトウェアは、ソフトウェアの名とバージョンでグループ分けされています。 各ソフトウェア レコードの詳細情報を表で確認できます。 たとえば、ソフトウェア名、バージョン、発行元、最終更新時刻 (グループ内のマシンから報告された最新の更新時間)、マシン数 (そのソフトウェアがインストールされているマシンの数) などの情報です。

    ![ソフトウェア インベントリ](./media/automation-tutorial-installed-software/inventory-software.png)

4. 行をクリックすると、ソフトウェア レコードのプロパティと、そのソフトウェアがインストールされたマシンの名前が表示されます。

5. 個々のソフトウェアまたはソフトウェアのグループを探すには、ソフトウェア一覧の上部にあるテキスト ボックスで直接検索します。
このフィルターを使用すると、ソフトウェアの名前、バージョン、または発行元に基づいて検索することができます。 たとえば、**Contoso** を検索すると、名前、発行元、またはバージョンに **Contoso** を含むすべてのソフトウェアが返されます。

## <a name="search-inventory-logs-for-installed-software"></a>インストールされているソフトウェアのインベントリ ログを検索する

Change Tracking とインベントリによってログ データが生成され、Azure Monitor ログに送信されます。 クエリを実行してログを検索するには、[インベントリ] ページの上部にある **[Log Analytics]** を選択します。 インベントリ データは、`ConfigurationData` 型に格納されます。

次のサンプル Log Analytics クエリでは、発行元が Microsoft Corporation となっているインベントリ結果が返されます。

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| where Publisher == "Microsoft Corporation"
| summarize arg_max(TimeGenerated, *) by SoftwareName, Computer
```

Azure Monitor ログでのログ ファイルの実行と検索については、[Azure Monitor ログ](../azure-monitor/logs/log-query-overview.md)に関するページを参照してください。

## <a name="see-the-software-inventory-for-a-single-machine"></a>1 台のマシンのソフトウェア インベントリを表示する

1 台のマシンのソフトウェア インベントリを表示するには、Azure VM リソース ページから [インベントリ] にアクセスするか、Azure Monitor ログでフィルターを使用して対応するマシンを表示します。 次の例の Log Analytics クエリは、**ContosoVM** という名前のマシンのソフトウェア一覧を返します。

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| summarize arg_max(TimeGenerated, *) by SoftwareName, CurrentVersion
| where Computer =="ContosoVM"
| render table
| summarize by Publisher, SoftwareName
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、ソフトウェア インベントリの表示方法について、以下のように学習しました。

> [!div class="checklist"]
> * Change Tracking と Inventory の有効化
> * Azure VM を有効にする
> * Azure 以外の VM を有効にする
> * インストールされているソフトウェアを表示する
> * インストールされているソフトウェアのインベントリ ログを検索する

さらに詳しく学ぶには、Change Tracking およびインベントリ機能の概要に進んでください。

> [!div class="nextstepaction"]
> [Change Tracking とインベントリの概要](change-tracking/overview.md)
