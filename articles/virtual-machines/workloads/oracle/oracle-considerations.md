---
title: Microsoft Azure での Oracle ソリューション | Microsoft Docs
description: Microsoft Azure でサポートされている Oracle ソリューションの構成と制限事項について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: jeconnoc
tags: azure-resource-management
ms.assetid: 5d71886b-463a-43ae-b61f-35c6fc9bae25
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/02/2018
ms.author: rogirdh
ms.custom: seodec18
ms.openlocfilehash: 945ba9b2ba4dbc22941ca6b105417f591f2dd837
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58012743"
---
# <a name="oracle-solutions-and-their-deployment-on-microsoft-azure"></a>Microsoft Azure での Oracle ソリューションとそのデプロイ
この記事では、Microsoft Azure でさまざまな Oracle ソリューションを適切にデプロイするために必要な情報を取り上げます。 これらのソリューションは、Azure Marketplace で Oracle によって発行された仮想マシン イメージに基づいています。 現在利用可能なイメージの一覧を取得するには、次のコマンドを実行します。
```azurecli-interactive
az vm image list --publisher oracle -o table --all
```
2017 年 6 月 16 日時点でのイメージの一覧は以下のとおりです。
```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170202             12.1.20170202
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170202             12.1.20170202
Oracle-Linux            Oracle       6.4                     Oracle:Oracle-Linux:6.4:6.4.20141206                         6.4.20141206
Oracle-Linux            Oracle       6.7                     Oracle:Oracle-Linux:6.7:6.7.20161007                         6.7.20161007
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20161020                         6.8.20161020
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20170406                         6.9.20170406
Oracle-Linux            Oracle       7.0                     Oracle:Oracle-Linux:7.0:7.0.20141217                         7.0.20141217
Oracle-Linux            Oracle       7.2                     Oracle:Oracle-Linux:7.2:7.2.20161020                         7.2.20161020
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20170320                         7.3.20170320
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
```

これらのイメージは、"ライセンス持ち込み" と見なされるため、VM の実行によって発生するコンピューティング、ストレージ、およびネットワーキングのコストのみが課金されます。  つまり、Oracle ソフトウェアを使用するライセンスが適切に供与されていて、Oracle と現在サポート契約を結んでいることを前提としています。 Oracle では、オンプレミスから Azure へのライセンス モビリティを保証しています。 ライセンス モビリティの詳細については、公開されている [Oracle および Microsoft](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) の覚書を参照してください。 

ユーザーは、Azure で一から作成したカスタム イメージに基づくソリューションにするか、オンプレミス環境からカスタム イメージをアップロードするかを選択することもできます。

## <a name="support-for-jd-edwards"></a>JD Edwards のサポート
JD Edwards EnterpriseOne バージョン 9.2、以降は、Oracle のサポート情報 [Doc ID 2178595.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4) に従って、固有の `Minimum Technical Requirements` (MTR) を満たす**すべてのパブリック クラウド ソリューション**でサポートされています。  OS およびソフトウェア アプリケーションの互換性の MTR 仕様に準拠したカスタム イメージを作成する必要があります。 

## <a name="oracle-database-virtual-machine-images"></a>Oracle データベース仮想マシン イメージ
Oracle では、Oracle Linux に基づく仮想マシン イメージで、Azure における Oracle DB 12.1 Standard Edition および Enterprise Edition の実行をサポートしています。  Azure で Oracle DB の実稼働ワークロードのパフォーマンスを最大にするには、VM イメージを必ず適切にサイズ変更し、Premium Storage でサポートされている Managed Disks を使用してください。 Oracle の発行済み VM イメージを使用して Azure で Oracle DB を迅速に立ち上げて実行する方法については、[Oracle DB のクイック スタート チュートリアルを試してください](oracle-database-quick-create.md)。

### <a name="attached-disk-configuration-options"></a>接続ディスクの設定オプション

接続ディスクは Azure Blob Storage サービスを利用します。 各 Standard ディスクは理論上、毎秒最大約 500 回 の入出力の操作 (IOPS) が可能です。 Premium ディスク オファリングでは、高パフォーマンスのデータベース ワークロードが優先され、ディスクあたり最大 5000 IOps を実現できます。 パフォーマンスの要件を満たしている場合は、1 つのディスクを使用できます。一方、複数の接続ディスクを使い、それらの間でデータベースのデータを分散させ、Oracle Automatic Storage Management (ASM) を利用すると、効果的に IOPS パフォーマンスを改善できます。 Oracle ASM に固有の情報については、[Oracle Automatic Storage の概要](https://www.oracle.com/technetwork/database/index-100339.html)に関するページを参照してください。 Linux の Azure VM で Oracle ASM をインストールおよび構成する方法の例については、[Oracle Automated Storage Management のインストールと構成](configure-oracle-asm.md)のチュートリアルを試みることができます。

## <a name="oracle-real-application-cluster-oracle-rac"></a>Oracle Real Application Cluster (Oracle RAC)
Oracle RAC では、オンプレミスのマルチノード クラスター構成の単一ノードで発生する障害を軽減するように設計されています。 このクラスターは、超大規模パブリック クラウド環境にネイティブではない、2 つのオンプレミスのテクノロジに依存しています。それがネットワーク マルチキャストと共有ディスクです。 お使いのデータベース ソリューションの Azure で Oracle RAC が必要な場合は、サード パーティのソフトウェアを使用してこれらのテクノロジを有効にする必要があります。 Oracle RAC の詳細については、[FlashGrid ソリューション](https://www.flashgrid.io/oracle-rac-in-azure/)に関するページを参照してください。

## <a name="high-availability-and-disaster-recovery-considerations"></a>高可用性とディザスター リカバリーに関する考慮
Azure で Oracle データベースを使用する場合、いかなるダウンタイムも回避するために高可用性とディザスター リカバリー ソリューションを実装する責任があります。 

Azure の Oracle Database Enterprise Edition (Oracle RAC を使用しない) では、Azure で [Data Guard, Active Data Guard](https://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html) または [Oracle Golden Gate](https://www.oracle.com/technetwork/middleware/goldengate) と 2 つの異なる仮想マシンの 2 つのデータベースを使用することで高可用性とディザスター リカバリーを実現できます。 両方の仮想マシンを同じ[仮想ネットワーク](https://azure.microsoft.com/documentation/services/virtual-network/)に置き、プライベートの固定 IP アドレスで互いにアクセスできるようにする必要があります。  さらに、Azure が仮想マシンを個別の障害ドメインやアップグレード ドメインに配置できるように、仮想マシンを同じ可用性セットに配置することをお勧めします。  地理的冗長性を実現する必要がある場合は、2 つの異なるリージョン間でこれらの 2 つのデータベースをレプリケートし、2 つのインスタンスを VPN Gateway で接続することができます。

[Azure での Oracle DataGuard の実装](configure-oracle-dataguard.md)に関するチュートリアルでは、Azure でこれを試すための基本的なセットアップ手順を説明しています。  

Oracle Data Guard では、1 つの仮想マシンにプライマリ データベース、別の仮想マシンにセカンダリ データベース (待機)、そしてその間に一方向のレプリケーションセットを配置することで高可用性を実現できます。 データベースのコピーへのアクセスを結果として読み込みます。 Oracle GoldenGate では、2 つのデータベース間に双方向レプリケーションを構成することができます。 これらのツールを使用してデータベース用に高可用性ソリューションを設定する方法については、Oracle の Web サイトにある [Active Data Guard](https://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) および [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) に関する文書をご覧ください。 データベースのコピーに読み込み/書き込みアクセスをする必要がある場合は、 [Oracle Active Data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html)もご利用いただけます。

[Azure での Oracle GoldenGate の実装](configure-oracle-golden-gate.md)に関するチュートリアルでは、Azure でこれを試すための基本的なセットアップ手順を説明しています。

Azure で高可用性と災害復旧ソリューションが設計されていたとしても、データベースを復元するバックアップ戦略の確保が必要になります。  [Oracle データベースのバックアップと復旧](oracle-backup-recovery.md)に関するチュートリアルでは、一貫性のあるバックアップを確立するための基本的な手順を説明しています。

## <a name="oracle-weblogic-server-virtual-machine-images"></a>Oracle WebLogic Server 仮想マシン イメージ
* **クラスタリングは Enterprise エディションでのみサポートされています。** WebLogic Server の Enterprise Edition を使用する場合にのみ、WebLogic クラスタリングを使用するライセンスが許諾されます。 WebLogic Server Standard Edition の場合、クラスタリングを使用しないでください。
* **UDP マルチキャストはサポートされていません。** Azure は UDP ユニキャストをサポートしていますが、マルチキャストとブロードキャストはサポートしていません。 WebLogic Server は Azure の UDP ユニキャスト機能に依存できます。 UDP ユニキャストへの依存で最適な結果を得るための、WebLogic クラスターのサイズを静的に保ち、クラスターに 10 以上の管理サーバーを置かないことを推奨します。
* **WebLogic Server は T3 アクセスに対し、パブリック ポートとプライベート ポートを同じにすることを要求します (Enterprise JavaBeans を使用する場合など)。** 次の多層シナリオを検討してみましょう。**SLWLS** という vNet 上の、2 つ以上の VM で構成される WebLogic Server クラスターで、サービス層 (EJB) アプリケーションが実行されているとします。 クライアント層は同じ vNet の別のサブネットにあり、サービス層にある EJB を呼び出すためにシンプルな Java プログラムを実行します。 サービス層の負荷を分散する必要があるため、負荷分散されたパブリックなエンドポイントを WebLogic Server クラスター内の仮想マシンに作成する必要があります。 指定したプライベート ポートがパブリックポート (7006:7008 など) と異なる場合、次のようなエラーが発生します。

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   どのようなリモート T3 アクセスにおいても、WebLogic Server に対してロード バランサー ポートおよび WebLogic 管理サーバーのポートが同じでなければいけないためです。 上記の場合、クライアントはポート 7006 (ロード バランサー ポート) へ接続し、管理サーバーは 7008 (プライベート ポート) で待機します。 この制限は、HTTP ではなく T3 アクセスにのみ適用されます。

   この問題を避けるには、次の回避策のいずれかを実施してください。

  * T3 アクセス専用の負荷分散エンドポイントに対して、同じプライベートおよびパブリック ポート番号を使用します。
  * WebLogic Server を開始する場合、次の JVM パラメーターを含めてください。

         -Dweblogic.rjvm.enableprotocolswitch=true

関連情報については、<https://support.oracle.com> にあるサポート技術情報 **860340.1** をご覧ください。

* **動的なクラスタリングと負荷分散の制限事項。** WebLogic Server で動的なクラスターを使用し、Azure 上の単一のパブリック負荷分散エンドポイントでそれを公開すると仮定します。 これは、それぞれの管理サーバーに固定のポート番号を使用し (範囲から動的に割り当てられていない)、管理者が追跡記録しているマシンより多く管理サーバーを起動しない (つまり、1 つの仮想マシンに対する管理サーバーが複数にならない) 限り、実現できます。 設定の結果、起動する WebLogic Server の数が仮想マシンより多くなる場合 (つまり、複数の WebLogic Server インスタンスが同じ仮想マシンを共有する場合)、指定したポート番号に複数の WebLogic Server インスタンス サーバーをバインドすることはできません。その仮想マシンのその他の WebLogic Server インスタンスは失敗します。

   管理サーバーに個別のポート番号を自動的に割り当てるように管理サーバーを設定した場合、負荷分散はできません。そのような設定では、単一のパブリック ポートから複数のプライベート ポートにマッピングする必要がありますが、Azure ではサポートされていないためです。
* **仮想マシン上の Weblogic サーバーの複数のインスタンス。** 仮想マシンが十分に大きい場合、デプロイ要件によりますが、同じ仮想マシン上で複数の WebLogic Server のインスタンスを実行することも検討できます。 たとえば、2 つのコアを持つ中程度のサイズの仮想マシンでは、WebLogic Server で 2 つのインスタンスを実行できます。 ただし、1 つの仮想マシンで複数の WebLogic Server インスタンスを実行する場合のように、単一障害点をアーキテクチャに導入することは避けることが推奨されます。 2 つ以上の仮想マシンを使うほうがより優れた手法であり、各仮想マシンは複数の WebLogic Server のインスタンスを実行できるようになります。 それでもなお、これらの WebLogic Server のインスタンスは同じクラスターの一部にすることができます。 ただし、現在は、Azure のロード バランサーを利用する場合、負荷を分散するサーバーを個別の仮想マシン間で配布する必要があるため、同じ仮想マシン内の WebLogic Server のデプロイによって公開されるエンドポイントの負荷を Azure で分散することはできません。

## <a name="oracle-jdk-virtual-machine-images"></a>Oracle JDK 仮想マシンのイメージ
* **JDK 6 および 7 の最新アップデート。** パブリックをサポートする Java の最新バージョン (現在は Java 8) の使用を推奨していますが、Azure には JDK 6 および JDK 7 のイメージもご利用いただけます。 これは JDK 8 へのアップグレードの準備が整っていない従来のアプリケーションのための対処としてです。 一般向けには以前の JDK イメージの一般公開は終了していますが、Microsoft と Oracle の業務提携により、Azure 提供の JDK 6 および 7 のイメージには、通常、Oracle のサポート対象顧客の選ばれたグループにのみ Oracle が提供する最近の非公開更新が含まれます。 新しいバージョンの JDK イメージは JDK 6 および 7 の更新版がリリースされた後に利用可能になります。

   この JDK 6 および JDK 7 のイメージで利用可能な JDK と、そこから派生した仮想マシンおよびイメージは、Azure 内でのみ使用できます。
* **64-bit JDK.** Azure によって提供される Oracle WebLogic Server 仮想マシンおよび Oracle JDK 仮想マシンのイメージには、Windows Server と JDK 両方の 64 ビット版が含まれています。

## <a name="next-steps"></a>次の手順
Microsoft Azure での現在の Oracle ソリューションの概要を理解しました。 次の手順では、Azure で最初の Oracle データベースをデプロイします。
- 手始めに、[Azure での Oracle データベースの作成](oracle-database-quick-create.md)に関するチュートリアルを試してください。
