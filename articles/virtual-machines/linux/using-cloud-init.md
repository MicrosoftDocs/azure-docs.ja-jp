---
title: Azure での Linux VM に対する cloud-init のサポートの概要
description: Azure でのプロビジョニング時に VM を構成する cloud-init 機能の概要。
author: danielsollondon
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/14/2021
ms.author: danis
ms.openlocfilehash: ac907c2ea2ae53bd192c01232c66e0467025daae
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102563235"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Azure での仮想マシンに対する cloud-init のサポート
この記事では、Azure でのプロビジョニング時に仮想マシン (VM) または仮想マシン スケール セットを構成するための [cloud-init](https://cloudinit.readthedocs.io) のサポートについて説明します。 これらの cloud-init 構成は、Azure によってリソースがプロビジョニングされた後の最初の起動時に実行されます。  

VM のプロビジョニングとは、指定した VM Create のパラメーター値 (ホスト名、ユーザー名、パスワードなど) が Azure によって渡され、VM の起動時に使用できるようにするプロセスのことです。 "プロビジョニング エージェント" によって、これらの値を使用して VM が構成され、完了時に報告されます。 

Azure では、2 つのプロビジョニング エージェント [cloud-init](https://cloudinit.readthedocs.io) と [Azure Linux エージェント (WALA)](../extensions/agent-linux.md) がサポートされています。

## <a name="cloud-init-overview"></a>cloud-init の概要
[cloud-Init](https://cloudinit.readthedocs.io) は、Linux VM を初回起動時にカスタマイズするために広く使用されている手法です。 cloud-init を使って、パッケージをインストールしてファイルを書き込んだり、ユーザーとセキュリティを構成したりすることができます。 cloud-init は初回起動プロセスの間に呼び出されるので、構成を適用するために追加の手順や必要なエージェントはありません。  `#cloud-config` ファイルやその他の入力の形式を正しく設定する方法について詳しくは、[cloud-init のドキュメント サイト](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data)をご覧ください。  `#cloud-config` ファイルは、base64 でエンコードされたテキスト ファイルです。

cloud-init はディストリビューション全体でも有効です。 たとえば、パッケージをインストールするときに **apt-get install** や **yum install** は使用しません。 代わりに、cloud-init ではインストールするパッケージの一覧をユーザーが定義できます。 cloud-init によって、選択したディストリビューションに対してネイティブのパッケージ管理ツールが自動的に使用されます。

Microsoft は、動作保証済み Linux ディストリビューションのパートナーと協力して、cloud-init 対応のイメージを Azure Marketplace で利用できるようにする作業を行っています。 これらのイメージによって、cloud-init のデプロイと構成が、VM および仮想マシン スケール セット とシームレスに動作するようになります。 まず、Microsoft では動作保証済み Linux ディストリビューションのパートナーおよび上流と共同作業を行い、Azure 上の OS で cloud-init が機能するようにします。次に、パッケージが更新され、ディストリビューションのパッケージ リポジトリで一般公開されます。 

cloud-init が Azure 上の動作保証済み Linux ディストリビューション OS で利用できるようになり、パッケージ、そしてイメージがサポートされる場合、次の 2 つの段階があります。
* "Azure での cloud-init パッケージ サポート" には、どの cloud-init パッケージ以降がサポートされているか、またはプレビュー段階であるかが記載されています。そのため、カスタム イメージの OS でこれらのパッケージを使用できます。
* "cloud-init 対応イメージ" には、イメージが既に cloud-init を使用するように構成されているかどうかが記載されています。


### <a name="canonical"></a>Canonical
| 発行元 / バージョン| プラン | SKU | Version | cloud-init 対応イメージ | Azure での cloud-init パッケージ サポート|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canonical 20.04 |UbuntuServer |18.04-LTS |latest |はい | はい |
|Canonical 18.04 |UbuntuServer |18.04-LTS |latest |はい | はい |
|Canonical 16.04|UbuntuServer |16.04 LTS |latest |はい | はい |
|Canonical 14.04|UbuntuServer |14.04.5-LTS |latest |はい | はい |

### <a name="rhel"></a>RHEL
| 発行元 / バージョン | プラン | SKU | Version | cloud-init 対応イメージ | Azure での cloud-init パッケージ サポート|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7.6 |RHEL |7-RAW-CI |7.6.2019072418 |はい | はい - 次のパッケージ バージョンからサポート:*18.2-1.el7_6.2*|
|RedHat 7.7 |RHEL |7-RAW-CI |7.7.2019081601 | はい (注: これはプレビュー イメージであり、**必ず** 今後は使用停止してください。これは 2020 年 9 月 1 日に削除されます) | 該当なし |
|RedHat 7.7 (Gen1)|RHEL |7.7 | 7.7.2020051912 | はい | はい - 次のパッケージ バージョンからサポート:*18.5-6.el7*|
|RedHat 7.7 (Gen2)|RHEL | 77-gen2 | 7.7.2020051913 | はい | はい - 次のパッケージ バージョンからサポート:*18.5-6.el7*|
|RedHat 7.7 (Gen1)|RHEL |7-LVM | 7.7.2020051921 | はい | はい - 次のパッケージ バージョンからサポート:*18.5-6.el7*|
|RedHat 7.7 (Gen2)|RHEL | 7lvm-gen2 | 7.7.2020051922  | はい | はい - 次のパッケージ バージョンからサポート:*18.5-6.el7*|
|RedHat 7.7 (Gen1) |rhel-byos | rhel-lvm77 | 7.7.20200416 | はい  | はい - 次のパッケージ バージョンからサポート:*18.5-6.el7*|
|RedHat 8.1 (Gen1) |RHEL |8.1-ci |8.1.2020042511 | はい (注: これはプレビュー イメージであり、すべての RHEL 8.1 イメージで cloud-init がサポートされたら、これは 2020 年 8 月 1 日に削除されます) | いいえ、2020 年 6 月の完全サポートを予定しています|
|RedHat 8.1 (Gen2) |RHEL |81-ci-gen2 |8.1.2020042524 | はい (注: これはプレビュー イメージであり、すべての RHEL 8.1 イメージで cloud-init がサポートされたら、これは 2020 年 8 月 1 日に削除されます) | いいえ、2020 年 6 月の完全サポートを予定しています |

* すべての RedHat: RHEL 7.8 と 8.2 (Gen1 と Gen2) のイメージは、cloud-init を使用してプロビジョニングされます。

### <a name="centos"></a>CentOS

| 発行元 / バージョン | プラン | SKU | Version | cloud-init 対応イメージ | Azure での cloud-init パッケージ サポート|
|:--- |:--- |:--- |:--- |:--- |:--- |
|OpenLogic 7.7 |CentOS |7-CI |7.7.20190920 |はい (注: これはプレビュー イメージであり、**必ず** 今後は使用停止してください。これは 2020 年 9 月 1 日に削除されます) | 該当なし |
|OpenLogic 7.7 |CentOS | 7.7 |7.7.2020062400 |はい | はい - 次のパッケージ バージョンからサポート: `18.5-6.el7.centos.5`|
|OpenLogic 7.7 (Gen2) |CentOS | 7_7-gen2 |7.7.2020062401 |はい | はい - 次のパッケージ バージョンからサポート: `18.5-6.el7.centos.5`|
|OpenLogic 7.7 |CentOS-HPC | 7.7 |7.6.2020062600 |はい | はい - 次のパッケージ バージョンからサポート: `18.5-6.el7.centos.5`|
|OpenLogic 7.7 (Gen2) |CentOS-HPC | 7_7-gen2 |7.6.2020062601 |はい | はい - 次のパッケージ バージョンからサポート: `18.5-6.el7.centos.5`|
|OpenLogic 8.1 |CentOS | 8_1 |8.1.2020062400 |はい | はい - 次のパッケージ バージョンからサポート: `18.5-7.el8_1.1`|
|OpenLogic 8.1 (Gen2) |CentOS | 8_1-gen2 |8.1.2020062401 |はい | はい - 次のパッケージ バージョンからサポート: `18.5-7.el8_1.1`|
|OpenLogic 8.1 |CentOS-HPC | 8_1 |8.1.2020062400 |はい | はい - 次のパッケージ バージョンからサポート: `18.5-7.el8_1.1`|
|OpenLogic 8.1 (Gen2) |CentOS-HPC:8_1-gen2 | 8_1-gen2 |8.1.2020062401 |はい | はい - 次のパッケージ バージョンからサポート: `18.5-7.el8_1.1`|

* すべての OpenLogic:CentOS 7.8 と 8.2 (Gen1 と Gen2) のイメージは、cloud-init を使用してプロビジョニングされます。

### <a name="oracle"></a>Oracle

| 発行元 / バージョン | プラン | SKU | Version | cloud-init 対応イメージ | Azure での cloud-init パッケージ サポート|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Oracle 7.7 |Oracle-Linux |77-ci |7.7.01| プレビュー イメージ (注: これはプレビュー イメージであり、すべての Oracle 7.7 イメージで cloud-init がサポートされると、これは削除されます。2020 年の中頃に通知があります) | いいえ。次のパッケージがプレビュー段階です:*18.5-3.0.1.el7*

### <a name="suse-sles"></a>SUSE SLES
これらの SLES イメージは、cloud-init を使用してプロビジョニングするように更新されました。Gen2 イメージのバリエーションも更新されました。
* suse:sles-15-sp1-{basic/byos/hpc/hpc-byos/chost-byos}:gen1:2020.06.10
* suse:sles-sap-15-sp1:gen1:2020.06.10
* suse:sles-sap-15-sp1-byos:gen1:2020.06.10
* suse:manager-proxy-4-byos:gen1:2020.06.10
* suse:manager-server-4-byos:gen1:2020.06.10
* suse:sles-{byos/sap/sap-byos}:15:2020.06.10
* suse:sles-12-sp5:gen1:2020.06.10
* suse:sles-12-sp5{-byos/basic/hpc-byos/hpc}:gen1:2020.06.10
* suse:sles-{byos/sap/sap-byos}:12-sp4:2020.06.10
* suse:sles-{byos/sap/sap-byos}:12-sp3:2020.06.10
* suse:sles-{byos/sap/sap-byos}:12-sp2:2020.06.10


### <a name="debian"></a>Debian
| 発行元 / バージョン | プラン | SKU | Version | cloud-init 対応イメージ | Azure での cloud-init パッケージ サポート|
|:--- |:--- |:--- |:--- |:--- |:--- |
| debian (Gen1) |debian-10 | 10-cloudinit |cloud-init-preview| はい (注: これはプレビュー イメージであり、**必ず** 今後は使用停止してください。これは 2021 年 1 月 1 日に削除されます) | いいえ、プレビュー段階です。 |
| debian (Gen2) |debian-10 | 10-cloudinit-gen2 |cloud-init-preview| はい (注: これはプレビュー イメージであり、**必ず** 今後は使用停止してください。これは 2021 年 1 月 1 日に削除されます) | いいえ、プレビュー段階です。 |
| debian (Gen1) |debian-10 | 10-cloudinit |10:0.20201013.422| はい | はい - 次のパッケージ バージョンからサポート: `20.2-2~deb10u1` |
| debian (Gen2) |debian-10 | 10-cloudinit-gen2 |0.20201013.422| はい | はい - 次のパッケージ バージョンからサポート: `20.2-2~deb10u1` |


現在、Azure Stack では、cloud-init 対応のイメージのプロビジョニングがサポートされます。

## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>cloud-init と Linux エージェント (WALA) の相違点
WALA は、VM のプロビジョニングと構成および [Azure 拡張機能](../extensions/features-linux.md)の処理に使われる、Azure プラットフォーム固有のエージェントです。 

既存の cloud-init のお客様が各自の現在の cloud-init スクリプトを使用したり、新しいお客様が cloud-init の豊富な構成機能を利用したりできるようにするために、Linux エージェントではなく cloud-init を使うように VM 構成タスクの強化が行われています。 Linux システム構成用の cloud-init スリプトが既にある場合、cloud-init でそれらを処理できるようにするために **追加の設定は必要ありません**。 

cloud-init では Azure 拡張機能を処理できないため、拡張機能を処理するためにはイメージ内に引き続き WALA が必要ですが、そのプロビジョニング コードが無効にされる必要があります。cloud-init によるプロビジョニングに変換中の動作保証済み Linux ディストリビューションのイメージの場合、WALA がインストールされ、適切にセットアップされます。

VM を作成する際、プロビジョニング時に Azure CLI `--custom-data` スイッチを指定しないと、cloud-init または WALA では、VM をプロビジョニングして既定値でデプロイを完了するために必要な最低限の VM プロビジョニング パラメーターが取得されます。  `--custom-data` スイッチを指定して cloud-init の構成を参照した場合、カスタム データに含まれているものはすべて、VM 起動時に cloud-init で利用可能になります。

VM に適用される cloud-init の構成には時間制限がなく、タイムアウトによってデプロイが失敗することはありません。これは WALA には当てはまりません。カスタム データを処理するように WALA の既定値を変更した場合、VM のプロビジョニング合計時間は許容値の 40 分を超えることはできません。超えた場合、VM Create は失敗します。

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
> [!NOTE]
> cloud-init には複数の[入力の種類](https://cloudinit.readthedocs.io/en/latest/topics/format.html)があります。cloud-init では、customData/userData の最初の行を使用して入力を処理する方法が指定されます。たとえば、`#cloud-config` は、コンテンツを cloud-init 構成として処理する必要があることを指定します。


`ctrl-X` キーを押してファイルを終了し、「`y`」と入力してファイルを保存して、`enter` キーを押して終了時にファイル名を確認します。

最後のステップでは、[az vm create](/cli/azure/vm) コマンドで VM を作成します。 

次の例では、*centos74* という名前の VM を作成し、既定のキーの場所にまだ SSH キーが存在しない場合は SSH キーを作成します。 特定のキーのセットを使用するには、`--ssh-key-value` オプションを使用します。  `--custom-data` パラメーターを使用して、cloud-init 構成ファイルを渡します。 現在の作業ディレクトリの外部に構成ファイル *cloud-init.txt* を保存していた場合には、このファイルの完全パスを指定します。 

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

VM が作成されると、Azure CLI はデプロイ固有の情報を表示します。 `publicIpAddress` を書き留めておきます。 このアドレスは、VM へのアクセスに使用されます。  VM が作成され、パッケージがインストールされて、アプリが開始されるには、少し時間がかかります。 Azure CLI がプロンプトに戻った後にも引き続き実行するバック グラウンド タスクがあります。 SSH で VM に接続し、トラブルシューティングのセクションで説明されている手順を使って、cloud-init のログを見ることができます。 

[ARM テンプレートでパラメーター](../../azure-resource-manager/templates/deploy-cli.md#inline-parameters)を渡すことによって、cloud-init 対応の VM をデプロイすることもできます。

## <a name="troubleshooting-cloud-init"></a>cloud-init のトラブルシューティング
VM のプロビジョニングが済むと、cloud-init は `--custom-data` で定義されているすべてのモジュールとスクリプトを実行して、VM を構成します。  構成のエラーまたは漏れをトラブルシューティングする必要がある場合は、 **/var/log/cloud-init.log** にある cloud-init のログで、モジュール名 (たとえば、`disk_setup` や `runcmd`) を検索する必要があります。

> [!NOTE]
> モジュールの障害により、cloud-init 全体の致命的な構成エラーが必ず発生するわけではありません。 たとえば、`runcmd` モジュールを使っている場合は、スクリプトが失敗しても、runcmd モジュールが実行されたため、cloud-init はプロビジョニング成功を報告します。

cloud-init のログについて詳しくは、[cloud-init のドキュメント](https://cloudinit.readthedocs.io/en/latest/topics/logging.html)をご覧ください 

## <a name="next-steps"></a>次のステップ

[cloud-init による問題のトラブルシューティング](cloud-init-troubleshooting.md)。


構成変更の cloud-init の例については、以下のドキュメントをご覧ください。
 
- [VM に他の Linux ユーザーを追加する](cloudinit-add-user.md)
- [初回起動時にパッケージ マネージャーを実行して既存のパッケージを更新する](cloudinit-update-vm.md)
- [VM のローカル ホスト名を変更する](cloudinit-update-vm-hostname.md) 
- [アプリケーション パッケージのインストール、構成ファイルの更新、キーの挿入](tutorial-automate-vm-deployment.md)
