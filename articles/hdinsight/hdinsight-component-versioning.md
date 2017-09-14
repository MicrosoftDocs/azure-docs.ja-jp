---
title: "Hadoop コンポーネントとバージョン - Azure HDInsight | Microsoft Docs"
description: "HDInsight での Hadoop コンポーネントおよびバージョンと、このクラウド ディストリビューションの Hortonworks Data Platform で使用できるサービス レベルについて説明します。"
keywords: "hadoop バージョン,hadoop エコシステム コンポーネント,hadoop コンポーネント,hadoop バージョンの確認方法"
services: hdinsight
editor: cgronlun
manager: asadk
author: bprakash
tags: azure-portal
documentationcenter: 
ms.assetid: 367b3f4a-f7d3-4e59-abd0-5dc59576f1ff
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2017
ms.author: bprakash
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: 26512049dba3d4dde575c5a67ba884fb56374236
ms.contentlocale: ja-jp
ms.lasthandoff: 08/10/2017

---
# <a name="what-are-the-hadoop-components-and-versions-available-with-hdinsight"></a>HDInsight で使用可能な Hadoop コンポーネントとバージョンとは

Microsoft Azure HDInsight の Apache Hadoop エコシステムのコンポーネントおよびバージョンと、Standard と Premium の各サービス レベルについて説明します。 また、HDInsight で Hadoop コンポーネントのバージョンを確認する方法についても説明します。 

HDInsight の各バージョンは、あるバージョンの Hortonworks Data Platform (HDP) のクラウド ディストリビューションです。

## <a name="hadoop-components-available-with-different-hdinsight-versions"></a>HDInsight の各バージョンで使用できる Hadoop コンポーネント
Azure HDInsight は、いつでもデプロイできる Hadoop クラスター バージョンを複数サポートしています。 各バージョンを選択すると、特定のバージョンの HDP ディストリビューションと、そのディストリビューションに含まれるコンポーネントが作成されます。 2017 年 2 月 17 日現在、Azure HDInsight で使用される既定のクラスター バージョンは、HDP 2.5 を基盤とした 3.5 です。

HDInsight クラスター バージョンに対応するコンポーネントのバージョンを、次の表にまとめます。 

> [!NOTE]
> HDInsight サービスの既定のバージョンは、予告なく変更される場合があります。 バージョンの依存関係がある場合は、Azure PowerShell や Azure CLI を含む .NET SDK を使用してクラスターを作成する際に HDInsight バージョンを指定してください。

| コンポーネント | HDInsight 3.6 (既定) | HDInsight 3.5 | HDInsight 3.4 | HDInsight 3.3 | HDInsight 3.2 | HDInsight 3.1 | HDInsight 3.0 |
| --- | --- | --- | --- | --- | --- | --- |--- |
| Hortonworks Data Platform |2.6 |2.5 |2.4 |2.3 |2.2 |2.1.7 |2.0 |
| Apache Hadoop と YARN |2.7.3 |2.7.3 |2.7.1 |2.7.1 |2.6.0 |2.4.0 |2.2.0 |
| Apache Tez |0.7.0 |0.7.0 |0.7.0 |0.7.0 |0.5.2 |0.4.0 |-|
| Apache Pig |0.16.0 |0.16.0 |0.15.0 |0.15.0 |0.14.0 |0.12.1 |0.12.0 |
| Apache Hive と HCatalog |1.2.1 |1.2.1 |1.2.1 |1.2.1 |0.14.0 |0.13.1 |0.12.0 |
| Apache Hive2 | 2.1.0 |-|-|-|-|-|-|
| Apache Tez Hive2 | 0.8.4 |-|-|-|-|-|-|
| Apache Ranger | 0.7.0 |0.6.0 |-|-|-|-|-|
| Apache HBase |1.1.2 |1.1.2 |1.1.2 |1.1.1 |0.98.4 |0.98.0 |-|
| Apache Sqoop |1.4.6 |1.4.6 |1.4.6 |1.4.6 |1.4.5 |1.4.4 |1.4.4 |
| Apache Oozie |4.2.0 |4.2.0 |4.2.0 |4.2.0 |4.1.0 |4.0.0 |4.0.0 |
| Apache Zookeeper |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.5 |3.4.5 |
| Apache Storm |1.1.0 |1.0.1 |0.10.0 |0.10.0 |0.9.3 |0.9.1 |-|
| Apache Mahout |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0 |0.9.0 |-|
| Apache Phoenix |4.7.0 |4.7.0 |4.4.0 |4.4.0 |4.2.0 |4.0.0.2.1.7.0-2162 |-|
| Apache Spark |2.1.0 (Linux のみ) |1.6.2 + 2.0 (Linux のみ) |1.6.0 (Linux のみ) |1.5.2 (Linux の試験的ビルドのみ) |1.3.1 (Windows のみ) |-|-|
| Apache Kafka | 0.10.0 | 0.10.0 | 0.9.0 |-|-|-|-|
| Apache Ambari | 2.5.0 | 2.4.0 | 2.2.1 | 2.1.0 |-|-|-|
| Apache Zeppelin | 0.7.0 |-|-|-|-|-|-|
| Mono |4.2.1 |4.2.1 |3.2.8 |-|-|-|

## <a name="check-for-current-hadoop-component-version-information"></a>現在の Hadoop コンポーネントのバージョン情報の確認

HDInsight クラスターのバージョンに関連付けられた Hadoop エコシステム コンポーネントのバージョンは、将来 HDInsight が更新されたときに変更される可能性があります。 Hadoop コンポーネントを調べて、どのバージョンがクラスターに使用されているかを確認するには、Ambari REST API を使用します。 **GetComponentInformation** コマンドによって、サービス コンポーネントに関する情報を取得します。 詳細については、[Ambari のドキュメント][ambari-docs]を参照してください。

Windows 向けのクラスターでは、コンポーネントのバージョンを確認するもう 1 つの方法は、リモート デスクトップを使用してクラスターにログインし、C:\apps\dist\ ディレクトリの内容を確認することです。

> [!IMPORTANT]
> Linux は、バージョン 3.4 以降の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](#hdinsight-windows-retirement)に関する記事をご覧ください。

### <a name="release-notes"></a>リリース ノート

HDInsight の最新バージョンに関する追加のリリース ノートは、 [HDInsight リリース ノート](hdinsight-release-notes.md) を参照してください。

## <a name="supported-hdinsight-versions"></a>サポートされる HDInsight のバージョン
次の表は、現在 Azure Portal で使用できる HDInsight のバージョンをまとめたものです。 各 HDInsight バージョンに対応する HDP バージョンを製品のリリース日とともにまとめています。 サポート有効期限と提供終了日も記載されます (これらが既知の場合)。

> [!NOTE]
> バージョンのサポートが期限切れになると、Microsoft Azure クラシック ポータルで使用できなくなります。 ただし、クラスター バージョンは、Windows PowerShell [New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) コマンドの `Version` パラメーターと .NET SDK を使用することで、バージョンの提供終了日まで利用できます。
> 
> ヘッド ノードを 2 つ備えた可用性の高いクラスターは、HDInsight バージョン 2.1 以降では既定でデプロイされています。 HDInsight バージョン 1.6 クラスターでは利用できません。

| HDInsight のバージョン | HDP のバージョン | VM の OS | 高可用性 | リリース日 | Azure Portal での可用性 | サポート有効期限 | 提供終了日 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.6 |HDP 2.6 |Ubuntu 16 |あり |2017 年 4 月 4 日 |あり | | |
| HDInsight 3.5 |HDP 2.5 |Ubuntu 16 |あり |2016 年 9 月 30 日 |あり |2017 年 9 月 5 日 |2018 5 月 31 日 |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |あり |2016 年 3 月 29 日 |あり |2016 年 12 月 29 日 |2018 年 1 月 9 日 |
| HDInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |あり |2015 年 12 月 2 日 |あり |2016 年 6 月 27 日 |2018 年 7 月 31日 |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |あり |2015 年 12 月 2 日 |あり |2016 年 6 月 27 日 |2017 年 7 月 31 日 |
| HDInsight 3.2 |HDP 2.2 |Ubuntu 12.04 LTS または Windows Server 2012 R2 |あり |2015 年 2 月 18 日 |いいえ |2016 年 3 月 1 日 |2017 年 4 月 1 日 |
| HDInsight 3.1 |HDP 2.1 |Windows Server 2012 R2 |あり |2014 年 6 月 24 日 |いいえ |2015 年 5 月 18 日 |2016 年 6 月 30 日 |
| HDInsight 3.0 |HDP 2.0 |Windows Server 2012 R2 |あり |2014 年 2 月 11 日 |いいえ |2014 年 9 月 17 日 |2015 年 6 月 30 日 |
| HDInsight 2.1 |HDP 1.3 |Windows Server 2012 R2 |あり |2013 年 10 月 28 日 |いいえ |2014 年 5 月 12 日 |2015 年 5 月 31 日 |
| HDInsight 1.6 |HDP 1.1 | |いいえ |2013 年 10 月 28 日 |いいえ |2014 年 4 月 26 日 |2015 年 5 月 31 日 |

## <a name="hdinsight-windows-retirement"></a>Windows での HDInsight の提供終了
Microsoft Azure HDInsight バージョン 3.3 が Windows 向け HDInsight の最終バージョンでした。 Windows 向けの HDInsight の提供終了日は 2018 年 7 月 31 日です。 Windows 向けの HDInsight クラスター バージョン 3.3 以前をお持ちの場合、2018 年 7 月 31 日までに Linux 向けの HDInsight (HDInsight バージョン 3.5 以降) に移行する必要があります。 Linux OS に移行しても、HDInsight クラスターの作成やサイズ変更の機能を保持できます。 Windows での HDInsight バージョン 3.3 のサポートは 2016 年 6 月 27 日で有効期限が切れました。

HDInsight バージョン 3.4 以降では、Microsoft は Linux OS でのみ HDInsight をリリースしています。 その結果、HDInsight 内のコンポーネントの一部は、Linux でのみ使用できます。 これらには、Apache Ranger、Kafka、Interactive Hive、Spark、HDInsight の各種アプリケーション、および プライマリ ファイル システムとしての Azure Data Lake Store が含まれています。 HDInsight の今後のリリースは、Linux OS でのみ利用可能です。 Windows 向けの HDInsight は今後一切リリースされません。 

## <a name="faqs"></a>FAQ

### <a name="what-is-the-timeline-for-retiring-hdinsight-on-windows"></a>Windows 向けの HDInsight 提供終了のタイムラインはどうなっていますか。
Windows 向けの HDInsight の提供終了日は 2018 年 7 月 31 日です。 お客様のリージョンにおいて予定提供終了日が異なる場合は、個別に通知されます。 

### <a name="what-is-the-impact-of-retiring-hdinsight-on-windows-for-existing-customers"></a>Windows 向けの HDInsight が提供終了することで、既存のユーザーにはどのような影響がありますか。
Windows 向けの HDInsight が提供終了になった後、Windows 向けの新しい HDInsight クラスターを作成することや、既存の HDInsight のサイズを変更することはできません。 HDInsight バージョン 3.3 のサポートは 2016 年 6 月 27 日で有効期限が切れました。 したがって、HDInsight 3.3 またはそれ以前のバージョンについては、サポートやバグ修正はありません。 HDInsight の今後のリリースは、Linux OS でのみ利用可能です。 Windows 向けの HDInsight は今後一切リリースされません。
 
### <a name="which-versions-of-hdinsight-on-windows-are-affected"></a>Windows 向け HDInsight のどのバージョンが影響を受けますか。
Azure HDInsight バージョン 3.3 が Windows 向け HDInsight の最終バージョンです。 Windows 向け HDInsight が提供終了になる前に、Windows 向けのバージョン 3.3 以前のすべての HDInsight クラスターを Linux 向け HDInsight バージョン 3.5 以降に移行する必要があります。 クラスターを Linux 向け HDInsight に移行しても、新しいクラスターの作成や既存のサイズ変更の機能は保持されます。 

### <a name="what-do-i-need-to-do"></a>何をする必要がありますか?
2018 年 7 月 31 日までに、Windows 向け HDInsight クラスターをサポート対象の Linux 向け HDInsight クラスターに移行します。 詳細については、[HDInsight の移行のドキュメント](https://docs.microsoft.com/en-gb/azure/hdinsight/hdinsight-migrate-from-windows-to-linux)をご覧ください。 Azure HDInsight のバージョンの詳細については、[サポートされているバージョン](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-component-versioning#supported-hdinsight-versions)の一覧をご覧ください。 

### <a name="where-do-i-find-the-cluster-os-type"></a>クラスター OS の種類はどこでわかりますか。
Azure Portal で [HDInsight クラスターの概要] ページに移動し、**[要点]** の下の **[クラスターの種類]** を見つけます。 クラスター OS の種類は、そのページに一覧表示されます。 

### <a name="i-cant-migrate-to-an-hdinsight-linux-cluster-by-july-31-2018-what-is-the-impact-to-my-hdinsight-windows-cluster"></a>2018 年 7 月 31 日までに Linux 向け HDInsight クラスターに移行できません。 Windows 向けの HDInsight クラスターにはどのような影響がありますか。
Windows 向けの HDInsight クラスターは現状のまま動作しますが、Windows 向けの新しい HDInsight クラスターを作成することや、Windows 向けの既存の HDInsight のサイズを変更することはできません。 

### <a name="my-cluster-has-a-net-dependency-how-do-i-resolve-this-dependency-on-linux"></a>クラスターには .NET との依存関係が存在します。 この依存関係は Linux でどのように解決すればいいですか。
[Mono プロジェクト](http://www.mono-project.com/)を使用して、Linux 向けクラスターの依存関係を解決できます。 .NET のオープン ソースの実装は、Linux 向けの HDInsight クラスターで利用できます。 詳細については、[HDInsight の移行のドキュメント](https://docs.microsoft.com/en-gb/azure/hdinsight/hdinsight-migrate-from-windows-to-linux)をご覧ください。 

### <a name="im-a-new-customer-for-hdinsight-on-windows-how-can-i-create-an-hdinsight-windows-cluster"></a>Windows 向けの HDInsight を初めて使用します。 Windows 向けの HDInsight クラスターはどうすれば作成できますか。
2017 年 7 月 3 日現在、Windows 向けの新しい HDInsight クラスターを作成できるのは既存の Windows 向けの HDInsight ユーザーのみです。 新規ユーザーは、PowerShell または SDK を使用して、Azure Portal で、Windows 向けの HDInsight クラスターを作成することはできません。 新規ユーザーは Linux 向けの HDInsight クラスターを作成することをお勧めします。 既存のユーザーは、Windows 向けの HDInsight 提供終了日まで Windows 向けの新しい HDInsight クラスターを作成できます。 

### <a name="is-there-a-pricing-impact-associated-with-moving-from-hdinsight-on-windows-to-hdinsight-on-linux"></a>Windows 向け HDInsight から Linux 向け HDInsight への移行に関して、料金に影響はありますか。
いいえ、いずれの OS の HDInsight でも料金は同じです。 

### <a name="what-are-the-customer-advantages-associated-with-the-move-to-only-using-hdinsight-on-linux"></a>Linux 向けの HDInsight のみを使用することによるユーザーの利点とは何ですか。
* HDInsight サービスを介した、オープン ソースのビッグ データ テクノロジをより迅速に市場に提供
* サポートを目的とした大規模なコミュニティとエコシステム
* Hadoop などのビッグ データ テクノロジのオープン ソース コミュニティによる活発な開発を活用する機能

### <a name="does-hdinsight-on-linux-provide-additional-functionality-beyond-what-is-available-in-hdinsight-on-windows"></a>Linux 向けの HDInsight では、Windows 向け HDInsight での機能を超えるような追加機能が提供されますか。
HDInsight バージョン 3.4 以降では、Microsoft は Linux OS でのみ HDInsight をリリースしています。 その結果、HDInsight 内のコンポーネントの一部は、Linux でのみ使用できます。 これらには、Apache Ranger、Kafka、Interactive Hive、Spark、HDInsight の各種アプリケーション、および プライマリ ファイル システムとしての Azure Data Lake Store が含まれています。 

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>HDInsight クラスター バージョンのサービス レベル アグリーメント
サービス レベル アグリーメント (SLA) は、_サポート ウィンドウ_の条件で定義されます。 サポート ウィンドウとは、HDInsight クラスターのバージョンが Microsoft カスタマー サービス & サポートによってサポートされる期間です。 バージョンの_サポート有効期限_が切れている場合、HDInsight クラスターはサポート対象外となります。 サポートされているバージョンの詳細については、[サポートされている HDInsight クラスター バージョン](https://docs.microsoft.com/en-gb/azure/hdinsight/hdinsight-migrate-from-windows-to-linux)の一覧をご覧ください。 所定の HDInsight Version X のサポート有効期限は (新しい X+1 バージョンが利用可能になった後)、次の数式で計算した日付のうち、遅い方とされます。  

* 数式 1: HDInsight クラスター バージョン X がリリースされた日に 180 日を加える。
* 数式 2: HDInsight クラスター バージョン X+1 が Azure Portal で使用可能になった日付に 90 日を加える。

_提供終了日_とは、その日を過ぎると HDInsight でクラスター バージョンを作成できなくなる日付です。 2017 年 7 月 31 日以降は、提供終了日より後に HDInsight クラスターのサイズを変更できません。 

> [!NOTE]
> Windows 向けの HDInsight クラスター (バージョン 2.1、3.0、3.1、3.2、および 3.3 を含む) は Azure ゲスト OS ファミリ 4 で実行されます。この OS は Windows Server 2012 R2 の 64 ビット版を使用します。 Azure ゲスト OS ファミリ バージョン 4 は、.NET Framework のバージョン 4.0、4.5、4.5.1、および 4.5.2 をサポートします。

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>HDInsight バージョンに対応する Hortonworks リリース ノート

このセクションでは、HDInsight で使用される Hortonworks Data Platform ディストリビューションと Apache コンポーネントのリリース ノートへのリンクを提供します。
* HDInsight クラスター Version 3.6 は、[Hortonworks Data Platform 2.6](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html) を基盤とする Hadoop ディストリビューションを使用します。
* HDInsight クラスター Version 3.5 は、[Hortonworks Data Platform 2.5](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html) を基盤とする Hadoop ディストリビューションを使用します。 HDInsight クラスター バージョン 3.5 は、Azure Portal で作成される_既定の_ Hadoop クラスターです。
* HDInsight クラスター Version 3.4 は、 [Hortonworks Data Platform 2.4](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html)を基盤とする Hadoop ディストリビューションを使用します。
* HDInsight クラスター Version 3.3 は、 [Hortonworks Data Platform 2.3](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html)を基盤とする Hadoop ディストリビューションを使用します。

  * [Apache Storm のリリース ノート](https://storm.apache.org/2015/11/05/storm0100-released.html)は Apache Web サイトで入手できます。
  * [Apache Hive のリリース ノート](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843)は Apache Web サイトで入手できます。
* HDInsight クラスター Version 3.2 は、[Hortonworks Data Platform 2.2][hdp-2-2] を基盤とする Hadoop ディストリビューションを使用します。

  * 各 Apache コンポーネントのリリース ノートは次の場所で入手できます: [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450)、[Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954)、[HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810)、[Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581)、[M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180)、[HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181)、[YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197)、[Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179)、[Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742)、[Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486)、[Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112)、[Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620)。
* HDInsight クラスター バージョン 3.1 は、[Hortonworks Data Platform 2.1.7][hdp-2-1-7] を基盤とする Hadoop ディストリビューションを使用します。 2014 年 11 月 7 日より前に作成された HDInsight 3.1 クラスターは、[Hortonworks Data Platform 2.1.1][hdp-2-1-1] に基づいています。
* HDInsight クラスター Version 3.0 は、[Hortonworks Data Platform 2.0][hdp-2-0-8] を基盤とする Hadoop ディストリビューションを使用します。
* HDInsight クラスター Version 2.1 は、[Hortonworks Data Platform 1.3][hdp-1-3-0] を基盤とする Hadoop ディストリビューションを使用します。
* HDInsight クラスター Version 1.6 は、[Hortonworks Data Platform 1.1][hdp-1-1-0] を基盤とする Hadoop ディストリビューションを使用します。

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight Standard と HDInsight Premium

Azure HDInsight では、_Standard_ と _Premium_ の 2 つのカテゴリでビッグ データ クラウド サービスを提供します。 次の表は、HDInsight Premium で_のみ_利用できる機能の一覧です。 表で明示的に記述していない機能は、HDInsight Standard と Premium の両方で利用できます。

> [!NOTE]
> HDInsight Premium は現在プレビューの段階で、Linux クラスターでのみ利用できます。

| HDInsight Premium の機能 | Description |
| --- | --- |
| ドメイン参加済み HDInsight クラスター |HDInsight クラスターを Azure Active Directory (Azure AD) のドメインに参加させることで、エンタープライズ レベルのセキュリティを実現します。 HDInsight Premium では、Azure AD を使って 企業からHDInsight クラスターへのログオンを認証できる従業員の一覧を構成できるようになりました。 エンタープライズ管理者は、[Apache Ranger](http://hortonworks.com/apache/ranger/) を使用して Hive のセキュリティを確保するためのロールベースのアクセス制御を構成できるため、データへのアクセスを必要な分だけに制限できます。 また、管理者は、従業員によるデータ アクセスとアクセス制御ポリシーに対して行われた変更を監査できるため、企業リソースの高度なガバナンスを実現できます。 詳しくは、[ドメイン参加済み HDInsight クラスターの構成](hdinsight-domain-joined-configure.md)に関するページをご覧ください。 |

### <a name="cluster-types-supported-in-hdinsight-premium"></a>HDInsight Premium でサポートされるクラスターの種類
次の表は、HDInsight Premium でサポートされるクラスターの種類の一覧です。

| クラスターの種類 | Standard | Premium (プレビュー) |
| --- | --- | --- |
| Hadoop |はい |はい (HDInsight 3.6 のみ) |
| Spark |はい |いいえ |
| HBase |はい |いいえ |
| Storm |はい |いいえ |
| R Server |はい |いいえ |
| 対話型 Hive (プレビュー) |はい |なし |
| Kafka (プレビュー) |はい |いいえ | 

### <a name="support-for-azure-data-lake-store-in-hdinsight-premium"></a>HDInsight Premium での Azure Data Lake Store のサポート

HDInsight Premium クラスターは、プライマリ ストレージとして Azure Data Lake Store の使用をサポートしません。 ただし、HDInsight Premium クラスターで Azure Data Lake Store をアドオン ストレージとして使用することはできます。

### <a name="pricing-and-sla"></a>料金と SLA
HDInsight Premium の料金と SLA の詳細については、「 [HDInsight の価格](https://azure.microsoft.com/pricing/details/hdinsight/)」を参照してください。

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>クラスターの既定のノード構成と仮想マシン サイズ
次の表に、HDInsight クラスターの既定の仮想マシン (VM) サイズを示します。

> [!IMPORTANT]
> 1 つのクラスターに 32 個を超えるワーカー ノードが必要な場合、コア数が 8 個以上で RAM が 14 GB 以上のサイズのヘッド ノードを選択する必要があります。
> 
> 

* ブラジル南部と西日本を除くすべてのサポートされているリージョン:

  | クラスターの種類 | Hadoop | HBase | Storm | Spark | R Server |
  | --- | --- | --- | --- | --- | --- |
  | ヘッド: 既定の VM サイズ |D3 v2 |D3 v2 |A3 |D12 v2 |D12 v2 |
  | ヘッド: 推奨される VM サイズ |D3 v2、D4 v2、D12 v2 |D3 v2、D4 v2、D12 v2 |A3、A4、A5 |D12 v2、D13 v2、D14 v2 |D12 v2、D13 v2、D14 v2 |
  | worker: 既定の VM サイズ |D3 v2 |D3 v2 |D3 v2 |Windows: D12 v2、Linux: D4 v2 |Windows: D12 v2、Linux: D4 v2 |
  | worker: 推奨される VM サイズ |D3 v2、D4 v2、D12 v2 |D3 v2、D4 v2、D12 v2 |D3 v2、D4 v2、D12 v2 |Windows: D12 v2、D13 v2、D14 v2、Linux: D4 v2、D12 v2、D13 v2、D14 v2 |Windows: D12 v2、D13 v2、D14 v2、Linux: D4 v2、D12 v2、D13 v2、D14 v2 |
  | ZooKeeper: 既定の VM サイズ | |A3 |A2 | | |
  | ZooKeeper: 推奨される VM サイズ | |A3、A4、A5 |A2、A3、A4 | | |
  | エッジ: 既定の VM サイズ | | | | |Windows: D12 v2、Linux: D4 v2 |
  | エッジ: 推奨される VM サイズ | | | | |Windows: D12 v2、D13 v2、D14 v2、Linux: D4 v2、D12 v2、D13 v2、D14 v2 |
* ブラジル南部と西日本のみ (v2 サイズはありません):

  | クラスターの種類 | Hadoop | HBase | Storm | Spark | R Server |
  | --- | --- | --- | --- | --- | --- |
  | ヘッド: 既定の VM サイズ |D3 |D3 |A3 |D12 |D12 |
  | ヘッド: 推奨される VM サイズ |D3、D4、D12 |D3、D4、D12 |A3、A4、A5 |D12、D13、D14 |D12、D13、D14 |
  | worker: 既定の VM サイズ |D3 |D3 |D3 |Windows: D12、Linux: D4 |Windows: D12、Linux: D4 |
  | worker: 推奨される VM サイズ |D3、D4、D12 |D3、D4、D12 |D3、D4、D12 |Windows: D12、D13、D14、Linux: D4、D12、D13、D14 |Windows: D12、D13、D14、Linux: D4、D12、D13、D14 |
  | ZooKeeper: 既定の VM サイズ | |A2 |A2 | | |
  | ZooKeeper: 推奨される VM サイズ | |A2、A3、A4 |A2、A3、A4 | | |
  | エッジ: 既定の VM サイズ | | | | |Windows: D12、Linux: D4 |
  | エッジ: 推推奨される VM サイズ | | | | |Windows: D12、D13、D14、Linux: D4、D12、D13、D14 |

> [!NOTE]
> - ヘッドは、Storm クラスター タイプでは *Nimbus* と呼ばれます。
> - Worker は、Storm クラスター タイプでは *Supervisor* と呼ばれます。
> - Worker は、HBase クラスター タイプでは *Region* と呼ばれます。

## <a name="next-steps"></a>次のステップ
- [HDInsight で Hadoop、Spark、その他のクラスターをセットアップする](hdinsight-hadoop-provision-linux-clusters.md)
- [Windows PC から HDInsight の Hadoop で作業する](hdinsight-hadoop-windows-tools.md)

[Supported HDInsight versions]:(#supported-hdinsight-versions)

[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://azure.microsoft.com/support/forums/

[connect-excel-with-hive-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md

[hdp-2-2]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: http://zookeeper.apache.org/

