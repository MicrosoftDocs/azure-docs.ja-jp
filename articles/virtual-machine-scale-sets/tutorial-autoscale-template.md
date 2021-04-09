---
title: チュートリアル - Azure テンプレートを使用してスケール セットを自動スケーリングする
description: Azure Resource Manager テンプレートを使用して CPU 需要の増減に合わせて仮想マシンのスケール セットを自動的にスケーリングする方法について説明します
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 03/27/2018
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: 88cec878ca5d3ccab3a232888ff3a3c0b0faa1db
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97705253"
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-an-azure-template"></a>チュートリアル:Azure テンプレートを使用して仮想マシン スケール セットを自動的にスケーリングする
スケール セットを作成するときに、実行する VM インスタンスの数を定義します。 アプリケーションの需要の変化に応じて、VM インスタンスの数を自動的に増減することができます。 自動スケールにより、顧客のニーズに対応したり、アプリのライフサイクル全体でアプリケーション パフォーマンスの変化に対応したりできます。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * スケール セットの自動スケールを使用する
> * 自動スケール ルールを作成および使用する
> * VM インスタンスのストレステストを行い、自動スケール ルールをトリガーする
> * 需要の減少に合わせて元のサイズに自動的にスケーリングする

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0.29 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。 


## <a name="define-an-autoscale-profile"></a>自動スケール プロファイルの定義
Azure テンプレート内で自動スケール プロファイルを定義するには、*Microsoft.insights/autoscalesettings* リソース プロバイダーを使用します。 "*プロファイル*" には、スケール セットの容量の詳細と関連ルールを記述します。 次の例では、*Autoscale by percentage based on CPU usage* という名前のプロファイルを定義し、VM インスタンスの既定容量と最小容量を *2*、最大容量を *10* に設定しています。

```json
{
"type": "Microsoft.insights/autoscalesettings",
"name": "Autoscale",
"apiVersion": "2015-04-01",
"location": "[variables('location')]",
"scale": null,
"properties": {
  "profiles": [
    {
      "name": "Autoscale by percentage based on CPU usage",
      "capacity": {
        "minimum": "2",
        "maximum": "10",
        "default": "2"
      }
    }
  ]
}
```


## <a name="define-a-rule-to-autoscale-out"></a>自動スケールアウト ルールの定義
アプリケーションの需要が増加すると、スケール セット内の VM インスタンスに対する負荷が増加します。 この増加した負荷が短期的な需要ではなく持続したものである場合は、スケール セット内の VM インスタンスの数を増やす自動スケール ルールを構成できます。 これらの VM インスタンスが作成され、アプリケーションがデプロイされると、スケール セットはロード バランサーを通じてそれらへのトラフィックの分散を開始します。 監視するメトリック (CPU やディスクなど)、指定されたしきい値をアプリケーションの負荷が満たす必要がある期間、およびスケール セットに追加する VM インスタンスの数を制御します。

次の例では、CPU に対する負荷の平均が 5 分間に 70% を上回った場合にスケール セット内の VM インスタンスの数を増やすルールを定義します。 ルールがトリガーされると、VM インスタンスの数が 3 つ増えます。

このルールでは、次のパラメーターが使用されます。

| パラメーター         | 説明                                                                                                         | 値           |
|-------------------|---------------------------------------------------------------------------------------------------------------------|-----------------|
| *metricName*      | スケール セット アクションを監視して適用するパフォーマンス メトリック。                                                   | Percentage CPU  |
| *timeGrain*       | 分析のためにメトリックを収集する頻度。                                                                   | 1 分        |
| *timeAggregation* | 分析のために収集したメトリックの集計方法を定義します。                                                | Average         |
| *timeWindow*      | メトリックとしきい値を比較する前に監視する時間。                                   | 5 分       |
| *operator*        | しきい値に対してメトリック データを比較するために使用する演算子。                                                     | より大きい    |
| *threshold*       | 自動スケール ルールがアクションをトリガーする値。                                                      | 70%             |
| *direction*       | ルールが適用されるときにスケール セットをスケールインするかスケールアウトするかを定義します。                                              | Increase (増加)        |
| *type*            | VM インスタンスの数を特定の値で変更することを示します。                                    | 変更数    |
| *value*           | ルールが適用されるときにスケールインまたはスケールアウトする VM インスタンスの数。                                             | 3               |
| *cooldown*        | 自動スケール アクションを有効にする時間を稼ぐため、ルールを再度適用する前に待機する時間。 | 5 分       |

次のルールを、前のセクションの *Microsoft.insights/autoscalesettings* リソース プロバイダーのプロファイル セクションに追加します。

```json
"rules": [
  {
    "metricTrigger": {
      "metricName": "Percentage CPU",
      "metricNamespace": "",
      "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', variables('vmssName'))]",
      "metricResourceLocation": "[variables('location')]",
      "timeGrain": "PT1M",
      "statistic": "Average",
      "timeWindow": "PT5M",
      "timeAggregation": "Average",
      "operator": "GreaterThan",
      "threshold": 70
    },
    "scaleAction": {
      "direction": "Increase",
      "type": "ChangeCount",
      "value": "3",
      "cooldown": "PT5M"
    }
  }
]
```


## <a name="define-a-rule-to-autoscale-in"></a>自動スケールイン ルールの定義
夜間や週末は、アプリケーションの需要が低下する可能性があります。 この低下した負荷が一定期間持続する場合、スケール セット内の VM インスタンスの数を減らす自動スケール ルールを構成できます。 このスケールイン アクションによって、現在の需要を満たすのに必要な数のインスタンスのみが実行されるようになるため、スケール セットの実行コストが削減されます。

次の例では、CPU に対する負荷の平均が 5 分間に 30% を下回った場合に VM インスタンスの数を 1 つスケールインするルールを定義します。 このルールは、自動スケール プロファイル内の、前のスケールアウト ルールの後に追加します。

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', variables('vmssName'))]",
    "metricResourceLocation": "[variables('location')]",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT5M",
    "timeAggregation": "Average",
    "operator": "LessThan",
    "threshold": 30
  },
  "scaleAction": {
    "direction": "Decrease",
    "type": "ChangeCount",
    "value": "1",
    "cooldown": "PT5M"
  }
}
```


## <a name="create-an-autoscaling-scale-set"></a>自動スケール スケール セットの作成
サンプル テンプレートを使用してスケール セットを作成し、自動スケール ルールを適用しましょう。 [完全なテンプレートを確認](https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/autoscale.json)するか、テンプレートの [*Microsoft.insights/autoscalesettings* リソース プロバイダー セクションを確認](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/autoscale.json#L220)してください。

最初に、[az group create](/cli/azure/group) を使用して、リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

ここでは、[az deployment group create](/cli/azure/deployment/group) を使用して仮想マシン スケール セットを作成します。 メッセージが表示されたら、*azureuser* などの独自のユーザー名と、各 VM インスタンスの資格情報として使用するパスワードを入力します。

```azurecli-interactive
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/autoscale.json
```

すべてのスケール セットのリソースと VM を作成および構成するのに数分かかります。


## <a name="generate-cpu-load-on-scale-set"></a>スケール セットに対する CPU 負荷の生成
自動スケール ルールをテストするには、スケール セットの VM インスタンスに対する CPU 負荷を生成します。 このシミュレートされた CPU 負荷を適用すると、自動スケール ルールによってスケールアウトされ、VM インスタンスの数が増えます。 次に、シミュレートされた CPU 負荷を小さくすると、自動スケール ルールによってスケールインされ、VM インスタンスの数が減ります。

最初に、[az vmss list-instance-connection-info](/cli/azure/vmss) を使用して、スケール セット内の VM インスタンスに接続するためのアドレスとポートを一覧表示します。

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

次の出力例には、インスタンス名、ロード バランサーのパブリック IP アドレス、およびネットワーク アドレス変換 (NAT) 規則によってトラフィックが転送されるポート番号が示されています。

```json
{
  "instance 1": "13.92.224.66:50001",
  "instance 3": "13.92.224.66:50003"
}
```

最初の VM インスタンスに SSH 接続します。 `-p` パラメーターを使用して、独自のパブリック IP アドレスとポート番号を、前のコマンドで示されたとおりに指定します。

```console
ssh azureuser@13.92.224.66 -p 50001
```

ログインしたら、**stress** ユーティリティをインストールします。 最初は *10* 個の **stress** worker を使用して CPU 負荷を生成します。 これらの worker は、*420* 秒間実行されます。これは、自動スケール ルールで目的のアクションを実行するのに十分な値です。

```console
sudo apt-get update
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

**stress** から *stress: info: [2688] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd* のような出力が表示されたら、*Enter* キーを押してプロンプトに戻ります。

**stress** によって CPU 負荷が生成されていることを確認するために、**top** ユーティリティを使用してアクティブなシステム負荷を調べます。

```console
top
```

**top** を終了し、VM インスタンスへの接続を閉じます。 **stress** は、VM インスタンスで実行され続けます。

```console
Ctrl-c
exit
```

前の [az vmss list-instance-connection-info](/cli/azure/vmss) によって一覧表示されたポート番号を使用して、2 番目の VM インスタンスに接続します。

```console
ssh azureuser@13.92.224.66 -p 50003
```

**stress** をインストールして実行し、この 2 番目の VM インスタンスで 10 個の worker を起動します。

```console
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

再び **stress** から *stress: info: [2713] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd* のような出力が表示されたら、*Enter* キーを押してプロンプトに戻ります。

2 番目の VM インスタンスへの接続を閉じます。 **stress** は、VM インスタンスで実行され続けます。

```console
exit
```

## <a name="monitor-the-active-autoscale-rules"></a>アクティブな自動スケール ルールの監視
スケール セット内の VM インスタンスの数を監視するには、**watch** を使用します。 各 VM インスタンスで **stress** によって生成された CPU 負荷に対するスケールアウト プロセスが自動スケール ルールによって開始されるまでに 5 分かかります。

```azurecli-interactive
watch az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```

CPU しきい値に達すると、自動スケール ルールによってスケール セット内の VM インスタンスの数が増えます。 次の出力には、スケール セットの自動スケールアウトによって作成された 3 つの VM が示されています。

```output
Every 2.0s: az vmss list-instances --resource-group myResourceGroup --name myScaleSet --output table

  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup    VmId
------------  --------------------  ----------  ------------  -------------------  ---------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUP  4f92f350-2b68-464f-8a01-e5e590557955
           2  True                  eastus      myScaleSet_2  Succeeded            MYRESOURCEGROUP  d734cd3d-fb38-4302-817c-cfe35655d48e
           4  True                  eastus      myScaleSet_4  Creating             MYRESOURCEGROUP  061b4c90-0d73-49fc-a066-19eab0b3d95c
           5  True                  eastus      myScaleSet_5  Creating             MYRESOURCEGROUP  4beff8b9-4e65-40cb-9652-43899309da27
           6  True                  eastus      myScaleSet_6  Creating             MYRESOURCEGROUP  9e4133dd-2c57-490e-ae45-90513ce3b336
```

**stress** が最初の VM インスタンスで停止すると、平均 CPU 負荷は正常に戻ります。 その 5 分後、自動スケール ルールによって VM インスタンスの数がスケールインされます。 スケールイン アクションでは、ID が最も大きな VM インスタンスが最初に削除されます。 スケール セットが可用性セットまたは可用性ゾーンを使用する場合、スケールイン アクションはこれらの VM インスタンスに均等に分散されます。 次の出力例には、スケール セットの自動スケールインによって削除された 1 つの VM インスタンスが示されています。

```output
           6  True                  eastus      myScaleSet_6  Deleting             MYRESOURCEGROUP  9e4133dd-2c57-490e-ae45-90513ce3b336
```

`Ctrl-c` キーを押して *watch* を終了します。 スケール セットは、引き続き 5 分ごとにスケールインされ、その都度 VM インスタンスが 1 つ削除されます。この操作は、VM インスタンスの数が最小数の 2 になるまで繰り返されます。


## <a name="clean-up-resources"></a>リソースをクリーンアップする
スケール セットと追加のリソースを削除するには、[az group delete](/cli/azure/group) を使用して、リソース グループとそのすべてのリソースを削除します。

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>次のステップ
このチュートリアルでは、Azure CLI を使用してスケール セットを自動的にスケールインまたはスケールアウトする方法について学習しました。

> [!div class="checklist"]
> * スケール セットの自動スケールを使用する
> * 自動スケール ルールを作成および使用する
> * VM インスタンスのストレステストを行い、自動スケール ルールをトリガーする
> * 需要の減少に合わせて元のサイズに自動的にスケーリングする
