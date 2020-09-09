---
title: Avere vFXT クラスターの管理 - Azure
description: Avere クラスターの管理方法 - ノードの追加または削除、vFXT クラスターの再起動、停止、または破棄
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 4135bfe528c33a2beaeb21438181deb5b19ad12e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85505496"
---
# <a name="manage-the-avere-vfxt-cluster"></a>Avere vFXT クラスターの管理

Avere vFXT for Azure クラスターのライフサイクルのある時点で、クラスター ノードの追加や、クラスターの起動または再起動が必要になる場合があります。 プロジェクトの完了時に、クラスターを停止して完全に削除する方法を知っておく必要があります。

この記事では、クラスター ノードを追加または削除する方法およびその他の基本的なクラスター操作について説明します。 クラスター設定を変更したり、その動作を監視したりする必要がある場合は、[Avere コントロール パネル](avere-vfxt-cluster-gui.md)を使用します。

管理タスクによっては、次の 3 つのツールのいずれかを使用する必要があります。Avere コントロール パネル、vfxt.py コマンド ライン クラスター管理スクリプト、および Azure portal。

以下のテーブルに、各タスクについて使用できるツールの概要を示します。

| アクション | Avere コントロール パネル | vfxt.py  | Azure portal |
| --- | --- | --- | --- |
| クラスター ノードの追加 | no | はい | no |
| クラスター ノードの削除 | はい | no | no |
| クラスター ノードの停止 | はい (サービスの再開または再起動も可能) | no | ポータルからのノード VM の停止はノード障害として解釈されます |
| 停止したノードの開始 | no | no | はい |
| 1 つのクラスター ノードの破棄 | no | no | はい |
| クラスターの再起動 |  |  |  |
| クラスターの安全なシャットダウンまたは停止 | はい | はい | no |
| クラスターの破棄  | no | はい | はい (ただしデータの整合性は保証されません) |

各ツールの詳細な手順については後述します。

## <a name="about-stopped-instances-in-azure"></a>Azure での停止中のインスタンスについて

任意の Azure VM をシャットダウンまたは停止すると、コンピューティング料金の発生は停止しますが、VM のストレージに対する支払いは続きます。 1 つの VFXT ノードまたは vFXT クラスター全体をシャットダウンして再起動する予定がない場合は、Azure portal を使用して関連する VM を削除する必要があります。

Azure portal では、*停止された* (再起動可能な) ノードは **[停止済み]** の状態で表示されます。 *削除された*ノードは、 **[停止済み (割り当て解除)]** の状態で表示され、計算やストレージの料金は発生しなくなります。

VM を削除する前に、クラスターを停止またはシャットダウンするために、Avere コントロール パネルまたは vfxt.py オプションを使用して、変更されたすべてのデータがキャッシュからバック エンド ストレージに書き込まれたことを確認します。

## <a name="manage-the-cluster-with-avere-control-panel"></a>Avere コントロール パネルでクラスターを管理する

Avere コントロール パネルは次のタスクのために使用できます。

* 個々のノードの停止または再起動
* クラスターからのノードの削除
* クラスター全体の停止または再起動

Avere コントロール パネルではデータの整合性が優先されるため、破壊的な操作を行う前に、変更されたデータをバックエンド ストレージに書き込もうとします。 このため、Azure portal よりも安全なオプションとなります。

Avere コントロール パネルには Web ブラウザーからアクセスします。 支援が必要な場合、[vFXT クラスターへのアクセス](avere-vfxt-cluster-gui.md)の指示に従ってください。

### <a name="manage-nodes-with-avere-control-panel"></a>Avere コントロール パネルでノードを管理する

**[FXT Nodes]\(FXT ノード)** 設定ページには、個々のノードを管理するためのコントロールがあります。

ノードをシャットダウン、再起動、または削除するには、 **[FXT Nodes]\(FXT ノード)** ページの一覧でノードを見つけ、 **[アクション]** 列で適切なボタンをクリックします。

> [!NOTE]
> アクティブ ノード数が変更されたときに IP アドレスがクラスター ノード間で移動することがあります。

詳細については、Avere クラスター設定ガイドの[[クラスター] > [FXT Nodes]\(FXT ノード)](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html#gui-fxt-nodes>) をお読みください。

### <a name="stop-or-reboot-the-cluster-with-avere-control-panel"></a>Avere コントロール パネルでクラスターを停止または再起動する

**[System Maintenance]\(システム メンテナンス)** 設定ページには、クラスター サービスの再起動、クラスターの再起動、またはクラスターの安全な電源停止のためのコマンドがあります。 詳細については、[[管理] > [System Maintenance]\(システム メンテナンス)](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_system_maintenance.html#gui-system-maintenance>) (Avere クラスター設定ガイド) をお読みください。

クラスターのシャットダウンが開始されると、最初に状態メッセージが **[ダッシュ ボード]** タブに表示されます。しばらくすると、メッセージが停止して Avere コントロール パネル セッションが応答を停止します。これはクラスターがシャットダウンしたことを意味します。

## <a name="manage-the-cluster-with-vfxtpy"></a>vfxt.py でクラスターを管理する

vfxt.py は、クラスターの作成と管理のためのコマンドライン ツールです。

vfxt.py は、クラスター コント ローラー VM に事前にインストールされています。 別のシステムにインストールする場合は、<https://github.com/Azure/AvereSDK> にあるドキュメントを参照してください。

vfxt.py スクリプトは、以下のクラスター管理タスクに使用できます。

* クラスターへの新しいノードの追加
* クラスターの停止または開始  
* クラスターの破棄

Avere コントロール パネルと同じように、vfxt.py は、クラスターまたはノードを破棄する前に、変更されたデータがバック エンド ストレージに永続的に保管されるように動作します。 このため、Azure portal よりも安全なオプションとなります。

vfxt.py の使用に関する詳細なガイドが、GitHub の「[Cloud cluster management with vfxt.py](https://github.com/azure/averesdk/blob/master/docs/README.md)」(vfxt.py でのクラウド クラスター管理) で提供されています。

### <a name="add-cluster-nodes-with-vfxtpy"></a>vfxt.py によるクラスター ノードの追加

クラスター コント ローラーには、クラスター ノードを追加するためのサンプル コマンド スクリプトが含まれます。 コントローラーから ``./add-nodes`` を見つけてエディターで開き、クラスター情報を使用してカスタマイズします。

このコマンドを使用するにはクラスターが実行中である必要があります。

次の値を入力します。

* クラスターのリソース グループ名、およびネットワークとストレージ リソースのリソース グループ名 (クラスターと同じリソース グループでない場合)
* クラスターの位置
* クラスター ネットワークとサブネット
* クラスター ノード アクセス ロール (組み込みロール [Avere 演算子](../role-based-access-control/built-in-roles.md#avere-operator)を使用)
* クラスターの管理 IP アドレスと管理パスワード
* 追加するノードの数 (1、2、または 3)
* ノード インスタンスの種類とキャッシュ サイズの値

プロトタイプを使用していない場合は、上記の情報のすべてを含めて、次のようなコマンドを構築する必要があります。

```bash
   vfxt.py --cloud-type azure --from-environment \
   --resource-group GROUP_NAME \
   [--network-resource-group GROUP_NAME --storage-resource-group GROUP_NAME]  \
   --location LOCATION --azure-network NETWORK_NAME --azure-subnet SUBNET_NAME \
   --add-nodes --nodes NODE_COUNT \
   --management-address CLUSTER_IP --admin-password ADMIN_PASSWORD \
   --instance-type TYPE --node-cache-size SIZE \
   --azure-role "Avere Operator" \
   --log ~/vfxt.log
```

詳細については、vfxt.py の使用ガイドの[クラスターへのノードの追加](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#add-nodes-to-a-cluster)に関する記事をお読みください。

### <a name="stop-a-cluster-with-vfxtpy"></a>vfxt.py でクラスターを停止する

```bash
vfxt.py --cloud-type azure --from-environment --stop --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET
```

### <a name="start-a-stopped-cluster-with-vfxtpy"></a>vfxt.py で停止しているクラスターを開始する

```bash
vfxt.py --cloud-type azure --from-environment --start --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --instances INSTANCE1_ID INSTANCE2_ID INSTANCE3_ID ...
```

クラスターが停止しているため、クラスター ノードを指定するためにインスタンスの識別子を渡す必要があります。 詳しくは、vfxt.py 使用ガイドの[変更するクラスターの指定](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#specifying-which-cluster-to-modify)に関する記事をお読みください。

### <a name="destroy-a-cluster-with-vfxtpy"></a>vfxt.py でクラスターを破棄する

```bash
vfxt.py --cloud-type azure --from-environment --destroy --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --management-address ADMIN_IP
```

変更されたデータをクラスター キャッシュから保存しない場合、オプション ``--quick-destroy`` を使用できます。

追加情報については、 [vfxt.py 使用ガイド](<https://github.com/Azure/AvereSDK/blob/master/docs/README.md>)をお読みください。

## <a name="manage-cluster-vms-from-the-azure-portal"></a>Azure portal からのクラスター VM の管理

Azure portal を使用して、クラスター VM を個別に破棄することができますが、クラスターがまず正常にシャットダウンされない場合、データの整合性は保証されません。

Azure portal は、以下のクラスター管理タスクに使用できます。

* 停止した vFXT ノードを開始する
* 個々の vFXT ノードを停止する (クラスターはこれをノード障害として解釈します)
* VFXT クラスターを破棄する (クラスター キャッシュ内の変更されたデータをコア ファイラーに確実に書き込む*必要がない場合*)
* VFXT ノードとその他のクラスター リソースを安全にシャットダウンした後にこれらを完全に削除する

### <a name="restart-vfxt-instances-from-the-azure-portal"></a>Azure portal から vFXT インスタンスを再起動する

停止しているノードを再起動する必要がある場合は、Azure portal を使用する必要があります。 左側のメニューで **[Virtual machines]\(仮想マシン)** を選択し、一覧から VM 名をクリックしてその概要ページを開きます。

概要ページの上部にある **[開始]** ボタンをクリックして、VM を再アクティブ化します。

![停止している VM を開始するためのオプションを示す Azure portal 画面](media/avere-vfxt-start-stopped-incurring-annot.png)

### <a name="delete-cluster-nodes"></a>クラスター ノードを削除する

vFXT クラスターから 1 つのノードを削除し、クラスターの残りの部分を保持する場合は、最初に Avere コントロール パネルで[クラスターからノードを削除](#manage-nodes-with-avere-control-panel)する必要があります。

> [!CAUTION]
> vFXT クラスターからノードを削除せずにノードを削除すると、データが失われる可能性があります。

vFXT ノードとして使用されている 1 つまたは複数のインスタンスを完全に破棄するには、Azure portal を使用します。
左側のメニューで **[Virtual machines]\(仮想マシン)** を選択し、一覧から VM 名をクリックしてその概要ページを開きます。

概要ページの上部にある **[削除]** ボタンをクリックして、VM を完全に破棄します。

この方法を使用すると、クラスター ノードが安全にシャットダウンされた後にクラスター ノードを完全に削除できます。

### <a name="destroy-the-cluster-from-the-azure-portal"></a>Azure portal からクラスターを破棄する

> [!NOTE]
> キャッシュに残っているクライアントの変更内容をバックエンド ストレージに書き込む場合は、vfxt.py の `--destroy` オプションを使用するか、Avere コントロール パネルを使用して、Azure portal でのノード インスタンスを削除する前に、クラスターを完全にシャットダウンします。

Azure portal で削除することにより、ノード インスタンスを完全に破棄できます。 上記の説明に従って 1 つずつ削除できますが、 **[Virtual Machines]\(仮想マシン)** ページを使用してすべてのクラスター VM を検索し、チェック ボックスを使用して選択し、 **[削除]** ボタンをクリックしてそれらを 1 回のアクションで削除できます。

![ポータルの VM の一覧。"cluster" という単語でフィルター処理され、3 ～ 4 つの VM にチェックマークが付いて強調表示されている](media/avere-vfxt-multi-vm-delete.png)

### <a name="delete-additional-cluster-resources-from-the-azure-portal"></a>Azure portal から追加のクラスター リソースを削除する

vFXT クラスター専用の追加のリソースを作成した場合、クラスターの破棄の一部としてそれらを削除したい場合もあります。 必要とするデータを格納している要素や、他のプロジェクトと共有されているすべての項目は、破棄しないでください。

クラスター ノードを削除することに加えて、以下のコンポーネントの削除を検討してください。

* クラスター コントローラー VM
* クラスター ノードに関連付けられているデータ ディスク
* クラスター コンポーネントに関連付けられているネットワーク インターフェイスとパブリック IP
* 仮想ネットワーク
* ストレージ コンテナーとストレージ アカウント (重要なデータがない場合**のみ**)
* 可用性セット

![テスト クラスター用に作成されたリソースを表示する Azure portal の「すべてのリソース」一覧](media/avere-vfxt-all-resources-list.png)

### <a name="delete-a-clusters-resource-group-from-the-azure-portal"></a>Azure portal からクラスターのリソース グループを削除する

クラスター専用のリソース グループを作成した場合は、リソース グループを破棄することによって、クラスターのすべての関連リソースを破棄できます。

> [!Caution]
> グループに値が残っていないことが確実な場合のみ、リソース グループを破棄してください。 たとえば、リソース グループ内の任意のストレージ コンテナーから必要なデータを移動したことを確認します。  

リソース グループを削除するには、ポータルの左側のメニューで **[リソース グループ]** をクリックし、リソース グループのリストをフィルター処理して、該当する vFXT クラスター用に作成したものを見つけます。 リソース グループを選択し、パネルの右側にある 3 つのドットをクリックします。 **[リソース グループの削除]** を選択します。 ポータルは削除してよいか確認を求めます。削除は元に戻せません。

![「リソース グループの削除」操作を表示するリソース グループ](media/avere-vfxt-delete-resource-group.png)
