---
title: "Azure クラウドのバッチと HPC のリソース | Microsoft Docs"
description: "Azure で大規模並列、Batch、高性能コンピューティング (HPC) ワークロードを実行するのに役立つテクニカル リソースの一覧を示します。"
services: batch, cloud-services, virtual-machines
documentationcenter: 
author: dlepow
manager: timlt
editor: 
ms.assetid: 6f8be911-c841-41ae-88d3-3bcfc029eb7f
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: big-compute
ms.date: 03/17/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 671d5c3e88585d7874dbfb0b03adc3cd9374ceca
ms.lasthandoff: 03/27/2017


---
# <a name="big-compute-in-azure-technical-resources-for-batch-and-high-performance-computing"></a>Azure における大規模なコンピューティング: Batch とハイパフォーマンス コンピューティング (HPC) に関するテクニカル リソース
この記事では、Azure で大規模並列、Batch、ハイパフォーマンス コンピューティング (HPC) の各ワークロードを実行する際に役立つテクニカル リソースを紹介します。 既存の Batch または HPC ワークロードを Azure クラウドに拡張するか、さまざまな Azure サービスを使用して新しい大規模なコンピューティング ソリューションを構築します。

## <a name="solutions-options"></a>ソリューションのオプション
Azure の大規模な計算オプションについて説明し、ワークロードやビジネス ニーズに適切なアプローチを選択します。

* [Batch と HPC ソリューション](batch-hpc-solutions.md)
* [ビデオ: Azure と HPC を使用したクラウドでの大規模なコンピューティング](https://azure.microsoft.com/documentation/videos/teched-europe-2014-big-compute-in-the-cloud-with-high-performance-computing-on-azure/)

## <a name="azure-batch"></a>Azure Batch
[Batch](https://azure.microsoft.com/services/batch/) は、Linux および Windows アプリケーションを簡単にクラウド対応にすることができ、クラスターやジョブ スケジューラーをセットアップして管理しなくてもジョブを実行できるプラットフォーム サービスです。 SDK を使用して、各種言語でクライアント アプリケーションを Azure Batch と統合し、Azure にデータをステージングして、ジョブ実行パイプラインを作成します。

* [ドキュメント](https://azure.microsoft.com/documentation/services/batch/)
* [.NET](https://msdn.microsoft.com/library/azure/mt348682.aspx)、[Python](http://azure-sdk-for-python.readthedocs.io/latest/)、[Node.js](http://azure.github.io/azure-sdk-for-node/azure-batch/latest/)、[Java](http://azure.github.io/azure-sdk-for-java/)、および [REST](https://msdn.microsoft.com/library/azure/dn820158.aspx) API リファレンス
* [Batch Management .NET ライブラリ](https://msdn.microsoft.com/library/mt463120.aspx) リファレンス
* チュートリアル: [.NET 向け Azure Batch ライブラリ](batch-dotnet-get-started.md)および [Batch Python クライアント](batch-python-tutorial.md)の概要
* [Batch フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurebatch)
* [Batch のビデオ](https://azure.microsoft.com/documentation/videos/index/?services=batch)

## <a name="hpc-cluster-solutions"></a>HPC クラスターのソリューション
既存の Windows または Linux HPC クラスターを Azure にデプロイまたは拡張して、コンピューティング集中型のワークロードを実行します。  

### <a name="microsoft-hpc-pack"></a>Microsoft HPC Pack
HPC Pack は、Microsoft Azure と Windows Server テクノロジに基づいて構築された、Microsoft の無料 HPC ソリューションであり、Windows と Linux HPC の両方のワークロードを実行できます。  

* [HPC Pack 2016 をダウンロードする](https://www.microsoft.com/download/details.aspx?id=54507)
* [HPC Pack 2012 R2 Update 3 をダウンロードする](https://www.microsoft.com/download/details.aspx?id=49922)
* [ドキュメント](https://technet.microsoft.com/library/jj899572.aspx)
* Azure の HPC Pack クラスター オプション: [Linux](../virtual-machines/virtual-machines-linux-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) および [Windows](../virtual-machines/virtual-machines-windows-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 
* [HPC Pack を使用した Azure ワーカー インスタンスへのバースト](https://technet.microsoft.com/library/gg481749.aspx)
* [HPC Pack を使用した Azure Batch へのバースト](https://technet.microsoft.com/library/mt612877.aspx)
* [Windows HPC フォーラム](https://social.microsoft.com/Forums/home?category=windowshpc)

### <a name="linux-and-oss-cluster-solutions"></a>Linux と OSS クラスターのソリューション
次の Azure テンプレートを使用して、Linux HPC クラスターをデプロイします。

* [SLURM クラスターの起動](https://azure.microsoft.com/documentation/templates/slurm/)と[ブログの投稿](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx)
* [トルク クラスターの起動](https://azure.microsoft.com/documentation/templates/torque-cluster/)
* [PBS Professional によるグリッド テンプレートの計算](https://github.com/xpillons/azure-hpc/tree/master/Compute-Grid-Infra)

## <a name="hpc-storage"></a>HPC ストレージ
* [Azure での HPC ストレージの並列ファイル システム](https://blogs.msdn.microsoft.com/azurecat/2017/03/17/parallel-file-systems-for-hpc-storage-on-azure/)
* [Intel Cloud Edition for Lustre Software - Eval](https://azure.microsoft.com/marketplace/partners/intel/lustre-cloud-edition-evaleval-lustre-2-7/)
* [CentOS 7.2 テンプレートでの BeeGFS](https://github.com/smith1511/hpc/tree/master/beegfs-shared-on-centos7.2)




## <a name="microsoft-mpi"></a>Microsoft MPI
[Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) (MS-MPI) は、Windows プラットフォームで並列アプリケーションを開発して実行するための、Microsoft が実装する標準 Message Passing Interface です。

* [MS-MPI のダウンロード](http://go.microsoft.com/FWLink/p/?LinkID=389556)
* [MS-MPI リファレンス](https://msdn.microsoft.com/library/dn473458.aspx)
* [MPI フォーラム](https://social.microsoft.com/Forums/en-us/home?forum=windowshpcmpi)

## <a name="compute-intensive-instances"></a>コンピューティング集中型インスタンス
Azure では、Linux と Windows の HPC ワークロードを実行するために、[さまざまな VM サイズ](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)が用意されています。たとえば、[コンピューティング集中型 H シリーズ](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) インスタンスはバックエンド RDMA ネットワークに接続できます。 

* [MPI アプリケーションを実行するように Linux RDMA クラスターを設定する](../virtual-machines/linux/classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Set up a Windows RDMA cluster with HPC Pack to run MPI applications (HPC Pack を使用して Windows RDMA クラスターをセットアップして MPI アプリケーションを実行する)](../virtual-machines/windows/classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

GPU 集中型ワークロードの場合は、[NC および NV サイズ](https://azure.microsoft.com/blog/azure-n-series-general-availability-on-december-1/)に関する記事を参照してください。

## <a name="samples-and-demos"></a>サンプルとデモ
* [Azure Batch C# および Python のコード サンプル](https://github.com/Azure/azure-batch-samples)
* バッチ型の Dockerized ワークロードを Azure Batch に簡単にデプロイするための [Batch Shipyard](https://azure.github.io/batch-shipyard/) ツールキット
* [doAzureParallel](http://www.github.com/Azure/doAzureParallel) Azure Batch の上に構築された R パッケージ
* [体験版 SUSE Linux Enterprise Server for HPC](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)

## <a name="related-azure-services"></a>関連する Azure サービス
* [Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)
* [Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/)
* [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)
* [Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [仮想マシン スケール セット](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/)
* [Cloud Services](https://azure.microsoft.com/documentation/services/cloud-services/)
* [App Service](https://azure.microsoft.com/documentation/services/app-service/)
* [Media Services](https://azure.microsoft.com/documentation/services/media-services/)
* [関数](https://azure.microsoft.com/documentation/services/functions/)

## <a name="architecture-blueprints"></a>アーキテクチャの設計図
* [HPC and data orchestration using Azure Batch and Azure Data Factory (Azure Batch と Azure Data Factory を使用した HPC とデータのオーケストレーション)](http://go.microsoft.com/fwlink/?linkid=717686) (PDF) と[記事](../data-factory/data-factory-data-processing-using-batch.md)

## <a name="industry-solutions"></a>業界のソリューション
* [銀行と証券](https://finance.azure.com/)
* [エンジニアリング シミュレーション](https://simulation.azure.com/) 

## <a name="customer-stories"></a>顧客事例
* [ANEO](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=4168) 
* [d3View](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=22088)
* [Ludwig Institute of Cancer Research](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5830)
* [Microsoft Research](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=15634)
* [Milliman](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=14967)
* [Mitsubishi UFJ Securities International](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=26266)
* [Schlumberger](http://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)
* [Towers Watson](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18222)
* [UberCloud](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)

## <a name="next-steps"></a>次のステップ
* 最新情報については、[Microsoft HPC と Batch のチーム ブログ](http://blogs.technet.com/b/windowshpc/)と[Azure ブログ](https://azure.microsoft.com/blog/tag/hpc/)をご覧ください。
* また、「[Batch の新機能](https://azure.microsoft.com/updates/?service=batch)」を確認するか、[RSS フィード](https://azure.microsoft.com/updates/feed/?service=batch)にご登録ください。


