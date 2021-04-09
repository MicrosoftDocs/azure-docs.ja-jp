---
title: バージョン管理の概要 - Azure HDInsight
description: Azure HDInsight でのバージョン管理のしくみについて説明します。
ms.service: hdinsight
ms.topic: conceptual
author: deshriva
ms.author: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: 6db4c7856ebdf75d5bf94de1e3110bb25bc93e69
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493868"
---
# <a name="how-versioning-works-in-hdinsight"></a>HDInsight でのバージョン管理のしくみ

HDInsight サービスには、1 つのクラスターにデプロイされるリソース プロバイダーと Apache Hadoop コンポーネントという 2 つの主要なコンポーネントがあります。 

## <a name="hdinsight-resource-provider"></a>HDInsight リソース プロバイダー

Azure のリソースは、リソース プロバイダーによって利用可能になります。 HDInsight リソース プロバイダーによって、クラスターの作成、管理、削除が行われます。

## <a name="hdinsight-images"></a>HDInsight のイメージ

HDInsight により、イメージを使用して、クラスターにデプロイできるオープンソース ソフトウェア (OSS) コンポーネントがまとめられます。 これらのイメージには、Ubuntu の基本的なオペレーティング システムと、Spark、Hadoop、Kafka、HBase、Hive などのコアコンポーネントが含まれています。

## <a name="versioning-in-hdinsight"></a>HDInsight でのバージョン管理

Microsoft により、イメージと HDInsight リソース プロバイダーが定期的にアップグレードされ、新しい機能強化と機能が組み込まれます。

次の 1 つ以上が当てはまる場合、HDInsight の新しいバージョンが作成されることがあります。

- HDInsight リソース プロバイダーの機能に対する大きな変更または更新
- OSS コンポーネントのメジャー リリース
- Ubuntu オペレーティング システムに対する大きな変更

次の 1 つ以上が当てはまる場合は、イメージの新しいバージョンが作成されます。

- OSS コンポーネントのメジャー リリースまたはマイナー リリースと更新
- イメージ内のコンポーネントのパッチまたは修正

## <a name="next-steps"></a>次のステップ

- [HDInsight での Apache のコンポーネントとバージョン](./hdinsight-component-versioning.md)
