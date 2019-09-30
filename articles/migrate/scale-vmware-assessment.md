---
title: Azure Migrate を使用して Azure に移行するために多数の VMware VM を評価する | Microsoft Docs
description: Azure Migrate サービスを使用して Azure に移行するために多数の VMware VM を評価する方法について説明します。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/12/2019
ms.author: hamusa
ms.openlocfilehash: 0d279781cdc27dbf2140c0100d84de5128c6a3d5
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279421"
---
# <a name="assess-large-numbers-of-vmware-vms-for-migration-to-azure"></a>Azure への移行のために多数の VMware VM を評価する


この記事では、Azure Migrate Server Assessment ツールを使用して、Azure に移行するために多数 (1000 - 35,000) のオンプレミス VMware VM を評価する方法について説明します

[Azure Migrate](migrate-services-overview.md) では、アプリ、インフラストラクチャ、およびワークロードを検出、評価、および Microsoft Azure に移行するために役立つツールのハブが提供されます。 このハブには、Azure Migrate ツールと、サードパーティ製の独立系ソフトウェア ベンダー (ISV) オファリングが含まれています。 

この記事では、次のことについて説明します。
> [!div class="checklist"]
> * 大規模な評価を計画します。
> * Azure のアクセス許可を構成し、評価用に VMware を準備します。
> * Azure Migrate プロジェクトを作成し、評価を作成します。
> * 移行を計画するときに評価をレビューします。


> [!NOTE]
> 大規模な評価を行う前に、いくつかの VM を評価するための概念実証を試す場合は、[チュートリアル シリーズ](tutorial-prepare-vmware.md)に従ってください

## <a name="plan-for-assessment"></a>評価の計画

多数の VMware VM の評価を計画する際には、以下についていくつかの考慮点があります。

- **Azure Migrate プロジェクトの計画**: Azure Migrate プロジェクトをデプロイする方法を調べます。 たとえば、データセンターが地理的に異なる場所にある場合や、検出、評価、または移行に関連するメタデータを地理的に異なる場所に格納する必要がある場合は、複数のプロジェクトが必要になることがあります。 
- **アプライアンスの計画**: Azure Migrate では、VMware VM としてデプロイされたオンプレミスの Azure Migrate アプライアンスを使用して、VM が継続的に検出されます。 アプライアンスでは、VM、ディスク、ネットワーク アダプターなどの環境の変化が監視されます。 また、これらに関するメタデータとパフォーマンス データが Azure に送信されます。 ユーザーは、デプロイする必要があるアプライアンスの数を確認する必要があります。
- **検出用のアカウントの計画**: Azure Migrate アプライアンスでは、評価と移行の対象の VM を検出するために、vCenter Server へのアクセス権を持つアカウントが使われます。 10,000 を超える VM を検出している場合は、複数のアカウントを設定します。


## <a name="planning-limits"></a>計画の制限
 
計画には、この表にまとめた制限を使用します。

**計画** | **制限**
--- | --- 
**Azure Migrate プロジェクト** | 1 つのプロジェクトで最大 35,000 個の VM を評価します。
**Azure Migrate アプライアンス** | 1 つのアプライアンスでは、1 つの vCenter Server で最大 10,000 個の VM を検出できます。<br/> 1 つのアプライアンスでは、1 つの vCenter Server にだけ接続できます。<br/> 1 つのアプライアンスは、1 つの Azure Migrate リソースにのみ関連付けることができます。<br/>  任意の数のアプライアンスを 1 つの Azure Migrate プロジェクトに関連付けることができます。 <br/><br/> 
**グループ** | 1 つのグループに最大 35,000 個の VM を追加できます。
**Azure Migrate の評価** | 1 回の評価で最大 35,000 個の VM を評価できます。

これらの制限事項を考慮して、デプロイの例をいくつか示します。


**vCenter サーバー** | **サーバー上の VM** | **推奨事項** | **アクション**
---|---|---
1 つ | < 10,000 | 1 つの Azure Migrate プロジェクト。<br/> 1 つのアプライアンス。<br/> 検出用に 1 つの vCenter アカウント。 | アプライアンスを設定し、アカウントを使って vCenter Server に接続します。
1 つ | > 10,000 | 1 つの Azure Migrate プロジェクト。<br/> 複数のアプライアンス。<br/> 複数の vCenter アカウント。 | 10,000 VM ごとにアプライアンスを設定します。<br/><br/> vCenter アカウントを設定し、1 つのアカウントでのアクセスが 10,000 VM 未満に制限されるように、インベントリを分割します。<br/> アカウントを使って、各アプライアンスを vCenter Server に接続します。<br/> 異なるアプライアンスで検出されたマシン間の依存関係を分析できます。
複数 | < 10,000 |  1 つの Azure Migrate プロジェクト。<br/> 複数のアプライアンス。<br/> 検出用に 1 つの vCenter アカウント。 | アプライアンスを設定し、アカウントを使って vCenter Server に接続します。<br/> 異なるアプライアンスで検出されたマシン間の依存関係を分析できます。
複数 | > 10,000 | 1 つの Azure Migrate プロジェクト。<br/> 複数のアプライアンス。<br/> 複数の vCenter アカウント。 | vCenter Server での検出が 10,000 VM 以下の場合は、vCenter Server ごとにアプライアンスを設定します。<br/><br/> vCenter Server での検出が 10,000 VM を超える場合は、10,000 VM ごとにアプライアンスを設定します。<br/> vCenter アカウントを設定し、1 つのアカウントでのアクセスが 10,000 VM 未満に制限されるように、インベントリを分割します。<br/> アカウントを使って、各アプライアンスを vCenter Server に接続します。<br/> 異なるアプライアンスで検出されたマシン間の依存関係を分析できます。


## <a name="plan-discovery-in-a-multi-tenant-environment"></a>マルチテナント環境での検出の計画

マルチテナント環境を計画している場合は、検出のスコープを vCenter Server にできます。

- vCenter Server のデータセンター、クラスター、クラスターのフォルダー、ホスト、ホストのフォルダー、または個々の VM に、アプライアンスの検出のスコープを設定できます。
- 環境が複数のテナント間で共有されていて、各テナントを個別に検出する必要がある場合は、アクセスのスコープを、アプライアンスで検出に使われる vCenter アカウントに設定できます。 
    - テナントでホストが共有されている場合は、VM フォルダーごとにスコープを設定することができます。 vCenter アカウントに VM フォルダー レベルでアクセス権を付与した場合、Azure Migrate で VM を検出することはできません。 VM フォルダーで検出のスコープを指定する場合は、vCenter アカウントに VM レベルで読み取り専用アクセス権を割り当てることにより実現できます。 検出のスコープ設定について詳しくは、[こちら](tutorial-assess-vmware.md#scoping-discovery)をご覧ください。

## <a name="prepare-for-assessment"></a>評価の準備

サーバー評価のために Azure と VMware を準備します。 

1. [VMware のサポート要件と制限事項](migrate-support-matrix-vmware.md)を確認します。
2. Azure Migrate とやり取りするために、自分の Azure アカウントのアクセス許可を設定します。
3. 評価用に VMware を準備します。

[このチュートリアル](tutorial-prepare-vmware.md)の手順に従って、これらの設定を構成します。


## <a name="create-a-project"></a>プロジェクトの作成

計画の要件に従って、以下を実行します。

1. Azure Migrate プロジェクトを作成します。
2. Azure Migrate Server Assessment ツールをプロジェクトに追加します。

[詳細情報](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>評価の作成とレビュー

1. VMware VM の評価を作成します。
1. 移行計画に備えて評価をレビューします。


[このチュートリアル](tutorial-assess-vmware.md)の手順に従って、これらの設定を構成します。
    

## <a name="next-steps"></a>次の手順

この記事では、次の内容について説明します。
 
> [!div class="checklist"] 
> * VMware VM に対して Azure Migrate 評価のスケーリングを準備しました
> * 評価のために Azure と VMware を準備しました
> * Azure Migrate プロジェクトを作成し、評価を実行しました
> * 移行に備えて評価をレビューしました。

次に、[評価の計算](concepts-assessment-calculation.md)方法と、[評価の変更](how-to-modify-assessment.md)方法について確認します。
