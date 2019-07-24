---
title: Azure Service Fabric セキュリティの概要 | Microsoft Docs
description: この記事では、Azure Service Fabric のセキュリティの概要を示します。
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.openlocfilehash: c5b5f80a43530fe6d0b90e65c3aef89a815157e4
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2019
ms.locfileid: "57241393"
---
# <a name="azure-service-fabric-security-overview"></a>Azure Service Fabric セキュリティの概要
[Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) は、拡張性と信頼性に優れたマイクロサービスのパッケージ化とデプロイ、管理を簡単に行うことができる分散システム プラットフォームです。 Service Fabric は、クラウド アプリケーションの開発と管理における課題に対処します。 開発者と管理者は複雑なインフラストラクチャの問題を避けることができ、スケーラブルで信頼性が高い、ミッション クリティカルで要求の厳しいワークロードの実装に注力できます。

この記事は、Service Fabric をデプロイするためのセキュリティに関する考慮事項の概要です。

## <a name="secure-your-cluster"></a>クラスターのセキュリティ保護
Azure Service Fabric は、マシンのクラスター全体のサービスを統制します。 クラスターは、特に運用ワークロードが実行されている場合に、未承認ユーザーがクラスターに接続するのを防ぐためにセキュリティで保護する必要があります。 セキュリティで保護されていないクラスターを作成することはできますが、これを行うと、(パブリック インターネットに管理エンドポイントを公開している場合に) 匿名ユーザーがそのクラスターに接続できるようになります。

スタンドアロンで、または Azure 上で実行されているクラスターに対して考慮する 2 つのシナリオは、ノード間のセキュリティとクライアントとノード間のセキュリティです。 さまざまなテクノロジを使用して、これらのシナリオを実装できます。

### <a name="node-to-node-security"></a>ノード間のセキュリティ
ノード間のセキュリティは、クラスター内の VM またはマシン間の通信に適用されます。 ノード間のセキュリティの場合、クラスターへの参加が許可されているコンピューターのみが、クラスター内でホストされているアプリケーションとサービスに参加できます。

Azure で実行するクラスターまたは Windows で実行するスタンドアロン クラスターには、[証明書セキュリティ](https://msdn.microsoft.com/library/ff649801.aspx)または [Windows セキュリティ](https://msdn.microsoft.com/library/ff649396.aspx) (Windows Server マシンの場合) を利用できます。

#### <a name="node-to-node-certificate-security"></a>ノード間の証明書セキュリティ

Service Fabric では、クラスターを作成するときに指定した X.509 サーバー証明書を使用します。 これらの証明書の概要とその入手または作成方法については、「[証明書の使用](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates)」を参照してください。

証明書セキュリティは、Azure Portal、Azure Resource Manager テンプレート、またはスタンドアロン JSON テンプレートを使用してクラスターを作成する際に構成します。 プライマリ証明書と、証明書のロールオーバーに使用されるオプションのセカンダリ証明書を指定できます。 指定するプライマリ証明書とセカンダリ証明書は、[クライアントとノードの間のセキュリティ](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security)のために指定する管理用クライアント証明書と読み取り専用クライアント証明書とは異なっている必要があります。

### <a name="client-to-node-security"></a>クライアントとノードの間のセキュリティ
クライアントとノードの間のセキュリティはクライアント ID を使用して構成します。 クライアントとクラスターの間の信頼を確立するためには、どのクライアントの ID なら信頼できるのかを認識できるようにクラスターを構成する必要があります。

Service Fabric では、Service Fabric クラスターに接続されるクライアントのために、2 種類のアクセス コントロールがサポートされています。

-   **管理者**: 読み取り/書き込み機能も含む管理機能へのフル アクセス権があります。
-   **User**:管理機能 (クエリ機能など) への読み取りアクセス権のみがあり、アプリケーションとサービスの問題を解決できます。

クラスター管理者はアクセス制御を使用して、特定の種類のクラスター操作に対するアクセスを制限できます。 その結果、クラスターのセキュリティが強化されます。

#### <a name="client-to-node-certificate-security"></a>クライアントとノードの間の証明書セキュリティ

クライアントとノード間の証明書セキュリティは、Azure Portal、Resource Manager テンプレート、またはスタンドアロン JSON テンプレートを使用してクラスターを作成する際に構成します。 管理用クライアント証明書、ユーザー クライアント証明書、またはその両方を指定する必要があります。 これらの証明書は、ノード間セキュリティ用に指定するプライマリ証明書とセカンダリ証明書とは異なっていることを確認してください。

管理用証明書を使用してクラスターに接続するクライアントには、管理機能へのフル アクセス権があります。 読み取り専用ユーザー クライアント証明書を使用してクラスターに接続するクライアントには、管理機能に対する読み取りアクセス権しかありません。 つまり、これらの証明書は、ロールベースのアクセス制御 (RBAC) に使用されます。

クラスターの証明書セキュリティを構成する方法については、「[Azure Resource Manager を使用して Service Fabric クラスターを作成する](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)」を参照してください。

#### <a name="client-to-node-azure-active-directory-security"></a>Azure Active Directory のクライアントとノード間のセキュリティ

Azure で実行されているクラスターは、Azure Active Directory (Azure AD) を使用して管理エンドポイントへのアクセスをセキュリティで保護することもできます。 必要な Azure Active Directory アーティファクトを作成する方法、クラスターの作成中にそれらを設定する方法、これらのクラスターに接続する方法については、「[Azure Resource Manager を使用して Service Fabric クラスターを作成する」](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)を参照してください。

Azure AD は組織 (テナントと呼ばれます) を有効にしてアプリケーションに対するユーザー アクセスを管理します。 Web ベースのサインイン UI を持つアプリケーションと、ネイティブ クライアントのエクスペリエンスを持つアプリケーションがあります。

Service Fabric クラスターでは、Web ベースの Service Fabric Explorer や Visual Studio など、いくつかのエントリ ポイントから管理機能にアクセスできます。 このため、クラスターへのアクセスを制御するには、2 つの Azure AD アプリケーション (Web アプリケーションとネイティブ アプリケーション) を作成します。

Azure クラスターについては、クライアントの認証に Azure AD セキュリティ、ノード間のセキュリティに証明書を使用することをお勧めします。

Windows Server 2012 R2 と Active Directory を使用しているスタンドアロン Windows Server クラスターの場合、Windows セキュリティとグループの管理されたサービス アカウント (gMSAs) を使用することをお勧めします。 それ以外の場合は、Windows アカウントで Windows セキュリティを使用してください。

## <a name="understand-monitoring-and-diagnostics-in-service-fabric"></a>Azure Service Fabric での監視と診断を理解する
[監視と診断](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview)は、あらゆる環境でアプリケーションやサービスを開発、テスト、およびデプロイするために非常に重要です。 アプリケーションやサービスがローカル開発環境や運用環境で想定どおり機能するように監視と診断を実装する場合に、Service Fabric ソリューションは最も効果的に機能します。

セキュリティの観点から見た、監視と診断の主な目標は次のとおりです。

-   セキュリティ イベントのため発生した可能性があるハードウェアおよびインフラストラクチャの問題を検出し、診断します。
-   脅威存在痕跡 (IoC) の可能性があるソフトウェアおよびアプリの問題を検出します。
-   不注意によるサービス拒否攻撃を防ぐために役立つリソースの消費量を把握します。

監視と診断のワークフローは、次の 3 つの手順で構成されます。

1.  **イベントの生成**: インフラストラクチャ (クラスター) レベルとアプリケーション/サービス レベルの両方でのイベント (ログ、トレース、カスタム イベント) が含まれます。 提供される内容とインストルメンテーションの追加方法については、[インフラストラクチャ レベルのイベント](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-infra)と[アプリケーション レベルのイベント](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app)に関する記事を参照してください。

2.  **イベントの集計**: 生成されたイベントを表示するには、生成されたイベントを収集して集計する必要があります。 一般的に、[Azure Diagnostics](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) (エージェント ベースのログ収集に類似) または [EventFlow](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-eventflow) (インプロセスのログ収集) を使用することをお勧めします。

3.  **分析**: イベントは、分析して表示できるように、視覚化して、特定の形式でアクセスできるようにする必要があります。 データの監視と診断を分析し、視覚化するプラットフォームはいくつかあります。 Service Fabric と緊密に統合できるため、[Azure Monitor ログ](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-oms)と [Azure Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights) の 2 つを推奨します。

[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) を使用しても、Service Fabric クラスターの基盤となる Azure リソースの多くを監視できます。

ウォッチドッグは、複数のサービスにわたって正常性と負荷を監視し、正常性モデル階層内のあらゆるものの正常性を報告することができる別のサービスです。 ウォッチドッグを使用することで、1 つのサービスの観点では検出されないエラーを防ぐことができます。 

ウォッチドッグは、ユーザーの介入なしで対応策を実行するコードをホストするのに適した場所でもあります。 たとえば、一定の間隔でストレージ内のログ ファイルをクリーンアップします。 サンプルのウォッチドッグ サービス実装については、「[Azure Service Fabric watchdog sample](https://azure.microsoft.com/resources/samples/service-fabric-watchdog-service/)」(Azure Service Fabric のウォッチドッグ サンプル) を参照してください。

## <a name="secure-communication-by-using-certificates"></a>証明書を使用して通信をセキュリティで保護する
証明書を使用すると、スタンドアロン Windows クラスターの多様なノード間で行われる通信をセキュリティで保護できます。 X.509 証明書を使用すると、そのクラスターに接続しているクライアントを認証することもできます。 これにより、権限を持つユーザーのみがクラスターにアクセスできるようになります。 クラスターを作成するときに、クラスターで証明書を有効にすることをお勧めします。

一般的に、X.509 デジタル証明書は、クライアントとサーバーの認証に使用されています。 また、メッセージの暗号化やデジタル署名にも使用されます。

次の表は、クラスターのセットアップに必要な証明書の一覧です。

|証明書情報の設定 |説明|
|-------------------------------|-----------|
|ClusterCertificate|    クラスターのノード間の通信をセキュリティで保護するには、この証明書が必要です。 2 つの異なるクラスター証明書を使用できます。プライマリ証明書と、アップグレードのためのセカンダリ証明書です。|
|ServerCertificate| この証明書は、クライアントがこのクラスターに接続しようとしたときに、クライアントに提示されます。 2 つのサーバー証明書を使用できます。プライマリ証明書と、アップグレードのためのセカンダリ証明書です。|
|ClientCertificateThumbprints|  認証されたクライアントにインストールする証明書のセットです。|
|ClientCertificateCommonNames|  CertificateCommonName の最初のクライアント証明書の共通名です。 CertificateIssuerThumbprint は、この証明書の発行者の拇印です。|
|ReverseProxyCertificate|   これは、[リバース プロキシ](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy)をセキュリティで保護するときに指定できる、省略可能な証明書です。|

証明書のセキュリティ保護の詳細については、「[X.509 証明書を使用した Windows でのスタンドアロン クラスターの保護](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security)」を参照してください。

## <a name="understand-role-based-access-control"></a>ロールベースのアクセス制御を理解する
クラスターの作成時に、管理者ロールとユーザー クライアント ロールを指定して、それぞれに個別の ID (証明書など) を設定します。 既定のアクセス制御の設定と、既定の設定を変更する方法の詳細については、「[ロール ベースのアクセス制御 (Service Fabric クライアント用)](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles)」を参照してください。

## <a name="secure-standalone-clusters-by-using-windows-security"></a>Windows セキュリティを使用してスタンドアロン クラスターをセキュリティで保護する
Service Fabric クラスターへの未承認のアクセスを防ぐには、クラスターをセキュリティで保護する必要があります。 クラスターで運用環境のワークロードが実行されている場合は、セキュリティが特に重要となります。 ClusterConfig.JSON ファイルで Windows セキュリティを使用して、ノード間およびクライアントとノード間のセキュリティを構成します。

Service Fabric を gMSA で実行する必要がある場合は、[ClustergMSAIdentity](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security) を設定してノード間のセキュリティを構成します。 ノード間の信頼関係を構築するには、各ノードに互いを認識させる必要があります。

Active Directory ドメイン内のコンピューター グループを使用する場合、ClusterIdentity を設定してノード間のセキュリティを構成します。 詳細については、[Active Directory でのグループの作成](https://msdn.microsoft.com/library/aa545347)に関する記事を参照してください。

クライアントとノードの間のセキュリティはクライアント ID を使用して構成します。 どのクライアントの ID なら信頼できるのかを認識するようにクラスターを構成する必要があります。 信頼は、次の 2 つの方法で確立できます。

-   接続可能なドメイン グループ ユーザーを指定する。
-   接続可能なドメイン ノード ユーザーを指定する。

## <a name="configure-application-security-in-service-fabric"></a>Service Fabric でアプリケーションのセキュリティを構成する
### <a name="manage-secrets-in-service-fabric-applications"></a>Service Fabric アプリケーションでシークレットを管理する
シークレットは、ストレージ接続文字列、パスワード、プレーン テキストで処理できないその他の値など、機密情報である可能性があります。

[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) を使用してキーとシークレットを管理できます。 ただし、アプリケーションでのシークレットの使用は、特定のクラウド プラットフォームに依存しません。 任意の場所でホストされているクラスターにアプリケーションをデプロイできます。 このフローには、次の 4 つの主な手順があります。

1.  データ暗号化証明書を取得します。
2.  クラスターに証明書をインストールします。
3.  アプリケーションをデプロイするときに、証明書を使用してシークレット値を暗号化し、サービスの Settings.xml 構成ファイルに挿入します。
4.  同じ暗号化証明書によって暗号化を解除して、Settings.xml から暗号化された値を読み取ります。

詳細については、「[Service Fabric アプリケーションでのシークレットの管理](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management)」を参照してください。

### <a name="configure-security-policies-for-an-application"></a>アプリケーションのセキュリティ ポリシーを構成する
Azure Service Fabric セキュリティを使用すると、別のユーザー アカウントを使用してクラスターで実行しているアプリケーションをセキュリティで保護できます。 Service Fabric セキュリティは、そのユーザー アカウントでデプロイするときにアプリケーションによって使用されるリソース (ファイル、ディレクトリ、証明書など) を保護するためにも役立ちます。 これにより、実行中のアプリケーションは、共有のホスト環境にある場合でも、より安全になります。

セキュリティ ポリシーを構成するためのタスクには、以下が含まれます。

-   サービスのセットアップ エントリ ポイントのポリシーを構成する
-   セットアップ エントリ ポイント から PowerShell コマンドを起動する
-   ローカル デバッグにコンソールのリダイレクトを使用する
-   サービス コード パッケージのポリシーを構成する
-   HTTP と HTTPS エンドポイントのセキュリティ アクセス ポリシーを割り当てる

## <a name="secure-communication-for-services"></a>サービスの通信をセキュリティで保護する
セキュリティは、通信の最も重要な側面の 1 つです。 Reliable Services アプリケーション フレームワークに最初から用意されている通信スタックとツールを利用してセキュリティを確保することができます。 詳細については、[サービスのリモート処理通信のセキュリティ保護](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順
- クラスターのセキュリティの概念については、「[Azure Resource Manager を使用して Service Fabric クラスターを作成する](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)」と「[Azure Portal を使用して Service Fabric クラスターを作成する](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal)」を参照してください。
- Service Fabric のクラスターのセキュリティの詳細については、「[Service Fabric クラスターのセキュリティに関するシナリオ](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security)」を参照してください。
