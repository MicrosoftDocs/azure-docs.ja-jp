---
title: Azure Government Operations Management Suite | Microsoft Docs
description: "この記事では、Operations Management Suite での Log Analytics がどのように米国政府機関およびソリューション プロバイダーに適用可能であるかについて説明します"
services: azure-government
cloud: gov
documentationcenter: 
author: sacha
manager: jobruno
ms.assetid: 
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/05/2016
ms.author: saweld
translationtype: Human Translation
ms.sourcegitcommit: 35cefe7f394e0f332b7b6fd4ec1609ae04f5fc6f
ms.openlocfilehash: 84eabe4bb79262c47f530c59e36119bacc4115d7

---

# <a name="azure-government-cybersecurity-monitoring-and-securing-your-assets-with-operations-management-suite"></a>Azure Government Cybersecurity: Operations Management Suite による資産の監視と保護 

## <a name="cybersecurity-in-the-cloud"></a>クラウドでのサイバーセキュリティ
クラウドに移行するお客様にとって最も重要で関心があることは、クラウドにデプロイした Azure Government サービスの資産の管理とセキュリティを維持することです。 仮想マシンのファイアウォールを正しく構成する必要があります。 仮想ネットワークには、適切なネットワーク セキュリティ グループを適用する必要があります。 資産へのアクセスは、適切な時間にロックダウンする必要があります。 これらの作業ストリームはすべて、機関が使用するインフラストラクチャのセキュリティを保護するために計画、設計、およびプロビジョニングする必要があります。

このような環境のセットアップは、場合によっては一筋縄ではいきません。 サーバーのフリートを監視サービスにオンボードするように拡張する操作は難しく、監視サービスを更新するのも困難です。 複数のクラウド プロバイダーにまたがるインフラストラクチャや、クラウドとオンプレミスにまたがるインフラストラクチャを監視することは困難です。 また、常に最新の状況を監視し、Azure Application Insights を有効にしてサイバーセキュリティの脅威に対する監視、検出、アラート、対策を行うには、時間やリソース、コンピューティング能力が必要です。

## <a name="microsoft-operations-management-suite"></a>Microsoft Operations Management Suite
Azure Government で利用できるようになった Microsoft Operations Management Suite は、これらすべてを迅速に簡単に実行できる Azure のサービス セットです。 この記事は、ハイパースケールのログ検索機能を使用してすばやくデータを分析し、環境内の脅威を明らかにする Log Analytics の使用に焦点を当てて説明します。

Azure Log Analytics で、次のことが可能になります。

* Azure、その他のクラウド プロバイダー、およびオンプレミスの個々の VM (Linux および Windows) にエージェントをデプロイできます。
* 既存のログ データのある Azure Government ストレージ アカウントまたは System Center Operations Manager エンドポイントを通じて、既存のログに接続できます。

ここで記述した考慮事項に対処するいくつかの既製のソリューションやフリートに Log Analytics を統合する方法を見てみましょう。

## <a name="onboarding-servers-to-log-analytics"></a>Log Analytics へのサーバーのオンボード
クラウド資産と Log Analytics を統合するための最初のステップは、すべてのログのソースに Log Analytics エージェントをインストールすることです。 仮想マシンの場合は、エージェントを Log Analytics ポータルから手動でダウンロードできるため、非常に簡単です。

![図 1: Operations Manager Suite に接続されている Windows サーバー](./media/documentation-government-oms-figure1.png)
<p align="center">図 1: Log Analytics に接続されている Windows サーバー</p>

Azure Portal を通じて、Azure VM を直接 Log Analytics に接続できます。 手順については、「[New ways to enable Log Analytics on your Azure VMs](https://blogs.technet.microsoft.com/momteam/2016/02/10/new-ways-to-enable-log-analytics-oms-on-your-azure-vms/)」(Azure VM で Log Analytics を有効にする新しい方法) を参照してください。

プログラムで接続したり、Log Analytics 仮想マシン拡張機能を Azure Resource Manager テンプレートに構成したりすることもできます。 Windows ベースのマシンでの手順については「[Windows コンピューターを Log Analytics に接続する](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-windows-agents)」を、Linux ベースのマシンでの手順については「[Linux コンピューターを Log Analytics に接続する](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-linux-agents)」を参照してください。

## <a name="onboarding-storage-accounts-and-operations-manager-to-log-analytics"></a>ストレージ アカウントおよび Operations Manager を Log Analytics にオンボードする
Log Analytics は、ストレージ アカウントや既存の System Center Operations Manager デプロイにも接続できるため、ハイブリッドのシナリオ (複数のクラウド プロバイダーにまたがったり、クラウドとオンプレミスにまたがったりするインフラストラクチャ) で運用管理を行うことができます。

![図 2: Log Analytics への Azure Storage および Operations Manager の接続](./media/documentation-government-oms-figure2.png)
<p align="center">図 2: Log Analytics への Azure Storage および Operations Manager の接続</p>

Log Analytics は、Chef や Puppet など、他の監視サービスからのログ情報の収集もサポートしています。 さらに、Azure デプロイでは、Log Analytics が有効な Azure Resource Manager テンプレートがある VM があるため、コンピューティングのデプロイと Log Analytics ワークスペースへのオンボードを同時に行うことができます。

![図 3: Log Analytics VM 拡張機能を持つ Azure VM の Azure Resource Manager テンプレート](./media/documentation-government-oms-figure3a.png)
![図 3: Log Analytics 拡張機能を持つ Azure VM の Azure Resource Manager テンプレート](./media/documentation-government-oms-figure3b.png)
<p align="center">図 3: Log Analytics VM 拡張機能を持つ Azure VM の Azure Resource Manager テンプレート</p>

既存のオンプレミス Operations Manager 実装での Log Analytics のセットアップについては、「[Operations Manager を Log Analytics に接続する](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-om-agents)」を参照してください。

## <a name="applying-intelligence-through-operations-management-suite-solution-packs"></a>Operations Management Suite ソリューション パックによるインテリジェンスの適用
ログ データのためのさまざまなソースを利用できるようになりまた。次はそれらのデータの意味を理解する必要があります。

Log Analytics は、基本的には、数千から数百万にも及ぶログを迅速に検索するための強力なクエリを記述することができる、ログ検索サービスです。 ただし、クエリの記述に必要な問題を発見することは困難な場合があります。

そのような場合に Operations Management Suite ソリューションを使用します。 これらは、Log Analytics とネイティブに統合されているクエリのパックであり、Log Analytics で管理されているフリートに対する洞察をプロアクティブに得ることができます。

サイバーセキュリティのテーマで、Log Analytics ですぐに解決できる&3; つのサイバーセキュリティ シナリオについて簡単に説明します。

### <a name="antimalware-assessment"></a>マルウェア対策評価
マルウェア対策評価には、フリートがマルウェアからどのくらい適切に保護されているかをひとめで把握できるようにするためのクエリ、通知、および監視ダッシュボードの既定のセットが用意されています。

このダッシュボードでは、次の&4; つのことのリストが示されます。
* アクティブまたは修復済みの脅威があるサーバー。
* 現在検出されている脅威。
* 十分に保護されていないコンピューター。 Log Analytics は、コンピューターのログをクロールし、一般的な Web ブラウザーで開かれている FW のサイトや不適切に構成されたルールを探して、この情報をを検出します。
* 保護されているサーバーがどのように保護されているかの分析。例えば、ネイティブの Windows OS ウイルス防止や、System Center Endpoint Protection のようなソリューションが使用されているかどうか。

次の例では、Systems Center によって脅威が捕捉され、自動的にトリアージされているのがわかります。

![図 4: Operation Management Suite マルウェア対策評価ソリューション](./media/documentation-government-oms-figure4.png)
<p align="center">図 4: Operation Management Suite マルウェア対策評価ソリューション</p>

マルウェア対策評価の詳細については、「[Log Analytics のマルウェアの評価ソリューション](https://azure.microsoft.com/en-us/documentation/articles/log-analytics-malware/)」を参照してください。

### <a name="identity-and-access"></a>ID とアクセス
クラウドでのもう&1; つの一般的なサイバーセキュリティ シナリオは、資格情報の侵害に関するものです。 クラウド サブスクリプションに資格情報があるだけでなく、各 VM にもユーザーやユーザーに関連付けられているシークレット (通常は証明書またはパスワード) があります。

Log Analytics は、フリートでのすべてのサインイン試行を整理し、種類 (リモート、ローカル、ユーザー名など) に応じてバケットに分類します。 例えば次の例では、ランダムな文字列をユーザー名として使用したサインイン試行が、大量に失敗していることがわかります。 これは、コンピューターが公開され、ファイアウォールおよびアクセス制御リストによって適切に保護されていない可能性が高いことを示しています。

![図 5: 過去 24 時間以内に 97.3% のサインインが失敗](./media/documentation-government-oms-figure5.png)
<p align="center">図 5: 過去 24 時間以内に 97.3% のサインインが失敗</p>

### <a name="threat-intelligence"></a>脅威インテリジェンス
Log Analytics には、悪意のある内部関係者のシナリオに対する保護も用意されています。組織内でセキュリティ侵害があり、悪意のあるユーザーがデータを外部に漏えいさせようとしている場合です。

Log Analytics 脅威インテリジェンスは、コンピューターのすべてのネットワーク ログを調べ、既知の悪意のある IP (たとえば、インデックスのないダーク ネット上の IP アドレス) への受信/送信ネットワーク接続を自動的に検索して通知します。

例えば、次のスクリーンショットでは、中華人民共和国への送受信両方のネットワーク接続があることがわかります。

受信タグをダブルクリックすると、Log Analytics によって管理されている Linux VM が中国の既知のダーク ネット IP アドレスへの送信接続を行っていることがわかります。

脅威インテリジェンスのような Operations Management Suite ソリューションに対するアラートをセットアップすることもできます。 次のスクリーンショットでは、Log Analytics が既知の悪意のある IP アドレスへの 10 を超える送信接続を検出したら、電子メールでアラートを送信するように、アラートをセットアップしています。 次に、そのアラートを構成して、その VM を自動的にシャットダウンするようにセットアップされた Azure Automation ジョブを起動するようにします。

![図 6: Operations Management Suite アラートおよびオートメーション](./media/documentation-government-oms-figure6.png)
<p align="center">図 6: Operations Management Suite アラートおよびオートメーション</p>

これは、フリートに適用できる既製の Operations Management Suite ソリューションの一例に過ぎません。実行されている場所がどこであるか (Azure、他のクラウド サービス プロバイダー、またはオンプレミス) は、関係ありません。

Operations Managemt Suite は最新の脅威用対策として今後も自動的に機械学習を更新し、Azure Marketplace への新しいソリューションのロールアウトも引き続き行われます。

Operations Management Suite の詳細については、[ドキュメント](https://azure.microsoft.com/en-us/documentation/articles/documentation-government-overview/)のページを参照してください。



<!--HONumber=Jan17_HO1-->


