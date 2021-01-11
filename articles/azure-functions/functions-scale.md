---
title: Azure Functions のスケールとホスティング
description: Azure Functions の従量課金プランと Premium プランの選択方法について説明します。
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 08/17/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 80bb59527f416afd78b992fb12a4ef72956f91b7
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88587227"
---
# <a name="azure-functions-scale-and-hosting"></a>Azure Functions のスケールとホスティング

Azure で Function App を作成するときは、アプリのホスティング プランを選択する必要があります。 Azure Functions で利用できる基本のホスティング プランは 3 つあります。[従量課金プラン](#consumption-plan)、[Premium プラン](#premium-plan)、[専用 (App Service) プラン](#app-service-plan)です。 すべてのホスティング プランは、Linux と Windows 仮想マシンの両方で、一般提供 (GA) されています。

お客様が選択したホスティング プランによって、次の動作が決まります。

* Function App をスケールする方法。
* 各 Function App インスタンスに利用できるリソース。
* Azure Virtual Network 接続などの高度な機能のサポート。

従量課金プランと Premium プランのいずれも、コードの実行時に自動的にコンピューティング能力が追加されます。 アプリは、負荷を処理する必要があるときはスケールアウトされ、コードの実行が停止するとスケールインされます。 従量課金プランの場合、アイドル状態の仮想マシンの料金や、予約容量の事前の料金を支払う必要もありません。  

Premium プランには、Premium コンピューティング インスタンス、無期限にインスタンスをウォーム状態に保つ機能、VNet 接続などの追加機能があります。

App Service プランでは、お客様が管理している専用のインフラストラクチャを利用できます。 Function App はイベントに基づいてスケーリングされません。つまり、ゼロにスケールインされることはありません。 ([[常時接続]](#always-on) を有効にする必要があります)。

さまざまなホスティング プラン (Kubernetes ベースのホスティングを含む) の詳細な比較については、[ホスティング プランの比較セクション](#hosting-plans-comparison)を参照してください。

## <a name="consumption-plan"></a>従量課金プラン

従量課金プランを使用する場合、Azure Functions ホストのインスタンスは、受信イベントの数に基づいて動的に追加および削除されます。 このサーバーレス プランではスケーリングが自動的に行われ、関数の実行中にのみコンピューティング リソースに対して料金が発生します。 従量課金プランでは、構成可能な期間が経過すると関数の実行はタイムアウトします。

課金は、実行数、実行時間、およびメモリの使用量に基づいて行われ、 Function App 内のすべての関数にわたって集計されます。 詳細については、[Azure Functions の価格に関するページ](https://azure.microsoft.com/pricing/details/functions/)を参照してください。

従量課金プランは既定のホスティング プランであり、次の利点があります。

* 関数の実行中にのみ課金されます。
* 負荷が高い期間中であっても、自動的にスケールアウトされます。

同じリージョンの Function App を同じ従量課金プランに割り当てることができます。 同じ従量課金制プランで複数のアプリケーションを実行しても、マイナス面や影響はありません。 同じ従量課金プランに複数のアプリを割り当てても、各アプリの回復性、スケーラビリティ、または信頼性には影響しません。

従量課金プランで実行しているときのコストを見積もる方法の詳細については、[従量課金プランのコストの概要](functions-consumption-costs.md)に関するページを参照してください。

## <a name="premium-plan"></a><a name="premium-plan"></a>Premium プラン

Premium プランを使用すると、従量課金プランと同じように、Azure Functions ホストのインスタンスが、受信イベントの数に基づいて追加および削除されます。  Premium プランは次の機能をサポートします。

* コールド スタートを回避するために常にウォーム状態のインスタンス
* VNet の接続
* 無制限の実行期間 (60 分保証)
* Premium インスタンス サイズ (1 コア、2 コア、4 コアのインスタンス)
* 予測可能な料金
* 複数の Function App を含むプランでの高密度アプリ割り当て

Premium プランで Function App を作成する方法については、「[Azure Functions の Premium プラン](functions-premium-plan.md)」を参照してください。

Premium プランの課金は、実行や消費されたメモリごとの課金ではなく、必要なインスタンスや事前ウォーミングされたインスタンスで使用されたコア秒数とメモリに基づいています。 1 つのプランにつき、少なくとも 1 つのインスタンスが常にウォーム状態である必要があります。 つまり、実行数に関係なく、アクティブなプランごとに最小の月額コストがかかります。 なお、Premium プランのすべての Function App では、事前ウォーミングされたインスタンスとアクティブなインスタンスが共有されことに注意してください。

次のような状況では、Azure Functions の Premium プランを検討してください。

* 関数を継続的に、またはほぼ継続的に実行したい。
* 小規模な実行の回数が多く、実行料金が高いが、従量課金プランでの GB 秒の請求額は低い。
* 従量課金プランで提供されるよりも多くの CPU またはメモリのオプションが必要である。
* 従量課金プランで[許可されている最大実行時間](#timeout)よりも長くコードを実行する必要がある。
* 仮想ネットワーク接続など、Premium プランでのみ利用できる機能が必要である。 

## <a name="dedicated-app-service-plan"></a><a name="app-service-plan"></a>専用 (App Service) プラン

Function App は、他の App Service アプリ (Basic、Standard、Premium、Isolated SKU) と同じ専用 VM 上でも実行できます。

次のような状況では、App Service プランを検討してください。

* 既に他の App Service インスタンスを実行している、使用率の低い既存の VM がある。
* 関数を実行するカスタム イメージを提供したい。

App Service プランの Function App に対する支払いは、Web アプリなどの他の App Service リソースの場合と同じです。 App Service プランの仕組みの詳細については、「[Azure App Service プランの詳細な概要](../app-service/overview-hosting-plans.md)」を参照してください。

App Service プランでは、VM インスタンスを追加して、手動でスケールアウトできます。 自動スケーリングを有効にすることもできます。 詳細については、「[手動または自動によるインスタンス数のスケール変更](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json)」を参照してください。 別の App Service プランを選択してスケールアップすることもできます。 詳細については、 [Azure でのアプリのスケールアップ](../app-service/manage-scale-up.md) に関するページを参照してください。 

App Service プランで JavaScript 関数を実行する場合は、CPUの少ないプランを選択してください。 詳細については、[シングルコア App Service プランの選択](functions-reference-node.md#choose-single-vcpu-app-service-plans)に関するページをご覧ください。 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

[App Service Environment](../app-service/environment/intro.md) (ASE) で実行すると、関数を完全に分離し、高スケールを活用できます。

### <a name="always-on"></a><a name="always-on"></a> 常にオン

App Service プランを実行する場合、Function App が正常に実行されるように、**常時接続** 設定を有効にする必要があります。 App Service プランでは、関数のランタイムは非アクティブな状態が数分続くとアイドル状態となるため、関数を "起こす" ことができるのは HTTP トリガーのみとなります。 常時接続は App Service プランでのみ使用可能です。 従量課金プランでは、Function App はプラットフォームにより自動的にアクティブ化されます。

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]


常時接続が有効になっている場合でも、個々の関数の実行タイムアウトは [host.json](functions-host-json.md#functiontimeout) プロジェクト ファイルの `functionTimeout` 設定によって制御できます。

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>既存のアプリケーションのホスティング プランを決定する

自分の Function App で使用されるホスティング プランを確認するには、[Azure portal](https://portal.azure.com) で Function App の **[概要]** タブの **[App Service プラン]** を表示します。 価格レベルを表示するには、 **[App Service プラン]** の名前を選択し、左側のウィンドウから **[プロパティ]** を選択します。

![ポータルでのスケーリング プランの表示](./media/functions-scale/function-app-overview-portal.png)

次のように、プランを判断するのに、Azure CLI を使用することもできます。

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

このコマンドの出力が `dynamic` の場合、Function App は従量課金プランです。 このコマンドの出力が `ElasticPremium` の場合、Function App は Premium プランです。 その他すべての値は、App Service プランの各レベルを示します。

## <a name="storage-account-requirements"></a>ストレージ アカウントの要件

どのプランでも、Function App を使用するには、Azure BLOB、Queue、Files、Table Storage をサポートする一般的な Azure ストレージ アカウントが必要です。 これは、Azure Functions が、Azure Storage を利用してトリガーの管理や関数実行のログなどの操作を行っているためですが、ストレージ アカウントによってはキューと表はサポートされません。 そのようなアカウント (BLOB専用ストレージ アカウント (including premium storage) や、ゾーン冗長ストレージ レプリケーションを備えた汎用ストレージ アカウントなど) は、Function App を作成するときに既存の **[ストレージ アカウント]** の選択肢から除外されます。

Function App で使用されるものと同じストレージ アカウントを、アプリケーション データを格納するためのトリガーとバインドで使用することもできます。 ただし、ストレージを集中的に使用する操作の場合は、別のストレージ アカウントを使用する必要があります。  

複数の Function App では、同じストレージ アカウントを問題なく共有できます。 (これの良い例は、1 つのストレージ アカウントのように動作する Azure ストレージ エミュレーターを使用してローカル環境で複数のアプリを開発する場合です。) 

<!-- JH: Does using a Premium Storage account improve perf? -->

ストレージ アカウントの種類の詳細については、[Azure Storage サービスの概要](../storage/common/storage-introduction.md#core-storage-services)に関する記事をご覧ください。

## <a name="how-the-consumption-and-premium-plans-work"></a>従量課金プランと Premium プランのしくみ

従量課金プランと Premium プランでは、関数がトリガーされるイベントの数に基づいて Functions ホストのインスタンスを追加することで、Azure Functions インフラストラクチャによって CPU とメモリのリソースがスケーリングされます。 従量課金プランの Functions ホストの各インスタンスは、1.5 GB のメモリと 1 個の CPU に制限されています。  ホストのインスタンスは Function App全体です。つまり、Function App 内のすべての関数がインスタンス内のリソースを共有し、同時にスケールされます。 同じ従量課金プランを共有する Function App は、個別にスケーリングされます。  Premium プランでは、プランのサイズにより、そのインスタンス上のそのプランのすべてのアプリで使用可能なメモリと CPU が決まります。  

関数コード ファイルは、関数のメイン ストレージ アカウントの Azure Files 共有に格納されます。 Function App のメイン ストレージ アカウントを削除すると、関数コード ファイルは削除され、復元できません。

### <a name="runtime-scaling"></a>実行時のスケーリング

Azure Functions は "*スケール コントローラー*" と呼ばれるコンポーネントを使用して、イベント レートを監視し、スケールアウトとスケールインのどちらを実行するかを決定します。 スケール コントローラーは、トリガーの種類ごとにヒューリスティックを使用します。 たとえば、Azure Queue Storage トリガーを使用した場合、拡大縮小はキューの長さや最も古いキュー メッセージの経過時間に基づいて実施されます。

Azure Functions のスケールの単位は Function App です。 Function App がスケールアウトするときは、Azure Functions ホストの複数のインスタンスを実行するための追加リソースが割り当てられます。 反対に、コンピューティングの需要が減ると、スケール コントローラーにより、関数ホストのインスタンスが削除されます。 Function App 内で関数が何も実行されていない場合、インスタンスの数は最終的に 0 に "*スケールイン*" されます。

![イベントを監視してインスタンスを作成しているスケール コントローラー](./media/functions-scale/central-listener.png)

### <a name="cold-start"></a>コールド スタート

Function App が数分の間アイドル状態になると、プラットフォームによって、アプリが実行されるインスタンスの数が 0 にスケール ダウンされる可能性があります。 次の要求には、0 から 1 へのスケーリングの待機時間が追加されています。 この待機時間は、_コールド スタート_と呼ばれます。 Function App によって読み込まれる必要がある依存関係の数は、コールド スタート時間に影響を与える可能性があります。 コールド スタートは、応答を必ず返す必要がある HTTP トリガーなどの同期操作においては問題です。 コールド スタートが関数に影響を与えている場合は、Always On が有効になっている Premium プランまたは専用プランで実行することを検討してください。   

### <a name="understanding-scaling-behaviors"></a>スケーリングの動作について

スケーリングはさまざまな要因によって異なる可能性があり、選択したトリガーと言語に基づいて異なる方法でスケールします。 スケーリング動作には、注意が必要な複雑な作業がいくつかあります。

* 1 つの Function App がスケールアウトされるのは、最大 200 インスタンスまでのみとなります。 1 つのインスタンスで一度に複数のメッセージや要求を処理できるので、同時実行の数に上限は設定されていません。  必要な場合は[最大値を下げて](#limit-scale-out)スケーリングを制限できます。
* HTTP トリガーの場合、新しいインスタンスは最大で 1 秒間に 1 回割り当てられます。
* HTTP 以外のトリガーの場合、新しいインスタンスは最大で 30 秒ごとに 1 回割り当てられます。 [Premium プラン](#premium-plan)で実行しているときは、スケーリングが速くなります。
* Service Bus トリガーの場合、最も効率的なスケーリングを行うためには、リソースに対して "_管理_" 権限を使用します。 "_リッスン_" 権限では、スケーリングの決定を通知するためにキューの長さを使用できないため、スケーリングが正確ではありません。 Service Bus アクセス ポリシーで権限を設定する方法の詳細については、「[共有アクセス承認ポリシー](../service-bus-messaging/service-bus-sas.md#shared-access-authorization-policies)」を参照してください。
* イベント ハブのトリガーについては、リファレンス記事の[スケーリングのガイダンス](functions-bindings-event-hubs-trigger.md#scaling)を参照してください。 

### <a name="limit-scale-out"></a>スケールアウトを制限する

アプリをスケールアウトするインスタンス数を制限したい場合があります。  これは、データベースなどのダウンストリーム コンポーネントのスループットに制限があるケースで最も一般的です。  既定では、従量課金プランの関数は最大 200 インスタンスにスケールアウトでき、Premium プランの関数は最大 100 インスタンスにスケールアウトできます。  特定のアプリで最大値を下げるには、`functionAppScaleLimit` 値の指定を変更します。  `functionAppScaleLimit` は、0、(制限しない場合は) null、1 からアプリの最大値間の有効値に設定できます。

```azurecli
az resource update --resource-type Microsoft.Web/sites -g <resource_group> -n <function_app_name>/config/web --set properties.functionAppScaleLimit=<scale_limit>
```

### <a name="best-practices-and-patterns-for-scalable-apps"></a>スケーラブルなアプリのベスト プラクティスとパターン

Function App には、スケールに影響を及ぼすさまざまな側面 (ホスト構成、ランタイム フットプリント、リソースの効率など) があります。  詳細については、[パフォーマンスの考慮事項に関する記事のスケーラビリティのセクション](functions-best-practices.md#scalability-best-practices)をご覧ください。 Function App がスケールするにつれて、接続がどのように変化するかを認識する必要もあります。 詳細については、「[How to manage connections in Azure Functions](manage-connections.md)」(Azure Functions で接続を管理する方法) を参照してください。

Python と Node.js のスケールインの詳細については、「[Azure Functions の Python 開発者向けガイド - スケーリングとコンカレンシー](functions-reference-python.md#scaling-and-concurrency)」および [Azure Functions Node.js 開発者ガイド - スケーリングとコンカレンシー](functions-reference-node.md#scaling-and-concurrency)に関するページを参照してください。

### <a name="billing-model"></a>課金モデル

各プランの課金の詳細については、[Azure Functions の価格に関するページ](https://azure.microsoft.com/pricing/details/functions/)を参照してください。 使用量は Function App レベルで集計され、関数コードが実行されている期間のみカウントされます。 課金の単位は、次のとおりです。

* **ギガバイト/秒 (GB/秒) 単位でのリソース使用量**。 メモリ サイズと、Function App 内の全関数の実行時間の組み合わせとして計算されます。 
* **実行回数**。 イベント トリガーに応じて関数が実行されるたびにカウントされます。

従量課金の請求を理解する方法についての便利なクエリと情報については、[請求に関する FAQ](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ) をご覧ください。

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="hosting-plans-comparison"></a>ホスティング プランの比較

次の比較表は、Azure Functions アプリ ホスティングプランを選ぶ上で役立つすべての重要な側面を示しています。

### <a name="plan-summary"></a>プラン概要
| | |
| --- | --- |  
|**[従量課金プラン](#consumption-plan)**| 自動的にスケールし、関数が実行されている際のコンピューティング リソースに対してのみ課金されます。 従量課金プランでは、Functions ホストのインスタンスは、受信イベントの数に基づいて動的に追加および削除されます。<br/> ✔ 既定のホスティング プランです。<br/>✔ 関数の実行中にのみ課金されます。<br/>✔ 負荷が高い期間中であっても、自動的にスケールアウトされます。|  
|**[Premium プラン](#premium-plan)**|需要に応じて自動的にスケーリングを行いながら、事前ウォーミングされたワーカーを使用して、アイドル状態になっても遅延なくアプリケーションを実行したり、より強力なインスタンスで実行したり、VNET に接続したりすることができます。 次の状況では、App Service プランのすべての機能に加えて、Azure Functions Premium プランを検討してください。 <br/>✔ 関数を継続的に、またはほぼ継続的に実行したい。<br/>✔ 小規模な実行の回数が多く、実行料金が高いが、従量課金プランでの GB 秒の請求額は低い。<br/>✔ 従量課金プランで提供されるよりも多くの CPU またはメモリのオプションが必要である。<br/>✔ 従量課金プランで許可されている最大実行時間よりも長くコードを実行する必要がある。<br/>✔ 仮想ネットワーク接続など、Premium プランでのみ利用できる機能が必要である。|  
|**[専用プラン](#app-service-plan)** <sup>1</sup>|App Service プラン内で、関数を通常の App Service プラン料金で実行します。 長期の操作や、より予測的なスケーリングやコストが必要な場合に適しています。 次のような状況では、App Service プランを検討してください。<br/>✔ 既に他の App Service インスタンスを実行している、使用率の低い既存の VM がある。<br/>✔ 関数を実行するカスタム イメージを提供したい。|  
|**[ASE](#app-service-plan)** <sup>1</sup>|App Service Environment (ASE) は、App Service アプリを大規模かつ安全に実行するために完全に分離された専用の環境を提供する、Azure App Service の機能です。 ASE は、以下を必要とするアプリケーション ワークロードに最適です。 <br/>✔ 高スケール。<br/>✔ 分離およびセキュリティで保護されたネットワーク アクセス。<br/>✔ 高いメモリ使用率。|  
| **[Kubernetes](functions-kubernetes-keda.md)** | Kubernetes は、Kubernetes プラットフォーム上で実行される完全に分離された専用の環境を提供します。  Kubernetes は、以下を必要とするアプリケーション ワークロードに最適です。 <br/>✔ ハードウェア要件のカスタマイズ。<br/>✔ 分離およびセキュリティで保護されたネットワーク アクセス。<br/>✔ ハイブリッド環境またはマルチクラウド環境で実行可能。<br/>✔ 既存の Kubernetes アプリケーションやサービスと並行して実行。|  

<sup>1</sup> 各種 App Service プラン オプションに固有の制限については、[App Service プランの制限](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)に関する記事を参照してください。

### <a name="operating-systemruntime"></a>オペレーティング システム/ランタイム

| | Linux<sup>1</sup><br/>コードのみ | Windows<sup>2</sup><br/>コードのみ | Linux<sup>1、3</sup><br/>Docker コンテナー |
| --- | --- | --- | --- |
| **[従量課金プラン](#consumption-plan)** | .NET Core<br/>Node.js<br/>Java<br/>Python | .NET Core<br/>Node.js<br/>Java<br/>PowerShell Core | サポートなし  |
| **[Premium プラン](#premium-plan)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python  | 
| **[専用プラン](#app-service-plan)** <sup>4</sup> | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |
| **[ASE](#app-service-plan)** <sup>4</sup> | .NET Core<br/>Node.js<br/>Java<br/>Python |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core  |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python | 
| **[Kubernetes](functions-kubernetes-keda.md)** | 該当なし | 該当なし |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |

<sup>1</sup>Linux は、Python ランタイム スタックでサポートされている唯一のオペレーティング システムです。  
<sup>2</sup>Windows は、PowerShell ランタイム スタックでサポートされている唯一のオペレーティング システムです。   
<sup>3</sup>Linux は、Docker コンテナーでサポートされている唯一のオペレーティング システムです。
<sup>4</sup> 各種 App Service プラン オプションに固有の制限については、[App Service プランの制限](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)に関する記事を参照してください。

### <a name="scale"></a>スケール

| | スケール アウト | 最大インスタンス数 |
| --- | --- | --- |
| **[従量課金プラン](#consumption-plan)** | イベント ドリブン。 負荷が高い期間中であっても、自動的にスケールアウトされます。 Azure Functions インフラストラクチャでは、関数がトリガーされるイベントの数に基づいて Functions ホストのインスタンスを追加することで、CPU とメモリのリソースがスケーリングされます。 | 200 |
| **[Premium プラン](#premium-plan)** | イベント ドリブン。 負荷が高い期間中であっても、自動的にスケールアウトされます。 Azure Functions インフラストラクチャでは、関数がトリガーされるイベントの数に基づいて Functions ホストのインスタンスを追加することで、CPU とメモリのリソースがスケーリングされます。 |100|
| **[専用プラン](#app-service-plan)** <sup>1</sup> | 手動/自動スケール |10 - 20|
| **[ASE](#app-service-plan)** <sup>1</sup> | 手動/自動スケール |100 |
| **[Kubernetes](functions-kubernetes-keda.md)**  | [KEDA](https://keda.sh) を使用した Kubernetes クラスターのイベント ドリブン自動スケーリング。 | &nbsp;クラスター&nbsp;によって&nbsp;異なります。&nbsp;|

<sup>1</sup> 各種 App Service プラン オプションに固有の制限については、[App Service プランの制限](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)に関する記事を参照してください。

### <a name="cold-start-behavior"></a>コールド スタートの動作

|    |    | 
| -- | -- |
| **[従量課金&nbsp;プラン](#consumption-plan)** | アプリが一定期間アイドル状態になった場合、0 にスケーリングされます。つまり、一部の要求の起動時に追加の待機時間が発生する可能性があります。  従量課金プランには、コールド スタート時間を短縮するためのいくつかの最適化があります。これには、既に関数ホストと言語プロセスが実行されている、事前ウォーミングされたプレースホルダー関数からのプルが含まれます。 |
| **[Premium プラン](#premium-plan)** | コールド スタートを回避するためにインスタンスを常にウォーム状態に維持します。 |
| **[専用プラン](#app-service-plan)** <sup>1</sup> | 専用プランで実行していると、Functions ホストを継続的に実行できます。つまり、コールド スタートは問題にならないということです。 |
| **[ASE](#app-service-plan)** <sup>1</sup> | 専用プランで実行していると、Functions ホストを継続的に実行できます。つまり、コールド スタートは問題にならないということです。 |
| **[Kubernetes](functions-kubernetes-keda.md)**  | KEDA の構成に依存します。 アプリは、常に実行可能でコールド スタートにならないよう設定したり、新しいイベントの際にコールド スタートになる 0 にスケーリングされるよう設定することもできます。 

<sup>1</sup> 各種 App Service プラン オプションに固有の制限については、[App Service プランの制限](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)に関する記事を参照してください。

### <a name="service-limits"></a>サービスの制限

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]

### <a name="networking-features"></a>ネットワーク機能

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

### <a name="billing"></a>課金

| | | 
| --- | --- |
| **[従量課金プラン](#consumption-plan)** | 関数が実行された時間に対してだけ支払います。 課金は、実行数、実行時間、およびメモリの使用量に基づいて行われ、 |
| **[Premium プラン](#premium-plan)** | Premium プランは、必要なインスタンスや事前ウォーミングされたインスタンスで使用されたコア秒数とメモリに基づいています。 プランごとに少なくとも 1 つのインスタンスが常にウォーム状態である必要があります。 このプランでは、より予測可能な価格が提供されます。 |
| **[専用プラン](#app-service-plan)** <sup>1</sup> | App Service プランの Function App に対する支払いは、Web アプリなどの他の App Service リソースの場合と同じです。|
| **[ASE](#app-service-plan)** <sup>1</sup> | インフラストラクチャの支払いを行うための ASE には一定の月額料金があり、ASE のサイズが変化しても料金は変わりません。 それに加えて、App Service プランの vCPU あたりのコストがあります。 ASE でホストされているすべてのアプリは、分離された価格 SKU に含まれます。 |
| **[Kubernetes](functions-kubernetes-keda.md)**| お支払いは Kubernetes クラスターのコストだけです。関数に対する追加の課金はありません。 Function App は、通常のアプリと同じように、クラスターのアプリケーションのワークロードとして実行されます。 |

<sup>1</sup> 各種 App Service プラン オプションに固有の制限については、[App Service プランの制限](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

+ [クイック スタート: Visual Studio Code を使用して Azure Functions プロジェクトを作成する](functions-create-first-function-vs-code.md)
+ [Azure Functions のデプロイ テクノロジ](functions-deployment-technologies.md) 
+ [Azure Functions 開発者ガイド](functions-reference.md)
