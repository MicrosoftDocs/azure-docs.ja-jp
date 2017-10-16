---
title: "Azure での Linux 仮想マシンの監視と更新 | Microsoft Docs"
description: "Azure の Linux 仮想マシンを対象に、ブート診断とパフォーマンス メトリックを監視する方法と、パッケージの更新を管理する方法について説明します"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/08/2017
ms.author: davidmu
ms.custom: mvc
ms.openlocfilehash: 70c17d9a8f7bf6d9106efcb56eee7cd996460c18
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-monitor-and-update-a-linux-virtual-machine-in-azure"></a>Azure で Linux 仮想マシンを監視および更新する方法

Azure の仮想マシン (VM) が正常に実行されていることを確認するためには、ブート診断とパフォーマンス メトリックを確認し、パッケージの更新を管理します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * VM でブート診断を有効にする
> * ブート診断を表示する
> * ホストのメトリックを表示する
> * VM で診断拡張機能を有効にする
> * VM のメトリックを表示する
> * 診断のメトリックに基づくアラートを作成する
> * パッケージの更新を管理する
> * 高度な監視をセットアップする


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.4 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

## <a name="create-vm"></a>VM を作成する

診断とメトリックの動作を確認するには、VM が必要です。 最初に、[az group create](/cli/azure/group#create) を使用して、リソース グループを作成します。 次の例では、*myResourceGroupMonitor* という名前のリソース グループを場所 *eastus* に作成します。

```azurecli-interactive 
az group create --name myResourceGroupMonitor --location eastus
```

ここで [az vm create](https://docs.microsoft.com/cli/azure/vm#az_vm_create) を使用して VM を作成します。 次の例では、*myVM* という名前の VM を作成します。

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="enable-boot-diagnostics"></a>ブート診断を有効にする

Linux VM が起動すると、ブート診断拡張機能によってブート出力がキャプチャされて Azure Storage に格納されます。 VM の起動に関する問題は、このデータを使ってトラブルシューティングすることができます。 Azure CLI を使用して Linux VM を作成した場合、ブート診断が自動的に有効になりません。

ブート診断を有効にするにはまず、ブート ログを格納するためのストレージ アカウントを作成しておく必要があります。 ストレージ アカウントには、グローバルに一意の名前が必要です。名前は 3 ～ 24 文字とし、数字と小文字のみを使用できます。 ストレージ アカウントは、[az storage account create](/cli/azure/storage/account#create) コマンドで作成します。 この例では、ランダムな文字列を使って一意のストレージ アカウント名を作成しています。 

```azurecli-interactive 
storageacct=mydiagdata$RANDOM

az storage account create \
  --resource-group myResourceGroupMonitor \
  --name $storageacct \
  --sku Standard_LRS \
  --location eastus
```

ブート診断を有効にするときは、Blob Storage コンテナーの URI が必要となります。 次のコマンドは、ストレージ アカウントを照会して、この URI を取得しています。 この URI 値を *bloburi* という変数に格納しておき、次の手順で使用します。

```azurecli-interactive 
bloburi=$(az storage account show --resource-group myResourceGroupMonitor --name $storageacct --query 'primaryEndpoints.blob' -o tsv)
```

今度は、[az vm boot-diagnostics enable](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_enable) を使用して、ブート診断を有効にします。 `--storage` の値は、前の手順で取得した BLOB の URI です。

```azurecli-interactive 
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```


## <a name="view-boot-diagnostics"></a>ブート診断を表示する

ブート診断が有効になっている場合、VM を停止して起動するたびに、ブート プロセスに関する情報がログ ファイルに書き込まれます。 この例ではまず、次のように [az vm deallocate](/cli/azure/vm#deallocate) コマンドで VM の割り当てを解除します。

```azurecli-interactive 
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

次に、[az vm start]( /cli/azure/vm#stop) コマンドで VM を起動します。

```azurecli-interactive 
az vm start --resource-group myResourceGroupMonitor --name myVM
```

*myVM* のブート診断データは、次のように [az vm boot-diagnostics get-boot-log](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az_vm_boot_diagnostics_get_boot_log) コマンドで取得できます。

```azurecli-interactive 
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```


## <a name="view-host-metrics"></a>ホストのメトリックを表示する

Azure には、Linux VM と連動する専用のホストがあります。 メトリックは、そのホストを対象に自動的に収集され、Azure Portal に次のように表示されます。

1. Azure Portal で **[リソース グループ]** をクリックし、**[myResourceGroupMonitor]** を選択して、リソース一覧から **[myVM]** を選択します。
1. ホスト VM の実行状況を確認するには、VM ブレードの **[メトリック]** をクリックし、**[利用可能なメトリック]** からいずれかの *[Host]* メトリックを選択します。

    ![ホストのメトリックを表示する](./media/tutorial-monitoring/monitor-host-metrics.png)


## <a name="install-diagnostics-extension"></a>診断拡張機能をインストールする

> [!IMPORTANT]
> このドキュメントでは、非推奨の Linux Diagnostic Extension バージョン 2.3 について説明します。 バージョン 2.3 は 2018 年 6 月 30 日までサポートされる予定です。
>
> 代わりに Linux Diagnostic Extension バージョン 3.0 をご利用いただけます。 詳細については、[こちらのドキュメント](./diagnostic-extension.md)をご覧ください。

基本的なホスト メトリックは利用できますが、さらに粒度の細かい VM 固有のメトリックを表示するためには、Azure 診断拡張機能を VM にインストールする必要があります。 Azure 診断拡張機能を通じて、より詳しい監視データと診断データを VM から取得することができます。 これらのパフォーマンス メトリックを確認したり、VM のパフォーマンスに基づくアラートを作成したりすることができます。 診断拡張機能は、次のように Azure Portal からインストールします。

1. Azure Portal で **[リソース グループ]** をクリックし、**[myResourceGroup]** を選択して、リソース一覧から **[myVM]** を選択します。
1. **[診断の設定]** をクリックします。 *[ブート診断]* は、前のセクションで既に有効にしたので、そのように表示されています。 *[基本メトリック]* のチェック ボックスをオンにします。
1. *[ストレージ アカウント]* セクションで、前のセクションで作成した *mydiagdata[1234]* アカウントに移動して選択します。
1. **[保存]** ボタンをクリックします。

    ![診断メトリックの表示](./media/tutorial-monitoring/enable-diagnostics-extension.png)


## <a name="view-vm-metrics"></a>VM のメトリックを表示する

VM のメトリックは、ホスト VM のメトリックと同じ方法で表示できます。

1. Azure Portal で **[リソース グループ]** をクリックし、**[myResourceGroup]** を選択して、リソース一覧から **[myVM]** を選択します。
1. VM の実行状況を確認するには、VM ブレードの **[メトリック]** をクリックし、**[利用可能なメトリック]** からいずれかの診断メトリックを選択します。

    ![VM のメトリックを表示する](./media/tutorial-monitoring/monitor-vm-metrics.png)


## <a name="create-alerts"></a>アラートを作成する

特定のパフォーマンス メトリックに基づいてアラートを作成することができます。 平均 CPU 使用率が特定のしきい値を超えたときや、空きディスク領域が特定の容量を下回ったときなどに、アラートによって通知を受け取ることができます。 アラートは、Azure Portal に表示されるほか、電子メールで送信することもできます。 アラートが生成されたことへの対応として、Azure Automation Runbook または Azure Logic Apps をトリガーすることもできます。

平均 CPU 使用率のアラートを作成する例を次に示します。

1. Azure Portal で **[リソース グループ]** をクリックし、**[myResourceGroup]** を選択して、リソース一覧から **[myVM]** を選択します。
2. VM ブレードの **[アラート ルール]** をクリックし、アラート ブレード上部にある **[メトリック アラートの追加]** をクリックします。
4. **[名前]** にアラートの名前を入力します (例: *myAlertRule*)。
5. CPU の割合が 1.0 を超えた状態が 5 分間続いたときにアラートをトリガーするために、それ以外の選択肢はすべて既定値のままにします。
6. 必要に応じて *[所有者、共同作成者、閲覧者に電子メールを送信]* のチェック ボックスをオンにして、電子メール通知を送信することもできます。 既定のアクションでは、ポータルに通知が表示されます。
7. **[OK]** ボタンをクリックします。

## <a name="manage-package-updates"></a>パッケージの更新を管理する

Update Management を使用することで、Azure Linux VM のパッケージの更新とパッチを管理できます。 VM から直接、利用可能な更新プログラムのステータスを迅速に把握したり、必要な更新プログラムのインストールをスケジュールしたり、デプロイの結果を確認して、VM に更新プログラムが正常に適用されたことを検証したりできます。

価格情報については、[Update Management の Automation の価格](https://azure.microsoft.com/pricing/details/automation/)に関するページをご覧ください。

### <a name="enable-update-management-preview"></a>Update Management の有効化 (プレビュー)

お使いの VM で Update Management を有効にする

1. 画面左側の **[仮想マシン]** を選択します。
1. 一覧から VM を選択します。
1. [VM] 画面の **[操作]** セクションで、**[更新の管理]** をクリックします。 **[更新管理の有効化]** 画面が開きます。

この VM で Update Management が有効になっているかを確認する検証が行われます。 この検証では、Log Analytics ワークスペースの確認、リンクされた Automation アカウントの確認、ソリューションがワークスペースにあるかどうかの確認が行われます。

Log Analytics ワークスペースは、Update Management のような機能およびサービスによって生成されるデータを収集するために使用されます。 ワークスペースには、複数のソースからのデータを確認および分析する場所が 1 つ用意されています。 更新を必要とする VM で追加のアクションを実行する場合、Azure Automation を使用すると、VM に対してスクリプトを実行して、更新プログラムをダウンロードして適用するなどの操作を行うことができます。

また、検証プロセスでは、VM が Microsoft Monitoring Agent (MMA) とハイブリッド worker でプロビジョニングされているかどうかが確認されます。 このエージェントは VM との通信に使用され、更新ステータスに関する情報を取得します。 

これらの前提条件が満たされていない場合、バナーが表示され、ソリューションを有効にするオプションを選択できます。

![Update Management のオンボード構成バナー](./media/tutorial-monitoring/manage-updates-onboard-solution-banner.png)

バナーをクリックすると、ソリューションが有効になります。 検証後、次の前提条件のいずれかを満たしていないことがわかった場合は、自動的に追加されます。

* [Log Analytics](../../log-analytics/log-analytics-overview.md) ワークスペース
* [Automation](../../automation/automation-offering-get-started.md)
* [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) が VM で有効になっている

**[更新管理の有効化]** 画面が開きます。 設定を構成し、**[有効]** をクリックします。

![Update Management ソリューションを有効にする](./media/tutorial-monitoring/manage-updates-update-enable.png)

ソリューションを有効にするは最大 15 分かかります。このとき、ブラウザー ウィンドウを閉じないでください。 ソリューションが有効になると、VM 上のパッケージ マネージャーからの不足している更新プログラムの情報が Log Analytics に送られます。
データの分析に使用できるようになるまでに、30 分から 6 時間かかる場合があります。

### <a name="view-update-assessment"></a>更新の評価を確認する

**Update Management** ソリューションが有効になると、**[更新の管理]** 画面が表示されます。 **[不足している更新プログラム]** タブに、不足している更新プログラムのリストが表示されます。

![更新ステータスを確認する](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>更新プログラムのデプロイをスケジュールする

更新プログラムをインストールするには、リリース スケジュールとメンテナンス期間に従ってデプロイをスケジュールします。

**[更新の管理]** 画面の上部にある **[更新プログラムの展開のスケジュール]** をクリックして、VM の新しい更新プログラムのデプロイをスケジュールします。 **[新しい更新プログラムの展開]** 画面で、次の情報を指定します。

* **名前**: 更新プログラムのデプロイを識別する一意の名前を指定します。
* **更新して除外する**: 更新プログラムから除外するパッケージの名前を入力する場合に選択します。
* **スケジュール設定**: 既定の日時 (現在の時刻から 30 分後) を使用するか、別の時刻を指定します。 デプロイを 1 回行うか、定期的なスケジュールを設定するかを指定することもできます。 定期的なスケジュールを設定するには、[繰り返し] の下の [繰り返し] オプションをクリックします。

  ![更新プログラムのスケジュール設定画面](./media/tutorial-monitoring/manage-updates-schedule-linux.png)

* **メンテナンス期間 (分)**: 更新プログラムをデプロイする期間を指定します。  これにより、定義したメンテナンス期間内に変更が実行されます。 

スケジュールの構成が完了したら、**[作成]** ボタンをクリックして、ステータス ダッシュボードに戻ります。
**スケジュール済み**の表に、作成したデプロイ スケジュールが表示されていることを確認してください。

> [!WARNING]
> メンテナンス期間に十分な時間が設定されている場合は、更新プログラムがインストールされた後、VM が自動的に再起動します。

Update Management では、パッケージをインストールするのに、ご使用の VM 上の既存のパッケージ マネージャーが使用されます。

### <a name="view-results-of-an-update-deployment"></a>更新プログラムのデプロイの結果を確認する

スケジュールされたデプロイの開始後、**[更新管理]** 画面の **[更新プログラムの展開]** タブに、デプロイの状態が表示されます。
実行中の場合、状態は **[処理中]** と表示されます。 正常に完了すると、ステータスは **[成功]** に変わります。
デプロイ時に 1 つ以上の更新プログラムでエラーが発生した場合、ステータスは **[失敗]** になります。
完了した更新プログラムのデプロイをクリックし、その更新プログラムのデプロイのダッシュボードを確認します。

![特定のデプロイに関する更新プログラムのデプロイ ステータスのダッシュボード](./media/tutorial-monitoring/manage-updates-view-results.png)

**[新プログラムを実行した結果]** のタイルに表示されるのは、VM 上の更新プログラムの合計数とデプロイ結果の概要です。
右側の表に、各更新プログラムとインストールの結果の詳細が示されます。これは、次の値のいずれかになります。

* **試行されていません**: メンテナンス期間として定義した時間が十分ではなかったため、更新プログラムがインストールされませんでした。
* **成功**: 更新プログラムが正常にダウンロードされ、VM にインストールされています。
* **失敗**: 更新プログラムの VM へのダウンロードまたはインストールに失敗しました。

**[All logs]\(すべてのログ\)** をクリックし、デプロイで作成されたすべてのログ エントリを確認します。

**[出力]** タイルをクリックし、ターゲット VM での更新プログラムのデプロイを管理する runbook のジョブ ストリームを確認します。

デプロイで発生したエラーの詳細情報を確認するには、**[エラー]** をクリックします。

## <a name="advanced-monitoring"></a>高度な監視 

[Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) を使用すると、VM に対してさらに高度な監視を行うことができます。 まだサインアップしていない場合は、Operations Management Suite の[無料試用版](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-trial)にサインアップしてください。

OMS ポータルにアクセスすると、[設定] ブレードにワークスペース キーとワークスペース識別子が表示されます。 以下のように **az vm extension set** を使用して OMS 拡張機能を VM に追加できます。<workspace-key> と <workspace-id> は、実際の OMS ワークスペースの値に置き換えてください。

```azurecli-interactive 
az vm extension set \
  --resource-group myResourceGroupMonitor \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.3 \
  --protected-settings '{"workspaceKey": "<workspace-key>"}' \
  --settings '{"workspaceId": "<workspace-id>"}'
```

OMS ポータルの [ログ検索] ブレードに *myVM* が表示されます (下図参照)。

![OMS ブレード](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、VM 向けに更新プログラムを構成、確認、管理しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * VM でブート診断を有効にする
> * ブート診断を表示する
> * ホストのメトリックを表示する
> * VM で診断拡張機能を有効にする
> * VM のメトリックを表示する
> * 診断のメトリックに基づくアラートを作成する
> * パッケージの更新を管理する
> * 高度な監視をセットアップする

次のチュートリアルに進み、Azure Security Center について学習してください。

> [!div class="nextstepaction"]
> [VM のセキュリティの管理](./tutorial-azure-security.md)
