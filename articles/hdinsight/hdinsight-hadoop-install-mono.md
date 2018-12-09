---
title: HDInsight での Mono のインストールまたは更新 - Azure
description: HDInsight クラスターで Mono の特定のバージョンを使用する方法について説明します。 Mono を使用して、Linux ベースの HDInsight クラスターで .NET アプリケーションを実行します。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: hrasheed
ms.custom: hdinsightactive
ms.openlocfilehash: 9daf0d45c9bb24192750991e7bf84fa3614c970d
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2018
ms.locfileid: "51011036"
---
# <a name="install-or-update-mono-on-hdinsight"></a>HDInsight での Mono のインストールまたは更新

HDInsight 3.4 以降で [Mono](https://www.mono-project.com) の特定のバージョンをインストールする方法について説明します。

Mono は、HDInsight 3.4 以降のバージョンにインストールされます。Mono を使用して .NET アプリケーションを実行します。 各 HDInsight バージョンに付属する Mono のバージョンの詳細については、「[HDInsight コンポーネントのバージョン管理](hdinsight-component-versioning.md)」を参照してください。 クラスターに別のバージョンをインストールするには、このドキュメントに記載されたスクリプト アクションを使用します。 

## <a name="how-it-works"></a>動作のしくみ

このスクリプトは、次のパラメーターを受け取ります。

* __Mono バージョン番号__: インストールする Mono のバージョン。 このバージョンは、こちら ([https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/](https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/)) から入手してください。

スクリプトで、次の Mono パッケージをインストールします。

* __mono-complete__

* __ca-certificates-mono__

## <a name="the-script"></a>スクリプト

__スクリプトの場所__: [https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash](https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash)

__要件__:

* このスクリプトは、__ヘッド ノード__ と __ワーカー ノード__ に適用する必要があります。

## <a name="to-use-the-script"></a>スクリプトを使用するには

このスクリプトを HDInsight で使用する方法については、ドキュメント「[スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)」を参照してください。 スクリプトの使用には、Azure portal、Azure PowerShell、または Azure クラシック CLI を利用できます。

スクリプト アクションのドキュメントに従って作業する際は、次の URI を使用します。

    https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash

インストールされる Mono のバージョンを指定するには、__Parameters__ フィールドのバージョン番号を使用します。 たとえば、`5.4` と入力して Mono 5.4 をインストールします。

> [!NOTE]
> このスクリプトを使用して HDInsight を構成する場合は、スクリプトを __Persisted__ としてマークします。 この設定によって、HDInsight では、スケーリング操作を使用して追加されたワーカー ノードにスクリプトを適用できるようになります。

## <a name="next-steps"></a>次の手順

ここでは、HDInsight で特定のバージョンの Mono をインストールまたはアップグレードする方法について説明しました。 HDInsight で Mono を利用して .NET アプリケーションを使用する方法の詳細については、次のドキュメントを参照してください。

* [HDInsight で MapReduce をストリーミングするための .NET の使用](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [HDInsight における .NET と Hive および Pig の使用](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)
* [Storm を使用した HDInsight での C# ソリューションの開発](storm/apache-storm-develop-csharp-visual-studio-topology.md)
* [Linux ベースの HDInsight への .NET ソリューションの 移行](hdinsight-hadoop-migrate-dotnet-to-linux.md)

スクリプト アクションの使用方法の詳細については、「[スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」を参照してください。
