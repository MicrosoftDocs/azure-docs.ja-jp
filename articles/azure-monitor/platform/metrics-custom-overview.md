---
title: Azure Monitor のカスタム メトリック (プレビュー)
description: Azure Monitor のカスタム メトリックと、それらのモデリング方法について説明します。
author: ancav
ms.author: ancav
services: azure-monitor
ms.topic: conceptual
ms.date: 06/01/2020
ms.subservice: metrics
ms.openlocfilehash: 73c9b2bf8cf88ca5e8576c451c9d9ac5f0eae8a3
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88639904"
---
# <a name="custom-metrics-in-azure-monitor-preview"></a>Azure Monitor のカスタム メトリック (プレビュー)

Azure でリソースやアプリケーションをデプロイするとき、それらのパフォーマンスや正常性についての洞察を得るために、テレメトリの収集を開始することができます。 Azure には、すぐに利用できるいくつかのメトリックがあります。 これらのメトリックは[標準またはプラットフォーム](./metrics-supported.md)と呼ばれます。 ただし、これらの用途はあくまで限定的なものです。 

より詳細な洞察を得るために、いくつかのカスタム パフォーマンス指標またはビジネス固有のメトリックを収集することができます。 これらの**カスタム** メトリックは、アプリケーション テレメトリ、Azure リソース上で実行されるエージェント、またはアウトサイドイン型の監視システムによって収集し、Azure Monitor に直接送信することができます。 Azure のリソースおよびアプリケーションのカスタム メトリックが Azure Monitor に発行されたら、Azure によって出力された標準メトリックと共に、それらのメトリックを参照、クエリしたり、メトリックに基づいたアラートを設定したりできます。

Azure Monitor のカスタム メトリックは現在、パブリック プレビューの段階にあります。 

## <a name="methods-to-send-custom-metrics"></a>カスタム メトリックを送信する方法

カスタム メトリックは複数の方法で Azure Monitor に送信できます。
- Azure Application Insights SDK を使用してアプリケーションをインストルメント化し、カスタム テレメトリを Azure Monitor に送信する。 
- [Windows または Linux Azure VM](azure-monitor-agent-overview.md) に Azure Monitor エージェント (プレビュー) をインストールし、[データ収集ルール](data-collection-rule-azure-monitor-agent.md)を使用してパフォーマンス カウンターを Azure Monitor メトリックに送信する。
- [Azure VM](collect-custom-metrics-guestos-resource-manager-vm.md)、[仮想マシン スケール セット](collect-custom-metrics-guestos-resource-manager-vmss.md)、[クラシック VM](collect-custom-metrics-guestos-vm-classic.md)、または[クラシック Cloud Services](collect-custom-metrics-guestos-vm-cloud-service-classic.md) に Windows Azure 診断 (WAD) 拡張機能をインストールして、パフォーマンス カウンターを Azure Monitor に送信する。 
- [InfluxData Telegraf エージェント](collect-custom-metrics-linux-telegraf.md)を Azure Linux VM にインストールし、Azure Monitor 出力プラグインを使用してメトリックを送信する。
- [Azure Monitor REST API](./metrics-store-custom-rest-api.md) にカスタム メトリックを直接送信する (`https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics`)。

## <a name="pricing-model-and-retention"></a>価格モデルと保持期間

カスタム メトリックとメトリック クエリに対する課金を有効にする場合の詳細については、[「Azure Monitor の価格」ページ](https://azure.microsoft.com/pricing/details/monitor/)を参照してください。 カスタム メトリックやメトリック クエリを含むすべてのメトリックの具体的な価格の詳細は、このページに記載されています。 要約すると、標準メトリック (プラットフォーム メトリック) を Azure Monitor メトリック ストアに取り込むコストは発生しませんが、カスタム メトリックは一般提供されると、コストが発生します。 メトリック API クエリには、コストが発生します。

カスタム メトリックは、[プラットフォーム メトリックと同じ時間](data-platform-metrics.md#retention-of-metrics)保持されます。 

> [!NOTE]  
> Application Insights SDK を介して Azure Monitor に送信されたメトリックは、取り込まれたログ データとして課金されます。 追加のメトリック料金が発生するのは、Application Insights 機能の [[カスタム メトリック ディメンションに関するアラートを有効にします]](../app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation) が選択されている場合のみです。 このチェックボックスにより、カスタム メトリック API を使用して Azure Monitor メトリック データベースにデータが送信され、より複雑なアラートが実行可能になります。  詳細については、[Application Insights の価格モデル](../app/pricing.md#pricing-model)と、[お客様のリージョンでの価格](https://azure.microsoft.com/pricing/details/monitor/)に関するページを参照してください。


## <a name="how-to-send-custom-metrics"></a>カスタム メトリックを送信する方法

Azure Monitor にカスタム メトリックを送信するときは、報告する各データ ポイント (または値) に次の情報が含まれている必要があります。

### <a name="authentication"></a>認証
Azure Monitor にカスタム メトリックを送信するには、メトリックを送信する側のエンティティで、要求の **Bearer** ヘッダーに有効な Azure Active Directory (Azure AD) トークンが存在する必要があります。 有効なベアラー トークンを取得するいくつかの方法がサポートされています。
1. [Azure リソースの管理 ID](../../active-directory/managed-identities-azure-resources/overview.md)。 Azure リソース自身 (VM など) に ID を付与します。 マネージド サービス ID (MSI) は、特定の操作を実行するためのアクセス許可をリソースに提供するように設計されています。 たとえば、自身に関するメトリックをリソースが出力できるようにします。 リソース (またはその MSI) には、別のリソースに対する "**メトリックの発行元の監視**" アクセス許可を付与することもできます。 このアクセス許可を付与すると、その MSI で他のリソースのメトリックも生成できるようになります。
2. [Azure AD サービス プリンシパル](../../active-directory/develop/app-objects-and-service-principals.md)。 このシナリオでは、Azure リソースについてのメトリックを出力するためのアクセス許可を Azure AD アプリケーション (またはサービス) に割り当てることができます。
要求を認証するために、Azure Monitor は Azure AD 公開キーを使用してアプリケーション トークンを検証します。 既存の "**メトリックの発行元の監視**" ロールには、既にこのアクセス許可が付与されています。 これは Azure portal で入手できます。 サービス プリンシパルには、どのようなリソースについてのカスタム メトリックをそれが出力するのかに応じて、必要なスコープで "**メトリックの発行元の監視**" ロールを付与することができます。 (たとえば、サブスクリプション、リソース グループ、または特定のリソース)。

> [!TIP]  
> カスタム メトリックを出力するための Azure AD トークンを要求する場合は、トークンの要求対象であるユーザーやリソースが、`https://monitoring.azure.com/` であることを確認してください。 必ず、末尾のスラッシュ (/) を含めるようにしてください。

### <a name="subject"></a>サブジェクト
このプロパティは、どの Azure リソース ID についてのカスタム メトリックが報告されるのかを表します。 この情報は、実行される API 呼び出しの URL にエンコードされます。 各 API は、単一の Azure リソースのメトリック値のみを送信できます。

> [!NOTE]  
> リソース グループまたはサブスクリプションのリソース ID に対してカスタム メトリックを出力することはできません。


### <a name="region"></a>リージョン
このプロパティは、メトリックを出力する対象のリソースがデプロイされている Azure リージョンを表します。 メトリックは、リソースがデプロイされているリージョンと同じリージョンの Azure Monitor エンドポイントに出力される必要があります。 たとえば、米国西部にデプロイされている VM についてのカスタム メトリックは、WestUS リージョンの Azure Monitor エンドポイントに送信される必要があります。 リージョン情報は API 呼び出しの URL にもエンコードされます。

> [!NOTE]  
> パブリック プレビュー期間中、カスタム メトリックは一部の Azure リージョンでのみ利用できます。 サポートされているリージョンの一覧は、この記事の後半のセクションに示しています。
>
>

### <a name="timestamp"></a>Timestamp
Azure Monitor に送信されるデータ ポイントには、それぞれタイムスタンプが記録されている必要があります。 このタイムスタンプはメトリック値が測定または収集された日時を表します。 Azure Monitor は、現在から 20 分前までの、および現在から 5 分後までのタイムスタンプが付いたメトリック データを受け付けます。 タイムスタンプは、ISO 8601 形式である必要があります。

### <a name="namespace"></a>名前空間
名前空間は類似のメトリックを分類またはグループ化する方法です。 名前空間を使用すると、収集する情報またはパフォーマンス指標に基づいてメトリックのグループを分離できます。 たとえば、**contosomemorymetrics** という名前空間では、アプリをプロファイリングするメモリ使用量メトリックを追跡し、 **contosoapptransaction** という別の名前空間では、アプリケーション内のユーザー トランザクションに関するすべてのメトリックを追跡するといったことが可能です。

### <a name="name"></a>名前
**Name** は、報告されるメトリックの名前です。 通常は、測定内容を識別するのに十分なほど説明的な名前です。 たとえば、特定の VM で使用されるメモリのバイト数を測定するメトリックの場合は、 **Memory Bytes In Use** などのようなメトリック名になります。

### <a name="dimension-keys"></a>ディメンション キー
ディメンションは、収集しているメトリックに関する追加の特性を説明するのに役立つキーと値のペアです。 この追加の特性を使用することで、より多くの情報をメトリックについて収集し、より深い洞察を得ることが可能になります。 たとえば、**Memory Bytes In Use** メトリックに **Process** というディメンション キーを追加して、VM 上の各プロセスが消費するメモリのバイト数を取得することもできます。 このキーを使用すれば、メトリックをフィルタリングし、メモリ固有のプロセスが使用する容量を確認したり、メモリ使用量で上位 5 つのプロセスを識別したりすることが可能になります。
ディメンションは省略可能です。すべてのメトリックがディメンションを持つとは限りません。 カスタム メトリックは最大 10 個のディメンションを持つことができます。

### <a name="dimension-values"></a>ディメンション値
メトリックのデータ ポイントを報告するとき、報告されるメトリックのディメンション キーごとに、対応するディメンション値が存在します。 たとえば、VM で ContosoApp によって使用されているメモリを報告する場合、

* メトリック名は **Memory Bytes in Use**、
* ディメンション キーは **Process**、
* ディメンション値は **ContosoApp.exe** のようになります。

メトリック値を発行するとき、ディメンション キーごとに 1 つのディメンション値しか指定できません。 VM 上の複数のプロセスについて同じメモリ使用率を収集する場合、そのタイムスタンプで複数のメトリック値を報告できます。 各メトリック値は、**Process** ディメンション キーに異なるディメンション値を指定します。
ディメンションは省略可能です。すべてのメトリックがディメンションを持つとは限りません。 メトリックの投稿にディメンション キーが定義されている場合、対応するディメンション値は必須です。

### <a name="metric-values"></a>メトリックの値
Azure Monitor では、すべてのメトリックを 1 分刻みの間隔で保存します。 ただし場合によっては、メトリックを 1 分間に複数回サンプリングする必要があります。 たとえば、CPU 使用率などがそうです。 また、多数のイベントごとに測定が必要な場合もあります。 たとえば、サインイン トランザクションの待機時間などです。 出力して Azure Monitor に送信する必要がある未処理値の数を制限するために、ローカルで値を事前集計して出力することができます。

* **Min**:1 分間のすべてのサンプルと測定値からの最小観測値。
* **Max**:1 分間のすべてのサンプルと測定値からの最大観測値。
* **Sum**:1 分間のすべてのサンプルと測定値からのすべての観測値の合計。
* **Count**:1 分間に取得されたサンプルと測定値の数。

たとえば、ある 1 分の間に、アプリへのサインイン トランザクションが 4 件あり、それぞれについて測定された待機時間の結果が次のとおりであったとします。

|トランザクション 1|トランザクション 2|トランザクション 3|トランザクション 4|
|---|---|---|---|
|7 ミリ秒|4 ミリ秒|13 ミリ秒|16 ミリ秒|
|

その場合、結果として Azure Monitor に送信されるメトリックは次のようになります。
* 最小:4
* 最大:16
* 合計:40
* カウント:4

アプリケーションにおいて、ローカルでの事前集計ができず、収集してすぐに個別のサンプルまたはイベントを出力する必要がある場合、未処理の測定値を出力することができます。 たとえば、アプリでサインイン トランザクションが発生するたびに、ただ 1 つの測定値と共にメトリックを Azure Monitor に発行することもできます。 その場合、サインイン トランザクションに要した時間が 12 ミリ秒だとすると、送信されるメトリックは次のようになります。
* 最小:12
* 最大:12
* 合計:12
* カウント:1

このプロセスでは、ある 1 分の間に、同じメトリックとディメンションの組み合わせに対して複数の値を出力できます。 その場合は、Azure Monitor によって、ある 1 分の間に出力されたすべての未処理値が集計されます。

### <a name="sample-custom-metric-publication"></a>カスタム メトリックの送信例
次の例では、仮想マシン用のメトリック名前空間 **Memory Profile** の下に **Memory Bytes in Use** というカスタム メトリックを作成します。 このメトリックは **Process** という 1 つのディメンションを持ちます。 特定のタイムスタンプに対して、2 つの異なるプロセスについてのメトリック値を出力します。

```json
{
    "time": "2018-08-20T11:25:20-7:00",
    "data": {

      "baseData": {

        "metric": "Memory Bytes in Use",
        "namespace": "Memory Profile",
        "dimNames": [
          "Process"        ],
        "series": [
          {
            "dimValues": [
              "ContosoApp.exe"
            ],
            "min": 10,
            "max": 89,
            "sum": 190,
            "count": 4
          },
          {
            "dimValues": [
              "SalesApp.exe"
            ],
            "min": 10,
            "max": 23,
            "sum": 86,
            "count": 4
          }
        ]
      }
    }
  }
```
> [!NOTE]  
> Application Insights、診断拡張機能、および InfluxData Telegraf エージェントは、正しいリージョンのエンドポイントに対するメトリック値を出力し、各出力で上記のプロパティをすべて保持するよう、既に構成されています。
>
>

## <a name="custom-metric-definitions"></a>カスタム メトリック定義
出力される前に Azure Monitor でカスタム メトリックを事前定義する必要はありません。 発行された各メトリック データ ポイントには、名前空間、名前、およびディメンションの情報が含まれています。 そのため、カスタム メトリックが Azure Monitor に最初に出力された際に、メトリックの定義が自動的に作成されます。 このメトリック定義はその後、そのメトリック定義によってメトリックが出力された対象である任意のリソースで検出可能です。

> [!NOTE]  
> Azure Monitor では、カスタム メトリックの**単位**の定義はまだサポートされていません。

## <a name="using-custom-metrics"></a>カスタム メトリックの使用
カスタム メトリックが Azure Monitor に送信されたら、Azure portal を使ってそれらを参照し、Azure Monitor REST API を使ってクエリを実行できます。 また、アラートを作成して、特定の条件が満たされたときに通知が送られるようにすることもできます。

> [!NOTE]
> カスタム メトリックを表示するには、閲覧者または共同作成者ロールである必要があります。

### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Azure portal からカスタム メトリックを参照する
1.    [Azure ポータル](https://portal.azure.com)にアクセスします。
2.    **[監視]** ウィンドウを選択します。
3.    **[メトリック]** を選びます。
4.    カスタム メトリックを出力した対象のリソースを選択します。
5.    カスタム メトリックのメトリック名前空間を選択します。
6.    カスタム メトリックを選択します。

## <a name="supported-regions"></a>サポートされているリージョン
パブリック プレビュー期間中、カスタム メトリックを発行する機能は一部の Azure リージョンでのみ利用できます。 つまりメトリックは、サポートされているいずれかのリージョンにあるリソースに対してしか発行できません。 次の表は、カスタム メトリックがサポートされている Azure リージョンを示したものです。 この表には、それらのリージョンにあるリソースのメトリックが発行される、対応エンドポイントも示してあります。

|Azure リージョン |リージョンのエンドポイントのプレフィックス|
|---|---|
| **米国およびカナダ** | |
|米国中西部 | https:\//westcentralus.monitoring.azure.com |
|米国西部 2       | https:\//westus2.monitoring.azure.com |
|米国中北部 | https:\//northcentralus.monitoring.azure.com
|米国中南部| https:\//southcentralus.monitoring.azure.com |
|米国中部      | https:\//centralus.monitoring.azure.com |
|カナダ中部 | https:\//canadacentral.monitoring.azure.com |
|米国東部| https:\//eastus.monitoring.azure.com |
|米国東部 2 | https:\//eastus2.monitoring.azure.com |
| **ヨーロッパ** | |
|北ヨーロッパ    | https:\//northeurope.monitoring.azure.com |
|西ヨーロッパ     | https:\//westeurope.monitoring.azure.com |
|英国南部 | https:\//uksouth.monitoring.azure.com
|フランス中部 | https:\//francecentral.monitoring.azure.com |
| **アフリカ** | |
|南アフリカ北部 | https:\//southafricanorth.monitoring.azure.com |
| **アジア** | |
|インド中部 | https:\//centralindia.monitoring.azure.com |
|オーストラリア東部 | https:\//australiaeast.monitoring.azure.com |
|東日本 | https:\//japaneast.monitoring.azure.com |
|東南アジア  | https:\//southeastasia.monitoring.azure.com |
|東アジア | https:\//eastasia.monitoring.azure.com |
|韓国中部   | https:\//koreacentral.monitoring.azure.com |

## <a name="latency-and-storage-retention"></a>待機時間とストレージのリテンション期間

新しいメトリックを追加した場合、またはメトリックに新しいディメンションが追加された場合、表示されるまでに最大 2 分から 3 分かかる場合があります。 システムに追加されたデータは、ほぼ間違いなく 30 秒未満で表示されます。 

メトリックを削除するかディメンションを削除した場合、変更が反映されてシステムから削除されるまでに 1 週間から 1 か月かかることがあります。

## <a name="quotas-and-limits"></a>クォータと制限
Azure Monitor では、カスタム メトリックの使用に次の制限があります。

|カテゴリ|制限|
|---|---|
|アクティブ時系列/サブスクリプション/リージョン|50,000|
|メトリックあたりのディメンション キー|10|
|メトリック名前空間、メトリック名、ディメンション キー、およびディメンション値の文字列の長さ|256 文字|

アクティブ時系列は、メトリック、ディメンション キー、またはディメンション値の任意の一意な組み合わせであり、過去 12 時間以内にメトリック値が発行されたものとして定義されます。

## <a name="next-steps"></a>次のステップ
さまざまなサービスのカスタム メトリックを使用する。 
 - [Virtual Machines](collect-custom-metrics-guestos-resource-manager-vm.md)
 - [仮想マシン スケール セット](collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Azure Virtual Machines (クラシック)](collect-custom-metrics-guestos-vm-classic.md)
 - [Telegraf エージェントを使用した Linux 仮想マシン](collect-custom-metrics-linux-telegraf.md)
 - [REST API](./metrics-store-custom-rest-api.md)
 - [従来の Cloud Services](collect-custom-metrics-guestos-vm-cloud-service-classic.md)
 

