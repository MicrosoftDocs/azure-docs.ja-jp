---
title: 統合サービス環境 (ISE) を介して Azure Logic Apps から Azure Virtual Network に接続する
description: 統合サービス環境 (ISE) を作成して、ロジック アプリと統合アカウントが Azure 仮想ネットワーク (VNET) にアクセスできるが、同時にパブリックすなわち "グローバル" の Azure から分離されたプライベートな状態を維持できるようにします。
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 4865a2b3b02a1e7a6db19418122b66aeb79dd332
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099473"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>統合サービス環境 (ISE) を使用して Azure Logic Apps から Azure Virtual Network に接続する

ロジック アプリと統合アカウントが [Azure 仮想ネットワーク](../virtual-network/virtual-networks-overview.md)にアクセスする必要があるシナリオでは、"[*統合サービス環境*" (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) を作成します。 ISE は、専用のストレージと、パブリックまたは "グローバル" の Logic Apps サービスとは別に保存されている他のリソースを使用するプライベートな分離環境です。 この分離で、他の Azure テナントがご利用のアプリのパフォーマンスに与える可能性がある影響も軽減されます。

ISE を作成すると、Azure によってその ISE が Azure 仮想ネットワークに "*挿入*" され、その仮想ネットワークに Logic Apps サービスがデプロイされます。 ロジック アプリまたは統合アカウントを作成するときに、お使いの ISE を場所として選択します。 ロジック アプリまたは統合アカウントは、仮想ネットワーク内の仮想マシン (VM)、サーバー、システム、サービスなどのリソースに直接アクセスできます。

![統合サービス環境を選択する](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> ISE でロジック アプリと統合アカウントを連携させるには、両方とも "*同じ ISE*" を場所として使用する必要があります。

ISE では、実行継続時間、ストレージのリテンション期間、スループット、HTTP の要求と応答のタイムアウト、メッセージのサイズ、およびカスタム コネクタの要求の上限が引き上げられました。 詳細については、[Azure Logic Apps の制限と構成](logic-apps-limits-and-config.md)に関するページを参照してください。 ISE の詳細については、[Azure Logic Apps から Azure Virtual Network リソースへのアクセス](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)に関する記事を参照してください。

この記事では、次のタスクの実行方法について説明します。

* トラフィックが仮想ネットワーク内のサブネット間で ISE を通過できるように、その仮想ネットワーク上の必要なすべてのポートがオープンしていることを確認します。

* ISE を作成します。

* ISE に容量を追加します。

> [!IMPORTANT]
> ISE 内で実行されるロジック アプリ、組み込みトリガー、組み込みアクション、およびコネクターでは、使用量ベースの価格プランとは異なる価格プランが使用されます。 ISE の価格と課金のしくみについては、「[固定価格モデル](../logic-apps/logic-apps-pricing.md#fixed-pricing)」を参照してください。 価格については、「[Logic Apps の価格](../logic-apps/logic-apps-pricing.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* [Azure 仮想ネットワーク](../virtual-network/virtual-networks-overview.md)。 仮想ネットワークがない場合は、[Azure 仮想ネットワークの作成](../virtual-network/quick-create-portal.md)方法について学んでください。

  * 仮想ネットワークには、ISE 内にリソースを作成およびデプロイするため、"*空の*" サブネットが 4 つ必要です。 このようなサブネットは、事前に作成するか、同時にサブネットを作成できる ISE を作成するまで待つことができます。 [サブネット要件](#create-subnet)の詳細を参照してください。
  
    > [!NOTE]
    > Microsoft クラウド サービスにプライベート接続を提供する [ExpressRoute](../expressroute/expressroute-introduction.md) を使用する場合、次のルートを持つ[ルート テーブルを作成](../virtual-network/manage-route-table.md)して、ISE によって使用される各サブネットにそのテーブルをリンクします。
    > 
    > **名前**: <*route-name*><br>
    > **アドレス プレフィックス**:0.0.0.0/0<br>
    > **次ホップ**:インターネット

  * 仮想ネットワークで[これらのポートが利用可能になっており](#ports)、ISE が正常に動作し、アクセス可能な状態であることを確認します。

* Azure 仮想ネットワークでカスタム DNS サーバーを使用する場合は、ISE を仮想ネットワークにデプロイする前に、[次の手順に従ってそのようなサーバーを設定します](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。 そうしないと、DNS サーバーを変更するたびに、ISE の再起動が必要になります (これは、ISE パブリック プレビューで利用できる機能です)。

<a name="ports"></a>

## <a name="check-network-ports"></a>ネットワーク ポートを確認する

既存の仮想ネットワークで ISE を使用した場合、一般的な設定の問題はブロックされたポートが 1 つ以上あることです。 ISE と宛先システムとの間の接続を作成するために使用するコネクターにも独自のポート要件がある可能性があります。 たとえば、FTP コネクタを使用して FTP システムと通信する場合、その FTP システム上で使用するポート (コマンド送信用のポート 21 など) を使用できることを確認します。

新しい仮想ネットワークとサブネットを制約なしで作成した場合、サブネット間のトラフィックを制御できるように、仮想ネットワーク内に[ネットワーク セキュリティ グループ (NSG)](../virtual-network/security-overview.md) を設定する必要はありません。 既存の仮想ネットワークの場合は、"*必要に応じて*"、[サブネット間のネットワーク トラフィックをフィルター処理](../virtual-network/tutorial-filter-network-traffic.md)して NSG を設定できます。 このルートを選択する場合は、次の表で説明するように、NSG を持つ仮想ネットワーク上で ISE によって特定のポートが開かれることを確認してください。 仮想ネットワークに既存の NSG またはファイアウォールがある場合は、それらによってこれらのポートが開かれることを確認してください。 そうすると、ISE はアクセス可能状態のままとなり、ISE へのアクセスを失わないように正しく機能することができます。 そうしないと、いずれかの必要なポートが使用できなった場合、ISE は機能を停止します。

> [!IMPORTANT]
> サブネット内の内部通信の場合、それらのサブネット内のすべてのポートを開いておくことが ISE では必要になります。

次の表は、ISE で使用される仮想ネットワーク内のポートと、それらのポートが使用される場所を説明したものです。 [Resource Manager のサービス タグ](../virtual-network/security-overview.md#service-tags)は、IP アドレス プレフィックスのグループを表し、セキュリティ規則を作成する際の複雑さを最小限に抑えるために役立ちます。

> [!NOTE]
> ソース ポートは一時的なものなので、すべての規則に対して `*` に設定します。

| 目的 | Direction | ターゲット ポート | 発信元サービス タグ | 宛先サービス タグ | メモ |
|---------|-----------|-------------------|--------------------|-------------------------|-------|
| Azure Logic Apps からの通信 | 送信 | 80、443 | VirtualNetwork | インターネット | このポートは、Logic Apps サービスが通信する外部サービスに依存しています。 |
| Azure Active Directory | 送信 | 80、443 | VirtualNetwork | AzureActiveDirectory | |
| Azure Storage の依存関係 | 送信 | 80、443 | VirtualNetwork | Storage | |
| Intersubnet 通信 | 受信および送信 | 80、443 | VirtualNetwork | VirtualNetwork | サブネット間の通信用 |
| Azure Logic Apps への通信 | 受信 | 443 | 内部アクセス エンドポイント: <br>VirtualNetwork <p><p>外部アクセス エンドポイント: <br>インターネット <p><p>**メモ**:これらのエンドポイントは、[ISE 作成時に選択](#create-environment)されたエンドポイント設定を参照します。 詳細については、「[エンドポイント アクセス](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)」を参照してください。 | VirtualNetwork | ロジック アプリ内に存在する任意の要求トリガーまたは Webhook を呼び出すコンピューターまたはサービスの IP アドレス。 このポートを閉じるかブロックすると、要求トリガーでロジック アプリに HTTP 呼び出しできなくなります。 |
| ロジック アプリの実行履歴 | 受信 | 443 | 内部アクセス エンドポイント: <br>VirtualNetwork <p><p>外部アクセス エンドポイント: <br>インターネット <p><p>**メモ**:これらのエンドポイントは、[ISE 作成時に選択](#create-environment)されたエンドポイント設定を参照します。 詳細については、「[エンドポイント アクセス](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)」を参照してください。 | VirtualNetwork | ロジック アプリの実行履歴を表示するコンピューターの IP アドレス。 このポートを閉じたりブロックしたりしても実行履歴を表示できますが、その実行履歴に含まれる各ステップの入出力は表示されなくなります。 |
| 接続管理 | 送信 | 443 | VirtualNetwork  | インターネット | |
| 診断ログとメトリックの発行 | 送信 | 443 | VirtualNetwork  | AzureMonitor | |
| Azure Traffic Manager からの通信 | 受信 | 443 | AzureTrafficManager | VirtualNetwork | |
| Logic Apps デザイナー - 動的プロパティ | 受信 | 454 | インターネット | VirtualNetwork | 要求は、Logic Apps の[リージョン内のアクセス エンドポイント受信 IP アドレス](../logic-apps/logic-apps-limits-and-config.md#inbound)から送信されます。 |
| App Service の管理の依存関係 | 受信 | 454、455 | AppServiceManagement | VirtualNetwork | |
| コネクタのデプロイ | 受信 | 454 | AzureConnectors | VirtualNetwork | コネクタのデプロイと更新に必要。 このポートを閉じたりブロックしたりすると、ISE のデプロイが失敗し、コネクタの更新や修正ができなくなります。 |
| コネクタ ポリシーのデプロイ | 受信 | 3443 | インターネット | VirtualNetwork | コネクタのデプロイと更新に必要。 このポートを閉じたりブロックしたりすると、ISE のデプロイが失敗し、コネクタの更新や修正ができなくなります。 |
| Azure SQL 依存関係 | 送信 | 1433 | VirtualNetwork | SQL | |
| Azure Resource Health | 送信 | 1886 | VirtualNetwork | AzureMonitor | 正常性の状態を Resource Health に公開するために必要 |
| API Management - 管理エンドポイント | 受信 | 3443 | APIManagement | VirtualNetwork | |
| ログから Event Hub ポリシーおよび監視エージェントへの依存関係 | 送信 | 5672 | VirtualNetwork | EventHub | |
| ロール インスタンス間での Azure Cache for Redis インスタンスへのアクセス | 受信 <br>送信 | 6379 - 6383 | VirtualNetwork | VirtualNetwork | また、Azure Cache for Redis で動作する ISE の場合は、[「Azure Cache for Redis の FAQ」で説明されている送信ポートと受信ポート](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements)を開く必要があります。 |
| Azure Load Balancer | 受信 | * | AzureLoadBalancer | VirtualNetwork | |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>ISE を作成する

統合サービス環境 (ISE) を作成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) の Azure メイン メニューで、 **[リソースの作成]** を選択します。
検索ボックスに、フィルターとして「統合サービス環境」と入力します。

   ![新しいリソースの作成](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. 統合サービス環境の作成ウィンドウで、 **[作成]** を選択します。

   ![[作成] を選択する](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. 次の例のように、環境について次の詳細を指定してから **[確認と作成]** を選択します。

   ![環境の詳細を指定する](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **サブスクリプション** | はい | <*Azure サブスクリプション名*> | 環境に使用する Azure サブスクリプション |
   | **リソース グループ** | はい | <*Azure-resource-group-name*> | 環境を作成する Azure リソース グループ |
   | **統合サービス環境の名前** | はい | <*environment-name*> | ISE 名。英字、数字、ハイフン (`-`)、アンダースコア (`_`)、ピリオド (`.`) のみを含めることができます。 |
   | **Location** | はい | <*Azure-datacenter-region*> | 環境をデプロイする Azure データセンター リージョン |
   | **SKU** | はい | **Premium** または **Developer (SLA なし)** | 作成および使用する ISE SKU。 これらの SKU の違いについては、[ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) に関する記事を参照してください。 <p><p>**重要**:このオプションは、ISE 作成時にのみ使用できます。後で変更することはできません。 |
   | **追加容量** | Premium: <br>はい <p><p>Developer: <br>適用不可 | Premium: <br>0 から 10 <p><p>Developer: <br>適用不可 | この ISE リソースに使用する追加の処理ユニット数。 作成後に容量を追加する場合は、「[ISE の容量を追加する](#add-capacity)」を参照してください。 |
   | **アクセス エンドポイント** | はい | **内部**または**外部** | ISE に使用するアクセス エンドポイントの種類。ISE 内のロジック アプリ上で要求または Webhook トリガーが仮想ネットワークの外からの呼び出しを受信できるかどうかを決定します。 エンドポイントの種類は、ロジック アプリの実行履歴の入力と出力へのアクセスにも影響します。 詳細については、「[エンドポイント アクセス](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)」を参照してください。 <p><p>**重要**:このオプションは、ISE 作成時にのみ使用できます。後で変更することはできません。 |
   | **Virtual Network** | はい | <*Azure-virtual-network-name*> | 環境内のロジック アプリが仮想ネットワークにアクセスできるように、その環境を挿入する Azure 仮想ネットワーク。 ネットワークがない場合は、[まず Azure 仮想ネットワークを作成](../virtual-network/quick-create-portal.md)します。 <p>**重要**:ISE を作成するときに "*のみ*"、この挿入を実行することができます。 |
   | **サブネット** | はい | <*subnet-resource-list*> | ISE では、環境内にリソースを作成およびデプロイするために "*空の*" サブネットが 4 つ必要です。 各サブネットを作成するには、[この表の下の手順に従います](#create-subnet)。 |
   |||||

   <a name="create-subnet"></a>

   **サブネットを作成する**

   環境内にリソースを作成およびデプロイする場合、どのサービスにも委任されていない*空の*サブネットが 4 つ ISE に必要です。 環境の作成後に、これらのサブネット アドレスを変更することは*できません*。 各サブネットは、次の基準を満たしている必要があります。

   * 名前の先頭がアルファベット文字かアンダースコアで、名前に `<`、`>`、`%`、`&`、`\\`、`?`、`/` の文字が含まれていない。

   * [Classless Inter-Domain Routing (CIDR) 形式](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)とクラス B アドレス空間を使用する。

   * 各サブネットには*最小値*として*少なくとも* 32 個のアドレスが必要なため、そのアドレス空間で最低でも `/27` を使用する。 例:

     * 2<sup>(32-27)</sup> は 2<sup>5</sup> (つまり 32) なので、`10.0.0.0/27` には 32 個のアドレスがあります。

     * 2<sup>(32-24)</sup> は 2<sup>8</sup> (つまり 256) なので、`10.0.0.0/24` には 256 個のアドレスがあります。

     * 2<sup>(32-28)</sup> は 2<sup>4</sup> (つまり 16) なので、`10.0.0.0/28` には 16 個のアドレスしかなく、これでは少なすぎます。

     アドレス計算の詳細については、「[IPv4 CIDR blocks (IPv4 CIDR ブロック)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks)」を参照してください。

   * [ExpressRoute](../expressroute/expressroute-introduction.md) を使用する場合、次のルートを持つ[ルート テーブルを作成](../virtual-network/manage-route-table.md)して、ISE によって使用される各サブネットにそのテーブルをリンクします。

     **名前**: <*route-name*><br>
     **アドレス プレフィックス**:0.0.0.0/0<br>
     **次ホップ**:インターネット

   1. **[サブネット]** 一覧で **[サブネット構成の管理]** を選択します。

      ![サブネット構成の管理](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet.png)

   1. **[サブネット]** ウィンドウで **[サブネット]** を選択します。

      ![サブネットの追加](./media/connect-virtual-network-vnet-isolated-environment/add-subnet.png)

   1. **[サブネットの追加]** ウィンドウで次の情報を指定します。

      * **名前**:サブネットの名前
      * **アドレス範囲 (CIDR ブロック)** :仮想ネットワークのサブネットの範囲 (CIDR 形式)

      ![サブネットの詳細を追加する](./media/connect-virtual-network-vnet-isolated-environment/subnet-details.png)

   1. 完了したら、 **[OK]** を選びます。

   1. さらに 3 つのサブネットについてこれらの手順を繰り返します。

      > [!NOTE]
      > 作成しようとしているサブネットが有効でない場合、Azure portal にメッセージが表示されますが、作業の進行は中断されません。

   サブネットの作成の詳細については、[仮想ネットワーク サブネットの追加](../virtual-network/virtual-network-manage-subnet.md)に関する記事を参照してください。

1. Azure で ISE 情報の検証が正常に完了したら、次の例のように **[作成]** を選択します。

   ![検証が完了したら [作成] を選択します](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure で環境のデプロイが開始されますが、このプロセスが完了するまでに最長 2 時間かかる "*可能性があります*"。 展開の状態を確認するには、Azure ツールバーで通知アイコンを選択します。これで通知ウィンドウが開きます。

   ![展開の状態を確認する](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   デプロイが正常に完了すると、Azure に次の通知が表示されます。

   ![デプロイメント成功](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

   表示されない場合は、デプロイのトラブルシューティングに関する Azure portal の手順に従います。

   > [!NOTE]
   > デプロイが失敗するか、ISE を削除する場合、サブネットがリリースされるまでに最長 1 時間かかる可能性があります。 この遅延のため、このようなサブネットを他の ISE で再利用できるようになるまで待たなければならない場合があります。
   >
   > 仮想ネットワークを削除すると、通常、サブネットがリリースされるまでに最長 2 時間かかる可能性があり、この操作にさらに時間がかかる場合があります。 
   > 仮想ネットワークを削除する場合は、まだ接続されているリソースがないことを確認してください。 
   > 「[仮想ネットワークの削除](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)」を参照してください。

1. 展開の完了後に Azure で環境が自動的に表示されない場合は、環境を表示するために **[リソースに移動]** を選択します。

1. ISE のネットワーク正常性を確認するには、[統合サービス環境の管理](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)に関する記事を参照してください。

1. ISE 内でロジック アプリやその他の成果物の作成を開始するには、「[成果物を統合サービス環境に追加する](../logic-apps/add-artifacts-integration-service-environment-ise.md)」を参照してください。

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>ISE の容量を追加する

Premium ISE ベース ユニットの容量は固定されているため、さらにスループットが必要な場合は、作成中または作成後にスケール ユニットを追加できます。 パフォーマンス メトリックや追加の処理ユニット数に基づいて自動スケーリングできます。 メトリックに基づいた自動スケーリングを選択する場合は、さまざまな条件から選択し、その条件を満たすしきい値条件を指定できます。 Developer SKU には、スケール ユニットを追加する機能が含まれていません。

1. Azure portal で ISE を見つけます。

1. ISE の使用状況とパフォーマンスのメトリックを確認するには、ISE のメイン メニューで、 **[概要]** を選択します。

   ![ISE の使用状況を表示する](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-usage.png)

1. 自動スケーリングを設定するには、 **[設定]** で **[スケールアウト]** を選択します。 **[構成]** タブで、 **[自動スケーリングの有効化]** を選択します。

   ![自動スケーリングを有効にする](./media/connect-virtual-network-vnet-isolated-environment/scale-out.png)

1. **[自動スケーリング設定の名前]** に設定の名前を入力します。

1. **[既定値]** セクションで、 **[メトリックに基づいてスケーリングする]** または **[特定のインスタンス数にスケーリングする]** のいずれかを選択します。

   * インスタンス ベースを選択した場は、0 から 10 の範囲で処理ユニット数を入力します。

   * メトリック ベースを選択した場合は、次の手順に従います。

     1. **[ルール]** セクションで、 **[ルールの追加]** をクリックします。

     1. **[スケール ルール]** ウィンドウで、ルールがトリガーされる条件とその際に実行するアクションを設定します。

     1. 完了したら、 **[追加]** を選択します。

1. 自動スケーリングの設定が終了したら、変更を保存します。

## <a name="next-steps"></a>次の手順

* [成果物を統合サービス環境に追加する](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [統合サービス環境のネットワーク正常性を確認する](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) の詳細を理解する
* [Azure サービスの仮想ネットワーク統合](../virtual-network/virtual-network-for-azure-services.md)について理解する
