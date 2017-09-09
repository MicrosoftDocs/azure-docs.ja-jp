---
title: "Azure IoT Suite コネクテッド ファクトリの概要 | Microsoft Docs"
description: "Azure IoT Suite コネクテッド ファクトリ事前構成済みソリューションの説明。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-suite
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2017
ms.author: dobett
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: d502c8e2e2715899279f6ebcf7ed89c19a1bb9a6
ms.contentlocale: ja-jp
ms.lasthandoff: 08/25/2017

---
# <a name="get-started-with-the-connected-factory-preconfigured-solution"></a>コネクテッド ファクトリ事前構成済みソリューションの概要

Azure IoT Suite の[事前構成済みソリューション][lnk-preconfigured-solutions]は、さまざまな Azure IoT サービスを組み合わせて、IoT のビジネス シナリオを満たすエンド ツー エンド ソリューションを提供します。 "*コネクテッド ファクトリ*" は、対象の産業デバイスに接続して監視するための事前構成済みソリューションです。 このソリューションを使用して、対象デバイスから流れてくるデータのストリームを分析し、業務の生産性や収益性を高めることができます。

このチュートリアルでは、コネクテッド ファクトリ事前構成済みソリューションをプロビジョニングする方法について説明します。 また、事前構成済みソリューションの基本機能についても詳しく説明します。 こうした機能の多くには、事前構成済みソリューションの一部としてデプロイされるソリューション *ダッシュボード*からアクセスできます。

![コネクテッド ファクトリ事前構成済みソリューションのダッシュボード][img-cf-home]

このチュートリアルを完了するには、アクティブな Azure サブスクリプションが必要になります。

> [!NOTE]
> アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、[Azure の無料試用版サイト][lnk_free_trial]をご覧ください。
> 
> 

## <a name="provision-the-solution"></a>ソリューションのプロビジョニング

1. Azure アカウントの資格情報を使用して azureiotsuite.com にログオンし、**[+]** をクリックしてソリューションを作成します。
2. **[Connected factory (コネクテッド ファクトリ)]** タイルで **[選択]** をクリックします。
3. コネクテッド ファクトリの事前構成済みソリューションの **[ソリューション名]** を入力します。
4. ソリューションのプロビジョニングに使用する**サブスクリプション**と**リージョン**を選択します。
5. **[ソリューションの作成]** をクリックして、プロビジョニング プロセスを開始します。 通常、このプロセスの実行までに数分かかります。

### <a name="while-you-wait-for-the-provisioning-process-to-complete"></a>プロビジョニング プロセスが完了するのを待つ間

1. **プロビジョニング** の状態を表示する、目的のソリューションのタイルをクリックします。
2. Azure サービスが Azure サブスクリプションにデプロイされたら、 **プロビジョニングの状態** を確認します。
3. プロビジョニングが完了すると、状態が **[準備完了]** に変わります。
4. タイルをクリックし、右側のウィンドウでソリューションの詳細を確認します。

> [!NOTE]
> 構成済みソリューションのデプロイの問題が発生している場合は、「[azureiotsuite.com サイトでのアクセス許可][lnk-permissions]」と[コネクテッド ファクトリの FAQ](iot-suite-faq-cf.md) を参照してください。 それでも問題が解決しない場合は、[ポータル][lnk-portal]でサービス チケットを作成してください。

ソリューションの一覧に予測どおりに表示されない詳細がありますか? [ユーザーの声](https://feedback.azure.com/forums/321918-azure-iot)のページで機能に関する提案をお寄せください。

## <a name="scenario-overview"></a>シナリオの概要

デプロイが終わったコネクテッド ファクトリ事前構成済みソリューションには、代表的な産業シナリオに順を追って対処できるリソースが事前に入力されています。 このシナリオでは、ソリューションに接続された複数の工場から、総合設備効率 (OEE) と主要業績評価指標 (KPI) を計算するために必要なデータ値が報告されます。 以降のセクションでは、次の方法について説明します。

* 工場、生産ライン、ステーションの OEE と KPI の値を監視する
* Azure Time Series Insights を使用して、これらのデバイスで生成されたテレメトリ データを分析する
* アラートに対処して、問題を修正する

このシナリオの主な特徴は、ソリューション ダッシュボードからこれらのアクションをすべてリモートで実行できることです。 デバイスに物理的にアクセスする必要はありません。

## <a name="view-the-solution-dashboard"></a>ソリューション ダッシュボードの表示

ソリューションのダッシュボードでは、デプロイされたソリューションを管理できます。 このダッシュボードには、工場の構成全体が階層的に表示されます。 たとえば、OEE と KPI を表示し、テレメトリとアクションに関するアラート用に新しいノードを発行することができます。

1. プロビジョニングが完了し、事前構成済みソリューションのタイルに **[準備完了]** と表示されたら、**[起動]** を選択し、新しいタブでコネクテッド ファクトリ ソリューション ポータルを開きます。

    ![Launch the preconfigured solution][img-launch-solution]

1. 既定では、ソリューション ポータルに "*ダッシュボード*" が表示されます。 ポータルの他の領域に移動するには、ページの左端にあるメニューを使用します。

    ![コネクテッド ファクトリ事前構成済みソリューションのダッシュボード][cf-img-menu]

ダッシュボードには次の情報が表示されます。

* **工場の一覧**パネル: ソリューションに接続された工場の状態、場所、および現在の生産構成が表示されます。 初めてソリューションを実行すると、シミュレートされたデバイスが多数表示されます。 この生産ラインのシミュレーションでは、シミュレートされたタスクを実行してデータを共有する実際の OPC UA サーバーが生産ラインあたり 3 台構成されています。 OPC UA の詳細については、[コネクテッド ファクトリの FAQ](iot-suite-faq-cf.md) を参照してください。
* **マップ**: ソリューションに接続されている各デバイスの場所が表示されます。 ソリューションは Bing Maps API を使用して、マップに情報をプロットします。 サブスクリプションで Bing Maps Enterprise API が有効になっている場合は、この機能が自動的に使用されます。 有効になっていない場合は、マップを動的に表示する方法について、[よく寄せられる質問][lnk-faq]に関するページを参照してください。
* **[アラート]** パネル: テレメトリまたは OEE/KPI の値が、指定されたしきい値を超えた場合に、アラートが生成されて表示されます。
* **[Overall Equipment Efficiency (総合設備効率)]** パネル: 企業全体または表示中の工場/生産ライン/ステーションの OEE 値が表示されます。 この値は、各ステーション ビューから全社レベルに集計されます。 OEE の数値とその構成要素は、さらに詳しく分析できます。
* **[主要業績評価指標]** パネル: 企業全体または表示中の工場/生産ライン/ステーションで生産されたユニット数と使用されたエネルギー量が表示されます。 これらの値は、各ステーション ビューから全社レベルに集計されます。

## <a name="view-factories"></a>工場を表示する

"*工場*" パネルには、ソリューション内のすべての工場の地理的な場所、状態、現在の生産構成が表示されます。 場所の一覧から、ソリューション階層の他のレベルに移動することができます。 一覧内の行はハイパーリンクになっており、その場所にある生産ラインの詳細情報にリンクされています。 行を選択して、生産ラインの詳細を表示したり、ステーション レベル ビューまでドリル ダウンしたりできます。 また、一覧にフィルターを適用することもできます。

![コネクテッド ファクトリの事前構成済みソリューションの工場][cf-img-factories] 

1. **工場パネル**に、このソリューションの工場一覧が表示されます。

2. 工場一覧には、初期状態で、プロビジョニング プロセスで作成された 6 つの工場が表示されます。 ソリューションには、シミュレートされたデバイスや物理デバイスを追加できます。

3. 工場の詳細を表示するには、工場一覧で対象の行をクリックします。

4. 生産ラインの詳細を表示するには、一覧で対象の行をクリックします。

5. 生産ラインの特定のステーションについて発行された OPC UA ノードの詳細を表示するには、一覧で対象の行をクリックします。

6. ステーション内の特定のノードの詳細を表示するには、一覧で対象の行をクリックします。 この操作によって、Time Series Insight の視覚エフェクトを使用したコンテキスト パネルが表示されます。 これらのグラフをクリックすると、Time Series Insights エクスプローラー環境でさらに詳しい分析を行うことができます。

## <a name="view-map"></a>マップを表示する

お使いのサブスクリプションで Bing Maps API にアクセスできる場合は、"*工場のマップ*" にソリューション内のすべての工場の地理的な場所と状態が表示されます。 その場所の詳細を表示するには、マップに表示されている場所をクリックします。

![コネクテッド ファクトリの事前構成済みソリューションのマップ][cf-img-map]

## <a name="view-alerts"></a>アラートを表示する

**[アラート]** パネルには、報告された値や計算された OEE/KPI 値が指定したしきい値を超えたために生成されたアラートが表示されます。 このパネルには、ステーション レベル ビューからグローバル ビューまで、階層の各レベルでアラートが表示されます。 アラートには、アラートの説明、日付、時刻、場所、および発生回数が含まれています。 Time Series Insights データを使用して、アラートの原因となったデータを調査できます。 Time Series Insights データは、可能な場合はアラート内で視覚化されます。 管理者は、アラートに対して次のような既定のアクションを実行できます。

* アラートを閉じる。
* アラートを確認する。

必要に応じて、さらに複雑なアクションを実行できます。 たとえば、組立工程の圧力 OPC UA ノードに対して、次のようなアクションを実行できます。

* 新しいブラウザー ウィンドウの Web ページに関連情報を表示する。
* デバイスで OPC UA メソッドを呼び出して、アラートの原因を軽減する。
* 既定のアクションの可用性を抑制する。

    ![コネクテッド ファクトリ事前構成済みソリューションのアラート][cf-img-alerts]

> [!NOTE]
> これらのアラートは、事前構成済みソリューション内の構成ファイルで指定されたルールに基づいて生成されます。 このルールを使用して、OEE/KPI の数値または OPC UA ノードの値が指定されたしきい値を超えた場合にアラートを生成できます。

1. **[アラート]** パネルには、このソリューションで生成されたアラートが表示されます。

2. アラートの詳細を表示するには、[アラート] パネル内で対象のアラートをクリックします。

3. アラート データをさらに詳しく分析するには、[アラート] パネルでグラフをクリックし、Time Series Insights エクスプローラー環境を開きます。

4. [アラート] パネルには、アラートに対処するためのアクションがいくつか用意されています。 適切なオプションを選択し、[アクションの実行] コマンド ボタンをクリックします。

## <a name="view-overall-equipment-efficiency"></a>総合設備効率を表示する

総合設備効率 (OEE) は、生産関連の主要な運用パラメーターを使用して製造工程の効率を評価したものです。 OEE は業界標準の指標の 1 つで、時間稼働率、性能稼働率、良品率を掛け合わせて計算されます (OEE = 稼働率 x 性能 x 品質)。

![コネクテッド ファクトリ事前構成済みソリューションの OEE][cf-img-oee]

1. 階層内の任意のレベルの OEE を表示するには、対象となるビューに移動します。 パネルに、そのビューの OEE が、OEE 率を構成する各要素と共に表示されます。

2. 階層データ内の任意のレベルの OEE をさらに詳しく分析するには、OEE、稼働率、性能、品質のいずれかのパーセンテージをクリックします。 コンテキスト パネルに、過去 1 時間、過去 24 時間、過去 7 日間のデータを示す Time Series Insights の視覚エフェクトが表示されます。

    ![コネクテッド ファクトリ事前構成済みソリューションの TSI 視覚エフェクト][cf-img-tsi-visualization]

3. アラート データをさらに詳しく分析するには、[アラート] パネルでグラフをクリックしきます。 クリックすると、Time Series Insights エクスプローラー環境が開きます。

    ![コネクテッド ファクトリ事前構成済みソリューションの TSI エクスプローラー][cf-img-tsi-explorer]

## <a name="view-key-performance-indicators"></a>主要業績評価指標を表示する

このソリューションでは、"*1 時間あたりのユニット数*" と "*kWh 単位のエネルギー使用量*" の 2 つの主要業績評価指標が提供されます。

![コネクテッド ファクトリ事前構成済みソリューションの KPI][cf-img-kpi]

1. 階層内の任意のレベルの 1 時間あたりのユニット数またはエネルギー使用量を表示するには、対象のビューに移動します。 パネルに 1 時間あたりのユニット数またはエネルギー使用量が表示されます。

2. 階層内の任意のレベルの 1 時間あたりのユニット数またはエネルギー使用量をさらに詳しく分析するには、**[主要業績評価指標]** パネルのゲージをクリックします。 コンテキスト パネルに、過去 1 時間、過去 24 時間、過去 7 日間のデータを示す Time Series Insights の視覚エフェクトが表示されます。

## <a name="scenario-review"></a>シナリオのまとめ

このシナリオでは、工場の OEE と KPI の値をダッシュボードで監視しました。 その後、Time Series Insights を使用して、OEE および KPI のテレメトリ データを詳しく調べ、異常の検出に役立てました。 さらに、[アラート] パネルを使用して工場に関する問題を確認し、用意されたアクションを使用してアラートを解決しました。

## <a name="other-features"></a>その他の機能

以降のセクションでは、上記のシナリオで扱わなかった、コネクテッド ファクトリ ソリューションが備える他の機能をいくつか紹介します。

## <a name="apply-filters"></a>フィルターを適用する

1. [工場の場所] パネルか [アラート] パネルで**シェブロン**をクリックすると、使用可能なフィルターの一覧が表示されます。

2. [フィルター] パネルが表示されます。 

    ![コネクテッド ファクトリ構成済みソリューションのフィルター][cf-img-alert-filter]

3. 必要なフィルターを選択します。 フィルター フィールドにフリー テキストを入力することもできます。

4. フィルターが適用されます。 ダッシュボードの工場またはアラートの表に、フィルターが適用された状態であることを示すじょうごアイコンが表示されます。

    ![コネクテッド ファクトリ構成済みソリューションのフィルター][cf-img-alert-filter-funnel]

    > [!NOTE]
    > アクティブなフィルターは、一覧の内容をフィルター処理するだけで、表示されている OEE と KPI の値には影響しません。

5. フィルターをクリアするには、じょうごアイコンをクリックし、[フィルター] コンテキスト パネルでフィルターをクリアします。 工場とアラートの表に **[すべて]** というテキストが表示されます。

## <a name="browse-an-opc-ua-server"></a>OPC UA サーバーを参照する

事前構成済みソリューションをデプロイすると、シミュレートされた OPC UA サーバーが自動的にプロビジョニングされ、ソリューション ブラウザーで参照できるようになります。 これらのサーバーは、"*シミュレートされた OPC UA サーバー*" です。 シミュレートされたサーバーを使用することで、実際に物理サーバーをデプロイしなくても、事前構成済みソリューションを簡単にテストできます。 実際の OPC UA サーバーをソリューションに接続する方法については、[OPC UA デバイスをコネクテッド ファクトリ構成済みソリューションに接続する方法][lnk-connect-cf]に関するチュートリアルを参照してください。

1. ダッシュボードのナビゲーション バーにある**工場アイコン**をクリックします。

    ![コネクテッド ファクトリ事前構成済みソリューションのサーバー ブラウザー][cf-img-server-browser]

2. 事前構成済みの一覧から、いずれかのサーバーを選択します。 この一覧には、事前構成済みソリューションでデプロイされているサーバーが表示されます。

    ![コネクテッド ファクトリ事前構成済みソリューションのサーバーの選択][cf-img-server-choice]

3. **[接続]** をクリックすると、セキュリティ ダイアログが表示されます。 シミュレーションの場合は、そのまま **[続行]** をクリックして問題ありません。

4. サーバー ツリーのノードを展開するには、目的のノードをクリックします。 テレメトリを発行しているノードの横には、チェック マークが付いています。

    ![コネクテッド ファクトリ事前構成済みソリューションのサーバー ツリー][cf-img-server-tree]

5. アイテムを右クリックすると、そのアイテムの読み取り、書き込み、発行、呼び出しを行うことができます。 使用できる操作は、付与されているアクセス許可やノードの属性によって異なります。 読み取りオプションを選択すると、特定のノードの値を示すコンテキスト パネルが表示されます。 書き込みオプションを選択すると、コンテキスト パネルが開き、新しい値を入力できます。 呼び出しオプションを選択すると、ノードが表示され、呼び出しに使用するパラメーターを入力できます。

## <a name="publish-a-node"></a>ノードを発行する

"*シミュレートされた OPC UA サーバー*" を参照しているときに、新しいノードを発行することもできます。 発行したノードからテレメトリを収集して、ソリューションで分析できます。 "*シミュレートされた OPC UA サーバー*" を使用することで、実際に物理デバイスをデプロイしなくても、事前構成済みソリューションを簡単にテストできます。

1. OPC UA サーバー ブラウザー ツリーで、発行するノードを参照します。

2. ノードを右クリックします。

3. **[発行]** をクリックします。

    ![コネクテッド ファクトリでのノードの発行][cf-img-publish-node]

4. コンテキスト パネルに、正常に発行されたことを示すメッセージが表示されます。 ノードはステーション レベル ビューに、横にチェック マークが付いた状態で表示されます。

    ![コネクテッド ファクトリ事前構成済みソリューションの正常に発行された状態][cf-img-publish-success]

## <a name="command-and-control"></a>コマンドと制御

コネクテッド ファクトリでは、コマンドを使用して、クラウドから直接産業デバイスを制御できます。 この機能を使用して、デバイスで生成されたアラートに対処できます。 たとえば、クラウドからデバイスにコマンドを送信できます。 使用可能なコマンドは、OPC UA サーバー ブラウザー ツリーの **StationCommands** で確認できます。 このシナリオでは、ミュンヘンにある生産ラインの組立ステーションで圧力解放バルブを開きます。 コマンドと制御の機能を使用するには、事前構成済みソリューションのデプロイに対する**管理者**ロールが必要です。

1. OPC UA サーバー ブラウザー ツリーで、**StationCommands** ノードを参照します。

2. 使用するコマンドを選択します。

3. ノードを右クリックします。

4. **[呼び出し]** を選択します。

    ![コネクテッド ファクトリ事前構成済みソリューションの呼び出しコマンド][cf-img-call-command]

5. コンテキスト パネルに、呼び出そうとしているメソッドと適用可能なパラメーターの詳細が表示されます。

6. **[呼び出し]** を選択します。

    ![コネクテッド ファクトリ事前構成済みソリューションの呼び出しコンテキスト][cf-img-call-context]

7. コンテキスト パネルが更新され、メソッド呼び出しが成功したことが表示されます。 呼び出しの結果として更新された圧力ノードの値を読み取ることで、呼び出しが成功したことを確認できます。

    ![コネクテッド ファクトリ事前構成済みソリューションの呼び出しの成功][cf-img-call-success]


## <a name="behind-the-scenes"></a>バックグラウンド処理

事前構成済みソリューションをデプロイすると、デプロイ プロセスにより、選択した Azure サブスクリプションに複数のリソースが作成されます。 これらのリソースを [Azure Portal][lnk-portal] で表示できます。 デプロイ プロセスにより、事前構成済みソリューション用に選択した名前に基づいた名前で、 **リソース グループ** が作成されます。

![Preconfigured solution in the Azure portal][img-cf-portal]

各リソースの設定を表示するには、リソース グループ内のリソースのリストでそれを選択します。

また、事前構成済みソリューションのソース コードを表示することもできます。 コネクテッド ファクトリ事前構成済みソリューションのソース コードは、[azure-iot-connected-factory][lnk-cfgithub] GitHub リポジトリにあります。

完了したら、[azureiotsuite.com][lnk-azureiotsuite] で事前構成済みソリューションを Azure サブスクリプションから削除できます。 このサイトでは、構成済みのソリューションを作成したときにプロビジョニングされていたすべてのリソースを簡単に削除することができます。

> [!NOTE]
> 事前構成済みソリューションに関連するすべての要素を確実に削除するには、[azureiotsuite.com][lnk-azureiotsuite] サイトで事前構成済みソリューションを削除してください。 ポータルでリソース グループを削除しないようにしてください。

## <a name="next-steps"></a>次のステップ

これで、事前構成済みの実際のソリューションをデプロイできました。引き続き IoT Suite の概要について学習するには、次の記事を参照してください。

* [コネクテッド ファクトリ事前構成済みソリューションのチュートリアル][lnk-rm-walkthrough]
* [デバイスをコネクテッド ファクトリ事前構成済みソリューションに接続する][lnk-connect-cf]
* [azureiotsuite.com サイトでのアクセス許可][lnk-permissions]

[img-cf-home]:media/iot-suite-connected-factory-overview/cf-dashboard.png
[img-launch-solution]: media/iot-suite-connected-factory-overview/launch-cf.png
[cf-img-menu]: media/iot-suite-connected-factory-overview/cf-dashboard-menu.png
[cf-img-factories]:media/iot-suite-connected-factory-overview/cf-dashboard-factory.png
[cf-img-map]:media/iot-suite-connected-factory-overview/cf-dashboard-map.png
[cf-img-alerts]:media/iot-suite-connected-factory-overview/cf-dashboard-alerts.png
[cf-img-oee]:media/iot-suite-connected-factory-overview/cf-dashboard-oee.png
[cf-img-kpi]:media/iot-suite-connected-factory-overview/cf-dashboard-kpi.png
[cf-img-tsi-visualization]:media/iot-suite-connected-factory-overview/cf-dashboard-tsi.png
[cf-img-tsi-explorer]:media/iot-suite-connected-factory-overview/tsi-explorer.png
[cf-img-server-browser]: media/iot-suite-connected-factory-overview/cf-dashboard-browser.png
[cf-img-server-choice]: media/iot-suite-connected-factory-overview/cf-select-server.png
[cf-img-server-tree]:media/iot-suite-connected-factory-overview/cf-server-tree.png
[cf-img-publish-node]:media/iot-suite-connected-factory-overview/cf-publish-node1.png
[cf-img-publish-success]:media/iot-suite-connected-factory-overview/cf-publish-success.png
[cf-img-call-command]:media/iot-suite-connected-factory-overview/cf-command-and-control-call.png
[cf-img-call-context]:media/iot-suite-connected-factory-overview/cf-command-and-control-call-button.png
[cf-img-call-success]:media/iot-suite-connected-factory-overview/cf-command-and-control-succeed.png
[img-cf-portal]:media/iot-suite-connected-factory-overview/cf-resource-group.png
[cf-img-alert-filter]:media/iot-suite-connected-factory-overview/cf-filter.png
[cf-img-alert-filter-funnel]:media/iot-suite-connected-factory-overview/cf-filter-funnel.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-portal]: http://portal.azure.com/
[lnk-cfgithub]: https://github.com/Azure/azure-iot-connected-factory
[lnk-rm-walkthrough]: iot-suite-connected-factory-sample-walkthrough.md
[lnk-connect-cf]: iot-suite-connected-factory-gateway-deployment.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-faq]: iot-suite-faq.md
