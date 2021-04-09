---
title: Azure 仮想マシンでの Oracle ソリューション | Microsoft Docs
description: Microsoft Azure でサポートされている Oracle 仮想マシン イメージの構成と制限事項について説明します。
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 05/12/2020
ms.author: kegorman
ms.openlocfilehash: 2f34e0bb3c4abcf4efba807f95decd798bbc1f86
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101669066"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Microsoft Azure での Oracle VM イメージとそのデプロイ

この記事では、Azure Marketplace で Oracle によって発行された仮想マシン イメージに基づく Oracle ソリューションについての情報を扱います。 Oracle Cloud Infrastructure を使用したクラウド横断アプリケーション ソリューションに関心がある場合は、[Microsoft Azure と Oracle Cloud Infrastructure を統合する Oracle アプリケーション ソリューション](oracle-oci-overview.md)に関する記事を参照してください。

現在利用可能なイメージの一覧を取得するには、次のコマンドを実行します。

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

2020 年 6 月の時点では、次のイメージが利用可能です。

```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
oracle-database-19-3    Oracle       oracle-db-19300         Oracle:oracle-database-19-3:oracle-db-19300:19.3.0           19.3.0
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Ee      Oracle       12.2.0.1                Oracle:Oracle-Database-Ee:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Ee      Oracle       18.3.0.0                Oracle:Oracle-Database-Ee:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Se      Oracle       12.2.0.1                Oracle:Oracle-Database-Se:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Se      Oracle       18.3.0.0                Oracle:Oracle-Database-Se:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Linux            Oracle       6.10                    Oracle:Oracle-Linux:6.10:6.10.00                             6.10.00
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.0                                6.8.0
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20190529                         6.8.20190529
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.0                                6.9.0
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20190529                         6.9.20190529
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.0                                7.3.0
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20190529                         7.3.20190529
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.1                                7.4.1
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.20190529                         7.4.20190529
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.1                                7.5.1
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.2                                7.5.2
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20181207                         7.5.20181207
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20190529                         7.5.20190529
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.2                                7.6.2
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.3                                7.6.3
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.4                                7.6.4
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.1                                 7.7.1
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.2                                 7.7.2
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.3                                 7.7.3
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.4                                 7.7.4
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.5                                 7.7.5
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.01                             7.7.01
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.02                             7.7.02
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.03                             7.7.03
Oracle-Linux            Oracle       78                      Oracle:Oracle-Linux:78:7.8.01                                7.8.01
Oracle-Linux            Oracle       8                       Oracle:Oracle-Linux:8:8.0.2                                  8.0.2
Oracle-Linux            Oracle       8-ci                    Oracle:Oracle-Linux:8-ci:8.0.11                              8.0.11
Oracle-Linux            Oracle       81                      Oracle:Oracle-Linux:81:8.1.0                                 8.1.0
Oracle-Linux            Oracle       81                      Oracle:Oracle-Linux:81:8.1.2                                 8.1.2
Oracle-Linux            Oracle       81-ci                   Oracle:Oracle-Linux:81-ci:8.1.0                              8.1.0
Oracle-Linux            Oracle       81-gen2                 Oracle:Oracle-Linux:81-gen2:8.1.11                           8.1.11
Oracle-Linux            Oracle       ol77-ci-gen2            Oracle:Oracle-Linux:ol77-ci-gen2:7.7.1                       7.7.1
Oracle-Linux            Oracle       ol77-gen2               Oracle:Oracle-Linux:ol77-gen2:7.7.01                         7.7.01
Oracle-Linux            Oracle       ol77-gen2               Oracle:Oracle-Linux:ol77-gen2:7.7.02                         7.7.02
Oracle-Linux            Oracle       ol78-gen2               Oracle:Oracle-Linux:ol78-gen2:7.8.11                         7.8.11
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
weblogic-122130-jdk8u3  Oracle       owls-122130-8u131-ol73  Oracle:weblogic-122130-jdk8u131-ol73:owls-122130-8u131-ol7   1.1.6
weblogic-122130-jdk8u4  Oracle       owls-122130-8u131-ol74  Oracle:weblogic-122130-jdk8u131-ol74:owls-122130-8u131-ol7   1.1.1
weblogic-122140-jdk8u6  Oracle       owls-122140-8u251-ol76  Oracle:weblogic-122140-jdk8u251-ol76:owls-122140-8u251-ol7   1.1.1
weblogic-141100-jdk116  Oracle       owls-141100-11_07-ol76  Oracle:weblogic-141100-jdk11_07-ol76:owls-141100-11_07-ol7   1.1.1
weblogic-141100-jdk8u6  Oracle       owls-141100-8u251-ol76  Oracle:weblogic-141100-jdk8u251-ol76:owls-141100-8u251-ol7   1.1.1
```

これらのイメージは、"ライセンス持ち込み" と見なされるため、VM の実行によって発生するコンピューティング、ストレージ、およびネットワーキングのコストのみが課金されます。  つまり、Oracle ソフトウェアを使用するライセンスが適切に供与されていて、Oracle と現在サポート契約を結んでいることを前提としています。 Oracle では、オンプレミスから Azure へのライセンス モビリティを保証しています。 ライセンス モビリティの詳細については、公開されている [Oracle および Microsoft](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) の覚書を参照してください。

ユーザーは、Azure で一から作成したカスタム イメージに基づくソリューションにするか、オンプレミス環境からカスタム イメージをアップロードするかを選択することもできます。

## <a name="oracle-database-vm-images"></a>Oracle データベースの VM イメージ

Oracle では、Oracle Linux ベースの仮想マシンイメージ上で、Azure での Oracle Database 12.1 以降の Standard および Enterprise エディションの実行をサポートしています。  Azure 上で Oracle DB の実稼働ワークロードのパフォーマンスを最大にするには、VM イメージのサイズを必ず適切に設定し、Premium SSD または Ultra SSD Managed Disks を使用してください。 Oracle で公開されている VM イメージを使用して Azure で Oracle DB を迅速に立ち上げて実行する方法については、「[Oracle DB のクイック スタート チュートリアル](oracle-database-quick-create.md)」をご覧ください。

### <a name="attached-disk-configuration-options"></a>接続ディスクの設定オプション

接続ディスクは Azure Blob Storage サービスを利用します。 各 Standard ディスクは理論上、毎秒最大約 500 回 の入出力の操作 (IOPS) が可能です。 Premium ディスク オファリングでは、高パフォーマンスのデータベース ワークロードが優先され、ディスクあたり最大 5000 IOps を実現できます。 パフォーマンスのニーズを満たす場合は、1 つのディスクを使用できます。 ただし、複数の接続ディスクを使い、それらの間でデータベースのデータを分散させ、Oracle Automatic Storage Management (ASM) を利用すると、効果的に IOPS パフォーマンスを改善できます。 Oracle ASM に固有の情報については、[Oracle Automatic Storage の概要](https://www.oracle.com/technetwork/database/index-100339.html)に関するページを参照してください。 Linux の Azure VM で Oracle ASM をインストールおよび構成する方法の例については、[Oracle Automated Storage Management のインストールと構成](configure-oracle-asm.md)のチュートリアルを参照してください。

### <a name="shared-storage-configuration-options"></a>共有ストレージの構成オプション

Azure NetApp Files は、クラウド内のデータベースなどの高パフォーマンスのワークロードを実行するというコア要件を満たすように設計されており、次のものを提供します。

- VM ネイティブな NFS クライアントまたは Oracle dNFS のどちらかを経由して Oracle ワークロードを実行するための Azure ネイティブな共有 NFS ストレージ サービス
- 実世界のさまざまな IOPS の需要が反映されたスケーラブルなパフォーマンス レベル
- 待ち時間の短縮
- 通常は、ミッション クリティカルなエンタープライズ ワークロード (SAP や Oracle など) によって要求される、大規模な高可用性、高い持続性、および管理容易性
- 最もアグレッシブな RTO と RPO の SLA を達成するための、高速で効率的なバックアップと復旧

これらの機能は、Azure NetApp Files が Azure データ センター環境内で (Azure ネイティブなサービスとして) 実行されている NetApp® ONTAP® オール フラッシュ システムに基づいているために可能になります。 その結果、他の Azure ストレージ オプションと同様にプロビジョニングしたり、消費したりできる理想的なデータベース ストレージ テクノロジが提供されます。 Azure NetApp Files の NFS ボリュームをデプロイし、それにアクセスする方法の詳細については、[Azure NetApp Files のドキュメント](../../../azure-netapp-files/index.yml)を参照してください。 Azure NetApp Files 上で Oracle データベースを動作させるためのベスト プラクティスの推奨事項については、[Azure NetApp Files を使用した Oracle on Azure デプロイのベスト プラクティス ガイド](https://www.netapp.com/us/media/tr-4780.pdf)に関するページを参照してください。

## <a name="licensing-oracle-database--software-on-azure"></a>Azure での Oracle Database およびソフトウェアのライセンス契約

Microsoft Azure は、Oracle Database を実行するための承認されたクラウド環境です。 Oracle Core Factor テーブルは、クラウドで Oracle Database をライセンス契約する場合には適用されません。 代わりに、Enterprise Edition のデータベースでハイパースレッディング テクノロジが有効になっている VM を使用するときは、(ポリシー ドキュメントに記載されているように) ハイパースレッディングが有効になっている場合は、1 つの Oracle プロセッサ ライセンスと同等の 2 つの vCPU をカウントします。 ポリシーの詳細については、[こちら](http://www.oracle.com/us/corporate/pricing/cloud-licensing-070579.pdf)をご覧ください。
一般に、Oracle Database には、より高いメモリと IO が必要です。 このため、これらのワークロードには、[メモリ最適化済み VM](../../sizes-memory.md) を使用することをお勧めします。 ワークロードをさらに最適化するには、Oracle Database のワークロード対応の[制約付きコア vCPU](../../constrained-vcpu.md) を使用するよう推奨します。これは、高メモリ、ストレージ、I / O帯域幅が必要ですが、コア数を多く必要としません。

Oracle ソフトウェアとワークロードをオンプレミスから Microsoft Azure に移行する場合、Oracle から、「[Oracle on Azure FAQ](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)」 (Azure での Oracle に関する FAQ) に記載されているライセンス モビリティが提供されています

## <a name="high-availability-and-disaster-recovery-considerations"></a>高可用性とディザスター リカバリーに関する考慮

Azure で Oracle データベースを使用する場合、いかなるダウンタイムも回避するために高可用性とディザスター リカバリー ソリューションを実装する責任があります。

Azure の Oracle Database Enterprise Edition (Oracle RAC を使用しない) では、Azure で [Data Guard, Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html) または [Oracle GoldenGate](https://www.oracle.com/technetwork/middleware/goldengate) と 2 つの異なる仮想マシンの 2 つのデータベースを使用することで高可用性とディザスター リカバリーを実現できます。 両方の仮想マシンを同じ[仮想ネットワーク](https://azure.microsoft.com/documentation/services/virtual-network/)に置き、プライベートの固定 IP アドレスで互いにアクセスできるようにする必要があります。  さらに、Azure が仮想マシンを個別の障害ドメインやアップグレード ドメインに配置できるように、仮想マシンを同じ可用性セットに配置することをお勧めします。 地理的冗長性を実現する必要がある場合は、2 つの異なるリージョン間でレプリケートするように 2 つのデータベースをセットアップし、2 つのインスタンスを VPN Gateway で接続することができます。

[Azure での Oracle Data Guard の実装](configure-oracle-dataguard.md)に関するチュートリアルでは、Azure での基本的なセットアップ手順を説明しています。  

Oracle Data Guard では、1 つの仮想マシンにプライマリ データベース、別の仮想マシンにセカンダリ データベース (待機)、そしてその間に一方向のレプリケーションセットを配置することで高可用性を実現できます。 データベースのコピーへのアクセスを結果として読み込みます。 Oracle GoldenGate では、2 つのデータベース間に双方向レプリケーションを構成することができます。 これらのツールを使用してデータベース用に高可用性ソリューションを設定する方法については、Oracle の Web サイトにある [Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html) および [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) に関する文書をご覧ください。 データベースのコピーに読み込み/書き込みアクセスをする必要がある場合は、 [Oracle Active Data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html)もご利用いただけます。

[Azure での Oracle GoldenGate の実装](configure-oracle-golden-gate.md)に関するチュートリアルでは、Azure での基本的なセットアップ手順を説明しています。

Azure で高可用性と災害復旧ソリューションを設計することに加えて、データベースを復元するためのバックアップ戦略を立てる必要があります。 [Oracle Database のバックアップと復旧](./oracle-overview.md)に関するチュートリアルでは、一貫性のあるバックアップを確立するための基本的な手順を説明しています。

## <a name="support-for-jd-edwards"></a>JD Edwards のサポート

JD Edwards EnterpriseOne バージョン 9.2、以降は、Oracle のサポート情報 [Doc ID 2178595.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4) に従って、固有の `Minimum Technical Requirements` (MTR) を満たす **すべてのパブリック クラウド ソリューション** でサポートされています。  OS およびソフトウェア アプリケーションの互換性の MTR 仕様に準拠したカスタム イメージを作成する必要があります。

## <a name="oracle-weblogic-server-virtual-machine-offers"></a>Oracle WebLogic Server 仮想マシン プラン

Oracle と Microsoft は、Azure アプリケーション プランのコレクションの形式で WebLogic Server を Azure Marketplace に提供するために共同作業を行っています。  これらのプランについては、「[Oracle WebLogic Server Azure アプリケーション](oracle-weblogic.md)」で説明されています。

### <a name="oracle-weblogic-server-virtual-machine-images"></a>Oracle WebLogic Server 仮想マシン イメージ

- **クラスタリングは Enterprise エディションでのみサポートされています。** Oracle WebLogic Server の Enterprise Edition を使用する場合にのみ、WebLogic クラスタリングを使用するライセンスが許諾されます。 Oracle WebLogic Server Standard Edition の場合、クラスタリングを使用しないでください。
- **UDP マルチキャストはサポートされていません。** Azure は UDP ユニキャストをサポートしていますが、マルチキャストとブロードキャストはサポートしていません。 Oracle WebLogic Server は、Azure UDP ユニキャスト機能に依存できます。 UDP ユニキャストへの依存で最適な結果を得るには、WebLogic クラスターのサイズを静的に保つか、管理サーバーを 10 台以下に保つことをお勧めします。
- **Oracle WebLogic Server は、パブリック ポートとプライベート ポートが T3 アクセスで同じであると想定されています (たとえば、Enterprise JavaBeans を使用する場合)。** *SLWLS* という名前の仮想ネットワーク内で、サービスレイヤー（EJB）アプリケーションが、2 つ以上の VM で構成される Oracle WebLogic Server クラスター上で実行されている、多層シナリオを考えてみましょう。 クライアント層は同じ仮想ネットワークの別のサブネットにあり、サービス層にある EJB を呼び出すためにシンプルな Java プログラムを実行します。 サービス層の負荷を分散する必要があるため、負荷分散されたパブリック エンド ポイントを Oracle WebLogic Server クラスター内の仮想マシンに作成する必要があります。 指定したプライベート ポートがパブリックポート (7006:7008 など) と異なる場合、次のようなエラーが発生します。

```bash
   [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

   Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]
```

   これは、どのリモート T3 アクセスの場合でも、Oracle WebLogic Server は、ロード バランサー ポートと WebLogic 管理サーバーのポートが同じであると想定しているためです。 上記の場合、クライアントはポート 7006 (ロード バランサー ポート) へ接続し、管理サーバーは 7008 (プライベート ポート) で待機します。 この制限は、HTTP ではなく T3 アクセスにのみ適用されます。

   この問題を避けるには、次の回避策のいずれかを実施してください。

- T3 アクセス専用の負荷分散エンドポイントに対して、同じプライベートおよびパブリック ポート番号を使用します。
- Oracle WebLogic Server の起動時に、次の JVM パラメータを含めてください。

```bash
   -Dweblogic.rjvm.enableprotocolswitch=true
```

関連情報については、<https://support.oracle.com> にあるサポート技術情報 **860340.1** をご覧ください。

- **動的なクラスタリングと負荷分散の制限事項。** Oracle WebLogic Server で動的クラスターを使用し、Azure 上の単一のパブリック負荷分散エンドポイントを介して、それを公開すると仮定します。 これは、それぞれの管理サーバーに固定のポート番号を使用し (範囲から動的に割り当てられていない)、管理者が追跡記録しているマシンより多く管理サーバーを起動しない限り、実現できます。 つまり、1 つの仮想マシンに対する管理サーバーが複数にならないようにします。 設定の結果、起動する Oracle WebLogic Server の数が仮想マシンより多くなる場合 (つまり、複数の Oracle WebLogic Server インスタンスが同じ仮想マシンを共有する場合)、指定したポート番号に複数の Oracle WebLogic Server インスタンス サーバーをバインドすることはできません。 その仮想マシン上ではその他は失敗します。

   管理サーバーに個別のポート番号を自動的に割り当てるように管理サーバーを設定した場合、負荷分散はできません。そのような設定では、単一のパブリック ポートから複数のプライベート ポートにマッピングする必要がありますが、Azure ではサポートされていないためです。
- **仮想マシン上の Oracle WebLogic Server の複数のインスタンス。** デプロイ要件によりますが、仮想マシンが十分に大きい場合、同じ仮想マシン上で複数の Oracle WebLogic Server のインスタンスを実行することを検討できます。 たとえば、2 つのコアを持つ中規模のサイズの仮想マシンでは、 Oracle WebLogic Server で 2 つのインスタンスを実行できます。 ただし、1 つの仮想マシンで複数の Oracle WebLogic Server インスタンスを実行する場合のように、単一障害点をアーキテクチャに導入することは避けることが推奨されます。 2 つ以上の仮想マシンを使うほうがより優れた手法であり、各仮想マシンは複数の Oracle WebLogic Server のインスタンスを実行できるようになります。 それでもなお、Oracle WebLogic Server の各インスタンスは同じクラスターの一部にすることができます。 ただし、今のところ、Azure のロード バランサーを利用する場合、負荷を分散するサーバーを個別の仮想マシン間で配布する必要があるため、同じ仮想マシン内の Oracle WebLogic Server のデプロイによって公開されるエンドポイントの負荷を Azure で分散することはできません。

## <a name="oracle-jdk-virtual-machine-images"></a>Oracle JDK 仮想マシンのイメージ

- **JDK 6 および 7 の最新アップデート。** パブリックをサポートする Java の最新バージョン (現在は Java 8) の使用を推奨していますが、Azure には JDK 6 および JDK 7 のイメージもご利用いただけます。 これは JDK 8 へのアップグレードの準備が整っていない従来のアプリケーションのための対処としてです。 一般向けには以前の JDK イメージの一般公開は終了していますが、Microsoft と Oracle の業務提携により、Azure 提供の JDK 6 および 7 のイメージには、通常、Oracle のサポート対象顧客の選ばれたグループにのみ Oracle が提供する最近の非公開更新が含まれます。 新しいバージョンの JDK イメージは JDK 6 および 7 の更新版がリリースされた後に利用可能になります。

   JDK 6 および JDK 7 のイメージで利用可能な JDK と、そこから派生した仮想マシンおよびイメージは、Azure 内でのみ使用できます。
- **64-bit JDK.** Azure によって提供される Oracle WebLogic Server 仮想マシンおよび Oracle JDK 仮想マシンのイメージには、Windows Server と JDK 両方の 64 ビット版が含まれています。

## <a name="next-steps"></a>次のステップ

Microsoft Azure 内の仮想マシン イメージに基づいた現在の Oracle ソリューションの概要を理解しました。 次の手順では、Azure で最初の Oracle データベースをデプロイします。

> [!div class="nextstepaction"]
> [Azure に Oracle データベースを作成する](oracle-database-quick-create.md)
