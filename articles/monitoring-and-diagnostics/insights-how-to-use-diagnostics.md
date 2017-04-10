---
title: "Microsoft Azure での監視と診断の有効化 |Microsoft Docs"
description: "Azure でリソースの診断を設定する方法について説明します。"
author: rboucher
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: af1947a9-c211-4aa1-8924-880a86240be4
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2015
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: c30ffa9e392bb6411312b0789ae6e8218f770bc1


---
# <a name="enable-monitoring-and-diagnostics"></a>監視と診断の有効化
[Azure ポータル](https://portal.azure.com)では、リソースに関する監視と診断の頻度の高い豊富なデータを構成することができます。 また、[REST API](https://msdn.microsoft.com/library/azure/dn931932.aspx) または [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) を使用して、プログラムで診断を構成することもできます。

Azure の診断、監視、およびメトリック データは、選択したストレージ アカウントに保存されます。 これにより、ストレージ エクスプローラーから、Power BI、サード パーティ製ツールまで、どれでも好きなツールを使用してデータを読み取ることができます。

## <a name="when-you-create-a-resource"></a>リソースを作成する場合
ほとんどのサービスでは、 [Azure ポータル](https://portal.azure.com)でサービスを最初に作成するときに診断を有効にすることができます。

1. **[新規]** に移動し、興味のあるリソースを選択します。
2. **[オプションの構成]**を選択します。
    ![[診断] ブレード](./media/insights-how-to-use-diagnostics/Insights_CreateTime.png)
3. **[診断]** を選択して、**[オン]** をクリックします。 診断の保存先となるストレージ アカウントを選択する必要があります。 診断データをストレージ アカウントに送信する際には、ストレージおよびトランザクションの通常のデータ料金が発生します。
4. **[OK]** をクリックしてリソースを作成します。

## <a name="change-settings-for-an-existing-resource"></a>既存のリソースの設定の変更
リソースを既に作成済みで、診断設定を変更する (データ コレクションのレベル変更など) 必要がある場合は、Azure ポータルで実行できます。

1. リソースに移動して、 **[設定]** コマンドをクリックします。
2. **[診断]**を選択します。
3. **[診断]** ブレードには、そのリソースに可能な診断と監視コレクション データが表示されます。 リソースによっては、データの **[保持]** ポリシーを選択して、ストレージ アカウントからクリーンアップすることもできます。
    ![ストレージ診断](./media/insights-how-to-use-diagnostics/Insights_StorageDiagnostics.png)
4. 設定を選択したら、 **[保存]** コマンドをクリックします。 監視データは、初めて有効にする場合に表示されるまで少し時間がかかることがあります。

### <a name="categories-of-data-collection-for-virtual-machines"></a>仮想マシンのデータ収集のカテゴリ
仮想マシンでは、メトリックとログがすべて 1 分間隔で記録されるので、ご使用のマシンに関する情報はいつでも最新のものとなります。

* **基本メトリック** : プロセッサやメモリなどの仮想マシンについての正常性のメトリック
* **ネットワークおよび Web メトリック** : ネットワーク接続と Web サービスに関するメトリック
* **.NET メトリック** : 仮想マシン上で実行されている .NET アプリケーションおよび ASP.NET アプリケーションについてのメトリック
* **SQL メトリック** : Microsoft SQL Service を実行している場合はそのパフォーマンス メトリック
* **Windows イベント アプリケーション ログ** : アプリケーション チャネルに送信される Windows イベント
* **Windows イベント システム ログ**: システム チャネルに送信される Windows イベント。 これには、[Microsoft マルウェア対策](http://go.microsoft.com/fwlink/?LinkID=404171&clcid=0x409)からのすべてのイベントも含まれます。
* **Windows イベント セキュリティ ログ** : セキュリティ チャネルに送信される Windows イベント
* **診断インフラストラクチャ ログ** : 診断コレクション インフラストラクチャについてのログ
* **IIS ログ** : IIS サーバーについてのログ

現時点では、Linux の特定のディストリビューションはサポートされていません。また、ゲスト エージェントを仮想マシンにインストールする必要があります。

## <a name="next-steps"></a>次のステップ
* [アラート通知を受け取り](insights-receive-alert-notifications.md) ます。
* [サービス メトリックスを監視](insights-how-to-customize-monitoring.md) して、サービスの可用性と応答性を確認します。
* [インスタンス数を自動的にスケール変更](insights-how-to-scale.md) して、必要に応じてサービスのスケールが変更されるようにします。
* [アプリケーションのパフォーマンスを監視](../application-insights/app-insights-azure-web-apps.md) します。
* [イベントとアクティビティ ログを表示](insights-debugging-with-events.md)して、サービスで発生したすべてのことを理解します。
* [サービス正常性を追跡](insights-service-health.md) して、Azure でパフォーマンスの低下やサービスの中断が発生したことを検出します。




<!--HONumber=Nov16_HO3-->


