---
title: Azure Functions の専用ホスティング
description: 専用の App Service ホスティング プランで Azure Functions を実行する利点について説明します。
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: a9f976eda4a551c302ba7df92fbdbbf7a4fce1d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101704567"
---
# <a name="dedicated-hosting-plans-for-azure-functions"></a>Azure Functions の専用ホスティング プラン

この記事は、App Service プラン (App Service Environment (ASE) を含む) での関数アプリのホスティングに関するものです。 その他のホスティング オプションについては、[ホスティング プランの記事](functions-scale.md)を参照してください。

App Service プランでは、アプリを実行するための一連のコンピューティング リソースを定義します。 これらのコンピューティング リソースは、従来のホスティングでの ["_サーバー ファーム_"](https://wikipedia.org/wiki/Server_farm) に似ています。 1 つ以上の関数アプリを、他の App Service アプリ (Web アプリなど) と同じコンピューティング リソース (App Service プラン) 上で実行されるように構成できます。 これらのプランには、Basic、Standard、Premium、Isolated SKU が含まれます。 App Service プランの仕組みの詳細については、「[Azure App Service プランの詳細な概要](../app-service/overview-hosting-plans.md)」を参照してください。

次のような状況では、App Service プランを検討してください。

* 既に他の App Service インスタンスを実行している、使用率の低い既存の VM がある。
* 関数を実行するカスタム イメージを提供したい。

## <a name="billing"></a>課金

App Service プランの関数アプリに対する支払いは、他の App Service リソースの場合と同じです。 これは、使用量ベースのコスト コンポーネントを含む、Azure Functions の[従量課金プラン](consumption-plan.md)または [Premium プラン](functions-premium-plan.md) ホスティングとは異なります。 このプランで実行される関数アプリまたは Web アプリの数には関係なく、プランに対してのみ課金されます。 詳細については、[App Service の料金ページ](https://azure.microsoft.com/pricing/details/app-service/windows/)を参照してください。 

## <a name="always-on"></a><a name="always-on"></a> 常にオン

App Service プランを実行する場合、関数アプリが正常に実行されるように、**常時接続** 設定を有効にする必要があります。 App Service プランでは、関数のランタイムは非アクティブな状態が数分続くとアイドル状態となるため、関数を "起こす" ことができるのは HTTP トリガーのみとなります。 **[常時接続]** 設定は、App Service プランでのみ使用できます。 従量課金プランでは、関数アプリはプラットフォームにより自動的にアクティブ化されます。

常時接続が有効になっている場合でも、個々の関数の実行タイムアウトは [host.json](functions-host-json.md#functiontimeout) プロジェクト ファイルの `functionTimeout` 設定によって制御できます。

## <a name="scaling"></a>Scaling

App Service プランを使用し、VM インスタンスを追加して、手動でスケールアウトできます。 自動スケーリングを有効にすることもできます。ただし、自動スケーリングは、Premium プランのエラスティック スケールより遅くなります。 詳細については、「[手動または自動によるインスタンス数のスケール変更](../azure-monitor/autoscale/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json)」を参照してください。 別の App Service プランを選択してスケールアップすることもできます。 詳細については、 [Azure でのアプリのスケールアップ](../app-service/manage-scale-up.md) に関するページを参照してください。 

> [!NOTE] 
> App Service プランで JavaScript (Node.js) 関数を実行している場合は、vCPU の数が少ないプランを選択する必要があります。 詳細については、[シングルコア App Service プランの選択](functions-reference-node.md#choose-single-vcpu-app-service-plans)に関するページをご覧ください。 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

## <a name="app-service-environments"></a>App Service Environment

App Service Environment (ASE) で実行すると、関数を完全に分離し、App Service プランより多い数のインスタンスを利用できます。 開始するには、「[App Service Environment の概要](../app-service/environment/intro.md)」を参照してください。

関数アプリを仮想ネットワークで実行するだけであれば、[Premium プラン](functions-premium-plan.md)を使用してそれを行うことができます。 詳細については、[Azure Functions のプライベート サイト アクセスの設定](functions-create-private-site-access.md)に関するページを参照してください。 

## <a name="next-steps"></a>次のステップ

+ [Azure Functions のホスティング オプション](functions-scale.md)
+ [Azure App Service プランの概要](../app-service/overview-hosting-plans.md)
