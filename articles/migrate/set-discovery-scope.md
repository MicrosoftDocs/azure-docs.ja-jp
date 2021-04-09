---
title: Azure Migrate を使用して VMware 上のサーバーの検出範囲を設定する
description: Azure Migrate を使って VMware にホストされたサーバーの評価と移行の検出範囲を設定する方法について説明します。
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: 29ac42da6560a717f12cd256fdd71282e0bd313f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104775359"
---
# <a name="set-discovery-scope-for-servers-in-vmware-environment"></a>VMware 環境内のサーバーの検出範囲を設定する

この記事では、次のような場合に VMware 環境内のサーバーの検出範囲を制限する方法について説明します。

- Azure Migrate: Discovery and Assessment ツールを使用しているときの、[Azure Migrate アプライアンス](migrate-appliance-architecture.md)でのサーバーの検出。
- VMware 環境から Azure へのサーバーのエージェントレス移行に Azure Migrate: Server Migration ツールを使用しているときの、[Azure Migrate アプライアンス](migrate-appliance-architecture.md)でのサーバーの検出。

アプライアンスを設定すると、vCenter Server に接続されて、探索が開始されます。 アプライアンスを vCenter Server に接続する前に、検出を vCenter Server データセンター、クラスター、クラスターのフォルダー、ホスト、ホストのフォルダー、または個々のサーバーに制限することができます。 範囲を設定するには、vCenter Server にアクセスするためにアプライアンスで使用されているアカウントにアクセス許可を割り当てます。

## <a name="before-you-start"></a>開始する前に

Azure Migrate で検出に使用される vCenter ユーザー アカウントを設定していない場合は、ここで[評価](./tutorial-discover-vmware.md#prepare-vmware)用または[エージェントレス移行](./migrate-support-matrix-vmware-migration.md#agentless-migration)用にそれを行います。


## <a name="assign-permissions-and-roles"></a>アクセス許可とロールを割り当てる

次の 2 つの方法のいずれかを使用して、VMware インベントリ オブジェクトに対するアクセス許可を割り当てることができます。

- アプライアンスによって使用されるアカウントで、範囲を設定するオブジェクトに対して必要なアクセス許可を持つロールを割り当てます。
- または、データセンター レベルでアカウントにロールを割り当てて、子オブジェクトに伝達します。 その後、範囲内にしないすべてのオブジェクトについて、**アクセス権のない** ロールをアカウントに割り当てます。 すべての新しい子オブジェクトにも、親から継承されたアクセス権が自動的に付与されるので、この構成は煩雑であると同時に、アクセス制御が行われる場合があるため、この方法は推奨されません。

vCenter サーバー フォルダー レベルでインベントリ検出の範囲を設定することはできません。 フォルダー内のサーバーに検出の範囲を設定する必要がある場合は、ユーザーを作成し、必要な各サーバーに対するアクセス権を個別に付与します。 ホストとクラスターのフォルダーはサポートされています。


### <a name="assign-a-role-for-assessment"></a>評価のためのロールを割り当てる

1. 検出に使用しているアプライアンス vCenter アカウントで、検出して評価するサーバーがホストされているすべての親オブジェクト (ホスト、クラスター、ホスト フォルダー、クラスター フォルダー、データセンターまで) に対して **読み取り専用** ロールを適用します。
2. 階層内の子オブジェクトにこれらのアクセス許可を反映します。

    ![アクセス許可の割り当て](./media/tutorial-assess-vmware/assign-perms.png)

### <a name="assign-a-role-for-agentless-migration"></a>エージェントレス移行のためのロールを割り当てる

1. 移行に使用しているアプライアンス vCenter アカウントで、検出して評価するサーバーがホストされているすべての親オブジェクトに対して、[必要なアクセス許可](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)を持つユーザー定義ロールを適用します。
2. ロールには、識別しやすい名前を指定できます。 たとえば、<em>Azure_Migrate</em> などです。

## <a name="work-around-for-server-folder-restriction"></a>サーバー フォルダーの制限を回避する

現在、アクセス権が vCenter サーバー フォルダー レベルで付与されている場合は、Azure Migrate: Discovery and Assessment ツールでサーバーを検出することはできません。 サーバー フォルダーごとに検出と評価の範囲を設定する場合は、この回避策を使用します。

1. 検出と評価の範囲を設定するフォルダー内に存在するすべてのサーバーに対して、読み取り専用アクセス許可を割り当てます。
2. サーバーがホストされているすべての親オブジェクト (ホスト、クラスター、ホスト フォルダー、クラスター フォルダー、データセンターまで) に対して、読み取り専用アクセス権を付与します。 すべての子オブジェクトに権限を広める必要はありません。
3. 検出に資格情報を使用するには、 **[コレクション スコープ]** としてデータセンターを選択します。


ロール ベースのアクセス制御を設定すると、対応する vCenter ユーザー アカウントがテナント固有のサーバーのみにアクセスできるようになります。


## <a name="next-steps"></a>次のステップ

[アプライアンスを設定する](how-to-set-up-appliance-vmware.md)