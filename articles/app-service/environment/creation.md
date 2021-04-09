---
title: App Service Environment を作成する
description: App Service Environment の作成方法について説明します。
author: ccompy
ms.assetid: 7690d846-8da3-4692-8647-0bf5adfd862a
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 021c85037ea105ff7d8ff642e3a9f28ed3ebe991
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594111"
---
# <a name="create-an-app-service-environment"></a>App Service Environment を作成する

> [!NOTE]
> この記事では、App Service Environment v3 (プレビュー) について説明します。
> 

[App Service Environment (ASE)][Intro] は、ご使用の Azure 仮想ネットワーク (VNet) に導入される App Service のシングル テナント デプロイです。  ASEv3 でサポートされているのは、Vnet 内のプライベート アドレスでのアプリの公開のみです。 ASEv3 がプレビュー期間中に作成されると、次のリソースがサブスクリプションに追加されます。

- App Service Environment
- プライベート エンドポイント

ASE のデプロイには 2 つのサブネットを使用する必要があります。  一方のサブネットでプライベート エンドポイントを保持します。  このサブネットは、VM などの他の用途に使用できます。  もう一方のサブネットは、ASE からの送信呼び出しに使用されます。  このサブネットは、ASE 以外には使用できません。 

## <a name="before-you-create-your-ase"></a>ASE を作成する前に

ASE 作成後は、次の変更はできません。

- 場所
- サブスクリプション
- Resource group
- 使用する Azure 仮想ネットワーク (VNet)
- 使用するサブネット
- サブネットのサイズ
- ASE の名前

送信サブネットは、ASE をスケーリングする最大サイズを保持するのに十分な大きさである必要があります。 作成後に変更することはできないため、最大スケールのニーズをサポートするために十分な大きさのサブネットを選択します。 推奨されるサイズは、256 のアドレスを持つ /24 です。

ASE を作成した後、それにアプリを追加できます。 ASEv3 内に App Service プランがない場合、ASE に I1v2 App Service プランの 1 つのインスタンスがある場合のように課金されます。  

ASEv3 は、一部のリージョンでのみ提供されます。 プレビューから GA に進むにつれて、より多くのリージョンが追加されます。 

## <a name="creating-an-ase-in-the-portal"></a>ポータルでの ASE の作成

1. ASEv3 を作成するには、マーケットプレースで **App Service Environment (プレビュー)** を検索します。  
2. 基本:サブスクリプションを選択し、リソース グループを選択または作成して、ASE の名前を入力します。  ASE 名は、ASE のドメインのサフィックスにも使用されます。  ASE 名が *contoso* の場合、ドメインのサフィックスは *contoso.appserviceenvironment.net* になります。  ASE のデプロイ先である Vnet で使用する Azure DNS プライベート ゾーンに、この名前が自動的に設定されます。 

    ![App Service Environment の作成の [基本] タブ](./media/creation/creation-basics.png)

3. ホスティング: [OS Preference]\(OS 基本設定\)、[Host Group deployment]\(ホスト グループ デプロイ\) を選択します。 OS 設定は、この ASE でアプリに最初に使用するオペレーティング システムを示します。 ASE 作成後、他の OS のアプリを追加できます。 ホスト グループ デプロイは、専用ハードウェアを選択するために使用されます。 ASEv3 を使用すると、[有効] を選択し、専用ハードウェアに配置できます。 ASE 作成時に専用ホスト全体に対して課金された後、App Service プラン インスタンスの料金が引き下げられます。 

    ![App Service Environment のホスティングの選択項目](./media/creation/creation-hosting.png)

4. ネットワーク: 仮想ネットワークを選択または作成し、受信サブネットを選択または作成して、送信サブネットを選択または作成します。 送信に使用するサブネットはすべて空で、Microsoft.Web/hostingEnvironments に委任されている必要があります。 ポータルを使用してサブネットを作成する場合は、サブネットの委任が自動的に行われます。

    ![App Service Environment のネットワークの選択項目](./media/creation/creation-networking.png)

5. 確認と作成: 構成が正しいことを確認し、[作成] を選択します。 ASE の作成には 1 時間ほどかかります。 

    ![App Service Environment の確認と作成](./media/creation/creation-review.png)

ASE の作成が完了したら、アプリを作成するときにそれを場所として選択できます。 新しい ASE でのアプリの作成について詳しくは、「[App Service Environment を使用する][UsingASE]」を参照してください。

## <a name="os-preference"></a>OS 基本設定
ASE には、Windows アプリ、Linux アプリ、またはその両方を含めることができます。 ASEv2 では、作成時に選択した初期基本設定によって、ASE の作成時にその OS に高可用性インフラストラクチャが追加されます。 他の OS のアプリを追加するには、通常どおりにアプリを作成し、目的の OS を選択します。 ASEv3 では、これはバックエンド動作にそれほど影響しません。  

## <a name="dedicated-hosts"></a>専用ホスト
ASE は、通常、マルチテナント ハイパーバイザーでプロビジョニングされた VM にデプロイされます。 ハードウェアなどの専用システム上にデプロイする必要がある場合は、ASE を専用ホストにプロビジョニングできます。 初期 ASEv3 プレビューでは、専用ホストはペアになっています。 各専用ホストは、別々の可用性ゾーンに存在します (リージョンでそれが許可されている場合)。 専用ホストベースの ASE デプロイは、通常とは異なる方法で価格が決まります。 専用ホストに料金が発生し、App Service プラン インスタンスごとに別の料金が発生します。  

<!--Links-->
[Intro]: ./overview.md
[MakeASE]: ./creation.md
[ASENetwork]: ./networking.md
[UsingASE]: ./using.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/alerts/alerts-log.md
