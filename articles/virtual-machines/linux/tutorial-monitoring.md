---
title: "Azure の Linux 仮想マシンの監視 | Microsoft Docs"
description: "Azure の Linux 仮想マシンを対象にブート診断とパフォーマンス メトリックを監視する方法について説明します。"
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
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 3fe8390e88e609b57a462e066f972346f8e8730e
ms.contentlocale: ja-jp
ms.lasthandoff: 08/09/2017

---
# <a name="how-to-monitor-a-linux-virtual-machine-in-azure"></a>Azure の Linux 仮想マシンを監視する方法

Azure の仮想マシン (VM) が正常に実行されていることを確認するためには、ブート診断とパフォーマンス メトリックを確認します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * VM でブート診断を有効にする
> * ブート診断を表示する
> * ホストのメトリックを表示する
> * VM で診断拡張機能を有効にする
> * VM のメトリックを表示する
> * 診断のメトリックに基づくアラートを作成する
> * 高度な監視をセットアップする


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.4 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

## <a name="create-vm"></a>VM を作成する

診断とメトリックの動作を確認するには、VM が必要です。 最初に、[az group create](/cli/azure/group#create) を使用して、リソース グループを作成します。 次の例では、*myResourceGroupMonitor* という名前のリソース グループを場所 *eastus* に作成します。

```azurecli-interactive 
az group create --name myResourceGroupMonitor --location eastus
```

ここで [az vm create](https://docs.microsoft.com/cli/azure/vm#create) を使用して VM を作成します。 次の例では、*myVM* という名前の VM を作成します。

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

今度は、[az vm boot-diagnostics enable](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#enable) を使用して、ブート診断を有効にします。 `--storage` の値は、前の手順で取得した BLOB の URI です。

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

*myVM* のブート診断データは、次のように [az vm boot-diagnostics get-boot-log](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#get-boot-log) コマンドで取得できます。

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

このチュートリアルでは、Azure Security Center で VM を構成して確認しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * VM でブート診断を有効にする
> * ブート診断を表示する
> * ホストのメトリックを表示する
> * VM で診断拡張機能を有効にする
> * VM のメトリックを表示する
> * 診断のメトリックに基づくアラートを作成する
> * 高度な監視をセットアップする

次のチュートリアルに進み、Azure Security Center について学習してください。

> [!div class="nextstepaction"]
> [VM のセキュリティの管理](./tutorial-azure-security.md)
