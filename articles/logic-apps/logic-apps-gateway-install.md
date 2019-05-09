---
title: オンプレミス データ ゲートウェイのインストール - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps からオンプレミスのデータにアクセスするには、オンプレミス データ ゲートウェイをダウンロードしてインストールします。
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: 67a918b227ad3b33a2f63b17f86b94f36fbc9fa3
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59679127"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Azure Logic Apps 用のオンプレミス データ ゲートウェイのインストール

Azure Logic Apps からオンプレミスのデータ ソースに接続するには、ローカル コンピューターにオンプレミス データ ゲートウェイをダウンロードしてインストールします。 ゲートウェイは、(クラウドではなく) オンプレミスのデータ ソースとロジック アプリとの間でデータ転送と暗号化を高速で行うブリッジとして機能します。 この記事では、オンプレミス データ ゲートウェイのダウンロード、インストール、設定を行う方法について説明します。 

Power BI、Microsoft Flow、PowerApps、Azure Analysis Services など、他のサービスと同じゲートウェイ インストールを使用できます。 詳細については、[データ ゲートウェイのしくみ](#gateway-cloud-service)に関するセクションを参照してください。

<a name="supported-connections"></a>

ゲートウェイでは、以下のデータ ソースに関する Azure Logic Apps の[オンプレミス コネクタ](../connectors/apis-list.md#on-premises-connectors)がサポートされます。

*   BizTalk Server 2016
*   ファイル システム
*   IBM DB2  
*   IBM Informix
*   IBM MQ
*   MySQL
*   Oracle Database
*   PostgreSQL
*   SAP アプリケーション サーバー 
*   SAP メッセージ サーバー
*   SharePoint Server
*   SQL Server
*   Teradata

他のサービスでゲートウェイを使用する方法については、次の記事を参照してください。

* [Microsoft Power BI オンプレミス データ ゲートウェイ](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft PowerApps オンプレミス データ ゲートウェイ](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Microsoft Flow オンプレミス データ ゲートウェイ](https://flow.microsoft.com/documentation/gateway-manage/)
* [Azure Analysis Services オンプレミス データ ゲートウェイ](../analysis-services/analysis-services-gateway.md)

<a name="requirements"></a>

## <a name="prerequisites"></a>前提条件

* [Azure サブスクリプション](https://docs.microsoft.com/azure/architecture/cloud-adoption-guide/adoption-intro/subscription-explainer)がある[職場または学校アカウント](../active-directory/fundamentals/sign-up-organization.md)。 

  ゲートウェイ インストールを Azure サブスクリプションに関連付けられるよう、ゲートウェイのインストール中にこのアカウントにサインインします。 
  後で、Azure portal でゲートウェイ インストールの Azure リソースを作成する際にも、同じアカウントを使用します。 
  Azure サブスクリプションがない場合は、<a href="https://azure.microsoft.com/free/" target="_blank">無料の Azure アカウントにサインアップ</a>してください。

* ローカル コンピューターの要件を以下に示します。

  **最小要件**

  * .NET Framework 4.5.2
  * Windows 7 または Windows Server 2008 R2 (以降) の 64 ビット バージョン

  **推奨要件**

  * 8 コア CPU
  * 8 GB メモリ
  * Windows Server 2012 R2 (以降) の 64 ビット バージョン

* **重要な考慮事項**

  * オンプレミス データ ゲートウェイはローカル コンピューターにのみインストールできます。ドメイン コントローラーにはインストールできません。 ただし、データ ソースと同じコンピューターにゲートウェイをインストールする必要はありません。 さらに、すべてのデータ ソースに関して必要なゲートウェイは 1 つだけです。そのため、データ ソースごとにゲートウェイをインストールする必要はありません。

    > [!TIP]
    > 待機時間を最小限に抑えるために、アクセス許可があることを前提として、データ ソースにできるだけ近いコンピューターまたは同じコンピューターにゲートウェイをインストールできます。

  * インターネットに接続され、常に電源が入っており、スリープ状態に移行*しない*コンピューターにゲートウェイをインストールします。 それ以外の場合は、ゲートウェイを実行できません。 
  また、ワイヤレス ネットワークではパフォーマンスが低下する可能性があります。

  * インストール時には、@hotmail.com や @outlook.com などの Azure B2B (ゲスト) アカウントや個人用の Microsoft アカウントではなく、たとえば @contoso.onmicrosoft.com など、Azure Active Directory (Azure AD) によって管理されている[職場または学校アカウント](../active-directory/sign-up-organization.md)でのみサインインできます。 
  ゲートウェイ リソースを作成して Azure portal でゲートウェイ インストールを登録するときには、同じサインイン アカウントを使用するようにします。 
  ロジック アプリからオンプレミス データ ソースへの接続を作成するときに、このゲートウェイ リソースを選択できます。 
  [Azure AD の職場または学校アカウントを使用する必要があるのはなぜですか?](#why-azure-work-school-account)

  > [!TIP]
  > Office 365 プランにサインアップしたが、実際の職場のメール アドレスを指定しなかった場合、サインイン アドレスはこの例 `username@domain.onmicrosoft.com` のようになる可能性があります。 
  >
  > [Visual Studio Standard サブスクリプション](https://visualstudio.microsoft.com/vs/pricing/)がある Microsoft アカウントを使用するには、最初に Microsoft アカウントで [Azure Active Directory にディレクトリ (テナント) を作成する](../active-directory/develop/quickstart-create-new-tenant.md)か、または既定のディレクトリを使用します。 
  > ディレクトリにパスワードを持つユーザーを追加した後、そのユーザーにサブスクリプションへのアクセス権を与えます。 
  > その後、ゲートウェイのインストール中に、このユーザー名とパスワードでサインインできます。

  * ゲートウェイ インストールに関して選択するリージョンによって、後で Azure リソースを作成して Azure でゲートウェイを登録する場所が決まります。 
  Azure でこのゲートウェイ リソースを作成するとき、ゲートウェイ インストールと "*同じ*" 場所を選択する必要があります。 既定のリージョンは、お使いの Azure アカウントが管理される Azure AD テナントと同じ場所です。ただし、この場所はゲートウェイのインストール中に変更できます。

  * バージョンが 14.16.6317.4 より前のインストーラーで設定を行ったゲートウェイが既にある場合は、最新のインストーラーを実行してもゲートウェイの場所を変更することはできません。 ただし、最新のインストーラーを使用して、目的の場所に新しいゲートウェイを設定できます。
  
    バージョンが 14.16.6317.4 より前のゲートウェイ インストーラーはあるものの、ゲートウェイをまだインストールしていない場合は、代わりに最新のインストーラーをダウンロードして使用できます。

## <a name="high-availability-support"></a>高可用性のサポート

オンプレミス データ ゲートウェイでは、2 つ以上のゲートウェイ インストールがあってそれらをクラスターとして設定した場合に、高可用性がサポートされます。 別のゲートウェイを作成しようとしたときに既存のゲートウェイがあると、必要に応じて高可用性クラスターを作成できます。 これらのクラスターでは、ゲートウェイが、単一障害点の回避に役立つグループへと整理されます。 また、オンプレミス データ ゲートウェイのコネクタはすべて、高可用性をサポートするようになりました。

オンプレミス データ ゲートウェイを使用する場合は、以下の要件と考慮事項を確認してください。

* 少なくとも 1 つのゲートウェイ インストールが既に、プライマリ ゲートウェイとそのインストールの回復キーと同じ Azure サブスクリプション内にある必要があります。 

* プライマリ ゲートウェイでは、2017 年 11 月以降のゲートウェイ更新プログラムが実行されている必要があります。

これらの要件を満たした後、次のゲートウェイを作成する際に、**[既存のゲートウェイ クラスターに追加します]** を選択し、クラスターのプライマリ ゲートウェイを選択してから、そのプライマリ ゲートウェイの回復キーを入力します。
詳細については、[オンプレミス データ ゲートウェイのための高可用性クラスター](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters)に関するページを参照してください。

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>データ ゲートウェイをインストールする

1. [ゲートウェイ インストーラーをローカル コンピューターにダウンロードして保存し、実行します](https://aka.ms/on-premises-data-gateway-installer)。

2. 既定のインストール パスをそのまま使用します。または、ゲートウェイをインストールしたいコンピューター上の場所を指定します。

3. 使用条件とプライバシーに関する声明を確認して同意します。次に、**[インストール]** を選択します。

   ![使用条件とプライバシーに関する声明への同意](./media/logic-apps-gateway-install/accept-terms.png)

4. ゲートウェイが正常にインストールされた後、職場または学校アカウントにメール アドレスを入力し、**[サインイン]** を選択します。

   ![職場または学校アカウントでのサインイン](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

5. **[このコンピューターに新しいゲートウェイを登録します。]** > **[次へ]** の順に選択します。これにより、ゲートウェイ インストールが[ゲートウェイ クラウド サービス](#gateway-cloud-service)に登録されます。 

   ![ゲートウェイを登録](./media/logic-apps-gateway-install/register-new-gateway.png)

6. ゲートウェイ インストールについて以下の情報を入力します。

   * インストールに付ける名前 

   * 作成する回復キー。これは 8 文字以上である必要があります

     > [!IMPORTANT]
     > 回復キーは、安全な場所に保存して管理してください。 ゲートウェイの場所を変更する場合、または既存のゲートウェイの移行、復元、引き継ぎを行う場合にこのキーが必要になります。

   * 回復キーの確認 

     ![ゲートウェイの設定](./media/logic-apps-gateway-install/set-up-gateway.png)

7. ゲートウェイ インストールで使用されるゲートウェイ クラウド サービスと Azure Service Bus に関して選択されているリージョンをチェックします。 

   ![リージョンのチェック](./media/logic-apps-gateway-install/check-region.png)

   > [!IMPORTANT]
   > ゲートウェイのインストールを完了した後にこのリージョンを変更するには、このゲートウェイ インストールの回復キーが必要です。 さらに、ゲートウェイをアンインストールして再インストールする必要があります。 詳細については、「[場所の変更、または既存のゲートウェイの移行、復元、引き継ぎ](#update-gateway-installation)」を参照してください。

   "*ゲートウェイ インストールのリージョンを変更する理由*" 

   たとえば、待ち時間を減らすために、ゲートウェイのリージョンをロジック アプリと同じリージョンに変更する場合があります。 
   または、オンプレミス データ ソースに最も近いリージョンを選択する場合があります。 
   "*Azure のゲートウェイ リソース*" とロジック アプリの場所は、違っていてもかまいません。

8. 既定のリージョンをそのまま使用するには、**[構成]** を選択します。 または、既定のリージョンを変更するには、以下の手順に従います。

   1. 現在のリージョンの横にある **[リージョンの変更]** を選択します。 

      ![リージョンの変更](./media/logic-apps-gateway-install/change-region.png)

   2. 次のページで、**[リージョンの選択]** リストを開いて目的のリージョンを選択し、**[完了]** を選択します。

      ![別のリージョンの選択](./media/logic-apps-gateway-install/select-region-gateway-install.png)

9. 確認ページが表示されたら、**[閉じる]** を選択します。 

   ゲートウェイがオンラインになって使用できるようになったことが、インストーラーによって確認されます。

   ![完了したゲートウェイ](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

10. 次に、[ゲートウェイ インストールの Azure リソースを作成して](../logic-apps/logic-apps-gateway-connection.md)、Azure でゲートウェイを登録します。 

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>場所の変更、または既存のゲートウェイの移行、復元、引き継ぎ

ゲートウェイの場所の変更が必要な場合、または、新しいコンピューターへのゲートウェイ インストールの移行、破損したゲートウェイの復元、既存のゲートウェイの所有権の取得を行う必要がある場合、ゲートウェイのインストール中に提供された回復キーが必要です。 このアクションでは、古いゲートウェイが切断されます。

1. コンピューターの**コントロール パネル**から、**[プログラムと機能]** に移動します。 プログラムの一覧で **[オンプレミス データ ゲートウェイ]** を選択してから、**[アンインストール]** を選択します。

2. [オンプレミス データ ゲートウェイを再インストールします](https://aka.ms/on-premises-data-gateway-installer)。

3. インストーラーが開いたら、ゲートウェイのインストールに使用したのと同じ職場または学校アカウントでサインインします。

4. **[既存のゲートウェイを移行、復元、または置き換えます。]** を選択してから、**[次へ]** を選択します。

   ![[既存のゲートウェイを移行、復元、または置き換えます。] の選択](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

5. **[利用可能なゲートウェイ]** または **[使用可能なゲートウェイ クラスター]** で、変更したいゲートウェイ インストールを選択します。 ゲートウェイ インストールの回復キーを入力します。 

   ![プライマリ ゲートウェイの選択](./media/logic-apps-gateway-install/select-existing-gateway.png)

6. リージョンを変更するには、**[リージョンの変更]**、新しいリージョンの順に選択します。

7. 操作が完了したら、**[構成]** を選択します。

## <a name="configure-proxy-or-firewall"></a>プロキシまたはファイアウォールの構成

オンプレミス データ ゲートウェイによって、[Azure Service Bus](https://azure.microsoft.com/services/service-bus/) への送信接続が作成されます。 お使いの作業環境で、インターネットにアクセスするためにそのトラフィックがプロキシを経由する必要がある場合は、この制限によって、データ ゲートウェイがゲートウェイ クラウド サービスに接続できない場合があります。 ネットワークでプロキシが使用されているかどうかを確認するには、superuser.com の次の記事を参照してください。 

[How do I know what proxy server I'm using? (使用中のプロキシ サーバーを確認する方法) (SuperUser.com)](https://superuser.com/questions/346372/how-do-i-know-what-proxy-server-im-using) 

ゲートウェイのプロキシ情報を指定するには、[プロキシ設定の構成](https://docs.microsoft.com/power-bi/service-gateway-proxy)に関するページを参照してください。 プロキシまたはファイアウォールが接続をブロックする可能性があるかどうかを確認するには、マシンがインターネットと [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) に実際に接続できるかどうかを確認します。 PowerShell プロンプトから、次のコマンドを実行します。

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

> [!NOTE]
> このコマンドは、ネットワーク接続と、Azure Service Bus への接続のみをテストします。 ゲートウェイ、または資格情報とゲートウェイの詳細を暗号化して格納するゲートウェイ クラウド サービスに対しては、何も行われません。 
>
> また、このコマンドは、Windows Server 2012 R2 以降と Windows 8.1 以降でのみ使用できます。 それより前のバージョンの OS では、Telnet を使用して接続をテストすることができます。 詳細については、[Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) に関するページを参照してください。

結果は次の例のようになります。この例では、**TcpTestSucceeded** が **True** に設定されています。

```text
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

**TcpTestSucceeded** が **True** に設定されていない場合、ゲートウェイがファイアウォールによってブロックされている可能性があります。 包括的にテストしたい場合は、**ComputerName** と **Port** の値を、この記事の「[ポートの構成](#configure-ports)」の一覧に記載されている値に置き換えます。

Azure Service Bus から Azure データ センターへの接続も、ファイアウォールによってブロックされる可能性があります。 その場合は、リージョン内にあるそれらのデータ センターの IP アドレスをすべて承認 (ブロック解除) してください。 これらの IP アドレスについては、[こちら](https://www.microsoft.com/download/details.aspx?id=41653)で Azure の IP アドレスの一覧を取得してください。

## <a name="configure-ports"></a>ポートの構成

ゲートウェイは [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) への送信接続を作成し、TCP 443 (既定)、5671、5672、9350 ～ 9354 の送信ポート上で通信します。 ゲートウェイでは受信ポートは必要ありません。 詳細については、[Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) に関するページを参照してください。

ゲートウェイでは、以下の完全修飾ドメイン名が使用されます。

| ドメイン名 | 送信ポート | 説明 | 
| ------------ | -------------- | ----------- | 
| *. analysis.windows.net | 443 | HTTPS | 
| *.core.windows.net | 443 | HTTPS | 
| *. frontend.clouddatahub.net | 443 | HTTPS | 
| *.login.windows.net | 443 | HTTPS | 
| *.microsoftonline-p.com | 443 | 構成によっては認証に使用されます。 | 
| *. msftncsi.com | 443 | Power BI サービスによってゲートウェイにアクセスできない場合、インターネット接続性のテストに使用されます。 | 
| *.servicebus.windows.net | 443、9350 ～ 9354 | TCP 経由での Service Bus Relay のリスナー (Access Control トークンの取得には 443 が必要) | 
| *.servicebus.windows.net | 5671 ～ 5672 | Advanced Message Queuing Protocol (AMQP) | 
| login.microsoftonline.com | 443 | HTTPS | 
||||

Azure Service Bus 接続は、完全修飾ドメイン名ではなく IP アドレスを使用して行われる場合があります。 そのため、データ リージョンの IP アドレスをファイアウォールのホワイトリストに登録することが必要な場合があります。 ドメインではなく IP アドレスをホワイトリストに登録するには、[Microsoft Azure データセンター IP 範囲リスト](https://www.microsoft.com/download/details.aspx?id=41653)をダウンロードして使用できます。 このリストの IP アドレスは、[Classless Inter-Domain Routing (CIDR)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 表記で記述されています。

### <a name="force-https-communication-with-azure-service-bus"></a>Azure Service Bus との HTTPS 通信の強制

一部のプロキシでは、ポート 80 および 443 へのトラフィックのみ、通過が許されます。 既定では、Azure Service Bus との通信は 443 以外のポートで行われます。
ダイレクト TCPではなく HTTPS 経由で Azure Service Bus と通信するようゲートウェイに強制できます。ただし、これを行うと、パフォーマンスが大幅に低下するおそれがあります。 このタスクを実行するには、次の手順に従います。

1. オンプレミス データ ゲートウェイ クライアントの場所を参照します。これは通常、```C:\Program Files\On-premises data gateway\Microsoft.PowerBI.EnterpriseGateway.exe``` にあります。

   そうでない場合にクライアントの場所を探すには、同じコンピューターのサービス コンソールを開き、**オンプレミス データ ゲートウェイ サービス**を探して、**実行可能ファイルへのパス** プロパティを表示します。

2. 次の*構成*ファイルを開きます。**Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

3. **ServiceBusSystemConnectivityModeString** の値を **AutoDetect** から **Https** に変更します。

   ```html
   <setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
      <value>Https</value>
   </setting>
   ```

<a name="windows-service-account"></a>

## <a name="windows-service-account"></a>Windows サービス アカウント

オンプレミス データ ゲートウェイをインストールするコンピューター上で、ゲートウェイは "オンプレミス データ ゲートウェイ サービス" という名前の Windows サービス アカウントとして実行されます。 ただし、ゲートウェイは、その "ログオン方法" アカウントの資格情報に "NT SERVICE\PBIEgwService" の名前を使用します。 既定では、ゲートウェイは、そのゲートウェイをインストールするコンピューター上に "サービスとしてログオン" アクセス許可を持っています。 ゲートウェイの Windows サービス アカウントは通常、オンプレミスのデータ ソースに接続するために使用するアカウントや、クラウド サービスにサインインするために使用する職場または学校アカウントとは異なります。

Azure Portal でゲートウェイを作成および保守するには、この Windows サービス アカウントに少なくとも**共同作成者**のアクセス許可が必要です。 これらのアクセス許可を確認するには、「[RBAC と Azure portal を使用してアクセスを管理する](../role-based-access-control/role-assignments-portal.md)」を参照してください。 

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>ゲートウェイの再起動

データ ゲートウェイは Windows サービスとして実行されるため、他の Windows サービスと同様に、開始と停止をさまざまな方法で行えます。 たとえば、ゲートウェイが実行されているコンピューターで管理者特権を使用してコマンド プロンプトを開き、いずれかのコマンドを実行できます。

* サービスを停止するには、次のコマンドを実行します。
  
  `net stop PBIEgwService`

* サービスを開始するには、次のコマンドを実行します。
  
  `net start PBIEgwService`

## <a name="tenant-level-administration"></a>テナント レベルの管理 

現在、他のユーザーがインストールおよび構成したすべてのゲートウェイをテナント管理者が管理できる 1 つの場所はありません。 テナント管理者の場合、インストールするゲートウェイごとにテナント管理者を管理者として追加するように、組織内のユーザーに依頼してください。 これにより、[ゲートウェイ設定] ページまたは [PowerShell コマンド](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters#powershell-support-for-gateway-clusters)を使用して組織内のすべてのゲートウェイを管理できます。 

<a name="gateway-cloud-service"></a>

## <a name="how-does-the-gateway-work"></a>ゲートウェイの動作

データ ゲートウェイによって、ロジック アプリ、ゲートウェイ クラウド サービス、オンプレミスのデータ ソース間に迅速かつセキュリティで保護された通信が促進されます。 ゲートウェイ クラウド サービスでは、データ ソースの資格情報とゲートウェイの詳細を暗号化して格納します。 サービスは、ロジック アプリ、オンプレミス データ ゲートウェイ、オンプレミスのデータ ソース間のクエリとその結果もルーティングします。 

ゲートウェイはファイアウォールと共に動作し、ゲートウェイでは送信接続のみが使用されます。 すべてのトラフィックは、ゲートウェイ エージェントからの安全な送信トラフィックとして生成されます。 ゲートウェイは、オンプレミスのソースから、Azure Service Bus 経由の暗号化されたチャネルでデータを転送します。 このサービス バスによって、ゲートウェイと呼び出しサービスとの間のチャネルが作成されます。ただし、データは格納されません。 ゲートウェイを経由するすべてのデータは暗号化されます。

![diagram-for-on-premises-data-gateway-flow](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

以下の手順では、オンプレミス データ ソースに接続された要素をクラウドのユーザーが操作した場合に何が起こるかについて説明します。

1. ゲートウェイ クラウド サービスが、データ ソース用の暗号化された資格情報と併せてクエリを作成し、そのクエリを処理するためのゲートウェイのキューに送信します。

2. ゲートウェイ クラウド サービスはクエリを分析し、Azure Service Bus に要求をプッシュします。

3. オンプレミス データ ゲートウェイは、保留中の要求がないか Azure Service Bus をポーリングします。

4. ゲートウェイはクエリを取得して資格情報を復号化し、その資格情報でデータ ソースに接続します。

5. ゲートウェイは、実行するためにクエリをデータ ソースに送信します。

6. 結果がデータ ソースからゲートウェイに返送され、その後ゲートウェイ クラウド サービスに送信されます。 ゲートウェイ クラウド サービスが結果を使用します。

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="general"></a>全般

**Q**: クラウド内のデータ ソース (Azure SQL Database など) にゲートウェイは必要ですか? <br/>
**A**: いいえ。ゲートウェイは、オンプレミスのデータ ソースにのみ接続します。

**Q**: ゲートウェイをデータ ソースと同じコンピューターにインストールする必要がありますか? <br/>
**A**: いいえ。ゲートウェイは、指定された接続情報を使用してデータ ソースに接続します。 その意味ではゲートウェイをクライアント アプリケーションと見なすことができます。 ゲートウェイに必要なのは、指定されたサーバー名に接続する機能だけです。

<a name="why-azure-work-school-account"></a>

**Q**: 職場または学校アカウントを使用してサインインする必要があるのはなぜですか? <br/>
**A**: オンプレミス データ ゲートウェイをインストールする場合は、職場または学校アカウントしか使用できません。 サインイン アカウントは、Azure Active Directory (Azure AD) によって管理されるテナントに格納されます。 通常、Azure AD アカウントのユーザー プリンシパル名 (UPN) は電子メール アドレスと一致します。

**Q**: 自分の資格情報はどこに格納されますか? <br/>
**A**: データ ソースのために入力した資格情報は暗号化され、ゲートウェイ クラウド サービスに格納されます。 資格情報の復号化はオンプレミス データ ゲートウェイで行われます。

**Q**: ネットワーク帯域幅に何か要件はありますか? <br/>
**A**: ネットワーク接続のスループットが良好なことを確認してください。 すべての環境は異なっており、送信されるデータの量が結果に影響を及ぼす可能性があります。 オンプレミス データ ソースと Azure データセンターの間のスループット レベルを確保するには、[Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) をお試しください。 スループットを測定するには、Azure Speed Test などの外部ツールをお試しください。

**Q**: ゲートウェイからデータ ソースにクエリを実行するための待ち時間とは何ですか? 最適なアーキテクチャとは何ですか? <br/>
**A**: ネットワーク待ち時間を削減するには、ゲートウェイをデータ ソースのできるだけ近くにインストールします。 実際のデータ ソース上にゲートウェイをインストールできれば、その近接性によって待ち時間の発生を最小限に抑えることができます。 さらに、Azure データセンターとの近接性を考慮します。 たとえば、お客様のサービスで米国西部のデータセンターを使用しており、SQL Server が Azure VM でホストされている場合は、その Azure VM も米国西部リージョンに配置されているのがよいでしょう。 こうすることで待ち時間は最短となり、Azure VM でデータ送信料金が発生することもありません。

**Q**: 結果はクラウドにどのように送り返されますか? <br/>
**A**: 結果は Azure Service Bus 経由で送信されます。

**Q**: クラウドからゲートウェイへの受信接続はありますか? <br/>
**A**: いいえ。ゲートウェイは、Azure Service Bus への送信接続を使用します。

**Q**: 送信接続をブロックするとどうなりますか? 開くために何をする必要がありますか? <br/>
**A**: ゲートウェイが使用するポートとホストを確認してください。

**Q**: 実際の Windows サービスは何と呼ばれていますか? <br/>
**A**: タスク マネージャーの [サービス] タブでは、サービス名は "PBIEgwService" (つまり Power BI Enterprise Gateway Service) です。 サービス コンソールでは、サービス名は "オンプレミス データ ゲートウェイ サービス" です。 Windows サービスでは、サービス SID (SSID) として "NT SERVICE\PBIEgwService" が使用されます。

**Q**: ゲートウェイ Windows サービスは、Azure Active Directory アカウントを使用して実行できますか? <br/>
**A**: いいえ。Windows サービスには有効な Windows アカウントが必要です。

### <a name="disaster-recovery"></a>障害復旧

**Q**: ディザスター リカバリーにはどのようなオプションを使用できますか? <br/>
**A**: 回復キーを使用してゲートウェイを復元または移動できます。 ゲートウェイをインストールするときに、回復キーを指定します。

**Q**: 回復キーの利点は何ですか? <br/>
**A**: 回復キーは、障害発生後にゲートウェイの設定を移行または復旧する方法を提供します。

## <a name="troubleshooting"></a>トラブルシューティング

このセクションでは、オンプレミス データ ゲートウェイの設定時および使用時に発生する可能性がある一般的な問題をいくつか取り上げます。

**Q**: ゲートウェイのインストールが失敗するのはなぜですか? <br/>
**A**: この問題は、ターゲット コンピューター上のウイルス対策ソフトウェアが古くなった場合に発生することがあります。 ウイルス対策ソフトウェアを更新できます。または、ゲートウェイのインストール中にのみウイルス対策ソフトウェアを無効にして、ソフトウェアを再び有効にできます。

**Q**: Azure でゲートウェイ リソースを作成するときにゲートウェイ インストールが表示されないはなぜですか? <br/>
**A**: この問題は、次の理由で発生することがあります。

* ゲートウェイ インストールが登録済みで、Azure の別のゲートウェイ リソースによって既に要求されている。 ゲートウェイ インストールは、そのゲートウェイ リソースが作成された後、インスタンスの一覧に表示されません。
Azure portal でゲートウェイの登録を確認するには、"*すべて*" の Azure サブスクリプションについて、種類が**オンプレミス データ ゲートウェイ**であるすべての Azure リソースを確認します。 

* ゲートウェイをインストールしたユーザーの Azure AD ID が、Azure portal にサインインしたユーザーのものと異なる。 ゲートウェイをインストールしたのと同じ ID でサインインしていることを確認してください。

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

**Q**: ゲートウェイのログはどこにありますか? <br/>
**A**: この記事の後の方にある「[**ログ**」セクション](#logs)を参照してください。

**Q**: オンプレミスのデータ ソースに送信されるクエリを確認するにはどうすればよいですか? <br/>
**A**: 送信されるクエリを含むクエリ トレースを有効にすることができます。 クエリ トレースは、トラブルシューティングが完了したら忘れずに元の値に戻してください。 クエリ トレースをオンのままにしておくと、ログのサイズが増大します。

クエリをトレースするためにデータ ソースに用意されているツールを使用することもできます。 たとえば、SQL Server 用の拡張イベントまたは SQL Profiler や Analysis Services を使用できます。

### <a name="outdated-gateway-version"></a>期限切れのゲートウェイ バージョン

ゲートウェイのバージョンが古くなるにつれ、数多くの問題が表面化する可能性があります。 一般的には、常に最新バージョンを使用することが最適な対処法となります。 1 か月以上ゲートウェイを更新していない場合は、最新バージョンのゲートウェイをインストールし、問題が再発するかどうかを確認してください。

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>エラー:ユーザーをグループに追加できませんでした。 (-2147463168 PBIEgwService Performance Log Users)

ドメイン コントローラー (サポート対象外) にゲートウェイをインストールしようとすると、このエラーが表示されることがあります。 ゲートウェイは、必ずドメイン コントローラー以外のマシンにデプロイしてください。

<a name="logs"></a>

### <a name="logs"></a>ログ

トラブルシューティングを行いやすいよう、いつでも最初にゲートウェイ ログを収集して確認します。 ログを収集する方法はいくつかありますが、ゲートウェイのインストール後で最も簡単なのは、ゲートウェイ インストーラーのユーザー インターフェイスを使用する方法です。 

1. コンピューターで、オンプレミス データ ゲートウェイ インストーラーを開きます。
2. 左側のメニューで、**[診断]** を選択します。
3. **[ゲートウェイ ログ]** で **[ログのエクスポート]** を選択します。

   ![ゲートウェイ インストーラーでのログのエクスポート](./media/logic-apps-gateway-install/export-logs.png)

各種のログがある他の場所は以下のとおりです。

| ログのタイプ | Location | 
|----------|----------| 
| **インストーラー ログ** | %localappdata%\Temp\On-premises_data_gateway_<*yyyymmdd*>.<*数値*>.log | 
| **構成ログ** | C:\Users\<*ユーザー名*>\AppData\Local\Microsoft\On-premises data gateway\GatewayConfigurator<*yyyymmdd*>.<*数値*>.log | 
| **エンタープライズ ゲートウェイ サービスのログ** | C:\Users\PBIEgwService\AppData\Local\Microsoft\On-premises data gateway\Gateway<*yyyymmdd*>.<*数値*>.log | 
||| 

**イベント ログ**

ゲートウェイのイベント ログを見つけるには、以下の手順に従います。

1. ゲートウェイがインストールされたコンピューターで、**イベント ビューアー**を開きます。 
2. **[イベント ビューアー (ローカル)]** > **[アプリケーションとサービス ログ]** の順に展開します。 
3. **[On-premises data gateway service]\(オンプレミス データ ゲートウェイ サービス\)** を選択します。

   ![ゲートウェイのイベント ログの表示](./media/logic-apps-gateway-install/event-viewer.png)

### <a name="review-slow-query-performance"></a>低速なクエリ パフォーマンスの確認

クエリがゲートウェイを通過する速度が遅いことがわかった場合、クエリとその実行時間が出力される追加のログを有効にできます。 これらのログは、低速なクエリまたは実行時間の長いクエリがどれであるかを特定するのに役立ちます。 クエリ パフォーマンスを調整するには、たとえば SQL Server クエリのインデックスの調整など、データ ソースの変更が必要な場合があります。

クエリの実行時間を確認するには、以下の手順に従います。

1. ゲートウェイ クライアントと同じ場所を参照します。これは通常、```C:\Program Files\On-premises data gateway``` にあります。

   そうでない場合にクライアントの場所を探すには、同じコンピューターのサービス コンソールを開き、**オンプレミス データ ゲートウェイ サービス**を探して、**実行可能ファイルへのパス** プロパティを表示します。

2. 下記のとおり、以下の構成ファイルを開いて編集します。

   * **Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config**

     このファイルで、**EmitQueryTraces** の値を **false** から **true** に変更します。これで、ゲートウェイからデータ ソースに送信されたクエリが、ゲートウェイによって記録されるようになります。

     ```html
     <setting name="EmitQueryTraces" serializeAs="String">
        <value>true</value>
     </setting>
     ```

     > [!IMPORTANT]
     > EmitQueryTraces 設定を有効にすると、ゲートウェイの使用状況によってはログのサイズが非常に大きくなる可能性があります。 ログの確認が完了したら、必ず EmitQueryTraces を **false** に設定し直して、この設定が長時間有効なままでないようにします。

   * **Microsoft.PowerBI.DataMovement.Pipeline.Diagnostics.dll.config**

     実行時間を示すエントリなど、ゲートウェイ ログの詳細エントリを取得するには、いずれかの手順を実行して、**TracingVerbosity** の値を **4** から **5** に変更します。 

     * この構成ファイルで、**TracingVerbosity** の値を **4** から **5** に変更します。 

       ```html
       <setting name="TracingVerbosity" serializeAs="String">
          <value>5</value>
       </setting>
       ```

     * ゲートウェイ インストーラーを開いて **[診断]** を選択し、**[追加ログ]** を有効にしてから、**[適用]** を選択します。

       ![追加のログの有効化](./media/logic-apps-gateway-install/turn-on-additional-logging.png)

     > [!IMPORTANT]
     > TracingVerbosity 設定を有効にすると、ゲートウェイの使用状況によってはログのサイズが非常に大きくなる可能性があります。 ログの確認が完了したら、必ずゲートウェイ インストーラーで **[追加ログ]** を無効にするか、構成ファイルで TracingVerbosity を **4** に設定し直すかして、この設定が長時間有効なままでないようにします。

3. クエリの実行時間を確認するには、以下の手順に従います。

   1. ゲートウェイ ログを[エクスポート](#logs)して開きます。

   2. クエリを見つけるには、アクティビティの種類を検索します。例: 

      | アクティビティの種類 | 説明 | 
      |---------------|-------------| 
      | MGEQ | ADO.NET で実行されるクエリ。 | 
      | MGEO | OLEDB で実行されるクエリ。 | 
      | MGEM | マッシュアップ エンジンから実行されるクエリ。 | 
      ||| 

   3. 2 つ目の GUID をメモします。これは要求 ID です。

   4. 実行時間をミリ秒で示す "FireActivityCompletedSuccessfullyEvent" という名前のエントリが見つかるまで、アクティビティの種類を探し続けます。 
   エントリに同じ要求 ID が含まれていることを確認します。例:

      ```text 
      DM.EnterpriseGateway Verbose: 0 : 2016-09-26T23:08:56.7940067Z DM.EnterpriseGateway    baf40f21-2eb4-4af1-9c59-0950ef11ec4a    5f99f566-106d-c8ac-c864-c0808c41a606    MGEQ    21f96cc4-7496-bfdd-748c-b4915cb4b70c    B8DFCF12 [DM.Pipeline.Common.TracingTelemetryService] Event: FireActivityCompletedSuccessfullyEvent (duration=5004)
      ```

      > [!NOTE] 
      > "FireActivityCompletedSuccessfullyEvent" エントリは詳細エントリであり、"TracingVerbosity" がレベル 5 に設定されない限り記録されません。

### <a name="trace-traffic-with-fiddler"></a>Fiddler によるトラフィックのトレース

[Fiddler](https://www.telerik.com/fiddler) は、HTTP トラフィックを監視する Telerik の無料ツールです。 Power BI サービスに関するこのトラフィックをクライアント マシンで確認できます。 このサービスを利用することで、エラーやその他の関連情報が明らかになる場合があります。

## <a name="next-steps"></a>次の手順
    
* [ロジック アプリからオンプレミスのデータに接続する](../logic-apps/logic-apps-gateway-connection.md)
* [エンタープライズ統合機能](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Azure Logic Apps のコネクタ](../connectors/apis-list.md)
