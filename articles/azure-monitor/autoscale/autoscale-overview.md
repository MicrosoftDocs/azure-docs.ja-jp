---
title: Microsoft Azure の自動スケール
description: Microsoft Azure の自動スケール
ms.subservice: autoscale
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 4727d562e21b92e58c8091f1161cf53198ff0b26
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101726004"
---
# <a name="overview-of-autoscale-in-microsoft-azure"></a>Microsoft Azure の自動スケールの概要
この記事では、Microsoft Azure 自動スケールの概要、利点、および使用方法について説明します。  

Azure Monitor 自動スケールは、[Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/)、[Cloud Services](https://azure.microsoft.com/services/cloud-services/)、[App Service - Web Apps](https://azure.microsoft.com/services/app-service/web/)、[API Management サービス](../../api-management/api-management-key-concepts.md)、および [Azure Data Explorer クラスター](/azure/data-explorer/)にのみ適用されます。

> [!NOTE]
> Azure には、2 通りの自動スケールの方法があります。 以前のバージョンの自動スケールは、仮想マシン (可用性セット) に適用されます。 この機能はサポートが限定されているので、より高速で信頼性の高い自動スケールのサポートを得るために、仮想マシン スケール セットに移行することをお勧めします。 この記事には、古い技術を使用する方法についてのリンクも含まれています。  
>

## <a name="what-is-autoscale"></a>自動スケールとは何ですか。
自動スケールを使用すると、適切な量のリソースを実行して、アプリケーションに対する負荷を処理することができます。 リソースを追加して負荷の増加に対処したり、アイドル状態のリソースを削除して経費を節約したりできます。 実行するインスタンスの最小値と最大数を指定して、一連のルールに基づいて自動的に仮想マシンを追加または削除します。 最小値を指定すると、負荷のない状況でも、アプリケーションが常に実行されます。 最大値を指定すると、時間単位の合計コストが制限されます。 作成するルールを使用して、これら 2 つの値の間で自動的にスケールします。

 ![自動スケールについて説明します。 仮想マシンの追加と削除](./media/autoscale-overview/AutoscaleConcept.png)

ルール条件が満たされると、1 つまたは複数の自動スケール アクションがトリガーされます。 仮想マシンを追加または削除したり、その他のアクションを実行したりできます。 次の概念図では、このプロセスを示します。  

 ![自動スケールのフロー図](./media/autoscale-overview/Autoscale_Overview_v4.png)

次の説明は、上の図の各部分に適用されます。   

## <a name="resource-metrics"></a>リソース メトリック
リソースは、メトリックを出力します。これらのメトリックは、後でルールに従って処理されます。 メトリックは、さまざまな方法で取得されます。
仮想マシン スケール セットは、Azure Diagnostics エージェントからのテレメトリ データを使用します。一方、Web アプリおよびクラウド サービスのテレメトリは、Azure インフラストラクチャから直接取得されます。 一般的に使用される統計情報として、CPU 使用率、メモリ使用量、スレッド数、キューの長さ、ディスク使用量などがあります。 使用できるテレメトリ データの一覧については、[自動スケールの一般的なメトリック](autoscale-common-metrics.md)に関するページを参照してください。

## <a name="custom-metrics"></a>カスタム メトリック
アプリケーションから出力されている可能性のある独自のカスタム メトリックを利用することもできます。 メトリックを Application Insights に送信するようにアプリケーションを構成している場合は、これらのメトリックを利用して、スケールするかどうかを決定することができます。

## <a name="time"></a>Time
スケジュール ベースの規則は、UTC に基づいています。 規則を設定する際に、タイム ゾーンを正しく設定する必要があります。  

## <a name="rules"></a>ルール
次の図では 1 つの自動スケール規則だけを示していますが、複数の規則を使用できます。 状況に応じて、複雑に重複する規則を作成することができます。  規則の種類には、次のようなものがあります。  

* **メトリック ベース** - たとえば、CPU 使用率が 50% を超える場合に、この操作を実行します。
* **時間ベース** - たとえば、特定のタイム ゾーンで毎週土曜の午前 8 時に webhook をトリガーします。

メトリック ベースのルールでは、アプリケーションの負荷が測定され、その負荷に基づいて仮想マシンが追加または削除されます。 スケジュール ベースのルールでは、負荷の時間パターンを確認し、負荷の増加や減少が発生する前にスケールしたい場合に、スケールを行うことができます。  

## <a name="actions-and-automation"></a>アクションと自動化
ルールは、1 つまたは複数の種類の操作をトリガーできます。

* **スケール** - VM をスケール インまたはアウトします。
* **電子メール** - サブスクリプションの管理者、共同管理者、および指定された追加の電子メール アドレスに電子メールを送信します。
* **webhook による自動化** - Azure の内部または外部で複数の複雑な操作をトリガーできる webhook を呼び出します。 Azure 内で、Azure Automation Runbook、Azure 関数、または Azure ロジック アプリを開始できます。 Azure 外部のサードパーティ URL の例として、Slack や Twilio のようなサービスがあります。

## <a name="autoscale-settings"></a>自動スケール設定
自動スケールでは、次の用語と構造を使用します。

- **自動スケール設定** は自動スケールエンジンによって読み取られ、スケールアップまたはダウンするかどうかが判断されます。 1 つ以上のプロファイル、ターゲット リソースに関する情報、および通知の設定が含まれています。

  - **自動スケール プロファイル** は、次の要素を組み合わせたものです。

    - **容量設定**。インスタンスの数の最小値、最大値、既定値を示します。
    - **一連のルール**。各ルールに、トリガー (時間またはメトリック) とスケール アクション (スケールアップまたはスケールダウン) が含まれています。
    - **繰り返し**。自動スケールによってこのプロファイルがいつ有効になるかを示します。

      さまざまな重複する要件に対処できるように、複数のプロファイルを設定できます。 たとえば、一日 の異なる時間、または曜日に異なる自動スケール プロファイルを設定することができます。

  - **通知設定** によって、自動スケール設定プロファイルのいずれかの基準が満たされていることに基づいて、自動スケール イベントが発生したときに、どの通知が行われるかが定義されます。 自動スケールでは、1 つ以上の電子メール アドレスに通知したり、1 つ以上の webhook の呼び出しを行ったりすることができます。


![Azure 自動スケール設定、プロファイル、およびルール構造](./media/autoscale-overview/AzureResourceManagerRuleStructure3.png)

構成可能なフィールドと説明の完全な一覧については、 [自動スケール REST API](/rest/api/monitor/autoscalesettings)に関するページを参照してください。

コード例については、次を参照してください。

* [VM スケール セットに対する Resource Manager テンプレートを使用した高度な自動スケール構成](autoscale-virtual-machine-scale-sets.md)  
* [自動スケールの REST API](/rest/api/monitor/autoscalesettings)

## <a name="horizontal-vs-vertical-scaling"></a>水平および垂直方向のスケーリング
自動スケールは、水平的なスケーリングだけを行います。つまり、VM インスタンスの数が増加 ("アウト") したり、減少 ("イン") したりします。  水平スケーリングは、クラウド環境では負荷を処理するために数千の VM さえ実行できるため、より柔軟です。

一方、垂直方向のスケーリングは、それとは異なります。 VM の数は同じままですが、VM の能力を強めたり ("アップ") 弱めたり ("ダウン") します。 能力は、メモリ、CPU 速度、ディスク領域などで計測します。垂直スケーリングには、より多くの制限があります。 これはハードウェアの規模によって左右されます。大規模なハードウェアは拡張の上限に達しやすく、リージョンによって違いが出ることもあります。 垂直スケーリングでは、多くの場合、VM の停止と再起動も必要になります。

## <a name="methods-of-access"></a>アクセスの方法
以下を通じて、自動スケールを設定することができます。

* [Azure Portal](autoscale-get-started.md)
* [PowerShell](../powershell-samples.md#create-and-manage-autoscale-settings)
* [クロスプラットフォーム コマンド ライン インターフェイス (CLI)](../cli-samples.md#autoscale)
* [Azure 監視 REST API](/rest/api/monitor/autoscalesettings)

## <a name="supported-services-for-autoscale"></a>自動スケールでサポートされているサービス
| サービス | スキーマとドキュメント |
| --- | --- |
| Web Apps |[Web アプリのスケーリング](autoscale-get-started.md) |
| Cloud Services |[クラウド サービスの自動スケール](../../cloud-services/cloud-services-how-to-scale-portal.md) |
| Virtual Machines: クラシック |[従来の仮想マシン可用性セットのスケーリング](/archive/blogs/kaevans/autoscaling-azurevirtual-machines) |
| Virtual Machines: Windows スケール セット |[Windows での仮想マシン スケール セットのスケーリング](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md) |
| Virtual Machines: Linux スケール セット |[Linux での仮想マシン スケール セットのスケーリング](../../virtual-machine-scale-sets/tutorial-autoscale-cli.md) |
| Virtual Machines: Windows の例 |[VM スケール セットに対する Resource Manager テンプレートを使用した高度な自動スケール構成](autoscale-virtual-machine-scale-sets.md) |
| Azure App Service |[Azure App Service でアプリをスケールアップする](../../app-service/manage-scale-up.md)|
| API Management サービス|[Azure API Management インスタンスを自動的にスケーリングする](../../api-management/api-management-howto-autoscale.md)
| Azure Data Explorer クラスター|[需要の変化に対応するために Azure Data Explorer のクラスターのスケーリングを管理する](/azure/data-explorer/manage-cluster-horizontal-scaling)|
| Logic Apps |[統合サービス環境 (ISE) のキャパシティを追加する](../../logic-apps/ise-manage-integration-service-environment.md#add-ise-capacity)|
| Spring Cloud |[マイクロサービス アプリケーションの自動スケーリングを設定する](../../spring-cloud/spring-cloud-tutorial-setup-autoscale.md)|
| Service Bus |[Azure Service Bus 名前空間のメッセージング ユニットを自動的に更新する](../../service-bus-messaging/automate-update-messaging-units.md)|

## <a name="next-steps"></a>次のステップ
自動スケールについてさらに学習するには、上の一覧にある自動スケールのチュートリアルを使用するか、以下のリソースを参照してください。

* [Azure Monitor の自動スケールの一般的なメトリック](autoscale-common-metrics.md)
* [Azure Monitor の自動スケールのベスト プラクティス](autoscale-best-practices.md)
* [自動スケール操作を使用して電子メールと Webhook アラート通知を送信する](autoscale-webhook-email.md)
* [自動スケールの REST API](/rest/api/monitor/autoscalesettings)
* [仮想マシン スケール セットの自動スケールに関するトラブルシューティング](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)