---
title: パッケージの管理
description: Azure Synapse Analytics で、Apache Spark によって使用されるライブラリを追加および管理する方法について説明します。
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 03/01/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: e8ad6d072af6979eb8509068c1dcd239e7840950
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598016"
---
# <a name="manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Azure Synapse Analytics で Apache Spark 用のライブラリを管理する
ライブラリでは、プログラムまたはプロジェクトに含めることができる再利用可能なコードが提供されます。 

さまざまな理由から、サーバーレス Apache Spark プール環境を更新する必要がある場合があります。 たとえば、次のようなことが考えられます。
- コア依存関係の 1 つで新しいバージョンがリリースされた。
- 機械学習モデルのトレーニングやデータの準備のために追加のパッケージが必要になった。
- より優れたパッケージが見つかり、前のパッケージが不要になった。
- Apache Spark プールで使用可能にする必要のあるカスタム パッケージがチームによって構築された。

サード パーティのコードまたはローカル環境でビルドされたコードをアプリケーションで使用できるようにするには、いずれかのサーバーレス Apache Spark プールまたはノートブック セッションにライブラリをインストールすることができます。
  
## <a name="default-installation"></a>既定のインストール
Azure Synapse Analytics の Apache Spark には、Anacondas の完全インストールと追加のライブラリがあります。 完全なライブラリの一覧については、[Apache Spark のバージョンのサポート](apache-spark-version-support.md)に関するページを参照してください。 

これらのライブラリは、Spark インスタンスが起動されるときに自動的に組み込まれます。 追加のパッケージは、Spark プール レベルまたはセッション レベルで追加できます。

## <a name="workspace-packages"></a>ワークスペース パッケージ
カスタムのアプリケーションまたはモデルを開発する場合は、コードをパッケージ化するために、wheel または jar ファイルなどのさまざまなコード成果物をチームで開発することができます。 

Synapse では、ワークスペース パッケージをカスタムまたはプライベートの wheel または jar ファイルにすることができます。 これらのパッケージをワークスペースにアップロードし、後で特定の Spark プールに割り当てることができます。 割り当てが完了すると、これらのワークスペース パッケージはすべての Spark プール セッションに自動的にインストールされます。

ワークスペース ライブラリを管理する方法の詳細については、次の操作方法ガイドをご覧ください。

- [Python ワークスペース パッケージ (プレビュー):](./apache-spark-manage-python-packages.md#install-wheel-files) Python wheel ファイルをワークスペース パッケージとしてアップロードし、後からこれらのパッケージを特定のサーバーレス Apache Spark プールに追加します。
- [Scala または Java ワークスペース パッケージ (プレビュー):](./apache-spark-manage-scala-packages.md#workspace-packages) Scala および Java jar ファイルをワークスペース パッケージとしてアップロードし、後からこれらのパッケージを特定のサーバーレス Apache Spark プールに追加します。

## <a name="pool-packages"></a>プール パッケージ
場合によっては、特定の Apache Spark プールで使用されるパッケージのセットを標準化する必要があります。 この標準化は、チームの複数のユーザーが同じパッケージをよくインストールするという場合に便利です。 

Azure Synapse Analytics のプール管理機能を使用すると、特定のサーバーレス Apache Spark プールにインストールする既定のライブラリのセットを構成できます。 これらのライブラリは、[基本ランタイム](./apache-spark-version-support.md)の上にインストールされます。 

現時点では、プールの管理は Python でのみサポートされています。 Python の場合、Synapse Spark プールでは、Conda を使用して Python パッケージの依存関係がインストールされ、管理されます。 プール レベルのライブラリを指定するときに、requirements.txt または environment.yml を指定できるようになりました。 この環境構成ファイルは、その Spark プールから Spark インスタンスが作成されるたびに使用されます。 

これらの機能の詳細については、[Python プール管理](./apache-spark-manage-python-packages.md#pool-libraries)に関するドキュメントをご覧ください。

> [!IMPORTANT]
> - インストールするパッケージが大きいか、インストールに時間がかかる場合、これは Spark インスタンスのアップタイムに影響します。
> - PySpark、Python、Scala/Java、.NET、または Spark のバージョンの変更はサポートされていません。
> - DEP が有効なワークスペースでは、PyPI からのパッケージのインストールはサポートされていません。

## <a name="session-scoped-packages"></a>セッション スコープのパッケージ
多くの場合、対話型でデータ分析または機械学習を行うときには、より新しいパッケージを試す必要が生じたり、Apache Spark プールでまだ使用可能になっていないパッケージが必要になったりする場合があります。 プールの構成を更新する代わりに、ユーザーがセッション スコープのパッケージを使用してセッションの依存関係を追加、管理、更新できるようになりました。

セッション スコープのパッケージでは、ユーザーがセッションの開始時にパッケージの依存関係を定義できます。 セッション スコープのパッケージをインストールすると、指定されたパッケージにアクセスできるのは現在のセッションのみとなります。 そのため、これらのセッション スコープのパッケージが、同じ Apache Spark プールを使用する他のセッションやジョブに影響することはありません。 また、これらのライブラリは、基本ランタイムおよびプール レベルのパッケージの上にインストールされます。 

セッション スコープのパッケージを管理する方法の詳細については、次の操作方法ガイドをご覧ください。

- [Python セッション パッケージ (プレビュー):](./apache-spark-manage-python-packages.md) セッションの開始時に Conda *environment.yml* を提供して、一般的なリポジトリから追加の Python パッケージをインストールします。 
- [Scala または Java セッション パッケージ:](./apache-spark-manage-scala-packages.md) セッションの開始時に、`%%configure` を使用して、インストールする jar ファイルの一覧を指定します。

## <a name="next-steps"></a>次のステップ
- 既定のライブラリを確認します: [Apache Spark バージョンのサポート](apache-spark-version-support.md)
