---
title: App Service プランの概要 - Azure | Microsoft Docs
description: Azure App Service の App Service プランのしくみと、それが管理機能にもたらすメリットについて説明します。
keywords: App Service, Azure App Service, スケール, スケーラブル, スケーラビリティ, App Service プラン, App Service コスト
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: ab04d1288eb3a851774128b8aaaae03868c2ffa7
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53730621"
---
# <a name="azure-app-service-plan-overview"></a>Azure App Service プランの概要

App Service では、アプリは "_App Service プラン_" で実行されます。 App Service プランでは、Web アプリを実行するための一連のコンピューティング リソースを定義します。 これらのコンピューティング リソースは従来の Web ホスティングの ["_サーバー ファーム_"](https://wikipedia.org/wiki/Server_farm) に似ています。 1 つまたは複数のアプリを同じコンピューティング リソース (または、同じ App Service プラン) で実行するように構成することができます。

特定のリージョン (西ヨーロッパなど) の App Service プランを作成する場合、一連のコンピューティング リソースはそのリージョンのそのプランに対して作成されます。 この App Service プランに入れたアプリは、App Service プランで定義されたとおりにこれらのコンピューティング リソースで実行されます。 各 App Service プランは以下を定義します。

- リージョン (米国西部、米国東部など)
- VM インスタンスの数
- VM インスタンスのサイズ (小、中、大)
- 価格レベル (Free、Shared、Basic、Standard、Premium、PremiumV2、Isolated、従量課金)

App Service プランの "_価格レベル_" は、取得する App Service の機能とプランの価格を決定します。 価格レベルにはいくつかのカテゴリがあります。

- **共有コンピューティング**: 2 つの基本レベルである **Free** と **Shared** は、他のお客様のアプリを含む他の App Service アプリと同じ Azure VM 上でアプリを実行します。 これらのレベルは共有リソースで実行される各アプリに CPU クォータを割り当て、リソースはスケールアウトできません。
- **専用のコンピューティング**: **Basic**、**Standard**、**Premium**、**PremiumV2** のレベルはアプリを専用の Azure VM 上で実行します。 同じ App Service プラン内のアプリのみが同じコンピューティング リソースを共有します。 レベルが高いほど、スケールアウトに使用できる VM インスタンスが多くなります。
- **Isolated**: このレベルは、専用の Azure VM を専用の Azure 仮想ネットワーク上で実行し、コンピューティングの分離の上のネットワークの分離をアプリに提供します。 最大のスケールアウト機能を提供します。
- **従量課金**: このレベルは[関数アプリ](../azure-functions/functions-overview.md)にのみ使用できます。 ワークロードに応じて関数を動的にスケーリングします。 詳しくは、「[Azure Functions のホスティング プラン](../azure-functions/functions-scale.md)」をご覧ください。

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

各レベルには、App Service 機能の特定のサブセットも用意されています。 これらの機能には、カスタム ドメインと SSL 証明書、自動スケーリング、デプロイ スロット、バックアップ、Traffic Manager の統合などが含まれます。 レベルが高いほど、多くの機能を使用できます。 各価格レベルでサポートされる機能については、[App Service プランの詳細](https://azure.microsoft.com/pricing/details/app-service/plans/)に関するページをご覧ください。

<a name="new-pricing-tier-premiumv2"></a>

> [!NOTE]
> 新しい **PremiumV2** 価格レベル は、**標準** レベルと比較して高速なプロセッサ、SSD ストレージ、メモリコア比が 2 倍の [Dv2 シリーズ VM](../virtual-machines/windows/sizes-general.md#dv2-series) を提供します。 **PremiumV2** は、インスタンス数が多いためサポートできるスケールも大きく、その一方で Standard プランの高度な機能もすべて提供します。 既存の **Premium** レベルで使用可能なすべての機能が **PremiumV2** に含まれています。
>
> 他の特化したレベルと同様に、次の 3 つの VM サイズをこのレベルに利用できます。
>
> - 小 (1 CPU コア、3.5 GiB のメモリ) 
> - 中 (2 CPU コア、7 GiB のメモリ) 
> - 大 (4 CPU コア、14 GiB のメモリ)  
>
> **PremiumV2** の価格については、「[App Service の価格](https://azure.microsoft.com/pricing/details/app-service/)」をご覧ください。
>
> 新しい価格レベル **PremiumV2** を使用するには、「[Configure PremiumV2 tier for App Service (App Service 向け PremiumV2 レベルの構成)](app-service-configure-premium-tier.md)」をご覧ください。

## <a name="how-does-my-app-run-and-scale"></a>アプリの実行とスケールの方法

**Free** と **Shared** のレベルでは、アプリは共有 VM インスタンス上の CPU 時間 (分) を受け取り、スケールアウトできません。他のレベルでは、アプリは次のように実行およびスケーリングされます。

App Service でアプリを作成すると、App Service プランに入れられます。 アプリを実行すると、App Service プランで構成されているすべての VM インスタンスで実行されます。 複数のアプリが同じ App Service プランにある場合、これらはすべて同じ VM インスタンスを共有します。 アプリのデプロイ スロットが複数ある場合は、すべてのデプロイ スロットも同じ VM インスタンスで実行されます。 診断ログを有効にするか、バックアップを実行するか、Web ジョブを実行すると、これらもこれらの VM インスタンス上の CPU サイクルとメモリを使用します。

このように、App Service プランは App Service アプリのスケール ユニットです。 プランが 5 つの VM インスタンスを実行するように構成されている場合、プラン内のすべてのアプリが 5 つすべてのインスタンスで実行されます。 プランが自動スケール用に構成されている場合は、プラン内のすべてのアプリが自動スケール設定に基づいて一緒にスケールアウトされます。

アプリのスケールアウトについては、「[手動または自動によるインスタンス数のスケール変更](../monitoring-and-diagnostics/insights-how-to-scale.md)」をご覧ください。

<a name="cost"></a>

## <a name="how-much-does-my-app-service-plan-cost"></a>App Service プランのコスト

このセクションでは、App Service アプリの課金方法について説明します。 リージョン固有の価格情報について詳しくは、「[App Service の価格](https://azure.microsoft.com/pricing/details/app-service/)」をご覧ください。

**Free** レベルを除き、App Service プランは使用するコンピューティング リソースを時間単位で課金します。

- **Shared** レベルでは、それぞれのアプリが CPU の分単位のクォータを受け取るので、"_各アプリ_" は CPU クォータの時間単位で課金されます。
- 専用コンピューティング レベル (**Basic**、**Standard**、**Premium**、**PremiumV2**) では、App Service プランはアプリがスケールされる VM インスタンスの数を定義するので、App Service プランの "_各 VM インスタンス_" には時間単位の料金があります。 これらの VM インスタンスには、実行されているアプリの数にかかわらず同じ料金が課金されます。 予期しない課金を避けるには、[App Service プランのクリーンアップ](app-service-plan-manage.md#delete)に関するページをご覧ください。
- **Isolated** レベルでは、App Service 環境は、アプリを実行する分離された worker の数を定義し、"_各 worker_" は時間単位で課金されます。 さらに、App Service 環境自体の実行に時間単位の基本料金があります。 
- (Azure Functions の場合のみ) **従量課金**レベルは、VM インスタンスを動的に割り当てて関数アプリのワークロードを処理し、Azure によって 1 秒ごとに動的に課金されます。 詳細については、[Azure Functions の価格](https://azure.microsoft.com/pricing/details/functions/)に関するページを参照してください。

使用可能な App Service 機能 (カスタム ドメインの構成、SSL 証明書、デプロイ スロット、バックアップなど) の使用には課金されません。 ただし、次のような例外があります。

- App Service ドメイン - Azure での購入時と毎年の更新時に支払います。
- App Service 証明書 - Azure での購入時と毎年の更新時に支払います。
- IP ベースの SSL 接続 - IP ベースの SSL 接続ごとに時間単位の課金がありますが、**Standard** 以上の一部のレベルでは、1 つの IP ベースの SSL 接続が無料で提供されます。 SNI ベースの SSL 接続は無料です。

> [!NOTE]
> App Service を別の Azure サービスと統合する場合は、これらの他のサービスの料金を考慮する必要があります。 たとえば、Azure Traffic Manager を使用してアプリを地理的にスケーリングする場合、Azure Traffic Manager も使用状況に基づいて課金されます。 Azure でクロスサービス コストを見積もるには、「[料金計算ツール](https://azure.microsoft.com/pricing/calculator/)」をご覧ください。 
>
>

## <a name="what-if-my-app-needs-more-capabilities-or-features"></a>アプリに能力や機能の追加が必要になった場合

App Service プランは、いつでもスケールアップまたはスケールダウンできます。 プランの価格レベルを変更するだけなのでシンプルです。 最初に低い価格レベルを選び、後で App Service 機能がさらに必要になったときにスケールアップできます。

たとえば、**Free** App Service プランで Web アプリのテストを無料で開始できます。 [カスタム DNS 名](app-service-web-tutorial-custom-domain.md)を Web アプリに追加する場合は、プランを **Shared** レベルにスケーリングします。 その後、[カスタム SSL 証明書](app-service-web-tutorial-custom-ssl.md)を追加するときに、プランを **Basic** レベルにスケーリングします。 [ステージング環境](deploy-staging-slots.md)が必要な場合は、**Standard** レベルにスケールアップします。 さらに多くのコア、メモリ、ストレージが必要になった場合は、同じレベルのより大きな VM サイズにスケールアップします。

逆も同じように動作します。 上位レベルの能力や機能が不要になったと感じる場合は、下位レベルにスケールダウンして、コストを節約できます。

App Service プランのスケールアップについては、「[Azure でのアプリのスケールアップ](web-sites-scale.md)」をご覧ください。

アプリが他のアプリと同じ App Service プランにある場合は、コンピューティング リソースを分離すると、アプリのパフォーマンスが向上します。 これを行うには、アプリを別の App Service プランに移動します。 詳しくは、「[Move an app to another App Service plan (アプリを別の App Service プランに移動する)](app-service-plan-manage.md#move)」をご覧ください。

## <a name="should-i-put-an-app-in-a-new-plan-or-an-existing-plan"></a>アプリを新しいプランと既存のプランのどちらに入れる必要があるか

App Service プランが割り当てるコンピューティング リソースの使用料を支払うので (「[App Service プランのコスト](#cost)」をご覧ください)、1 つの App Service プランに複数のアプリを配置するとコストを節約できる可能性があります。 プランに負荷を処理するための十分なリソースがある限り、既存のプランにアプリを追加し続けることができます。 ただし、同じ App Service プラン内のアプリはすべて同じコンピューティング リソースを共有することにご注意ください。 新しいアプリが必要なリソースを持てるかどうかを判断するには、既存の App Service プランの容量と新しいアプリの予想される負荷について理解する必要があります。 App Service プランのオーバーロードは、新規および既存のアプリのダウンタイムを引き起こす可能性があります。

次の場合にはアプリを新しい App Service プランに分離してください。

- アプリが多くのリソースを消費している。
- 既存のプランの他のアプリから独立してアプリをスケーリングする必要がある。
- アプリに別の地理的リージョン内のリソースが必要である。

こうすると、アプリの新しいリソース セットを割り当てることができるため、アプリをより効果的に制御できます。

## <a name="manage-an-app-service-plan"></a>App Service プランの管理

> [!div class="nextstepaction"]
> [App Service プランの管理](app-service-plan-manage.md)
