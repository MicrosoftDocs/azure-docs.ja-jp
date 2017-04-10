---
title: "Web アプリケーション ファイアウォールのある Azure Application Gateway の作成または更新 | Microsoft Docs"
description: "ポータルを使用して Web アプリケーション ファイアウォールのある Application Gateway を作成する方法について説明します。"
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: b561a210-ed99-4ab4-be06-b49215e3255a
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/03/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 9f16384a3944c3943dbfc094aaba37a24969e949
ms.lasthandoff: 03/30/2017


---

# <a name="create-an-application-gateway-with-web-application-firewall-by-using-the-portal"></a>ポータルを使用した、Web アプリケーション ファイアウォールのあるアプリケーション ゲートウェイの作成

> [!div class="op_single_selector"]
> * [Azure ポータル](application-gateway-web-application-firewall-portal.md)
> * [Azure Resource Manager の PowerShell](application-gateway-web-application-firewall-powershell.md)

Azure Application Gateway の Web アプリケーション ファイアウォール (WAF) は、SQL インジェクション、クロスサイト スクリプティング攻撃、セッション ハイジャックなどの一般的な Web ベースの攻撃から Web アプリケーションを保護します。 Web アプリケーションは、OWASP の上位 10 件の一般的な Web 脆弱性の多くを防ぎます。

Azure Application Gateway はレイヤー 7 のロード バランサーです。 クラウドでもオンプレミスでも、異なるサーバー間のフェールオーバーと HTTP 要求のパフォーマンス ルーティングを提供します。
Application Gateway は、HTTP 負荷分散、Cookie ベースのセッション アフィニティ、Secure Sockets Layer (SSL) オフロード、カスタムの正常性プローブ、マルチサイトのサポートなどの多くのアプリケーション配信コントローラー (ADC) 機能を備えています。
サポートされている機能の完全な一覧については、「 [Application Gateway の概要](application-gateway-introduction.md)

## <a name="scenarios"></a>シナリオ

この記事では、次の 2 つのシナリオを扱います。

最初のシナリオでは、 [既存のアプリケーション ゲートウェイに Web アプリケーション ファイアウォールを追加する](#add-web-application-firewall-to-an-existing-application-gateway)方法を説明します。

2 番目のシナリオでは、 [Web アプリケーション ファイアウォールのあるアプリケーション ゲートウェイを作成する](#create-an-application-gateway-with-web-application-firewall)

![Scenario example][scenario]

> [!NOTE]
> カスタムの正常性プローブ、バックエンド プール アドレス、追加規則など、アプリケーション ゲートウェイの追加の構成は、初めてデプロイしている間ではなく、アプリケーション ゲートウェイが構成された後で構成されます。

## <a name="before-you-begin"></a>開始する前に

Azure Application Gateway には、専用のサブネットが必要です。 仮想ネットワークを作成する場合は、複数のサブネットを持つことができるように十分なアドレス空間を残しておいてください。 アプリケーション ゲートウェイをサブネットにデプロイすると、追加のアプリケーション ゲートウェイのみをそのサブネットにデプロイすることができます。

##<a name="add-web-application-firewall-to-an-existing-application-gateway"></a> 既存のアプリケーション ゲートウェイに Web アプリケーション ファイアウォールを追加する

このシナリオでは、防止モードで Web アプリケーション ファイアウォールをサポートするように、既存のアプリケーション ゲートウェイを更新します。

### <a name="step-1"></a>手順 1

Azure ポータルに移動し、既存の Application Gateway を選択します。

![Creating Application Gateway][1]

### <a name="step-2"></a>手順 2.

**[Web アプリケーション ファイアウォール]** をクリックして、アプリケーション ゲートウェイの設定を更新します。 完了したら、 **[保存]**

Web アプリケーション ファイアウォールをサポートするように既存のアプリケーション ゲートウェイを更新する設定は次のとおりです。

* **[WAF 層にアップグレードする]** - WAF の構成に必要です。
* **[ファイアウォールの状態]** - Web アプリケーション ファイアウォールを無効または有効にします。
* **[ファイアウォール モード]** - Web アプリケーション ファイアウォールが悪意のあるトラフィックを処理する方法です。 **[検出]** モードではイベントの記録のみを行い、**[防止]** モードではイベントをログに記録し、悪意のあるトラフィックを停止します。
* **[ルール セット]** - バックエンド プールのメンバーの保護に使用される[コア ルール セット](application-gateway-web-application-firewall-overview.md#core-rule-sets)を決定します。
* **[Configure disabled rules]** (無効化ルールの設定) - 特定の[ルールとルール グループ](application-gateway-crs-rulegroups-rules.md)を無効にして、起こりうる誤検知を防ぎます。

>[!NOTE]
> 既存のアプリケーション ゲートウェイを WAF SKU にアップグレードするときに、SKU サイズは **[中]** に変更されます。 これは構成が完了した後に再設定できます。

![blade showing basic settings][2]

> [!NOTE]
> Web アプリケーション ファイアウォール ログを表示するには、診断を有効にし、ApplicationGatewayFirewallLog を選択している必要があります。 テスト目的では、インスタンス数として 1 を選択できます。 重要なのは、2 より小さいインスタンス数は SLA の対象外のため、推奨されていないことを把握しておくことです。 Web アプリケーション ファイアウォールを使用する場合、小規模のゲートウェイを利用できません。

## <a name="create-an-application-gateway-with-web-application-firewall"></a>Web アプリケーション ファイアウォールのあるアプリケーション ゲートウェイを作成する

このシナリオで、以下の作業を行います。

* 2 つのインスタンスを持つ中規模の、Web アプリケーション ファイアウォールのあるアプリケーション ゲートウェイを作成します。
* 予約済み CIDR ブロック 10.0.0.0/16 を持つ、AdatumAppGatewayVNET という名前の仮想ネットワークを作成します。
* CIDR ブロックとして 10.0.0.0/28 を使用する Appgatewaysubnet という名前のサブネットを作成します。
* SSL オフロード用の証明書を構成します。

### <a name="step-1"></a>手順 1

Azure ポータルに移動し、**[新規]**、 > **[ネットワーク]** > 、**[Application Gateway]** の順にクリックします。

![Creating Application Gateway][1-1]

### <a name="step-2"></a>手順 2.

次に、アプリケーション ゲートウェイに関する基本情報を入力します。 層として **[WAF]** を選択してください。 完了したら、 **[OK]**

基本設定に必要な情報は次のとおりです。

* **[名前]** - アプリケーション ゲートウェイの名前。
* **[層]** - アプリケーション ゲートウェイの層。使用できるオプションは (**[Standard]** と **[WAF]**) です。 Web アプリケーション ファイアウォールは、WAF 層のみで利用できます。
* **[SKU サイズ]** - アプリケーション ゲートウェイのサイズ。使用できるオプションは、(**[中]** と **[大]**) です。
* **[インスタンス数]** - インスタンスの数。この値は、**2** ～ **10** の数値で指定する必要があります。
* **[リソース グループ]** - アプリケーション ゲートウェイを保持するリソース グループ。既存のリソース グループを指定することも、新しいグループを指定することもできます。
* **[場所]** - アプリケーション ゲートウェイのリージョン。リソース グループと同じ場所にします。 "*この場所は、仮想ネットワークとパブリック IP はゲートウェイと同じ場所に存在する必要があるため、重要です。*"

![blade showing basic settings][2-2]

> [!NOTE]
> テスト目的では、インスタンス数として 1 を選択できます。 重要なのは、2 より小さいインスタンス数は SLA の対象外のため、推奨されていないことを把握しておくことです。 Web アプリケーション ファイアウォールのシナリオでは、小規模のゲートウェイはサポートされていません。

### <a name="step-3"></a>手順 3.

基本設定を定義したら、次の手順では、使用する仮想ネットワークを定義します。 仮想ネットワークは、アプリケーション ゲートウェイによる負荷分散の対象となるアプリケーションを収容します。

仮想ネットワークを構成するには、 **[仮想ネットワークの選択]** をクリックします。

![blade showing settings for application gateway][3]

### <a name="step-4"></a>手順 4.

**[仮想ネットワークの選択]** ブレードで、 **[新規作成]**

このシナリオでは説明しませんが、この時点で既存の仮想ネットワークを選択することもできます。  既存の仮想ネットワークを使用する場合、仮想ネットワークで使用する空のサブネットまたはアプリケーション ゲートウェイ リソースのみのサブネットが必要です。

![choose virtual network blade][4]

### <a name="step-5"></a>手順 5.

**[仮想ネットワークの作成]** ブレードで、前述の「 [シナリオ](#scenario) 」で説明したように、ネットワーク情報を入力します。

![Create virtual network blade with information entered][5]

### <a name="step-6"></a>手順 6.

仮想ネットワークが作成されたら、次の手順ではアプリケーション ゲートウェイのフロントエンド IP を定義します。 現時点では、フロントエンドにはパブリック IP アドレスかプライベート IP アドレスを選択できます。 どちらを選択するかは、アプリケーションがインターネットに接続しているか、内部使用のみであるかによって決まります。 このシナリオでは、パブリック IP アドレスを使用することを前提としています。 プライベート IP アドレスを選択するには、**[プライベート]** ボタンをクリックします。 自動的に割り当てられた IP アドレスが選択されます。または、**[特定のプライベート IP アドレスの選択]** チェック ボックスをオンにして、手動で入力することもできます。

**[パブリック IP アドレスの選択]** をクリックします。 既存のパブリック IP アドレスが使用可能な場合は、この時点で選択できます。このシナリオでは、新しいパブリック IP アドレスを作成します。 **Create new**

![Choose public IP address blade][6]

### <a name="step-7"></a>手順 7.

次に、パブリック IP アドレスにフレンドリ名を付け、 **[OK]**

![Create public IP Address blade][7]

### <a name="step-8"></a>手順 8.

次に、リスナーの構成を設定します。  **http** が使用される場合は、何も構成する必要がないため、**[OK]** をクリックします。 **https** を利用する場合は、さらに構成が必要になります。

**https**を使用するには、証明書が必要です。 証明書の秘密キーが必要であるため、証明書の .pfx エクスポートとファイルのパスワードを指定する必要があります。

**[HTTPS]** をクリックし、**[PFX 証明書のアップロード]** テキスト ボックスの横にある**フォルダー** アイコンをクリックします。
ファイル システム上の .pfx 証明書ファイルに移動します。 そのファイルを選択したら、証明書にフレンドリ名を付け、.pfx ファイルのパスワードを入力します。

完了したら **[OK]** をクリックし、アプリケーション ゲートウェイの設定を確認します。

![Listener Configuration section on Settings blade][8]

### <a name="step-9"></a>手順 9.

**WAF** 固有の設定を構成します。

* **[ファイアウォールの状態]** - WAF のオンとオフを切り替えます。
* **[ファイアウォール モード]** - 悪意のあるトラフィックに対処するときの WAF のアクションを決定します。 **[検出]** を選択すると、トラフィックがログに記録されます。  **[防止]** を選択すると、トラフィックがログに記録され、"403 許可されていません" という応答で停止します。

![Web アプリケーション ファイアウォールの設定][9]

### <a name="step-10"></a>手順 10.

[概要] ページを確認し、 **[OK]**をクリックします。  これで、アプリケーション ゲートウェイがキューに登録され、作成されます。

### <a name="step-11"></a>手順 11.

アプリケーション ゲートウェイが作成されたら、ポータルでそのゲートウェイに移動し、アプリケーション ゲートウェイの構成を続けます。

![Application Gateway resource view][10]

これで、リスナー、バックエンド プール、バックエンド http 設定、ルールの既定の設定を持つ基本的なアプリケーション ゲートウェイが作成されます。 プロビジョニングが成功したら、独自のデプロイに合わせて、これらの設定を変更することができます。

> [!NOTE]
> 基本的な Web アプリケーション ファイアウォールの構成で作成されたアプリケーション ゲートウェイは、CRS 3.0 の保護で構成されています。

## <a name="next-steps"></a>次のステップ

[Application Gateway の診断](application-gateway-diagnostics.md)に関するページにアクセスして、診断ログを構成する方法と、Web アプリケーション ファイアウォールで検出または防止されたイベントを記録する方法を確認します。

[カスタムの正常性プローブの作成](application-gateway-create-probe-portal.md)

[SSL オフロードの構成](application-gateway-ssl-portal.md)

<!--Image references-->
[1]: ./media/application-gateway-web-application-firewall-portal/figure1.png
[2]: ./media/application-gateway-web-application-firewall-portal/figure2.png
[1-1]: ./media/application-gateway-web-application-firewall-portal/figure1-1.png
[2-2]: ./media/application-gateway-web-application-firewall-portal/figure2-2.png
[3]: ./media/application-gateway-web-application-firewall-portal/figure3.png
[4]: ./media/application-gateway-web-application-firewall-portal/figure4.png
[5]: ./media/application-gateway-web-application-firewall-portal/figure5.png
[6]: ./media/application-gateway-web-application-firewall-portal/figure6.png
[7]: ./media/application-gateway-web-application-firewall-portal/figure7.png
[8]: ./media/application-gateway-web-application-firewall-portal/figure8.png
[9]: ./media/application-gateway-web-application-firewall-portal/figure9.png
[10]: ./media/application-gateway-web-application-firewall-portal/figure10.png
[scenario]: ./media/application-gateway-web-application-firewall-portal/scenario.png

