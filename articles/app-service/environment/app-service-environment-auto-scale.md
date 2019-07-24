---
title: 自動スケーリングと App Service Environment v1 - Azure
description: 自動スケールと App Service Environment
services: app-service
documentationcenter: ''
author: btardif
manager: erikre
editor: ''
ms.assetid: c23af2d8-d370-4b1f-9b3e-8782321ddccb
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 29a639142395c43fea06c1d6d18909b3c9f33b86
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53270027"
---
# <a name="autoscaling-and-app-service-environment-v1"></a>自動スケールと App Service Environment v1

> [!NOTE]
> この記事は、App Service Environment v1 に関するものです。  より強力なインフラストラクチャ上で実行できる、使いやすい新しいバージョンの App Service Environment があります。 新しいバージョンの詳細については、「[App Service Environment の概要](intro.md)」を参照してください。
> 

Azure App Service 環境は " *自動スケール*" をサポートしています。 メトリックまたはスケジュールに基づいて個々のワーカー プールの規模を自動調整することができます。

![Autoscale options for a worker pool.][intro]

自動スケールでは、予算または負荷プロファイルに適合するように App Service 環境を自動的に拡大または縮小することにより、リソースの使用率が最適化されます。

## <a name="configure-worker-pool-autoscale"></a>ワーカー プールの自動スケールの構成
自動スケール機能には、ワーカー プールの **[設定]** タブからアクセスできます。

![Settings tab of the worker pool.][settings-scale]

そこからは、App Service プランのスケールを行う場合と同じ操作となるので、非常になじみの深いインターフェイスとなります。 

![Manual scale settings.][scale-manual]

また、自動スケール プロファイルを構成することもできます。

![Autoscale settings.][scale-profile]

自動スケール プロファイルは、スケールに対して制限を設定するのに便利です。 このように、スケールの下限値 (1) を設定してパフォーマンスの状態を安定させると共に、スケールの上限値 (2) を設定して使用上限を予測可能とすることができます。

![Scale settings in profile.][scale-profile2]

プロファイルを定義したら、自動スケール ルールを追加して、ワーカー プール内のインスタンスの数をプロファイルで定義されている範囲内で増減することができます。 自動スケール ルールには、メトリックが使用されます。

![Scale rule.][scale-rule]

 ワーカー プールまたはフロントエンドの任意のメトリックを使用して、自動スケール ルールを定義することができます。 これらのメトリックは、リソース ブレード グラフで監視したり、アラートを設定したりできるメトリックと同じものです。

## <a name="autoscale-example"></a>自動スケールの例
App Service 環境の自動スケールの説明は、シナリオを通して行うのが最も効果的です。

この記事では、自動スケールを設定するときに必要なすべての考慮事項を説明します。 この記事では、App Service Environment でホストされている App Service 環境の自動スケールを考慮に入れる場合に必要なあらゆるやり取りについて説明します。

### <a name="scenario-introduction"></a>シナリオの概要
Frank は企業の sysadmin です。彼は管理を担当しているワークロードの一部を App Service 環境に移行しました。

App Service 環境は、次のように手動で拡張するように構成されています。

* **フロントエンド:** 3
* **ワーカー プール 1**:10
* **ワーカー プール 2**:5
* **ワーカー プール 3**:5

ワーカー プール 1 は運用環境のワークロードとして使用されます。一方、ワーカー プール 2 とワーカー プール 3 は品質保証 (QA) および開発用のワークロードとして使用されます。

QA および開発用の App Service プランは、手動スケールに構成されます。 この運用環境の App Service プランは、負荷とトラフィックの変動に対処するために自動スケールに設定されます。

Frank は、アプリケーションに関する知識がきわめて豊富です。 このアプリケーションは従業員がオフィスにいる間に使用する基幹業務 (LOB) アプリケーションであることから、負荷がピークとなる時間帯は 9:00 AM ～ 6:00 PM であることがわかっています。 その後、ユーザーがその日の業務を終了すると、使用率は低下します。 アプリにはユーザーがリモートからモバイル デバイスまたは自宅の PC を使ってアクセスできるので、ピーク時間外にも負荷は発生します。 運用環境の App Service プランは、次のルールを使用して CPU 使用率に基づき自動スケールを行うように構成済みです。

![Specific settings for LOB app.][asp-scale]

| **自動スケール プロファイル – 平日 – App Service プラン** | **自動スケール プロファイル – 週末 – App Service プラン** |
| --- | --- |
| **名前:** 平日のプロファイル |**名前:** 週末のプロファイル |
| **スケール:** スケジュールおよびパフォーマンスのルール |**スケール:** スケジュールおよびパフォーマンスのルール |
| **プロファイル:** 平日の日中 |**プロファイル:** 週末 |
| **種類:** 繰り返し |**種類:** 繰り返し |
| **対象範囲:** 5 ～ 20 のインスタンス |**対象範囲:** 3 ～ 10 のインスタンス |
| **日:** 月曜日、火曜日、水曜日、木曜日、金曜日 |**日:** 土曜日、日曜日 |
| **開始時刻:** 9:00 AM |**開始時刻:** 9:00 AM |
| **タイム ゾーン:** UTC-08 |**タイム ゾーン:** UTC-08 |
|  | |
| **自動スケール ルール (スケールアップ)** |**自動スケール ルール (スケールアップ)** |
| **リソース:** 運用環境 (App Service 環境) |**リソース:** 運用環境 (App Service 環境) |
| **メトリック:** CPU % |**メトリック:** CPU % |
| **操作:** 60% 超過 |**操作:** 80% 超過 |
| **期間:** 5 分 |**期間:** 10 分 |
| **時間の集計:** 平均 |**時間の集計:** 平均 |
| **アクション:** カウント数を 2 ずつ増加 |**アクション:** カウント数を 1 ずつ増加 |
| **クールダウン (分):** 15 |**クールダウン (分):** 20 |
|  | |
| **自動スケール ルール (スケールダウン)** |**自動スケール ルール (スケールダウン)** |
| **リソース:** 運用環境 (App Service 環境) |**リソース:** 運用環境 (App Service 環境) |
| **メトリック:** CPU % |**メトリック:** CPU % |
| **操作:** 30% 未満 |**操作:** 20% 未満 |
| **期間:** 10 分 |**期間:** 約 15 分 |
| **時間の集計:** 平均 |**時間の集計:** 平均 |
| **アクション:** カウント数を 1 ずつ減少 |**アクション:** カウント数を 1 ずつ減少 |
| **クールダウン (分):** 20 |**クールダウン (分):** 10 |

### <a name="app-service-plan-inflation-rate"></a>App Service プランの増加率
自動スケールを行うように構成された App Service プランは、1 時間あたりの最大レートで処理を行います。 このレートは、自動スケール ルールで指定された値に基づいて算出できます。

ワーカー プールに対するスケールの変更は瞬時に適用されるわけではないので、App Service 環境の自動スケールでは " *App Service プランの増加率* " を理解し計算することが重要となります。

App Service プランの増加率は次のように計算します。

![App Service plan inflation rate calculation.][ASP-Inflation]

運用環境の App Service プランの平日プロファイルに対する自動スケール - スケールアップ ルールに基づくと、計算式は次のようになります。

![App Service plan inflation rate for weekdays based on Autoscale – Scale Up rule.][Equation1]

運用環境の App Service プランの週末プロファイルに対する自動スケール - スケールアップ ルールに基づく場合、計算式は次のようになります。

![App Service plan inflation rate for weekends based on Autoscale – Scale Up rule.][Equation2]

この値は、スケールダウン操作についても計算できます。

運用環境の App Service プランの平日プロファイルに対する自動スケール - スケールダウン ルールに基づくと、計算式は次のようになります。

![App Service plan inflation rate for weekdays based on Autoscale – Scale Down rule.][Equation3]

運用環境の App Service プランの週末プロファイルに対する自動スケール - スケールダウン ルールに基づく場合、計算式は次のようになります。  

![App Service plan inflation rate for weekends based on Autoscale – Scale Down rule.][Equation4]

運用環境の App Service プランは、平日は 1 時間あたり 8 個のインスタンスという最大レートで成長することができ、週末は 1 時間あたり 4 個のインスタンスという最大レートで成長することができます。 平日は 1 時間あたり 4 個のインスタンスという最大レートでインスタンスをリリースし、週末は 1 時間あたり、6 個のインスタンスという最大レートでインスタンスをリリースできます。

複数の App Service プランを 1 つのワーカー プールでホストする場合は、そのワーカー プールでホストされるすべての App Service プランの増加率の合計として " *合計増加率* " を計算する必要があります。

![Total inflation rate calculation for multiple App Service plans hosted in a worker pool.][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>App Service プランの増加率を使用してワーカー プールの自動スケール ルールを定義する
自動スケールを行うように構成された App Service プランのホストとなるワーカー プールには、容量のバッファーを割り当てる必要があります。 自動スケール操作で App Service プランを必要に応じて拡大したり縮小したりするために別途必要となる容量が、このバッファーによって確保されます。 最小バッファーは、計算した App Service プラン増加率合計となります。

App Service 環境のスケール操作は適用されるのに多少時間がかかるため、何らかの変更を行う場合は、スケール操作の進行中は起こりうる需要の変化を考慮する必要があります。 この時間差に対応するため、計算した App Service プラン増加率合計を、自動スケール操作ごとに追加される最小インスタンス数として使用することをお勧めします。

Frank は、この情報を使用して次の自動スケール プロファイルとルールを定義することができます。

![Autoscale profile rules for LOB example.][Worker-Pool-Scale]

| **自動スケール プロファイル – 平日** | **自動スケール プロファイル – 週末** |
| --- | --- |
| **名前:** 平日のプロファイル |**名前:** 週末のプロファイル |
| **スケール:** スケジュールおよびパフォーマンスのルール |**スケール:** スケジュールおよびパフォーマンスのルール |
| **プロファイル:** 平日の日中 |**プロファイル:** 週末 |
| **種類:** 繰り返し |**種類:** 繰り返し |
| **対象範囲:** 13 ～ 25 のインスタンス |**対象範囲:** 6 ～ 15 のインスタンス |
| **日:** 月曜日、火曜日、水曜日、木曜日、金曜日 |**日:** 土曜日、日曜日 |
| **開始時刻:** 7:00 AM |**開始時刻:** 9:00 AM |
| **タイム ゾーン:** UTC-08 |**タイム ゾーン:** UTC-08 |
|  | |
| **自動スケール ルール (スケールアップ)** |**自動スケール ルール (スケールアップ)** |
| **リソース:** ワーカー プール 1 |**リソース:** ワーカー プール 1 |
| **メトリック:** WorkersAvailable |**メトリック:** WorkersAvailable |
| **操作:** 8 未満 |**操作:** 3 未満 |
| **期間:** 20 分 |**期間:** 30 分 |
| **時間の集計:** 平均 |**時間の集計:** 平均 |
| **アクション:** カウント数を 8 ずつ増加 |**アクション:** カウント数を 3 ずつ増加 |
| **クールダウン (分):** 180 |**クールダウン (分):** 180 |
|  | |
| **自動スケール ルール (スケールダウン)** |**自動スケール ルール (スケールダウン)** |
| **リソース:** ワーカー プール 1 |**リソース:** ワーカー プール 1 |
| **メトリック:** WorkersAvailable |**メトリック:** WorkersAvailable |
| **操作:** 8 超過 |**操作:** 3 超過 |
| **期間:** 20 分 |**期間:** 約 15 分 |
| **時間の集計:** 平均 |**時間の集計:** 平均 |
| **アクション:** カウント数を 2 ずつ減少 |**アクション:** カウント数を 3 ずつ減少 |
| **クールダウン (分):** 120 |**クールダウン (分):** 120 |

プロファイルで定義される対象範囲は、App Service プランのプロファイルで定義された最小インスタンス + バッファーによって算出されます。

最大範囲は、ワーカー プールでホストされているすべての App Service プランのすべての最大範囲の合計になります。

スケールアップ ルールの増加数は、少なくとも 1X スケールアップの App Service プランの増加率に設定する必要があります。

減少数は、1/2X スケールダウンの App Service プラン増加率と 1X スケールダウンの App Service プラン増加率との間のいずれかの値に調整することができます。

### <a name="autoscale-for-front-end-pool"></a>フロントエンド プールの自動スケール
フロントエンドの自動スケール ルールはワーカー プールの場合よりも簡単です。 主に  
確認しておくべき点は、測定およびクールダウン タイマーの期間を設定する際には App Service プランに対するスケール操作が瞬間的なものではないということを必ず考慮に入れる必要があるということです。

このシナリオで、フランクはフロントエンドが 80% の CPU 使用率に到達すると、エラー率が増大することを認識し、自動スケール ルールを設定して以下のようにインスタンス数を増やしています。

![Autoscale settings for front-end pool.][Front-End-Scale]

| **自動スケール プロファイル – フロントエンド** |
| --- |
| **名前:** 自動スケーリング – フロントエンド |
| **スケール:** スケジュールおよびパフォーマンスのルール |
| **プロファイル:** 毎日 |
| **種類:** 繰り返し |
| **対象範囲:** 3 ～ 10 のインスタンス |
| **日:** 毎日 |
| **開始時刻:** 9:00 AM |
| **タイム ゾーン:** UTC-08 |
|  |
| **自動スケール ルール (スケールアップ)** |
| **リソース:** フロントエンド プール |
| **メトリック:** CPU % |
| **操作:** 60% 超過 |
| **期間:** 20 分 |
| **時間の集計:** 平均 |
| **アクション:** カウント数を 3 ずつ増加 |
| **クールダウン (分):** 120 |
|  |
| **自動スケール ルール (スケールダウン)** |
| **リソース:** ワーカー プール 1 |
| **メトリック:** CPU % |
| **操作:** 30% 未満 |
| **期間:** 20 分 |
| **時間の集計:** 平均 |
| **アクション:** カウント数を 3 ずつ減少 |
| **クールダウン (分):** 120 |

<!-- IMAGES -->
[intro]: ./media/app-service-environment-auto-scale/introduction.png
[settings-scale]: ./media/app-service-environment-auto-scale/settings-scale.png
[scale-manual]: ./media/app-service-environment-auto-scale/scale-manual.png
[scale-profile]: ./media/app-service-environment-auto-scale/scale-profile.png
[scale-profile2]: ./media/app-service-environment-auto-scale/scale-profile-2.png
[scale-rule]: ./media/app-service-environment-auto-scale/scale-rule.png
[asp-scale]: ./media/app-service-environment-auto-scale/asp-scale.png
[ASP-Inflation]: ./media/app-service-environment-auto-scale/asp-inflation-rate.png
[Equation1]: ./media/app-service-environment-auto-scale/equation1.png
[Equation2]: ./media/app-service-environment-auto-scale/equation2.png
[Equation3]: ./media/app-service-environment-auto-scale/equation3.png
[Equation4]: ./media/app-service-environment-auto-scale/equation4.png
[ASP-Total-Inflation]: ./media/app-service-environment-auto-scale/asp-total-inflation-rate.png
[Worker-Pool-Scale]: ./media/app-service-environment-auto-scale/wp-scale.png
[Front-End-Scale]: ./media/app-service-environment-auto-scale/fe-scale.png
