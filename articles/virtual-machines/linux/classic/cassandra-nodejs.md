---
title: "Azure 上の Linux での Cassandra の実行 | Microsoft Docs"
description: "Node.js アプリから Azure Virtual Machines の Linux で Cassandra クラスターを実行する方法"
services: virtual-machines-linux
documentationcenter: nodejs
author: hanuk
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 30de1f29-e97d-492f-ae34-41ec83488de0
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/22/2016
ms.author: hanuk;robmcm
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 1cc14b99a4c0dfeb9eec0afaf72200e93cd22e12
ms.lasthandoff: 03/27/2017


---
# <a name="running-cassandra-with-linux-on-azure-and-accessing-it-from-nodejs"></a>Azure 上の Linux で Cassandra を実行して Node.js からアクセス
> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。 [Datastax Enterprise](https://azure.microsoft.com/documentation/templates/datastax) および [CentOS 上の Spark クラスターと Cassandra](https://azure.microsoft.com/documentation/templates/spark-and-cassandra-on-centos/) の Resource Manager テンプレートをご覧ください。

## <a name="overview"></a>概要
Microsoft Azure は Microsoft のソフトウェアと Microsoft 以外のソフトウェアの両方を実行するオープン クラウド プラットフォームであり、実行するソフトウェアには、オペレーティング システム、アプリケーション サーバー、メッセージング ミドルウェアだけでなく、市販モデルおよびオープン ソース モデルの SQL および NoSQL のデータベースが含まれています。 Azure などのパブリック クラウド上での回復力のあるサービスの構築には、アプリケーション サーバーとストレージ レイヤーの両方の慎重な計画と熟慮されたアーキテクチャが必要です。 Cassandra の分散ストレージ アーキテクチャは、クラスターの障害へのフォールト トレランスを可能にする高可用性を備えたシステムの構築に役立ちます。 Cassandra は cassandra.apache.org で Apache Software Foundation によって管理されているクラウド スケールのNoSQL のデータベースです。Cassandra は Java で記述されており、Windows プラットフォームと Linux プラットフォームの両方で稼働します。

この記事では、Microsoft Azure Virtual Machines および Virtual Networks を利用する単一または複数のデータ センター クラスターとしての Ubuntu への Cassandra のデプロイの説明に焦点を当てています。 運用環境にワークロードを最適化するためのクラスター デプロイについては、必要なレプリケーション、データ一貫性、スループット、高可用性の要件をサポートする複数ディスク ノード構成、適切なリング トポロジ設計、データ モデリングが必要なので、この記事では取り扱いません。

基本的にはこの記事では、Docker、Chef、Puppet と比較して、インフラストラクチャのデプロイを大幅に簡略化可能な Cassandra クラスターの構築の関連事項を説明します。  

## <a name="the-deployment-models"></a>デプロイ モデル
Microsoft Azure のネットワークでは、ネットワークのセキュリティのきめ細かな設定を実現するためにアクセスを制限できる、分離プライベート クラスターのデプロイが可能です。  この記事は Cassandra のデプロイの基本を説明することを目的としているため、一貫性レベル、およびスループットに最適化したストレージ設計については焦点を当てません。 仮想クラスターのネットワーク要件の一覧を次に示します。

* 外部システムは、Azure の内部からも外部からも Cassandra データベースにアクセスできない
* Cassandra クラスターは Thrift トラフィックのロード バランサーの内側に配置する必要がある
* クラスターの可用性を強化するためにデータ センターごとに 2 つのグループに Cassandra ノードをデプロイする
* アプリケーション サーバー ファームのみがデータベースに直接アクセスできるように、クラスターをロック ダウンする
* パブリック ネットワーク エンドポイントを SSH のみにする
* 各 Cassandra ノードには、固定の内部 IP アドレスが必要である

Cassandra は、単一の Azure リージョンにデプロイすることも、ワークロードを分散するために複数のリージョンにデプロイすることもできます。 複数リージョンのデプロイ モデルは、エンド ユーザーから地理的により近い場所で、同一の Cassandra インフラストラクチャを介してサービスを提供するために利用されています。 Cassandra 組み込みのノード レプリケーション機能は、複数のデータ センターからのマルチマスターの同期書き込みを処理し、アプリケーションに一貫性のあるデータのビューを提供します。 複数リージョン デプロイは、Azure サービスの広範なシステム停止のリスクの軽減にも役立ちます。 Cassandra の一貫性制御の自由度の高さとレプリケーション トポロジは、アプリケーションの多様な RPO ニーズに対応します。

### <a name="single-region-deployment"></a>単一リージョン デプロイ
まず単一リージョン デプロイから学習し、その後、複数リージョン モデルを作成します。 Azure の仮想ネットワークは、上記で説明したネットワーク セキュリティ要件を実現するための分離サブネットの作成に使用されます。  ここでは、Ubuntu 14.04 LTS と Cassandra 2.08 を使用する場合の単一リージョン デプロイの作成のプロセスについて説明しますが、このプロセスは、Linux の他のバリエーションにも簡単に応用できます。 単一リージョン デプロイのシステムの特徴を次に示します。  

**高可用性:** 図 1 の Cassandra ノードは 2 つの可用性セットにデプロイされており、ノードが複数の障害ドメインに分散されて高可用性が確保されるようになっています。 各可用性セットが注釈として付与された VM は、2 つの障害ドメインにマップされています。  Microsoft Azure では、予期しないダウンタイム (ハードウェアやソフトウェアの障害など) の管理には障害ドメインの概念を使用し、スケジュール設定されたダウンタイム (ホスト OS またはゲスト OS への修正プログラムの適用またはアップグレード、アプリケーションのアップグレードなど) の管理にはアップグレード ドメインの概念を使用しています。 高可用性を実現する上での障害ドメインおよびアップグレード ドメインの役割については、「 [Azure アプリケーションの障害復旧と高可用性](http://msdn.microsoft.com/library/dn251004.aspx) 」を参照してください。

![単一リージョン デプロイ](./media/cassandra-nodejs/cassandra-linux1.png)

図 1: 単一リージョン デプロイ

この記事の執筆時において、Azure では、特定の障害ドメインへの VM のグループの明示的なマッピングを許可していません。したがって、図 1 に示すデプロイ モデルを使用している場合でも、多くの場合、すべての仮想マシンが 4 つの障害ドメインではなく、2 つの障害ドメインにマップされています。

**負荷分散 Thrift トラフィック:** Web サーバー内の Thrift クライアント ライブラリは、内部ロード バランサーを介してクラスターに接続します。 このため、Cassandra クラスターをホストするクラウド サービスのコンテキストでは、"data" サブネット (図 1 を参照) に内部ロード バランサーを追加するプロセスが必要になります。 内部ロード バランサーの定義後は、この定義されたロード バランサー名を持つ負荷分散セットの注釈を使用して、各ノードに、負荷分散エンドポイントを追加する必要があります。 詳細については、「 [内部負荷分散 ](../../../load-balancer/load-balancer-internal-overview.md)」を参照してください。

**クラスター シード:** 新しいノードは、クラスターのトポロジを検出するためにシード ノードと通信するため、シードには最も可用性の高いノードを選択することが重要です。 単一障害点を回避するために、可用性セットごとに 1 つのノードをシード ノードとして指定します。

**レプリケーション係数と一貫性レベル:** Cassandra に組み込まれた高い可用性とデータの持続性は、レプリケーション係数 (RF、クラスターに格納されている行ごとのコピーの数) と一貫性レベル (呼び出し元に結果を返すまでのレプリカの読み取り/書き込み数) で示されます。 レプリケーション係数は、KEYSPACE (リレーショナル データベースに似ています) の作成時に指定され、一貫性レベルは CRUD クエリの発行時に指定されます。 一貫性についての詳細とクォーラムの計算式については、Cassandra のマニュアルで、 [一貫性の設定](http://www.datastax.com/documentation/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) に関するページを参照してください。

Cassandra では、"一貫性" と "結果的な一貫性" の 2 種類のデータ一貫性モデルがサポートされています。レプリケーション係数と一貫性レベルで、書き込み操作が完了してすぐに一貫性が確保されるのか、結果的に一貫性が確保されるのかが決まります。 たとえば、一貫性レベルに、QUORUM を指定すると、常に、データの一貫性が確保されます。一方、QUORUM を確保するために必要に応じて書き込まれるレプリカの数未満の一貫性レベル (例: ONE) を指定すると、データの一貫性は結果的に確保されます。

レプリケーション係数が 3 で、読み取り/書き込みの一貫性レベルが QUORUM (一貫性を保つため読み取りや書き込みを、2 ノードで行う) の上の図の 8 ノードのクラスターでは、理論上、最大で 1 レプリケーション グループにつき 1 ノードが失われた場合でも、アプリケーションが障害の影響を受ける前に対処できます。 この場合、すべてのキースペースに対して、バランスの取れた読み取り/書き込み要求が行われることになります。  デプロイしたクラスターで使用するパラメーターを次に示します。

単一リージョン Cassandra クラスター構成:

| クラスター パラメーター | 値 | 解説 |
| --- | --- | --- |
| ノード の数 (N) |8 |クラスター内のノードの合計数 |
| レプリケーション係数 (RF) |3 |行のレプリカの数 |
| 一貫性レベル (書き込み) |QUORUM [(RF/2) +1= 2] (数式の結果の小数点以下の値は、切り捨てられます) |呼び出し元に応答が送信される前に、最大で 2 つのレプリカに書き込みます。3 番目のレプリカには、結果的に一貫性を確保する方式で書き込まれます。 |
| 一貫性レベル (読み取り) |QUORUM [(RF/2) +1= 2] (数式の結果の小数点以下の値は、切り捨てられます) |呼び出し元に応答を送信する前に、2 つのレプリカを読み取ります。 |
| レプリケーションの方法 |NetworkTopologyStrategy (詳細については、Cassandra のマニュアルの「 [Data Replication (データ レプリケーション)](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) 」を参照してください) |デプロイ トポロジを把握し、すべてのレプリカが同じラックになることがないように、ノードにレプリカを配置します。 |
| スニッチ |GossipingPropertyFileSnitch (詳細については、Cassandra マニュアルの「 [Snitches (スニッチ)](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) 」を参照してください) |NetworkTopologyStrategy を指定すると、スニッチの概念を使用してトポロジを把握します。 GossipingPropertyFileSnitch を指定すると、各ノードのデータ センターとラックへのマッピングが、より適切に制御されます。 この場合、クラスターはゴシップを使用して情報を伝達します。 このため、PropertyFileSnitch と比べて、非常に簡単に動的 IP 設定を行うことができます。 |

**Cassandra クラスターに関する Azure での考慮事項:** Microsoft Azure Virtual Machines では、ディスクの永続性を実現するために Azure Blob ストレージを使用してします。Azure Storage は、高い耐久性を確保するために、各ディスクのレプリカを 3 つ保存します。 つまり、Cassandra のテーブルに挿入されたデータの各行は、3 つのレプリカに格納されており、そのためレプリケーション係数 (RF) が 1 の場合でも、データの一貫性を確保するための処理が行われていることになります。 レプリケーション係数が 1 の場合の主な問題は、1 つの Cassandra ノードのみで障害が発生した場合でも、アプリケーションにダウンタイムが発生することです。 ただし、Azure ファブリック コントローラーによって認識される問題 (ハードウェア、システム ソフトウェアの障害など) によってノードがダウンした場合は、同じストレージ ドライブを使用して、代わりの新しいノードが準備されます。 古いノードを置き換えるための新しいノードのプロビジョニングには、数分間かかる場合があります。  同様に、ゲスト OS の変更、Cassandra のアップグレード、アプリケーションの変更といった計画済みのメンテナンス アクティビティの場合も、Azure ファブリック コントローラーは、クラスター内のノードのローリング アップグレードを実行します。  ローリング アップグレードの実行時にも、一度にいくつかのノードがダウンする場合があり、そのため、クラスターで、いくつかのパーティションのダウンタイムが短時間発生する可能性があります。 ただし、Azure Storage に冗長性が組み込まれているため、データが失われることはありません。  

Azure にデプロイされたシステムのうち高い可用性(例: 99.9 は、およそ 8.76 時間/年に相当します。詳細については、[高可用性](http://en.wikipedia.org/wiki/High_availability)に関する Wikipedia のページをご覧ください) を必要としないものに関しては、RF = 1、一貫性レベル = ONE で実行できます。  高い可用性を必要とするアプリケーションでは、RF = 3 および一貫性レベル = QUORUM を指定すると、レプリカのうちの 1 つにあるノードのうちの 1 つのダウンタイムを許容します。 従来型のデプロイ (例: オンプレミス) では、ディスク障害などでデータ損失が生じる可能性があるため、RF = 1 は使用できません。   

## <a name="multi-region-deployment"></a>複数リージョン デプロイ
上で述べたレプリケーションと一貫性モデルに対応した Cassandra のデータ センターを利用すると、外部ツールを使用することなく、複雑な設定なしで複数リージョン デプロイを実施できます。 この点は、場合によっては、マルチ マスターの書き込みのデータベース ミラーリングのセットアップが非常に複雑な従来のリレーショナル データベースとは大きく異なります。 複数リージョンにセットアップした Cassandra は、次のような使用シナリオで効果的です。

**近接性ベースのデプロイ:** テナント ユーザーとリージョンの明確なマッピングを持つマルチ テナント アプリケーションでは、複数リージョン クラスターによる待機時間の短さがメリットになることもあります。 たとえば、ある教育機関の学習管理システムでは、米国東部リージョンおよび米国西部リージョンに分散クラスターをデプロイし、各キャンパスでトランザクションと分析を利用できるようにしています。 データの一貫性は読み取り時および書き込み時にはローカルで確保され、結果的には両方のリージョンにまたがる一貫性が確保されます。 他にも、メディア配布、e コマースなど、特定の geo に集中するユーザー ベースにサービスを提供するありとあらゆる事例で、このデプロイ モデルを活用できます。

**高可用性:** 冗長性は、ソフトウェアとハードウェアの高可用性を実現するための鍵になります。詳細については、Microsoft Azure での信頼性のあるクラウド システムの構築に関するページをご覧ください。 Microsoft Azure で完全な冗長性を実現するための唯一の確実な方法は、複数のリージョンにクラスターをデプロイすることです。 アプリケーションはアクティブ/アクティブ モードまたはアクティブ/パッシブ モードでデプロイすることができ、1 つのリージョンがダウンした場合でも、Azure Traffic Manager で、稼働中のリージョンにトラフィックをリダイレクトできます。  単一リージョン デプロイで可用性が 99.9 の場合、数式 (1-(1-0.999) *(1-0.999))*100) より、2 リージョン デプロイにすると 99.9999 の可用性を実現できます。詳細については、上記の文書をご覧ください。

**障害復旧:** 複数リージョン Cassandra クラスターは、適切に設計されている場合、データ センターの致命的な停止にも耐えられます。 1 つのリージョンがダウンした場合、その他のリージョンにデプロイされたアプリケーションでエンド ユーザーへのサービスの提供を開始できます。 その他のビジネス継続性の実装と同様に、アプリケーションは、非同期パイプライン内にデータがあることによって生じるある程度のデータ損失を許容することが必要です。 ただし、Cassandra は、従来のデータベースの復旧プロセスよりも、はるかに迅速に復旧を行います。 図 2 に、各リージョンに 8 つのノードがある典型的な複数リージョンのデプロイ モデルを示します。 両方のリージョンは、左右対称の互いのミラー イメージになります。実際の設計は、ワークロードのタイプ (例: トランザクションまたは分析)、RPO、RTO、データの一貫性および可用性の要件によって異なります。

![複数リージョン デプロイ](./media/cassandra-nodejs/cassandra-linux2.png)

図 2: 複数リージョンの Cassandra デプロイ

### <a name="network-integration"></a>ネットワーク統合
2 つのリージョンのプライベート ネットワークにデプロイされた仮想マシンのセットは、VPN トンネルを使用して、互いと通信します。 VPN トンネルは、ネットワーク デプロイ プロセスでプロビジョニングされた 2 つのソフトウェア ゲートウェイを接続します。 "web" サブネットおよび "data" サブネットに関しては、両方のリージョンに、ほぼ同じネットワーク アーキテクチャが使用されています。Azure のネットワークでは、必要な数だけサブネットを作成できます。ネットワーク セキュリティ上の必要に応じて ACL を適用してください。 クラスターのトポロジの設計時には、データ センター間の通信の待機時間と、ネットワーク トラフィックの経済的影響を、考慮する必要があります。

### <a name="data-consistency-for-multi-data-center-deployment"></a>複数データ センター デプロイの場合のデータの一貫性
分散デプロイでは、クラスター トポロジのスループットと可用性への影響に注意する必要があります。 RF および一貫性レベルは、クォーラムがデータ センターすべての可用性に依存しないように設定しなければなりません。
高い一貫性が必要なシステムの場合、LOCAL_QUORUM を一貫性レベル (読み取りと書き込み) に指定すると、データがリモート データ センターに非同期で複製される一方で、ローカルの読み取りと書き込みはローカル ノードのみで行われます。  表 2 は、この記事で後ほど説明する複数リージョン クラスターの構成の詳細をまとめたものです。

**2 リージョンの Cassandra クラスター構成**

| クラスター パラメーター | 値 | 解説 |
| --- | --- | --- |
| ノード の数 (N) |8 + 8 |クラスター内のノードの合計数 |
| レプリケーション係数 (RF) |3 |行のレプリカの数 |
| 一貫性レベル (書き込み) |LOCAL_QUORUM [(sum(RF)/2) +1) = 4] (数式の結果の小数点以下の値は、切り捨てられます) |2 つのノードが、最初のデータ センターに同期的に書き込まれます。クォーラムに必要な追加の 2 つのノードは、2 番目のデータ センターに非同期的に書き込まれます。 |
| 一貫性レベル (読み取り) |LOCAL_QUORUM ((RF/2) +1) = 2 数式の結果の小数点以下の値は、切り捨てられます。 |1 つのリージョンのみで、読み取り要求に対応します。応答がクライアントに送信される前に、2 つのノードが読み取られます。 |
| レプリケーションの方法 |NetworkTopologyStrategy (詳細については、Cassandra のマニュアルの「 [Data Replication (データ レプリケーション)](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) 」を参照してください) |デプロイ トポロジを把握し、すべてのレプリカが同じラックになることがないように、ノードにレプリカを配置します。 |
| スニッチ |GossipingPropertyFileSnitch (詳細については、Cassandra マニュアルの「 [Snitches (スニッチ)](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) 」を参照してください) |NetworkTopologyStrategy を指定すると、スニッチの概念を使用してトポロジを把握します。 GossipingPropertyFileSnitch を指定すると、各ノードのデータ センターとラックへのマッピングが、より適切に制御されます。 この場合、クラスターはゴシップを使用して情報を伝達します。 このため、PropertyFileSnitch と比べて、非常に簡単に動的 IP 設定を行うことができます。 |

## <a name="the-software-configuration"></a>ソフトウェア構成
デプロイ時には、次のソフトウェア バージョンが使用されます。

<table>
<tr><th>ソフトウェア</th><th>から</th><th>バージョン</th></tr>
<tr><td>JRE    </td><td>[JRE 8](http://www.oracle.com/technetwork/java/javase/downloads/server-jre8-downloads-2133154.html) </td><td>8U5</td></tr>
<tr><td>JNA    </td><td>[JNA](https://github.com/twall/jna) </td><td> 3.2.7</td></tr>
<tr><td>Cassandra</td><td>[Apache Cassandra 2.0.8](http://www.apache.org/dist/cassandra/2.0.8/apache-cassandra-2.0.8-bin.tar.gz)</td><td> 2.0.8</td></tr>
<tr><td>Ubuntu    </td><td>[Microsoft Azure](https://azure.microsoft.com/) </td><td>14.04 LTS</td></tr>
</table>

JRE のダウンロードには、Oracle のライセンスの手動での承認が必要です。そのため、デプロイメントを簡略化するには、クラスターのデプロイメントの前段階として作成する Ubuntu テンプレート イメージに後でアップロードするために、必要なすべてのソフトウェアをあらかじめデスクトップにダウンロードしておく必要があります。

上記のソフトウェアは、ローカル コンピューター上の既知のダウンロード ディレクトリ (Windows の %TEMP%/downloads や大部分の Linux ディストリビューションや Mac の ~/Downloads など) にダウンロードします。

### <a name="create-ubuntu-vm"></a>Ubuntu VM の作成
必要なソフトウェアを含む Ubuntu イメージを作成し、複数の Cassandra ノードのプロビジョニングに、イメージを再利用できるようにします。  

#### <a name="step-1-generate-ssh-key-pair"></a>手順 1. SSH キー ペアの生成
Azure では、プロビジョニング時に PEM または DER でエンコードされた X509 公開キーが必要です。 「Azure 上の Linux における SSH の使用方法」の指示に従って公開キーと秘密キーのペアを生成します。 Windows または Linux で SSH クライアントとして putty.exe を使用する場合は、PEM でエンコードされた RSA 秘密キーを、puttygen.exe を使用して PPK 形式に変換する必要があります。手順については、上記の Web ページを参照してください。

#### <a name="step-2-create-ubuntu-template-vm"></a>手順 2. Ubuntu テンプレート VM の作成
テンプレート VM を作成するには、Azure クラシック ポータルにログインし、次の処理を行ってください。[新規]、[Compute]、[仮想マシン]、[ギャラリーから]、[UBUNTU]、[Ubuntu Server 14.04 LTS] の順にクリックしてから、右矢印をクリックします。 Linux VM の作成方法を説明したチュートリアルについては、「Linux を実行する仮想マシンの作成」を参照してください。

[仮想マシンの構成] 画面 1 で、次の情報を入力します。

<table>
<tr><th>フィールド名              </td><td>       フィールド値               </td><td>         解説                </td><tr>
<tr><td>バージョンのリリース日    </td><td> ドロップダウンから日付を選択します。</td><td></td><tr>
<tr><td>仮想マシン名    </td><td> cass-template                   </td><td> これは、VM のホスト名です。 </td><tr>
<tr><td>レベル                     </td><td> Standard                           </td><td> 既定値をそのまま使用します。              </td><tr>
<tr><td>サイズ                     </td><td> A1                              </td><td>IO ニーズに応じて VM を選択します。ここでは、既定値をそのまま使用します。 </td><tr>
<tr><td> 新しいユーザー名             </td><td> localadmin                       </td><td> "admin" は Ubuntu 12.xx 以降の予約ユーザー名です。</td><tr>
<tr><td> 認証         </td><td> チェック ボックスをオンにします                 </td><td>SSH キーによるセキュリティが必要な場合にオンにします。 </td><tr>
<tr><td> 証明書             </td><td> 公開キー証明書のファイル名 </td><td> 先ほど生成した公開キーを使用します。</td><tr>
<tr><td> 新しいパスワード    </td><td> 強力なパスワード </td><td> </td><tr>
<tr><td> パスワードの確認    </td><td> 強力なパスワード </td><td></td><tr>
</table>

[仮想マシンの構成] 画面 2 で、次の情報を入力します。

<table>
<tr><th>フィールド名             </th><th> フィールド値                       </th><th> 解説                                 </th></tr>
<tr><td> クラウド サービス    </td><td> [新しいクラウド サービスの作成]    </td><td>クラウド サービスとは、仮想マシンのようなコンテナー コンピューティング リソースのことです。</td></tr>
<tr><td> クラウド サービス DNS 名    </td><td>ubuntu-template.cloudapp.net    </td><td>マシンとは独立したロード バランサー名を指定します。</td></tr>
<tr><td> リージョン/アフィニティ グループ/仮想ネットワーク </td><td>    米国西部    </td><td> Cassandra クラスターにアクセスする Web アプリケーションのリージョンを選択します。</td></tr>
<tr><td>ストレージ アカウント </td><td>    既定値を使用します。    </td><td>既定のストレージ アカウントまたは特定のリージョンに事前に作成したストレージ アカウントを使用します。</td></tr>
<tr><td>可用性セット </td><td>    なし </td><td>    空白のままにします。</td></tr>
<tr><td>エンドポイント    </td><td>既定値を使用します。 </td><td>    既定の SSH 構成を使用します。 </td></tr>
</table>

右矢印をクリックし、画面 3 の既定値を変更せずに、[チェック] ボタンをクリックし、VM のプロビジョニング プロセスを完了します。 数分後には、"ubuntu-template" という名前の VM の状態が、[実行中] になります。

### <a name="install-the-necessary-software"></a>必要なソフトウェアのインストール
#### <a name="step-1-upload-tarballs"></a>手順 1. tarball のアップロード
先ほどダウンロードしたソフトウェアを、scp または pscp を使って、次のコマンド形式で ~/downloads ディレクトリにコピーします。

##### <a name="pscp-server-jre-8u5-linux-x64targz-localadminhk-cas-templatecloudappnethomelocaladmindownloadsserver-jre-8u5-linux-x64targz"></a>pscp server-jre-8u5-linux-x64.tar.gz localadmin@hk-cas-template.cloudapp.net:/home/localadmin/downloads/server-jre-8u5-linux-x64.tar.gz
上記のコマンドを、JRE および Cassandra ビットにも繰り返します。

#### <a name="step-2-prepare-the-directory-structure-and-extract-the-archives"></a>手順 2: ディレクトリ構造を準備し、アーカイブを抽出
VM にログインして、スーパー ユーザーとして次の bash スクリプトを使用して、ディレクトリ構造を作成し、ソフトウェアを抽出します。

    #!/bin/bash
    CASS_INSTALL_DIR="/opt/cassandra"
    JRE_INSTALL_DIR="/opt/java"
    CASS_DATA_DIR="/var/lib/cassandra"
    CASS_LOG_DIR="/var/log/cassandra"
    DOWNLOADS_DIR="~/downloads"
    JRE_TARBALL="server-jre-8u5-linux-x64.tar.gz"
    CASS_TARBALL="apache-cassandra-2.0.8-bin.tar.gz"
    SVC_USER="localadmin"

    RESET_ERROR=1
    MKDIR_ERROR=2

    reset_installation ()
    {
       rm -rf $CASS_INSTALL_DIR 2> /dev/null
       rm -rf $JRE_INSTALL_DIR 2> /dev/null
       rm -rf $CASS_DATA_DIR 2> /dev/null
       rm -rf $CASS_LOG_DIR 2> /dev/null
    }
    make_dir ()
    {
       if [ -z "$1" ]
       then
          echo "make_dir: invalid directory name"
          exit $MKDIR_ERROR
       fi

       if [ -d "$1" ]
       then
          echo "make_dir: directory already exists"
          exit $MKDIR_ERROR
       fi

       mkdir $1 2>/dev/null
       if [ $? != 0 ]
       then
          echo "directory creation failed"
          exit $MKDIR_ERROR
       fi
    }

    unzip()
    {
       if [ $# == 2 ]
       then
          tar xzf $1 -C $2
       else
          echo "archive error"
       fi

    }

    if [ -n "$1" ]
    then
       SVC_USER=$1
    fi

    reset_installation
    make_dir $CASS_INSTALL_DIR
    make_dir $JRE_INSTALL_DIR
    make_dir $CASS_DATA_DIR
    make_dir $CASS_LOG_DIR

    #unzip JRE and Cassandra
    unzip $HOME/downloads/$JRE_TARBALL $JRE_INSTALL_DIR
    unzip $HOME/downloads/$CASS_TARBALL $CASS_INSTALL_DIR

    #Change the ownership to the service credentials

    chown -R $SVC_USER:$GROUP $CASS_DATA_DIR
    chown -R $SVC_USER:$GROUP $CASS_LOG_DIR
    echo "edit /etc/profile to add JRE to the PATH"
    echo "installation is complete"


このスクリプトを vim ウィンドウに貼り付ける場合は、次のコマンドを使用して、必ずキャリッジ リターン ("\r") を削除してください。

    tr -d '\r' <infile.sh >outfile.sh

#### <a name="step-3-edit-etcprofile"></a>手順 3. etc/profile の編集
末尾に、次のスクリプトを追加します。

    JAVA_HOME=/opt/java/jdk1.8.0_05
    CASS_HOME= /opt/cassandra/apache-cassandra-2.0.8
    PATH=$PATH:$HOME/bin:$JAVA_HOME/bin:$CASS_HOME/bin
    export JAVA_HOME
    export CASS_HOME
    export PATH

#### <a name="step-4-install-jna-for-production-systems"></a>手順 4. 実稼働システムへの JNA のインストール
次のコマンド シーケンスを使用します。次のコマンドは、/usr/share.java ディレクトリに jna 3.2.7.jar と jna-platform-3.2.7.jar をインストールします。sudo apt-get install libjna-java

Cassandra のスタートアップ スクリプトがこれらの jar を見つけることができるように $CASS_HOME/lib ディレクトリにシンボリック リンクを作成します。

    ln -s /usr/share/java/jna-3.2.7.jar $CASS_HOME/lib/jna.jar

    ln -s /usr/share/java/jna-platform-3.2.7.jar $CASS_HOME/lib/jna-platform.jar

#### <a name="step-5-configure-cassandrayaml"></a>手順 5. cassandra.yaml の構成
各 VM の cassandra.yaml を編集して、すべての仮想マシンで必要な構成を反映させます。[この調整は、実際のプロビジョニング時に行います]:

<table>
<tr><th>フィールド名   </th><th> 値  </th><th>    解説 </th></tr>
<tr><td>cluster_name </td><td>    CustomerService    </td><td> デプロイを反映した名前を使用します。</td></tr>
<tr><td>listen_address    </td><td>[空白のままにします]    </td><td> 「localhost」を削除します。 </td></tr>
<tr><td>rpc_addres   </td><td>[空白のままにします]    </td><td> 「localhost」を削除します。 </td></tr>
<tr><td>seeds    </td><td>"10.1.2.4, 10.1.2.6, 10.1.2.8"    </td><td>シードとして指定されているすべての IP アドレスのリスト。</td></tr>
<tr><td>endpoint_snitch </td><td> org.apache.cassandra.locator.GossipingPropertyFileSnitch </td><td> VM のデータ センターとラックを推測するために NetworkTopologyStrategy で使用されます。</td></tr>
</table>

#### <a name="step-6-capture-the-vm-image"></a>手順 6. VM イメージのキャプチャ
ホスト名 (hk ca template.cloudapp.net) と、先ほど作成した SSH 秘密キーを使用して、仮想マシンにログインします。 コマンド ssh または putty.exe を使用してログインする詳しい手順については、「Azure 上の Linux における SSH の使用方法」を参照してください。

次の処理を順番に行い、イメージをキャプチャします。

##### <a name="1-deprovision"></a>1.プロビジョニング解除
コマンド "sudo waagent –deprovision+user" を使用して、仮想マシン インスタンス固有の情報を削除します。 イメージのキャプチャ プロセスの詳細については、「 [テンプレートとして使用するために Linux 仮想マシンをキャプチャする方法](capture-image.md) 」を参照してください。

##### <a name="2-shutdown-the-vm"></a>2: VM のシャット ダウン
仮想マシンが強調表示されていることを確認し、下部にあるコマンド バーから [シャットダウン] リンクをクリックします。

##### <a name="3-capture-the-image"></a>3: イメージのキャプチャ
仮想マシンが強調表示されていることを確認し、下部にあるコマンド バーから [キャプチャ] リンクをクリックします。 次の画面で、[イメージの名前] (例: hk-cas-2-08-ub-14-04-2014071)、イメージの説明を入力し、[チェック] マークをクリックして、キャプチャ プロセスを終了します。

これは数秒で完了し、その後、イメージ ギャラリーの [マイ イメージ] セクションでイメージが利用できるようになります。 ソース VM は、イメージが正常にキャプチャされた後に自動的に削除されます。 

## <a name="single-region-deployment-process"></a>単一リージョン デプロイ プロセス
**手順 1. Virtual Network の作成** Azure クラシック ポータルにログインし、次の表に記載の属性で Virtual Network を作成します。 プロセスの詳細な手順については、「 [Azure クラシック ポータルでのクラウド専用の Virtual Network の構成](../../../virtual-network/virtual-networks-create-vnet-classic-portal.md) 」を参照してください。      

<table>
<tr><th>VM の属性名</th><th>値</th><th>解説</th></tr>
<tr><td>Name</td><td>vnet-cass-west-us</td><td></td></tr>
<tr><td>リージョン</td><td>米国西部</td><td></td></tr>
<tr><td>DNS サーバー    </td><td>なし</td><td>DNS サーバーを使用していないため、無視します。</td></tr>
<tr><td>ポイント対サイト VPN の構成</td><td>なし</td><td> 無視します。</td></tr>
<tr><td>[サイト間 VPN の構成]</td><td>なし</td><td> 無視します。</td></tr>
<tr><td>アドレス空間</td><td>10.1.0.0/16</td><td></td></tr>    
<tr><td>開始 IP</td><td>10.1.0.0</td><td></td></tr>    
<tr><td>CIDR </td><td>/16 (65531)</td><td></td></tr>
</table>

次のサブネットを追加します。

<table>
<tr><th>名前</th><th>開始 IP</th><th>CIDR</th><th>解説</th></tr>
<tr><td>web</td><td>10.1.1.0</td><td>/24 (251)</td><td>Web ファームのサブネット</td></tr>
<tr><td>data</td><td>10.1.2.0</td><td>/24 (251)</td><td>データベース ノードのサブネット</td></tr>
</table>

データと Web のサブネットは、ネットワーク セキュリティ グループを使用して保護できます。ただし、ネットワーク セキュリティ グループについては、この記事では取り扱っていません。  

**手順 2. 仮想マシンのプロビジョニング** 先ほど作成したイメージを使用して、クラウド サーバー "hk-c-svc-west" に次の仮想マシンを作成し、次に示すように、対応する各サブネットにバインドします。

<table>
<tr><th>コンピューター名    </th><th>サブネット    </th><th>IP アドレス    </th><th>可用性セット</th><th>DC/ラック</th><th>シード?</th></tr>
<tr><td>hk-c1-west-us    </td><td>data    </td><td>10.1.2.4    </td><td>hk-c-aset-1    </td><td>dc =WESTUS rack =rack1 </td><td>はい</td></tr>
<tr><td>hk-c2-west-us    </td><td>data    </td><td>10.1.2.5    </td><td>hk-c-aset-1    </td><td>dc =WESTUS rack =rack1    </td><td>なし </td></tr>
<tr><td>hk-c3-west-us    </td><td>data    </td><td>10.1.2.6    </td><td>hk-c-aset-1    </td><td>dc =WESTUS rack =rack2    </td><td>はい</td></tr>
<tr><td>hk-c4-west-us    </td><td>data    </td><td>10.1.2.7    </td><td>hk-c-aset-1    </td><td>dc =WESTUS rack =rack2    </td><td>なし </td></tr>
<tr><td>hk-c5-west-us    </td><td>data    </td><td>10.1.2.8    </td><td>hk-c-aset-2    </td><td>dc =WESTUS rack =rack3    </td><td>はい</td></tr>
<tr><td>hk-c6-west-us    </td><td>data    </td><td>10.1.2.9    </td><td>hk-c-aset-2    </td><td>dc =WESTUS rack =rack3    </td><td>なし </td></tr>
<tr><td>hk-c7-west-us    </td><td>data    </td><td>10.1.2.10    </td><td>hk-c-aset-2    </td><td>dc =WESTUS rack =rack4    </td><td>はい</td></tr>
<tr><td>hk-c8-west-us    </td><td>data    </td><td>10.1.2.11    </td><td>hk-c-aset-2    </td><td>dc =WESTUS rack =rack4    </td><td>なし </td></tr>
<tr><td>hk-w1-west-us    </td><td>web    </td><td>10.1.1.4    </td><td>hk-w-aset-1    </td><td>                       </td><td>該当なし</td></tr>
<tr><td>hk-w2-west-us    </td><td>web    </td><td>10.1.1.5    </td><td>hk-w-aset-1    </td><td>                       </td><td>該当なし</td></tr>
</table>

上のリストの VM の作成には、次のプロセスが必要です。

1. 特定のリージョンに、空のクラウド サービスを作成します。
2. 先ほどキャプチャしたイメージから VM を作成し、先ほど作成した仮想ネットワークに接続します。すべての VM に、この手順を繰り返します。
3. クラウド サービスに内部ロード バランサーを追加し、"data" サブネットに追加します。
4. 先ほど作成した各 VM に、先ほど作成した内部ロード バランサーに接続されている負荷分散セットを使用して Thrift トラフィックの負荷分散エンドポイントを追加します。

上記のプロセスは、Azure クラシック ポータルを使用して実行できます。Windows マシン (Windows マシンにアクセスできない場合は、Azure 上の VM を使用) で、次の PowerShell スクリプトを使用して、8 台すべての VM を自動でプロビジョニングしてください。

**リスト 1: 仮想マシンをプロビジョニングするための PowerShell スクリプト**

        #Tested with Azure Powershell - November 2014
        #This powershell script deployes a number of VMs from an existing image inside an Azure region
        #Import your Azure subscription into the current Powershell session before proceeding
        #The process: 1. create Azure Storage account, 2. create virtual network, 3.create the VM template, 2. crate a list of VMs from the template

        #fundamental variables - change these to reflect your subscription
        $country="us"; $region="west"; $vnetName = "your_vnet_name";$storageAccount="your_storage_account"
        $numVMs=8;$prefix = "hk-cass";$ilbIP="your_ilb_ip"
        $subscriptionName = "Azure_subscription_name";
        $vmSize="ExtraSmall"; $imageName="your_linux_image_name"
        $ilbName="ThriftInternalLB"; $thriftEndPoint="ThriftEndPoint"

        #generated variables
        $serviceName = "$prefix-svc-$region-$country"; $azureRegion = "$region $country"

        $vmNames = @()
        for ($i=0; $i -lt $numVMs; $i++)
        {
           $vmNames+=("$prefix-vm"+($i+1) + "-$region-$country" );
        }

        #select an Azure subscription already imported into Powershell session
        Select-AzureSubscription -SubscriptionName $subscriptionName -Current
        Set-AzureSubscription -SubscriptionName $subscriptionName -CurrentStorageAccountName $storageAccount

        #create an empty cloud service
        New-AzureService -ServiceName $serviceName -Label "hkcass$region" -Location $azureRegion
        Write-Host "Created $serviceName"

        $VMList= @()   # stores the list of azure vm configuration objects
        #create the list of VMs
        foreach($vmName in $vmNames)
        {
           $VMList += New-AzureVMConfig -Name $vmName -InstanceSize ExtraSmall -ImageName $imageName |
           Add-AzureProvisioningConfig -Linux -LinuxUser "localadmin" -Password "Local123" |
           Set-AzureSubnet "data"
        }

        New-AzureVM -ServiceName $serviceName -VNetName $vnetName -VMs $VMList

        #Create internal load balancer
        Add-AzureInternalLoadBalancer -ServiceName $serviceName -InternalLoadBalancerName $ilbName -SubnetName "data" -StaticVNetIPAddress "$ilbIP"
        Write-Host "Created $ilbName"
        #Add add the thrift endpoint to the internal load balancer for all the VMs
        foreach($vmName in $vmNames)
        {
            Get-AzureVM -ServiceName $serviceName -Name $vmName |
                Add-AzureEndpoint -Name $thriftEndPoint -LBSetName "ThriftLBSet" -Protocol tcp -LocalPort 9160 -PublicPort 9160 -ProbePort 9160 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ilbName |
                Update-AzureVM

            Write-Host "created $vmName"     
        }

**手順 3: 各 VM での Cassandra の構成**

VM にログインし、次の処理を行います。

* $CASS_HOME/conf/cassandra-rackdc.properties を編集して、データ センターとラックのプロパティを指定します。
  
       dc =EASTUS, rack =rack1
* cassandra.yaml を編集して、シード ノードを次のように構成します。
  
       Seeds: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10"

**手順 4. VM の起動とクラスターのテスト**

いずれかのノード (例:hk-c1-west-us) にログインし、次のコマンドを実行してクラスターの状態を確認します。

       nodetool –h 10.1.2.4 –p 7199 status

8 ノードのクラスターについて、以下のような情報が画面に表示されます。

<table>
<tr><th>状態</th><th>Address    </th><th>Load    </th><th>トークン    </th><th>所有 </th><th>ホスト ID    </th><th>ラック</th></tr>
<tr><th>UN    </td><td>10.1.2.4     </td><td>87.81 KB    </td><td>256    </td><td>38.0%    </td><td>Guid (removed)</td><td>rack1</td></tr>
<tr><th>UN    </td><td>10.1.2.5     </td><td>41.08 KB    </td><td>256    </td><td>68.9%    </td><td>Guid (removed)</td><td>rack1</td></tr>
<tr><th>UN    </td><td>10.1.2.6     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>Guid (removed)</td><td>rack2</td></tr>
<tr><th>UN    </td><td>10.1.2.7     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>Guid (removed)</td><td>rack2</td></tr>
<tr><th>UN    </td><td>10.1.2.8     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>Guid (removed)</td><td>rack3</td></tr>
<tr><th>UN    </td><td>10.1.2.9     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>Guid (removed)</td><td>rack3</td></tr>
<tr><th>UN    </td><td>10.1.2.10     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>Guid (removed)</td><td>rack4</td></tr>
<tr><th>UN    </td><td>10.1.2.11     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>Guid (removed)</td><td>rack4</td></tr>
</table>

## <a name="test-the-single-region-cluster"></a>単一リージョン クラスターのテスト
次の手順で、クラスターをテストします。

1. Powershell コマンドの Get-azureinternalloadbalancer コマンドレットを使用して、内部ロード バランサーの IP アドレス (例: 10.1.2.101) を取得します。 コマンドの構文は、次のとおりです。Get-AzureLoadbalancer –ServiceName "hk-c-svc-west-us” [内部ロード バランサーの IP アドレスを含む詳細な情報を表示します]
2. Putty または ssh を使用して、Web ファーム VM (例: hk-w1-west-us) にログインします。
3. $CASS_HOME/bin/cqlsh 10.1.2.101 9160 を実行します。
4. 次の CQL コマンドを使用して、クラスターが動作しているかどうか確認します。
   
     CREATE KEYSPACE customers_ks WITH REPLICATION = { 'class' : 'SimpleStrategy', 'replication_factor' : 3 };   USE customers_ks;   CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);   INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');   INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');
   
     SELECT * FROM Customers;

次のような情報が画面に表示されます。

<table>
  <tr><th> customer_id </th><th> firstname </th><th> Lastname </th></tr>
  <tr><td> 1 </td><td> John </td><td> Doe </td></tr>
  <tr><td> 2 </td><td> Jane </td><td> Doe </td></tr>
</table>

手順 4. で作成したキースペースで、replication_factor を 3 にして SimpleStrategy が 使用されることに注意してください。 単一データ センター デプロイには SimpleStrategy の使用が、複数データ センター デプロイには NetworkTopologyStrategy の使用が推奨されています。 replication_factor を 3 にすると、ノード障害へのフォールト トレランスを確保できます。

## <a id="tworegion"> </a>複数リージョン デプロイ プロセス
完了した単一リージョン デプロイを利用して、同じ処理を繰り返し、2 番目のリージョンをインストールします。 単一リージョン デプロイと複数リージョン デプロイの主な違いは、リージョン間通信のための VPN トンネルのセットアップです。ここでは、最初にネットワーク インストールを実行してから、VM をプロビジョニングし、Cassandra を構成します。

### <a name="step-1-create-the-virtual-network-at-the-2nd-region"></a>手順 1. 2 つ目のリージョンでの Virtual Network の作成
Azure クラシック ポータルにログインし、次の表に記載の属性で Virtual Network を作成します。 プロセスの詳細な手順については、「 [Azure クラシック ポータルでのクラウド専用の Virtual Network の構成](../../../virtual-network/virtual-networks-create-vnet-classic-pportal.md) 」を参照してください。      

<table>
<tr><th>属性名    </th><th>値    </th><th>解説</th></tr>
<tr><td>名前    </td><td>vnet-cass-east-us</td><td></td></tr>
<tr><td>リージョン    </td><td>米国東部</td><td></td></tr>
<tr><td>DNS サーバー        </td><td></td><td>DNS サーバーを使用していないため、無視します。</td></tr>
<tr><td>ポイント対サイト VPN の構成</td><td></td><td>        無視します。</td></tr>
<tr><td>[サイト間 VPN の構成]</td><td></td><td>        無視します。</td></tr>
<tr><td>アドレス空間    </td><td>10.2.0.0/16</td><td></td></tr>
<tr><td>開始 IP    </td><td>10.2.0.0    </td><td></td></tr>
<tr><td>CIDR    </td><td>/16 (65531)</td><td></td></tr>
</table>

次のサブネットを追加します。

<table>
<tr><th>名前    </th><th>開始 IP    </th><th>CIDR    </th><th>解説</th></tr>
<tr><td>web    </td><td>10.2.1.0    </td><td>/24 (251)    </td><td>Web ファームのサブネット</td></tr>
<tr><td>data    </td><td>10.2.2.0    </td><td>/24 (251)    </td><td>データベース ノードのサブネット</td></tr>
</table>


### <a name="step-2-create-local-networks"></a>手順 2. ローカル ネットワークの作成
Azure の仮想ネットワークのローカル ネットワークは、プライベート クラウドや別の Azure リージョンなどのリモート サイトにマップされるプロキシ アドレス空間です。 このプロキシ アドレス空間は、ネットワークを正しいネットワーク送信先にルーティングするために、リモート ゲートウェイにバインドされます。 VNET 間通信を確立する方法については、「 [VNet 間の接続の構成](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) 」を参照してください。

次の情報に従って、ローカル ネットワークを 2 つ作成します。

| ネットワーク名 | VPN Gateway アドレス | アドレス空間 | 解説 |
| --- | --- | --- | --- |
| hk-lnet-map-to-east-us |23.1.1.1 |10.2.0.0/16 |ローカル ネットワークの作成時には、プレース ホルダー ゲートウェイ アドレスを指定します。 実際のゲートウェイ アドレスは、ゲートウェイを作成した後にセットされます。 アドレス空間が、それぞれのリモート VNET (ここでは、米国東部リージョンに作成された VNET) と完全に一致するかどうかを確認してください。 |
| hk-lnet-map-to-west-us |23.2.2.2 |10.1.0.0/16 |ローカル ネットワークの作成時には、プレース ホルダー ゲートウェイ アドレスを指定します。 実際のゲートウェイ アドレスは、ゲートウェイを作成した後にセットされます。 アドレス空間が、それぞれのリモート VNET (ここでは、米国西部リージョンに作成された VNET) と完全に一致するかどうかを確認してください。 |

### <a name="step-3-map-local-network-to-the-respective-vnets"></a>手順 3. "ローカル" ネットワークを各 VNET にマップ
Azure クラシック ポータルから、各 VNET を選択し、[構成] をクリックして、[ローカル ネットワークに接続する] をチェックして、次の情報にしたがってローカル ネットワークを選択します。

| Virtual Network | ローカル ネットワーク |
| --- | --- |
| hk-vnet-west-us |hk-lnet-map-to-east-us |
| hk-vnet-east-us |hk-lnet-map-to-west-us |

### <a name="step-4-create-gateways-on-vnet1-and-vnet2"></a>手順 4. VNET1 と VNET2 にゲートウェイを作成
両方の仮想ネットワークのダッシュボードで、[ゲートウェイの作成] をクリックして、VPN Gateway のプロビジョニング プロセスをトリガーします。 数分後に、各仮想ネットワークのダッシュボードに、実際のゲートウェイ アドレスが表示されます。

### <a name="step-5-update-local-networks-with-the-respective-gateway-addresses"></a>手順 5. それぞれのゲートウェイ アドレスで "ローカル" ネットワークを更新
両方のローカル ネットワークを編集して、プレースホルダー ゲートウェイ IP アドレスを先ほどプロビジョニングしたゲートウェイの実際の IP アドレスに置き換えます。 次のマッピングを使用します。

<table>
<tr><th>ローカル ネットワーク    </th><th>Virtual Network ゲートウェイ</th></tr>
<tr><td>hk-lnet-map-to-east-us </td><td>hk-vnet-west-us のゲートウェイ</td></tr>
<tr><td>hk-lnet-map-to-west-us </td><td>hk-vnet-east-us のゲートウェイ</td></tr>
</table>

### <a name="step-6-update-the-shared-key"></a>手順 6. 共有キーの更新
次の Powershell スクリプトを使用して、各 VPN Gateway の IPSec キーを更新します [両方のゲートウェイの安全キーを使用します]: Set-AzureVNetGatewayKey -VNetName hk-vnet-east-us -LocalNetworkSiteName hk-lnet-map-to-west-us -SharedKey D9E76BKK Set-AzureVNetGatewayKey -VNetName hk-vnet-west-us -LocalNetworkSiteName hk-lnet-map-to-east-us -SharedKey D9E76BKK

### <a name="step-7-establish-the-vnet-to-vnet-connection"></a>手順 7: VNET 間接続の確立
Azure クラシック ポータルで、両方の仮想ネットワークの [ダッシュボード] メニューを使用して、ゲートウェイ間接続を確立します。 下部のツールバーで、[接続] メニュー項目を使用します。 数分後に、ダッシュボードに、接続の詳細情報がグラフィックで表示されます。

### <a name="step-8-create-the-virtual-machines-in-region-2"></a>手順 8: リージョン 2 での仮想マシンの作成
リージョン 1 のデプロイで説明された手順と同じ方法で Ubuntu イメージを作成、または、イメージ VHD ファイルをリージョン 2 にある Azure ストレージ アカウントにコピーしてイメージを作成します。 このイメージを使用して、新しいクラウド サービス hk-c-svc-east-us に、次のリストの仮想マシンを作成します。

| コンピューター名 | サブネット | IP アドレス | 可用性セット | DC/ラック | シード? |
| --- | --- | --- | --- | --- | --- |
| hk-c1-east-us |data |10.2.2.4 |hk-c-aset-1 |dc =EASTUS rack =rack1 |はい |
| hk-c2-east-us |data |10.2.2.5 |hk-c-aset-1 |dc =EASTUS rack =rack1 |いいえ |
| hk-c3-east-us |data |10.2.2.6 |hk-c-aset-1 |dc =EASTUS rack =rack2 |はい |
| hk-c5-east-us |data |10.2.2.8 |hk-c-aset-2 |dc =EASTUS rack =rack3 |はい |
| hk-c6-east-us |data |10.2.2.9 |hk-c-aset-2 |dc =EASTUS rack =rack3 |いいえ |
| hk-c7-east-us |data |10.2.2.10 |hk-c-aset-2 |dc =EASTUS rack =rack4 |はい |
| hk-c8-east-us |data |10.2.2.11 |hk-c-aset-2 |dc =EASTUS rack =rack4 |いいえ |
| hk-w1-east-us |web |10.2.1.4 |hk-w-aset-1 |該当なし |該当なし |
| hk-w2-east-us |web |10.2.1.5 |hk-w-aset-1 |該当なし |該当なし |

リージョン 1 と同じ手順に従います。ただし、アドレス空間は 10.2.xxx.xxx を使用します。

### <a name="step-9-configure-cassandra-on-each-vm"></a>手順 9: 各 VM での Cassandra の構成
VM にログインし、次の処理を行います。

1. $CASS_HOME/conf/cassandra-rackdc.properties を編集して、次の形式でデータ センターとラックのプロパティを指定します。dc =EASTUS  rack =rack1
2. cassandra.yaml を編集して、シード ノードを構成します。Seeds: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10,10.2.2.4,10.2.2.6,10.2.2.8,10.2.2.10"

### <a name="step-10-start-cassandra"></a>手順 10: Cassandra の開始
各 VM にログインし、次のコマンドを実行して、バック グラウンドで Cassandra を開始します。$CASS_HOME/bin/cassandra

## <a name="test-the-multi-region-cluster"></a>複数リージョン クラスターのテスト
ここまでで Cassandra が、各 Azure リージョンに 8 ノードの計 16 ノードにデプロイされました。 これらのノードは共通のクラスター名とシード ノード構成により、同じクラスター内に存在します。 次のプロセスを使用して、クラスターをテストします。

### <a name="step-1-get-the-internal-load-balancer-ip-for-both-the-regions-using-powershell"></a>手順 1. PowerShell を使用して両方のリージョンの内部ロード バランサーの IP アドレスを取得
* Get-AzureInternalLoadbalancer -ServiceName "hk-c-svc-west-us"
* Get-AzureInternalLoadbalancer -ServiceName "hk-c-svc-east-us"  
  
    表示される IP アドレスに注意してください (例: 西部 - 10.1.2.101、東部 - 10.2.2.101)。

### <a name="step-2-execute-the-following-in-the-west-region-after-logging-into-hk-w1-west-us"></a>手順 2. hk-w1-west-us にログイン後に、西部リージョンで次の処理を実行
1. $CASS_HOME/bin/cqlsh 10.1.2.101 9160 を実行します。
2. 次の CQL コマンドを実行します。
   
     CREATE KEYSPACE customers_ks   WITH REPLICATION = { 'class' : 'NetworkToplogyStrategy', 'WESTUS' : 3, 'EASTUS' : 3};   USE customers_ks;   CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);   INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');   INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');   SELECT * FROM Customers;

次のような情報が画面に表示されます。

| customer_id | firstname | Lastname |
| --- | --- | --- |
| 1 |John |Doe |
| 2 |Jane |Doe |

### <a name="step-3-execute-the-following-in-the-east-region-after-logging-into-hk-w1-east-us"></a>手順 3. hk-w1-east-us にログイン後に、東部リージョンで次の処理を実行
1. $CASS_HOME/bin/cqlsh 10.2.2.101 9160 を実行します。
2. 次の CQL コマンドを実行します。
   
     USE customers_ks;   CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);   INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');   INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');   SELECT * FROM Customers;

西部リージョンで表示されたのと、同じ画面が表示されます。

| customer_id | firstname | Lastname |
| --- | --- | --- |
| 1 |John |Doe |
| 2 |Jane |Doe |

追加の挿入をいくつか実行し、それらが、クラスターの west-us 部分にレプリケートされることを確認します。

## <a name="test-cassandra-cluster-from-nodejs"></a>Node.js からの Cassandra クラスターのテスト
先ほど "web" 階層に作成に作成した Linux VM のうちの 1 つを使用して、単純な Node.js スクリプトを実行し、先ほど挿入したデータを読み取ります。

**手順 1. Node.js とクライアントの Cassandra のインストール**

1. Node.js と npm をインストールします。
2. npm を使用して、ノード パッケージ "cassandra-client" をインストールします。
3. 取得データの JSON 文字列を表示する次のスクリプトを、シェル プロンプトで実行します。
   
        var pooledCon = require('cassandra-client').PooledConnection;
        var ksName = "custsupport_ks";
        var cfName = "customers_cf";
        var hostList = ['internal_loadbalancer_ip:9160'];
        var ksConOptions = { hosts: hostList,
                             keyspace: ksName, use_bigints: false };
   
        function createKeyspace(callback){
           var cql = 'CREATE KEYSPACE ' + ksName + ' WITH strategy_class=SimpleStrategy AND strategy_options:replication_factor=1';
           var sysConOptions = { hosts: hostList,  
                                 keyspace: 'system', use_bigints: false };
           var con = new pooledCon(sysConOptions);
           con.execute(cql,[],function(err) {
           if (err) {
             console.log("Failed to create Keyspace: " + ksName);
             console.log(err);
           }
           else {
             console.log("Created Keyspace: " + ksName);
             callback(ksConOptions, populateCustomerData);
           }
           });
           con.shutdown();
        }
   
        function createColumnFamily(ksConOptions, callback){
          var params = ['customers_cf','custid','varint','custname',
                        'text','custaddress','text'];
          var cql = 'CREATE COLUMNFAMILY ? (? ? PRIMARY KEY,? ?, ? ?)';
        var con =  new pooledCon(ksConOptions);
          con.execute(cql,params,function(err) {
              if (err) {
                 console.log("Failed to create column family: " + params[0]);
                 console.log(err);
              }
              else {
                 console.log("Created column family: " + params[0]);
                 callback();
              }
          });
          con.shutdown();
        }
   
        //populate Data
        function populateCustomerData() {
           var params = ['John','Infinity Dr, TX', 1];
           updateCustomer(ksConOptions,params);
   
           params = ['Tom','Fermat Ln, WA', 2];
           updateCustomer(ksConOptions,params);
        }
   
        //update will also insert the record if none exists
        function updateCustomer(ksConOptions,params)
        {
          var cql = 'UPDATE customers_cf SET custname=?,custaddress=? where custid=?';
          var con = new pooledCon(ksConOptions);
          con.execute(cql,params,function(err) {
              if (err) console.log(err);
              else console.log("Inserted customer : " + params[0]);
          });
          con.shutdown();
        }
   
        //read the two rows inserted above
        function readCustomer(ksConOptions)
        {
          var cql = 'SELECT * FROM customers_cf WHERE custid IN (1,2)';
          var con = new pooledCon(ksConOptions);
          con.execute(cql,[],function(err,rows) {
              if (err)
                 console.log(err);
              else
                 for (var i=0; i<rows.length; i++)
                    console.log(JSON.stringify(rows[i]));
            });
           con.shutdown();
        }
   
        //exectue the code
        createKeyspace(createColumnFamily);
        readCustomer(ksConOptions)

## <a name="conclusion"></a>まとめ
Microsoft Azure は、この演習でもわかるように、オープン ソース ソフトウェアと Microsoft のソフトウェアの両方を実行できる柔軟性の高いプラットフォームです。 可用性の高い Cassandra クラスターは、クラスター ノードを複数の障害ドメインに分散させることで、単一のデータ センターにデプロイすることができます。 また、災害に耐えられるシステムを実現するために、Cassandra クラスターを、複数の地理的に離れている Azure リージョンにまたがってデプロイできます。 Azure と Cassandra を一緒に使用することで、今日のインターネット規模のサービスに必要な、拡張性と可用性が高く障害復旧が可能なクラウド サービスの構築が可能になります。  

## <a name="references"></a>参照
* [http://cassandra.apache.org](http://cassandra.apache.org)
* [http://www.datastax.com](http://www.datastax.com)
* [http://www.nodejs.org](http://www.nodejs.org)


