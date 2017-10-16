---
title: "サービス マップ ソリューションの自習デモ | Microsoft Docs"
description: "サービス マップは、Windows および Linux システム上のアプリケーション コンポーネントを自動的に検出し、サービス間の通信をマップする、Operations Management Suite (OMS) のソリューションです。  これは、サービス マップを実際に使用して、Web アプリケーションでシミュレートされた問題を特定および診断する自習デモです。"
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 9dc437b9-e83c-45da-917c-cb4f4d8d6333
ms.service: operations-management-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: bwren
ms.openlocfilehash: c3548d24c74f8ad865b22d6af3490d0b5cc77a84
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="operations-management-suite-oms-self-paced-demo---service-map"></a>Operations Management Suite (OMS) の自習デモ - サービス マップ
これは、Operations Management Suite (OMS) の[サービス マップ ソリューション](operations-management-suite-service-map.md)を実際に使用して、Web アプリケーションでシミュレートされた問題を特定および診断する自習デモです。  サービス マップは、Windows および Linux システムのアプリケーション コンポーネントを自動的に検出し、サービス間の通信をマップします。  また、パフォーマンスの分析や問題の特定に活かせるように、他の OMS サービスによって収集されたデータを統合します。  根本原因を特定するために、[Log Analytics のログ検索](../log-analytics/log-analytics-log-searches.md)を使用して、収集したデータのドリル ダウンも行います。


## <a name="scenario-description"></a>シナリオの説明
ACME Customer Portal アプリケーションにパフォーマンスの問題があるという通知を受け取りました。  わかっているのは、問題が本日の午前 4:00 (太平洋標準時) ごろに始まったということだけです。  一連の Web サーバー以外に、ポータルが依存しているコンポーネントは、完全には把握できていません。  

## <a name="components-and-features-used"></a>使用されるコンポーネントと機能
- [サービス マップ ソリューション](operations-management-suite-service-map.md)
- [Log Analytics のログ検索](../log-analytics/log-analytics-log-searches.md)


## <a name="walk-through"></a>チュートリアル

### <a name="1-connect-to-the-oms-experience-center"></a>1.OMS Experience Center に接続する
このチュートリアルでは、サンプル データを含む完全な OMS 環境を提供する [Operations Management Suite Experience Center](https://experience.mms.microsoft.com/) を使用します。 まず、このリンクに従って、情報を提供し、**[Insight and Analytics]** シナリオを選択します。


### <a name="2-start-service-map"></a>2.サービス マップを開始する
**[サービス マップ]** タイルをクリックして、サービス マップ ソリューションを開始します。

![[サービス マップ] タイル](media/operations-management-suite-walkthrough-servicemap/tile.png)

サービス マップ コンソールが表示されます。  左側のウィンドウには、サービス マップ エージェントがインストールされている、環境内のコンピューターの一覧が表示されます。  この一覧から表示するコンピューターを選択します。

![コンピューターの一覧](media/operations-management-suite-walkthrough-servicemap/computer-list.png)


### <a name="3-view-computer"></a>3.コンピューターを表示する
Web サーバーが AcmeWFE001 および AcmeWFE002 という名前であることはわかっているので、そこから始めるのが適切でしょう。  **[AcmeWFE001]** をクリックします。  AcmeWFE001 とそのすべての依存関係のマップが表示されます。  選択したコンピューターでどのプロセスが実行されていて、どの外部サービスと通信しているかがわかります。

![Web サーバー](media/operations-management-suite-walkthrough-servicemap/web-server.png)

気になっているのは Web アプリケーションのパフォーマンスなので、**[AcmeAppPool (IIS アプリケーション プール)]** プロセスをクリックします。  このプロセスの詳細が表示され、その依存関係が強調表示されます。  

![アプリケーション プール](media/operations-management-suite-walkthrough-servicemap/app-pool.png)


### <a name="4-change-time-window"></a>4.時間枠を変更する

問題は午前 4:00 に始まったということだったので、その時刻に何が起きたかを確認しましょう。 **[時間の範囲]** をクリックして、時刻を太平洋標準時の午前 4:00 に変更します (現在の日付は維持し、自分のローカル タイム ゾーンに調整します)。期間は 20 分にします。

![時刻の選択](./media/operations-management-suite-walkthrough-servicemap/time-picker.png)


### <a name="5-view-alert"></a>5.アラートを表示する

**acmetomcat** の依存関係でアラートが表示されていることがわかったので、ここに問題がある可能性があります。  **acmetomcat** のアラート アイコンをクリックして、アラートの詳細を表示します。  CPU 使用率が非常に高くなっていることがわかります。それを展開して、詳細を確認できます。  これがパフォーマンスの低下の原因だと考えられます。 

![アラート:](./media/operations-management-suite-walkthrough-servicemap/alert.png)


### <a name="6-view-performance"></a>6.パフォーマンスを表示する

**acmetomcat** を詳しく調べてみましょう。  **acmetomcat** の右上をクリックし、**[サーバー マップを読み込む]** を選択して、このコンピューターの詳細と依存関係を表示します。 その後、これらのパフォーマンス カウンターをもう少し詳しく調べて、疑念を検証することができます。  **[パフォーマンス]** タブを選択し、時間の範囲内に [Log Analytics によって収集されたパフォーマンス カウンター](../log-analytics/log-analytics-data-sources-performance-counters.md)を表示します。  プロセッサとメモリの使用が定期的に急増していることがわかります。

![パフォーマンス](./media/operations-management-suite-walkthrough-servicemap/performance.png)


### <a name="7-view-change-tracking"></a>7.変更の履歴を表示する
この高使用率の原因を特定できるか試してみましょう。  **[概要]** タブをクリックします。OMS がコンピューターから収集した情報 (失敗した接続、重大なアラート、ソフトウェアの変更など) が表示されます。  気になる最新の情報が含まれているセクションは既に展開されていますが、他のセクションを展開してその情報を調べることもできます。


まだ **[変更履歴]** が開かれていない場合は、展開してください。  ここには、[変更履歴ソリューション](../log-analytics/log-analytics-change-tracking.md)によって収集された情報が表示されます。  この期間内に、ソフトウェアの変更が行われたようです。  **[ソフトウェア]** をクリックして、詳細を取得します。  午前 4:00 になった直後にバックアップ プロセスがコンピューターに追加されているので、これがリソースの過剰消費の原因であると思われます。

![変更の追跡](./media/operations-management-suite-walkthrough-servicemap/change-tracking.png)



### <a name="8-view-details-in-log-search"></a>8.ログ検索で詳細を表示する
Log Analytics リポジトリに収集された詳細なパフォーマンス情報を調べて、このことをさらに検証することができます。  **[アラート]** タブをもう一度クリックし、**[High CPU (高 CPU)]** のアラートのいずれかをクリックします。  **[ログ検索で表示]** をクリックします。  [ログ検索] ウィンドウが開かれます。ここで、リポジトリに格納されている任意のデータに対する[ログ検索](../log-analytics/log-analytics-log-searches.md)を行うことができます。  必要なアラートを取得するためのクエリが、サービス マップによって既に入力されています。  

![ログ検索](./media/operations-management-suite-walkthrough-servicemap/log-search.png)


### <a name="9-open-saved-search"></a>9.保存された検索を開く
このアラートを生成したパフォーマンスのコレクションに関してさらに詳細を入手できるかどうかを確認し、このバックアップ プロセスによって問題が発生しているのではないかという疑念を検証しましょう。  時間の範囲を **6 時間**に変更します。  **[お気に入り]** をクリックし、**[サービス マップ]** の保存された検索まで下へスクロールします。  これらは、我々がこの分析のために特別に作成したクエリです。  **[Top 5 Processes by CPU for acmetomcat (acmetomcat の CPU の上位 5 プロセス)]** をクリックします。

![保存された検索条件](./media/operations-management-suite-walkthrough-servicemap/saved-search.png)


このクエリは、**acmetomcat** 上のプロセッサを使用している上位 5 プロセスの一覧を返します。  クエリを調べて、ログ検索に使用されるクエリ言語の基礎的な情報を得ることができます。  他のコンピューター上のプロセスを調べたい場合は、その情報を取得するようにクエリを変更できます。

ここでは、バックアップ プロセスがアプリ サーバーの CPU の約 60% を常に使用していることがわかります。  この新しいプロセスがパフォーマンスの問題の原因になっていることは、ほぼ確実です。  解決策は、当然、この新しいバックアップ ソフトウェアをアプリケーション サーバーから削除することです。  実際に、我々は、Azure Automation によって管理されている Desired State Configuration (DSC) を利用して、このプロセスがこれらの重要なシステム上で実行されないようにするポリシーを定義することができました。


## <a name="summary-points"></a>まとめ
- [サービス マップ](operations-management-suite-service-map.md)では、サーバーと依存関係のすべてはわからない場合でも、アプリケーション全体を視覚化することができます。
- サービス マップは、他の OMS ソリューションによって収集されたデータを示すので、アプリケーションとその基になるインフラストラクチャの問題を特定するために役立ちます。
- [ログ検索](../log-analytics/log-analytics-log-searches.md)を使用すると、Log Analytics リポジトリに収集された特定のデータにドリルダウンできます。    

## <a name="next-steps"></a>次のステップ
- [サービス マップ](operations-management-suite-service-map.md)の詳細について学習します。
- サービス マップを[デプロイおよび構成](operations-management-suite-service-map-configure.md)します。
- エージェントによって格納されている運用データを格納する、サービス マップに必要な [Log Analytics](../log-analytics/log-analytics-overview.md) について学習します。