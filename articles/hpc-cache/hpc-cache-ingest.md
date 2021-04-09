---
title: Azure HPC Cache クラウド コンテナーにデータを移動する
description: Azure HPC Cache で使用する Azure Blob Storage にデータを事前設定する方法
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: 0da8a4fc1b59976c50cd96f2155715a4cb178cc9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87072766"
---
# <a name="move-data-to-azure-blob-storage"></a>Azure Blob Storage にデータを移動する

Azure Blob Storage へのデータの移動がワークフローに含まれる場合は、効率的な方法を使用していることを確認してください。 ストレージ ターゲットとして定義する前に、新しい BLOB コンテナーにデータを事前に読み込むか、コンテナーを追加してから、Azure HPC Cache を使用してデータをコピーすることができます。

この記事では、Azure HPC Cache で使用する Blob Storage にデータを移動する最良の方法について説明します。

次の情報に留意してください。

* Azure HPC Cache では、Blob Storage 内のデータを整理するために、専用のストレージ形式が使用されます。 Blob Storage ターゲットが、新しいコンテナーか空のコンテナー、またはそれまで Azure HPC Cache データに使用されていた BLOB コンテナーのいずれかでなければならないのは、そのためです。

* バックエンドのストレージ ターゲットに Azure HPC Cache 経由でデータをコピーするのが最善の選択肢となるのは、複数のクライアントおよび並列操作を使用するときです。 1 つのクライアントからの単純なコピー コマンドでは、データの移動が低速になります。

Blob Storage コンテナーにコンテンツを読み込む Python ベースのユーティリティが利用できます。 詳細については、[Blob Storage へのデータの事前読み込み](#pre-load-data-in-blob-storage-with-clfsload)のセクションを参照してください。

読み込みユーティリティを使用したくない場合、または既存のストレージ ターゲットにコンテンツを追加したい場合は、「[Azure HPC Cache 経由でデータをコピーする](#copy-data-through-the-azure-hpc-cache)」にある並列データ取り込み処理のヒントを参考にしてください。

## <a name="pre-load-data-in-blob-storage-with-clfsload"></a>CLFSLoad を使用して Blob Storage にデータを事前に読み込む

新しい Blob Storage コンテナーをストレージ ターゲットとして追加する前に、Avere CLFSLoad ユーティリティを使用して、Blob Storage コンテナーにデータをコピーすることができます。 このユーティリティは単一の Linux システム上で動作し、Azure HPC Cache に必要な独自の形式でデータを書き込みます。 キャッシュで使用する Blob Storage コンテナーにデータを事前設定する方法としては、CLFSLoad が最も効率的です。

Avere CLFSLoad ユーティリティは、Azure HPC Cache チームからの要求によって入手できます。 それについてチームの担当者に問い合わせるか、[サポート チケット](hpc-cache-support-ticket.md)を開いてサポートを依頼してください。

この方法が使用できるのは新しい空のコンテナーのみです。 コンテナーは、Avere CLFSLoad を使用する前に作成してください。

詳細情報は、Avere CLFSLoad ディストリビューションに含まれています。これは、Azure HPC Cache チームからの要請によって利用できます。

大まかな手順を以下に示します。

1. Python バージョン 3.6 以降を使用して Linux システム (VM または物理) を準備します パフォーマンスを高めるために Python 3.7 を推奨します。
1. Avere-CLFSLoad ソフトウェアを Linux システムにインストールします。
1. Linux のコマンド ラインから転送を実行します。

Avere CLFSLoad ユーティリティには、次の情報が必要です。

* Blob Storage コンテナーが存在するストレージ アカウントの ID
* 空の Blob Storage コンテナーの名前
* コンテナーへの書き込みをユーティリティに許可する Shared Access Signature (SAS) トークン
* データ ソースへのローカル パス (コピーするデータが格納されたローカル ディレクトリ、またはデータが格納されているマウントされたリモート システムのローカル パス)

## <a name="copy-data-through-the-azure-hpc-cache"></a>Azure HPC Cache 経由でデータをコピーする

Avere CLFSLoad ユーティリティを使用したくない場合、または大量のデータを既存の Blob Storage ターゲットに追加したい場合は、キャッシュ経由でデータをコピーすることができます。 Azure HPC Cache は、複数のクライアントからの要求を同時に処理するように設計されています。したがって、キャッシュ経由でデータをコピーするには、複数クライアントからの並列書き込みを使用する必要があります。

![マルチクライアントのマルチスレッドのデータ移動を示す図:左上に、オンプレミスのハードウェア ストレージを表すアイコンがあり、そこから複数の矢印が出ています。 矢印は、4 つのクライアント マシンを指しています。 各クライアント マシンから Azure HPC Cache に向かって 3 本の矢印が出ています。 Azure HPC Cache から、複数の矢印が Blob Storage を指しています。](media/hpc-cache-parallel-ingest.png)

ストレージ システム間でデータを転送するためによく使われる ``cp`` または ``copy`` コマンドは、一度に 1 つのファイルだけをコピーするシングルスレッドのプロセスです。 つまり、一度に 1 つのファイルしかファイル サーバーに取り込まれません。これでは、キャッシュのリソースを浪費してしまいます。

このセクションでは、Azure HPC Cache の Blob Storage にデータを移動するための、マルチクライアント、マルチスレッドのファイル コピー システムを作成する方法について説明します。 複数のクライアントとシンプルなコピー コマンドを使ってデータを効率よくコピーするために使用できる、ファイル転送の概念と決定点について説明します。

便利なユーティリティについても説明します。 ``msrsync`` ユーティリティを使用すると、データセットを複数のバケットに分割したうえで rsync コマンドを使用するというプロセスを部分的に自動化できます。 もう 1 つ、``parallelcp`` スクリプトというユーティリティがあります。これはソース ディレクトリを読み取り、自動的にコピー コマンドを発行するユーティリティです。

### <a name="strategic-planning"></a>戦略的計画

データを並列コピーするための戦略を作成するときは、ファイルのサイズ、ファイルの数、ディレクトリの深さにおけるトレードオフを理解する必要があります。

* ファイルが小さいとき、注目すべきメトリックは 1 秒あたりのファイル数です。
* ファイルが大きい場合 (10 MiBi 以上)、注目すべきメトリックは 1 秒あたりのバイト数です。

コピー プロセスごとにスループット率とファイル転送された率が示されます。これらは、コピー コマンドの長さのタイミングを計り、ファイル サイズとファイル数を考慮することによって、測定できます。 それらの測定方法についての説明は、このドキュメントの範囲外ですが、小さいファイルを処理するのか、大きいファイルを処理するのかを把握することが不可欠です。

Azure HPC Cache を使用した並列データ取り込みには、次の方法があります。

* 手動コピー - クライアント上で、マルチスレッドのコピーを手動で作成できます。そのためには、定義済みのファイル セットまたはパスのセットに対して、一度に複数のコピー コマンドをバックグラウンドで実行します。 詳細については、[Azure HPC Cache のデータ取り込み (手動でコピーする方法)](hpc-cache-ingest-manual.md) に関するページを参照してください。

* ``msrsync`` を使用して部分的に自動化されたコピー  - ``msrsync`` は、複数の並列 ``rsync`` プロセスを実行するラッパー ユーティリティです。 詳細については、「[Azure HPC Cache のデータ取り込み - msrsync を使用した方法](hpc-cache-ingest-msrsync.md)」を参照してください。

* コピー スクリプト (``parallelcp``) の使用 - 並列コピー スクリプトの作成と実行の方法については、「[Azure HPC Cache のデータ取り込み - 並列コピー スクリプトを使用した方法](hpc-cache-ingest-parallelcp.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

ストレージのセットアップ後にクライアントからキャッシュをマウントする方法を確認しましょう。

* [Azure HPC Cache システムにアクセスする](hpc-cache-mount.md)
