---
title: 統合サービス環境 (ISE) を介して Azure Logic Apps から Azure Virtual Network に接続する
description: 統合サービス環境 (ISE) を作成して、ロジック アプリと統合アカウントが Azure 仮想ネットワーク (VNET) にアクセスできるが、同時にパブリックすなわち "グローバル" の Azure から分離されたプライベートな状態を維持できるようにします。
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: 6be897cc1ae11b8d3032e3ffc669eac05dafe5b2
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522317"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>統合サービス環境 (ISE) を使用して Azure Logic Apps から Azure Virtual Network に接続する

> [!NOTE]
> この機能は、[*パブリック プレビュー*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)の段階です。

ロジック アプリと統合アカウントが [Azure 仮想ネットワーク](../virtual-network/virtual-networks-overview.md)にアクセスする必要があるシナリオでは、"[*統合サービス環境*" (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) を作成します。 ISE は、専用のストレージと、パブリックまたは "グローバル" の Logic Apps サービスとは別に保存されている他のリソースを使用するプライベートな分離環境です。 この分離で、他の Azure テナントがご利用のアプリのパフォーマンスに与える可能性がある影響も軽減されます。 ISE が Azure 仮想ネットワークに "*挿入*" され、Logic Apps サービスが仮想ネットワークにデプロイされます。 ロジック アプリまたは統合アカウントを作成するときに、この ISE を場所として選択します。 ロジック アプリまたは統合アカウントは、仮想ネットワーク内の仮想マシン (VM)、サーバー、システム、サービスなどのリソースに直接アクセスできます。

![統合サービス環境を選択する](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

この記事では、次のタスクの実行方法について説明します。

* トラフィックが仮想ネットワーク内のサブネット間で統合サービス環境 (ISE) を通過できるように、Azure 仮想ネットワーク上のポートを設定します。

* 統合サービス環境 (ISE) を作成します。

* ISE で実行できるロジック アプリを作成します。

* ISE のロジック アプリ用に統合アカウントを作成します。

統合サービス環境の詳細については、[Azure Logic Apps から Azure Virtual Network リソースにアクセスする方法](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションがない場合は、<a href="https://azure.microsoft.com/free/" target="_blank">無料の Azure アカウントにサインアップ</a>してください。

  > [!IMPORTANT]
  > ご利用の ISE で実行されるロジック アプリ、組み込みアクション、コネクタは、使用量ベースの料金プランではなく、異なる料金プランを使用します。 詳細については、「[Logic Apps の価格](../logic-apps/logic-apps-pricing.md)」をご覧ください。

* [Azure 仮想ネットワーク](../virtual-network/virtual-networks-overview.md)。 仮想ネットワークがない場合は、[Azure 仮想ネットワークの作成](../virtual-network/quick-create-portal.md)方法について学んでください。 

  * 仮想ネットワークには、ISE 内にリソースをデプロイおよび作成するため、*空の*サブネットが 4 つ必要です。 このようなサブネットは、事前に作成するか、同時にサブネットを作成できる ISE を作成するまで待つことができます。 [サブネット要件](#create-subnet)の詳細を参照してください。 
  
    > [!NOTE]
    > Microsoft クラウド サービスにプライベート接続を提供する [ExpressRoute](../expressroute/expressroute-introduction.md) を使用する場合、次のルートを持つ[ルート テーブルを作成](../virtual-network/manage-route-table.md)して、ISE によって使用される各サブネットにそのテーブルをリンクします。
    > 
    > **名前**: <*route-name*><br>
    > **アドレス プレフィックス**:0.0.0.0/0<br>
    > **次ホップ**:インターネット

  * 仮想ネットワークで[これらのポートが利用可能になっており](#ports)、ISE が正常に動作し、アクセス可能な状態であることを確認します。

* Azure 仮想ネットワークでカスタム DNS サーバーを使用する場合は、ISE を仮想ネットワークにデプロイする前に、[次の手順に従ってそのようなサーバーを設定します](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。 そうしないと、DNS サーバーを変更するたびに、ISE の再起動が必要になります (これは、ISE パブリック プレビューで利用できる機能です)。

* [ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する基本的な知識

<a name="ports"></a>

## <a name="set-up-network-ports"></a>ネットワーク ポートを設定する

正常に動作し、アクセスできる状態を維持するには、統合サービス環境 (ISE) に仮想ネットワーク上で使用できる特定のポートが必要です。 そうしないと、このようなポートのいずれかが使用できない場合、ISE へのアクセスが失われ、動作しなくなる可能性があります。 仮想ネットワーク内で ISE を使用する場合、一般的な設定の問題はブロックされたポートが 1 つ以上あることです。 ISE と宛先システム間の接続の場合、使用するコネクタにも独自のポート要件があります。 たとえば、FTP コネクタを使用して FTP システムと通信する場合、その FTP システム上で使用するポート (コマンド送信用のポート 21 など) を使用できることを確認します。

ISE をデプロイする仮想ネットワークのサブネット間でトラフィックを制御するには、[サブネット間のネットワーク トラフィックをフィルター処理する](../virtual-network/tutorial-filter-network-traffic.md)ことで、そのようなサブネットに対して[ネットワーク セキュリティ グループ](../virtual-network/security-overview.md)を設定できます。 以下の表は、ISE で使用される仮想ネットワーク内のポートと、それらのポートが使用される場所を説明したものです。 [サービス タグ](../virtual-network/security-overview.md#service-tags)は、IP アドレス プレフィックスのグループを表し、セキュリティ規則を作成する際の複雑さを最小限に抑えるために役立ちます。

> [!IMPORTANT]
> サブネット内の内部通信の場合、それらのサブネット内のすべてのポートを開いておくことが ISE では必要になります。

| 目的 | 方向 | ポート | 発信元サービス タグ | 宛先サービス タグ | メモ |
|---------|-----------|-------|--------------------|-------------------------|-------|
| Azure Logic Apps からの通信 | 送信 | 80、443 | VIRTUAL_NETWORK | INTERNET | このポートは、Logic Apps サービスが通信する外部サービスに依存しています。 |
| Azure Active Directory | 送信 | 80、443 | VIRTUAL_NETWORK | AzureActiveDirectory | |
| Azure Storage の依存関係 | 送信 | 80、443 | VIRTUAL_NETWORK | Storage | |
| Intersubnet 通信 | 受信および送信 | 80、443 | VIRTUAL_NETWORK | VIRTUAL_NETWORK | サブネット間の通信用 |
| Azure Logic Apps への通信 | 受信 | 443 | INTERNET  | VIRTUAL_NETWORK | ロジック アプリ内に存在する任意の要求トリガーまたは Webhook を呼び出すコンピューターまたはサービスの IP アドレス。 このポートを閉じるかブロックすると、要求トリガーでロジック アプリに HTTP 呼び出しできなくなります。  |
| ロジック アプリの実行履歴 | 受信 | 443 | INTERNET  | VIRTUAL_NETWORK | ロジック アプリの実行履歴を表示するコンピューターの IP アドレス。 このポートを閉じたりブロックしたりしても実行履歴を表示できますが、その実行履歴に含まれる各ステップの入出力は表示されなくなります。 |
| 接続管理 | 送信 | 443 | VIRTUAL_NETWORK  | INTERNET | |
| 診断ログとメトリックの発行 | 送信 | 443 | VIRTUAL_NETWORK  | AzureMonitor | |
| Logic Apps デザイナー - 動的プロパティ | 受信 | 454 | INTERNET  | VIRTUAL_NETWORK | 要求は、Logic Apps の[リージョン内のアクセス エンドポイント受信 IP アドレス](../logic-apps/logic-apps-limits-and-config.md#inbound)から送信されます。 |
| App Service の管理の依存関係 | 受信 | 454、455 | AppServiceManagement | VIRTUAL_NETWORK | |
| コネクタのデプロイ | 受信 | 454 および 3443 | INTERNET  | VIRTUAL_NETWORK | コネクタのデプロイと更新に必要。 このポートを閉じたりブロックしたりすると、ISE のデプロイが失敗し、コネクタの更新や修正ができなくなります。 |
| Azure SQL 依存関係 | 送信 | 1433 | VIRTUAL_NETWORK | SQL |
| Azure Resource Health | 送信 | 1886 | VIRTUAL_NETWORK | INTERNET | 正常性の状態を Resource Health に公開するために必要 |
| API Management - 管理エンドポイント | 受信 | 3443 | APIManagement  | VIRTUAL_NETWORK | |
| ログから Event Hub ポリシーおよび監視エージェントへの依存関係 | 送信 | 5672 | VIRTUAL_NETWORK  | EventHub | |
| ロール インスタンス間での Azure Cache for Redis インスタンスへのアクセス | 受信 <br>送信 | 6379 - 6383 | VIRTUAL_NETWORK  | VIRTUAL_NETWORK | また、Azure Cache for Redis で動作する ISE の場合は、[「Azure Cache for Redis の FAQ」で説明されている送信ポートと受信ポート](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements)を開く必要があります。 |
| Azure Load Balancer | 受信 | * | AZURE_LOAD_BALANCER | VIRTUAL_NETWORK |  |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>ISE を作成する

統合サービス環境 (ISE) を作成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) の Azure メイン メニューで、**[リソースの作成]** を選択します。

   ![新しいリソースの作成](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. 検索ボックスに、フィルターとして「統合サービス環境」と入力します。
結果リストで **[統合サービス環境 (プレビュー)]** を選択し、**[作成]** を選択します。

   ![[統合サービス環境] を選択する](./media/connect-virtual-network-vnet-isolated-environment/select-integration-service-environment.png)

   ![[作成] を選択する](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. 次の例のように、環境について次の詳細を指定してから **[確認と作成]** を選択します。

   ![環境の詳細を指定する](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **サブスクリプション** | はい | <*Azure サブスクリプション名*> | 環境に使用する Azure サブスクリプション |
   | **リソース グループ** | はい | <*Azure-resource-group-name*> | 環境を作成する Azure リソース グループ |
   | **統合サービス環境の名前** | はい | <*environment-name*> | 環境を示す名前 |
   | **場所** | はい | <*Azure-datacenter-region*> | 環境をデプロイする Azure データセンター リージョン |
   | **追加容量** | はい | 0、1、2、3 | この ISE リソースに使用する処理ユニット数。 作成後に容量を追加する場合は、「[容量を追加する](#add-capacity)」を参照してください。 |
   | **Virtual Network** | はい | <*Azure-virtual-network-name*> | 環境内のロジック アプリが仮想ネットワークにアクセスできるように、その環境を挿入する Azure 仮想ネットワーク。 ネットワークがない場合は、ここで作成することができます。 <p>**重要**:ISE を作成するときに "*のみ*"、この挿入を実行することができます。 ただし、この関係を作成する前に、必ず Azure Logic Apps 用に仮想ネットワークにロールベースのアクセス制御を設定しておきます。 |
   | **サブネット** | はい | <*subnet-resource-list*> | ISE では、環境内にリソースを作成するために "*空の*" サブネットが 4 つ必要です。 各サブネットを作成するには、[この表の下の手順に従います](#create-subnet)。  |
   |||||

   <a name="create-subnet"></a>

   **サブネットを作成する**

   環境内にリソースを作成する場合、どのサービスにも委任されていない*空の*サブネットが 4 つ ISE に必要です。 
   環境の作成後に、これらのサブネット アドレスを変更することは*できません*。 各サブネットは、次の基準を満たしている必要があります。

   * 名前の先頭がアルファベット文字かアンダースコアで、名前に `<`、`>`、`%`、`&`、`\\`、`?`、`/` の文字が含まれていない。

   * [Classless Inter-Domain Routing (CIDR) 形式](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)とクラス B アドレス空間を使用する。

   * 各サブネットに*少なくとも* 32 個のアドレスが必要なため、そのアドレス空間で最低でも `/27` を使用する。 例: 

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
      * **アドレス範囲 (CIDR ブロック)**:仮想ネットワークのサブネットの範囲 (CIDR 形式)

      ![サブネットの詳細を追加する](./media/connect-virtual-network-vnet-isolated-environment/subnet-details.png)

   1. 完了したら、**[OK]** を選びます。

   1. さらに 3 つのサブネットについてこれらの手順を繰り返します。

1. Azure で ISE 情報の検証が正常に完了したら、次の例のように **[作成]** を選択します。

   ![検証が完了したら [作成] を選択します](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure で環境のデプロイが開始されますが、このプロセスが完了するまでに最長 2 時間かかる "*可能性があります*"。 
   展開の状態を確認するには、Azure ツールバーで通知アイコンを選択します。これで通知ウィンドウが開きます。

   ![展開の状態を確認する](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   デプロイが正常に完了すると、Azure に次の通知が表示されます。

   ![デプロイメント成功](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

   > [!NOTE]
   > デプロイが失敗するか、ISE を削除した場合、サブネットがリリースされるまでに最長 1 時間かかる "*可能性があります*"。 そのため、状況に応じて、このようなサブネットを他の ISE で再利用できるようになるまで待つ必要があります。

1. 展開の完了後に Azure で環境が自動的に表示されない場合は、環境を表示するために **[リソースに移動]** を選択します。  

<a name="add-capacity"></a>

### <a name="add-capacity"></a>容量を追加する

ISE 基本単位の容量は固定されているため、さらにスループットが必要な場合はスケール ユニットを追加できます。 パフォーマンス メトリックや処理ユニット数に基づいて自動スケーリングできます。 メトリックに基づいた自動スケーリングを選択する場合は、さまざまな条件から選択し、その条件を満たすしきい値条件を指定できます。

1. Azure portal で ISE を見つけます。

1. ISE のパフォーマンス メトリックを表示するには、ISE のメイン メニューで、**[概要]** を選択します。

1. 自動スケーリングを設定するには、**[設定]** で **[スケールアウト]** を選択します。**[構成]** タブで、**[自動スケーリングの有効化]** を選択します。

1. **[既定値]** セクションで、**[メトリックに基づいてスケーリングする]** または **[特定のインスタンス数にスケーリングする]** のいずれかを選択します。

1. インスタンス ベースを選択した場は、0 から 3 の範囲で処理ユニット数を入力します。 メトリック ベースの場合は、次の手順に従います。

   1. **[既定値]** セクションで、**[ルールの追加]** を選択します。

   1. **[スケール ルール]** ウィンドウで、ルールがトリガーされる条件とその際に実行するアクションを設定します。

   1. 完了したら、**[追加]** を選択します。

1. 完了したら、忘れずに変更を保存してください。

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>ロジック アプリを作成する - ISE

統合サービス環境 (ISE) を使用するロジック アプリを作成するには、[ロジック アプリの作成方法](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関するページに記載されている手順に従いますが、次の違いがあります。 

* ロジック アプリを作成したら、次の例のように **[場所]** プロパティの **[統合サービス環境]** セクションから使用する ISE を選択します。

  ![統合サービス環境を選択する](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-integration-service-environment.png)

* HTTP など、同じ組み込みトリガーとアクションを使用できます。これらは、ロジック アプリと同じ ISE 内で動作します。 **ISE** ラベルがあるコネクタも、ロジック アプリと同じ ISE 内で動作します。 **ISE** ラベルがないコネクタはグローバルな Logic Apps サービスで実行されます。

  ![ISE のコネクタを選択する](./media/connect-virtual-network-vnet-isolated-environment/select-ise-connectors.png)

* Azure 仮想ネットワークに ISE を挿入した後は、ISE のロジック アプリからその仮想ネットワーク内のリソースに直接アクセスできます。 仮想ネットワークに接続されているオンプレミス システムの場合は、そのネットワークに ISE を挿入し、次のいずれかの項目を使用することで、ロジック アプリからそれらのシステムに直接アクセスできるようになります。 

  * そのシステムの ISE コネクタ (SQL Server など)
  
  * HTTP アクション 
  
  * カスタム コネクタ

  仮想ネットワーク内にない、または ISE コネクタがないオンプレミス システムの場合は、まず[オンプレミスのデータ ゲートウェイを設定します](../logic-apps/logic-apps-gateway-install.md)。

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>統合アカウントを作成する - ISE

統合サービス環境 (ISE) でロジック アプリに統合アカウントを使用するには、その統合アカウントでロジック アプリと*同じ環境*を使用する必要があります。 ISE のロジック アプリは、同じ ISE 内の統合アカウントのみを参照できます。 

ISE を使用する統合アカウントを作成するには、[統合アカウントの作成方法](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)に関する記事の手順を実行します。ただし、**[統合サービス環境]** セクションが表示されるようになった **[場所]** プロパティを除きます。 次の例のように、リージョンではなく ISE を選択します。

![統合サービス環境を選択する](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、<a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">Azure Logic Apps フォーラム</a>にアクセスしてください。
* 機能のアイデアについて投稿や投票を行うには、<a href="https://aka.ms/logicapps-wish" target="_blank">Logic Apps のユーザー フィードバック サイト</a>にアクセスしてください。

## <a name="next-steps"></a>次の手順

* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) の詳細を理解する
* [Azure サービスの仮想ネットワーク統合](../virtual-network/virtual-network-for-azure-services.md)について理解する
