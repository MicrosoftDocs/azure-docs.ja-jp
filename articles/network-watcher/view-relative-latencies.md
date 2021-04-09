---
title: 特定の場所から Azure リージョンへの相対待機時間を確認する
description: 特定の場所から Azure リージョンへのインターネット プロバイダー全体の相対的な待機時間を確認する方法について説明します。
services: network-watcher
documentationcenter: ''
author: damendo
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2017
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: 938315dac80ae51984851083753a9cb8f77662b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84737905"
---
# <a name="view-relative-latency-to-azure-regions-from-specific-locations"></a>特定の場所から Azure リージョンへの相対待機時間を確認する

> [!WARNING]
> 現在、この機能はプレビュー段階にあり、安定性がまだテスト中です。

このチュートリアルでは、Azure [Network Watcher](network-watcher-monitoring-overview.md) サービスを使用して、ユーザーの統計分析に基づいてアプリケーションまたはサービスをデプロイする Azure リージョンを決定する方法について説明します。 このチュートリアルは、サービス プロバイダーの Azure への接続を評価する場合にも役立ちます。  
        

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-network-watcher"></a>ネットワーク ウォッチャーを作成する

Azure [リージョン](https://azure.microsoft.com/regions)に 1 つ以上のネットワーク ウォッチャーが既にある場合は、このセクションのタスクをスキップできます。 ネットワーク ウォッチャーのリソース グループを作成します。 この例では、米国東部リージョンでリソース グループを作成しますが、任意の Azure リージョンでリソース グループを作成できます。

```powershell
New-AzResourceGroup -Name NetworkWatcherRG -Location eastus
```

ネットワーク ウォッチャーを作成します。 少なくとも 1 つの Azure リージョンでネットワーク ウォッチャーを作成する必要があります。 この例では、米国東部の Azure リージョンにネットワーク ウォッチャーを作成しています。

```powershell
New-AzNetworkWatcher -Name NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG -Location eastus
```

## <a name="compare-relative-network-latencies-to-a-single-azure-region-from-a-specific-location"></a>特定の場所から単一の Azure リージョンに対する相対的なネットワーク待機時間を比較する

特定の場所からサービスがデプロイされている Azure リージョンまでについてサービス プロバイダーを評価し、ユーザーから "サイトが遅い" などと報告される問題を解決します。 たとえば、次のコマンドは、2017 年 12 月 13 日から 15 日の米国ワシントン州と米国西部 2 Azure リージョン間のインターネット サービス プロバイダーの平均相対待機時間を返します。

```powershell
Get-AzNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Location "West US 2" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

> [!NOTE]
> 前のコマンドで指定したリージョンは、ネットワーク ウォッチャーを取得したときに指定したリージョンと同じである必要はありません。 前のコマンドで必要な指定は、既存のネットワーク ウォッチャーのみです。 ネットワーク ウォッチャーはどのリージョンにも設置できます。 `-Country` と `-State` には、有効な値を指定する必要があります。 値は大文字と小文字が区別されます。 限られた数の国/リージョン、州、および市区町村のデータを使用できます。 「[使用できる国/リージョン、州、市区町村、プロバイダーを表示する](#view-available)」のコマンドを実行して、前のコマンドで使用できる国/リージョン、州、市区町村の一覧を表示します。 

> [!WARNING]
> `-StartTime` と `-EndTime` には、過去 30 日以内の日付を指定する必要があります。 それより前の日付を指定すると、データが返されません。

前のコマンドを実行すると、次のように出力されます。

```powershell
AggregationLevel   : State
ProviderLocation   : {
                       "Country": "United States",
                       "State": "washington"
                     }
ReachabilityReport : [
                       {
                         "Provider": "Qwest Communications Company, LLC - ASN 209",
                         "AzureLocation": "West US 2",
                         "Latencies": [
                           {
                             "TimeStamp": "2017-12-14T00:00:00Z",
                             "Score": 92
                           },
                           {
                             "TimeStamp": "2017-12-13T00:00:00Z",
                             "Score": 92
                           }
                         ]
                       },
                       {
                         "Provider": "Comcast Cable Communications, LLC - ASN 7922",
                         "AzureLocation": "West US 2",
                         "Latencies": [
                           {
                             "TimeStamp": "2017-12-14T00:00:00Z",
                             "Score": 96
                           },
                           {
                             "TimeStamp": "2017-12-13T00:00:00Z",
                             "Score": 96
                           }
                         ]
                       }
                     ]
```

返される出力の **Score** の値はリージョンとプロバイダー全体の相対的な待機時間です。 スコア 1 は最悪 (最長) の待機時間であり、100 は最短の待機時間です。 相対待機時間はその日の平均です。 前の例では、待機時間が両日とも同じであり、2 つのプロバイダーの待機時間の差が小さいことは明らかですが、1 から 100 のスケールでは 2 つのプロバイダーの待機時間が短いこともわかります。 これは予想されることですが、米国ワシントン州は米国西部 2 の Azure リージョンに物理的に近いため、結果が予想どおりにならないことがあります。 指定する日付の範囲を大きくするほど、時間経過と共に待機時間を平均化することができます。

## <a name="compare-relative-network-latencies-across-azure-regions-from-a-specific-location"></a>特定の場所から Azure リージョン全体に対する相対的なネットワーク待機時間を比較する

`-Location` を使用して特定の場所と特定の Azure リージョン間の相対的な待機時間を指定するのではなく、特定の物理的な場所からすべての Azure リージョンへの相対的な待機時間を判断することもできます。 たとえば、次のコマンドを使用すると、プライマリ ユーザーがワシントン州にある Comcast ユーザーである場合に、サービスをデプロイする Azure リージョンを評価できます。

```powershell
Get-AzNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Provider "Comcast Cable Communications, LLC - ASN 7922" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

> [!NOTE]
> 単一の場所を指定する場合とは異なり、場所を指定しない場合や、複数の場所 ("米国西部 2"、"米国西部" など) を指定する場合は、コマンドの実行時にインターネット サービス プロバイダーを指定する必要があります。 

## <a name="view-available-countriesregions-states-cities-and-providers"></a><a name="view-available"></a>使用できる国/リージョン、州、市区町村、プロバイダーを表示する

特定のインターネット サービス プロバイダー、国/リージョン、州、および市区町村のデータを使用できます。 データを表示できるすべてのインターネット サービス プロバイダー、国/リージョン、州、および市区町村の一覧を表示するには、次のコマンドを入力します。

```powershell
Get-AzNetworkWatcherReachabilityProvidersList -NetworkWatcherName NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG
```

前のコマンドで返された国/リージョン、州、および市区町村のデータのみを使用できます。 前のコマンドでは、既存のネットワーク ウォッチャーを指定する必要があります。 この例では、*NetworkWatcherRG* というリソース グループの *NetworkWatcher_eastus* ネットワーク ウォッチャーを指定していますが、任意の既存のネットワーク ウォッチャーを指定できます。 既存のネットワーク ウォッチャーがない場合は、「[ネットワーク ウォッチャーを作成する](#create-a-network-watcher)」のタスクを実行して作成します。 

前のコマンドを実行した後は、必要に応じて、**Country**、**State**、**City** の有効な値を指定して返される出力をフィルター処理できます。  たとえば、米国ワシントン州シアトルで使用できるインターネット サービス プロバイダーの一覧を表示するには、次のコマンドを入力します。

```powershell
Get-AzNetworkWatcherReachabilityProvidersList `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -City Seattle `
  -Country "United States" `
  -State washington
```

> [!WARNING]
> **Country** の指定値には、大文字と小文字を使用する必要があります。 **State** と **City** の指定値には、小文字を使用する必要があります。 **Country**、**State**、**City** の値を指定せずにコマンドを実行すると、値の一覧が出力で返されます。 大文字と小文字の指定を間違ったり、これらのプロパティに値を指定せずにコマンドを実行した場合の出力に含まれない **Country**、**State**、または **City** の値を指定したりすると、返される出力は空になります。
