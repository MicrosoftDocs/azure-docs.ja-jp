---
title: "クラウドでの Batch ソリューションと HPC ソリューション - Azure | Microsoft Docs"
description: "Azure での Batch とハイ パフォーマンス コンピューティング (HPC とビッグ コンピューティング) のシナリオとソリューションのオプションについて説明します。"
services: batch, virtual-machines, cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: 
ms.assetid: aab5401d-2baf-4cf2-bf20-ad224de33888
ms.service: batch
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 0bab12648a31b416bd7ceca75f0f83f5da226bad
ms.lasthandoff: 03/31/2017


---
# <a name="batch-and-hpc-solutions-for-large-scale-computing-workloads"></a>大規模コンピューティング ワークロード用の Batch および HPC ソリューション

Azure では、Batch とハイ パフォーマンス コンピューティング (HPC) ("*ビッグ コンピューティング*" とも呼ばれます) 用の効率的でスケーラブルなクラウド ソリューションを提供しています。 ここでは、大規模な計算ワークロードとそれらをサポートする Azure のサービスについて説明します。説明を飛ばして、記事の後半に示されている[ソリューション シナリオ](#scenarios)を直接参照することもできます。 この記事の主な対象者は技術に関する意思決定者、IT 管理者、および独立系ソフトウェア ベンダーですが、その他の IT 担当者および開発者もこの記事を読むことでこれらのソリューションを理解できます。

組織が大規模コンピューティングに関する問題を抱えている分野としては、エンジニアリング設計と分析、イメージ レンダリング、複雑なモデリング、モンテカルロ シミュレーション、財務リスク計算などがあります。 Azure は、必要なリソース、スケール、スケジュールに関するこれらの問題を組織が解決するのを支援します。 Azure を使用すると以下のことが可能です。

* ハイブリッド ソリューションを作成し、オンプレミスの HPC クラスターを拡張して、ピーク時のワークロードをクラウドにオフロードします
* HPC クラスター ツールとワークロードを完全に Azure の中で実行します
* [Batch](https://azure.microsoft.com/documentation/services/batch/) などの管理されたスケーラブルな Azure サービスを使用して、コンピューティング インフラストラクチャをデプロイおよび管理することなく、計算量の多いワークロードを実行します

この記事で扱う範囲を超えていますが、Azure では、大規模なカスタム計算ワークフローを構築するための機能、アーキテクチャの選択肢、および開発ツールのフルセットも開発者とパートナーに提供します。 拡大するパートナー エコシステムは、Azure クラウドでビッグ コンピューティング ワークロードを生産的にすることを支援する準備ができています。

## <a name="batch-and-hpc-applications"></a>Batch および HPC アプリケーション
Web アプリケーションや多くの基幹業務アプリケーションとは異なり、Batch および HPC アプリケーションには定義された開始と終了があり、スケジュールに従って、またはオンデマンドで実行でき、場合によっては何時間もかかります。 ほとんどは 2 つの主なカテゴリ、"*本質的に並列*" (解決する問題の性質上、複数のコンピューターまたはプロセッサで並列に実行されるため「並列性がきわめて高い」とも呼ばれます) および "*密結合*" に分類されます。 これらのアプリケーションの種類の詳細については、次の表を参照してください。 一部の Azure ソリューション アプローチは、どちらか一方の種類に適しています。

> [!NOTE]
> Batch および HPC ソリューションでは、通常、アプリケーションの実行中インスタンスは "*ジョブ*" と呼ばれ、各ジョブは "*タスク*" に分かれている場合があります。 アプリケーションのクラスター化されたコンピューティング リソースは、 *コンピューティング ノード*と呼ばれることがよくあります。
> 
> 

| 種類 | 特性 | 例 |
| --- | --- | --- |
| **本質的に並列**<br/><br/>![本質的に並列][parallel] |• 個々のコンピューターがアプリケーション ロジックを独立して実行します<br/><br/> • コンピューターを追加するにより、アプリケーションを拡張して計算時間を短縮できます<br/><br/>• アプリケーションは複数の異なる実行可能ファイルで構成されるか、またはクライアントによって呼び出されるサービスのグループに分割されます (サービス指向アーキテクチャ (SOA) アプリケーション) |• 財務リスク モデリング<br/><br/>• 画像のレンダリングと画像処理<br/><br/>• メディアのエンコードとコード変換<br/><br/>• モンテカルロ シミュレーション<br/><br/>• ソフトウェアのテスト |
| **Tightly coupled**<br/><br/>![Tightly coupled][coupled] |• アプリケーションには中間結果をやりとり、または交換する計算ノードが必要です<br/><br/>• 計算ノードは、並列コンピューティング用の一般的な通信プロトコルである Message Passing Interface (MPI) を使用して通信できます<br/><br/>• アプリケーションはネットワークの遅延や帯域幅から大きな影響を受けます<br/><br/>• アプリケーションのパフォーマンスは、InfiniBand やリモート ダイレクト メモリ アクセス (RDMA) などの高速ネットワーク テクノロジを使用することで向上させることができます |• 石油とガスの貯蔵モデリング<br/><br/>• 計算流体力学などのエンジニアリング設計と分析<br/><br/>• 自動車事故や核反応などの物理的シミュレーション<br/><br/>• 気象予測 |

### <a name="considerations-for-running-batch-and-hpc-applications-in-the-cloud"></a>クラウドでの Batch および HPC アプリケーションの実行に関する考慮事項
オンプレミスの HPC クラスターで実行するように設計されている多くのアプリケーションは、Azure またはハイブリッド (クロスプレミス) 環境に簡単に移行できます。 ただし、次のようないくつかの制限事項または注意事項があります。

* **クラウド リソースの可用性** - 使用するクラウド コンピューティング リソースの種類によっては、ジョブの実行中に継続してコンピューターを利用できない可能性があります。 状態処理や進行状況のチェックポイント処理は、可能性のある一時的エラーを処理する一般的な手法であり、クラウド リソースを利用するときはいっそう必要になります。
* **データ アクセス** - NFS などのエンタープライズ クラスターで使用できる一般的なデータ アクセス手法には、クラウド内で特別な構成が必要な場合があります。 または、クラウド用に異なるデータ アクセス方法とパターンを採用することが必要になる場合もあります。
* **データの移動** - 大量のデータを処理するアプリケーションでは、クラウド ストレージとコンピューティング リソースにデータを移動するための戦略が必要です。 [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/)などの高速なクロスプレミス ネットワークが必要になる場合があります。 また、データの格納またはアクセスに対する法律、規制、またはポリシーによる制限を検討します。
* **ライセンス** - クラウドで実行するためのライセンスまたはその他の制限事項について、商用アプリケーションのベンダーに確認します。 すべてのベンダーが従量課金制ライセンスを提供しているとは限りません。 ソリューション用にクラウド内にライセンス サーバーを計画したり、オンプレミスのライセンス サーバーに接続することが必要になる場合があります。

### <a name="big-compute-or-big-data"></a>ビッグ コンピューティングかビッグ データか
ビッグ コンピューティング アプリケーションとビッグ データ アプリケーションの境界線は明確ではないことがあり、一部のアプリケーションは両方の特徴を併せ持つことがあります。 どちらにも、通常はコンピューターのクラスターで行われる大規模な計算の実行が含まれます。 ただし、ソリューションのアプローチとサポート ツールは異なる可能性があります。

• **大規模な計算** は、CPU パワーとメモリに依存するアプリケーションが関連する傾向があります (エンジニアリング シミュレーション、財務リスク モデリング、デジタル レンダリングなど)。 ビッグ コンピューティング ソリューションのインフラストラクチャには、生データの計算を実行するための専用のマルチコア プロセッサを備えたコンピューターや、コンピューターを接続するための特殊な高速ネットワーク ハードウェアが含まれることがあります。

• **ビッグ データ**は、単一のコンピューターやデータベース管理システムでは管理できない大量のデータが関係するデータ分析の問題を解決します  (大量の Web ログやその他のビジネス インテリジェンス データなど)。 ビッグ データは、CPU パワーよりディスク容量と I/O パフォーマンスに大きく依存する傾向があります。 Apache Hadoop など、クラスターを管理し、データを分割するための特殊なビッグ データ ツールもあります  (Azure HDInsight およびその他の Azure Hadoop ソリューションについては、「[Hadoop](https://azure.microsoft.com/solutions/hadoop/)」を参照してください)。

## <a name="compute-management-and-job-scheduling"></a>コンピューティングの管理とジョブのスケジューリング
通常、Batch および HPC アプリケーションの実行には、クラスター化されたコンピューティング リソースを管理し、ジョブを実行しているアプリケーションにリソースを割り当てるための、"*クラスター マネージャー*" と "*ジョブ スケジューラ*" が含まれます。 これらの機能は、個別のツール、または統合されたツールまたはサービスで実現されます。

* **クラスター マネージャー** - コンピューティング リソース (または計算ノード) をプロビジョニング、リリース、および管理します。 クラスター マネージャーは、コンピューティング ノードへのオペレーティング システム イメージおよびアプリケーションのインストールを自動化し、必要に応じてコンピューティング リソースを拡張し、ノードのパフォーマンスを監視します。
* **ジョブ スケジューラ** - アプリケーションで必要なリソース (プロセッサ、メモリなど) のほか、アプリケーションが実行する条件を指定します。 ジョブ スケジューラは、ジョブのキューを保持し、割り当てられた優先度またはその他の特性に基づいてジョブにリソースを割り当てます。

Windows ベースおよび Linux ベースのクラスター用のクラスタリングおよびジョブ スケジューリング ツールは、Azure に問題なく移行できます。 たとえば、Windows と Linux の HPC ワークロード用の Microsoft の無料のコンピューティング クラスター ソリューションである [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029)には、Azure で実行するためのさまざまなオプションがあります。 Linux クラスターを構築し、Torque や SLURM などのオープンソース ツールを実行することもできます。 [TIBCO DataSynapse GridServer](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/)、[IBM Spectrum Symphony や Symphony LSF](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/)、[Univa Grid Engine](http://www.univa.com/products/grid-engine) などの商用グリッド ソリューションを Azure に持ち込むこともできます。

次のセクションに示すように、Azure サービスを活用して、従来のクラスター管理ツールなしで (またはそれらに加えて) コンピューティング リソースの管理とジョブのスケジュールを実行できます。

## <a name="scenarios"></a>シナリオ
既存の HPC クラスター ソリューション、Azure サービス、またはこれら 2 つの組み合わせを利用して、Azure でビッグ コンピューティング ワークロードを実行する 3 つの一般的なシナリオを次に示します。 各シナリオを選択するための重要な考慮事項は示されていますが、完全ではありません。 ソリューションで利用できる Azure サービスについては、記事の後半で詳しく説明します。

| シナリオ | これを選択する理由 |
| --- | --- | --- |
| **Azure への HPC クラスターのバースト**<br/><br/>[![クラスターのバースト][burst_cluster]](./media/batch-hpc-solutions/burst_cluster.png) <br/><br/> 詳細情報:<br/>• [HPC Pack を使用した Azure ワーカー インスタンスへのバースト](https://technet.microsoft.com/library/gg481749.aspx)<br/><br/>• [HPC Pack を使用したハイブリッド コンピューティング クラスターのセットアップ](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)<br/><br/>• [HPC Pack を使用した Azure Batch へのバースト](https://technet.microsoft.com/library/mt612877.aspx)<br/><br/> |• ハイブリッド ソリューションで [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) などのオンプレミス クラスターを他の Azure リソースと結合させる。<br/><br/>• ビッグ コンピューティング ワークロードを拡張して、サービスとしてのプラットフォーム (PaaS) 仮想マシン インスタンスで実行する (現時点では Windows Server のみ)。<br/><br/>• オプションの Azure 仮想ネットワークを使用して、オンプレミスのライセンス サーバーまたはデータ ストアにアクセスする |
| **Azure 内での HPC クラスター全体の作成**<br/><br/>[![IaaS のクラスター][iaas_cluster]](./media/batch-hpc-solutions/iaas_cluster.png)<br/><br/>詳細情報:<br/>• [Azure 内の HPC クラスター ソリューション](big-compute-resources.md)<br/><br/> |• アプリケーションとクラスター ツールを Windows または Linux の標準またはカスタムのサービスとしてのインフラストラクチャ (IaaS) 仮想マシンに迅速かつ一貫性を持ってデプロイする。<br/><br/>• さまざまなビッグ コンピューティング ワークロードを、選択したジョブ スケジューリング ソリューションを使用して実行する。<br/><br/>• ネットワークやストレージを含む追加の Azure サービスを利用して、完全なクラウドベースのソリューションを作成する。 |
| **Azure への並列アプリケーションのスケールアウト**<br/><br/>[![Azure Batch][batch_proc]](./media/batch-hpc-solutions/batch_proc.png)<br/><br/>詳細情報:<br/>• [Azure Batch の基礎](batch-technical-overview.md)<br/><br/>• [.NET 向け Azure Batch ライブラリの概要](batch-dotnet-get-started.md) |• [Azure Batch](https://azure.microsoft.com/documentation/services/batch/) を使用した開発によって、さまざまなビッグ コンピューティング ワークロードをスケールアウトして、Windows 仮想マシンまたは Linux 仮想マシンのプールで実行する。<br/><br/>• Azure プラットフォーム サービスを使用して、仮想マシンのデプロイと自動スケール、ジョブのスケジューリング、障害復旧、データの移動、依存関係の管理、およびアプリケーションのデプロイを管理する。 |

## <a name="azure-services-for-big-compute"></a>大規模な計算用の Azure サービス
ここでは、ビッグ コンピューティング ソリューションとワークフローのために組み合わせることができる計算、データ、ネットワーク、関連のサービスについて詳しく説明します。 Azure サービスの詳細なガイドについては、Azure サービスの [ドキュメント](https://azure.microsoft.com/documentation/)を参照してください。 この記事の前半で挙げた [シナリオ](#scenarios) は、これらのサービスを使用する方法のほんの一部を示しています。

> [!NOTE]
> Azure には、シナリオで役立つ可能性がある新しいサービスが定期的に導入されています。 ご不明な点がある場合は、[Azure パートナー](https://pinpoint.microsoft.com/en-US/search?keyword=azure)に問い合わせるか、*bigcompute@microsoft.com* 宛てにメールをお送りください。
> 
> 

### <a name="compute-services"></a>Compute Services
Azure Compute Services はビッグ コンピューティング ソリューションの中核であり、さまざまなコンピューティング サービスによって多様なシナリオに利点を提供します。 基本的なレベルでは、これらのサービスは、Windows Server Hyper-V テクノロジを使用して Azure が提供する仮想マシン ベースのコンピューティング インスタンスでアプリケーションが実行するためのさまざまなモードを提供します。 これらのインスタンスは、標準およびカスタムの Linux および Windows オペレーティング システムとツールを実行できます。 Azure では、CPU コア、メモリ、ディスク容量などの特性の構成が異なる [さまざまなインスタンス サイズ](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) から選択できます。 ニーズに応じて何千ものコアまでインスタンスをスケールし、必要なリソースが減ったらスケールダウンできます。

> [!NOTE]
> Azure で [H シリーズなどのコンピューティング集中型インスタンス](../virtual-machines/windows/a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)を活用すれば、HPC ワークロードのパフォーマンスとスケーラビリティを改善できます。 これらのインスタンスでは、待機時間が短くスループットの高いアプリケーション ネットワークが必要とされる並列 MPI アプリケーションもサポートされています。 NVIDIA GPU と共に [N シリーズ](https://azure.microsoft.com/blog/azure-n-series-general-availability-on-december-1/) VM を使用することもできるため、Azure におけるコンピューティングと視覚化のシナリオの幅が広がります。  
> 
> 

| サービス | Description |
| --- | --- |
| **[Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)**<br/><br/> |• Microsoft Hyper-V テクノロジを使用してコンピューティングのサービスとしてのインフラストラクチャ (IaaS) を提供します<br/><br/>• 標準の Windows Server イメージや標準の Linux イメージ、[Azure Marketplace](https://azure.microsoft.com/marketplace/)、またはユーザー提供のイメージとデータ ディスクから、永続的なクラウド コンピューターを柔軟にプロビジョニングして管理できます<br/><br/>• [VM Scale Sets](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/) としてデプロイおよび管理することで、まったく同じ仮想マシンから大規模なサービスを構築し、自動スケールによって容量を自動的に増減できます<br/><br/>• オンプレミスのコンピューティング クラスター ツールおよびアプリケーションを完全にクラウドで実行します<br/><br/> |
| **[Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/)**<br/><br/> |• worker ロール インスタンスで大規模な計算アプリケーションを実行できます。このインスタンスは、あるバージョンの Windows Server を実行する仮想マシンであり、Azure によって完全に管理されます<br/><br/>• 管理オーバーヘッドが少なく、サービスとしてのプラットフォーム (PaaS) モデルで実行する、拡張性と信頼性の高いアプリケーションを可能にします<br/><br/>• オンプレミスの HPC クラスター ソリューションと統合するには、追加のツールまたは開発が必要になる場合があります |
| **[Batch](https://azure.microsoft.com/documentation/services/batch/)**<br/><br/> |• 大規模な並列および Batch ワークロードを、完全に管理されたサービスで実行します<br/><br/>• 仮想マシンの管理されたプールのジョブ スケジューリングと自動スケールを提供します<br/><br/>• 開発者は、サービスとしてのアプリケーションまたはクラウド対応の既存アプリケーションを構築して実行できます<br/> |

### <a name="storage-services"></a>ストレージ サービス
ビッグ コンピューティング ソリューションは、通常、一連の入力データを処理して、結果のデータを生成します。 ビッグ コンピューティング ソリューションで使用される Azure ストレージ サービスには次のようなものがあります。

* [BLOB、テーブル、キュー ストレージ](https://azure.microsoft.com/documentation/services/storage/) - ワークフローと通信のために大量の非構造化データ、NoSQL データ、およびメッセージを管理します。 たとえば、大きな技術的データ セットや、アプリケーションが処理する入力イメージまたはメディア ファイルには BLOB ストレージを使用する場合があります。 ソリューションでの非同期通信にはキューを使用する場合があります。 「 [Microsoft Azure Storage の概要](../storage/storage-introduction.md)」を参照してください。
* [Azure File Storage](https://azure.microsoft.com/services/storage/files/) - 一部の HPC クラスター ソリューションに必要な標準の SMB プロトコルを使用して Azure 内の一般的なファイルとデータを共有します。
* [Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) - クラウド向けのハイパースケール Apache Hadoop 分散ファイル システムで、バッチ分析、リアルタイム分析、インタラクティブ分析で高い利便性を発揮します。

### <a name="data-and-analysis-services"></a>データおよび分析サービス
一部の大規模な計算シナリオでは、大規模なデータのフローが含まれるか、またはさらに処理や分析が必要なデータが生成されます。 Azure には、いくつかのデータ サービスと分析サービスが用意されています。たとえば、次のものがあります。

* [Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) - オンプレミス、クラウドベース、およびインターネットのデータ ストアから取得したデータを結合、集計、および変換するデータ主導のワークフロー (パイプライン) を構築します。
* [SQL Database](https://azure.microsoft.com/documentation/services/sql-database/) - 管理対象サービスで Microsoft SQL Server リレーショナル データベース管理システムの主な機能を提供します。
* [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/) - Windows Server または Linux ベースの Apache Hadoop クラスターをクラウドにデプロイしてプロビジョニングし、ビッグ データの管理、分析、レポート生成を行います。
* [Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) - 完全に管理されたサービスでの予測分析ソリューションの作成、テスト、運用、管理に役立ちます。

### <a name="additional-services"></a>その他のサービス
ビッグ コンピューティング ソリューションでは、オンプレミスまたは他の環境のリソースに接続するために、他の Azure サービスが必要になる場合があります。 たとえば、次のようになります。

* [Virtual Network](https://azure.microsoft.com/documentation/services/virtual-network/) - 論理的に分離されたセクションを Azure 内に作成し、Azure リソースどうしを相互に接続したり、Azure リソースをオンプレミスのデータ センターに接続したりします。 クロスプレミス仮想ネットワークを利用することで、ビッグ コンピューティング アプリケーションは、オンプレミスのデータ、Active Directory サービス、ライセンス サーバーにアクセスできます。
* [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) - Microsoft のデータ センターとオンプレミスや共用環境にあるインフラストラクチャの間でプライベート接続を作成します。 ExpressRoute では、一般的なインターネットでの接続よりもセキュリティと信頼性が高く、高速で待機時間の少ない接続が提供されます。
* [Service Bus](https://azure.microsoft.com/documentation/services/service-bus/) - Azure または別のクラウド プラットフォームやデータ センターに存在するアプリケーションに、通信またはデータ交換のためのいくつかのメカニズムを提供します。

## <a name="next-steps"></a>次のステップ
* ソリューションを構築するためのテクニカル ガイダンスについては、 [Batch とハイ パフォーマンス コンピューティング (HPC) に関するテクニカル リソース](big-compute-resources.md) に関する記事を参照してください。
* Cycle Computing、Rescale、UberCloud などのパートナーと Azure のオプションを検討します。
* [Towers Watson](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18222)、[Altair](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/)、[ANSYS](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/)、[d3VIEW](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=22088) の Azure ビッグ コンピューティング ソリューションに関する記事を読みます。
* 最新情報については、[Microsoft HPC と Batch のチーム ブログ](http://blogs.technet.com/b/windowshpc/)と[Azure ブログ](https://azure.microsoft.com/blog/tag/hpc/)をご覧ください。

<!--Image references-->
[parallel]: ./media/batch-hpc-solutions/parallel.png
[coupled]: ./media/batch-hpc-solutions/coupled.png
[iaas_cluster]: ./media/batch-hpc-solutions/iaas_cluster.png
[burst_cluster]: ./media/batch-hpc-solutions/burst_cluster.png
[batch_proc]: ./media/batch-hpc-solutions/batch_proc.png

