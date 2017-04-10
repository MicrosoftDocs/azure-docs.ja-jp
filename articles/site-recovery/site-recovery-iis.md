---
title: "Azure Site Recovery を使用して IIS ベースの多層 Web アプリケーションをレプリケートする | Microsoft Docs"
description: "この記事では、Azure Site Recovery を使用して IIS Web ファームの仮想マシンをレプリケートする方法について説明します。"
services: site-recovery
documentationcenter: 
author: nsoneji
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: nisoneji
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: b23624fc7e82af1cb593a1aedd138ae0d6637ae7
ms.lasthandoff: 03/29/2017


---
# <a name="replicate-a-multi-tier-iis-based-web-application-using-azure-site-recovery"></a>Azure Site Recovery を使用して IIS ベースの多層 Web アプリケーションをレプリケートする

## <a name="overview"></a>概要


アプリケーション ソフトウェアは、組織のビジネス生産性の原動力です。 さまざまな Web アプリケーションが組織のさまざまな目的に役立ちます。 給与支払い処理、金融応用、顧客向けの Web サイトなどのアプリケーションは、組織にとって非常に重要です。 生産性の損失を防ぐために、そして何よりも組織のブランド イメージが傷つかないようにするために、そうしたアプリケーションが常時稼働していることが重要になります。

重要な Web アプリケーションは、通常、Web、データベース、アプリケーションを異なる階層に配置した多層アプリケーションとしてセットアップされます。 こうしたアプリケーションでは、さまざまな階層に分散するほか、各層の複数のサーバーを使用して、トラフィックを負荷分散することもできます。 さらに、さまざまな階層と Web サーバーの間のマッピングには、静的 IP アドレスを使用できます。 フェールオーバー時には、こうしたマッピングの一部を更新する必要があります (特に、Web サーバーで複数の Web サイトが構成されている場合)。 Web アプリケーションで SSL を使用している場合は、証明書のバインドを更新する必要があります。

従来の非レプリケーション ベースの回復方法では、手動による一連の操作で、さまざまな構成ファイル、レジストリ設定、バインディング、カスタム コンポーネント (COM または .NET)、コンテンツ、証明書をバックアップし、ファイルを回復する必要があります。 こうした手法は、明らかに煩雑で、エラーが発生しやすいうえに、スケーラブルではありません。 たとえば、証明書をバックアップし忘れ、フェールオーバー後にサーバーの新しい証明書を購入しなければならなくなる可能性が十分あります。

優れた障害復旧ソリューションでは、上記の複雑なアプリケーション アーキテクチャの復旧計画をモデル化できる必要があります。また、さまざまな階層間のアプリケーション マッピングを処理するカスタム手順を追加できる必要もあります。それによって、障害が発生した場合に備えたワンクリックの確実なソリューションを提供し、RTO を短縮します。


この記事では、[Azure Site Recovery](site-recovery-overview.md) を使用して IIS ベースの Web アプリケーションを保護する方法について説明します。 また、IIS ベースの 3 層 Web アプリケーションを Azure にレプリケートするためのベスト プラクティス、障害復旧の訓練を実行する方法、およびアプリケーションを Azure にフェールオーバーする方法についても説明します。 

 
## <a name="prerequisites"></a>前提条件

開始する前に、以下を理解していることを確認してください。

1. [Azure への仮想マシンのレプリケート](site-recovery-vmware-to-azure.md)
1. [復旧ネットワークの設計](site-recovery-network-design.md)方法
1. [Azure へのテスト フェールオーバーの実行](./site-recovery-test-failover-to-azure.md)
1. [Azure へのフェールオーバーの実行](site-recovery-failover.md)
1. [ドメイン コントローラーのレプリケート](site-recovery-active-directory.md)方法
1. [SQL Server のレプリケート](site-recovery-sql.md)方法

## <a name="deployment-patterns"></a>デプロイ パターン
IIS ベースの Web アプリケーションは、通常、次のいずれかのデプロイ パターンに従います。

**デプロイ パターン 1** アプリケーション要求ルーティング処理 (ARR)、IIS サーバー、および Microsoft SQL Server を使用した IIS ベースの Web ファーム。 

![デプロイ パターン](./media/site-recovery-iis/deployment-pattern1.png)

**デプロイ パターン 2** アプリケーション要求ルーティング処理 (ARR)、IIS サーバー、アプリケーション サーバー、および Microsoft SQL Server を使用した IIS ベースの Web ファーム。 


![デプロイ パターン](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Site Recovery のサポート

この記事の VMware 仮想マシンを作成するために、Windows Server 2012 R2 Enterprise の IIS サーバー バージョン 7.5 を使用しました。 Site Recovery レプリケーションはアプリケーションに依存しないため、ここで紹介する推奨事項は次のシナリオや別のバージョンの IIS にも適用できます。

### <a name="source-and-target"></a>ソースとターゲット

**シナリオ** | **セカンダリ サイトへ** | **Azure へ**
--- | --- | ---
**Hyper-V** | はい | はい
**VMware** | はい | はい
**物理サーバー** | いいえ | はい

## <a name="replicate-virtual-machines"></a>仮想マシンのレプリケート

[このガイダンス](site-recovery-vmware-to-azure.md)に従って、Azure への IIS Web ファームの全仮想マシンのレプリケートを開始します。 

静的 IP を使用している場合は、[コンピューティングとネットワーク] 設定の [**[ターゲット IP]**](./site-recovery-replicate-vmware-to-azure.md#view-and-manage-vm-properties) 設定で仮想マシンに割り当てる IP を指定します。 

![[ターゲット IP]](./media/site-recovery-active-directory/dns-target-ip.png)


## <a name="creating-a-recovery-plan"></a>復旧計画の作成

復旧計画では、多層アプリケーションのさまざまな階層のフェールオーバーをシーケンス処理できるため、アプリケーションの整合性が維持されます。 以下の手順に従って、多層 Web アプリケーションの復旧計画を作成します。  復旧計画の作成の詳細については、[こちら](./site-recovery-create-recovery-plans.md)を参照してください。

### <a name="adding-virtual-machines-to-failover-groups"></a>フェールオーバー グループへの仮想マシンの追加
一般的な IIS 多層 Web アプリケーションは、SQL 仮想マシンを使用したデータベース層、IIS サーバーを使用した Web 層、アプリケーション層で構成されます。 以下のように、これらの仮想マシンをすべて階層に応じた異なるグループに追加します。 復旧計画のカスタマイズの詳細については、[こちら](site-recovery-runbook-automation.md#customize-the-recovery-plan)を参照してください。

1. 復旧計画を作成します。 データベース層の仮想マシンをグループ 1 に追加して、最後にシャットダウンされ、最初に起動するようにします。 

1. アプリケーション層の仮想マシンをグループ 2 に追加して、データベース層の起動後に起動するようにします。

1. Web 層の仮想マシンをグループ 3 に追加して、アプリケーション層の起動後に起動するようにします。

1. 負荷分散の仮想マシンをグループ 4 に追加して、Web 層の起動後に起動するようにします。


### <a name="adding-scripts-to-the-recovery-plan"></a>復旧計画へのスクリプトの追加
フェールオーバーまたはテスト フェールオーバーの後に IIS Web ファームが正常に機能するように、Azure の仮想マシンに対して操作を実行することが必要な場合があります。 DNS エントリの更新、サイト バインドの変更、接続文字列の変更などのフェールオーバー後の操作は、次に示すように、対応するスクリプトを復旧計画に追加することで自動化できます。 復旧計画へのスクリプトの追加の詳細については、[こちら](./site-recovery-create-recovery-plans.md#add-scripts)を参照してください。

#### <a name="dns-update"></a>DNS の更新
動的 DNS 更新用に DNS が構成されている場合は、通常、仮想マシンの起動後に、新しい IP で DNS を更新します。 仮想マシンの新しい IP で DNS を更新する明示的な手順を追加する場合、この [DNS の IP を更新するスクリプト](https://aka.ms/asr-dns-update)を復旧計画グループの事後アクションとして追加します。  

#### <a name="connection-string-in-an-applications-webconfig"></a>アプリケーションの web.config 内の接続文字列
接続文字列では、Web サイトが通信するデータベースを指定します。 

接続文字列にデータベースの仮想マシンの名前が含まれている場合は、フェールオーバー後にそれ以上の手順は不要です。アプリケーションは DB と自動的に通信できるようになります。 また、データベースの仮想マシンの IP アドレスが保持される場合は、接続文字列を更新する必要もありません。 接続文字列で IP アドレスを使用してデータベースの仮想マシンを参照している場合は、フェールオーバー後に更新する必要があります。 例: 以下の接続文字列では、IP 127.0.1.2 で DB を参照しています。 

        <?xml version="1.0" encoding="utf-8"?> 
        <configuration> 
        <connectionStrings> 
        <add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" /> 
        </connectionStrings> 
        </configuration>

Web 層で接続文字列を更新するには、復旧計画のグループ 3 の後に [IIS 接続更新スクリプト](https://aka.ms/asr-update-webtier-script-classic)を追加します。

#### <a name="site-bindings-for-the-application"></a>アプリケーションのサイト バインド
すべてのサイトは、バインドの種類、IIS サーバーがサイトに対する要求をリッスンする IP アドレス、サイトのポート番号とホスト名で構成されています。 フェールオーバー時に、関連付けられている IP アドレスが変更された場合、これらのバインドを更新する必要があります。 

> [!NOTE]
> 
> 次の例に示すように、サイト バインドに [未使用の IP アドレスすべて] を指定した場合は、フェールオーバー後にこのバインドを更新する必要はありません。 また、サイトに関連付けられている IP アドレスがフェールオーバー後に変更されない場合も、サイト バインドを更新する必要はありません (IP アドレスのリテンション期間は、プライマリ サイトと復旧サイトに割り当てられているネットワーク アーキテクチャとサブネットによって異なります。そのため、組織によっては実現できない場合があります)。 

![SSL バインド](./media/site-recovery-iis/sslbinding.png)

サイトに IP アドレスを関連付けている場合は、新しい IP アドレスですべてのサイト バインドを更新する必要があります。 サイト バインドを変更する [IIS Web 層更新スクリプト](https://aka.ms/asr-web-tier-update-runbook-classic)を復旧計画のグループ 3 の後に追加できます。 


#### <a name="update-load-balancer-ip-address"></a>ロード バランサーの IP アドレスの更新
アプリケーション要求ルーティング処理の仮想マシンがある場合は、IP アドレスを更新する [IIS ARR フェールオーバー スクリプト](https://aka.ms/asr-iis-arrtier-failover-script-classic)をグループ 4 の後に追加します。

#### <a name="the-ssl-cert-binding-for-an-https-connection"></a>HTTPS 接続用の SSL 証明書バインド
Web サイトには、Web サーバーとユーザーのブラウザー間の通信をセキュリティで確実に保護するために役立つ SSL 証明書を関連付けることができます。 Web サイトで HTTPS 接続を利用でき、IIS サーバーの IP アドレスへの HTTPS サイト バインドが SSL 証明書バインドに関連付けられている場合、フェールオーバー後の IIS 仮想マシンの IP を使用した証明書の新しいサイト バインドを追加する必要があります。 

次に対する SSL 証明書を発行できます。 

a) Web サイトの完全修飾ドメイン名<br>
b) サーバーの名前<br>
c) ドメイン名のワイルドカード証明書<br>
d) IP アドレス - SSL 証明書が IIS サーバーの IP に対して発行されている場合、別の SSL 証明書を Azure サイトの IIS サーバーの IP アドレスに対して発行し、その証明書の追加の SSL バインドを作成する必要があります。 そのため、IP に対して発行された SSL 証明書を使用しないことをお勧めします。 これはあまり広く使用されていないオプションであり、新しい CA/ブラウザー フォーラムの変更に伴って間もなく廃止されます。

#### <a name="update-the-dependency-between-the-web-and-the-application-tier"></a>Web 層とアプリケーション層の依存関係の更新
仮想マシンの IP アドレスに基づいたアプリケーション固有の依存関係がある場合は、フェールオーバー後にその依存関係を更新する必要があります。

## <a name="doing-a-test-failover"></a>テスト フェールオーバーの実行
[このガイダンス](site-recovery-test-failover-to-azure.md)に従って、テスト フェールオーバーを実行します。

1.    Azure Portal に移動し、Recovery Services コンテナーを選択します。
1.    IIS Web ファーム用に作成された復旧計画をクリックします。
1.    [テスト フェールオーバー] をクリックします。
1.    テスト フェールオーバー プロセスを開始する復旧ポイントと Azure 仮想ネットワークを選択します。
1.    セカンダリ環境が立ち上がったら、検証を行うことができます。
1.    検証が完了したら、[Validations Complete (検証完了)] をクリックします。テスト フェールオーバー環境がクリーニングされます。

## <a name="doing-a-failover"></a>フェールオーバーの実行
[このガイダンス](site-recovery-failover.md)に従って、フェールオーバーを実行します。

1.    Azure Portal に移動し、Recovery Services コンテナーを選択します。
1.    IIS Web ファーム用に作成された復旧計画をクリックします。
1.    [フェールオーバー] をクリックします。
1.    フェールオーバー プロセスを開始する復旧ポイントを選択します。

## <a name="next-steps"></a>次のステップ
Site Recovery を使用した他のアプリケーションのレプリケートの詳細については、[こちら](site-recovery-workload.md)を参照してください。 

