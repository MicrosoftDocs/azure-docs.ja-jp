---
title: Azure Automation でマシンにインストールされているソフトウェアを検出する | Microsoft Docs
description: インベントリを使用して、環境全体のマシンにインストールされているソフトウェアを検出します。
services: automation
keywords: インベントリ, オートメーション, 変更, 追跡
author: jennyhunter-msft
ms.author: jehunte
ms.date: 04/11/2018
ms.topic: tutorial
ms.service: automation
ms.subservice: change-inventory-management
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 47313781756e460a8c30638661489874481b88a0
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476818"
---
# <a name="discover-what-software-is-installed-on-your-azure-and-non-azure-machines"></a>Azure マシンと Azure 以外のマシンにインストールされているソフトウェアを検出する

このチュートリアルでは、環境内にインストールされているソフトウェアを検出する方法について説明します。 コンピューター上のソフトウェア、ファイル、Linux デーモン、Windows サービス、Windows レジストリ キーのインベントリを収集し、表示することができます。 マシンの構成を追跡すると、環境全体の運用上の問題を特定し、マシンの状態をよりよく理解することができます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * ソリューションを有効にする
> * Azure VM の配布準備
> * Azure 以外の VM の配布準備
> * インストールされているソフトウェアを表示する
> * インストールされているソフトウェアのインベントリ ログを検索する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。

* Azure サブスクリプション。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)にサインアップしてください。
* 監視およびアクションの Runbook と監視タスクを保持する、[Automation アカウント](automation-offering-get-started.md)。
* オンボードする[仮想マシン](../virtual-machines/windows/quick-create-portal.md)。

## <a name="log-in-to-azure"></a>Azure にログインする

Azure Portal (https://portal.azure.com ) にログインします。

## <a name="enable-change-tracking-and-inventory"></a>変更履歴とインベントリを有効にする

このチュートリアルでは、まず変更履歴とインベントリを有効にする必要があります。 既に **Change Tracking** ソリューションを有効にしてある場合、この手順は不要です。

Automation アカウントに移動し、 **[構成管理]** で、 **[インベントリ]** を選択します。

Log Analytics ワークスペースおよび Automation アカウントを選択し、 **[Enable]\(有効にする\)** をクリックして、ソリューションを有効にします。 ソリューションを有効にするには最大 15 分かかります。

![インベントリのオンボード構成バナー](./media/automation-tutorial-installed-software/enableinventory.png)

このソリューションを有効にするには、使用する場所、Log Analytics ワークスペース、Automation アカウントを構成し、 **[Enable]\(有効にする\)** をクリックします。 フィールドが淡色表示されている場合は、その VM で別の Automation ソリューションが有効になっているため、同じワークスペースと Automation アカウントを使用する必要があることを示します。

[Log Analytics](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fautomation%2ftoc.json) ワークスペースは、インベントリのような機能およびサービスによって生成されるデータを収集するために使用されます。
ワークスペースには、複数のソースからのデータを確認および分析する場所が 1 つ用意されています。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

ソリューションを有効にするには最大 15 分かかります。 この処理中はブラウザーのウィンドウは閉じないでください。
ソリューションが有効になると、VM にインストールされているソフトウェアと変更に関する情報が Azure Monitor ログに送られます。
データの分析に使用できるようになるまでに、30 分から 6 時間かかる場合があります。

## <a name="onboard-a-vm"></a>VM の配布準備

Automation アカウントで、 **[構成管理]** の **[インベントリ]** に移動します。

**[+ Azure VM の追加]** を選択すると、 **[仮想マシン]** ページが表示され、既存の VM を一覧から選択することができます。 配布準備をする VM を選択します。 表示されたページで **[Enable]\(有効にする\)** をクリックすると、その VM 上でソリューションが有効になります。 Microsoft Management Agent が VM にデプロイされ、ソリューションを有効にするときに構成した Log Analytics ワークスペースと通信を行うようにエージェントが構成されます。 配布準備が完了するまでに数分かかる場合があります。 この時点で、新しい VM を一覧から選択し、別の VM の配布準備をすることができます。

## <a name="onboard-a-non-azure-machine"></a>Azure 以外のマシンの配布準備

Azure 以外のマシンを追加するには、お使いのオペレーティング システムに応じて、[Windows](../azure-monitor/platform/agent-windows.md) または [Linux](automation-linux-hrw-install.md) のエージェントをインストールします。 エージェントのインストール後、Automation アカウントに移動し、 **[構成管理]** の **[インベントリ]** に移動します。 **[マシンの管理]** をクリックすると、お使いの Log Analytics ワークスペースをレポート先とするマシンのうち、ソリューションが有効にされていないマシンが一覧表示されます。 実際の環境に合った適切なオプションを選んでください。

* **[Enable on all available machines]\(使用可能なすべてのマシンで有効にします\)** - このオプションを選択すると、その時点で Log Analytics ワークスペースをレポート先とするすべてのマシンでソリューションが有効になります。
* **[Enable on all available machines and future machines]\(使用可能なすべてのマシンおよび今後のマシンで有効にします\)** - このオプションを選択すると、Log Analytics ワークスペースをレポート先としているすべてのマシンおよび以後そのワークスペースに追加されることになるすべてのマシンでソリューションが有効になります。
* **[Enable on selected machines]\(選択したマシンで有効にします\)** - このオプションを選択すると、選択したマシンでのみソリューションが有効になります。

![マシンの管理](./media/automation-tutorial-installed-software/manage-machines.png)

## <a name="view-installed-software"></a>インストールされているソフトウェアを表示する

変更履歴とインベントリ ソリューションが有効になると、 **[インベントリ]** ページで結果を表示できます。

Automation アカウント内から、 **[構成管理]** の **[インベントリ]** を選択します。

**[インベントリ]** ページの **[ソフトウェア]** タブをクリックします。

**[ソフトウェア]** タブにあるテーブルに、検出されたソフトウェアの一覧が表示されます。 ソフトウェアは、ソフトウェアの名とバージョンでグループ分けされています。

各ソフトウェア レコードの詳細情報を表で確認できます。 たとえば、ソフトウェア名、バージョン、発行元、最終更新時刻 (グループ内のマシンから報告された最新の更新時間)、マシン数 (そのソフトウェアがインストールされているマシンの数) などの情報です。

![ソフトウェア インベントリ](./media/automation-tutorial-installed-software/inventory-software.png)

行をクリックすると、ソフトウェア レコードのプロパティと、そのソフトウェアがインストールされたマシンの名前が表示されます。

個々のソフトウェアまたはソフトウェアのグループを探すには、ソフトウェア一覧の上部にあるテキスト ボックスで直接検索します。
このフィルターを使用すると、ソフトウェアの名前、バージョン、または発行元に基づいて検索することができます。

たとえば、"Contoso" を検索すると、名前、発行元、またはバージョンに "Contoso" を含むすべてのソフトウェアが返されます。

## <a name="search-inventory-logs-for-installed-software"></a>インストールされているソフトウェアのインベントリ ログを検索する

インベントリは、Azure Monitor ログに送信されるログ データを生成します。 クエリを実行してログを検索するには、 **[インベントリ]** ウィンドウの上部にある **[Log Analytics]** ウィンドウを選択します。

インベントリ データは、型 **ConfigurationData** に格納されます。
次のサンプル Log Analytics クエリは、Publisher が "Microsoft Corporation" と等しいインベントリの結果を返します。

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| where Publisher == "Microsoft Corporation"
| summarize arg_max(TimeGenerated, *) by SoftwareName, Computer
```

Azure Monitor ログでのログ ファイルの実行と検索については、[Azure Monitor ログ](../azure-monitor/log-query/log-query-overview.md)に関するページを参照してください。

### <a name="single-machine-inventory"></a>1 台のマシンのインベントリ

1 台のマシンのソフトウェア インベントリを表示するには、Azure VM リソース ページから [インベントリ] にアクセスするか、Azure Monitor ログでフィルターを使用して対応するマシンを表示します。
次の例の Log Analytics クエリは、ContosoVM という名前のマシンのソフトウェア一覧を返します。

```loganalytics
ConfigurationData
| where ConfigDataType == "Software"
| summarize arg_max(TimeGenerated, *) by SoftwareName, CurrentVersion
| where Computer =="ContosoVM"
| render table
| summarize by Publisher, SoftwareName
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次のようなソフトウェア インベントリの表示方法について学習しました。

> [!div class="checklist"]
> * ソリューションを有効にする
> * Azure VM の配布準備
> * Azure 以外の VM の配布準備
> * インストールされているソフトウェアを表示する
> * インストールされているソフトウェアのインベントリ ログを検索する

さらに詳しく学ぶには、変更履歴とインベントリ ソリューションの概要に進んでください。

> [!div class="nextstepaction"]
> [変更管理とインベントリ ソリューション](automation-change-tracking.md)
