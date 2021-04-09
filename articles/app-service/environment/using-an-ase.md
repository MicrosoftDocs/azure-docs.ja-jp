---
title: App Service Environment の使用および管理
description: App Service Environment でアプリを作成、発行、スケーリングする方法について説明します。 この記事では、すべての一般的なタスクを紹介します。
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 9/22/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: a7fa9ece3728214fad31f0bae769e1e50206df7e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594047"
---
# <a name="use-an-app-service-environment"></a>App Service 環境の使用

App Service Environment (ASE) は、ユーザーの Azure Virtual Network インスタンス内のサブネットに Azure App Service をデプロイしたものです。 ASE は次の要素で構成されます。

- **フロントエンド:** HTTP または HTTPS が App Service Environment で終了する場所です
- **ワーカー**:アプリのホストとなるリソースです
- **データベース**:環境を定義する情報が格納されます
- **ストレージ**: 顧客によって発行されたアプリのホストとして使用されます

アプリ アクセスに対して外部または内部仮想 IP (VIP) を使用して ASE をデプロイできます。 外部 VIP を使用したデプロイは、*外部 ASE* と呼ばれます。 内部 VIP を使用したデプロイは、内部ロード バランサー (ILB) を使用するので、*ILB ASE* と呼ばれます。 ILB ASE の詳細については、「[App Service Environment で内部ロード バランサーを作成して使用する][MakeILBASE]」を参照してください。

## <a name="create-an-app-in-an-ase"></a>ASE 内にアプリを作成する

ASE でアプリを作成する方法は通常のアプリの作成方法とほとんど同じですが、いくつか違いがあります。 新しい App Service プランを作成する場合は、次の点が異なります。

- アプリのデプロイ先の場所として地理的な場所ではなく ASE を選択します。
- ASE で作成する App Service プランはすべて、Isolated の価格レベルでのみ使用できます。

ASE をご利用でない場合は、「[外部 App Service Environment の作成][MakeExternalASE]」の手順に従って作成できます。

ASE 内にアプリを作成するには:

1. **[リソースの作成]**  >  **[Web + モバイル]**  >  **[Web アプリ]** を選択します。

1. アプリの名前を入力します。 ASE 内で既に App Service プランが選択されている場合、アプリのドメイン名に、ASE のドメイン名が反映されます。

    ![アプリ名の選択][1]

1. サブスクリプションを選択します。

1. 新しいリソース グループの名前を指定するか、 **[既存のものを使用]** を選択して、ボックスの一覧からいずれかを選択します。

1. OS を選択します。

1. ASE で既存の App Service プランを選択するか、次の手順で新しく作成します。

    a. Azure portal の左側のメニューで、 **[リソースの作成] > [Web アプリ]** を選択します。

    b. サブスクリプションを選択します。

    c. リソース グループを選択または作成します。

    d. Web アプリの名前を入力します。

    e. **[コード]** または **[Docker コンテナー]** を選択します。

    f. ランタイム スタックを選択します。

    g. **Linux** か **Windows** を選択します。 

    h. **[リージョン]** ドロップダウン リストから該当する ASE を選択します。 

    i. 新しい App Service プランを選択または作成します。 新しい App Service プランを作成する場合は、適切な **Isolated** SKU サイズを選択します。

    ![[分離] 価格レベル][2]

    > [!NOTE]
    > Linux アプリと Windows アプリを同じ App Service プランに追加することはできませんが、同じ App Service Environment に追加することはできます。
    >

1. **[確認および作成]** を選択し、情報が正しいことを確認して、 **[作成]** を選択します。

## <a name="how-scale-works"></a>スケールのしくみ

すべての App Service アプリは、App Service プランで実行されます。 App Service Environment に App Service プランが存在し、App Service プランにアプリが存在します。 アプリをスケールするときは、App Service プラン、および同じプラン内のすべてのアプリをスケールすることになります。

App Service プランをスケールすると、必要なインフラストラクチャが自動的に追加されます。 インフラストラクチャが追加されるまでの間、スケール操作に時間差が生じます。 複数のスケール操作を順番に実行すると、最初のインフラストラクチャ スケール要求が処理され、他のものはキューに入れられます。 最初のスケール操作が終了すると、他のインフラストラクチャ要求はすべて一緒に動作します。 インフラストラクチャを追加すると、App Service プランが適切に割り当てられます。 新しい App Service プランを作成すること自体が、追加のハードウェアが必要となるため、スケール操作になります。

マルチテナントの App Service では、スケーリングが即座に行われます。それへの対応としてすぐに利用できるリソースのプールが存在するためです。 ASE にはそのようなバッファーが存在せず、リソースは必要に応じて割り当てられます。

ASE では、App Service プランを最大 100 インスタンスまでスケールアップできます。 ASE には、その ASE 内のすべての App Service プランにわたって合計で最大 201 のインスタンスを設定できます。

## <a name="ip-addresses"></a>IP アドレス

App Service では、アプリに専用の IP アドレスを割り当てることができます。 「[Azure App Service の TLS/SSL 証明書を購入して構成する][ConfigureSSL]」で説明されているように、この機能は IP ベースの SSL を構成した後に利用できます。 ILB ASE では、IP ベースの SSL に使用する IP アドレスをさらに追加することはできません。

外部 ASE では、マルチテナントの App Service の場合と同じ方法で、アプリに IP ベースの SSL を構成できます。 ASE には、IP アドレスが 30 個を上限として、常に予備のアドレスが 1 つ存在します。 すぐに使用できるアドレスを常時確保するために、アドレスを 1 つ使用すると、そのたびに、別のアドレスが追加されます。 別の IP アドレスを割り当てるには、時間の遅延が必要です。 この遅延により、IP アドレスが続けざまに追加されることを回避できます。

## <a name="front-end-scaling"></a>フロント エンドのスケーリング

App Service プランをスケールアウトすると、それをサポートする worker が自動的に追加されます。 各 ASE は 2 つのフロント エンドで作成されます。 フロント エンドは、15 の App Service プラン インスタンスのセットごとに 1 つのフロント エンドの割合で、自動的にスケールアウトされます。 たとえば、それぞれ 5 つのインスタンスを持つ App Service プランが 3 つある場合、合計 15 のインスタンスと 3 つのフロント エンドになります。 合計 30 インスタンスまでスケーリングすると、4 つのフロント エンドになります。 スケールアウトのたびにこのパターンが繰り返されます。

既定で割り当てられるフロント エンドの数は、中程度の負荷に適しています。 最小で 5 インスタンスにつき 1 フロント エンドの割合になるよう比率を下げることができますが、 フロント エンドのサイズを変更することもできます。 既定では、単一コアです。 Azure portal では、代わりに 2 つまたは 4 つのコアにサイズを変更できます。

この比率やフロント エンドのサイズの変更には料金がかかります。 詳細については、「[App Service の価格][Pricing]」を参照してください。 ASE の負荷容量を改善する場合は、まず 2 つのコア フロント エンドにスケーリングしてからスケール率を調整することで、さらに改善できます。 フロント エンドのコア サイズの変更は、ASE のアップグレードが発生するため、通常の業務時間外に実行する必要があります。

フロント エンド リソースは、ASE の HTTP/HTTPS エンドポイントです。 既定のフロント エンド構成では、各フロント エンドのメモリ使用量が常時 60% 程度となります。 フロントエンドをスケーリングする主な理由は CPU 使用率で、これは主に HTTPS トラフィックによって決まります。

## <a name="app-access"></a>アプリのアクセス

外部 ASE では、アプリの作成に使用されるドメイン サフィックスは *.&lt;asename&gt;.p.azurewebsites.net* です。 実際の ASE の名前が _external-ase_ で、その ASE でホストするアプリの名前が _contoso_ である場合、それには次の URL でアクセスすることになります。

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

外部 ASE の作成方法については、[App Service Environment の作成][MakeExternalASE]に関する記事を参照してください。

ILB ASE では、アプリの作成に使用されるドメイン サフィックスは *.&lt;asename&gt;.appserviceenvironment.net* です。 実際の ASE の名前が _ilb-ase_ で、その ASE でホストするアプリの名前が _contoso_ である場合、それには次の URL でアクセスすることになります。

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

ILB ASE の作成方法については、[ILB ASE の作成と使用][MakeILBASE]に関する記事を参照してください。

SCM URL は、Kudu コンソールにアクセスしたり、Web デプロイを使用してアプリを発行したりするために使用されます。 Kudu コンソールの詳細については、「[Azure App Service の Kudu コンソール][Kudu]」を参照してください。 Kudu コンソールを使用すると、デバッグやファイルのアップロード、ファイルの編集など、さまざまな作業を Web UI で行うことができます。

### <a name="dns-configuration"></a>DNS の構成 

外部 ASE を使用する場合、ASE で作成されたアプリは Azure DNS で登録されます。 外部 ASE には、アプリを公開するための追加の手順はその後ありません。 ILB ASE を使う場合は、独自の DNS を管理する必要があります。 これは、独自の DNS サーバーで、または Azure DNS プライベート ゾーンを使用して行うことができます。

ILB ASE を使用して独自の DNS サーバーで DNS を構成するには、次の操作を行ってください。

1. &lt;ASE 名&gt;.appserviceenvironment.net 用のゾーンを作成する
1. そのゾーンに、ILB の IP アドレスに * を指定する A レコードを作成する
1. そのゾーンに、ILB の IP アドレスに @ を指定する A レコードを作成する
1. &lt;ASE 名&gt;.appserviceenvironment.net に scm という名前のゾーンを作成する
1. scm ゾーンに、ILB の IP アドレスに * を指定する A レコードを作成する

Azure DNS プライベート ゾーンで DNS を構成するには、次の操作を行ってください。

1. &lt;ASE 名&gt;.appserviceenvironment.net という名前の Azure DNS プライベート ゾーンを作成する
1. そのゾーンに、ILB の IP アドレスに * を指定する A レコードを作成する
1. そのゾーンに、ILB の IP アドレスに @ を指定する A レコードを作成する
1. そのゾーンに、ILB の IP アドレスに *.scm を指定する A レコードを作成する

ASE の既定のドメイン サフィックスの DNS 設定では、アプリがこれらの名前によってのみアクセスできるように制限されていません。 ILB ASE では、アプリの検証なしでカスタム ドメイン名を設定できます。 その後、*contoso.net* という名前のゾーンを作成する場合は、ILB IP アドレスを指すようにすることができます。 カスタム ドメイン名はアプリ要求に対して機能しますが、scm サイトでは使用できません。 scm サイトは、 *&lt;appname&gt;.scm.&lt;asename&gt;.appserviceenvironment.net* でのみ使用できます。 

*.&lt;asename&gt;.appserviceenvironment.net* という名前のゾーンはグローバルに一意です。 2019 年 5 月より前のユーザーは、ILB ASE のドメイン サフィックスを指定できました。 ドメイン サフィックスで *.contoso.com* を使用した場合は、scm サイトが含まれていることになります。 このモデルには、既定の SSL 証明書の管理、scm サイトでのシングル サインオンの欠如、ワイルドカード証明書の使用要件といった課題がありました。 ILB ASE の既定の証明書アップグレード プロセスも中断され、アプリケーションが再起動されました。 これらの問題を解決するため、ILB ASE の動作が、ASE の名前と Microsoft の所有するサフィックスに基づくドメイン サフィックスを使用するように変更されました。 ILB ASE の動作の変更は、2019 年 5 月以降に作成された ILB ASE にのみ影響します。 既存の ILB ASE では、引き続き ASE の既定の証明書とその DNS 構成を管理する必要があります。

## <a name="publishing"></a>発行

マルチテナントの App Service と同様、ASE では次の方法を使用して発行できます。

- Web デプロイ
- FTP
- 継続的インテグレーション (CI)
- Kudu コンソールでのドラッグ アンド ドロップ
- IDE (Visual Studio、Eclipse、IntelliJ IDEA など)

外部 ASE では、これらの発行オプションの動作はすべて同じです。 詳細については、[Azure App Service へのデプロイ][AppDeploy]に関するページを参照してください。

ILB ASE では、発行エンドポイントは ILB を介してのみ利用できます。 ILB は、仮想ネットワークの ASE サブネット内のプライベート IP 上に存在します。 ILB へのネットワーク アクセスができなければ、その ASE にアプリを発行することはできません。 [ILB ASE の作成と使用][MakeILBASE]に関するページで説明されているように、システム内にアプリ用の DNS を構成する必要があります。 SCM のエンドポイントもこの要件の対象となります。 これらのエンドポイントが適切に定義されていないと、発行を行うことができません。 ご利用の IDE から直接発行するには、ILB へのネットワーク アクセスが IDE にも必要です。

追加の変更なしには、インターネットベースの CI システム (GitHub、Azure DevOps など) は ILB ASE では動作しません。発行エンドポイントにインターネットでアクセスできないためです。 ILB ASE を含む仮想ネットワークにセルフホステッド リリース エージェントをインストールすることで、Azure DevOps から ILB ASE への発行を有効にすることができます。 プル モデルを使用した CI システム (Dropbox など) を使用することもできます。

ILB ASE 内のアプリには、その ILB ASE の作成時に使用されたドメインが、発行エンドポイントとして使用されます。 これはアプリの発行プロファイルのほか、アプリのポータル ウィンドウ ( **[概要]**  >  **[要点]** 、 **[プロパティ]** など) で確認することができます。

## <a name="storage"></a>ストレージ

ASE には、ASE 内のすべてのアプリ用に 1 TB のストレージがあります。 Isolated 価格 SKU の App Service プランには、250 GB の制限があります。 ASE では、App Service プランごとに 250 GB のストレージが追加されます。上限は 1 TB です。 App Service プランは 4 つ以上持つことができますが、ストレージは 1 TB の上限を超えて追加されることはありません。

## <a name="logging"></a>ログ記録

ASE を Azure Monitor と統合して、ASE に関するログを Azure Storage、Azure Event Hubs、または Log Analytics に送信できます。 現在、次の項目がログに記録されます。

| 状況 | Message |
|---------|----------|
| ASE is unhealthy (ASE が異常です) | The specified ASE is unhealthy due to an invalid virtual network configuration. (無効な仮想ネットワーク構成が原因で、指定された ASE が異常です。) The ASE will be suspended if the unhealthy state continues. (異常な状態が続くと、ASE は中断されます。) Ensure the guidelines defined here are followed (ここで定義されているガイドラインに従っていることを確認してください): https://docs.microsoft.com/azure/app-service/environment/network-info |
| ASE subnet is almost out of space (ASE サブネットの領域が不足しています) | The specified ASE is in a subnet that is almost out of space. (指定された ASE は、空き領域がほとんどないサブネット内にあります。) There are {0} remaining addresses. (残りのアドレスは {0} 個です。) Once these addresses are exhausted, the ASE will not be able to scale (これらのアドレスが枯渇すると、ASE はスケーリングできなくなります)  |
| ASE is approaching total instance limit (ASE で、インスタンスの合計数の上限に近づいています) | The specified ASE is approaching the total instance limit of the ASE. (指定された ASE で、ASE のインスタンスの合計数の上限に近づいています) It currently contains {0} App Service Plan instances of a maximum 201 instances. (現在、最大インスタンス数 201 のうち、{0} 個の App Service プラン インスタンスが含まれています。) |
| ASE is unable to reach a dependency (ASE は依存関係に到達できません) | The specified ASE is not able to reach {0}. (指定された ASE は {0} に到達できません。)  Ensure the guidelines defined here are followed (ここで定義されているガイドラインに従っていることを確認してください): https://docs.microsoft.com/azure/app-service/environment/network-info |
| ASE is suspended (ASE は中断されています) | The specified ASE is suspended. (指定された ASE は中断されています。) The ASE suspension may be due to an account shortfall or an invalid virtual network configuration. (ASE の中断は、アカウントの不足または無効な仮想ネットワーク構成が原因である可能性があります。) Resolve the root cause and resume the ASE to continue serving traffic (根本原因を解決し、ASE を再開してトラフィックの処理を続行してください) |
| ASE upgrade has started (ASE のアップグレードが開始されました) | A platform upgrade to the specified ASE has begun. (指定された ASE へのプラットフォームのアップグレードが開始されました。) Expect delays in scaling operations (スケーリング操作で遅延が発生することが予想されます) |
| ASE upgrade has completed (ASE のアップグレードが完了しました) | A platform upgrade to the specified ASE has finished (指定された ASE へのプラットフォームのアップグレードが完了しました) |
| Scale operations have started (スケール操作が開始されました) | An App Service plan ({0}) has begun scaling. (App Service プラン ({0}) でスケーリングが開始されました。) Desired state: (必要な状態:){1} I{2} workers (ワーカー)
| Scale operations have completed (スケール操作が完了しました) | An App Service plan ({0}) has finished scaling. (App Service プラン ({0}) でスケーリングが完了しました。) Current state: (現在の状態:){1} I{2} workers (ワーカー) |
| Scale operations have failed (スケール操作が失敗しました) | An App Service plan ({0}) has failed to scale. (App Service プラン ({0}) でスケーリングに失敗しました。) Current state: (現在の状態:){1} I{2} workers (ワーカー) |

ASE でログ記録を有効にするには、次のようにします。

1. ポータルで **[診断設定]** に移動します。
1. **[診断設定の追加]** を選択します。
1. ログ統合の名前を指定します。
1. 目的のログの保存先を選択して構成します。
1. **[AppServiceEnvironmentPlatformLogs]** を選択します。

![ASE 診断ログの設定][4]

Log Analytics と統合している場合は、ASE ポータルから **[ログ]** を選択し、**AppServiceEnvironmentPlatformLogs** に対するクエリを作成することでログを表示できます。 ログは、ASE にトリガーされるイベントがある場合にのみ出力されます。 このようなイベントが ASE にない場合、ログはありません。 Log Analytics ワークスペースでログの例をすばやく確認するには、ASE で App Service プランのいずれかを使用してスケール操作を実行します。 その後、**AppServiceEnvironmentPlatformLogs** に対してクエリを実行し、これらのログを確認できます。 

**アラートの作成**

ログに対してアラートを作成するには、「[Azure Monitor を使用したログ アラートの作成、表示、管理](../../azure-monitor/alerts/alerts-log.md)」の手順に従います。 概要:

* ASE ポータルで [アラート] ページを開く
* **[新しいアラート ルール]** を選択する
* Log Analytics ワークスペースになるリソースを選択する
* カスタムログ検索で条件を設定し、「AppServiceEnvironmentPlatformLogs |」のようなクエリを使用します。ここで、ResultDescription には「has begun scaling」または任意のものを指定します。 適切なしきい値を設定します。 
* 適宜、アクション グループを追加するか作成します。 アクション グループでは、電子メールや SMS メッセージの送信など、アラートへの応答を定義します
* アラートに名前を付けて保存します。

## <a name="upgrade-preference"></a>アップグレードの優先順位

複数の ASE を使用している場合は、一部の ASE を他のものよりも先にアップグレードする必要が生じることがあります。 ASE **HostingEnvironment Resource Manager** オブジェクト内で、**upgradePreference** の値を設定できます。 **upgradePreference** の設定は、テンプレート、ARMClient、または https://resources.azure.com を使用して構成できます。 指定可能な 3 つの値は次のとおりです。

- **なし**: Azure では、特定のバッチなしに ASE がアップグレードされます。 この値は既定値です。
- **Early**:ASE は App Service アップグレードの前半でアップグレードされます。
- **Late**:ASE は App Service アップグレードの後半でアップグレードされます。

https://resources.azure.com を使用している場合は、次の手順に従って **upgradePreferences** 値を設定します。

1. resources.azure.com にアクセスし、お使いの Azure アカウントでサインインします。
1. サブスクリプション\/\[サブスクリプション名\]\/resourceGroups\/\[リソースグループ名\]\/プロバイダー\/Microsoft.Web\/hostingEnvironments\/\[ASE 名\]とリソースを移動します。
1. 上部にある **[読み取り/書き込み]** を選択します。
1. **[編集]** を選択します。
1. **upgradePreference** を 3 つの値のいずれかに設定します。
1. **[Patch]\(パッチ\)** を選択します。

![リソース azure com の表示][5]

**upgradePreferences** 機能は、複数の ASE がある場合に非常に便利です。"Early" の ASE が "Late" の ASE よりも先にアップグレードされるためです。 複数の ASE がある場合は、開発およびテストの ASE を "Early" に、実稼働の ASE を "Late" に設定します。

## <a name="pricing"></a>価格

*Isolated* という価格 SKU は、ASE でのみ使用されます。 ASE でホストされるすべての App Service プランは、Isolated 価格 SKU に属します。 App Service プランの分離率は地域によって異なる場合があります。

App Service プランの料金に加え、ASE そのものの固定料金がかかります。 定額料金は ASE のサイズによって変わることはありません。 App Service プラン インスタンス 15 個ごとに追加フロント エンド 1 という既定スケール率で、ASE インフラストラクチャの料金が発生します。

15 個の App Service プラン インスタンスごとに 1 フロント エンドという既定スケール率が十分でない場合は、フロント エンドが追加される比率またはフロント エンドのサイズを調整できます。 スケール率またはサイズを調整した場合は、既定では追加されないフロント エンド コアの料金が発生します。

たとえば、スケール率を調整して 10 にした場合、App Service プラン内の 10 インスタンスにつきフロント エンドが 1 つ追加されます。 固定料金で対応されるスケール率は、15 インスタンスにつき 1 フロント エンドです。 スケール率が 10 の場合、App Service プランの 10 インスタンスに対して追加される 3 つ目のフロント エンドの料金が追加でかかります。 インスタンス数が 15 になっても、フロント エンドは自動的に追加されるので追加料金は必要ありません。

フロント エンドのサイズを 2 コアに調整して、比率を調整しなかった場合、追加のコアの料金が発生します。 ASE は 2 つのフロント エンドで作成されています。このため、自動スケーリングしきい値を下回っていても、フロント エンドのサイズを 2 コアに拡張した場合は、追加の 2 つのコアの料金を支払うことになります。

詳細については、「[App Service の価格][Pricing]」を参照してください。

## <a name="delete-an-ase"></a>ASE の削除

ASE を削除するには、次の手順に従います。

1. **[App Service Environment]** ウィンドウの上部にある **[削除]** を選択します。

1. ASE の名前を入力して削除を確定します。 ASE を削除すると、その内部のコンテンツもすべて削除されます。

    ![ASE の削除][3]

1. **[OK]** を選択します。

## <a name="ase-cli"></a>ASE CLI

ASE に役立つコマンドライン機能があります。  以下で az cli コマンドを確認できます。

```azurecli
C:\>az appservice ase --help

Group
    az appservice ase : Manage App Service Environments v2.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    create         : Create app service environment.
    delete         : Delete app service environment.
    list           : List app service environments.
    list-addresses : List VIPs associated with an app service environment.
    list-plans     : List app service plans associated with an app service environment.
    show           : Show details of an app service environment.
    update         : Update app service environment.

For more specific examples, use: az find "az appservice ase"
```



<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png
[4]: ./media/using_an_app_service_environment/usingase-logsetup.png
[4]: ./media/using_an_app_service_environment/usingase-logs.png
[5]: ./media/using_an_app_service_environment/usingase-upgradepref.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/alerts/alerts-log.md
