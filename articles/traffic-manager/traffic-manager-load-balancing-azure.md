---
title: Azure で負荷分散サービスを使用する | Microsoft Docs
description: このチュートリアルでは、Azure の負荷分散ポートフォリオであるTraffic Manager、Application Gateway、Load Balancer を使用したシナリオの作成方法を説明します。
services: traffic-manager
documentationcenter: ''
author: duongau
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2016
ms.author: duau
ms.openlocfilehash: eaf50f3bdacaf5680bc5ecb1379faff20133b5ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98184375"
---
# <a name="using-load-balancing-services-in-azure"></a>Azure で負荷分散サービスを使用する

## <a name="introduction"></a>はじめに

Microsoft Azure では、ネットワーク トラフィックの分散や負荷分散の方法を管理するための複数のサービスを利用できます。 これらのサービスは、ニーズに応じて、個別に使用できるほか、それらのメソッドを組み合わせて最適なソリューションをビルドすることができます。

このチュートリアルでは、最初に顧客のユース ケースを定義し、Azure の負荷分散ポートフォリオであるTraffic Manager、Application Gateway、Load Balancer を使用したシナリオの作成方法を説明します。 その後、地理的に冗長性を持たせ、トラフィックを VM に分散し、さまざまな種類のリクエストを管理できるデプロイを作成する手順を詳しく説明します。

概念レベルでは、これらの各サービスは、負荷分散の階層において個別の役割を果たします。

* **Traffic Manager** には、グローバルな DNS グローバル負荷分散機能が備わっています。 これは受信 DNS 要求を調べ、ユーザーが選択したルーティング ポリシーに従って、正常なエンドポイントを使用して応答できます。 ルーティング方法のオプションは次のとおりです。
  * 待機時間の点で最も近いエンドポイントに要求元を送信するパフォーマンスのルーティング。
  * すべてのトラフィックを 1 つのエンドポイントに送信し、その他のエンドポイントはすべてバックアップとする優先順位によるルーティング。
  * 各エンドポイントに割り当てた重み付けに基づいてトラフィックを分散する、重み付けラウンド ロビンによるルーティング。
  * ユーザーの地理的な場所に基づいてアプリケーション エンドポイントにトラフィックを分散する地理ベースのルーティング。
  * ユーザーのサブネット (IP アドレス範囲) に基づいてアプリケーション エンドポイントにトラフィックを分散するサブネット ベースのルーティング。
  * 単一の DNS 応答で、複数のアプリケーション エンドポイントの IP アドレスを送信することができる複数値ルーティング。

  クライアントは、Traffic Manager によって返されたエンドポイントに直接接続します。 Azure Traffic Manager は、エンドポイントに異常が発生したことを検出すると、クライアントを別の正常なインスタンスに誘導します。 サービスの詳細については、[Azure Traffic Manager のドキュメント](traffic-manager-overview.md)をご覧ください。
* **Application Gateway** は、アプリケーション配信コントローラー (ADC) をサービスとして提供することで、さまざまなレイヤー 7 負荷分散機能をアプリケーションで利用できるようにします。 これにより、顧客は CPU 集中型の TLS 終端を Application Gateway にオフロードすることによって Web ファームの生産性を最適化できます。 着信トラフィックのラウンド ロビン分散、Cookie ベースのセッション アフィニティ、URL パス ベースのルーティング、単一の Application Gateway の背後で複数の Web サイトをホストする機能など、その他のレイヤー 7 ルーティング機能も用意されています。 Application Gateway は、インターネット接続ゲートウェイ、または内部的にのみ使用されるゲートウェイのいずれかとして構成できるほか、この両方を組み合わせて使用することも可能です。 Application Gateway は Azure によって完全に管理され、非常にスケーラブルで、高い可用性を備えています。 管理しやすいように診断機能とログ機能が豊富に用意されています。
* **ロード バランサー** は、Azure SDN スタックの重要な構成要素であり、すべての UDP と TCP プロトコル向けの高パフォーマンス、低待機時間のレイヤー 4 負荷分散サービスを備えています。 受信接続と送信接続を管理します。 負荷分散されるパブリックおよび内部のエンドポイントを構成したり、TCP を使用して受信接続をバックエンド プールの送信先にマッピングする規則や、サービスの可用性を管理するための HTTP の正常性プローブ オプションを定義したりできます。

## <a name="scenario"></a>シナリオ

このシナリオの例では、イメージと動的に表示される Web ページの 2 種類のコンテンツを提供する簡単な Web サイトを使用します。 Web サイトには地理的な冗長性が必要であり、最も近い場所にいる (待機時間が最短の) ユーザーにサービスを提供する必要があります。 アプリケーション開発者は、/images/* のパターンに一致する URL を、Web ファームの残りではなく、VM の専用プールから提供することに決めました。

さらに、動的コンテンツを提供する既定の VM プールは、高可用性クラスターでホストされているバックエンドのデータベースと通信する必要があります。 デプロイ全体は Azure Resource Manager を通じて設定されます。

Traffic Manager、Application Gateway、ロード バランサーを使用すると、この Web サイトで次のような設計の目標を達成できます。

* **複数の地理的な冗長性**:1 つのリージョンがダウンしたときに、Traffic Manager によって、最も近いリージョンにトラフィックがシームレスにルーティングされます。アプリケーション所有者による操作は必要ありません。
* **待機時間の短縮**:顧客は Traffic Manager によって最寄りのリージョンに自動的に誘導されるため、Web ページのコンテンツを要求したときの待機時間が短縮されます。
* **独立したスケーラビリティ**:Web アプリケーションのワークロードはコンテンツの種類によって分けられるため、アプリケーション所有者は、要求ワークロードを互いに影響を与えることなく拡張できます。 Application Gateway では、指定した規則とアプリケーションの正常性に基づいて、適切なプールにトラフィックをルーティングできます。
* **内部負荷分散**:Load Balancer が高可用性クラスターの前に配置されているため、データベースのアクティブで正常なエンドポイントのみがアプリケーションに公開されます。 さらに、データベース管理者は、フロントエンド アプリケーションから独立してアクティブとパッシブのレプリカをクラスター全体に分散させることによって、ワークロードを最適化できます。 ロード バランサーは接続を高可用性クラスターに提供し、正常なデータベースのみが接続要求を受け取るようにします。

次の図は、このシナリオのアーキテクチャを示します。

![負荷分散アーキテクチャの図](./media/traffic-manager-load-balancing-azure/scenario-diagram.png)

> [!NOTE]
> この例は、Azure が提供する多数の負荷分散サービスの 1 つの例に過ぎません。 Traffic Manager、Application Gateway、ロード バランサーは、負荷分散のニーズに合わせて組み合わせて使用できます。 たとえば、TLS オフロードまたはレイヤー 7 の処理が必要ない場合は、Application Gateway の代わりに Load Balancer を使用できます。

## <a name="setting-up-the-load-balancing-stack"></a>負荷分散スタックの設定

### <a name="step-1-create-a-traffic-manager-profile"></a>手順 1:Traffic Manager プロファイルの作成

1. Azure Portal で、 **[リソースの作成]**  >  **[ネットワーク]**  >  **[Traffic Manager プロファイル]**  >  **[作成]** の順にクリックします。
2. 次の基本情報を入力します。

   * **Name**:Traffic Manager プロファイルに DNS プレフィックス名を指定します。
   * **ルーティング方法**:トラフィック ルーティング方法のポリシーを選択します。 この方法の詳細については、「[Traffic Manager のトラフィック ルーティング方法について](traffic-manager-routing-methods.md)」をご覧ください。
   * **サブスクリプション**:プロファイルが含まれるサブスクリプションを選択します。
   * **[リソース グループ]** :プロファイルが含まれるリソース グループを選択します。 新しいリソース グループまたは既存の リソース グループを選択できます。
   * **リソース グループの場所**:Traffic Manager サービスは、1 つの場所に固定されず、グローバルです。 ただし、Traffic Manager プロファイルに関連付けられたメタデータが存在するグループのリージョンを指定する必要があります。 この場所は、プロファイルの実行時の可用性については影響ありません。

3. **[作成]** をクリックすると、Traffic Manager プロファイルが生成されます。

   ![[Create Traffic Manager profile (Traffic Manager プロファイルの作成)] ブレード](./media/traffic-manager-load-balancing-azure/s1-create-tm-blade.png)

### <a name="step-2-create-the-application-gateways"></a>手順 2:Application Gateway を作成する

1. Azure Portal の左側のウィンドウで、 **[リソースの作成]**  >  **[ネットワーク]**  >  **[Application Gateway]** の順にクリックします。
2. Application Gateway に関する次の基本情報を入力します。

   * **Name**:アプリケーション ゲートウェイの名前。
   * **SKU サイズ**:Application Gateway のサイズ。S、M、L のオプションを使用できます。
   * **インスタンス数**:インスタンスの数。2 ～ 10 の値。
   * **[リソース グループ]** :Application Gateway を保持するリソース グループ。 既存のリソース グループまたは新しいリソース グループを使用できます。
   * **[場所]** :Application Gateway のリージョン。リソース グループと同じ場所にします。 仮想ネットワークとパブリック IP はゲートウェイと同じ場所に存在する必要があるため、この場所は重要です。
3. **[OK]** をクリックします。
4. Application Gateway の仮想ネットワーク、サブネット、フロントエンド IP、およびリスナーの構成を定義します。 このシナリオでは、フロント エンド IP アドレスは **[パブリック]** に設定されているため、後で Traffic Manager プロファイルにエンドポイントとして追加できます。
5. 次のいずれかのオプションでリスナーを構成します。
    * HTTP を使用する場合は、構成する必要はありません。 **[OK]** をクリックします。
    * HTTPS を使用する場合は、さらに構成が必要になります。 [Application Gateway の作成](../application-gateway/quick-create-portal.md)に関するページの手順 9.以降を参照してください。 構成が完了したら、 **[OK]** をクリックします。

#### <a name="configure-url-routing-for-application-gateways"></a>Application Gateway の URL ルーティングを構成する

バックエンド プールを選択するときは、パスベースの規則で構成される Application Gateway では、ラウンド ロビン分散だけでなく、要求 URL のパスのパターンを使用します。 このシナリオでは、"/images/\*" 含む URL をイメージ サーバー プールに誘導するための、パス ベースの規制を追加します。 Application Gateway のパス ベースの URL ルーティングの詳細については、[Application Gateway のパスベース ルールの作成](../application-gateway/create-url-route-portal.md)に関するページをご覧ください。

![Application Gateway の Web 層の図](./media/traffic-manager-load-balancing-azure/web-tier-diagram.png)

1. リソース グループから、前のセクションで作成した Application Gateway のインスタンスに移動します。
2. **[設定]** で **[バックエンド プール]** を選択し、 **[追加]** を選択して Web 層のバックエンド プールに関連付ける VM を追加します。
3. バックエンド プールの名前と、プールに存在するすべてのマシンの IP アドレスを入力します。 このシナリオでは、仮想マシンの 2 つのバックエンド サーバー プールを接続します。

   ![Application Gateway [バックエンド プールの追加]](./media/traffic-manager-load-balancing-azure/s2-appgw-add-bepool.png)

4. Application Gateway の **[設定]** で、 **[Rules (規則)]** を選択し、 **[パス ベース]** ボタンをクリックして規則を追加します。

   ![Application Gateway 規則の [パス ベース] ボタン](./media/traffic-manager-load-balancing-azure/s2-appgw-add-pathrule.png)

5. 次の情報を入力して規則を構成します。

   基本設定:

   + **Name**:ポータルでアクセス可能な規則のフレンドリ名です。
   + **リスナー**: 規則で使用するリスナーです。
   + **既定のバックエンド プール**:既定の規則で使用するバック エンド プールです。
   + **既定の HTTP 設定**:既定の規則で使用する HTTP 設定です。

   パス ベースの規則:

   + **Name**:パスベースの規則のフレンドリ名です。
   + **パス**:トラフィックの転送に使用するパスの規則です。
   + **バックエンド プール**:この規則で使用するバックエンド プールです。
   + **HTTP 設定**:この規則で使用する HTTP 設定です。

   > [!IMPORTANT]
   > パス:有効なパスはスラッシュ "/" で始める必要があります。 ワイルドカード "\*" は末尾にのみ使用できます。 有効な例としては、/xyz、/xyz\*、または /xyz/\* があります。

   ![Application Gateway [Add path-based rule (パスベースの規則を追加する)] ブレード](./media/traffic-manager-load-balancing-azure/s2-appgw-pathrule-blade.png)

### <a name="step-3-add-application-gateways-to-the-traffic-manager-endpoints"></a>手順 3:Traffic Manager のエンドポイントに Application Gateway を追加する

このシナリオでは、Traffic Manager を、別のリージョンにある Application Gateway (前の手順で構成したもの) に接続します。 Application Gateway の構成は終わっているため、次の手順では Traffic Manager プロファイルに Application Gateway を接続します。

1. Traffic Manager プロファイルを開きます。 そのためには、リソース グループを検索するか、 **[すべてのリソース]** で Traffic Manager プロファイルの名前を検索します。
2. 左側のウィンドウで **[エンドポイント]** を選択し、 **[追加]** をクリックしてエンドポイントを追加します。

   ![Traffic Manager エンドポイント [追加] ボタン](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint.png)

3. 次の情報を入力してエンドポイントを作成します。

   * **[種類]** :負荷分散するエンドポイントの種類を選択します。 このシナリオでは、 **[Azure エンドポイント]** を選択します。以前構成した Application Gateway のインスタンスに接続するためです。
   * **Name**:エンドポイントの名前を入力します。
   * **ターゲット リソースの種類**: **[パブリック IP アドレス]** を選択し、 **[ターゲット リソース]** で、前に構成した Application Gateway のパブリック IP を選択します。

   ![Traffic Manager の [エンドポイントの追加]](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint-blade.png)

4. これで、Traffic Manager プロファイルの DNS (この例では、TrafficManagerScenario.trafficmanager.net) でアクセスすることによって、設定をテストできます。 要求を再送信したり、別のリージョンに作成した VM/Web サーバーをアップまたはダウンさせたり、Traffic Manager プロファイルの設定を変更して、設定をテストできます。

### <a name="step-4-create-a-load-balancer"></a>手順 4:ロード バランサーの作成

このシナリオでは、ロード バランサーは Web 層から高可用性クラスター内のデータベースに接続を分散します。

高可用性データベース クラスターが SQL Server AlwaysOn を使用している場合は、1 つまたは複数の [Always On 可用性グループ リスナーを構成する方法](../azure-sql/virtual-machines/windows/availability-group-listener-powershell-configure.md)をご覧ください。

内部ロード バランサーの構成方法について詳しくは、「[Azure Portal での内部ロード バランサーの作成](../load-balancer/quickstart-load-balancer-standard-internal-portal.md)」をご覧ください。

1. Azure Portal の左側のウィンドウで、 **[リソースの作成]**  >  **[ネットワーク]**  >  **[ロード バランサー]** の順にクリックします。
2. ロード バランサーの名前を選択します。
3. **[タイプ]** を **[内部]** に設定し、適切な仮想ネットワークと、ロード バランサーが入るサブネットを選択します。
4. **[IP アドレスの割り当て ]** で、 **[動的]** または **[静的]** を選択します。
5. **[リソース グループ]** で、ロード バランサーのリソース グループを選択します。
6. **[場所]** で、ロード バランサーの該当するリージョンを選択します。
7. **[作成]** をクリックして、ロード バランサーを生成します。

#### <a name="connect-a-back-end-database-tier-to-the-load-balancer"></a>バックエンド データベース層をロード バランサーに接続する

1. リソース グループから、前の手順で作成したロード バランサーを見つけます。
2. **[設定]** で、 **[バックエンド プール]** 、 **[追加]** の順にクリックし、バックエンド プールを追加します。

   ![ロード バランサー [バックエンド プールの追加]](./media/traffic-manager-load-balancing-azure/s4-ilb-add-bepool.png)

3. バックエンド プールの名前を入力します。
4. 個々のマシンまたは可用性セットをバック エンド プールに追加します。

#### <a name="configure-a-probe"></a>プローブを構成する

1. ロード バランサーの **[設定]** で、 **[プローブ]** を選択して **[追加]** をクリックし、プローブを追加します。

   ![ロード バランサー [プローブの追加]](./media/traffic-manager-load-balancing-azure/s4-ilb-add-probe.png)

2. プローブの名前を入力します。
3. プローブの **[プロトコル]** を選択します。 データベースの場合は、HTTP プローブではなく TCP プローブを使用する場合が多くなります。 ロード バランサー プローブの詳細については、「[Load Balancer プローブを理解する](../load-balancer/load-balancer-custom-probe-overview.md)」をご覧ください。
4. プローブにアクセスするときに使用するデータベースの **[ポート]** を入力します。
5. **[間隔]** で、アプリケーションをプローブする頻度を指定します。
6. **[異常しきい値]** に、バックエンドの VM が異常とみなされるまでの、プローブの連続失敗回数を指定します。
7. **[OK]** をクリックして、プローブを作成します。

#### <a name="configure-the-load-balancing-rules"></a>負荷分散規則を構成する

1. ロード バランサーの **[設定]** で **[負荷分散規則]** を選択し、 **[追加]** をクリックして、規則を作成します。
2. 負荷分散規則の **名前** を入力します。
3. ロード バランサーの **[フロントエンド IP アドレス]** 、 **[プロトコル]** 、 **[ポート]** を選択します。
4. **[バックエンド ポート]** で、バックエンド プールで使用するポートを指定します。
5. **[バックエンド プール]** を選択し、上述の手順で作成した、規則を適用する **[プローブ]** を選択します。
6. **[セッション永続化]** で、セッションを永続化する方法を選択します。
7. **[アイドル タイムアウト]** で、アイドル タイムアウトまでの時間を分単位で指定します。
8. **[Floating IP]** で、 **[無効]** または **[有効]** を選択します。
9. **[OK]** をクリックして規則を作成します。

### <a name="step-5-connect-web-tier-vms-to-the-load-balancer"></a>手順 5:Web 層の VM をロード バランサーに接続する

これで、Web 層の VM で実行されるアプリケーションの IP アドレスとロード バランサーのフロントエンド ポートを構成でき、データベースに接続できるようになりました。 この構成は、これらの VM で実行されるアプリケーション専用のものです。 接続先 IP アドレスとポートの構成については、アプリケーションのマニュアルをご覧ください。 フロントエンドの IP アドレスを見つけるには、Azure Portal で、 **[ロード バランサーの設定]** のフロントエンド IP プールに移動します。

![ロード バランサー [フロントエンド IP プール] ナビゲーション ウィンドウ](./media/traffic-manager-load-balancing-azure/s5-ilb-frontend-ippool.png)

## <a name="next-steps"></a>次のステップ

* [Traffic Manager の概要](traffic-manager-overview.md)
* [Application Gateway の概要](../application-gateway/overview.md)
* [Azure Load Balancer の概要](../load-balancer/load-balancer-overview.md)