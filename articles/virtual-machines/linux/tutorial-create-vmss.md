---
title: "Azure で Linux 用の仮想マシン スケール セットを作成する | Microsoft Docs"
description: "仮想マシン スケール セットを使って、Linux VM に高可用性アプリを作成およびデプロイします"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 09/08/2017
ms.author: iainfou
ms.openlocfilehash: 1f54bb04023ad61f4eae51389c6a902a029e9399
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-linux"></a>仮想マシン スケール セットを作成して Linux に高可用性アプリをデプロイする
仮想マシン スケール セットを使用すると、同一の自動スケールの仮想マシンのセットをデプロイおよび管理できます。 スケール セット内の VM の数を手動で拡張したり、CPU などのリソースの使用率、メモリの需要、またはネットワーク トラフィックに基づいて自動的にスケーリングするルールを定義したりできます。 このチュートリアルでは、仮想マシン スケール セットを Azure にデプロイします。 学習内容は次のとおりです。

> [!div class="checklist"]
> * cloud-init を使用して、スケーリングするアプリを作成する
> * 仮想マシン スケール セットを作成する
> * スケール セット内のインスタンスの数を増減させる
> * 自動スケール ルールを作成する
> * スケール セットのインスタンスの接続情報を表示する
> * スケール セット内でデータ ディスクを使用する


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.4 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

## <a name="scale-set-overview"></a>スケール セットの概要
仮想マシン スケール セットを使用すると、同一の自動スケールの仮想マシンのセットをデプロイおよび管理できます。 スケール セット内の VM は、1 つ以上の*配置グループ*内の論理障害ドメインおよび更新ドメインに配布されます。 これらは同じように構成された VM のグループであり、[可用性セット](tutorial-availability-sets.md)に似ています。

スケール セットには必要に応じて VM が作成されます。 スケール セットの VM を追加または削除する方法とタイミングを制御するには、自動スケール ルールを定義します。 これらのルールは、CPU の負荷、メモリの使用量、ネットワーク トラフィックなどのメトリックに基づいて発動できます。

Azure プラットフォーム イメージを使う場合、スケール セットは最大 1,000 個の VM をサポートします。 大量のインストールが必要なワークロードや、VM に大幅なカスタマイズが必要なワークロードについては、[カスタム VM イメージを作成する](tutorial-custom-images.md)という方法もあります。 カスタム イメージを使うと、最大 300 個の VM をスケール セットに作成できます。


## <a name="create-an-app-to-scale"></a>スケーリングするアプリを作成する
運用環境で使う場合は、インストールと構成が済んだアプリケーションを含む[カスタム VM イメージを作成](tutorial-custom-images.md)できます。 このチュートリアルでは、スケール セットの動作をすぐに確認できるように初回起動時に VM をカスタマイズします。

前のチュートリアルでは、cloud-init を使って [Linux 仮想マシンを初回起動時にカスタマイズする方法](tutorial-automate-vm-deployment.md)を説明しました。 同じ cloud-init 構成ファイルを使って、NGINX をインストールし、単純な "Hello World" Node.js アプリを実行することができます。 

現在のシェルで、*cloud-init.txt* というファイルを作成し、次の構成を貼り付けます。 たとえば、ローカル コンピューター上にない Cloud Shell でファイルを作成します。 `sensible-editor cloud-init.txt` を入力し、ファイルを作成して使用可能なエディターの一覧を確認します。 cloud-init ファイル全体 (特に最初の行) が正しくコピーされたことを確認してください。

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
  - path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
  - path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```


## <a name="create-a-scale-set"></a>スケール セットを作成する
スケール セットを作成する前に、[az group create](/cli/azure/group#create) を使ってリソース グループを作成します。 次の例では、*myResourceGroupScaleSet* という名前のリソース グループを場所 *eastus* に作成します。

```azurecli-interactive 
az group create --name myResourceGroupScaleSet --location eastus
```

ここでは、[az vmss create](/cli/azure/vmss#create) を使って仮想マシン スケール セットを作成します。 以下の例では、*myScaleSet* という名前のスケール セットを作成し、cloud-init ファイルを使って VM をカスタマイズして、存在しない場合は SSH キーを生成します。

```azurecli-interactive 
az vmss create \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```

すべてのスケール セットのリソースと VM を作成および構成するのに数分かかります。 Azure CLI がプロンプトに戻った後にも引き続き実行するバック グラウンド タスクがあります。 アプリにアクセスできるようになるには、さらに数分かかる場合があります。


## <a name="allow-web-traffic"></a>Web トラフィックを許可する
仮想マシン スケール セットの一部として、ロード バランサーが自動的に作成されました。 ロード バランサーはロード バランサー ルールを使用して定義した VM のセット全体にトラフィックを分散します。 ロード バランサーの概念と構成につい詳しくは、次のチュートリアル「[Azure の Linux 仮想マシンを負荷分散して高可用性アプリケーションを作成する方法](tutorial-load-balancer.md)」をご覧ください。

トラフィックが Web アプリに到達することを許可するには、[az network lb rule creat](/cli/azure/network/lb/rule#create) を使ってルールを作成します。 次の例では、*myLoadBalancerRuleWeb* という名前の規則を作成します。

```azurecli-interactive 
az network lb rule create \
  --resource-group myResourceGroupScaleSet \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

## <a name="test-your-app"></a>アプリケーションをテストする
Web 上の Node.js アプリを確認するには、[az network public-ip show](/cli/azure/network/public-ip#show) でロード バランサーのパブリック IP アドレスを取得します。 次の例では、スケール セットの一部として作成された *myScaleSetLBPublicIP* の IP アドレスを取得します。

```azurecli-interactive 
az network public-ip show \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSetLBPublicIP \
    --query [ipAddress] \
    --output tsv
```

このパブリック IP アドレスを Web ブラウザーに入力します。 アプリが表示され、ロード バランサーによって負荷分散されたトラフィックの宛先となった VM のホスト名が表示されます。

![実行中の Node.js アプリ](./media/tutorial-create-vmss/running-nodejs-app.png)

動作しているスケール セットを確認するには、Web ブラウザーを強制的に最新の情報に更新して、アプリが動作しているすべての VM に対してロード バランサーからトラフィックが負荷分散されているのを見ることができます。


## <a name="management-tasks"></a>管理タスク
スケール セットのライフサイクルを通じて、1 つ以上の管理タスクを実行する必要がある場合があります。 さらに、各種ライフサイクルのタスクを自動化するスクリプトを作成するほうが便利な場合もあります。 Azure CLI 2.0 には、これらのタスクを簡単に実行するための方法が用意されています。 一般的なタスクには、次のようなものがあります。

### <a name="view-vms-in-a-scale-set"></a>スケール セットの VM を表示する
スケール セットで実行されている VM の一覧を表示するには、[az vmss list-instances](/cli/azure/vmss#list-instances) を次のように使います。

```azurecli-interactive 
az vmss list-instances \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --output table
```

出力は次の例のようになります。

```azurecli-interactive 
  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup            VmId
------------  --------------------  ----------  ------------  -------------------  -----------------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUPSCALESET  c72ddc34-6c41-4a53-b89e-dd24f27b30ab
           3  True                  eastus      myScaleSet_3  Succeeded            MYRESOURCEGROUPSCALESET  44266022-65c3-49c5-92dd-88ffa64f95da
```


### <a name="increase-or-decrease-vm-instances"></a>VM インスタンスを増減させる
現時点でスケール セットに存在するインスタンスの数を確認するには、[az vmss show](/cli/azure/vmss#show) と *sku.capacity* に対するクエリを使います。

```azurecli-interactive 
az vmss show \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --query [sku.capacity] \
    --output table
```

[az vmss scale](/cli/azure/vmss#scale) を使って、スケール セット内の仮想マシンの数を手動で増減させることができます。 次の例では、スケール セット内の VM の数を *5* に設定します。

```azurecli-interactive 
az vmss scale \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --new-capacity 5
```


### <a name="configure-autoscale-rules"></a>自動スケール ルールを構成する
スケール セット内のインスタンスの数を手動でスケールする代わりに、自動スケール ルールを定義しておくこともできます。 自動スケール ルールを使うと、スケール セット内のインスタンスが監視され、定義しておいたメトリックとしきい値に基づいて適切な対応が自動で実行されます。 以下の例は、CPU に対する負荷の平均が 5 分間に 60% を上回った場合にインスタンスの数を 1 つ増やしてスケールアウトするためのものです。 このコードではほかにも、CPU に対する負荷の平均が 5 分間に 30% を下回った場合に、インスタンスを 1 つ減らしてスケールインすることが指定されています。 サブスクリプション ID を使用して、さまざまなスケール セット コンポーネントのリソース URI を構築します。 [az monitor autoscale-settings create](/cli/azure/monitor/autoscale-settings#create) でこれらのルールを作成するには、次の autoscale コマンド プロファイルをコピーして貼り付けます。

```azurecli-interactive 
sub=$(az account show --query id -o tsv)

az monitor autoscale-settings create \
    --resource-group myResourceGroupScaleSet \
    --name autoscale \
    --parameters '{"autoscale_setting_resource_name": "autoscale",
      "enabled": true,
      "location": "East US",
      "notifications": [],
      "profiles": [
        {
          "name": "Auto created scale condition",
          "capacity": {
            "minimum": "2",
            "maximum": "10",
            "default": "2"
          },
          "rules": [
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/myResourceGroupScaleSet/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet",
                "metricResourceLocation": "eastus",
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
                "value": "1",
                "cooldown": "PT5M"
              }
            },
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/myResourceGroupScaleSet/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet",
                "metricResourceLocation": "eastus",
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
          ]
        }
      ],
      "tags": {},
      "target_resource_uri": "/subscriptions/'$sub'/resourceGroups/myResourceGroupScaleSet/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet"
    }'
```

自動スケール プロファイルを再利用するには、JSON (JavaScript Object Notation) ファイルを作成し、`--parameters @autoscale.json` パラメーターで `az monitor autoscale-settings create` コマンドに渡します。 自動スケールの使用に関する詳しい設計情報については、[自動スケールのベスト プラクティス](/azure/architecture/best-practices/auto-scaling)に関する記事をご覧ください。


### <a name="get-connection-info"></a>接続情報を取得する
スケール セット内の VM に関する接続情報を取得するには、[az vmss list-instance-connection-info](/cli/azure/vmss#list-instance-connection-info) を使用します。 このコマンドでは、SSH での接続を許可する各 VM のパブリック IP アドレスとポートが出力されます。

```azurecli-interactive 
az vmss list-instance-connection-info \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet
```


## <a name="use-data-disks-with-scale-sets"></a>スケール セットでデータ ディスクを使用する
データ ディスクを作成し、スケール セットで使用できます。 少し前のチュートリアルでは、[Azure ディスクを管理する](tutorial-manage-disks.md)方法を説明しました。具体的には、OS ディスクではなくデータ ディスクにアプリを構築する際のベスト プラクティスとパフォーマンス改善のためのヒントを簡単に紹介しました。

### <a name="create-scale-set-with-data-disks"></a>データ ディスクを備えたスケール セットを作成する
スケール セットを作成してデータ ディスクをアタッチするには、[az vmss create](/cli/azure/vmss#create) コマンドに `--data-disk-sizes-gb` パラメーターを追加します。 次の例では、各インスタンスに *50* Gb のデータ ディスクがアタッチされたスケール セットを作成します。

```azurecli-interactive 
az vmss create \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSetDisks \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --custom-data cloud-init.txt \
    --admin-username azureuser \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50
```

インスタンスがスケール セットから削除されると、アタッチされているデータ ディスクも削除されます。

### <a name="add-data-disks"></a>データ ディスクを追加する
スケール セット内のインスタンスにデータ ディスクを追加するには、[az vmss disk attach](/cli/azure/vmss/disk#attach) を使用します。 次の例では、各インスタンスに *50* Gb のディスクを追加します。

```azurecli-interactive 
az vmss disk attach \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --size-gb 50 \
    --lun 2
```

### <a name="detach-data-disks"></a>データ ディスクをデタッチする
スケール セット内のインスタンスからデータ ディスクを削除するには、[az vmss disk detach](/cli/azure/vmss/disk#detach) を使用します。 以下の例では、各インスタンスから LUN *2* のデータ ディスクを削除します。

```azurecli-interactive 
az vmss disk detach \
    --resource-group myResourceGroupScaleSet \
    --name myScaleSet \
    --lun 2
```


## <a name="next-steps"></a>次のステップ
このチュートリアルでは、仮想マシン スケール セットを作成しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * cloud-init を使用して、スケーリングするアプリを作成する
> * 仮想マシン スケール セットを作成する
> * スケール セット内のインスタンスの数を増減させる
> * 自動スケール ルールを作成する
> * スケール セットのインスタンスの接続情報を表示する
> * スケール セット内でデータ ディスクを使用する

次のチュートリアルでは、仮想マシンでの負荷分散の概念について詳しく説明します。

> [!div class="nextstepaction"]
> [仮想マシンを負荷分散する](tutorial-load-balancer.md)
