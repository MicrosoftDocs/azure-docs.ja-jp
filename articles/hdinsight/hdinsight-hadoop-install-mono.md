---
title: "HDInsight での Mono のインストールまたは更新 - Azure | Microsoft Docs"
description: "HDInsight クラスターで Mono の特定のバージョンを使用する方法について説明します。 Mono を使用して、Linux ベースの HDInsight クラスターで .NET アプリケーションを実行します。"
services: hdinsight
documentationCenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/31/2017
ms.author: larryfr
ms.custom: hdinsightactive
ms.translationtype: Human Translation
ms.sourcegitcommit: 3bbc9e9a22d962a6ee20ead05f728a2b706aee19
ms.openlocfilehash: 2721b41e8736c9ebd75cd098ad376018b4563d3f
ms.contentlocale: ja-jp
ms.lasthandoff: 06/10/2017

---

# <a name="install-or-update-mono-on-hdinsight"></a>HDInsight での Mono のインストールまたは更新

HDInsight 3.4 以降で [Mono](https://www.mono-project.com) の特定のバージョンをインストールする方法について説明します。

Mono は、HDInsight 3.4 以降のバージョンにインストールされます。Mono を使用して .NET アプリケーションを実行します。 各 HDInsight バージョンに付属する Mono のバージョンの詳細については、「[HDInsight コンポーネントのバージョン管理](hdinsight-component-versioning.md)」を参照してください。 クラスターに別のバージョンをインストールするには、このドキュメントに記載されたスクリプト アクションを使用します。 

## <a name="how-it-works"></a>動作のしくみ

このスクリプトは、次のパラメーターを受け取ります。

* __Mono バージョン番号__: インストールする Mono のバージョン。 [https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/](https://download.mono-project.com/repo/debian/dists/wheezy/snapshots/) で利用可能なバージョンにする必要があります。

スクリプトで、次の Mono パッケージをインストールします。

* __mono-complete__

* __ca-certificates-mono__

## <a name="the-script"></a>スクリプト

__スクリプトの場所__: [https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash](https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash)

__要件__:

* このスクリプトは、__ヘッド ノード__と__ワーカー ノード__に適用する必要があります。

## <a name="to-use-the-script"></a>スクリプトを使用するには

このスクリプトを HDInsight で使用する方法については、ドキュメント「[スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)」を参照してください。 スクリプトの使用には、Azure Portal、Azure PowerShell、または Azure CLI を利用できます。

スクリプト アクションのドキュメントに従って作業する際は、次の URI を使用します。

    https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash

> [!NOTE]
> このスクリプトを使用して HDInsight を構成する場合は、スクリプトを __Persisted__ としてマークします。 この設定によって、HDInsight では、スケーリング操作を使用して追加されたワーカー ノードにスクリプトを適用できるようになります。


## <a name="next-steps"></a>次のステップ

ここでは、HDInsight で特定のバージョンの Mono をインストールまたはアップグレードする方法について説明しました。 HDInsight で Mono を利用して .NET アプリケーションを使用する方法の詳細については、次のドキュメントを参照してください。

* [HDInsight で MapReduce をストリーミングするための .NET の使用](hdinsight-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [HDInsight における .NET と Hive および Pig の使用](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)
* [Storm を使用した HDInsight での C# ソリューションの開発](hdinsight-storm-develop-csharp-visual-studio-topology.md)
* [Linux ベースの HDInsight への .NET ソリューションの 移行](hdinsight-hadoop-migrate-dotnet-to-linux.md)

スクリプト アクションの使用方法の詳細については、「[スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」を参照してください。
