---
title: Azure Site Recovery を使用して多層 IIS ベース Web アプリケーションのディザスター リカバリーを設定する | Microsoft Docs
description: Azure Site Recovery を使用して IIS Web ファームの仮想マシンをレプリケートする方法について説明します。
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: aa42371692cd5d0dc96835db5b66fe0877b90665
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2019
ms.locfileid: "54320499"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-iis-based-web-application"></a>多層 IIS ベース Web アプリケーションのディザスター リカバリーの設定

アプリケーション ソフトウェアは、組織のビジネス生産性の原動力です。 さまざまな Web アプリケーションが組織のさまざまな目的に役立ちます。 アプリケーションによっては、組織にとって不可欠なものとなる場合もあります (給与処理、財務、顧客向け Web サイトに使用されるアプリケーションなど)。 生産性の低下を防ぐには、それらのアプリケーションを常時稼働させる必要があります。 さらに、それらのアプリケーションを一貫性をもって使用できるようにすることは、組織のブランドやイメージが損なわれるのを防ぐことにもつながります。

重要な Web アプリケーションは通常、多層アプリケーションとして設定され、Web、データベース、およびアプリケーションがそれぞれ異なる階層に配置されます。 アプリケーションを各層に分散するだけでなく、各層で複数のサーバーを使用して、トラフィックの負荷を分散することもあります。 さらに、各層と Web サーバー間のマッピングに、静的 IP アドレスが使用されることもあります。 フェールオーバーの際には、これらマッピングの一部を更新する必要があります (特に、Web サーバーで複数の Web サイトが構成されている場合)。 Web アプリケーションで SSL を使用している場合には、証明書のバインドを更新する必要があります。

レプリケーションを使用しない従来の復旧方法では、さまざまな構成ファイル、レジストリ設定、バインド、カスタム コンポーネント (COM または .NET)、コンテンツ、および証明書をバックアップする必要があります。 各ファイルは、一連の手動の手順を通じて復旧されます。 ファイルをバックアップして手動で復旧する従来の復旧方法は、手間がかかるうえ、エラーが生じやすく、拡張性にも欠いています。 たとえば、証明書をバックアップし忘れることも起こりやすくなります。 その場合は、フェールオーバー後にサーバーの新しい証明書を購入するしか手段がなくなります。

優れたディザスター リカバリー ソリューションとは、複雑なアプリケーション アーキテクチャの復旧計画をモデル化できるものでなければなりません。 また、復旧計画にカスタム手順を追加して、階層間のアプリケーション マッピングを操作できることも条件となります。 アプリケーション マッピングが整備されていれば、障害が発生した際にも、1 クリックで確実に問題を解決し、RTO の低減につなげることができます。

この記事では、[Azure Site Recovery](site-recovery-overview.md) を使用して、インターネット インフォメーション サービス (IIS) に基づく Web アプリケーションを保護する方法について説明します。 また、IIS ベースの 3 層 Web アプリケーションを Azure にレプリケートするためのベスト プラクティス、ディザスター リカバリーの訓練を実行する方法、およびアプリケーションを Azure にフェールオーバーする方法についても説明します。

## <a name="prerequisites"></a>前提条件

この記事の内容を学習するには、次のタスクの実行方法を知っている必要があります。

* [仮想マシンを Azure にレプリケートする](vmware-azure-tutorial.md)
* [復旧ネットワークを設計する](site-recovery-network-design.md)
* [Azure へのテスト フェールオーバーを実行する](site-recovery-test-failover-to-azure.md)
* [Azure へのフェールオーバーを実行する](site-recovery-failover.md)
* [ドメイン コントローラーをレプリケートする](site-recovery-active-directory.md)
* [SQL Server のレプリケート](site-recovery-sql.md)

## <a name="deployment-patterns"></a>デプロイ パターン
IIS ベースの Web アプリケーションは、通常、次のいずれかのデプロイ パターンでデプロイされます。

**デプロイ パターン 1**

アプリケーション要求ルーティング処理 (ARR)、IIS サーバー、および SQL Server を使用した IIS ベースの Web ファーム。

![3 つの階層がある IIS ベース Web ファームの図](./media/site-recovery-iis/deployment-pattern1.png)

**デプロイ パターン 2**

ARR、IIS サーバー、アプリケーション サーバー、および SQL Server を使用した IIS ベースの Web ファーム。

![4 つの階層がある IIS ベース Web ファームの図](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Site Recovery のサポート

この記事に示す例では、Windows Server 2012 R2 Enterprise 上で IIS 7.5 を使用した、VMware 仮想マシンを使用しています。 Site Recovery のレプリケーションはアプリケーションに固有のものではないため、この記事にある推奨事項は、次の表に示すシナリオで、IIS のバージョンごとに適用することを前提としています。

### <a name="source-and-target"></a>ソースとターゲット

シナリオ | セカンダリ サイトへ | Azure へ
--- | --- | ---
Hyper-V | はい | はい
VMware | はい | はい
物理サーバー | いいえ  | はい
Azure|NA|はい

## <a name="replicate-virtual-machines"></a>仮想マシンのレプリケート

すべての IIS Web ファーム仮想マシンを Azure にレプリケートするには、「[Site Recovery での Azure へのフェールオーバーをテストする](site-recovery-test-failover-to-azure.md)」のガイダンスに従います。

静的 IP アドレスを使用している場合は、仮想マシンに割り当てる IP アドレスを指定できます。 IP アドレスを設定するには、**[コンピューティングとネットワーク] の設定** > **[ターゲット IP]** に移動します。

![Site Recovery の [コンピューティングとネットワーク] ウィンドウでターゲット IP を設定する方法を示したスクリーン ショット](./media/site-recovery-active-directory/dns-target-ip.png)

## <a name="create-a-recovery-plan"></a>復旧計画の作成
復旧計画では、フェールオーバー時における多層アプリケーション内の各種階層の順序付けがサポートされます。 順序付けは、アプリケーションの一貫性の保守に役立ちます。 多層 Web アプリケーションの復旧計画を作成する際には、「[復旧計画の作成](site-recovery-create-recovery-plans.md)」で説明されている手順を完了します。

### <a name="add-virtual-machines-to-failover-groups"></a>フェールオーバー グループへの仮想マシンの追加
一般的な多層 IIS Web アプリケーションは、次のコンポーネントで構成されます。
* SQL 仮想マシンがあるデータベース層。
* IIS サーバーとアプリケーション層で構成される Web 層。 

階層に基づいて、各種グループに仮想マシンを追加します。

1. 復旧計画を作成します。 データベース層の仮想マシンをグループ 1 に追加します。 これにより、データベース層の仮想マシンが最後にシャット ダウンされ、最初に起動するようになります。
1. アプリケーション層の仮想マシンをグループ 2 に追加します。 これにより、アプリケーション層の仮想マシンは、データベース層が起動した後に起動されるようになります。
1. Web 層の仮想マシンをグループ 3 に追加します。 これにより、Web 層の仮想マシンは、アプリケーション層が起動した後に起動されるようになります。
1. 負荷分散仮想マシンをグループ 4 に追加します。 これにより、負荷分散仮想マシンは、Web 層が起動した後に起動されるようになります。

詳しくは、「[復旧計画のカスタマイズ](site-recovery-runbook-automation.md#customize-the-recovery-plan)」をご覧ください。


### <a name="add-a-script-to-the-recovery-plan"></a>復旧計画へのスクリプトの追加
IIS Web ファームを正常に機能させるには、フェールオーバー後、またはテスト フェールオーバー時に、Azure の仮想マシンに対して一定の操作を実行することが必要な場合があります。 フェールオーバー後の操作は一部自動化することもできます。 たとえば、対応するスクリプトを復旧計画に追加することで、DNS エントリを更新したり、サイトのバインドを変更したり、接続文字列を変更したりすることができます。 スクリプトを使用して自動化タスクを設定する方法については、「[Add a VMM script to a recovery plan (復旧計画に VMM スクリプトを追加する)](site-recovery-how-to-add-vmmscript.md)」で説明されています。

#### <a name="dns-update"></a>DNS の更新
DNS が動的 DNS 更新用に構成されている場合、仮想マシンは通常、その起動後に新しい IP アドレスで DNS を更新します。 仮想マシンの新しい IP アドレスで DNS を更新するための明示的な手順を追加する場合は、[DNS の IP を更新するためのスクリプト](https://aka.ms/asr-dns-update)を、復旧計画グループのフェールオーバー後のアクションとして追加します。  

#### <a name="connection-string-in-an-applications-webconfig"></a>アプリケーションの web.config 内の接続文字列
接続文字列では、Web サイトの通信先となるデータベースを指定します。 接続文字列にデータベース仮想マシンの名前が含まれている場合は、フェールオーバー後の手順を追加する必要はありません。 アプリケーションは自動的に、データベースと通信できるようになります。 また、データベース仮想マシンの IP アドレスが保持される場合、接続文字列を更新する必要はありません。 

接続文字列が IP アドレスを使用してデータベース仮想マシンを参照する場合は、フェールオーバー後に接続文字列を更新する必要があります。 たとえば、次の接続文字列では、IP アドレス 127.0.1.2 でデータベースを参照しています。

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
        <connectionStrings>
        <add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
        </connectionStrings>
        </configuration>

Web 層の接続文字列を更新するには、復旧計画のグループ 3 の後に、[IIS 接続更新スクリプト](https://aka.ms/asr-update-webtier-script-classic)を追加します。

#### <a name="site-bindings-for-the-application"></a>アプリケーションのサイト バインド
すべてのサイトは、バインド情報で構成されます。 バインド情報には、バインドの種類、IIS サーバーがサイトに対する要求をリッスンする IP アドレス、ポート番号、およびサイトのホスト名が含まれます。 フェールオーバーの際、バインドに関連付けられている IP アドレスに変更がある場合には、これらのバインドの更新が必要になることがあります。

> [!NOTE]
>
> サイト バインドを **[すべて未割り当て]** に設定した場合は、そのバインドをフェールオーバー後に更新する必要はありません。 また、サイトに関連付けられた IP アドレスがフェールオーバー後に変更されない場合は、サイト バインドを更新する必要はありません。 (IP アドレスのリテンション期間は、プライマリ サイトと復旧サイトに割り当てられたネットワーク アーキテクチャとサブネットによって決まります。 これを更新することは、組織にとって適切でない場合もあります。)

![SSL バインドの設定画面を示すスクリーン ショット](./media/site-recovery-iis/sslbinding.png)

IP アドレスをサイトに関連付けた場合は、すべてのサイト バインドを新しい IP アドレスで更新する必要があります。 サイト バインドを変更するには、復旧計画のグループ 3 の後に、[IIS Web 層更新スクリプト](https://aka.ms/asr-web-tier-update-runbook-classic)を追加します。

#### <a name="update-the-load-balancer-ip-address"></a>ロード バランサーの IP アドレスの更新
ARR 仮想マシンがある場合、IP アドレスを更新するには、グループ 4 の後に [IIS ARR フェールオーバー スクリプト](https://aka.ms/asr-iis-arrtier-failover-script-classic)を追加します。

#### <a name="ssl-certificate-binding-for-an-https-connection"></a>HTTPS 接続用の SSL 証明書バインド
Web サイトには、Web サーバーとユーザーのブラウザー間の通信をセキュリティで保護するための、SSL 証明書が関連付けられている場合があります。 Web サイトで HTTPS 接続が使用されていて、かつ IIS サーバーの IP アドレスへの HTTPS サイト バインドが SSL 証明書バインドに関連付けられている場合は、フェールオーバー後に IIS 仮想マシンの IP アドレスを使用して、証明書の新しいサイト バインドを追加する必要があります。

SSL 証明書は、次のコンポーネントに対して発行できます。

* Web サイトの完全修飾ドメイン名。
* サーバーの名前。
* ドメイン名のワイルドカード証明書。  
* IP アドレス。 SSL 証明書が IIS サーバーの IP アドレスに対して発行されている場合、Azure サイト上にある IIS サーバーの IP アドレスに対して別の SSL 証明書を発行し、 その証明書の追加の SSL バインドを作成する必要があります。 そのため、IP アドレスに対して発行された SSL 証明書は使用しないことをお勧めします。 この方法はあまり広くは使用されておらず、新しい証明書機関/ブラウザー フォーラムの変更に従って、間もなく非推奨となる予定です。

#### <a name="update-the-dependency-between-the-web-tier-and-the-application-tier"></a>Web 層とアプリケーション層の依存関係の更新
仮想マシンの IP アドレスに基づいたアプリケーション固有の依存関係がある場合は、フェールオーバー後にその依存関係を更新する必要があります。

## <a name="run-a-test-failover"></a>テスト フェールオーバーの実行

1. Azure Portal で､Recovery Service コンテナーを選択します。
2. IIS Web ファーム用に作成した復旧計画を選択します。
3. **[テスト フェールオーバー]** を選択します。
4. テスト フェールオーバー プロセスを開始するには、復旧ポイントと Azure 仮想ネットワークを選択します。
5. セカンダリ環境が立ち上がったら、検証を行うことができます。
6. 検証が完了したら、テスト フェールオーバー環境をクリーニングするために、**[検証が完了しました]** をクリックします。

詳しくは、「[Site Recovery での Azure へのフェールオーバーをテストする](site-recovery-test-failover-to-azure.md)」をご覧ください。

## <a name="run-a-failover"></a>フェールオーバーの実行

1. Azure Portal で､Recovery Service コンテナーを選択します。
1. IIS Web ファーム用に作成した復旧計画を選択します。
1. **[フェールオーバー]** を選択します。
1. フェールオーバー プロセスを開始するには、復旧ポイントを選択します。

詳しくは、「[Site Recovery でのフェールオーバー](site-recovery-failover.md)」をご覧ください。

## <a name="next-steps"></a>次の手順
* Site Recovery を利用した[他のアプリケーションのレプリケーション](site-recovery-workload.md)に関する記事をご覧ください。
