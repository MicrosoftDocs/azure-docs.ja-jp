---
title: "Azure App Service でアプリを監視する | Microsoft Docs"
description: "Azure ポータルを使用して Azure App Service のアプリを監視する方法を説明します。"
services: app-service
documentationcenter: 
author: btardif
manager: erikre
editor: 
ms.assetid: d273da4e-07de-48e0-b99d-4020d84a425e
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2016
ms.author: byvinyal
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: d779170030b61540de2c0459e574a72cdc77f342
ms.lasthandoff: 02/16/2017


---
# <a name="how-to-monitor-apps-in-azure-app-service"></a>Azure App Service でアプリを監視する方法
[App Service](http://go.microsoft.com/fwlink/?LinkId=529714) では、組み込みの監視機能が [Azure ポータル](https://portal.azure.com)に用意されています。
この監視機能には、アプリと App Service プランの**クォータ**と**メトリック**を確認する機能、**アラート**のセットアップ、さらにこれらのメトリックに基づいた自動**スケール**が含まれています。

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="understanding-quotas-and-metrics"></a>クォータとメトリックについて
### <a name="quotas"></a>Quotas (クォータ)
App Service でホストされているアプリケーションは、使用できるリソースに一定の " *制限* " があります。 この制限は、アプリに関連付けられている **App Service プラン** によって定義されます。

アプリケーションが **Free** プランまたは **Shared** プランでホストされている場合、アプリで使用できるリソースの制限は**クォータ**によって定義されます。

アプリケーションが **Basic** プラン、**Standard** プラン、**Premium** プランでホストされている場合、アプリで使用できるリソースの制限は、**App Service** プランの**サイズ** (S、M、L) と**インスタンス数** (1、2、3...) によって設定されます。

**Free** アプリまたは **Shared** アプリの**クォータ**は、次のようになります。

* **CPU (ショート)**
  * このアプリケーションが 3 分ごとに使用できる CPU の量。 このクォータは、3 分ごとに再設定されます。
* **CPU (1 日)**
  * このアプリケーションが&1; 日に使用できる CPU の総量。 このクォータは、24 時間ごとに (UTC の午前 0 時に) 再設定されます。
* **メモリ**
  * このアプリケーションが使用できるメモリの総量。
* **帯域幅**
  * このアプリケーションが&1; 日に使用できる送信帯域幅の総量。
    このクォータは、24 時間ごとに (UTC の午前 0 時に) 再設定されます。
* **ファイルシステム**
  * 使用可能なストレージの総量。

**Basic** プラン、**Standard** プラン、**Premium** プランでホストされているアプリに適用できるクォータは、**ファイルシステム**のみです。

異なる App Service SKU で利用できる特定のクォータ、制限、機能の詳細については、こちらの [Azure サブスクリプション サービスの制限](../azure-subscription-service-limits.md#app-service-limits)

#### <a name="quota-enforcement"></a>クォータの適用
アプリケーションの使用量が **CPU (ショート)**、**CPU (1 日)**、**帯域幅**のクォータを超過すると、クォータが再設定されるまでアプリケーションは停止されます。 この停止期間中は、すべての受信要求の結果が **HTTP 403**になります。
![][http403]

アプリケーションで使用される **メモリ** がクォータを超過した場合、アプリケーションは再起動されます。

**ファイルシステム** がクォータを超過した場合、ログへの書き込みを含め、すべての書き込み操作が失敗するようになります。

クォータを引き上げたりアプリから削除したりするには、App Service プランをアップグレードしてください。

### <a name="metrics"></a>メトリック
**メトリック** を利用すると、アプリまたは App Service プランの動作に関する情報が得られます。

**アプリケーション**について利用できるメトリックには、次のものがあります。

* **平均応答時間**
  * アプリが要求に応答するのに要した平均時間 (ミリ秒)。
* **平均メモリ ワーキング セット**
  * アプリで使用された平均メモリ量 (MiB)。
* **CPU 時間**
  * アプリで消費された CPU の量 (秒)。 このメトリックの詳細については、「[CPU 時間と CPU の割合](#cpu-time-vs-cpu-percentage)」をご覧ください。
* **受信データ**
  * アプリで消費された受信帯域幅の量 (MiB)。
* **送信データ**
  * アプリで消費された送信帯域幅の量 (MiB)。
* **HTTP 2xx**
  * 200 以上 300 未満の HTTP 状態コードが結果として返された要求の数。
* **HTTP 3xx**
  * 300 以上 400 未満の HTTP 状態コードが結果として返された要求の数。
* **HTTP 401**
  * HTTP 401 状態コードが結果として返された要求の数。
* **HTTP 403**
  * HTTP 403 状態コードが結果として返された要求の数。
* **HTTP 404**
  * HTTP 404 状態コードが結果として返された要求の数。
* **HTTP 406**
  * HTTP 406 状態コードが結果として返された要求の数。
* **HTTP 4xx**
  * 400 以上 500 未満の HTTP 状態コードが結果として返された要求の数。
* **HTTP サーバー エラー**
  * 500 以上 600 未満の HTTP 状態コードが結果として返された要求の数。
* **メモリ ワーキング セット**
  * アプリで使用されている現在のメモリ量 (MiB)。
* **要求数**
  * 結果として返された HTTP 状態コードを問わない、要求の合計数。

**App Service プラン**について利用できるメトリックには、次のものがあります。

> [!NOTE]
> App Service プランのメトリックは、**Basic**、**Standard** および **Premium SKU** のプランでのみ利用できます。
> 
> 

* **CPU の割合**
  * プランの全インスタンスで使用された平均 CPU。
* **メモリの割合**
  * プランの全インスタンスで使用された平均メモリ。
* **受信データ**
  * プランの全インスタンスで使用された平均受信帯域幅。
* **送信データ**
  * プランの全インスタンスで使用された平均送信帯域幅。
* **ディスク キューの長さ**
  * ストレージのキューに登録された読み取り要求と書き込み要求の平均数。 ディスク キューが長い場合は、過剰なディスク I/O によってアプリケーションの速度が低下している可能性があることを示しています。
* **HTTP キューの長さ**
  * 処理される前にキューで待つ必要があった HTTP 要求の平均数。 HTTP キューが長いこと、または HTTP キューの長さが増加していることは、プランに大きな負荷がかかっているしるしです。

### <a name="cpu-time-vs-cpu-percentage"></a>CPU 時間と CPU の割合
<!-- To do: Fix Anchor (#CPU-time-vs.-CPU-percentage) -->

CPU の使用状況を反映するメトリックには、 **CPU 時間**と **CPU の割合**の&2; つがあります。

**CPU 時間**は、**Free** プランまたは **Shared** プランでホストされているアプリで役に立ちます。これは、これらのプランのクォータの&1; つが、アプリによって使用される CPU 時間 (分数) で定義されているためです。

一方、**CPU の割合**は、**Basic**、**Standard**、**Premium** のプランでホストされているアプリで役に立ちます。これらのアプリではスケールアウトが可能であり、このメトリックが全インスタンスの全体的な使用状況を示す優れた指標となるためです。

## <a name="metrics-granularity-and-retention-policy"></a>メトリックの粒度と保持ポリシー
アプリケーションと App Service プランのメトリックがサービスによってログに記録され、集計される際には、次の粒度と保持ポリシーが適用されます。

* 粒度が**分**のメトリックは **48 時間**保持されます。
* 粒度が**時間**のメトリックは **30 日間**保持されます。
* 粒度が**日**のメトリックは **90 日間**保持されます。

## <a name="monitoring-quotas-and-metrics-in-the-azure-portal"></a>Azure ポータルでのクォータとメトリックの監視
アプリケーションに影響するさまざまな**クォータ**と**メトリック**の状態を、[Azure ポータル](https://portal.azure.com)で確認できます。

![][quotas]
**クォータ**は、[設定] ブレードの **[クォータ]** にあります。 UX では、(1) クォータの名前、(2) リセットの間隔、(3) 現在の制限、(4) 現在の値を確認できます。

![][metrics]
**メトリック**は、リソースのブレードから直接アクセスできます。 (1) グラフを**クリック**し、(2) **[グラフの編集]** を選択することで、そのグラフをカスタマイズすることもできます。
ここから、表示する (3) **時間範囲**、(4) **グラフの種類**、(5) **メトリック**を変更できます。  

メトリックの詳細については、「[サービス メトリックの監視](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)」をご覧ください。

## <a name="alerts-and-autoscale"></a>アラートと自動スケール
アプリまたは App Service プランのメトリックは、アラートに関連付けることができます。詳細については、「[アラート通知の受信](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)」をご覧ください。

Basic、Standard、Premium の App Service プランでホストされている App Service アプリでは、 **自動スケール**がサポートされています。 これにより、App Service プランのメトリックを監視する規則を構成できるほか、インスタンス数を増減させ、必要に応じてリソースを追加したり、アプリケーションのプロビジョニングが過剰なときにコストを節約したりできます。 自動スケールの詳細については、[スケールの方法](../monitoring-and-diagnostics/insights-how-to-scale.md)に関するページと「[Azure Insights の自動スケールのベスト プラクティス](../monitoring-and-diagnostics/insights-autoscale-best-practices.md)」をご覧ください。

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、「[Azure App Service アプリケーションの作成](https://azure.microsoft.com/try/app-service/)」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
> 
> 

## <a name="whats-changed"></a>変更内容
* Websites から App Service への変更ガイドについては、「 [Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)

[fzilla]:http://go.microsoft.com/fwlink/?LinkId=247914
[vmsizes]:http://go.microsoft.com/fwlink/?LinkID=309169



<!-- Images. -->
[http403]: ./media/web-sites-monitor/http403.png
[quotas]: ./media/web-sites-monitor/quotas.png
[metrics]: ./media/web-sites-monitor/metrics.png

