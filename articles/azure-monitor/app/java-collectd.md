---
title: Linux での Java Web アプリ パフォーマンスの監視 | Microsoft Docs
description: Application Insights の CollectD プラグインを使用した Java Web サイトのアプリケーション パフォーマンスの拡張
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 40c68f45-197a-4624-bf89-541eb7323002
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/24/2016
ms.author: mbullwin
ms.openlocfilehash: 783cef6ff4e107838bb3ff7502fb4a8e9189ec3d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58011192"
---
# <a name="collectd-linux-performance-metrics-in-application-insights"></a>collectd:Application Insights での Linux パフォーマンス メトリック


[Application Insights](../../azure-monitor/app/app-insights-overview.md) で Linux システムのパフォーマンス メトリックを探索するには、[collectd](https://collectd.org/) を Application Insights のプラグインと共にインストールします。 このオープンソース ソリューションでは、さまざまなシステムおよびネットワーク統計情報を収集します。

既に [Application Insights で Java Web サービスをインストルメント化][java]してある場合は、通常、collectd を使用します。 collectd を使用すると、アプリのパフォーマンスの向上や問題の診断に役立つ多くのデータを得られます。 

## <a name="get-your-instrumentation-key"></a>インストルメンテーション キーの取得
[Microsoft Azure Portal](https://portal.azure.com) で、データを表示する [Application Insights](../../azure-monitor/app/app-insights-overview.md) リソースを開きます。 (または[新しいリソースを作成](../../azure-monitor/app/create-new-resource.md )します。)

リソースを識別する、インストルメンテーション キーのコピーを取ります。

![Browse all, open your resource, and then in the Essentials drop-down, select and copy the Instrumentation Key (すべてを参照し、リソースを開いてから、[Essentials] ドロップダウンでインストルメンテーション キーを選択およびコピーする)](./media/java-collectd/instrumentation-key-001.png)

## <a name="install-collectd-and-the-plug-in"></a>collectd とプラグインのインストール
Linux サーバー コンピューターで、次の操作を行います。

1. [collectd](https://collectd.org/) のバージョン 5.4.0 またはそれ以降をインストールします。
2. [Application Insights collectd ライター プラグイン](https://aka.ms/aijavasdk)をダウンロードします。 バージョン番号をメモしておきます。
3. プラグイン JAR を `/usr/share/collectd/java`にコピーします。
4. `/etc/collectd/collectd.conf`を編集します:
   * [Java プラグイン](https://collectd.org/wiki/index.php/Plugin:Java) が有効になっていることを確認します。
   * java.class.path の JVMArg を次の JAR を含むように更新します。 バージョン番号を、ダウンロードしたものと一致するように更新します。
   * `/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar`
   * リソースからのインストルメンテーション キーを使用して、次のスニペットを追加します。

```XML

     LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"
     <Plugin ApplicationInsightsWriter>
        InstrumentationKey "Your key"
     </Plugin>
```

サンプル構成ファイルの一部を次に示します。

```XML

    ...
    # collectd plugins
    LoadPlugin cpu
    LoadPlugin disk
    LoadPlugin load
    ...

    # Enable Java Plugin
    LoadPlugin "java"

    # Configure Java Plugin
    <Plugin "java">
      JVMArg "-verbose:jni"
      JVMArg "-Djava.class.path=/usr/share/collectd/java/applicationinsights-collectd-1.0.5.jar:/usr/share/collectd/java/collectd-api.jar"

      # Enabling Application Insights plugin
      LoadPlugin "com.microsoft.applicationinsights.collectd.ApplicationInsightsWriter"

      # Configuring Application Insights plugin
      <Plugin ApplicationInsightsWriter>
        InstrumentationKey "12345678-1234-1234-1234-123456781234"
      </Plugin>

      # Other plugin configurations ...
      ...
    </Plugin>
    ...
```

その他の [collectd プラグイン](https://collectd.org/wiki/index.php/Table_of_Plugins)を構成します。これにより、異なるソースからさまざまなデータを収集できます。

[マニュアル](https://collectd.org/wiki/index.php/First_steps)に従って collectd を起動します。

## <a name="view-the-data-in-application-insights"></a>Application Insights でデータを表示する
Application Insights のリソースで、[[メトリックス] および [グラフの追加]][metrics] を開き、[カスタム] カテゴリから表示したいメトリックを選択します。

既定では、メトリックは、メトリックの収集元のすべてのホスト コンピューターにわたって集計されます。 ホスト別のメトリックを表示するには、グラフの詳細ブレードで [グループ化] を有効にしてから、CollectD-Host でのグループ化を選択します。

## <a name="to-exclude-upload-of-specific-statistics"></a>特定の統計のアップロードを除外するには
既定では、Application Insights プラグインにより、すべての有効になっている collectd 'read' プラグインによって収集されたすべてのデータが送信されます。 

特定のプラグインまたはデータ ソースからのデータを除外するには:

* 構成ファイルを編集します。 
* `<Plugin ApplicationInsightsWriter>`に次のようなディレクティブ行を追加します。

| ディレクティブ | 効果 |
| --- | --- |
| `Exclude disk` |`disk` プラグインによって収集されたすべてのデータを除外します |
| `Exclude disk:read,write` |`read` および `write` という名前のソースを `disk` プラグインから除外します。 |

各ディレクティブを改行で区切ります。

## <a name="problems"></a>問題が発生した場合
*ポータルにデータが表示されません。*

* [[検索]][diagnostic] を開き、未加工のイベントが到着しているかどうかを確認します。 メトリックス エクスプローラーに表示されるまでに時間がかかる場合があります。
* 必要に応じて [データ送信についてファイアウォール例外を設定](../../azure-monitor/app/ip-addresses.md)
* Application Insights プラグインでトレースを有効にします。 `<Plugin ApplicationInsightsWriter>`に次の行を追加します。
  * `SDKLogger true`
* ターミナルを開き、詳細モードで collectd を起動して報告されている問題がないか確認します。
  * `sudo collectd -f`

## <a name="known-issue"></a>既知の問題

Application Insights の Write プラグインは特定の Read プラグインと互換性がありません。 一部のプラグインは "NaN" を送信することがありますが、Application Insights のプラグインでは浮動小数点数が想定されています。

症状:収集されたログに、"AI: ...SyntaxError:Unexpected token N" を含むエラーが示されています。

対処法:問題のある Write プラグインによって収集されるデータを除外します。 

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md


