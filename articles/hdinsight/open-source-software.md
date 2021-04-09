---
title: Azure HDInsight でのオープンソース ソフトウェアのサポート
description: Microsoft Azure は、オープン ソース テクノロジの一般的なレベルのサポートを提供します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: fec4cff974031982c782c9265a7d3186d6bb0233
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98942556"
---
# <a name="open-source-software-support-in-azure-hdinsight"></a>Azure HDInsight でのオープンソース ソフトウェアのサポート

Microsoft Azure HDInsight サービスは、Apache Hadoop を中心に形成されたオープン ソース テクノロジの環境を利用します。 Microsoft Azure は、オープン ソース テクノロジの一般的なレベルのサポートを提供します。 詳しくは、「[Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」の **サポート範囲** に関するセクションを参照してください。 HDInsight サービスでは、組み込みのコンポーネントに対してさらに高いレベルのサポートを提供しています。

## <a name="components"></a>Components

HDInsight サービスで利用できるオープン ソース コンポーネントには、2 つの種類があります。

### <a name="built-in-components"></a>組み込みコンポーネント

これらのコンポーネントは、HDInsight クラスターにプレインストールされており、クラスターの主要な機能を提供します。 次のコンポーネントは、このカテゴリに属します。

* [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) Resource Manager。
* Hive クエリ言語 [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)。
* [Apache Mahout](https://mahout.apache.org/)。

クラスター コンポーネントの完全な一覧は、「[HDInsight で使用できる Apache Hadoop コンポーネントおよびバージョンとは](hdinsight-component-versioning.md)」から入手できます。

### <a name="custom-components"></a>カスタム コンポーネント

クラスターのユーザーは、コミュニティで入手できるコンポーネントや自作のコンポーネントを、インストールするか、ワークロード内で使用することができます。

> [!WARNING]  
> HDInsight クラスターに用意されているコンポーネントは全面的にサポートされており、 これらのコンポーネントに関連する問題の分離と解決については、Microsoft サポートが支援します。
>
> カスタム コンポーネントについては、問題のトラブルシューティングを進めるための支援として、商業的に妥当な範囲のサポートを受けることができます。 Microsoft サポートで問題を解決できる場合があります。 または、オープン ソース テクノロジに関して、深い専門知識が入手できる場所への参加をお願いすることになる場合もあります。 多くのコミュニティ サイトを使用できます。 例として、[HDInsight に関する Microsoft Q&A 質問ページ](/answers/topics/azure-hdinsight.html)や [Stack Overflow](https://stackoverflow.com) があります。
>
> Apache プロジェクトにも、[Apache の Web サイト](https://apache.org)にプロジェクトのサイトがあります。 たとえば、[Hadoop](https://hadoop.apache.org/) などです。

## <a name="component-usage"></a>コンポーネントの使用

HDInsight サービスでは、カスタム コンポーネントを使用する方法をいくつか用意しています。 コンポーネントの用途やクラスターへのインストール方法にかかわらず、同じレベルのサポートが適用されます。 以下は、HDInsight クラスターでのカスタム コンポーネントの用途として、最も一般的な方法の表です。

|使用法 |説明 |
|---|---|
|ジョブの送信|Hadoop や他の種類のジョブを、カスタム コンポーネントを実行または使用するクラスターに送信できます。|
|クラスターのカスタマイズ|クラスター作成時に、追加設定や、クラスター ノードにインストールするカスタム コンポーネントを指定できます。|
|サンプル|よく利用されるカスタム コンポーネントに対しては、それらを HDInsight クラスターで使用する方法について Microsoft やその他の提供者がサンプルを用意している場合があります。 これらのサンプルはサポートなしで提供されます。|

## <a name="next-steps"></a>次のステップ

* [スクリプト アクションを使用して Azure HDInsight クラスターをカスタマイズする](./hdinsight-hadoop-customize-cluster-linux.md)
* [HDInsight 用のスクリプト アクションのスクリプトを開発する](hdinsight-hadoop-script-actions-linux.md)
* [スクリプト アクションを使用して Azure HDInsight で Python 環境を安全に管理する](./spark/apache-spark-python-package-installation.md)