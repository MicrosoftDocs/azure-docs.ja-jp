---
title: Azure Functions のスケールとホスティング
description: Azure Functions の従量課金プランと Premium プランの選択方法について説明します。
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 08/17/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 490fa46deabc822e416705fe9bf9c5cdb58f8cd6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936765"
---
# <a name="azure-functions-hosting-options"></a>Azure Functions のホスティング オプション

Azure で Function App を作成するときは、アプリのホスティング プランを選択する必要があります。 Azure Functions で利用できる基本のホスティング プランは 3 つあります。[従量課金プラン](consumption-plan.md)、[Premium プラン](functions-premium-plan.md)、[専用 (App Service) プラン](dedicated-plan.md)です。 すべてのホスティング プランは、Linux と Windows 仮想マシンの両方で、一般提供 (GA) されています。

お客様が選択したホスティング プランによって、次の動作が決まります。

*  Function App をスケールする方法。
* 各 Function App  インスタンスに利用できるリソース。
* Azure Virtual Network 接続などの高度な機能のサポート。

この記事では、さまざまなホスティング プランの詳しい比較を、Kubernetes ベースのホスティングと共に示します。

## <a name="overview-of-plans"></a>プランの概要

次に、Functions の 3 つの主要なホスティング プランの利点をまとめます。

| | |
| --- | --- |  
|**[従量課金プラン](consumption-plan.md)**| 自動的にスケールし、関数が実行されている際のコンピューティング リソースに対してのみ課金されます。<br/><br/>従量課金プランでは、Functions ホストのインスタンスは、受信イベントの数に基づいて動的に追加および削除されます。<br/><br/> ✔ 既定のホスティング プランです。<br/>✔ 関数の実行中にのみ課金されます。<br/>✔ 負荷が高い期間中でも、自動的にスケーリングします。|  
|**[Premium プラン](functions-premium-plan.md)**|需要に応じて自動的にスケーリングを行いながら、事前ウォーミングされたワーカーを使用して、アイドル状態になっても遅延なくアプリケーションを実行したり、より強力なインスタンスで実行したり、仮想ネットワークに接続したりすることができます。 <br/><br/>次のような状況では、Azure Functions の Premium プランを検討してください。 <br/><br/>✔ 関数を継続的に、またはほぼ継続的に実行したい。<br/>✔ 小規模な実行の回数が多く、実行料金が高いが、従量課金プランでの GB 秒は低い。<br/>✔ 従量課金プランで提供されるよりも多くの CPU またはメモリのオプションが必要である。<br/>✔ 従量課金プランで許可されている最大実行時間よりも長くコードを実行する必要がある。<br/>✔ 仮想ネットワーク接続など、従量課金プランでは利用できない機能が必要である。|  
|**[専用プラン](dedicated-plan.md)** |App Service プラン内で、Functions を通常の [App Service プラン料金](https://azure.microsoft.com/pricing/details/app-service/windows/)で実行します。<br/><br/>[Durable Functions](durable/durable-functions-overview.md) を使用できない、実行時間の長いシナリオに最適です。 次のような状況では、App Service プランを検討してください。<br/><br/>✔ 既に他の App Service インスタンスを実行している、使用率の低い既存の VM がある。<br/>✔ 関数を実行するカスタム イメージを提供したい。 <br/>✔ スケーリングとコストを予測できることが必要である。|  

この記事の比較表には、Functions アプリを実行するための最大の制御性と分離性を実現する、次のホスト オプションも含まれています。  

| | |
| --- | --- |  
|**[ASE](dedicated-plan.md)** | App Service Environment (ASE) は、App Service アプリを大規模かつ安全に実行するために完全に分離された専用の環境を提供する、Azure App Service の機能です。<br/><br/>ASE は、以下を必要とするアプリケーション ワークロードに最適です。 <br/><br/>✔ 高スケール。<br/>✔ コンピューティングの完全分離とセキュリティで保護されたネットワーク アクセス。<br/>✔ メモリ使用量が多い。|  
| **[Kubernetes](functions-kubernetes-keda.md)** | Kubernetes は、Kubernetes プラットフォーム上で実行される完全に分離された専用の環境を提供します。<br/><br/> Kubernetes は、以下を必要とするアプリケーション ワークロードに最適です。 <br/>✔ ハードウェア要件のカスタマイズ。<br/>✔ 分離およびセキュリティで保護されたネットワーク アクセス。<br/>✔ ハイブリッド環境またはマルチクラウド環境で実行可能。<br/>✔ 既存の Kubernetes アプリケーションやサービスと並行して実行。|  

この記事の残りの表では、さまざまな機能と動作のプランを比較します。 動的ホスティング プラン (従量課金と Premium) のコスト比較については、[Azure Functions の価格に関するページ](https://azure.microsoft.com/pricing/details/functions/)を参照してください。 さまざまな専用プラン オプションの価格については、[App Service の価格に関するページ](https://azure.microsoft.com/pricing/details/app-service/windows/)を参照してください。 

## <a name="operating-systemruntime"></a>オペレーティング システム/ランタイム

次の表は、ホスティング プランでサポートされているオペレーティング システムと言語ランタイムのサポートを示しています。

| | Linux<sup>1</sup><br/>コードのみ | Windows<sup>2</sup><br/>コードのみ | Linux<sup>1、3</sup><br/>Docker コンテナー |
| --- | --- | --- | --- |
| **[従量課金プラン](consumption-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python | .NET Core<br/>Node.js<br/>Java<br/>PowerShell Core | サポートなし  |
| **[Premium プラン](functions-premium-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python  | 
| **[専用プラン](dedicated-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |
| **[ASE](dedicated-plan.md)** | .NET Core<br/>Node.js<br/>Java<br/>Python |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core  |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python | 
| **[Kubernetes](functions-kubernetes-keda.md)** | 該当なし | 該当なし |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |

<sup>1</sup> Linux は、Python ランタイム スタックでサポートされている唯一のオペレーティング システムです。 <br/>
<sup>2</sup> Windows は、PowerShell ランタイム スタックでサポートされている唯一のオペレーティング システムです。<br/>
<sup>3</sup> Linux は、Docker コンテナーでサポートされている唯一のオペレーティング システムです。<br/>

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="scale"></a>スケール

次の表は、さまざまなホスティング プランのスケーリングの様子を比較したものです。

| | スケール アウト | 最大インスタンス数 |
| --- | --- | --- |
| **[従量課金プラン](consumption-plan.md)** | [イベント ドリブン](event-driven-scaling.md)。 負荷が高い期間中であっても、自動的にスケールアウトされます。 Azure Functions インフラストラクチャでは、受信するトリガー イベントの数に基づいて Functions ホストのインスタンスを追加することで、CPU とメモリのリソースがスケーリングされます。 | 200 |
| **[Premium プラン](functions-premium-plan.md)** | [イベント ドリブン](event-driven-scaling.md)。 負荷が高い期間中であっても、自動的にスケールアウトされます。 Azure Functions インフラストラクチャでは、関数がトリガーされるイベントの数に基づいて Functions ホストのインスタンスを追加することで、CPU とメモリのリソースがスケーリングされます。 |100|
| **[専用プラン](dedicated-plan.md)** <sup>1</sup> | 手動/自動スケール |10 - 20|
| **[ASE](dedicated-plan.md)** <sup>1</sup> | 手動/自動スケール |100 |
| **[Kubernetes](functions-kubernetes-keda.md)**  | [KEDA](https://keda.sh) を使用した Kubernetes クラスターのイベント ドリブン自動スケーリング。 | クラスタ&nbsp;によって&nbsp;変化&nbsp;&nbsp;|

<sup>1</sup> 各種 App Service プラン オプションに固有の制限については、[App Service プランの制限](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)に関する記事を参照してください。

## <a name="cold-start-behavior"></a>コールド スタートの動作

|    |    | 
| -- | -- |
| **[従量課金&nbsp;プラン](consumption-plan.md)** | アプリがアイドル状態になると 0 にスケーリングされます。つまり、一部の要求の起動時に追加の待機時間が発生する可能性があります。  従量課金プランには、コールド スタート時間を短縮するためのいくつかの最適化があります。これには、既に関数ホストと言語プロセスが実行されている、事前ウォーミングされたプレースホルダー関数からのプルが含まれます。 |
| **[Premium プラン](functions-premium-plan.md)** | コールド スタートを回避するためにインスタンスを常にウォーム状態に維持します。 |
| **[専用プラン](dedicated-plan.md)** | 専用プランで実行していると、Functions ホストを継続的に実行できます。つまり、コールド スタートは問題にならないということです。 |
| **[ASE](dedicated-plan.md)** | 専用プランで実行していると、Functions ホストを継続的に実行できます。つまり、コールド スタートは問題にならないということです。 |
| **[Kubernetes](functions-kubernetes-keda.md)**  | KEDA の構成によっては、コールド スタートを回避するようにアプリを構成できます。 0 にスケーリングするように構成されている場合は、新しいイベントに対してコールド スタートが発生します。 

## <a name="service-limits"></a>サービスの制限

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]

## <a name="networking-features"></a>ネットワーク機能

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

## <a name="billing"></a>課金

| | | 
| --- | --- |
| **[従量課金プラン](consumption-plan.md)** | 関数が実行された時間に対してだけ支払います。 課金は、実行数、実行時間、およびメモリの使用量に基づいて行われ、 |
| **[Premium プラン](functions-premium-plan.md)** | Premium プランは、必要なインスタンスや事前ウォーミングされたインスタンスで使用されたコア秒数とメモリに基づいています。 プランごとに少なくとも 1 つのインスタンスが常にウォーム状態である必要があります。 このプランでは、最も予測可能な価格が提供されます。 |
| * *[専用プラン](dedicated-plan.md)* | App Service プランの Function App に対する支払いは、Web アプリなどの他の App Service リソースの場合と同じです。|
| **[App Service Environment (ASE)](dedicated-plan.md)** | インフラストラクチャの支払いを行うための ASE には一定の月額料金があり、ASE のサイズが変化しても料金は変わりません。 App Service プランの vCPU あたりのコストもあります。 ASE でホストされているすべてのアプリは、分離された価格 SKU に含まれます。 |
| **[Kubernetes](functions-kubernetes-keda.md)**| お支払いは Kubernetes クラスターのコストだけです。関数に対する追加の課金はありません。  Function App は、通常のアプリと同じように、クラスターのアプリケーションのワークロードとして実行されます。 |

## <a name="next-steps"></a>次のステップ

+ [Azure Functions のデプロイ テクノロジ](functions-deployment-technologies.md) 
+ [Azure Functions 開発者ガイド](functions-reference.md)