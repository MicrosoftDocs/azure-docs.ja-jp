---
title: Azure portal での Azure HANA Large Instances の制御 | Microsoft Docs
description: ポータルで Azure HANA Large Instances を識別して操作する方法について説明します
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2d64fe6c244ffcb6da2926dfea6efaa6da315727
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234468"
---
# <a name="azure-hana-large-instances-control-through-azure-portal"></a>Azure portal を介した Azure HANA L インスタンスの制御
このドキュメントでは、[HANA Large Instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) が [Azure portal](https://portal.azure.com) で提供される方法、および自動的にデプロイされる HANA Large Instance ユニットについて Azure portal で実行できるアクティビティについて説明します。 Azure portal での HANA Large Instances の表示は、HANA Large Instances 用の Azure リソース プロバイダー (現在はパブリック プレビュー) によって提供されます

## <a name="register-hana-large-instance-resource-provider"></a>HANA Large Instance リソース プロバイダーを登録する
通常、HANA Large Instance のデプロイに使用した Azure サブスクリプションは、HANA Large Instance リソース プロバイダーに登録されます。 ただし、HANA Large Instance ユニットをデプロイしたことを確認できない場合は、Azure サブスクリプションにリソース プロバイダーを登録する必要があります。 HANA Large Instance リソース プロバイダーを登録するには 2 つの方法があります。

### <a name="register-through-cli-interface"></a>CLI インターフェイスを使用して登録する
Azure CLI インターフェイスを介した HANA Large Instance のデプロイに使用する Azure サブスクリプションにログインする必要があります。 次のコマンドで、HANA Large Instance プロバイダーを (再) 登録することができます。
    
    az provider register --namespace Microsoft.HanaOnAzure

詳細については、「[Azure リソース プロバイダーと種類](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-cli)」の記事を参照してください。


### <a name="register-through-azure-portal"></a>Azure portal を使用して登録する
Azure portal を使用して HANA Large Instance リソース プロバイダーを (再) 登録できます。 Azure portal でサブスクリプションを一覧表示し、HANA Large Instance ユニットのデプロイに使用したサブスクリプションをダブル クリックする必要があります。 サブスクリプションの概要ページが表示されたら、次のように [リソース プロバイダー] を選択し、検索ウィンドウに「HANA」と入力します。 

![Azure portal を使用して HLI RP を登録する](./media/hana-li-portal/portal-register-hli-rp.png)

このスクリーンショットでは、リソース プロバイダーは既に登録されています。 リソース プロバイダーがまだ登録されていない場合は、[再登録] または [登録] を押します。

詳細については、「[Azure リソース プロバイダーと種類](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#azure-powershell)」の記事を参照してください。


## <a name="display-of-hana-large-instance-units-in-the-azure-portal"></a>Azure portal での HANA Large Instance ユニットの表示
HANA Large Instance のデプロイ要求を送信するときは、HANA Large Instances にも接続している Azure サブスクリプションを指定するように求められます。 HANA Large Instance ユニットに対して動作する SAP アプリケーション レイヤーのデプロイに使っているのと同じサブスクリプションを使うことをお勧めします。
最初の HANA Large Instances がデプロイされると、HANA Large Instances のデプロイ要求で送信した Azure サブスクリプションに、新しい [Azure リソース グループ](https://docs.microsoft.com/azure/azure-resource-manager/manage-resources-portal)が作成されます。  新しいリソース グループには、特定のサブスクリプションにデプロイしたすべての HANA Large Instance ユニットが一覧表示されます。

新しい Azure リソース グループを探すには、Azure portal の左側のナビゲーション ウィンドウ内を移動して、ご自分のサブスクリプションのリソース グループを一覧表示します

![Azure portal のナビゲーション ウィンドウ](./media/hana-li-portal/portal-resource-group.png)

リソース グループの一覧では、HANA Large Instances のデプロイに使用したサブスクリプションでのフィルター処理が必要な場合があります

![Azure portal でのリソース グループのフィルター処理](./media/hana-li-portal/portal-filtering-subscription.png)

正しいサブスクリプションでフィルター処理した後も、リソース グループの一覧が長いままの場合があります。 末尾に **-Txxx** が付いたものを探します。"xxx" は 3 桁の数字で、たとえば **-T050** などです。 

リソース グループが見つかったら、その詳細を一覧表示します。 次のような一覧が表示されます。

![Azure portal の HLI 一覧](./media/hana-li-portal/portal-hli-units-list.png)

一覧表示されるすべてのユニットは、サブスクリプションにデプロイされている 1 つの HANA Large Instance ユニットを表しています。 この例では 8 つの異なる HANA Large Instance ユニットが表示されており、それらはご自分のサブスクリプションにデプロイされています。

同じ Azure サブスクリプションに複数の HANA Large Instance インスタンス テナントをデプロイした場合は、複数の Azure リソース グループが表示されます。 


## <a name="look-at-attributes-of-single-hli-unit"></a>1 つの HLI ユニットの属性を調べる
HANA Large Instance ユニットの一覧で 1 つのユニットをクリックすると、その HANA Large Instance ユニットの詳細を見ることができます。 

概要画面で [Show more] (詳細表示) をクリックした後、ユニットの表示は次のようになります。

![HLI ユニットの概要の表示](./media/hana-li-portal/portal-show-overview.png)

表示されるさまざまな属性を見ると、Azure VM の属性とほとんど変わりません。 左側のヘッダーでは、リソース グループ、Azure リージョン、サブスクリプション名、ID、追加したいくつかのタグが示されています。 既定では、HANA Large Instance ユニットにはタグは割り当てられていません。 ヘッダーの右側には、デプロイのときに割り当てられたユニットの名前が表示されています。 オペレーティング システムおよび IP アドレスが表示されています。 VM と同じように、HANA Large Instance の種類と CPU スレッドの数およびメモリも表示されます。 さまざまな HANA Large Instance ユニットの詳細については、以下をご覧ください。

- [HLI で利用可能な SKU](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus)
- [SAP HANA (Large Instances) のストレージ アーキテクチャ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-storage-architecture) 

右下にある追加データは、HANA Large Instance スタンプのリビジョンです。 次のいずれかの値になります。

- Revision 3
- Revision 4

Revision 4 は HANA Large Instances の最新アーキテクチャ リリースであり、Azure VM と、Revision 4 のスタンプまたは行にデプロイされた HANA Large Instance ユニット間のネットワーク待機時間が大幅に改善されています。
もう 1 つの非常に重要な情報が、概要の右下隅にあります。それは、デプロイされた HANA Large Instance ユニットのそれぞれに対して自動的に作成される Azure 近接通信配置グループの名前です。 SAP アプリケーション レイヤーをホストする Azure VM をデプロイするときは、この近接通信配置グループを参照する必要があります。 HANA Large Instance ユニットに関連付けられた [Azure 近接通信配置グループ](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)を使用することで、Azure VM が確実に HANA Large Instance ユニットに近接してデプロイされるようにします。 近接通信配置グループを使用して、リビジョン 4 でホストされている HANA Large Instance ユニットと同じ Azure データセンターに SAP アプリケーション レイヤーを配置する方法については、[SAP アプリケーションで最適なネットワーク待ち時間を実現する Azure 近接通信配置グループ](sap-proximity-placement-scenarios.md)に関する記事で説明しています。

ヘッダーの右側の列の追加フィールドでは、HANA Large Instance ユニットの電源の状態について通知されます。

> [!NOTE]
> 電源状態では、ハードウェア ユニットの電源がオンかオフかが示されます。 稼動しているオペレーティング システムに関する情報は提供されません。 HANA Large Instance を再起動すると、ユニットの状態が **[Starting]\(開始中\)** から **[Started]\(開始済み\)** に変わるまでにしばらく時間がかかります。 **[Started]\(開始済み\)** 状態になることは、OS が起動中であること、または OS が完全に起動したことを意味します。 その結果、ユニットの再起動後、状態が **[Started]\(開始済み\)** に切り替わったらすぐにユニットにログインすることはできません。
> 

[See more] (詳細表示) を押すと、追加の情報が表示されます。 追加情報の 1 つは、ユニットがデプロイされた HANA Large Instance スタンプのリビジョンの表示です。 HANA Large Instance スタンプのさまざまなリビジョンについては、「[SAP HANA on Azure (L インスタンス) とは](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)」の記事を参照してください。

## <a name="check-activities-of-a-single-hana-large-instance-unit"></a>1 つの HANA Large Instance ユニットのアクティビティを確認する 
HANA Large Instance ユニットの概要だけでなく、特定のユニットのアクティビティを確認できます。 アクティビティ ログは次のようになります。

![Azure portal のナビゲーション ウィンドウ](./media/hana-li-portal/portal-activity-list.png)

記録される主要なアクティビティの 1 つは、ユニットの再起動です。 表示されるデータには、アクティビティの状態、アクティビティがトリガーされたときのタイムスタンプ、アクティビティをトリガーしたサブスクリプション ID、アクティビティをトリガーした Azure ユーザーが含まれます。 

記録される別のアクティビティは、Azure メタ データでのユニットに対する変更です。 開始された再起動以外に、**Write HANAInstances** のアクティビティを確認できます。 この種のアクティビティでは、HANA Large Instance ユニット自体への変更は行われませんが、Azure でのユニットのメタ データに変更が文書化されます。 一覧表示されるケースでは、タグを追加および削除しました (次のセクションを参照)。

## <a name="add-and-delete-an-azure-tag-to-a-hana-large-instance-unit"></a>HANA Large Instance ユニットの Azure タグを追加および削除する
別に、HANA Large Instance ユニットに[タグ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)を追加する可能性があります。 タグを割り当てる方法は、VM へのタグの割り当てと同じです。 VM と同様に、タグは Azure メタ データ内に存在し、HANA Large Instances では、VM に対するタグと同じ制限があります。

タグの削除は VM と同じように動作します。 タグの適用と削除のアクティビティはどちらも、特定の HANA Large Instance ユニットのアクティビティ ログに記録されます。

## <a name="check-properties-of-a-hana-large-instance-unit"></a>HANA Large Instance ユニットのプロパティを確認する
**[プロパティ]** セクションには、ご自分に渡されるインスタンスの重要な情報が含まれています。 そのセクションでは、サポート ケースで必要なすべての情報、またはストレージ スナップショットの構成を設定するときに必要な情報がわかります。 このセクションは、ご自分のインスタンス、Azure へのインスタンスの接続、ストレージ バックエンドに関するデータのコレクションです。 セクションの上部は次のようになります。


![Azure portal での HLI プロパティの上部](./media/hana-li-portal/portal-properties-top.png)

最初のいくつかのデータ項目は、概要画面で既に見たものです。 しかし、データの重要な部分は ExpressRoute 回線 ID です。この ID は、最初にデプロイされたユニットが引き渡し時に取得されました。 サポート ケースによっては、そのデータを求められることがあります。 重要なデータ エントリは、スクリーンショットの下部に示されています。 表示されるデータは、HANA Large Instance スタック内のご自分の**テナント**に対してストレージを分離する NFS ストレージ ヘッドの IP アドレスです。 この IP アドレスは、[ストレージ スナップショット バックアップの構成ファイル](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore#set-up-storage-snapshots)を編集するときにも必要です。 

プロパティ ウィンドウを下にスクロールすると、ご自分の HANA Large Instance ユニットの一意のリソース ID や、デプロイに割り当てられたサブスクリプション ID のような、その他のデータが表示されます。

## <a name="restart-a-hana-large-instance-unit-through-azure-portal"></a>Azure portal から HANA Large Instance ユニットを再起動する
Linux オペレーティング システムの再起動を開始するときに、OS が再起動を正常に完了できないさまざまな状況がありました。 再起動を強制するためには、サービス要求を開き、Microsoft の運用担当者に HANA Large Instance ユニットの電源再起動を実行してもらう必要がありました。 現在は、HANA Large Instance ユニットの電源再起動の機能は、Azure portal に統合されています。 HANA Large Instance の概要部分のデータ セクションの上部に、再起動用のボタンが表示されています

![Azure portal での再起動ステップ 1](./media/hana-li-portal/portal-restart-first-step.png)

再起動ボタンをクリックすると、ユニットの再起動を本当に行うかどうかの確認を求められます。 [はい] ボタンをクリックして確認すると、ユニットが再起動されます。

> [!NOTE]
> 再起動プロセスでは、ユニットの状態が **[Starting]\(開始中\)** から **[Started]\(開始済み\)** に変わるまでにしばらく時間がかかります。 **[Started]\(開始済み\)** 状態になることは、OS が起動中であること、または OS が完全に起動したことを意味します。 その結果、ユニットの再起動後、状態が **[Started]\(開始済み\)** に切り替わったらすぐにユニットにログインすることはできません。

> [!IMPORTANT]
> HANA Large Instance ユニットのメモリ容量によっては、ハードウェアとオペレーティング システムの再開と再起動に最大 1 時間かかることがあります。


## <a name="open-a-support-request-for-hana-large-instances"></a>HANA large Instances のサポート リクエストを開く
Azure portal の HANA Large Instance ユニットの表示では、特に HANA large Instance ユニットに関するサポート リクエストも作成できます。 **[新しいサポート リクエスト]** リンクに従います 

![Azure portal でサポート リクエストのステップ 1 を開始する](./media/hana-li-portal/portal-initiate-support-request.png)

次の画面に一覧表示される SAP HANA Large Instancesl のサービスを受けるには、次に示すように [すべてのサービス] を選択することが必要な場合があります

![Azure portal で [すべてのサービス] を選択する](./media/hana-li-portal/portal-create-service-request.png)

サービスの一覧で、サービス **[SAP HANA 大規模なインスタンス]** が見つかります。 そのサービスを選択すると、次のように特定の問題の種類を選択できます。


![Azure portal で問題のクラスを選択する](./media/hana-li-portal/portal-select-problem-class.png)

さまざまな問題の種類のそれぞれに、問題の特徴をさらに指定するために選択する必要がある問題のサブタイプの選択肢があります。 サブタイプを選択した後は、件名を指定できます。 選択プロセスが完了すると、作成の次のステップに移動できます。 **[Solutions]\(解決策\)** セクションでは、HANA Large Instances に関するドキュメントが示されており、問題を解決できる可能性があります。 示されているドキュメントで問題の解決策が見つからない場合は、次のステップに進みます。 次のステップでは、問題が VM に関するものか、それとも HANA Large Instance ユニットに関するものかを尋ねられます。 この情報は、適切な専門家にサポート リクエストを送るのに役立ちます。 

![Azure portal でのサポート ケースの詳細](./media/hana-li-portal/portal-support-request-details.png)

質問に答えて、追加の詳細を提供したら、次のステップに進み、サポート リクエストを確認して送信できます。

## <a name="next-steps"></a>次の手順

- [SAP HANA on Azure (L インスタンス) の監視方法](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)
- [HANA 側からの監視とトラブルシューティング](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-monitor-troubleshoot)

