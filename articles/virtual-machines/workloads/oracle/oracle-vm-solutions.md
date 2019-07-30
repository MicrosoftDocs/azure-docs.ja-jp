---
title: Azure 仮想マシンでの Oracle ソリューション | Microsoft Docs
description: Microsoft Azure でサポートされている Oracle 仮想マシン イメージの構成と制限事項について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/23/2019
ms.author: rogirdh
ms.custom: seodec18
ms.openlocfilehash: 70e87a38373688c1b364a079cd07934309662e3e
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707430"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Microsoft Azure での Oracle VM イメージとそのデプロイ

この記事では、Azure Marketplace で Oracle によって発行された仮想マシン イメージに基づく Oracle ソリューションについての情報を扱います。 Oracle Cloud Infrastructure を使用したクラウド横断アプリケーション ソリューションに関心がある場合は、[Microsoft Azure と Oracle Cloud Infrastructure を統合する Oracle アプリケーション ソリューション](oracle-oci-overview.md)に関する記事を参照してください。

現在利用可能なイメージの一覧を取得するには、次のコマンドを実行します。

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

2019 年 5 月の時点では、次のイメージが利用可能です。

```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Ee      Oracle       12.2.0.1                Oracle:Oracle-Database-Ee:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Ee      Oracle       18.3.0.0                Oracle:Oracle-Database-Ee:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Se      Oracle       12.2.0.1                Oracle:Oracle-Database-Se:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Se      Oracle       18.3.0.0                Oracle:Oracle-Database-Se:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Linux            Oracle       6.10                    Oracle:Oracle-Linux:6.10:6.10.20190506                       6.10.20190506
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20190506                         6.8.20190506
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20190506                         6.9.20190506
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20190506                         7.3.20190506
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.20190506                         7.4.20190506
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20181207                         7.5.20181207
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20190506                         7.5.20190506
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.20181207                         7.6.20181207
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.20190506                         7.6.20190506
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
```

これらのイメージは、"ライセンス持ち込み" と見なされるため、VM の実行によって発生するコンピューティング、ストレージ、およびネットワーキングのコストのみが課金されます。  つまり、Oracle ソフトウェアを使用するライセンスが適切に供与されていて、Oracle と現在サポート契約を結んでいることを前提としています。 Oracle では、オンプレミスから Azure へのライセンス モビリティを保証しています。 ライセンス モビリティの詳細については、公開されている [Oracle および Microsoft](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) の覚書を参照してください。 

ユーザーは、Azure で一から作成したカスタム イメージに基づくソリューションにするか、オンプレミス環境からカスタム イメージをアップロードするかを選択することもできます。

## <a name="support-for-jd-edwards"></a>JD Edwards のサポート
JD Edwards EnterpriseOne バージョン 9.2、以降は、Oracle のサポート情報 [Doc ID 2178595.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4) に従って、固有の `Minimum Technical Requirements` (MTR) を満たす**すべてのパブリック クラウド ソリューション**でサポートされています。  OS およびソフトウェア アプリケーションの互換性の MTR 仕様に準拠したカスタム イメージを作成する必要があります。 

## <a name="oracle-database-vm-images"></a>Oracle データベースの VM イメージ
Oracle では、Oracle Linux に基づく仮想マシン イメージで、Azure における Oracle DB 12.1 Standard Edition および Enterprise Edition の実行をサポートしています。  Azure で Oracle DB の実稼働ワークロードのパフォーマンスを最大にするには、VM イメージを必ず適切にサイズ変更し、Premium Storage でサポートされている Managed Disks を使用してください。 Oracle の発行済み VM イメージを使用して Azure で Oracle DB を迅速に立ち上げて実行する方法については、[Oracle DB のクイック スタート チュートリアルを試してください](oracle-database-quick-create.md)。

### <a name="attached-disk-configuration-options"></a>接続ディスクの設定オプション

接続ディスクは Azure Blob Storage サービスを利用します。 各 Standard ディスクは理論上、毎秒最大約 500 回 の入出力の操作 (IOPS) が可能です。 Premium ディスク オファリングでは、高パフォーマンスのデータベース ワークロードが優先され、ディスクあたり最大 5000 IOps を実現できます。 パフォーマンスのニーズを満たす場合は、1 つのディスクを使用できます。 ただし、複数の接続ディスクを使い、それらの間でデータベースのデータを分散させ、Oracle Automatic Storage Management (ASM) を利用すると、効果的に IOPS パフォーマンスを改善できます。 Oracle ASM に固有の情報については、[Oracle Automatic Storage の概要](https://www.oracle.com/technetwork/database/index-100339.html)に関するページを参照してください。 Linux の Azure VM で Oracle ASM をインストールおよび構成する方法の例については、[Oracle Automated Storage Management のインストールと構成](configure-oracle-asm.md)のチュートリアルを参照してください。

### <a name="shared-storage-configuration-options"></a>共有ストレージの構成オプション

Azure NetApp Files は、クラウド内のデータベースなどの高パフォーマンスのワークロードを実行するというコア要件を満たすように設計されており、次のものを提供します。
- VM ネイティブな NFS クライアントまたは Oracle dNFS のどちらかを経由して Oracle ワークロードを実行するための Azure ネイティブな共有 NFS ストレージ サービス
- 実世界のさまざまな IOPS の需要が反映されたスケーラブルなパフォーマンス レベル
- 待ち時間の短縮
- 通常は、ミッション クリティカルなエンタープライズ ワークロード (SAP や Oracle など) によって要求される、大規模な高可用性、高い持続性、および管理容易性
- 最もアグレッシブな RTO と RPO の SLA を達成するための、高速で効率的なバックアップと復旧

これらの機能は、Azure NetApp Files が Azure データ センター環境内で (Azure ネイティブなサービスとして) 実行されている NetApp® ONTAP® オール フラッシュ システムに基づいているために可能になります。 その結果、他の Azure ストレージ オプションと同様にプロビジョニングしたり、消費したりできる理想的なデータベース ストレージ テクノロジが提供されます。 Azure NetApp Files の NFS ボリュームをデプロイし、それにアクセスする方法の詳細については、[Azure NetApp Files のドキュメント](https://docs.microsoft.com/azure/azure-netapp-files/)を参照してください。 Azure NetApp Files 上で Oracle データベースを動作させるためのベスト プラクティスの推奨事項については、[Azure NetApp Files を使用した Oracle on Azure デプロイのベスト プラクティス ガイド](https://www.netapp.com/us/media/tr-4780.pdf)に関するページを参照してください。


## <a name="oracle-real-application-cluster-oracle-rac"></a>Oracle Real Application Cluster (Oracle RAC)
Oracle RAC では、オンプレミスのマルチノード クラスター構成の単一ノードで発生する障害を軽減するように設計されています。 このクラスターは、超大規模パブリック クラウド環境にネイティブではない、2 つのオンプレミスのテクノロジに依存しています。それがネットワーク マルチキャストと共有ディスクです。 お使いのデータベース ソリューションの Azure で Oracle RAC が必要な場合は、サード パーティのソフトウェアを使用してこれらのテクノロジを有効にする必要があります。 Oracle RAC の詳細については、[FlashGrid SkyCluster に関するページ](https://www.flashgrid.io/oracle-rac-in-azure/)を参照してください。

## <a name="high-availability-and-disaster-recovery-considerations"></a>高可用性とディザスター リカバリーに関する考慮
Azure で Oracle データベースを使用する場合、いかなるダウンタイムも回避するために高可用性とディザスター リカバリー ソリューションを実装する責任があります。 

Azure の Oracle Database Enterprise Edition (Oracle RAC を使用しない) では、Azure で [Data Guard, Active Data Guard](https://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html) または [Oracle GoldenGate](https://www.oracle.com/technetwork/middleware/goldengate) と 2 つの異なる仮想マシンの 2 つのデータベースを使用することで高可用性とディザスター リカバリーを実現できます。 両方の仮想マシンを同じ[仮想ネットワーク](https://azure.microsoft.com/documentation/services/virtual-network/)に置き、プライベートの固定 IP アドレスで互いにアクセスできるようにする必要があります。  さらに、Azure が仮想マシンを個別の障害ドメインやアップグレード ドメインに配置できるように、仮想マシンを同じ可用性セットに配置することをお勧めします。 地理的冗長性を実現する必要がある場合は、2 つの異なるリージョン間でレプリケートするように 2 つのデータベースをセットアップし、2 つのインスタンスを VPN Gateway で接続することができます。

[Azure での Oracle Data Guard の実装](configure-oracle-dataguard.md)に関するチュートリアルでは、Azure での基本的なセットアップ手順を説明しています。  

Oracle Data Guard では、1 つの仮想マシンにプライマリ データベース、別の仮想マシンにセカンダリ データベース (待機)、そしてその間に一方向のレプリケーションセットを配置することで高可用性を実現できます。 データベースのコピーへのアクセスを結果として読み込みます。 Oracle GoldenGate では、2 つのデータベース間に双方向レプリケーションを構成することができます。 これらのツールを使用してデータベース用に高可用性ソリューションを設定する方法については、Oracle の Web サイトにある [Active Data Guard](https://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) および [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) に関する文書をご覧ください。 データベースのコピーに読み込み/書き込みアクセスをする必要がある場合は、 [Oracle Active Data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html)もご利用いただけます。

[Azure での Oracle GoldenGate の実装](configure-oracle-golden-gate.md)に関するチュートリアルでは、Azure での基本的なセットアップ手順を説明しています。

Azure で高可用性と災害復旧ソリューションを設計することに加えて、データベースを復元するためのバックアップ戦略を立てる必要があります。 [Oracle Database のバックアップと復旧](oracle-backup-recovery.md)に関するチュートリアルでは、一貫性のあるバックアップを確立するための基本的な手順を説明しています。

## <a name="oracle-weblogic-server-virtual-machine-images"></a>Oracle WebLogic Server 仮想マシン イメージ

* **クラスタリングは Enterprise エディションでのみサポートされています。** WebLogic Server の Enterprise Edition を使用する場合にのみ、WebLogic クラスタリングを使用するライセンスが許諾されます。 WebLogic Server Standard Edition の場合、クラスタリングを使用しないでください。
* **UDP マルチキャストはサポートされていません。** Azure は UDP ユニキャストをサポートしていますが、マルチキャストとブロードキャストはサポートしていません。 WebLogic Server は Azure の UDP ユニキャスト機能に依存できます。 UDP ユニキャストへの依存で最適な結果を得るには、WebLogic クラスターのサイズを静的に保つか、管理サーバーを 10 台以下に保つことをお勧めします。
* **WebLogic Server は T3 アクセスに対し、パブリック ポートとプライベート ポートを同じにすることを要求します (Enterprise JavaBeans を使用する場合など)。** 次の多層シナリオを検討してみましょう。*SLWLS* という仮想ネットワーク上の、2 つ以上の VM で構成される WebLogic Server クラスターで、サービス層 (EJB) アプリケーションが実行されているとします。 クライアント層は同じ仮想ネットワークの別のサブネットにあり、サービス層にある EJB を呼び出すためにシンプルな Java プログラムを実行します。 サービス層の負荷を分散する必要があるため、負荷分散されたパブリックなエンドポイントを WebLogic Server クラスター内の仮想マシンに作成する必要があります。 指定したプライベート ポートがパブリックポート (7006:7008 など) と異なる場合、次のようなエラーが発生します。

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   どのようなリモート T3 アクセスにおいても、WebLogic Server に対してロード バランサー ポートおよび WebLogic 管理サーバーのポートが同じでなければいけないためです。 上記の場合、クライアントはポート 7006 (ロード バランサー ポート) へ接続し、管理サーバーは 7008 (プライベート ポート) で待機します。 この制限は、HTTP ではなく T3 アクセスにのみ適用されます。

   この問題を避けるには、次の回避策のいずれかを実施してください。

  * T3 アクセス専用の負荷分散エンドポイントに対して、同じプライベートおよびパブリック ポート番号を使用します。
  * WebLogic Server を開始する場合、次の JVM パラメーターを含めてください。

    ```
    -Dweblogic.rjvm.enableprotocolswitch=true
    ```

関連情報については、<https://support.oracle.com> にあるサポート技術情報 **860340.1** をご覧ください。

* **動的なクラスタリングと負荷分散の制限事項。** WebLogic Server で動的なクラスターを使用し、Azure 上の単一のパブリック負荷分散エンドポイントでそれを公開すると仮定します。 これは、それぞれの管理サーバーに固定のポート番号を使用し (範囲から動的に割り当てられていない)、管理者が追跡記録しているマシンより多く管理サーバーを起動しない限り、実現できます。 つまり、1 つの仮想マシンに対する管理サーバーが複数にならないようにします。 設定の結果、起動する WebLogic Server の数が仮想マシンより多くなる場合 (つまり、複数の WebLogic Server インスタンスが同じ仮想マシンを共有する場合)、指定したポート番号に複数の WebLogic Server インスタンス サーバーをバインドすることはできません。 その仮想マシン上ではその他は失敗します。

   管理サーバーに個別のポート番号を自動的に割り当てるように管理サーバーを設定した場合、負荷分散はできません。そのような設定では、単一のパブリック ポートから複数のプライベート ポートにマッピングする必要がありますが、Azure ではサポートされていないためです。
* **仮想マシン上の WebLogic Server の複数のインスタンス。** 仮想マシンが十分に大きい場合、デプロイ要件によりますが、同じ仮想マシン上で複数の WebLogic Server のインスタンスを実行することを検討できます。 たとえば、2 つのコアを持つ中程度のサイズの仮想マシンでは、WebLogic Server で 2 つのインスタンスを実行できます。 ただし、1 つの仮想マシンで複数の WebLogic Server インスタンスを実行する場合のように、単一障害点をアーキテクチャに導入することは避けることが推奨されます。 2 つ以上の仮想マシンを使うほうがより優れた手法であり、各仮想マシンは複数の WebLogic Server のインスタンスを実行できるようになります。 それでもなお、WebLogic Server の各インスタンスは同じクラスターの一部にすることができます。 ただし、現在は、Azure のロード バランサーを利用する場合、負荷を分散するサーバーを個別の仮想マシン間で配布する必要があるため、同じ仮想マシン内の WebLogic Server のデプロイによって公開されるエンドポイントの負荷を Azure で分散することはできません。

## <a name="oracle-jdk-virtual-machine-images"></a>Oracle JDK 仮想マシンのイメージ
* **JDK 6 および 7 の最新アップデート。** パブリックをサポートする Java の最新バージョン (現在は Java 8) の使用を推奨していますが、Azure には JDK 6 および JDK 7 のイメージもご利用いただけます。 これは JDK 8 へのアップグレードの準備が整っていない従来のアプリケーションのための対処としてです。 一般向けには以前の JDK イメージの一般公開は終了していますが、Microsoft と Oracle の業務提携により、Azure 提供の JDK 6 および 7 のイメージには、通常、Oracle のサポート対象顧客の選ばれたグループにのみ Oracle が提供する最近の非公開更新が含まれます。 新しいバージョンの JDK イメージは JDK 6 および 7 の更新版がリリースされた後に利用可能になります。

   JDK 6 および JDK 7 のイメージで利用可能な JDK と、そこから派生した仮想マシンおよびイメージは、Azure 内でのみ使用できます。
* **64-bit JDK.** Azure によって提供される Oracle WebLogic Server 仮想マシンおよび Oracle JDK 仮想マシンのイメージには、Windows Server と JDK 両方の 64 ビット版が含まれています。

## <a name="next-steps"></a>次の手順
Microsoft Azure 内の仮想マシン イメージに基づいた現在の Oracle ソリューションの概要を理解しました。 次の手順では、Azure で最初の Oracle データベースをデプロイします。

> [!div class="nextstepaction"]
> [Azure に Oracle データベースを作成する](oracle-database-quick-create.md)
