---
title: "Web アプリケーション ファイアウォールがあるアプリケーション ゲートウェイを作成または更新する | Microsoft Docs"
description: "ポータルを使用して Web アプリケーション ファイアウォールがあるアプリケーション ゲートウェイを作成する方法について説明します"
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: b561a210-ed99-4ab4-be06-b49215e3255a
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: davidmu
ms.openlocfilehash: bfc06c1b44974fd17a3794654503d21d6407a917
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-by-using-the-portal"></a>ポータルを使用して Web アプリケーション ファイアウォールがあるアプリケーション ゲートウェイを作成する

> [!div class="op_single_selector"]
> * [Azure ポータル](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [Azure CLI](application-gateway-web-application-firewall-cli.md)

Web アプリケーション ファイアウォール (WAF) 対応のアプリケーション ゲートウェイを作成する方法について説明します。

Azure Application Gateway の WAF は、SQL インジェクション、クロスサイト スクリプティング攻撃、セッション ハイジャックなどの一般的な Web ベースの攻撃から Web アプリケーションを保護します。 WAF は、OWASP の上位 10 件の一般的な Web 脆弱性の多くを防ぎます。

## <a name="scenarios"></a>シナリオ

この記事では、2 つのシナリオを提示します。 最初のシナリオでは、[WAF があるアプリケーション ゲートウェイを作成する](#create-an-application-gateway-with-web-application-firewall)方法について説明します。 2 番目のシナリオでは、[既存のアプリケーション ゲートウェイに WAF を追加する](#add-web-application-firewall-to-an-existing-application-gateway)方法について説明します。

![Scenario example][scenario]

> [!NOTE]
> アプリケーション ゲートウェイには、カスタム正常性プローブ、バックエンド プール アドレス、およびルールを追加できます。 これらのアプリケーションは、初回のデプロイ中ではなく、アプリケーション ゲートウェイの構成後に構成されます。

## <a name="before-you-begin"></a>開始する前に

 アプリケーション ゲートウェイには、専用のサブネットが必要です。 仮想ネットワークを作成する場合は、複数のサブネットを持つことができるように十分なアドレス空間を残しておいてください。 アプリケーション ゲートウェイをサブネットにデプロイした後、そのサブネットには、追加のアプリケーション ゲートウェイのみをデプロイできます。

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>既存のアプリケーション ゲートウェイに Web アプリケーション ファイアウォールを追加する

ここでは、**[防止]** モードで WAF をサポートするように、既存のアプリケーション ゲートウェイを更新します。

1. Azure ポータルの **[お気に入り]** ウィンドウで **[すべてのリソース]** を選択します。 **[すべてのリソース]** ブレードでアプリケーション ゲートウェイを選択します。 選択したサブスクリプションに既存のリソースがいくつもある場合は、**[名前でフィルター]** ボックスに名前を入力することで、DNS ゾーンに簡単にアクセスできます。

   ![既存のアプリケーション ゲートウェイの選択][1]

2. **[Web アプリケーション ファイアウォール]** を選択し、アプリケーション ゲートウェイの設定を更新します。 更新が終了したら、**[保存]** を選択します。 

3. 次の設定を使用して、WAF をサポートするように既存のアプリケーション ゲートウェイを更新します。

   | **設定** | **値** | **詳細**
   |---|---|---|
   |**[WAF 層にアップグレードする]**| オン | このオプションは、アプリケーション ゲートウェイのレベルを WAF レベルに設定します。|
   |**[ファイアウォールの状態]**| 有効 | この設定では、WAF 上のファイアウォールが有効です。|
   |**[ファイアウォール モード]** | 防止 | この設定は、WAF が悪意のあるトラフィックを処理する方法を指定します。 **[検出]** モードは、イベントを記録するだけです。 **[防止]** モードはイベントをログに記録し、悪意のあるトラフィックを停止します。|
   |**[ルール セット]**|3.0|この設定は、バックエンド プールのメンバーの保護に使用される[コア ルール セット](application-gateway-web-application-firewall-overview.md#core-rule-sets)を決定します。|
   |**[無効化されたルールを構成する]**|多様|起こりうる誤検知を防ぐには、この設定を使用して特定の[ルールとルール グループ](application-gateway-crs-rulegroups-rules.md)を無効にすることができます。|

    >[!NOTE]
    > 既存のアプリケーション ゲートウェイを WAF SKU にアップグレードすると、SKU サイズは **[中]** に変更されます。 構成の完了後に、この設定を再構成することができます。

    ![[基本設定]][2-1]

    > [!NOTE]
    > WAF ログを表示するには、診断を有効にし、**ApplicationGatewayFirewallLog** を選択します。 テスト目的では、インスタンス数として **1** を選択できます。 **2** より少ないインスタンス数は SLA の対象外であるため、お勧めしません。 WAF を使用するときは、小規模のゲートウェイは使用できません。

## <a name="create-an-application-gateway-with-a-web-application-firewall"></a>Web アプリケーション ファイアウォールのあるアプリケーション ゲートウェイを作成する

このシナリオで、以下の作業を行います。

* 2 つのインスタンスを持つ中規模の WAF アプリケーション ゲートウェイを作成します。
* 予約済み CIDR ブロック 10.0.0.0/16 を持つ、AdatumAppGatewayVNET という名前の仮想ネットワークを作成します。
* CIDR ブロックとして 10.0.0.0/28 を使用する Appgatewaysubnet という名前のサブネットを作成します。
* SSL オフロード用の証明書を構成します。

1. [Azure ポータル](https://portal.azure.com)にサインインします。 まだアカウントを持っていない場合は、[1 か月間の無料試用版](https://azure.microsoft.com/free)にサインアップできます。

2. ポータルの **[お気に入り]** ウィンドウで **[新規]** を選択します。

3. **[新規]** ブレードで、**[ネットワーク]** を選択します。 **[ネットワーク]** ブレードで、次の図のように示すように、**[Application Gateway]** を選択します。

    ![アプリケーション ゲートウェイの作成][1]

4. **[基本]** ブレードが表示されたら、以下の値を入力し、**[OK]** を選択します。

   | **設定** | **値** | **詳細**
   |---|---|---|
   |**名前**|AdatumAppGateway|アプリケーション ゲートウェイの名前。|
   |**レベル**|WAF|使用できる値は Standard と WAF です。 WAF の詳細については、「[Web アプリケーション ファイアウォール](application-gateway-web-application-firewall-overview.md)」を参照してください。|
   |**SKU サイズ**|中|Standard レベルのオプションは **[小]**、**[中]**、および **[大]** です。 WAF レベルのオプションは **[中]** と **[大]** のみです。|
   |**インスタンス数**|2|高可用性を得るためのアプリケーション ゲートウェイのインスタンス数。 テスト目的では、インスタンス数として 1 を試用します。|
   |**サブスクリプション**|<該当するサブスクリプション>|アプリケーション ゲートウェイの作成するために使用するサブスクリプションを選択します。|
   |**[リソース グループ]**|**[新規作成]:** AdatumAppGatewayRG|リソース グループを作成します。 選択したサブスクリプション内で一意となるリソース グループ名を使用してください。 リソース グループについて詳しくは、[Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#resource-groups) の概要に関する記事をご覧ください。|
   |**場所**|米国西部||

   ![基本設定の構成][2-2]

5. **[仮想ネットワーク]**の下に表示される **[設定]** ブレードで、**[仮想ネットワークの選択]** を選択します。 **[仮想ネットワークの選択]** ブレードで、 **[新規作成]** を選択します。

   ![仮想ネットワークの選択][2]

6. **[仮想ネットワークの作成]** ブレードで、次の値を入力し、**[OK]** を選択します。 **[設定]** ブレードの **[サブネット]** フィールドに、選択されたサブネットが表示されます。

   |**設定** | **値** | **詳細** |
   |---|---|---|
   |**名前**|AdatumAppGatewayVNET|アプリケーション ゲートウェイの名前。|
   |**アドレス空間**|10.0.0.0/16| この値は、仮想ネットワークのアドレス空間です。|
   |**サブネット名**|AppGatewaySubnet|アプリケーション ゲートウェイのサブネット名。|
   |**サブネットのアドレス範囲**|10.0.0.0/28 | このサブネットを使用すると、バックエンド プール メンバーのために仮想ネットワーク内にさらにサブネットを作成できます。|

7. **[フロントエンド IP 構成]**の **[設定]** ブレードで、**[IP アドレスの種類]** として **[パブリック]** を選択します。

8. **[パブリック IP アドレス]** の **[設定]** ブレードで、**[パブリック IP アドレスの選択]** を選択します。 **[パブリック IP アドレスの選択]** ブレードで、**[新規作成]** を選択します。

   ![パブリック IP アドレスの選択][3]

9. **[パブリック IP アドレスの作成]** ブレードで、既定値を受け入れて、**[OK]** を選択します。 選択したパブリック IP アドレスが **[パブリック IP アドレス]** フィールドに表示されます。

10. **[リスナー構成]** の **[設定]** ブレードで、**[プロトコル]**の下の **[HTTP]** を選択します。 **HTTPS** を使用するには、証明書が必要です。 証明書の秘密キーが必要です。 証明書の .pfx エクスポートを指定し、ファイルのパスワードを入力します。

11. **WAF** 固有の設定を構成します。

   |**設定** | **値** | **詳細** |
   |---|---|---|
   |**[ファイアウォールの状態]**| 有効| この設定は、WAF のオンとオフを切り替えます。|
   |**[ファイアウォール モード]** | 防止| この設定は、悪意のあるトラフィックに対する WAF のアクションを決定します。 **[検出]** モードは、トラフィックをログに記録するだけです。 **[防止]** モードはトラフィックをログに記録し、"403 許可されていません" 応答でトラフィックを停止します。|


12. **[概要]** ページを確認し、**[OK]** を選択します。 これで、アプリケーション ゲートウェイがキューに登録され、作成されます。

13. アプリケーション ゲートウェイが作成されたら、ポータルでそのゲートウェイに移動し、アプリケーション ゲートウェイの構成を続けます。

    ![アプリケーション ゲートウェイのリソース ビュー][10]

これで、リスナー、バックエンド プール、バックエンド HTTP 設定、およびルールの既定の設定を持つ基本的なアプリケーション ゲートウェイが作成されます。 プロビジョニングが正常に終了したら、デプロイに合うようにこれらの設定を変更できます。

> [!NOTE]
> 基本的な WAF の構成で作成されたアプリケーション ゲートウェイは、CRS 3.0 の保護で構成されます。

## <a name="next-steps"></a>次のステップ

[パブリック IP アドレス](../dns/dns-custom-domain.md#public-ip-address)のカスタム ドメインの別名を構成するには、Azure DNS または別の DNS プロバイダーを使用できます。

WAF で検出または防止されたイベントをログに記録するための診断ログを構成するには、[Application Gateway の診断](application-gateway-diagnostics.md)に関する記事を参照してください。

カスタム正常性プローブを作成するには、[カスタムの正常性プローブの作成](application-gateway-create-probe-portal.md)に関する記事を参照してください。

SSL オフロードを構成してコストがかかる SSL サブスクリプションを Web サーバーから切り離すには、[SSL オフロードの構成](application-gateway-ssl-portal.md)に関する記事を参照してください。

<!--Image references-->
[1]: ./media/application-gateway-web-application-firewall-portal/figure1.png
[2]: ./media/application-gateway-web-application-firewall-portal/figure2.png
[2-1]: ./media/application-gateway-web-application-firewall-portal/figure2-1.png
[2-2]: ./media/application-gateway-web-application-firewall-portal/figure2-2.png
[3]: ./media/application-gateway-web-application-firewall-portal/figure3.png
[10]: ./media/application-gateway-web-application-firewall-portal/figure10.png
[scenario]: ./media/application-gateway-web-application-firewall-portal/scenario.png
