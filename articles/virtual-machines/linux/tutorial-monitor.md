---
title: チュートリアル - Azure の Linux 仮想マシンの監視
description: このチュートリアルでは、Linux 仮想マシンのパフォーマンスと、そこで検出された実行中のアプリケーション コンポーネントを監視する方法について説明します。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: mgoedtel
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/30/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: c8d7b13f9e35a41a414a44c908997cfcc550af41
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89011737"
---
# <a name="tutorial-monitor-a-linux-virtual-machine-in-azure"></a>チュートリアル:Azure の Linux 仮想マシンを監視する

Azure Monitoring は、エージェントを使用して Azure VM からブート データとパフォーマンス データを収集します。そのデータは、Azure Storage に格納され、ポータルや Azure PowerShell モジュール、Azure CLI からアクセスできるようになります。 Azure Monitor for VMs でパフォーマンス メトリックを収集し、VM にインストールされているアプリケーション コンポーネントを検出すると共に、パフォーマンス グラフと依存関係マップを追加することによって、先進の監視機能が実現されます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * VM でブート診断を有効にする
> * ブート診断を表示する
> * VM ホストのメトリックを表示する
> * Azure Monitor for VMs の有効化
> * VM のパフォーマンス メトリックを表示する
> * アラートを作成する

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/powershell](https://shell.azure.com/powershell) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、Enter キーを押して実行します。

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.30 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="create-vm"></a>VM を作成する

診断とメトリックの動作を確認するには、VM が必要です。 最初に、[az group create](/cli/azure/group?view=azure-cli-latest#az-group-create) を使用して、リソース グループを作成します。 次の例では、*myResourceGroupMonitor* という名前のリソース グループを場所 *eastus* に作成します。

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

ここで [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create) を使用して VM を作成します。 次の例では、*myVM* という名前の VM を作成し、SSH キーを生成します ( *~/.ssh/* にまだ存在していない場合)。

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

ブート診断を有効にするにはまず、ブート ログを格納するためのストレージ アカウントを作成しておく必要があります。 ストレージ アカウントには、グローバルに一意の名前が必要です。名前は 3 ～ 24 文字とし、数字と小文字のみを使用できます。 ストレージ アカウントは、[az storage account create](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) コマンドで作成します。 この例では、ランダムな文字列を使って一意のストレージ アカウント名を作成しています。

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

今度は、[az vm boot-diagnostics enable](/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-enable) を使用して、ブート診断を有効にします。 `--storage` の値は、前の手順で取得した BLOB の URI です。

```azurecli-interactive
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```

## <a name="view-boot-diagnostics"></a>ブート診断を表示する

ブート診断が有効になっている場合、VM を停止して起動するたびに、ブート プロセスに関する情報がログ ファイルに書き込まれます。 この例ではまず、次のように [az vm deallocate](/cli/azure/vm?view=azure-cli-latest#az-vm-deallocate) コマンドで VM の割り当てを解除します。

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

次に、[az vm start](/cli/azure/vm?view=azure-cli-latest#az-vm-start) コマンドで VM を起動します。

```azurecli-interactive
az vm start --resource-group myResourceGroupMonitor --name myVM
```

*myVM* のブート診断データは、次のように [az vm boot-diagnostics get-boot-log](/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-get-boot-log) コマンドで取得できます。

```azurecli-interactive
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```

## <a name="view-host-metrics"></a>ホストのメトリックを表示する

Azure には、Linux VM と連動する専用のホストがあります。 メトリックは、そのホストを対象に自動的に収集され、Azure Portal に次のように表示されます。

1. Azure Portal で **[リソース グループ]** を選択し、 **[myResourceGroupMonitor]** を選択します。次に、リソース一覧から **[myVM]** を選択します。
1. ホスト VM の実行状況を確認するには、VM ウィンドウの **[メトリック]** を選択し、 **[利用可能なメトリック]** からいずれかの *[Host]* メトリックを選択します。

    ![ホストのメトリックを表示する](./media/tutorial-monitoring/monitor-host-metrics.png)

## <a name="enable-advanced-monitoring"></a>高度な監視を有効にする

Azure Monitor for VMs を使用した Azure VM の監視を有効にするには、次の手順に従います。

1. Azure Portal で **[リソース グループ]** をクリックし、 **[myResourceGroupMonitor]** を選択して、リソース一覧から **[myVM]** を選択します。

2. [VM] ページの **[監視]** セクションで、 **[Insights (プレビュー)]** を選択します。

3. **[Insights (プレビュー)]** ページで、 **[今すぐ試す]** を選択します。

    ![VM に対して Azure Monitor for VMs を有効にする](../../azure-monitor/insights/media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

4. **[Azure Monitor Insights Onboarding]\(Azure Monitor Insights の配布準備\)** ページで、同じサブスクリプションに既存の Log Analytics ワークスペースがある場合は、ドロップダウン リストでそれを選択します。  

    この一覧では、サブスクリプションで VM がデプロイされている既定のワークスペースと場所が事前に選択されています。 

    >[!NOTE]
    >VM からの監視データを格納するための新しい Log Analytics ワークスペースを作成するには、[Log Analytics ワークスペースの作成](../../azure-monitor/learn/quick-create-workspace.md)に関するページを参照してください。 ワークスペースは、[サポートされているリージョン](../../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions)のいずれかに属している必要があります。

監視を有効にした後、VM のパフォーマンス メトリックが表示されるまでに数分かかることがあります。

![Azure Monitor for VMs の監視デプロイ プロセスを有効にする](../../azure-monitor/insights/media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="view-vm-performance-metrics"></a>VM のパフォーマンス メトリックを表示する

VM 用 Azure Monitor には、仮想マシンがどの程度効果的に実行されているかを判定するのに役立ついくつかの主要業績評価指標 (KPI) を対象とする一連のパフォーマンス グラフが含まれています。 VM からアクセスするには、次の手順を行います。

1. Azure Portal で **[リソース グループ]** をクリックし、 **[myResourceGroupMonitor]** を選択して、リソース一覧から **[myVM]** を選択します。

2. [VM] ページの **[監視]** セクションで、 **[Insights (プレビュー)]** を選択します。

3. **[パフォーマンス]** タブを選択します。

このページには、パフォーマンス使用率グラフだけでなく、検出された各論理ディスクについて、その容量、使用率、およびメジャーごとの全体平均も表示されます。

## <a name="create-alerts"></a>アラートを作成する

特定のパフォーマンス メトリックに基づいてアラートを作成することができます。 平均 CPU 使用率が特定のしきい値を超えたときや、空きディスク領域が特定の容量を下回ったときなどに、アラートによって通知を受け取ることができます。 アラートは、Azure Portal に表示されるほか、電子メールで送信することもできます。 アラートが生成されたことへの対応として、Azure Automation Runbook または Azure Logic Apps をトリガーすることもできます。

平均 CPU 使用率のアラートを作成する例を次に示します。

1. Azure Portal で **[リソース グループ]** をクリックし、 **[myResourceGroupMonitor]** を選択して、リソース一覧から **[myVM]** を選択します。

2. VM ブレードの **[アラート ルール]** をクリックし、アラート ブレード上部にある **[メトリック アラートの追加]** をクリックします。

3. **[名前]** にアラートの名前を入力します (例: *myAlertRule*)。

4. CPU の割合が 1.0 を超えた状態が 5 分間続いたときにアラートをトリガーするために、それ以外の選択肢はすべて既定値のままにします。

5. 必要に応じて *[所有者、共同作成者、閲覧者に電子メールを送信]* のチェック ボックスをオンにして、電子メール通知を送信することもできます。 既定のアクションでは、ポータルに通知が表示されます。

6. **[OK]** をクリックします。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、VM のパフォーマンスを構成して確認しました。 以下の方法を学習しました。

> [!div class="checklist"]
> * リソース グループと VM を作成する
> * VM でブート診断を有効にする
> * ブート診断を表示する
> * ホストのメトリックを表示する
> * Azure Monitor for VMs の有効化
> * VM のメトリックを表示する
> * アラートを作成する

次のチュートリアルに進み、Azure Security Center について学習してください。

> [!div class="nextstepaction"]
> [VM のセキュリティの管理](tutorial-azure-security.md)
