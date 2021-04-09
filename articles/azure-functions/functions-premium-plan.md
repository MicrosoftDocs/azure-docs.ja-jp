---
title: Azure Functions の Premium プラン
description: Azure Functions の Premium プランの詳細と構成オプション (VNet、コールド スタートなし、無制限の実行期間)。
author: jeffhollan
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: jehollan
ms.custom:
- references_regions
- fasttrack-edit
- devx-track-azurecli
ms.openlocfilehash: 3061329ad9dcb368dab586acc2146e6fb4e23028
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101708715"
---
# <a name="azure-functions-premium-plan"></a>Azure Functions の Premium プラン

Azure Functions の Premium プラン (Elastic Premium プランとも呼ばれます) は、 Function App のホスティング オプションです。 その他のホスティング プラン オプションについては、[ホスティング プランの記事](functions-scale.md)を参照してください。

Premium プランのホスティングでは、関数に次の利点があります。

* インスタンスを常にウォーム状態に維持することでコールド スタートを回避。
* 仮想ネットワーク接続。
* 無制限の実行期間 (60 分保証)。
* Premium インスタンス サイズ (1 コア、2 コア、4 コアのインスタンス)。
* 従量課金プランと比較して、予測可能な料金。
* 複数の Function App を含むプランでの高密度アプリ割り当て。

Premium プランを使用すると、[従量課金プラン](consumption-plan.md)と同じように、Azure Functions ホストのインスタンスが、受信イベントの数に基づいて追加および削除されます。 複数の Function App を同じ Premium プランにデプロイできます。プランでは、コンピューティング インスタンス サイズ、基本プラン サイズ、および最大プラン サイズを構成できます。 

## <a name="billing"></a>課金

Premium プランの課金は、インスタンス全体にわたって割り当てられたコア秒数とメモリに基づいています。 この課金は、実行および消費されたメモリに応じて課金される従量課金プランとは異なります。 Premium プランの場合、実行料金は発生しません。 1 つのプランにつき、少なくとも 1 つのインスタンスが常に割り当てられている必要があります。 この課金の結果として、関数がアクティブかアイドル状態かに関係なく、アクティブなプラン別の月間コストが最も少なくなります。 Premium プランのすべての Function App で割り当てられたインスタンスが共有されることにご注意ください。 詳しくは、「[Azure Functions の価格](https://azure.microsoft.com/pricing/details/functions/)」ページをご覧ください。

## <a name="create-a-premium-plan"></a>Premium プランを作成する

Azure portal で Function App を作成する場合は、従量課金プランが既定になります。 Premium プランで実行される Function App を作成するには、いずれかの "_エラスティック Premium_" SKU を使用して App Service プランを明示的に作成する必要があります。 作成した Function App は、このプランでホストされます。 Azure portal を使用すると、Premium プランと Function App の両方を同時に簡単に作成できます。 同じ Premium プランで複数の Function App を実行できますが、どちらも同じオペレーティング システム (Windows または Linux) で実行する必要があります。 

次の記事では、プログラムまたは Azure portal のいずれかを使用して、Premium プランで Function App を作成する方法について説明しています。

+ [Azure Portal](create-premium-plan-function-app-portal.md)
+ [Azure CLI](scripts/functions-cli-create-premium-plan.md)
+ [Azure Resource Manager テンプレート](functions-infrastructure-as-code.md#deploy-on-premium-plan)

## <a name="eliminate-cold-starts"></a>コールド スタートを排除する

従量課金プランでイベントも実行も発生しないときは、ゼロ インスタンスまでアプリをスケールインできます。 新しいイベントが発生したら、アプリの実行先となる新しいインスタンスを専用化する必要があります。 アプリによっては、新しいインスタンスの専用化に時間がかかる場合があります。 最初の呼び出しでこのように待機時間が長くなることを、アプリの "_コールド スタート_" と呼ぶことがよくあります。

Premium プランには、関数のコールド スタートを効果的に排除するために連携する、"_常時使用可能なインスタンス_" と "_事前ウォーミングされたインスタンス_" という 2つの機能が用意されています。 

### <a name="always-ready-instances"></a>常時使用可能なインスタンス

Premium プランでは、指定された数のインスタンスでアプリを常時使用可能にしておくことができます。 常時使用可能なインスタンスの最大数は 20 です。 イベントがアプリのトリガーを開始すると、そのイベントは最初に常時使用可能なインスタンスにルーティングされます。 関数がアクティブになると、追加のインスタンスがバッファーとしてウォーミングされます。 このバッファーは、スケール中に要求された新しいインスタンスのコールド スタートを防止します。 これらのバッファーに格納されたインスタンスは、[事前ウォーミングされたインスタンス](#pre-warmed-instances)と呼ばれます。 常時使用可能なインスタンスと事前ウォーミングされたバッファーを組み合わせることで、アプリは効果的にコールド スタートを排除できます。

> [!NOTE]
> すべての Premium プランには、常に 1 つ以上のアクティブな (課金された) インスタンスがあります。

# <a name="portal"></a>[ポータル](#tab/portal)

Azure portal で常時使用可能なインスタンスの数を構成するには、選択した **Function App** から、 **[プラットフォーム機能]** タブへ移動して、 **[スケールアウト]** オプションを選択します。  Function App の編集ウィンドウでは、常時使用可能なインスタンスはそのアプリに固有のものです。

![エラスティック スケールの設定](./media/functions-premium-plan/scale-out.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

アプリの常時使用可能なインスタンスは、Azure CLI を使用して構成することもできます。

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.minimumElasticInstanceCount=<desired_always_ready_count> --resource-type Microsoft.Web/sites
```
---

### <a name="pre-warmed-instances"></a>事前ウォーミングされたインスタンス

事前ウォーミングされたインスタンスは、スケールおよびアクティブ化イベント中にバッファーとしてウォーミングされるインスタンスです。 事前ウォーミングされたインスタンスは、スケールアウトの上限に達するまでバッファーに格納され続けます。 事前ウォーミングされたインスタンスの既定の数は 1 であり、ほとんどのシナリオではこの値を 1 のままにしておく必要があります。

アプリが長時間のウォームアップ (カスタム コンテナー イメージなど) を行うとき、このバッファーを増やす必要がある場合があります。 事前ウォーミングされたインスタンスは、すべてのアクティブなインスタンスが十分に使用された後にのみ、アクティブになります。

常時使用可能なインスタンスと事前ウォーミングされたインスタンスが連携して動作する例について考えてみましょう。 Premium  Function App に、5 つの常時使用可能なインスタンスが構成されており、事前ウォーミングされたインスタンスが既定で 1 つあります。 アプリがアイドル状態であり、トリガーしているイベントがない場合、このアプリは 5 つのインスタンスでプロビジョニングされ、実行されます。 現時点で、常時使用可能なインスタンスが使用されておらず、事前ウォーミングされたインスタンスが割り当てられていないため、事前ウォーミングされたインスタンスに対して課金されることはありません。

最初のトリガーが開始されるとすぐに、5 つの常時使用可能なインスタンスがアクティブになり、事前ウォーミングされたインスタンスが割り当てられます。 アプリは 6 つのプロビジョニングされたインスタンスを使用して実行されています。つまり、5 つの現在アクティブな常時使用可能なインスタンスと、6 番目の事前ウォーミングされた非アクティブなバッファーです。 実行率が増加し続けた場合は、最終的に 5 つのアクティブなインスタンスが使用されます。 5 つのインスタンスを超えて拡張することがプラットフォームによって決定された場合は、事前ウォーミングされたインスタンスに拡張されます。 その場合、6 つのアクティブなインスタンスが存在することになり、7 番目のインスタンスがすぐにプロビジョニングされ、事前ウォーミングされたバッファーに格納されます。 この一連のスケーリングと事前ウォーミングは、アプリの最大インスタンス数に達するまで続行されます。 最大値を超えてインスタンスが事前ウォーミングまたはアクティブ化されることはありません。

アプリの事前ウォーミングされたインスタンスの数は、Azure CLI を使用して変更できます。

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="maximum-function-app-instances"></a> Function App のインスタンスの最大数

[プランの最大インスタンス数](#plan-and-sku-settings)に加えて、アプリごとの最大数を構成できます。 アプリの最大数は、[アプリのスケール制限](./event-driven-scaling.md#limit-scale-out)を使用して構成できます。

## <a name="private-network-connectivity"></a>プライベート ネットワーク接続

Premium プランにデプロイされた Function App では、[Web アプリ向けの VNet 統合](../app-service/web-sites-integrate-with-vnet.md)を利用できます。 構成すると、アプリを VNet 内のリソースと通信させる、またはサービス エンドポイントを介してセキュリティで保護することができます。 受信トラフィックを制限するための IP 制限もアプリで利用できます。

Premium プランで Function App にサブネットを割り当てるときは、個々の潜在的インスタンスのための十分な IP アドレスがあるサブネットが必要です。 使用可能なアドレスが 100 以上の IP ブロックが必要です。

詳細については、[お使いの Function App と VNet の統合](functions-create-vnet.md)に関するページを参照してください。

## <a name="rapid-elastic-scale"></a>高速エラスティック スケール

従量課金プランと同じ高速スケーリング ロジックを使用して、追加のコンピューティング インスタンスが自動的にアプリのために追加されます。 同じ Azure App Service のアプリは、個々のアプリのニーズに基づき、互いに依存せずにスケーリングします。 ただし、同じ Azure App Service の Function App では、可能であれば、コストを下げる目的で VM リソースが共有されます。 VM に関連付けられているアプリの数は、各アプリの占有領域と VM のサイズによって変わります。

スケーリングのしくみの詳細については、「[Azure Functions でのイベント ドリブン スケーリング](event-driven-scaling.md)」を参照してください。

## <a name="longer-run-duration"></a>実行継続時間の延長

Azure Functions の従量課金プランでは、1 回の実行が 10 分までに制限されています。 Premium プランでは、実行中の暴走を防ぐために、実行継続時間の既定値が 30 分になっています。 ただし、[host.json 構成を変更](./functions-host-json.md#functiontimeout)して、Premium プランのアプリで継続期間を無制限にすることができます。 無制限の期間に設定すると、 Function App は少なくとも 60 分間実行されることが保証されます。 

## <a name="plan-and-sku-settings"></a>プランと SKU の設定

プランを作成するときは、2 つのプラン サイズ設定があります。インスタンスの最小数 (またはプラン サイズ) と最大バースト制限です。

常時使用可能なインスタンス数を超えるインスタンスがアプリに必要な場合、インスタンスの数が最大バースト制限に達するまでアプリのスケール アウトを続けることができます。 プラン サイズを超えたインスタンスに対する課金は、それらが実行されていて、秒あたりの単位で割り当てられている間のみ発生します。 定義された最大制限までのアプリのスケールアウトに関しては、プラットフォームのベスト エフォートでの提供となります。

プラン サイズまたは最大値は Azure portal で構成できます。プランまたはそのプランにデプロイされている Function App の **[スケール アウト]** オプションを選択します ( **[プラットフォーム機能]** の下にあります)。

Azure CLI から最大バースト制限を増やすこともできます。

```azurecli-interactive
az functionapp plan update -g <resource_group> -n <premium_plan_name> --max-burst <desired_max_burst>
```

各プランの最小値は、1 つ以上のインスタンスになります。 実際のインスタンスの最小数は、プラン内のアプリによって要求された常時使用可能なインスタンス数に基づいて自動構成されます。 たとえば、アプリ A が 5 つの常時使用可能なインスタンスを要求し、同じプラン内でアプリ B が 2 つの常時使用可能なインスタンスを要求した場合、最小プラン サイズは 5 として計算されます。 アプリ A は 5 つのすべてで実行され、アプリ B は 2 つでのみ実行されます。

> [!IMPORTANT]
> 関数が実行されているかどうかにかかわらず、最小インスタンスで割り当てられているインスタンスごとに課金されます。

ほとんどの場合、この自動計算される最小値で十分です。 ただし、最小値を超えるスケーリングはベスト エフォートで実行されます。 追加のインスタンスを使用できない場合は、まれですが、特定の時間にスケールアウトが遅延する可能性があります。 自動計算された最小値よりも大きな最小値を設定することにより、スケールアウトの前にインスタンスを予約します。

プランの計算された最小値を増やすには、Azure CLI を使用します。

```azurecli-interactive
az functionapp plan update -g <resource_group> -n <premium_plan_name> --min-instances <desired_min_instances>
```

### <a name="available-instance-skus"></a>利用可能インスタンス SKU

プランを作成またはスケーリングするときは、3 つのインスタンス サイズから選択できます。 プロビジョニングされたコアとメモリの合計数に対して、各インスタンスが割り当てられている秒単位で課金されます。 アプリは必要に応じて自動的に複数のインスタンスにスケール アウトできます。

|SKU|コア|メモリ|ストレージ|
|--|--|--|--|
|EP1|1|3.5 GB|250 GB|
|EP2|2|7 GB|250 GB|
|EP3|4|14 GB|250 GB|

### <a name="memory-usage-considerations"></a>メモリ使用量に関する考慮事項

メモリの多いコンピューターでお使いの Function App を実行しても、利用可能なすべてのメモリを使用できるとは限りません。

たとえば、JavaScript  Function App には、Node.js の既定のメモリ上限の制限があります。 この固定のメモリ制限を増やすには、値に `--max-old-space-size=<max memory in MB>` を使用して `languageWorkers:node:arguments` のアプリ設定を追加します。

4 GB を超えるメモリの場合は、プラットフォームのビット設定が `64 Bit` に設定されていることを [[全般設定]](../app-service/configure-common.md#configure-general-settings) で確認します。

## <a name="region-max-scale-out"></a>リージョン最大スケール アウト

以下は、1 つのプランでサポートされている最大スケールアウト値をリージョンと OS の構成ごとにまとめたものです。 増加を依頼するには、サポート チケットを開くことができます。

Functions のリージョン別の可用性の完全な情報については、[Azure の Web サイト](https://azure.microsoft.com/global-infrastructure/services/?products=functions)を参照してください。

|リージョン| Windows | Linux |
|--| -- | -- |
|オーストラリア中部| 100 | 利用不可 |
|オーストラリア中部 2| 100 | 利用不可 |
|オーストラリア東部| 100 | 20 |
|オーストラリア南東部 | 100 | 20 |
|ブラジル南部| 100 | 20 |
|カナダ中部| 100 | 20 |
|米国中部| 100 | 20 |
|中国東部 2| 100 | 20 |
|中国北部 2| 100 | 20 |
|東アジア| 100 | 20 |
|米国東部 | 100 | 20 |
|米国東部 2| 100 | 20 |
|フランス中部| 100 | 20 |
|ドイツ中西部| 100 | 利用不可 |
|東日本| 100 | 20 |
|西日本| 100 | 20 |
|韓国中部| 100 | 20 |
|韓国南部| 利用不可 | 20 |
|米国中北部| 100 | 20 |
|北ヨーロッパ| 100 | 20 |
|ノルウェー東部| 100 | 20 |
|米国中南部| 100 | 20 |
|インド南部 | 100 | 利用不可 |
|東南アジア| 100 | 20 |
|スイス北部| 100 | 利用不可 |
|スイス西部| 100 | 利用不可 |
|英国南部| 100 | 20 |
|英国西部| 100 | 20 |
|USGov アリゾナ| 100 | 20 |
|USGov バージニア州| 100 | 20 |
|USNat 東部| 100 | 利用不可 |
|USNat 西部| 100 | 利用不可 |
|西ヨーロッパ| 100 | 20 |
|インド西部| 100 | 20 |
|米国中西部| 100 | 20 |
|米国西部| 100 | 20 |
|米国西部 2| 100 | 20 |

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Functions のホスティングのオプションを理解する](functions-scale.md)