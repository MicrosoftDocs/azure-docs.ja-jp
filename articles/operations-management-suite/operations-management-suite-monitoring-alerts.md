---
title: "Microsoft 監視製品のアラート管理 | Microsoft Docs"
description: "アラートには、管理者の注意が必要な問題が示されます。  この記事では、System Center Operations Manager (SCOM) と Log Analytics におけるアラートの作成と管理の方法の違いについて説明し、ハイブリッド アラート管理戦略で 2 つの製品を活用するためのベスト プラクティスを紹介します。"
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 6572c3f8-78ca-4fa9-8fe1-d0b488590788
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/09/2016
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f2b0655bdddc162c7c8ef7d478b2ce1fd1993f15


---
# <a name="managing-alerts-with-microsoft-monitoring"></a>Microsoft の監視製品を使用したアラートの管理
アラートには、管理者の注意が必要な問題が示されます。  System Center Operations Manager (SCOM) と、Operations Management Suite (OMS) の Log Analytics は、アラートの生成方法、アラートの管理と分析の方法、重要な問題が検出された場合の通知方法に明確な相違点があります。

## <a name="alerts-in-operations-manager"></a>Operations Manager のアラート
SCOM のアラートは、個々のルールまたはモニターによって生成され、特定の問題を示します。  モニターはエラー状態になったときにアラートを生成するのに対して、ルールは管理オブジェクトの正常性とは直接関係がない重要な問題を示すためにアラートを生成します。  管理パックには、管理対象のアプリケーションやサービスに関してアラートを生成するさまざまなワークフローが含まれています。  新しい管理パックを構成する際は、重要とは見なさない問題に関してアラートが過剰に生成されないように調整する必要があります。

![SCOM alert view](media/operations-management-suite-monitoring-alerts/scom-alert-view.png)

SCOM では、管理者が問題の解決に対応する際に変更できる状態をアラートに設定できるため、アラートを包括的に管理できます。  問題が解決されたら、管理者はアラートの状態を "終了" に設定します。終了したアラートは、アクティブなアラートを表示するビューに表示されなくなります。  モニターから生成されたアラートは、モニターが正常な状態に戻ると自動的に解決されます。

![Alert status](media/operations-management-suite-monitoring-alerts/scom-alert-status.png)

## <a name="alerts-in-log-analytics"></a>Log Analytics のアラート
Log Analytics のアラートは、定期的に自動で実行されるログ クエリから生成されます。  アラート ルールは任意のログ クエリから生成できます。  指定した条件に一致する結果がクエリによって返されると、アラートが生成されます。  このクエリには、特定のイベントが検出された場合にアラートを生成する専用のクエリを使用することも、特定のアプリケーションに関するなんらかのエラー イベントを検出する汎用的なクエリを使用することもできます。

Log Analytics のアラートは、イベントとして OMS リポジトリに書き込まれ、ログ クエリを使用して取得できます。  SCOM イベントのような状態はないため、いつ問題が解決されたかを指定できます。

![OMS alert](media/operations-management-suite-monitoring-alerts/oms-alert.png)

SCOM が Log Analytics のデータ ソースとして使用されている場合、作成または変更された SCOM アラートは OMS リポジトリに書き込まれます。  

![SCOM alert](media/operations-management-suite-monitoring-alerts/scom-alert.png)

[アラート管理ソリューション](http://technet.microsoft.com/library/mt484092.aspx) を使用すると、アクティブなアラートの概要と、さまざまなアラート セットを取得するためによく使用されるクエリのいくつかを確認できます。  このソリューションを使用して、SCOM のレポートよりも効果的にアラートを分析できます。  概要から詳細なデータにドリルダウンしたり、アド ホック クエリを作成してさまざまなアラート セットを取得したりできます。

![アラート管理ソリューション](media/operations-management-suite-monitoring-alerts/alert-management.png)

## <a name="notifications"></a>通知
SCOM の通知では、特定の条件に一致するアラートに呼応して、メールまたはテキスト メッセージが送信されます。  複数の通知サブスクリプションを作成し、監視対象オブジェクト、アラートの重大度、検出された問題の種類、時間帯などの条件に応じて、異なる相手に通知することができます。

少数のサブスクリプションを使用して、多数の管理パックを対象とした包括的な完了通知戦略を実装できます。

![SCOM alerts](media/operations-management-suite-monitoring-alerts/alerts-overview-scom.png)

Log Analytics では、各 [アラート ルール](http://technet.microsoft.com/library/mt614775.aspx)に電子メール通知アクションを設定することで、アラートが生成されたことを示す通知をメールで受け取ることができます。  SCOM では、1 つのルールで複数のアラートをサブスクライブすることはできません。  また、OMS にはあらかじめ構成されたアラート ルールがないため、自分で作成する必要があります。

![Log Analytics alerts](media/operations-management-suite-monitoring-alerts/alerts-overview-oms.png)

SCOM アラートはオペレーション コンソールでしか変更できないため、Log Analytics では完全に管理することができません。  Log Analytics は、SCOM 単体には含まれていない分析ツールを備えているため、アラート管理プロセスの一部として使用すると有用です。

## <a name="alert-remediation"></a>アラートによる修復
[修復](http://technet.microsoft.com/library/mt614775.aspx) とは、アラートによって特定された問題を自動的に修正する試みを指します。

SCOM を使用すると、モニターが異常な状態になったことに呼応して診断と復元を実行できます。  これは、モニターによるアラートの生成と同時に行われます。  診断と復元は、通常、エージェントで実行されるスクリプトとして実装されます。  診断では、検出された問題の詳細情報を収集しようとするのに対して、復元では、問題の解決を試みます。

Log Analytics では、Log Analytics アラートに呼応して、[Azure Automation Runbook](https://azure.microsoft.com/documentation/services/automation/) を開始するか、webhook を呼び出すことができます。  Runbook には、PowerShell で実装される複雑なロジックを含めることができます。  このスクリプトは Azure で実行され、Azure のリソースやクラウドで提供される外部リソースにアクセスできます。  Azure Automation には、ローカル データセンター内のサーバーで Runbook を実行する機能がありますが、現時点では、Log Analytics のアラートに呼応して Runbook を開始する場合はこの機能を使用できません。

SCOM の復元と OMS の Runbook はどちらにも PowerShell スクリプトを含めることができますが、復元の場合は管理パックに含める必要があるため、復元の方が作成と管理が難しくなっています。  Runbook は Azure Automation に格納されます。Azure Automation には、Runbook の作成、テスト、および管理を行うための機能が用意されています。

Log Analytics のデータ ソースとして SCOM を使用する場合は、OMS リポジトリに格納されている SCOM アラートを取得するログ クエリを使用して Log Analytics アラートを作成できます。  これにより、SCOM アラートに呼応して Azure Automation Runbook を実行できます。  Runbook は Azure で実行されるため、オンプレミスの問題を復元するための実行可能な戦略としては使用できません。

## <a name="next-steps"></a>次のステップ
* [System Center Operations Manager (SCOM) のアラート](https://technet.microsoft.com/library/hh212913.aspx)に関するページで詳細を確認してください。




<!--HONumber=Nov16_HO3-->


