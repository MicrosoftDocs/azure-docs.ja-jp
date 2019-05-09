---
title: Azure での Linux 仮想マシンに対する cloud-init のサポートの概要 | Microsoft Docs
description: Microsoft Azure での cloud-init 機能の概要
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 6dd1dd0ce2395e2b06d80385ffd299835a280526
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58002023"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Azure での仮想マシンに対する cloud-init のサポート
この記事では、Azure でのプロビジョニング時に仮想マシン (VM) または仮想マシン スケール セット (VMSS) を構成するための [cloud-init](https://cloudinit.readthedocs.io) のサポートについて説明します。 これらの cloud-init スクリプトは、Azure によってリソースがプロビジョニングされた後の最初の起動時に実行されます。  

## <a name="cloud-init-overview"></a>cloud-init の概要
[cloud-Init](https://cloudinit.readthedocs.io) は、Linux VM を初回起動時にカスタマイズするために広く使用されているアプローチです。 cloud-init を使って、パッケージをインストールしてファイルを書き込んだり、ユーザーとセキュリティを構成したりすることができます。 cloud-init は初回起動プロセスの間に呼び出されるので、構成を適用するために追加の手順や必要なエージェントはありません。  `#cloud-config` ファイルの形式を正しく設定する方法について詳しくは、[cloud-init のドキュメント サイト](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data)をご覧ください。  `#cloud-config` ファイルは、base64 でエンコードされたテキスト ファイルです。

cloud-init はディストリビューション全体でも有効です。 たとえば、パッケージをインストールするときに **apt-get install** や **yum install** は使用しません。 代わりに、cloud-init ではインストールするパッケージの一覧をユーザーが定義できます。 cloud-init によって、選択したディストリビューションに対してネイティブのパッケージ管理ツールが自動的に使用されます。

 Microsoft は、動作保証済み Linux ディストリビューションのパートナーと協力して、cloud-init 対応のイメージを Azure Marketplace で利用できるようにする作業を行っています。 これらのイメージでは、cloud-init のデプロイと構成が VM および VM Scale Sets (VMSS) とシームレスに動作するようになります。 次の表は、現時点において Azure プラットフォームで利用できる cloud-init 対応イメージの概要を示したものです。

| Publisher | プラン | SKU | Version | cloud-init 対応 |
|:--- |:--- |:--- |:--- |:--- |
|Canonical |UbuntuServer |18.04-LTS |latest |はい | 
|Canonical |UbuntuServer |17.10 |latest |はい | 
|Canonical |UbuntuServer |16.04 LTS |latest |はい | 
|Canonical |UbuntuServer |14.04.5-LTS |latest |はい |
|CoreOS |CoreOS |安定版 |latest |はい |
|OpenLogic |CentOS |7-CI |latest |preview |
|RedHat |RHEL |7-RAW-CI |latest |preview |

現時点では、Azure Stack は cloud-init を使用した RHEL 7.4 および CentOS 7.4 のプロビジョニングをサポートしません。

## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>cloud-init と Linux エージェント (WALA) の相違点
WALA は、VM のプロビジョニングと構成および Azure 拡張機能の処理に使われる、Azure プラットフォーム固有のエージェントです。 既存の cloud-init のお客様が現在の cloud-init スクリプトを使用できるよう、Linux エージェントではなく cloud-init を使うように VM 構成タスクの拡張を行っています。  Linux システム構成用の cloud-init スリプトが既にある場合、それを有効にするために**追加の設定は必要ありません**。 

プロビジョニング時に Azure CLI `--custom-data` スイッチを指定しないと、WALA は、VM をプロビジョニングして既定値でデプロイを完了するために必要な最低限の VM プロビジョニング パラメーターを取得します。  cloud-init の `--custom-data` スイッチを指定すると、カスタム データに含まれるすべてのもので (個別の設定または完全なスクリプト)、WALA の既定値がオーバーライドされます。 

VM の WALA による構成は、VM の最大プロビジョニング時間内で行われるように期間が制限されています。  VM に適用される cloud-init の構成には時間制限がなく、タイムアウトによってデプロイが失敗することはありません。 

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>cloud-init 対応の仮想マシンのデプロイ
cloud-init 対応の仮想マシンのデプロイは簡単であり、デプロイの間に cloud-init が有効なディストリビューションを参照するだけです。  Linux ディストリビューションのメンテナンス担当者は、cloud-init を有効にし、Azure で発行された基本イメージに cloud-init を統合する必要があります。 デプロイするイメージが cloud-init 対応であることを確認した後は、Azure CLI を使ってイメージをデプロイできます。 

このイメージをデプロイする最初のステップは、[az group create](/cli/azure/group) コマンドでリソース グループを作成することです。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 

次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
次に、現在のシェルで *cloud-init.txt* という名前のファイルを作成し、次の構成を貼り付けます。 この例では、ローカル コンピューター上にない Cloud Shell でファイルを作成します。 任意のエディターを使用することができます。 `sensible-editor cloud-init.txt` を入力し、ファイルを作成して使用可能なエディターの一覧を確認します。 **nano** エディターを使用するには #1 を選びます。 cloud-init ファイル全体 (特に最初の行) が正しくコピーされたことを確認してください。

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
`ctrl-X` キーを押してファイルを終了し、「`y`」と入力してファイルを保存して、`enter` キーを押して終了時にファイル名を確認します。

最後のステップでは、[az vm create](/cli/azure/vm) コマンドで VM を作成します。 

次の例では、*centos74* という名前の VM を作成し、既定のキーの場所にまだ SSH キーが存在しない場合は SSH キーを作成します。 特定のキーのセットを使用するには、`--ssh-key-value` オプションを使用します。  `--custom-data` パラメーターを使用して、cloud-init 構成ファイルを渡します。 現在の作業ディレクトリの外部に構成ファイル *cloud-init.txt* を保存していた場合には、このファイルの完全パスを指定します。 次の例では、*centos74* という名前の VM を作成します。

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

VM が作成されると、Azure CLI はデプロイ固有の情報を表示します。 `publicIpAddress` を書き留めておきます。 このアドレスは、VM へのアクセスに使用されます。  VM が作成され、パッケージがインストールされて、アプリが開始されるには、少し時間がかかります。 Azure CLI がプロンプトに戻った後にも引き続き実行するバック グラウンド タスクがあります。 SSH で VM に接続し、トラブルシューティングのセクションで説明されている手順を使って、cloud-init のログを見ることができます。 

## <a name="troubleshooting-cloud-init"></a>cloud-init のトラブルシューティング
VM のプロビジョニングが済むと、cloud-init は `--custom-data` で定義されているすべてのモジュールとスクリプトを実行して、VM を構成します。  構成のエラーまたは漏れをトラブルシューティングする必要がある場合は、**/var/log/cloud-init.log** にある cloud-init のログで、モジュール名 (たとえば、`disk_setup` や `runcmd`) を検索する必要があります。

> [!NOTE]
> モジュールの障害により、cloud-init 全体の致命的な構成エラーが必ず発生するわけではありません。 たとえば、`runcmd` モジュールを使っている場合は、スクリプトが失敗しても、runcmd モジュールが実行されたため、cloud-init はプロビジョニング成功を報告します。

cloud-init のログについて詳しくは、[cloud-init のドキュメント](https://cloudinit.readthedocs.io/en/latest/topics/logging.html)をご覧ください 

## <a name="next-steps"></a>次の手順
構成変更の cloud-init の例については、以下のドキュメントをご覧ください。
 
- [VM に他の Linux ユーザーを追加する](cloudinit-add-user.md)
- [初回起動時にパッケージ マネージャーを実行して既存のパッケージを更新する](cloudinit-update-vm.md)
- [VM のローカル ホスト名を変更する](cloudinit-update-vm-hostname.md) 
- [アプリケーション パッケージのインストール、構成ファイルの更新、キーの挿入](tutorial-automate-vm-deployment.md)
