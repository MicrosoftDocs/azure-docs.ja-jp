---
title: "Cloud Cruiser と Microsoft Azure Billing API の統合 | Microsoft Docs"
description: "Microsoft Azure 課金パートナーの Cloud Cruiser が Azure Billing API を製品に統合した独自の事例について説明します。  この記事は、Microsoft Azure Pack の使用/試用に関心を持っている Azure と Cloud Cruiser のユーザーには特に役立ちます。"
services: 
documentationcenter: 
author: BryanLa
manager: ruchic
editor: 
tags: billing
ms.assetid: b65128cf-5d4d-4cbd-b81e-d3dceab44271
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 02/03/2017
ms.author: mobandyo;sirishap;bryanla
translationtype: Human Translation
ms.sourcegitcommit: 09c18ca6a967c2930ddd2b16d37f0bc606712ea1
ms.openlocfilehash: 125b777e0ab05066cf066789b603b8edb15a0936


---
# <a name="cloud-cruiser-and-microsoft-azure-billing-api-integration"></a>Cloud Cruiser と Microsoft Azure Billing API の統合
この記事では、新しい Azure Billing API から収集した情報を Cloud Cruiser で使用して、ワークフローのコスト シミュレーションと分析を行う方法について説明します。

## <a name="azure-ratecard-api"></a>Azure RateCard API
RateCard API は、Azure の料金情報を提供します。 適切な資格情報を認証した後は、API を照会し、Azure で使用できるサービスに関するメタデータや、自分のプラン ID に関連付けられた料金を収集できます。

次に、A0 (Windows) インスタンスの料金を表示する API の応答例を示します。

    {
        "MeterId": "0e59ad56-03e5-4c3d-90d4-6670874d7e29",
        "MeterName": "Compute Hours",
        "MeterCategory": "Virtual Machines",
        "MeterSubCategory": "A0 VM (Windows)",
        "Unit": "Hours",
        "MeterRates":
        {
            "0": 0.029
        },
        "EffectiveDate": "2014-08-01T00:00:00Z",
        "IncludedQuantity": 0.0,
        "MeterStatus": "Active"
    },

### <a name="cloud-cruisers-interface-to-azure-ratecard-api"></a>Azure RateCard API に対する Cloud Cruiser のインターフェイス
Cloud Cruiser は、RateCard API をさまざまな方法で利用できます。 この記事では、IaaS ワークロード コストのシミュレーションと分析に使用する方法を説明します。

この使用事例を読むにあたって、Microsoft Azure Pack (WAP) でいくつかのインスタンスが実行されているワークロードを想定してください。 目標は、この同じワークロードを Azure でシミュレートし、移行した場合のコストを見積もることです。 このシミュレーションを作成するために、主に次の&2; つの作業を実行します。

1. **RateCard API から収集したサービス情報のインポートと処理。** この作業はブックでも実行されます。この場合、RateCard API からの抽出を変換し、新しい料金プランに発行します。 この新しい料金プランは、Azure の料金を見積もるシミュレーションに使用されます。
2. **IaaS の WAP サービスと Azure サービスの正規化。** 既定では、WAP サービスは個々のリソース (CPU、メモリ サイズ、ディスク サイズなど) に基づいていますが、Azure サービスはインスタンス サイズ (A0、A1、A2 など) に基づいています。 この&1; つ目の作業は、Cloud Cruiser の ETL エンジン (ブックと呼ばれます) で実行できます。これらのリソースは、Azure インスタンス サービスと同様に、インスタンス サイズに基づいてまとめることができます。

### <a name="import-data-from-the-ratecard-api"></a>RateCard API からのデータのインポート
Cloud Cruiser のブックには、RateCard API の情報の収集と処理を自動化する機能があります。  ETL (抽出-変換-読み込み) ブックを使用して、データのコレクション、変換、および Cloud Cruiser データベースへの発行を構成できます。

各ブックには、1 つ以上のコレクションを含めることができます。そのため、複数のソースの情報を関連付けて、使用状況データの補完と拡大を行うことができます。 次の&2; つのスクリーンショットは、既存のブックに新しい "*コレクション*" を作成する方法と、RateCard API から情報を "*コレクション*" にインポートする方法を示しています。

![図 1 - 新しいコレクションの作成][1]

![図 2 - 新しいコレクションからのデータのインポート][2]

データをブックにインポートすると、複数のステップで変換プロセスを作成して、データを変更し、モデルを作成することができます。 この例では、サービスとしてのインフラストラクチャ (IaaS) にのみ関心があるので、変換ステップを使用して不要な行や、IaaS 以外のサービスに関連するレコードを削除することができます。

次のスクリーン ショットは、RateCard API から収集されたデータの処理に使用する変換ステップを示しています。

![図 3 - RateCard API から収集したデータを処理する変換ステップ][3]

### <a name="defining-new-services-and-rate-plans"></a>新しいサービスと料金プランの定義
Cloud Cruiser には、サービスを定義する方法が複数あります。 選択肢の&1; つは、使用状況データからサービスをインポートすることです。 一般的に、この方法は、プロバイダーがサービスを定義済みであるパブリック クラウドを使用する場合にのみ使用されます。

料金プランは、有効期間、ユーザー グループなどのオプションに基づいて、さまざまなサービスに適用できる料金または価格のセットです。 Cloud Cruiser の料金プランを使用して、シミュレーションまたは "what-if" シナリオを作成し、サービスの変更がワークロードの総コストに与える影響を理解できます。

この例では、RateCard API からのサービス情報を使用して Cloud Cruiser に新しいサービスを定義します。 同様に、サービスに関連付けられた料金を使用して、Cloud Cruiser の新しい料金プランを作成できます。

変換プロセスが終わった後は、新しいステップを作成し、新しいサービスと料金として RateCard API からデータを発行することができます。

![図 4 - 新しい料金としての RateCard APIのデータの発行][4]

### <a name="verify-azure-services-and-rates"></a>Azure サービスと料金
サービスと料金を発行したら、Cloud Cruiser の *[Services]* タブでインポートされたサービスの一覧を確認できます。

![図 5 - 新しいサービスの確認][5]

*[Rate Plans]* タブでは、“AzureSimulation” という新しい料金プランと、RateCard API からインポートされた料金を確認できます。

![図 6 - 新しい料金プランと関連付けられている料金の確認][6]

### <a name="normalize-wap-and-azure-services"></a>WAP と Azure サービスの正規化
WAP の既定では、コンピューティング、メモリ、およびネットワーク リソースの使用に基づいて使用状況情報を提供します。 Cloud Cruiser では、これらのリソースの割り当てまたは測定使用量に直接基づいてサービスを定義できます。 たとえば、各時間の CPU 使用量の基本料金を設定したり、インスタンスに割り当てられたメモリの GB 数に対して課金したりすることができます。

この例では、WAP と Azure のコストを比較するために、WAP のリソース使用状況を収集してまとめる必要があります。その結果のデータは、Azure サービスに対応付けることができます。 この変換は、ブックで簡単に実装できます。

![図 7 - サービスを正規化する WAP データの変換][7]

ブックの最後のステップは、データを Cloud Cruiser データベースに発行する処理です。 このステップで、使用状況データはサービスにまとめられ (サービスは Azure サービスに対応付けられます)、既定の料金に関連付けられて料金が作成されます。

ブックが完成すると、Scheduler に作業を追加し、ブックを実行する頻度と時間を指定することで、データの処理を自動化できます。

![図 8 - ブックのスケジュール設定][8]

### <a name="create-reports-for-workload-cost-simulation-analysis"></a>ワークロード コスト シミュレーション分析のレポート作成
使用状況を収集し、Cloud Cruiser データベースに読み込むと、Cloud Cruiser Insights モジュールを利用して、目的のワークロード コスト シミュレーションを作成できます。

このシナリオを説明するために、次のレポートを作成しました。

![コストの比較][9]

上のグラフは、サービスを別のコスト比較を示し、WAP (濃い青色) と Azure (明るい青色) の間の特定のサービスごとのワークロードの実行料金を比較します。

下のグラフは同じデータですが、部門別に分けられます。 これは、WAP と Azure でワークロードを実行している各部門の料金を示しています。また、節約バー (緑色) は&2; つの差です。

## <a name="azure-usage-api"></a>Azure Usage API
### <a name="introduction"></a>はじめに
最近、Microsoft は Azure Usage API を導入しました。これにより、利用者は利用状況データをプログラムで取得して、消費量についての洞察が得ることができます。 この API を介して豊富なデータセットを利用できるため、この機能の導入は Cloud Cruiser の顧客にとってはよいニュースです。

Cloud Cruiser では、いくつかの方法での Usage API との統合を利用できます。 API によって使用可能な粒度 (1 時間ごとの利用状況情報) とリソースのメタデータ情報は、柔軟なショーバック モデルまたはチャージバック モデルをサポートするために必要なデータセットを提供します。 

このチュートリアルでは、Cloud Cruiser が Usage API の情報から得ることができる利点の&1; つの例をご紹介します。 具体的には、Azure でリソース グループを作成し、アカウント構造のタグを関連付けます。その後、タグ情報の取得と Cloud Cruiser に対するタグ情報の処理についてプロセスを記述します。

最終的な目標は、次のようなレポートを作成し、タグによって設定されたアカウント構造に基づいて、コストと消費量を分析できるようになることです。

![図 10 - タグを使用した分析結果のレポート][10]

### <a name="microsoft-azure-tags"></a>Microsoft Azure のタグ
Azure Usage API を介して使用できるデータには、消費量の情報だけでなく、関連付けられているすべてのタグを含むリソースのメタデータも含まれています。 タグを使用すると、リソースを簡単に整理できますが、効果を上げるには、次のことを確認する必要があります。

* プロビジョニング時にタグがリソースに正しく適用されている
* タグが、ショーバック/チャージバック プロセスで適切に使用されて、組織のアカウント構造と利用状況を結び付けている

この両方の要件への適合は、特にプロビジョニング側や課金側で手動のプロセスを行っていると、難しくなる場合があります。 入力ミス、タグの誤りや不足は、顧客がタグの使用時に訴える共通した不満です。このようなエラーは、課金側の対応を非常に難しくする場合があります。

新しい Azure Usage API によって、Cloud Cruiser はリソースのタグ付け情報を取得できます。また、ブックと呼ばれる高度な ETL ツールを使用することで、一般的なタグ付けエラーを修正できます。 正規表現およびデータの相関関係を利用する変換で、Cloud Cruiser は、タグ付けが誤っているリソースを特定し、正しいタグを適用します。リソースとコンシューマーが正しく関連付けられるようにします。

課金側では、Cloud Cruiser はショーバック/チャージバック プロセスを自動化し、タグ情報を利用して適切なコンシューマー (部門、部門、プロジェクトなど) に利用状況を結び付けることができます。 この自動化は大きな進歩であり、一貫性のある監査可能な課金プロセスを実現できます。

### <a name="creating-a-resource-group-with-tags-on-microsoft-azure"></a>Microsoft Azure でのタグによるリソース グループの作成
このチュートリアルの最初の手順では、Azure Portal でリソース グループを作成し、リソースに関連付ける新しいタグを作成します。 この例では、Department、Environment、Owner、Project というタグを作成します。

以下のスクリーン ショットに、関連付けたタグを使用するサンプル リソース グループを示します。

![図 11 - Azure Portal でタグが関連付けられたリソース グループ][11]

次の手順では、Usage API から Cloud Cruiser に情報を取得します。 現在、Usage API は、JSON 形式で応答を提供します。 取得されるデータのサンプルを次に示します。

    {
      "id": "/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXX/providers/Microsoft.Commerce/UsageAggregates/Daily_BRSDT_20150623_0000",
      "name": "Daily_BRSDT_20150623_0000",
      "type": "Microsoft.Commerce/UsageAggregate",
      "properties":
      {
        "subscriptionId": "bb678b04-0e48-4b44-XXXX-XXXXXXXXX",
        "usageStartTime": "2015-06-22T00:00:00+00:00",
        "usageEndTime": "2015-06-23T00:00:00+00:00",
        "meterName": "Compute Hours",
        "meterRegion": "",
        "meterCategory": "Virtual Machines",
        "meterSubCategory": "Standard_D1 VM (Non-Windows)",
        "unit": "Hours",
        "instanceData": "{\"Microsoft.Resources\":{\"resourceUri\":\"/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXXX/resourceGroups/DEMOUSAGEAPI/providers/Microsoft.Compute/virtualMachines/MyDockerVM\",\"location\":\"eastus\",\"tags\":{\"Department\":\"Sales\",\"Project\":\"Demo Usage API\",\"Environment\":\"Test\",\"Owner\":\"RSE\"},\"additionalInfo\":{\"ImageType\":\"Canonical\",\"ServiceType\":\"Standard_D1\"}}}",
        "meterId": "e60caf26-9ba0-413d-a422-6141f58081d6",
        "infoFields": {},
        "quantity": 8

      },
    },


### <a name="import-data-from-the-usage-api-into-cloud-cruiser"></a>Usage API から Cloud Cruiser へのデータのインポート
Cloud Cruiser のブックには、Usage API の情報の収集と処理を自動化する機能があります。 ETL (抽出-変換-読み込み) ブックを使用して、データのコレクション、変換、および Cloud Cruiser データベースへの発行を構成できます。

各ブックには、1 つ以上のコレクションを含めることができます。 そのため、複数のソースの情報を関連付けて、使用状況データの補完と拡大を行うことができます。 この例では、Azure テンプレート ブックに新しいシート (*UsageAPI*) を作成し、新しい*コレクション*を設定して Usage API から情報をインポートします。

![図 3 - UsageAPI シートにインポートする Usage API データ][12]

既にこのブックには、サービスを Azure からインポートするシート (*ImportServices*) と Billing API の消費量情報を処理するシート (*PublishData*) があることに注意してください。

次に、*Usage API* を使用して UsageAPI シートを設定し、*PublishData* シートでその情報を Billing API の消費データと関連付けます。

### <a name="processing-the-tag-information-from-the-usage-api"></a>Usage API からのタグ情報の処理
ブックにデータをインポートした後は、API からの情報を処理するために、 *UsageAPI* シートで変換の手順を作成します。 最初の手順では、"JSON split" プロセッサを使用して&1; つのフィールドからタグを抽出し、それぞれ (Department、Project、Owner、Environment) に対してフィールドを&1; つ作成します。

![図 4 - タグ情報に対する新しいフィールドの作成][13]

"Networking" サービスにはタグ情報がありません (黄色のボックス) が、 *ResourceGroupName* フィールドを見れば、このサービスは同じリソース グループの一部であることを確認できます。 このリソース グループの他のリソースにはタグがあるため、以降のプロセスでは、この情報を使用して、不足しているタグをこのリソースに適用できます。

次の手順では、タグの情報を *ResourceGroupName*に関連付けるルックアップ テーブルを作成します。 このルックアップ テーブルは、次の手順でタグ情報を使用して、消費データを強化するために使用します。

### <a name="adding-the-tag-information-to-the-consumption-data"></a>消費データへのタグ情報の追加
ここからは、*PublishData* シートを使用できます。このシートでは、Billing API の消費情報を処理して、タグから抽出されるフィールドを追加します。 このプロセスは、前の手順で作成したルックアップ テーブルを参照して実行します。その際、参照のキーとして *ResourceGroupName* を使用します。

![図 5 - 参照からの情報を使用したアカウント構造の設定][14]

"Networking" サービスの適切なアカウント構造のフィールドが適用されて、不足タグの問題が解決されたことに注意してください。 さらに、ターゲット リソース グループ以外のリソースのアカウント構造フィールドに "Other" を設定して、レポートでこれらを区別するようにもしました。

ここで、利用状況データを公開するために、手順がもう&1; つ必要です。 この手順では、料金プランで定義されている各サービスの適切な料金が、利用状況情報に適用され、計算された料金がデータベースに読み込まれます。

この手順の最も大きなメリットは、このプロセスを&1; 回だけ行えばよいという点です。 ブックが完成したら、必要なのはスケジューラに追加することだけです。ブックは&1; 時間ごとに、または毎日スケジュールされた時刻に実行されます。 新しいレポートを作成するか、既存のレポートをカスタマイズするだけで、データの分析を行って、クラウドの利用状況から意味のある情報を取得できます。

### <a name="next-steps"></a>次のステップ
* Cloud Cruiser のブックとレポートの作成手順については、Cloud Cruiser のオンライン [ドキュメント](http://docs.cloudcruiser.com/) を参照してください (有効なログインが必要です)。  Cloud Cruiser の詳細については、 [info@cloudcruiser.com](mailto:info@cloudcruiser.com)に関連付けるルックアップ テーブルを作成します。
* Azure Resource Usage API と RateCard API の概要については、「 [Microsoft Azure のリソース消費を把握する](billing-usage-rate-card-overview.md) 」を参照してください。
* 両方の API の詳細については、「 [Azure Billing REST API リファレンス](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) 」を参照してください (Azure リソース マネージャーに用意されている API セットに含まれています)。
* サンプル コードをすぐに確認するには、「 [Azure コード サンプル](https://azure.microsoft.com/documentation/samples/?term=billing)」の「Microsoft Azure Billing API コード サンプル」を参照してください。

### <a name="learn-more"></a>詳細情報
* Azure リソース マネージャーの詳細については、「 [Azure リソース マネージャーの概要](../azure-resource-manager/resource-group-overview.md) 」を参照してください。

<!--Image references-->

[1]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Create-New-Workbook-Collection.png "図 1 - 新しいコレクションの作成"
[2]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Import-Data-From-RateCard.png "図 2 - 新しいコレクションからのデータのインポート"
[3]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transformation-Steps-Process-RateCard-Data.png "図 3 - RateCard API から収集したデータを処理する変換ステップ"
[4]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Publish-RateCard-Data-New-Services-Rates.png "図 4 - 新しい料金として RateCard API のデータを発行する"
[5]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing1.png "図 5 - 新しいサービスの確認"
[6]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing2.png "図 6 - 新しい料金プランと関連付けられている料金の確認"
[7]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transforming-WAP-Normalize-Services.png "図 7 - サービスを正規化する WAP データの変換"
[8]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workbook-Scheduling.png "図 8 - ブックのスケジュール設定"
[9]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workload-Cost-Simulation-Report.png "図 9 - ワークロードのコスト比較シナリオのサンプル レポート"
[10]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/1_ReportWithTags.png "図 10 - タグを使用した分析結果のレポート"
[11]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/2_ResourceGroupsWithTags.png "図 11 - Azure Portal でタグが関連付けられたリソース グループ"
[12]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/3_ImportIntoUsageAPISheet.png "図 12 - UsageAPI シートにインポートする Usage API データ"
[13]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/4_NewTagField.png "図 13 - タグ情報に対する新しいフィールドの作成"
[14]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/5_PopulateAccountStructure.png "図 14 - 参照からの情報を使用したアカウント構造の設定"



<!--HONumber=Feb17_HO2-->


