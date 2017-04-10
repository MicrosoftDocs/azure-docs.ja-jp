---
title: "予測的なメンテナンスの構成済みソリューション | Microsoft Docs"
description: "Azure IoT Suite の予測的なメンテナンスの構成済みソリューションの説明。"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: b370b3d7-2ce5-4906-9818-3aeedd471ee3
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: caecd0d6e546d4ddef71ebeee6fad3a5c7ba6451
ms.openlocfilehash: 5447dff1ff9aea1b90f7e18499f795e7c173e2b9
ms.lasthandoff: 02/01/2017


---
# <a name="predictive-maintenance-preconfigured-solution-overview"></a>予測的なメンテナンスの構成済みソリューションの概要

"*予測的なメンテナンス*" の[構成済みのソリューション][lnk_preconfigured_solutions]は、[Microsoft Azure IoT Suite][lnk_iot_suite] の構成済みソリューションの&1; つです。 このソリューションは、リアルタイムでのデバイスのテレメトリの収集と [Azure Machine Learning][lnk-machine-learning] を使用して作成された予測モデルを統合します。

Azure IoT Suite を使用することで、企業は資産にすばやく簡単に接続して資産を監視したり、リアルタイムでデータを分析したりできるようになります。 予測的なメンテナンスの構成済みソリューションは、そのデータを取り込み、豊富なダッシュボードと視覚エフェクトを使用して、新しいインテリジェンスを提供します。それを利用して、効率化を推進し、収益ストリームを強化することができます。

## <a name="the-scenario"></a>シナリオ
Fabrikam は、競争力のある価格での優れたカスタマー エクスペリエンスの提供に取り組んでいる地域航空会社です。 フライトの遅延の原因の&1; つは、メンテナンスです。また、航空機のエンジンのメンテナンスは特に困難です。 飛行中のエンジンの故障は、いかなる代価を払っても避ける必要があります。そのため、Fabrikam では定期的にエンジンを検査し、予定されたメンテナンス プログラムを確実に実行しています。 ただし、航空機のエンジンがすべて同じように損耗するわけではありません。 必要のないメンテナンスも一部、エンジンに対し実行されています。 さらに重要なことは、問題が発生すると、メンテナンスが実行されるまでは、航空機は飛行禁止になるということです。 このような問題では、適切な技術者がいなかったり予備の部品がなかったりする場所に航空機がある場合、特に費用のかかる遅延が発生します。

Fabrikam の航空機のエンジンは、飛行中のエンジンの状態を監視するセンサーを搭載しています。 Fabrikam は、予測的なメンテナンスの構成済みソリューションを使用して、フライト中に収集されるセンサー データを集めます。 長年にわたり、エンジンの運用データと障害データを蓄積した後、Fabrikam のデータ サイエンティストは、航空機エンジンの残存耐用年数 (RUL) を予測する方法をモデル化しました。 データ サイエンティストが特定したのは、4 つのエンジン センサーからのデータと障害につながるエンジンの損耗との相関関係です。 Fabrikam は、安全性を確保するための通常の検査を継続しつつ、このモデルにより、毎回の飛行後にそれぞれのエンジンの RUL を計算できるようになりました。 このモデルは、フライト中にエンジンから収集されたテレメトリを使用します。 そのため、Fabrikam は、将来における障害の時期を予測し、事前にメンテナンスおよび修復のための計画を立てることができるようになりました。

> [!NOTE]
> ソリューションのモデルでは、実際のエンジンの損耗データが使用されます。

メンテナンスが必要な時期を予測することで、Fabrikam は、運用状況を最適化し、コストを削減することができます。 メンテナンス コーディネーターは、スケジュール管理者と連携し、以下の作業を行います。

- 特定の場所での航空機の休止に合わせてメンテナンスを計画します。
- スケジュールの乱れを発生させずに航空機を運休できるだけの十分な時間を確保します。
- 待ち時間なしで効率的に航空機の点検が実行されるように技術者のスケジュールを決定します。

在庫管理マネージャーは、メンテナンスの計画を受け取ることで、注文処理と予備部品の在庫を最適化できます。 このようにして、Fabrikam は航空機の飛行禁止時間を最小限に抑え、乗客と乗組員の安全を確保しながら、運用コストを削減することができます。

予測的なメンテナンスの潜在力を実現するためにお客様が必要とする機能を [Azure IoT Suite][lnk_iot_suite] がどのように提供しているかについては、この[インフォグラフィック][lnk_infographic]を参照してください。

## <a name="how-the-predictive-maintenance-solution-is-built"></a>予測的なメンテナンス ソリューションの構築方法

IoT Suite サービスを通じて収集されたデバイスのテレメトリを使用するこれらの機能を示すために、ソリューションでは、テンプレートとして利用可能な既存の Azure Machine Learning モデルを使用しています。 Microsoft は、公開されているデータ<sup>\[1\]</sup> を基にして航空機エンジンの[回帰モデル][lnk_regression_model]と、モデルの使用方法に関するステップ バイ ステップ ガイダンスを作成しました。

Azure IoT の予測的メンテナンスの構成済みソリューションは、このテンプレートから作成された回帰モデルを使用します。 モデルは、Azure サブスクリプションにデプロイされ、自動的に生成される API を通じて公開されます。 ソリューションには、4 つのエンジン (合計 100) および 4 つのセンサー データ ストリーム (合計 21) を表すテスト データのサブセットが含まれます。 このデータは、トレーニング済みのモデルから正確な結果を得るには十分な量です。

*\[1\] A. Saxena と K. Goebel (2008 年)。「Turbofan Engine Degradation Simulation Data Set (ターボファン エンジンの劣化シミュレーション データ セット)」、NASA Ames Prognostics Data Repository (http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/)、NASA Ames Research Center、カリフォルニア州モフェット フィールド*

## <a name="get-started-with-predictive-maintenance"></a>予測的なメンテナンスを使ってみる

このチュートリアルでは、予測的なメンテナンス ソリューションをプロビジョニングする方法を示します。 また、予測的なメンテナンス ソリューションの基本機能についても詳しく説明します。 こうした機能の多くには、事前構成済みソリューションと一緒にデプロイされるソリューション ダッシュボードを通じてアクセスできます。

このチュートリアルを完了するには、アクティブな Azure サブスクリプションが必要になります。

> [!NOTE]
> アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、[Azure の無料試用版サイト][lnk_free_trial]をご覧ください。

1. Azure アカウントの資格情報を使用して [azureiotsuite.com][lnk-azureiotsuite] にログオンし、**[+]** をクリックしてソリューションを作成します。
1. **[Predictive maintenance (予測的なメンテナンス)]** タイルを**選択**します。
1. 予測的なメンテナンスの事前構成済みソリューションの**ソリューション名**を入力します。
1. ソリューションのプロビジョニングに使用する**リージョン**と**サブスクリプション**を選択します。
1. **[ソリューションの作成]** をクリックして、プロビジョニング プロセスを開始します。 通常、このプロセスの実行までに数分かかります。

### <a name="wait-for-the-provisioning-process-to-complete"></a>プロビジョニング プロセスが完了するまで待機します。

1. **プロビジョニング** の状態を表示する、目的のソリューションのタイルをクリックします。
1. Azure サービスが Azure サブスクリプションにデプロイされたら、 **プロビジョニングの状態** を確認します。
1. プロビジョニングが完了すると、状態が **[準備完了]**に変わります。
1. タイルをクリックし、右側のウィンドウでソリューションの詳細を確認します。 このウィンドウでは、ソリューション ダッシュボードを起動し、Machine Learning ワークスペースにアクセスすることができます。

> [!NOTE]
> 構成済みソリューションのデプロイの問題が発生している場合は、「[azureiotsuite.com サイトでのアクセス許可][lnk-permissions]」と [FAQ][lnk-faq] を参照してください。 それでも問題が解決しない場合は、[ポータル][lnk-portal]でサービス チケットを作成してください。

ソリューションの一覧に予測どおりに表示されない詳細がありますか? [ユーザーの声](https://feedback.azure.com/forums/321918-azure-iot)のページで機能に関する提案をお寄せください。

## <a name="view-the-solution"></a>ソリューションの表示

このセクションでは、ソリューションの UI について説明します。

### <a name="predictive-maintenance-dashboard"></a>予測的なメンテナンスのダッシュボード
Web アプリケーションのこのページでは、PowerBI JavaScript コントロールを使用して ([PowerBI-visuals リポジトリ][lnk-powerbi]を参照)、以下を視覚化しています。

* Blob Storage 内の Stream Analytics ジョブから出力されたデータ。
* 各航空機エンジンの RUL とサイクル数。

### <a name="observing-the-behavior-of-the-cloud-solution"></a>クラウド ソリューションの動作を確認する
Azure ポータルで、指定したソリューション名の付いたリソース グループに移動して、プロビジョニングされたリソースを確認します。

![][img-resource-group]

構成済みのソリューションをプロビジョニングすると、Machine Learning ワークスペースへのリンクを含む電子メールを受信します。 この Machine Learning ワークスペースには、プロビジョニングしたソリューションの状態が**準備完了**である場合、そのソリューションの [azureiotsuite.com][lnk-azureiotsuite] ページのタイルから移動することも可能です。

![][img-machine-learning]

ソリューション ポータルでは、シミュレートされた&4; つのデバイスがサンプルにプロビジョニングされていることがわかります。2 機の航空機それぞれにエンジンが&2; つあり、エンジンごとにセンサーが&4; つあります。 ソリューション ポータルに最初に移動すると、シミュレーションは停止します。

![][img-simulation-stopped]

**[シミュレーションの開始]** をクリックすると、シミュレーションが開始され、センサーの履歴、RUL、サイクル数、RUL 履歴がダッシュボードに入力されます。

![][img-simulation-running]

RUL が (デモンストレーション用に選択した任意のしきい値である) 160 未満の場合、ソリューション ポータルには RUL の表示の横に警告記号が示され、航空機エンジンは黄色で強調表示されます。 RUL 値は、全体的には一般的な下降傾向を示していますが、上下する傾向があることがわかります。 この動きはさまざまな長さのサイクルとモデル精度が含まれていることの結果です。

![][img-simulation-warning]

148 サイクルの完全なシミュレーションの完了には約 35 分かかります。 160 の RUL のしきい値には約 5 分で最初に達し、両エンジンがしきい値に達するのは、約 8 分後です。

148 サイクルの完全なデータセットのシミュレーションが実行され、最終的な RUL とサイクル数が出されます。

シミュレーションは任意のポイントで停止できますが、**[Start Simulation (シミュレーションの開始)]** をクリックすると、データセットの先頭からシミュレーションが再実行されます。

## <a name="next-steps"></a>次のステップ

Azure IoT が予測的なメンテナンスのシナリオを実現する方法の詳細については、「[Capture value from the Internet of Things (モノのインターネットから価値を得る)][lnk_capture_value]」を参照してください。

予測的なメンテナンスの構成済みソリューションの[チュートリアル][lnk-predictive-walkthrough]をご利用ください。

IoT Suite の事前構成済みのソリューションの他の機能について学習できます。

* [IoT スイートに関してよく寄せられる質問][lnk-faq]
* [IoT の徹底的なセキュリティ][lnk-security-groundup]

[img-resource-group]: media/iot-suite-predictive-overview/resource-group.png
[img-simulation-stopped]: media/iot-suite-predictive-overview/simulation-stopped.png
[img-simulation-running]: media/iot-suite-predictive-overview/simulation-running.png
[img-simulation-warning]: media/iot-suite-predictive-overview/simulation-warning.png
[img-machine-learning]: media/iot-suite-predictive-overview/machine-learning.png

[lnk-powerbi]: https://www.github.com/Microsoft/PowerBI-visuals
[lnk-predictive-walkthrough]: iot-suite-predictive-walkthrough.md
[lnk_preconfigured_solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk_iot_suite]: iot-suite-overview.md
[lnk_infographic]: https://www.microsoft.com/server-cloud/predictivemaintenance/Index.html
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3

[lnk_capture_value]: http://download.microsoft.com/download/0/7/D/07D394CE-185D-4B96-AC3C-9B61179F7080/Capture_value_from_the_Internet%20of%20Things_with_Predictive_Maintenance.PDF
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-permissions]: iot-suite-permissions.md
[lnk-portal]: http://portal.azure.com/
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/
