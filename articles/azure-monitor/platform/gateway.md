---
title: Log Analytics ゲートウェイを使ってコンピューターを接続する | Microsoft Docs
description: デバイスと Operations Manager で監視されたコンピューターがインターネットにアクセスできないときに、Log Analytics ゲートウェイを使ってそれらを接続して Azure Automation および Log Analytics サービスにデータを送信します。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 10/30/2019
ms.openlocfilehash: 7574f5c17c1b4598336b8db3108946164dc203f2
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847276"
---
# <a name="connect-computers-without-internet-access-by-using-the-log-analytics-gateway-in-azure-monitor"></a>インターネットにアクセスできないコンピューターを Azure Monitor で Log Analytics ゲートウェイを使って接続する

>[!NOTE]
>Microsoft Operations Management Suite (OMS) から Microsoft Azure Monitor への移行に伴って用語が変更されます。 この記事では、OMS ゲートウェイは Azure Log Analytics ゲートウェイと呼ばれます。 
>

この記事では、直接接続されたコンピューターまたは Operations Manager で監視されているコンピューターがインターネットにアクセスできないときに、Azure Monitor ゲートウェイを使用して Azure Automation および Log Analytics との通信を構成する方法について説明します。 

Log Analytics ゲートウェイは、HTTP CONNECT コマンドを使って HTTP トンネリングをサポートする HTTP 転送プロキシです。 このゲートウェイから、インターネットに直接接続できないコンピューターに代わって Azure Monitor の Azure Automation および Log Analytics ワークスペースにデータが送信されます。 

Log Analytics ゲートウェイでは、以下をサポートしています。

* Log Analytics の背後にある各エージェントに対して構成されている同じ Log Analytics ワークスペースにレポートします。これは、Azure Automation Hybrid Runbook Worker を使用して構成されています。  
* Microsoft Monitoring Agent が Azure Monitor 内の Log Analytics ワークスペースに直接接続されている Windows コンピューター。
* Log Analytics エージェント for Linux が Azure Monitor 内の Log Analytics ワークスペースに直接接続されている Linux コンピューター。  
* System Center Operations Manager 2012 SP1 with UR7、Operations Manager 2012 R2 with UR3、Operations Manager 2016 以降の管理グループは Log Analytics に統合されました。  

一部の IT セキュリティ ポリシーでは、ネットワーク コンピューターのインターネット接続が許可されません。 このような未接続のコンピューターには、販売時点管理 (POS) デバイスや IT サービスをサポートするサーバーなどがあります。 これらのデバイスを Azure Automation または Log Analytics ワークスペースに接続して管理および監視できるようにするには、Log Analytics ゲートウェイと直接通信するようにデバイスを構成します。 Log Analytics ゲートウェイでは、それらの代わりに構成情報を受信してデータを転送できます。 これに対して、コンピューターに Log Analytics ワークスペースと直接接続している Log Analytics エージェントを構成した場合には、コンピューターが Log Analytics ゲートウェイと通信します。  

Log Analytics ゲートウェイはエージェントからのデータをサービスに直接転送します。 転送中のデータは分析されず、ゲートウェイはサービスとの接続が失われてもデータをキャッシュしません。 ゲートウェイがサービスと通信できなくなったとき、エージェントは引き続き実行され、監視対象コンピューターのディスクにある収集したデータのキューを実行します。 接続が回復したとき、エージェントは収集したキャッシュ データを Azure Monitor に送信します。

Log Analytics と Operations Manager 管理グループが統合している場合には、管理サーバーが Log Analytics ゲートウェイに接続して構成情報を受信し、収集されたデータを有効にしているソリューションに応じて送信するという構成が可能です。  Operations Manager エージェントは、一部のデータを管理サーバーに送信します。 たとえば、Operations Manager アラート、構成評価データ、インスタンス スペース データ、および容量データをエージェントが送信できます。 その他の大容量データ (インターネット インフォメーション サービス (IIS) のログ、パフォーマンス データ、セキュリティ イベントなど) は Log Analytics ゲートウェイに直接送信されます。 

境界ネットワークまたは分離されたネットワークで信頼されていないシステムを監視するために 1 つ以上の Operations Manager ゲートウェイ サーバーがデプロイされている場合、それらのサーバーが Log Analytics ゲートウェイと通信することはできません。  Operations Manager ゲートウェイ サーバーは、管理サーバーに対してのみレポートを送信できます。  Operations Manager 管理グループが Log Analytics ゲートウェイと通信するように構成されている場合は、Azure Monitor 用のログ データを収集するように構成されているエージェント型マネージド コンピューターそれぞれに対して、プロキシの構成情報が自動で配信されます (設定が空欄であっても同じです)。

ゲートウェイを経由して Log Analytics と通信する Operations Management グループまたは Log Analytics ワークスペースに直接接続しているグループに対して高可用性を実現するには、ネットワーク負荷分散 (NLB) を使って複数のゲートウェイ サーバーにトラフィックをリダイレクトし、トラフィックを分散させます。 これにより、ゲートウェイ サーバーが 1 台ダウンした場合には、トラフィックが別の利用可能なノードにリダイレクトされます。  

ゲートウェイが通信する必要のあるサービス エンドポイントを特定するには、Log Analytics ゲートウェイを実行するコンピューターに Log Analytics Windows エージェントが必要です。 また、エージェントは、ゲートウェイの背後にあるエージェントや Operations Manager 管理グループが構成されている同じワークスペースにレポートを送信するようゲートウェイに指示する必要があります。 この構成では、ゲートウェイとエージェントが、それらに割り当てられているワークスペースと通信できます。

ゲートウェイは最大 4 つのワークスペースへのマルチホームにすることができます。 これは、Windows エージェントがサポートするワークスペースの合計数です。  

各エージェントは、ゲートウェイとの間で双方向にデータを自動的に転送できるように、ゲートウェイにネットワーク接続する必要があります。 ドメイン コントローラーへのゲートウェイのインストールは避けてください。

次の図は、ゲートウェイを使った場合のダイレクト エージェントから Azure Automation および Log Analytics へのデータ フローを示したものです。 エージェントのプロキシ構成のポートは、Log Analytics ゲートウェイに対して構成されているポートと同じになっている必要があります。  

![ダイレクト エージェントとサービスの通信の図](./media/gateway/oms-omsgateway-agentdirectconnect.png)

次の図は、Operations Manager 管理グループから Log Analytics へのデータ フローを示したものです。   

![Operations Manager と Log Analytics の通信の図](./media/gateway/log-analytics-agent-opsmgrconnect.png)

## <a name="set-up-your-system"></a>システムを設定する

Log Analytics ゲートウェイを実行するよう指定されているコンピューターには次の構成が必要です。

* Windows 10、Windows 8.1、または Windows 7
* Windows Server 2016、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2、または Windows Server 2008
* Microsoft .NET Framework 4.5
* 最低 4 コアのプロセッサと 8 GB のメモリ 
* ゲートウェイ経由で通信するエージェントと同じワークスペースにレポートを送信するように構成されている [Windows 用の Log Analytics エージェント](agent-windows.md)

### <a name="language-availability"></a>利用可能な言語

Log Analytics ゲートウェイは、次の言語で利用できます。

- 簡体中国語
- 繁体中国語
- チェコ語
- オランダ語
- 英語
- フランス語
- ドイツ語
- ハンガリー語
- イタリア語
- 日本語
- 韓国語
- ポーランド語
- ポルトガル語 (ブラジル)
- ポルトガル語 (ポルトガル)
- ロシア語
- スペイン語 (インターナショナル)

### <a name="supported-encryption-protocols"></a>サポート対象の暗号化プロトコル

Log Analytics ゲートウェイは、トランスポート層セキュリティ (TLS) 1.0、1.1、1.2 のみをサポートします。  Secure Sockets Layer (SSL) はサポートされません。  Log Analytics へのデータの転送時のセキュリティを保証するため、少なくとも TLS 1.2 を使用するようにゲートウェイを構成してください。 以前のバージョンの TLS または SSL には脆弱性が存在します。 現在、これらは下位互換性を維持するために残されていますが、使用は避けてください。  

詳細については、「[TLS 1.2 を使用して安全にデータを送信する](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12)」を参照してください。 

### <a name="supported-number-of-agent-connections"></a>サポートされるエージェント接続の数

次の表は、ゲートウェイ サーバーと通信可能なエージェントのおおよその数を示しています。 サポートは、6 秒ごとに約 200 KB のデータをアップロードするエージェントに基づいています。 テストされるエージェントごとのデータ量は、1 日あたり約 2.7 GB です。

|Gateway |サポートされるエージェントのおおよその数|  
|--------|----------------------------------|  
|CPU:Intel Xeon プロセッサ E5-2660 v3 \@ 2.6 GHz 2 コア<br> メモリ:4 GB<br> ネットワーク帯域幅:1 Gbps| 600|  
|CPU:Intel Xeon プロセッサ E5-2660 v3 \@ 2.6 GHz 4 コア<br> メモリ:8 GB<br> ネットワーク帯域幅:1 Gbps| 1000|  

## <a name="download-the-log-analytics-gateway"></a>Log Analytics ゲートウェイのダウンロード

最新バージョンの Log Analytics ゲートウェイのセットアップ ファイルを [Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=54443)または Azure portal から入手してください。

Azure portal から Log Analytics ゲートウェイを入手するには、以下の手順を実行します。

1. サービスの一覧を参照し、 **[Log Analytics]** を選択します。 
1. ワークスペースを選択します。
1. ワークスペース ブレードの **[全般]** で **[クイック スタート]** をクリックします。 
1. **[ワークスペースに接続するデータ ソースを選択する]** の **[コンピューター]** を選択します。
1. **[ダイレクト エージェント]** ブレードで、 **[Log Analytics ゲートウェイのダウンロード]** を選択します。
 
   ![Log Analytics ゲートウェイをダウンロードする手順のスクリーンショット](./media/gateway/download-gateway.png)

or 

1. ワークスペース ブレードの **[設定]** で、 **[詳細設定]** を選びます。
1. **[接続されたソース]**  >  **[Windows サーバー]** に移動し、 **[Log Analytics ゲートウェイのダウンロード]** を選択します。

## <a name="install-log-analytics-gateway-using-setup-wizard"></a>セットアップ ウィザードを使用して Log Analytics ゲートウェイをインストールする

セットアップ ウィザードを使用してゲートウェイをインストールするには、以下の手順に従います。 

1. インストール先のフォルダーから、**Log Analytics gateway.msi** をダブルクリックします。
1. **[ようこそ]** ページで **[次へ]** をクリックします。

   ![ゲートウェイのセットアップ ウィザードの [ようこそ] ページのスクリーンショット](./media/gateway/gateway-wizard01.png)

1. **[使用許諾契約書]** ページで、 **[使用許諾契約書の条項に同意する]** を選択してマイクロソフト ソフトウェア ライセンス条項に同意し、 **[次へ]** を選択します。
1. **[Port and proxy address (ポートとプロキシ アドレス)]** ページで、以下を実行します。

   a. ゲートウェイに使用する TCP ポートの番号を入力します。 セットアップでは、このポート番号を使用して Windows ファイアウォールに対して受信ルールが構成されます。  既定値は 8080 です。
      有効なポート番号の範囲は、1 ～ 65535 です。 この範囲の値を入力しないと、エラー メッセージが表示されます。

   b. ゲートウェイがインストールされているサーバーがプロキシを介して通信する必要がある場合は、ゲートウェイが接続する必要のあるプロキシのアドレスを入力します。 たとえば、「 `http://myorgname.corp.contoso.com:80`」のように入力します。  このフィールドを空白のままにすると、ゲートウェイはインターネットに直接接続しようとします。  プロキシ サーバーで認証が必要な場合は、ユーザー名とパスワードを入力します。

   c. **[次へ]** を選択します。

   ![ゲートウェイのプロキシの構成のスクリーンショット](./media/gateway/gateway-wizard02.png)

1. Microsoft Update が有効になっていない場合は、Microsoft Update のページが表示され、有効にするかどうかを選択できます。 選択を行い、 **[次へ]** をクリックします。 使用する場合は、次の手順に進みます。
1. **[インストール先のフォルダー]** ページでは、ゲートウェイのインストール先として、既定のフォルダー C:\Program Files\OMS Gateway をそのまま使用するか、目的の場所を入力します。 次に、 **[次へ]** を選択します。
1. **[インストールの準備完了]** ページで **[インストール]** をクリックします。 [ユーザー アカウント制御] でインストールのためのアクセス許可が要求された場合は、 **[はい]** を選択します。
1. セットアップが完了したら、 **[完了]** を選択します。 サービスが実行中であることを確認するには、services.msc スナップインを開きます。そこでサービスの一覧に **[OMS ゲートウェイ]** が表示されており、状態が **[実行中]** であれば、サービスは正常に実行されています。

   ![OMS ゲートウェイが実行されていることを示す、ローカル サービスのスクリーンショット](./media/gateway/gateway-service.png)

## <a name="install-the-log-analytics-gateway-using-the-command-line"></a>コマンド ラインを使用して Log Analytics ゲートウェイをインストールする
ゲートウェイ用にダウンロードしたファイルは、コマンド ラインまたはその他の自動化された方法によるサイレント インストールをサポートする Windows インストーラー パッケージです。 Windows インストーラーの標準コマンド ライン オプションに慣れていない場合は、「[Command-line options (コマンド ライン オプション)](https://docs.microsoft.com/windows/desktop/Msi/command-line-options)」を参照してください。
 
次の表に、セットアップでサポートされているパラメーターを示します。

|parameters| メモ|
|----------|------| 
|PORTNUMBER | リッスンするゲートウェイの TCP ポート番号 |
|PROXY | プロキシ サーバーの IP アドレス |
|INSTALLDIR | ゲートウェイ ソフトウェア ファイルのインストール ディレクトリを指定する完全修飾パス |
|USERNAME | プロキシ サーバーで認証するユーザー ID |
|PASSWORD | プロキシで認証するユーザー ID のパスワード |
|LicenseAccepted | 使用許諾契約書への同意を確認するには、値 **1** を指定します |
|HASAUTH | USERNAME/PASSWORD パラメーターが指定される場合は、値 **1** を指定します |
|HASPROXY | **PROXY** パラメーターに IP アドレスを指定する場合は、値 **1** を指定します |

サイレント モードでゲートウェイをインストールし、特定のプロキシ アドレスとポート番号で構成するには、次のように入力します。

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 LicenseAccepted=1 
```

/qn コマンド ライン オプションを使用するとセットアップが非表示になり、/qb ではサイレント インストール中にセットアップが表示されます。  

プロキシで認証する資格情報を指定する必要がある場合は、次のように入力します。

```dos
Msiexec.exe /I "oms gateway.msi" /qn PORTNUMBER=8080 PROXY="10.80.2.200" HASPROXY=1 HASAUTH=1 USERNAME="<username>" PASSWORD="<password>" LicenseAccepted=1 
```

インストール後、次の PowerShell コマンドレットを使用して、設定が受け入れられたことを確認できます (ユーザー名とパスワードを除く)。

- **Get-OMSGatewayConfig** – ゲートウェイでリッスンするように構成されている TCP ポートを返します。
- **Get-OMSGatewayRelayProxy** – 通信するように構成したプロキシ サーバーの IP アドレスを返します。

## <a name="configure-network-load-balancing"></a>ネットワーク負荷分散を構成する 
ネットワーク負荷分散 (NLB) を使えば、ゲートウェイに高可用性を構成できます。これには、Microsoft [ネットワーク負荷分散 (NLB)](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing)、[Azure Load Balancer](../../load-balancer/load-balancer-overview.md)、またはハードウェアベースのロード バランサーを使用します。 ロード バランサーは、Log Analytics エージェントまたは Operations Manager 管理サーバーからの接続要求を自らのノードにリダイレクトする形で、トラフィックを管理します。 1 台のゲートウェイ サーバーで障害が発生した場合、トラフィックは他のノードにリダイレクトされます。

### <a name="microsoft-network-load-balancing"></a>Microsoft ネットワーク負荷分散
Windows Server 2016 のネットワーク負荷分散クラスターの設計およびデプロイの方法については、「[ネットワーク負荷分散](https://docs.microsoft.com/windows-server/networking/technologies/network-load-balancing)」を参照してください。 以下の手順では、Microsoft ネットワーク負荷分散クラスターの構成方法を説明します。  

1. 管理者アカウントを使用して、NLB クラスターのメンバーとなっている Windows サーバーにサインオンします。  
2. サーバー マネージャーでネットワーク負荷分散マネージャーを開いて、 **[ツール]** 、 **[ネットワーク負荷分散マネージャー]** の順にクリックします。
3. Microsoft Monitoring Agent がインストールされている Log Analytics ゲートウェイ サーバーを接続するには、 **[ホストをクラスターに追加]** をクリックします。 

    ![ネットワーク負荷分散マネージャー - ホストをクラスターに追加](./media/gateway/nlb02.png)
 
4. 接続するゲートウェイ サーバーの IP アドレスを入力します。 

    ![ネットワーク負荷分散マネージャー -- ホストをクラスターに追加: 接続](./media/gateway/nlb03.png) 

### <a name="azure-load-balancer"></a>Azure Load Balancer
Azure Load Balancer を設計してデプロイする方法については、「[Azure Load Balancer の概要](../../load-balancer/load-balancer-overview.md)」を参照してください。 基本的なロード バランサーをデプロイするには、この[クイック スタート](../../load-balancer/quickstart-create-basic-load-balancer-portal.md)に記載されている手順 (「**バックエンド サーバーを作成する**」の手順は除く) を実行します。   

> [!NOTE]
> **Basic SKU** を使用して Azure Load Balancer を構成するには、可用性セットに属する Azure 仮想マシンが必要です。 可用性セットの詳細については、、「[Azure での Windows 仮想マシンの可用性の管理](../../virtual-machines/windows/manage-availability.md)」を参照してください。 既存の仮想マシンを可用性セットに追加するには、「[Set Azure Resource Manager VM Availability Set (Azure Resource Manager VM の可用性セットを設定する)](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4)」を参照してください。
> 

ロード バランサーが作成されたら、バックエンド プールを作成する必要があります。これにより、トラフィックが 1 つ以上のゲートウェイ サーバーに分散されます。 クイック スタートの記事の「[ロード バランサーのリソースを作成する](../../load-balancer/quickstart-create-basic-load-balancer-portal.md#create-resources-for-the-load-balancer)」の手順を実行してください。  

>[!NOTE]
>正常性プローブを構成する場合は、ゲートウェイ サーバーの TCP ポートを使用するように構成する必要があります。 正常性プローブは、ロード バランサーのローテーションに含めるゲートウェイ サーバーを、正常性チェックへの応答に基づいて動的に追加したり削除したりする働きをします。 
>

## <a name="configure-the-log-analytics-agent-and-operations-manager-management-group"></a>Log Analytics エージェントと Operations Manager 管理グループを構成する
このセクションでは、直接接続されている Log Analytics エージェント、Operations Manager 管理グループ、または Azure Automation Hybrid Runbook Worker に対して Azure Automation または Log Analytics との通信のために Log Analytics ゲートウェイを構成する方法を説明します。  

### <a name="configure-a-standalone-log-analytics-agent"></a>スタンドアロンの Log Analytics エージェントを構成する
Log Analytics エージェントを構成する場合は、プロキシ サーバーの値を Log Analytics ゲートウェイ サーバーの IP アドレスとそのポート番号に置き換えます。 ロード バランサーの背後に複数のゲートウェイ サーバーをデプロイしている場合には、Log Analytics エージェントのプロキシ構成が、ロード バランサーの仮想 IP アドレスとなります。  

>[!NOTE]
>ゲートウェイおよび Log Analytics に直接接続している Windows コンピューターに Log Analytics エージェントをインストールするには、「[Windows コンピューターを Azure の Log Analytics サービスに接続する](agent-windows.md)」を参照してください。 Linux コンピューターを接続するには、「[ハイブリッド環境の Linux コンピューターを対象とした Log Analytics エージェントの構成](../../azure-monitor/learn/quick-collect-linux-computer.md)」を参照してください。 
>

エージェントをゲートウェイ サーバーにインストールした後は、ゲートウェイと通信するワークスペースのエージェントにレポートを送信するように構成します。 Log Analytics Windows エージェントがゲートウェイにインストールされていない場合は、エージェントをインストールする必要があることを示すイベント 300 が OMS ゲートウェイのイベント ログに書き込まれます。 エージェントがインストールされていても、それを経由して通信するエージェントと同じワークスペースにレポートを送信するよう構成されていない場合は、ゲートウェイ上のエージェントはゲートウェイと通信するエージェントと同じワークスペースにレポートを送信するよう構成する必要があることを示すイベント 105 が、同じログに書き込まれます。

構成が完了した後は、変更を適用するために、OMS ゲートウェイ サービスを再起動します。 そうしないと、ゲートウェイは、Log Analytics との通信を試みるエージェントを拒否し、OMS ゲートウェイのイベント ログにイベント 105 をレポートします。 これは、ゲートウェイ サーバーのエージェント構成にワークスペースを追加または削除するときにも発生します。   

Automation Hybrid Runbook Worker に関連する情報については、「[Hybrid Runbook Worker を使用してデータ センターまたはクラウドのリソースを自動化する](../../automation/automation-hybrid-runbook-worker.md)」を参照してください。

### <a name="configure-operations-manager-where-all-agents-use-the-same-proxy-server"></a>Operations Manager を構成する (エージェントがすべて同じプロキシ サーバーを使用する)

Operations Manager に報告するすべてのエージェントに Operations Manager のプロキシ構成が自動的に適用されます (設定が空欄であっても同じです)。  

OMS ゲートウェイを使って Operations Manager をサポートするには、以下の条件を満たす必要があります。

* Microsoft Monitoring Agent (バージョン 8.0.10900.0 以降) が OMS ゲートウェイ サーバーにインストールされていて、管理グループがレポートを送信するよう構成されている同じ Log Analytics ワークスペースに対して構成されていること。
* インターネットに接続されていること。 または、OMS ゲートウェイがインターネットに接続できるプロキシ サーバーに接続されていること。

> [!NOTE]
> ゲートウェイの値を指定しなかった場合には、全部のエージェントに対して空の値がプッシュされます。
>

今回初めて Operations Manager 管理グループを Log Analytics ワークスペースに登録する場合、管理グループのプロキシ構成を指定するオプションはオペレーション コンソールに表示されません。 このオプションは、管理グループがサービスに登録されている場合にのみ使用できます。  

統合を構成するには、オペレーション コンソールを実行しているシステムおよび管理グループ内のすべての管理サーバーで Netsh を使用してシステム プロキシ構成を更新します。 次の手順に従います。

1. 管理者特権でのコマンド プロンプトを開きます。

   a. **[スタート]** を選択し、「**cmd**」と入力します。  

   b. **[コマンド プロンプト]** を右クリックし、 **[管理者として実行]** を選択します。  

1. 次のコマンドを入力します。

   `netsh winhttp set proxy <proxy>:<port>`

Log Analytics との統合を完了した後は、`netsh winhttp reset proxy` を実行して変更を削除します。 次に、オペレーション コンソールの **[プロキシ サーバーの構成]** オプションを使用して Log Analytics ゲートウェイ サーバーを指定します。 

1. Operations Manager コンソールの **[Operations Management Suite]** で、 **[接続]** を選択して、 **[プロキシ サーバーの構成]** を選択します。

   ![選択項目 [プロキシ サーバーの構成] を示す、Operations Manager のスクリーンショット](./media/gateway/scom01.png)

1. **[Operations Management Suite へのアクセスにプロキシ サーバーを使用する]** を選択し、Log Analytics ゲートウェイ サーバーの IP アドレスまたはロード バランサーの仮想 IP アドレスを入力します。 プレフィックス `http://` を省略しないで入力してください。

   ![プロキシ サーバーのアドレスを示す、Operations Manager のスクリーンショット](./media/gateway/scom02.png)

1. **[完了]** を選択します。 Operations Manager 管理グループは、Log Analytics サービスにゲートウェイ サーバー経由で通信するように構成されています。

### <a name="configure-operations-manager-where-specific-agents-use-a-proxy-server"></a>Operations Manager を構成する (特定のエージェントがプロキシ サーバーを使用する)

環境が大規模であるか、複雑な場合には、特定のサーバー (またはグループ) のみ Log Analytics ゲートウェイ サーバーを使用する構成が必要になることも考えられます。  そのようなサーバーでは、Operations Manager エージェントを直接更新する方法は使えません。値が、管理グループ全体に適用されるグローバルな値で上書きされてしまうからです。  そこで、これらの値をプッシュするルールをオーバーライドします。  

> [!NOTE] 
> 環境で複数の Log Analytics ゲートウェイ サーバーを許可する場合は、この構成のテクニックを使用します。  たとえば、リージョンごとに固有の Log Analytics ゲートウェイ サーバーの指定を必要とする場合が考えられます。
>

Log Analytics ゲートウェイ サーバーを使用するように特定のサーバーまたはグループを構成するには、以下の手順を実行します。 

1. Operations Manager コンソールを開き、 **[作成]** ワークスペースを選択します。  
1. [作成] ワークスペースで、 **[ルール]** を選びます。 
1. Operations Manager のツール バーで、 **[スコープ]** ボタンを選択します。 このボタンが利用できない場合には、 **[監視]** ウィンドウでフォルダーではなくオブジェクトを選択していることを確認してください。 **[管理パック オブジェクトのスコープ設定]** ダイアログ ボックスには、ターゲットになることが多いクラス、グループ、またはオブジェクトが一覧表示されます。 
1. **[検索]** フィールドに「**ヘルス サービス**」と入力し、一覧から選択します。 **[OK]** を選択します。  
1. "**Advisor Proxy Setting Rule\(Advisor プロキシ設定ルール\)** " を検索します。 
1. Operations Manager のツール バーで、 **[オーバーライド]** を選択し、 **[Override the Rule\For a specific object of class:Health Service]\(ルールのオーバーライド\クラスの特定のオブジェクト: ヘルス サービス\)** にカーソルを合わせて、一覧からオブジェクトを選択します。  または、このオーバーライドを適用するサーバーのヘルス サービス オブジェクトを含むカスタム グループを作成します。 次に、カスタム グループに対してオーバーライドを適用します。
1. **[オーバーライドのプロパティ]** ダイアログ ボックスで **WebProxyAddress** パラメーターの隣の **[オーバーライド]** 列にチェック マークを付けます。  **[オーバーライド値]** フィールドに、Log Analytics ゲートウェイ サーバーの URL を入力します。 プレフィックス `http://` を省略しないで入力してください。  

    >[!NOTE]
    > このルールを手動で有効にする必要はありません。 このルールは、Microsoft System Center Advisor 監視サーバー グループを対象とした Microsoft System Center Advisor Secure Reference Override 管理パック内のオーバーライドで既に自動で管理されています。
    > 

1. **[目的の管理パックの選択]** の一覧から管理パックを選択するか、 **[新規]** を選択して封印されていない管理パックを新規作成します。 
1. 終了したら、 **[OK]** を選択します。 

### <a name="configure-for-automation-hybrid-runbook-workers"></a>Automation Hybrid Runbook Worker 用に構成する

環境に Automation Hybrid Runbook Worker がある場合は、これらの手順に従って、ワーカーをサポートするようにゲートウェイを構成します。

各地域の URL を確認するには、Azure Automation ドキュメントの[ネットワークの構成](../../automation/automation-hybrid-runbook-worker.md#network-planning)に関するセクションを参照してください。

Update Management ソリューションが 1 つまたは複数の VM で有効化されている場合など、コンピューターが Hybrid Runbook Worker として自動的に登録されている場合は、次のステップに従います。

1. Log Analytics ゲートウェイの許可ホスト一覧に、ジョブ ランタイム データ サービスの URL を追加します。 次に例を示します。`Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. 次の PowerShell コマンドレットを使って、Log Analytics ゲートウェイ サービスを再起動します。`Restart-Service OMSGatewayService`

コンピューターが Hybrid Runbook Worker 登録コマンドレットを使用して Azure Automation に接続されている場合は、以下の手順を実行します。

1. Log Analytics ゲートウェイの許可ホスト一覧に、エージェント サービス登録 URL を追加します。 次に例を示します。`Add-OMSGatewayAllowedHost ncus-agentservice-prod-1.azure-automation.net`
1. Log Analytics ゲートウェイの許可ホスト一覧に、ジョブ ランタイム データ サービスの URL を追加します。 次に例を示します。`Add-OMSGatewayAllowedHost we-jobruntimedata-prod-su1.azure-automation.net`
1. Log Analytics ゲートウェイ サービスを再起動します。
    `Restart-Service OMSGatewayService`

## <a name="useful-powershell-cmdlets"></a>便利な PowerShell コマンドレット

コマンドレットを使用すると、Log Analytics ゲートウェイの構成設定を更新するタスクを実行できます。 コマンドレットを使う前に、必ず次の作業を行ってください。

1. Log Analytics ゲートウェイをインストールします (Microsoft Windows インストーラー)。
1. PowerShell コンソール ウィンドウを開きます。
1. 次のコマンドを入力してモジュールをインポートします。`Import-Module OMSGateway`
1. 前の手順でエラーが発生しなかった場合は、モジュールが正常にインポートされたので、コマンドレットを使うことができます。 「`Get-Module OMSGateway`」と入力します
1. コマンドレットを使って変更を行った後は、OMS ゲートウェイ サービスを再起動します。

手順 3 でエラーが発生した場合は、モジュールはインポートされませんでした。 PowerShell がモジュールを検出できない場合、エラーが発生することがあります。 モジュールは次に示す OMS ゲートウェイのインストール パスにあります。*C:\Program Files\Microsoft OMS Gateway\PowerShell\OmsGateway*。

| **コマンドレット** | **パラメーター** | **説明** | **例** |
| --- | --- | --- | --- |  
| `Get-OMSGatewayConfig` |Key |サービスの構成を取得します |`Get-OMSGatewayConfig` |  
| `Set-OMSGatewayConfig` |キー (必須) <br> 値 |サービスの構成を変更します |`Set-OMSGatewayConfig -Name ListenPort -Value 8080` |  
| `Get-OMSGatewayRelayProxy` | |リレー (アップストリーム) プロキシのアドレスを取得します |`Get-OMSGatewayRelayProxy` |  
| `Set-OMSGatewayRelayProxy` |Address<br> ユーザー名<br> パスワード |リレー (アップストリーム) プロキシのアドレス (および資格情報) を設定します |1.リレー プロキシと資格情報を設定します。<br> `Set-OMSGatewayRelayProxy`<br>`-Address http://www.myproxy.com:8080`<br>`-Username user1 -Password 123` <br><br> 2.認証を必要としないリレー プロキシを設定します。`Set-OMSGatewayRelayProxy`<br> `-Address http://www.myproxy.com:8080` <br><br> 3.リレー プロキシ設定をクリアします。<br> `Set-OMSGatewayRelayProxy` <br> `-Address ""` |  
| `Get-OMSGatewayAllowedHost` | |現在許可されているホストを取得します (ローカルに構成されている許可ホストだけであり、自動的にダウンロードされた許可ホストは含みません) |`Get-OMSGatewayAllowedHost` | 
| `Add-OMSGatewayAllowedHost` |ホスト (必須) |許可リストにホストを追加します |`Add-OMSGatewayAllowedHost -Host www.test.com` |  
| `Remove-OMSGatewayAllowedHost` |ホスト (必須) |許可リストからホストを削除します |`Remove-OMSGatewayAllowedHost`<br> `-Host www.test.com` |  
| `Add-OMSGatewayAllowedClientCertificate` |サブジェクト (必須) |クライアント証明書のサブジェクトを許可リストに追加します |`Add-OMSGatewayAllowed`<br>`ClientCertificate` <br> `-Subject mycert` |  
| `Remove-OMSGatewayAllowedClientCertificate` |サブジェクト (必須) |クライアント証明書のサブジェクトを許可リストから削除します |`Remove-OMSGatewayAllowed` <br> `ClientCertificate` <br> `-Subject mycert` |  
| `Get-OMSGatewayAllowedClientCertificate` | |現在許可されているクライアント証明書のサブジェクトを取得します (ローカルに構成されている許可サブジェクトだけであり、自動的にダウンロードされた許可サブジェクトは含みません) |`Get-`<br>`OMSGatewayAllowed`<br>`ClientCertificate` |  

## <a name="troubleshooting"></a>トラブルシューティング

ゲートウェイによってログに記録されたイベントを収集するには、Log Analytics エージェントをインストールしておく必要があります。

![Log Analytics ゲートウェイ ログのイベント ビューアーの一覧のスクリーンショット](./media/gateway/event-viewer.png)

### <a name="log-analytics-gateway-event-ids-and-descriptions"></a>Log Analytics ゲートウェイのイベント ID と説明

次の表では、Log Analytics ゲートウェイ ログ イベントのイベント ID と説明を示します。

| **ID** | **説明** |
| --- | --- |
| 400 |特定の ID がないすべてのアプリケーション エラー。 |
| 401 |正しくない構成。 例: listenPort が整数ではなくテキスト。 |
| 402 |TLS ハンドシェイク メッセージ解析中の例外。 |
| 403 |ネットワーク エラー。 例: ターゲット サーバーに接続できません。 |
| 100 |一般情報。 |
| 101 |サービスが開始しました。 |
| 102 |サービスが停止しました。 |
| 103 |クライアントから HTTP CONNECT コマンドを受信しました。 |
| 104 |HTTP CONNECT コマンドではありません。 |
| 105 |接続先サーバーが許可リストにないか、または接続先ポートがセキュリティで保護されたポート (443) ではありません。 <br> <br> OMS ゲートウェイ サーバー上の MMA エージェントと、OMS ゲートウェイと通信しているエージェントが、同じ Log Analytics ワークスペースに接続されていることを確認します。 |
| 105 |エラー TcpConnection – 無効なクライアント証明書: CN=Gateway。 <br><br> バージョン 1.0.395.0 以降の OMS ゲートウェイを使用していることを確認します。 また、OMS ゲートウェイ サーバー上の MMA エージェントと、OMS ゲートウェイと通信しているエージェントが、同じ Log Analytics ワークスペースに接続されていることを確認します。 |
| 106 |サポート対象外の TLS/SSL プロトコルのバージョンです。<br><br> Log Analytics ゲートウェイは、TLS 1.0、1.1、1.2 のみをサポートします。 SSL はサポートされません。|
| 107 |TLS セッションが確認されました。 |

### <a name="performance-counters-to-collect"></a>収集されるパフォーマンス カウンター

次の表では、Log Analytics ゲートウェイに使えるパフォーマンス カウンターを示します。 パフォーマンス モニターを使ってカウンターを追加します。

| **Name** | **説明** |
| --- | --- |
| Log Analytics ゲートウェイ/アクティブなクライアント接続 |アクティブなクライアント ネットワーク (TCP) 接続の数 |
| Log Analytics ゲートウェイ/エラーの数 |エラーの数 |
| Log Analytics ゲートウェイ/接続されたクライアント |接続されているクライアントの数 |
| Log Analytics ゲートウェイ/拒否の回数 |TLS 検証エラーによる拒否の回数 |

![パフォーマンス カウンターを示す、Log Analytics ゲートウェイのインターフェイスのスクリーンショット](./media/gateway/counters.png)

## <a name="assistance"></a>サポート

Azure portal にサインインすると、Log Analytics ゲートウェイまたは他の Azure サービスや機能についてのサポートを受けることができます。
サポートを受けるには、ポータルの右上隅にある疑問符アイコンを選択して、 **[新しいサポート要求]** を選択します。 その後、新しいサポート要求フォームを作成します。

![新しいサポート要求のスクリーンショット](./media/gateway/support.png)

## <a name="next-steps"></a>次の手順

[データ ソースを追加](../../azure-monitor/platform/agent-data-sources.md)して、接続されたソースからデータを収集して Log Analytics ワークスペースにデータを格納します。
