---
title: オンプレミスのデータ ゲートウェイのインストール
description: Azure Logic Apps からオンプレミスのデータにアクセスするには、オンプレミス データ ゲートウェイをダウンロードしてインストールします。
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 12/05/2019
ms.openlocfilehash: 4fbfb31feb2183e3175a96023cbb3b08c4d18027
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893685"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Azure Logic Apps 用のオンプレミス データ ゲートウェイのインストール

[Azure Logic Apps からオンプレミスのデータ ソースに接続する](../logic-apps/logic-apps-gateway-connection.md)には、ローカル コンピューターに[オンプレミス データ ゲートウェイ](https://aka.ms/on-premises-data-gateway-installer)をダウンロードしてインストールします。 このゲートウェイは、オンプレミスのデータ ソースとロジック アプリ間でのデータ転送と暗号化を高速で行うブリッジとして機能します。 同じゲートウェイ インストールを、Power BI、Power Automate、Power Apps、Azure Analysis Services など、他のクラウド サービスで使用できます。 これらのサービスでゲートウェイを使用する方法については、次の記事を参照してください。

* [Microsoft Power Automate オンプレミス データ ゲートウェイ](/power-automate/gateway-reference)
* [Microsoft Power BI オンプレミス データ ゲートウェイ](/power-bi/service-gateway-onprem)
* [Microsoft Power Apps オンプレミス データ ゲートウェイ](/powerapps/maker/canvas-apps/gateway-reference)
* [Azure Analysis Services オンプレミス データ ゲートウェイ](../analysis-services/analysis-services-gateway.md)

この記事では、Azure Logic Apps からオンプレミスのデータ ソースにアクセスできるように、オンプレミス データ ゲートウェイをダウンロード、インストール、設定する方法について説明します。 [データ ゲートウェイのしくみ](#gateway-cloud-service)についても、このトピックで後ほど詳しく説明します。 ゲートウェイの詳細については、「[オンプレミス データ ゲートウェイとは](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem)」を参照してください。

<a name="requirements"></a>

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 サブスクリプションを保持する Azure アカウントがない場合は、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

  * お使いの Azure アカウントは、1 つの [Azure Active Directory (Azure AD) テナントまたはディレクトリ](../active-directory/fundamentals/active-directory-whatis.md#terminology)に属している必要があります。 ローカル コンピューター上にゲートウェイをインストールして管理するには、同じ Azure アカウントを使用する必要があります。

  * ゲートウェイのインストール中に、Azure アカウントを使用してサインインします。これにより、ゲートウェイのインストールが Azure アカウントに関連付けられ、そのアカウントのみに限定されます。 その後、Azure portal で、ゲートウェイのインストールを登録して要求する Azure ゲートウェイ リソースを作成するとき、同じ Azure アカウントと Azure AD テナントを使用する必要があります。 Azure Logic Apps では、オンプレミスのトリガーとアクションにおいて、オンプレミスのデータ ソースへの接続にゲートウェイ リソースが使用されます。

    > [!NOTE]
    > 1 つのゲートウェイ インストールと 1 つの Azure ゲートウェイ リソースだけを互いに関連付けることができます。 同じゲートウェイのインストールを複数の Azure アカウントまたは Azure ゲートウェイ リソースに関連付けることはできません。 ただし、1 つの Azure アカウントを、複数のゲートウェイのインストールと Azure ゲートウェイ リソースに関連付けることは可能です。 オンプレミスのトリガーまたはアクションでは、さまざまな Azure サブスクリプションから選んで、関連付けられる 1 つのゲートウェイ リソースを選択できます。

  * `username@contoso.com` のような、職場アカウントまたは学校アカウントのどちらか (*組織*アカウントとも呼ばれる) を使用して、サインインする必要があります。 Azure B2B (ゲスト) アカウントや個人の Microsoft アカウント (@hotmail.com や @outlook.com など) は使用できません。

    > [!TIP]
    > Office 365 オファリングにサインアップして、仕事用メール アドレスを指定しなかった場合、アドレスは `username@domain.onmicrosoft.com` のようになります。 アカウントは Azure Active Directory (Azure AD) のテナント内に格納されます。 ほとんどの場合、Azure AD アカウントのユーザープリンシパル名 (UPN) は、メール アドレスと同じです。
    >
    > Microsoft アカウントに関連付けられている [Visual Studio Standard サブスクリプション](https://visualstudio.microsoft.com/vs/pricing/)を使用するには、まず [Azure AD でテナントを作成する](../active-directory/develop/quickstart-create-new-tenant.md)か、または既定のディレクトリを使用します。 ディレクトリにパスワードを持つユーザーを追加した後、そのユーザーに Azure サブスクリプションへのアクセス権を付与します。 その後、ゲートウェイのインストール中に、このユーザー名とパスワードでサインインできます。

* ローカル コンピューターの要件を以下に示します。

  **最小要件**

  * .NET Framework 4.7.2
  * Windows 7 または Windows Server 2008 R2 (以降) の 64 ビット バージョン

  **推奨要件**

  * 8 コア CPU
  * 8 GB メモリ
  * Windows Server 2012 R2 以降 の 64 ビット バージョン
  * スプール用のソリッドステート ドライブ (SSD) ストレージ

  > [!NOTE]
  > このゲートウェイでは、Windows Server Core はサポートされていません。

* **関連する考慮事項**

  * オンプレミス データ ゲートウェイはローカル コンピューターにのみインストールします。ドメイン コントローラーにはインストールしません。 データ ソースと同じコンピューターにゲートウェイをインストールする必要はありません。 すべてのデータ ソースに関して必要なゲートウェイは 1 つだけです。そのため、データ ソースごとにゲートウェイをインストールする必要はありません。

    > [!TIP]
    > 待機時間を最小限に抑えるために、アクセス許可があることを前提として、データ ソースにできるだけ近いコンピューターまたは同じコンピューターにゲートウェイをインストールできます。

  * ワイヤード (有線) ネットワーク上にある、インターネットに接続され、常に電源が入っており、スリープ状態に移行しないコンピューターにゲートウェイをインストールします。 そうしないと、ゲートウェイを実行できず、ワイヤレス ネットワーク経由ではパフォーマンスが低下する可能性もあります。

  * Windows 認証を使用する予定の場合は、必ず、ご使用のデータ ソースと同じ Active Directory 環境のメンバーであるコンピューターにゲートウェイをインストールしてください。

  * ゲートウェイ インストール用に選択するリージョンは、後でロジック アプリ用の Azure ゲートウェイ リソースを作成するときに選択する必要がある場所と同じです。 既定では、このリージョンは、Azure アカウントを管理する Azure AD テナントと同じ場所です。 ただし、この場所はゲートウェイのインストール中に変更できます。

  * ゲートウェイのインストールを最新バージョンに更新している場合は、最初に現在のゲートウェイをアンインストールすると手順がすっきりします。

  * ゲートウェイには、標準モードと個人用モード (Power BI にのみ適用) の 2 つのモードがあります。 同じコンピューターにおいて同じモードで複数のゲートウェイを実行することはできません。

  * Azure Logic Apps では、ゲートウェイを介した読み取りおよび書き込み操作がサポートされます。 ただし、これらの操作には、[ペイロードのサイズに制限](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations)があります。

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>データ ゲートウェイをインストールする

1. [ゲートウェイ インストーラーをローカル コンピューターにダウンロードして実行します](https://aka.ms/on-premises-data-gateway-installer)。

1. 最小要件を確認し、既定のインストール パスをそのまま使用して、使用条件に同意してから、 **[インストール]** を選択します。

   ![要件の確認と使用条件への同意](./media/logic-apps-gateway-install/review-and-accept-terms-of-use.png)

1. ゲートウェイが正常にインストールされた後、Azure アカウント用のメール アドレスを指定し、 **[サインイン]** を選択します。たとえば、次のようになります。

   ![職場または学校アカウントでのサインイン](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   ゲートウェイのインストールは、1 つの Azure アカウントのみに関連付けできます。

1. **[このコンピューターに新しいゲートウェイを登録します]**  >  **[次へ]** の順に選択します。 この手順では、[ゲートウェイ クラウド サービス](#gateway-cloud-service)を使用して、ゲートウェイのインストールを登録します。

   ![ローカル コンピューターにゲートウェイを登録する](./media/logic-apps-gateway-install/register-gateway-local-computer.png)

1. ゲートウェイ インストールについて以下の情報を入力します。

   * Azure AD テナント全体で一意のゲートウェイ名
   * 使用する回復キー。これは 8 文字以上である必要があります
   * 回復キーの確認

   ![ゲートウェイ インストールに関する情報を入力する](./media/logic-apps-gateway-install/gateway-name-recovery-key.png)

   > [!IMPORTANT]
   > 回復キーは、安全な場所に保存して管理してください。 このキーは、ゲートウェイ インストールの場所変更、移行、復旧、または引き継ぎを行う場合に必要になります。

   **[既存のゲートウェイ クラスターに追加します]** オプションに注意してください。これは、[高可用性のシナリオ](#high-availability)向けに追加のゲートウェイをインストールするときに選択します。

1. ゲートウェイ インストールで使用されるゲートウェイ クラウド サービスと [Azure Service Bus](https://azure.microsoft.com/services/service-bus/) のリージョンを確認します。 既定では、このリージョンは、ご使用の Azure アカウントの Azure AD テナントと同じ場所です。

   ![ゲートウェイ サービスとサービス バスのリージョンを確認する](./media/logic-apps-gateway-install/confirm-gateway-region.png)

1. 既定のリージョンをそのまま使用するには、 **[構成]** を選択します。 ただし、既定のリージョンが最も近いものでなければ、リージョンを変更できます。

   "*ゲートウェイ インストールのリージョンを変更する理由*"

   たとえば、待ち時間を減らすために、ゲートウェイのリージョンをロジック アプリと同じリージョンに変更する場合があります。 または、オンプレミス データ ソースに最も近いリージョンを選択する場合があります。 "*Azure のゲートウェイ リソース*" とロジック アプリの場所は、違っていてもかまいません。

   1. 現在のリージョンの横にある **[リージョンの変更]** を選択します。

      ![現在のゲートウェイ リージョンを変更する](./media/logic-apps-gateway-install/change-gateway-service-region.png)

   1. 次のページで、 **[リージョンの選択]** リストを開いて目的のリージョンを選択し、 **[完了]** を選択します。

      ![ゲートウェイ サービスに別のリージョンを選択する](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. 最後の確認ウィンドウの情報を確認します。 この例では、Logic Apps、Power BI、Power Apps、Power Automate に同じアカウントを使用するため、これらのすべてのサービスでこのゲートウェイを使用できます。 準備ができたら、 **[閉じる]** を選択します。

   ![データ ゲートウェイ情報を確認する](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. 次に、[ゲートウェイ インストール用の Azure リソースを作成します](../logic-apps/logic-apps-gateway-connection.md)。

## <a name="check-or-adjust-communication-settings"></a>通信の設定を確認または調整する

オンプレミス データ ゲートウェイでは、[Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) に依存してクラウドへの接続が行われ、ゲートウェイに関連付けられている Azure リージョンへの対応する送信接続が確立されます。 お使いの作業環境で、インターネットにアクセスするためにそのトラフィックがプロキシまたはファイアウォールを経由する必要がある場合は、この制限によって、オンプレミス データ ゲートウェイがゲートウェイ クラウド サービスおよび Azure Service Bus に接続できない場合があります。 ゲートウェイにはいくつかの通信設定があり、これを調整できます。 詳細については、以下のトピックを参照してください。

* [オンプレミス データ ゲートウェイの通信設定を調整する](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)
* [オンプレミス データ ゲートウェイのプロキシ設定を構成する](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)

<a name="high-availability"></a>

## <a name="high-availability-support"></a>高可用性のサポート

オンプレミスのデータ アクセスが単一障害点にならないようにするには、複数のゲートウェイ インストール (標準モードのみ) をそれぞれ別のコンピューターにインストールし、クラスターまたはグループとしてそれらを設定します。 このようにして、プライマリ ゲートウェイが使用できない場合、データ要求は 2 番目のゲートウェイにルーティングされます (それ以降も同様)。 1 台のコンピューターにインストールできる標準ゲートウェイは 1 つだけであるため、クラスター内の追加のゲートウェイをそれぞれ別のコンピューターにインストールする必要があります。 オンプレミス データ ゲートウェイと連携するすべてのコネクタにおいて高可用性がサポートされます。

* 少なくとも 1 つのゲートウェイ インストールが既に、プライマリ ゲートウェイとそのインストールに対する回復キーと同じ Azure アカウントに用意されている必要があります。

* プライマリ ゲートウェイでは、2017 年 11 月以降のゲートウェイ更新プログラムが実行されている必要があります。

プライマリ ゲートウェイを設定したら、別のゲートウェイのインストールに進むときに、 **[既存のゲートウェイ クラスターに追加します]** を選択して、インストールした最初のゲートウェイであるプライマリ ゲートウェイを選択し、そのゲートウェイの回復キーを指定します。 詳細については、[オンプレミス データ ゲートウェイのための高可用性クラスター](https://docs.microsoft.com/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster)に関するページを参照してください。

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>場所の変更、または既存のゲートウェイの移行、復元、引き継ぎ

ゲートウェイの場所の変更が必要な場合、または、新しいコンピューターへのゲートウェイ インストールの移行、破損したゲートウェイの復元、既存のゲートウェイの所有権の取得を行う必要がある場合、ゲートウェイのインストール中に提供された回復キーが必要です。

1. 既存のゲートウェイがあるコンピューターでゲートウェイ インストーラーを実行します。 最新のゲートウェイ インストーラーがない場合は、 [最新のゲートウェイ バージョンをダウンロードします](https://aka.ms/on-premises-data-gateway-installer)。

   > [!NOTE]
   > 元のゲートウェイ インストールがあるコンピューターでゲートウェイを復元する前に、まずそのコンピューターのゲートウェイをアンインストールする必要があります。 このアクションで、元のゲートウェイが切断されます。
   > 任意のクラウド サービスのゲートウェイ クラスターを削除した場合、そのクラスターを復元することはできません。

1. インストーラーが開いたら、ゲートウェイのインストールに使用したものと同じ Azure アカウントでサインインします。

1. **[既存のゲートウェイを移行、復元、または置き換えます。]**  >  **[次へ]** の順に選択します。例:

   ![[既存のゲートウェイを移行、復元、または置き換えます。] の選択](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. 使用可能なクラスターとゲートウェイから選択し、選択したゲートウェイの回復キーを入力します。例:

   ![ゲートウェイを選択し、回復キーを指定する](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. リージョンを変更するには、 **[リージョンの変更]** を選択し、新しいリージョンを選択します。

1. 準備ができたら、タスクを完了できるように **[構成]** を選択します。

## <a name="tenant-level-administration"></a>テナント レベルの管理

Azure AD テナント内のすべてのオンプレミス データ ゲートウェイを可視化するには、そのテナントの全体管理者は、テナント管理者として [Power Platform 管理センター](https://powerplatform.microsoft.com)にサインインし、 **[データ ゲートウェイ]** オプションを選択します。 詳細については、「[オンプレミス データ ゲートウェイのテナント レベルの管理](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin)」を参照してください。

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>ゲートウェイの再起動

既定では、ローカル コンピューター上のゲートウェイ インストールは、"オンプレミス データ ゲートウェイ サービス" という名前の Windows サービス アカウントとして実行されます。 ただし、ゲートウェイのインストールでは、その "ログオン" アカウントの資格情報に `NT SERVICE\PBIEgwService` という名前が使用され、"サービスとしてログオン" のアクセス許可が付与されています。

> [!NOTE]
> Windows サービス アカウントは、オンプレミスのデータ ソースへの接続に使用するアカウントとも、クラウド サービスへのサインイン時に使用する Azure アカウントとも異なります。

他の Windows サービスと同じように、さまざまな方法でゲートウェイを開始および停止できます。 詳細については、「[オンプレミス データ ゲートウェイを再起動する](https://docs.microsoft.com/data-integration/gateway/service-gateway-restart)」をご覧ください。

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>ゲートウェイのしくみ

組織内のユーザーは、既にアクセスを認可されているオンプレミスのデータにアクセスできます。 ただし、これらのユーザーがオンプレミスのデータ ソースに接続できるためには、その前にオンプレミス データ ゲートウェイをインストールして設定する必要があります。 通常、管理者がゲートウェイをインストールして設定します。 これらの作業には、サーバー管理者のアクセス許可、またはオンプレミス サーバーに関する特別な知識が必要になる場合があります。

ゲートウェイにより、バックグラウンド通信の迅速で安全な通信が容易になります。 この通信は、クラウド内のユーザー、ゲートウェイ クラウド サービス、オンプレミス データ ソースの間を流れます。 ゲートウェイ クラウド サービスでは、データ ソースの資格情報とゲートウェイの詳細を暗号化して格納します。 サービスでは、ユーザー、ゲートウェイ、オンプレミス データ ソースの間でのクエリとその結果のルーティングも行われます。

ゲートウェイはファイアウォールと共に動作し、ゲートウェイでは送信接続のみが使用されます。 すべてのトラフィックは、ゲートウェイ エージェントからの安全な送信トラフィックとして生成されます。 ゲートウェイでは、オンプレミス ソースからのデータが、[Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) 経由の暗号化されたチャネルで中継されます。 このサービス バスによって、ゲートウェイと呼び出しサービスとの間のチャネルが作成されます。ただし、データは格納されません。 ゲートウェイを経由するすべてのデータは暗号化されます。

![オンプレミス データ ゲートウェイのアーキテクチャ](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

> [!NOTE]
> クラウド サービスによっては、ゲートウェイ用のデータ ソースの設定が必要になる場合があります。

以下の手順では、オンプレミス データ ソースに接続された要素をユーザーが操作するときに行われることについて説明します。

1. クラウド サービスで、クエリと共に、データ ソースに対する暗号化された資格情報が作成されます。 その後、サービスにより、クエリと資格情報が処理のためにゲートウェイ キューに送信されます。

1. ゲートウェイ クラウド サービスで、クエリが分析されて、Azure Service Bus に要求がプッシュされます。

1. Azure Service Bus により、保留中の要求がゲートウェイに送信されます。

1. ゲートウェイにより、クエリが取得されて、資格情報が復号化され、その資格情報を使用して 1 つ以上のデータ ソースへの接続が行われます。

1. ゲートウェイにより、実行するためにクエリがデータ ソースに送信されます。

1. 結果がデータ ソースからゲートウェイに返送され、その後ゲートウェイ クラウド サービスに送信されます。 ゲートウェイ クラウド サービスが結果を使用します。

### <a name="authentication-to-on-premises-data-sources"></a>オンプレミス データ ソースに対して認証を行う

ゲートウェイからオンプレミス データ ソースに接続するには、格納されている資格情報が使用されます。 ユーザーに関係なく、ゲートウェイでは格納されている資格情報を使用して接続が行われます。 Power BI での Analysis Services に対する DirectQuery や LiveConnect など、特定のサービスで認証の例外が発生する可能性があります。

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

Microsoft クラウド サービスでは、[Azure AD](../active-directory/fundamentals/active-directory-whatis.md) を使用して、ユーザーの認証が行われます。 Azure AD テナントには、ユーザー名とセキュリティ グループが含まれています。 通常、サインインに使用するメール アドレスは、アカウントのユーザー プリンシパル名 (UPN) と同じです。

### <a name="what-is-my-upn"></a>自分の UPN を確認する

ドメイン管理者でないユーザーは、UPN がわからない可能性があります。 自分のアカウントの UPN を調べるには、ワークステーションから `whoami /upn` コマンドを実行します。 結果は、メール アドレスに似ていますが、ローカル ドメイン アカウントに対する UPN です。

### <a name="synchronize-an-on-premises-active-directory-with-azure-ad"></a>オンプレミスの Active Directory と Azure AD を同期する

オンプレミスの Active Directory アカウントの UPN と Azure AD アカウントの UPN は、同じである必要があります。 そのため、オンプレミスの各 Active Directory アカウントと Azure AD アカウントを一致させる必要があります。 クラウド サービスでは、Azure AD 内のアカウントのみが認識されます。 そのため、オンプレミスの Active Directory にアカウントを追加する必要はありません。 アカウントが Azure AD に存在しない場合、そのアカウントを使用することはできません。

オンプレミスの Active Directory アカウントと Azure AD を一致させる方法を次に示します。

* Azure AD に手動でアカウントを追加します。

  Azure portal または Microsoft 365 管理センターでアカウントを作成します。 アカウント名が、オンプレミスの Active Directory アカウントの UPN と一致していることを確認します。

* Azure Active Directory Connect ツールを使用して、ローカル アカウントを Azure AD テナントに同期します。

  Azure AD Connect ツールには、ディレクトリ同期と認証のセットアップのためのオプションが用意されています。 これらのオプションには、パスワード ハッシュ同期、パススルー認証、およびフェデレーションが含まれます。 テナント管理者またはローカル ドメイン管理者でない場合は、IT 管理者に連絡して Azure AD Connect のセットアップを依頼してください。 Azure AD Connect により、Azure AD UPN がローカルの Active Directory UPN と一致することが保証されます。 この一致方法は、Power BI またはシングル サインオン (SSO) 機能で Analysis Services ライブ接続を使用している場合に役立ちます。

  > [!NOTE]
  > Azure AD Connect ツールを使用してアカウントを同期すると、Azure AD テナントに新しいアカウントが作成されます。

<a name="faq"></a>

## <a name="faq-and-troubleshooting"></a>FAQ とトラブルシューティング

詳細については、以下のトピックを参照してください。

* [オンプレミス データ ゲートウェイに関する FAQ](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)
* [オンプレミス データ ゲートウェイのトラブルシューティング](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [ゲートウェイのパフォーマンスの監視と最適化](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)

## <a name="next-steps"></a>次の手順

* [ロジック アプリからオンプレミスのデータに接続する](../logic-apps/logic-apps-gateway-connection.md)
* [エンタープライズ統合機能](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Azure Logic Apps のコネクタ](../connectors/apis-list.md)
