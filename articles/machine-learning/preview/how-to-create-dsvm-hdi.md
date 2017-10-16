---
title: "Azure ML のコンピューティング ターゲットとして DSVM と HDI を作成する方法"
description: "Azure ML 実験用のコンピューティング ターゲットとして DSVM と HDI Spark クラスターを作成します。"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/26/2017
ms.openlocfilehash: 7325d15cc4bec5f0df50be222e1f0988775ded2c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-dsvm-and-hdi-spark-cluster-as-compute-targets"></a>コンピューティング ターゲットとしての DSVM および HDI Spark クラスターの作成

Ubuntu ベースの DSVM (データ サイエンス仮想マシン)、Azure HDInsight クラスターの Apache Spark などのコンピューティング ターゲットを追加することで、Machine Learning 実験を簡単にスケール アップまたはスケール アウトすることができます。 この記事は、Azure 内でこれらのコンピューティング ターゲットを作成する手順を説明します。 Azure ML コンピューティング ターゲットの詳細については、「[Azure Machine Learning 実験実行サービスの概要](experiment-execution-configuration.md)」を参照してください。

>[!NOTE]
>続行する前に、Azure で VM と HDI クラスターなどのリソースを作成するための適切なアクセス許可があることを確認する必要があります。 また、この両方のリソースは、構成に応じて、多くのコンピューティング コアを使用できます。 ご使用のサブスクリプションに仮想 CPU コア用の十分な容量があることを確認してください。 ご使用のサブスクリプションで許可されているコアの最大数を増やす場合は、いつでも Azure サポートにご連絡いただけます。

## <a name="create-an-ubuntu-dsvm-in-azure-portal"></a>Azure Portal で Ubuntu DSVM を作成する

Azure Portal から DSVM を作成できます。 

1. https://portal.azure.com から Azure Portal にログオンします。
2. **[+ 新規]** リンクをクリックして、「Linux 用データ サイエンス仮想マシン」を検索します。
    ![Ubuntu](media/how-to-create-dsvm-hdi/ubuntu_dsvm.png)
4. 一覧で **[Data Science Virtual Machine for Linux (Ubuntu)]\(Linux 用データ サイエンス仮想マシン (Ubuntu)\)** を選択し、画面の指示に従って DSVM を作成します。

>[!IMPORTANT]
>_[認証の種類]_ として必ず **[パスワード]** を選択します。

![use pwd](media/how-to-create-dsvm-hdi/use_pwd.png)

## <a name="create-an-ubuntu-dsvm-using-azure-cli"></a>azure-cli を使用して Ubuntu DSVM を作成する

Azure リソース管理テンプレートを使用して DSVM をデプロイすることもできます。

>[!NOTE]
>次のコマンドはすべて、Azure ML プロジェクトのルート フォルダーから発行することを前提としています。

最初に、使い慣れたテキスト エディターを使用して、`docs` フォルダーに `mydsvm.json` ファイルを作成します  (`docs` フォルダーがプロジェクト ルート フォルダーにない場合は、このフォルダーを作成します)。このファイルを使用して、Azure リソース管理テンプレートの基本パラメーターを構成します。 

次の JSON スニペットをコピーして `mydsvm.json` ファイルに貼り付け、適切な値を入力します。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "adminUsername": { "value" : "<admin username>"},
     "adminPassword": { "value" : "<admin password>"},
     "vmName": { "value" : "<vm name>"},
     "vmSize": { "value" : "<vm size>"}
  }
}
```

_[vmSize]_ フィールドには、[Ubuntu DSVM Azure リソース管理テンプレート](https://github.com/Azure/DataScienceVM/blob/master/Scripts/CreateDSVM/Ubuntu/multiazuredeploywithext.json)に記載されている、サポートされている任意の VM サイズを使用することができます。 Azure ML のコンピューティング ターゲットとして次のいずれかのサイズを使用することをお勧めします。 

- Standard_DS2_v2 
- Standard_DS3_v2 
- Standard_DS4_v2 
- Standard_DS12_v2 
- Standard_DS13_v2 
- Standard_DS14_v2 
- Standard_NC6 
- Standard_NC12 
- Standard_NC24 
 
>[!TIP]
> "NC" で始まる VM サイズが、GPU が搭載されるサイズです。

詳細については、「[Azure の Linux 仮想マシンのサイズ](../../virtual-machines/linux/sizes.md)」とその[価格情報](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)を参照してください。

**[ファイル]** --> **[コマンド プロンプトを開く]**、または **[Open PowerShell]\(PowerShell を開く\)** の順にメニュー項目をクリックして、Azure ML Workbench アプリから CLI ウィンドウを起動します。 

>[!NOTE]
>この操作は、az-cli がインストールされている任意のコマンドライン環境でも実行できます。

コマンドラインウィンドウに、以下のコマンドを入力します。

```azurecli
# first make sure you have a valid Azure authentication token
$ az account get-access-token

# if you don't have a valid token, please log in to Azure first. 
# if you already do, you can skip this step.
$ az login

# list all subscriptions you have access to
$ az account list -o table

# make sure you set the subscription you want to use to create DSVM as the current subscription
$ az account set -s <subscription name or Id>

# it is always a good idea to create a resource group for the VM and associated resources to live in.
# you can use any Azure region, but it is best to create them in the region where your Azure ML Experimentation account is, e.g. eastus2, westcentralus or australiaeast.
# also, only certain Azure regions has GPU-equipped VMs available.
$ az group create -n <resource group name> -l <azure region>

# now let's create the DSVM based on the JSON configuration file you created earlier.
# note we assume the mydsvm.json config file is placed in the "docs" sub-folder.
$ az group deployment create -g <resource group name> --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/azuredeploy.json --parameters @docs/mydsvm.json

# find the FQDN (fully qualified domain name) of the VM just created. 
# you can also use IP address from the next command if FQDN is not set.
$ az vm show -g <resource group name> -n <vm name> --query "fqdns"

# find the IP address of the VM just created
$ az vm show -g <resource group name> -n <vm name> --query "publicIps"
```
## <a name="attach-a-dsvm-compute-target"></a>DSVM コンピューティング ターゲットをアタッチする
DSVM を作成したら、これを Azure ML プロジェクトにアタッチできます。

```azurecli
# attach the DSVM compute target
# it is a good idea to use FQDN in case the IP address changes after you deallocate the VM and restart it
$ az ml computetarget attach --name <compute target name> --address <ip address or FQDN> --username <admin username> --password <admin password> --type remotedocker

# prepare the Docker image on the DSVM 
$ az ml experiment prepare -c <compute target name>
```
これで、この DSVM で実験を実行する準備が整いました。

## <a name="deallocate-a-dsvm-and-restart-it-later"></a>DSVM の割り当てを解除して後で再起動する
Azure ML のコンピューティング タスクを完了したら、DSVM の割り当てを解除できます。 このアクションによって VM がシャット ダウンされ、コンピューティング リソースがリリースされますが、仮想ディスクは保持されます。 VM が割り当て解除済み状態のときは、コンピューティング コストは課金されません。

VM を割り当て解除するには

```azurecli
$ az vm deallocate -g <resource group name> -n <vm name>
```

VM を元に戻すには、`az ml start` コマンドを使用します。

```azurecli
$ az vm start -g <resource group name> -n <vm name>
```

## <a name="expand-the-dsvm-os-disk"></a>DSVM OS ディスクを拡張する
Azure の Linux VM は通常、30 GB のオペレーティング システム ディスクと共に提供されます。 Azure ML のコンピューティング ターゲットとして使用すると、Docker イメージを取得し、その上に conda レイヤーを構築する Docker エンジンによって、短時間で使い果たされることがあります。 実行の途中で「ディスクがいっぱいです」というエラーが発生するのを回避するために、OS ディスクを大きなサイズ (200 GB など) に拡張することをお勧めします。 azure-cli から簡単にこの操作を行う方法については、「[Azure CLI を使用して Linux VM の仮想ハード ディスクを拡張する方法](../../virtual-machines/linux/expand-disks.md)」を参照してください。 

## <a name="create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal"></a>Azure Portal で Azure HDInsight クラスターの Apache Spark を作成する

スケール アウト Spark ジョブを実行するには、Azure Portal で Azure HDInsight クラスターの Apache Spark を作成する必要があります。

1. https://portal.azure.com から Azure Portal にログオンします。
2. **[+ 新規]** リンクをクリックして、"HDInsight" を検索します。

    ![HDI を検索する](media/how-to-create-dsvm-hdi/hdi.png)
    
3. 一覧で **[HDInsight]** を選択し、**[作成]** をクリックします。
4. **[基本]** 構成画面の **[クラスターの種類]** 設定で、_[クラスターの種類]_ として **[Spark]** を、_[オペレーティング システム]_ として **[Linux]** を、_[バージョン]_ として **[Spark 2.1.0 (HDI 3.6)]** を必ず選択します。

    ![HDI を構成する](media/how-to-create-dsvm-hdi/configure_hdi.png)

    >[!IMPORTANT]
    >上記の画面で、クラスターに _[クラスター ログイン ユーザー名]_ フィールドおよび _[Secure Shell (SSH) ユーザー名]_ フィールドがあることに注目してください。 両方のログインに便宜上同じパスワードを指定できたとしても、この 2 つのユーザー ID は異なるものです。 "_クラスター ログイン ユーザー名_" は、HDI クラスターの管理 Web UI へのログインに使用します。 "_SSH ログイン ユーザー名_" は、クラスターのヘッド ノードへのログインに使用します。これは、Spark ジョブをディスパッチするために Azure ML で必要です。

5. 必要なクラスター サイズとノード サイズを選択し、作成ウィザードを完了します。 プロビジョニングが完了するまでに、最大で 30 分かかることがあります。 

## <a name="attach-an-hdi-spark-cluster-compute-target"></a>HDI Spark クラスター コンピューティング ターゲットをアタッチする

Spark HDI クラスターが作成されたら、そのクラスターを Azure ML プロジェクトにアタッチできます。

```azurecli
# attach the HDI compute target
$ az ml computetarget attach --name <compute target name> --address <cluster name, such as myhdicluster123.azurehdinsight.net> --username <ssh username> --password <ssh password> --type cluster

# prepare the conda environment on HDI
$ az ml experiment prepare -c <compute target name>
```
これで、この Spark クラスター上で実験を実行する準備が整いました。

## <a name="next-steps"></a>次のステップ

各項目の詳細情報
- [Azure Machine Learning の実験実行サービスの概要](experiment-execution-configuration.md)
- [Azure Machine Learning Workbench 実行構成ファイル](experiment-execution-configuration-reference.md)
- [Apache Spark for Azure HDInsight クラスター](https://azure.microsoft.com/services/hdinsight/apache-spark/)
- [データ サイエンス仮想マシン](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)
