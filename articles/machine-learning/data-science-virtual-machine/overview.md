---
title: Azure Data Science Virtual Machine とは
description: Windows および Linux 用のデータ サイエンス仮想マシンの主要な分析シナリオとコンポーネント。
keywords: データ サイエンス ツール,データ サイエンス仮想マシン, データ サイエンス用ツール, linux データ サイエンス
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 02/22/2019
ms.openlocfilehash: 8359a8407e93a2b31466342b82539cef04b0fe01
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802191"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>Linux および Windows 用の Azure Data Science Virtual Machine とは

Data Science Virtual Machine (DSVM) とは、データ サイエンス専用に構築された Azure クラウド プラットフォーム上のカスタマイズされた VM イメージです。 多くのよく使われるデータ サイエンス ツールが事前にインストールおよび構成されており、高度な分析のためのインテリジェントなアプリケーションの構築をすぐに始めることができます。 

DSVM を利用できる OS:
+ Windows Server 2016
+ Ubuntu 16.04 LTS、CentOS 7.4

> [!NOTE]
> ディープ ラーニング用のすべての VM ツールは、Data Science Virtual Machine に組み込まれています。 


## <a name="why-choose-the-dsvm"></a>DSVM を選択する理由
Data Science Virtual Machine の目標は、業界を越えてすべてのスキル レベルのデータ専門家に、摩擦のない、事前に構成されたデータ サイエンス環境を提供することです。 DSVM をプロビジョニングすると、同等のワークスペースを独自にロールアウトしなくて済みます。 そうすることによって、インストール、構成、およびパッケージ管理プロセスにかかる数日、場合によっては "_数週間_" の期間を節約できます。 DSVM が割り当てられたら、直ちにデータ サイエンス プロジェクトに関する作業を開始できます。

## <a name="sample-use-cases"></a>サンプル ユース ケース

以下では、DSVM のお客様の一般的な使用例をいくつか紹介します。

### <a name="moving-data-science-workloads-to-the-cloud"></a>データ サイエンス ワークロードをクラウドに移行する

DSVM は、ローカル デスクトップをマネージド クラウド デスクトップに置き換えようと考えているデータ サイエンス チームに基準となる構成を提供します。これにより、チームのすべてのデータ科学者に、実験を検証して共同作業を促進するための一貫性のある設定が保証されます。 また、システム管理者の負担が軽減されるためコストも低減されます。 この負担軽減により、高度な分析用のソフトウェア パッケージの評価、インストール、および保守に必要な時間が節約されます。

### <a name="data-science-training-and-education"></a>データ サイエンスのトレーニングと教育
データ サイエンスのクラスを教える企業のトレーナーや教育担当者は、通常、仮想マシン イメージを提供します。 そのイメージにより、受講者が一貫したセットアップを行い、サンプルが予想どおりに動作するようにすることができます。 

DSVM は、一貫したセットアップでオンデマンドの環境を作成するため、サポートと非互換性の問題を軽減できます。 このような環境を頻繁に構築する必要がある場合 (特に短期間のトレーニング クラスの場合)、非常にメリットがあります。

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>大規模なプロジェクトのためのオンデマンドで柔軟な容量
データ サイエンスのハッカーソンや競技または大規模なデータ モデリングと探索では、スケールアウトしたハードウェア容量が通常は短期間に必要になります。 DSVM は、高性能コンピューティング リソースで実行できる実験をスケールアウトされたサーバー上で可能にするために、必要に応じて迅速にデータ サイエンス環境を複製する際に役立ちます。

### <a name="custom-compute-power-for-azure-notebooks"></a>Azure Notebooks のカスタム コンピューティング能力
[Azure Notebooks](../../notebooks/azure-notebooks-overview.md) は、インストールせずに、クラウドで Jupyter ノートブックを開発、実行、および共有するための無料のホストされるサービスです。 無料のサービス レベルは、4 GB のメモリと 1 GB のデータに制限されています。 

すべての制限を解除するには、Notebooks プロジェクトを DSVM か、Jupyter サーバー上で実行されている他の任意の VM に接続します。 Azure Active Directory を使用するアカウント (会社のアカウントなど) で Azure Notebooks にサインインすると、Notebooks には、そのアカウントに関連付けられているすべてのサブスクリプションの DSVM が自動的に表示されます。 [DSVM を Azure Notebooks に接続](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier)して、利用可能なコンピューティング能力を拡張することができます。

### <a name="short-term-experimentation-and-evaluation"></a>短期的な実験と評価
DSVM を使用すると、特に公開されている[サンプルとチュートリアル](./dsvm-samples-and-walkthroughs.md)のいくつかを試すことで、新しいデータ サイエンス [ツール](./tools-included.md)を評価または学習できます。


### <a name="deep-learning-with-gpus"></a>GPU を使用したディープ ラーニング
DSVM では、トレーニング モデルはグラフィックス処理装置 (GPU) ベースのハードウェア上でディープ ラーニング アルゴリズムを使用できます。 Azure プラットフォームの VM スケーリング機能を利用することにより、DSVM はニーズに応じてクラウドで GPU ベースのハードウェアを使用するのに役立ちます。 大規模モデルをトレーニングするときや、同じ OS ディスクを保持しつつ高速計算が必要なときに、GPU ベースの VM に切り替えることができます。 DSVM を使用して、N シリーズ GPU 対応の任意の仮想マシン SKU を選択できます。 Azure 無料アカウントでは、GPU 対応の仮想マシン SKU がサポートされないので注意してください。

Windows Server 2016 エディションの DSVM には、GPU ドライバー、フレームワーク、および GPU バージョンのディープ ラーニング フレームワークが事前インストールされています。 Linux エディションでは、GPU 上でのディープ ラーニングは、CentOS と Ubuntu の両方の DSVM で可能です。 

Ubuntu、CentOS、または Windows 2016 エディションの DSVM を、GPU ベースでない Azure 仮想マシンにデプロイすることもできます。 この場合、すべてのディープ ラーニング フレームワークは CPU モードにフォールバックはします。
 
[使用可能なディープ ラーニングと AI のフレームワークの詳細について学習](dsvm-deep-learning-ai-frameworks.md)してください。

<a name="included"></a>

## <a name="whats-included-on-the-dsvm"></a>DSVM に含まれているもの

Windows と Linux の両方の DSVM 上で使用できるツールの完全な一覧については、[こちら](tools-included.md)を参照してください。

## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。

+ Windows:
  + [Windows DSVM を設定する](provision-vm.md)
  + [Windows DSVM でできる 10 のこと](vm-do-ten-things.md)

+ Linux:
  + [Linux DSVM を設定する (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Linux DSVM を設定する (CentOS)](linux-dsvm-intro.md)
  + [Linux DSVM のデータ サイエンス](linux-dsvm-walkthrough.md)
