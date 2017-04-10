---
title: "ポータルを使用してアプリケーション ゲートウェイを作成する | Microsoft Docs"
description: "ポータルを使用してアプリケーション ゲートウェイを作成する方法について説明します。"
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 54dffe95-d802-4f86-9e2e-293f49bd1e06
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/12/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 9edaa7a101ae0e1a395491999854ee7009fb69cd
ms.lasthandoff: 03/30/2017


---
# <a name="create-an-application-gateway-by-using-the-portal"></a>ポータルを使用してアプリケーション ゲートウェイを作成する

> [!div class="op_single_selector"]
> * [Azure ポータル](application-gateway-create-gateway-portal.md)
> * [Azure Resource Manager の PowerShell](application-gateway-create-gateway-arm.md)
> * [Azure Classic PowerShell (Azure クラシック PowerShell)](application-gateway-create-gateway.md)
> * [Azure Resource Manager テンプレート](application-gateway-create-gateway-arm-template.md)
> * [Azure CLI](application-gateway-create-gateway-cli.md)

Azure Application Gateway はレイヤー 7 のロード バランサーです。 クラウドでもオンプレミスでも、異なるサーバー間のフェールオーバーと HTTP 要求のパフォーマンス ルーティングを提供します。
Application Gateway は、HTTP 負荷分散、Cookie ベースのセッション アフィニティ、Secure Sockets Layer (SSL) オフロード、カスタムの正常性プローブ、マルチサイトのサポートなどの多くのアプリケーション配信コントローラー (ADC) 機能を備えています。

サポートされている機能の完全な一覧については、「 [Application Gateway の概要](application-gateway-introduction.md)

## <a name="scenario"></a>シナリオ

このシナリオでは、Azure ポータルを使用してアプリケーション ゲートウェイを作成する方法について説明します。

このシナリオで、以下の作業を行います。

* 2 つのインスタンスを持つ中規模のアプリケーション ゲートウェイを作成します。
* 予約済み CIDR ブロック 10.0.0.0/16 を持つ、AdatumAppGatewayVNET という名前の仮想ネットワークを作成します。
* CIDR ブロックとして 10.0.0.0/28 を使用する Appgatewaysubnet という名前のサブネットを作成します。
* SSL オフロード用の証明書を構成します。

![Scenario example][scenario]

> [!IMPORTANT]
> カスタムの正常性プローブ、バックエンド プール アドレス、追加規則など、アプリケーション ゲートウェイの追加の構成は、初めてデプロイしている間ではなく、アプリケーション ゲートウェイが構成された後で構成されます。

## <a name="before-you-begin"></a>開始する前に

Azure Application Gateway には、専用のサブネットが必要です。 仮想ネットワークを作成する場合は、複数のサブネットを持つことができるように十分なアドレス空間を残しておいてください。 アプリケーション ゲートウェイをサブネットにデプロイすると、追加のアプリケーション ゲートウェイのみをそのサブネットにデプロイすることができます。

## <a name="create-the-application-gateway"></a>アプリケーション ゲートウェイの作成

### <a name="step-1"></a>手順 1

Azure ポータルに移動し、**[新規]**、 > **[ネットワーク]** > 、**[Application Gateway]** の順にクリックします。

![Creating Application Gateway][1]

### <a name="step-2"></a>手順 2.

次に、アプリケーション ゲートウェイに関する基本情報を入力します。 完了したら、 **[OK]**

基本設定に必要な情報は次のとおりです。

* **[名前]** - アプリケーション ゲートウェイの名前。
* **[レベル]** - アプリケーション ゲートウェイのレベル。 **WAF** と **Standard** の 2 つのレベルがあります。 WAF では、Web アプリケーション ファイアウォール機能が有効になります。
* **[SKU サイズ]** - アプリケーション ゲートウェイのサイズ。**S**、**M**、**L** のオプションがあります。 WAF レベルを選択した場合、S は使用できません。
* **[インスタンス数]** - インスタンスの数。この値は、2 ～ 10 の数値で指定する必要があります。
* **[リソース グループ]** - アプリケーション ゲートウェイを保持するリソース グループ。既存のリソース グループを指定することも、新しいグループを指定することもできます。
* **[場所]** - アプリケーション ゲートウェイのリージョン。リソース グループと同じ場所にします。 この場所は、仮想ネットワークとパブリック IP はゲートウェイと同じ場所に存在する必要があるため、重要です。

![blade showing basic settings][2]

> [!NOTE]
> テスト目的では、インスタンス数として 1 を選択できます。 重要なのは、2 より小さいインスタンス数は SLA の対象外のため、推奨されていないことを把握しておくことです。 小規模のゲートウェイは開発テスト用であり、運用目的ではありません。
> 
> 

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

### <a name="step-7"></a>手順 7.

**[パブリック IP アドレスの選択]** をクリックします。 既存のパブリック IP アドレスが使用可能な場合は、この時点で選択できます。このシナリオでは、新しいパブリック IP アドレスを作成します。 **Create new**

![Choose public IP address blade][6]

### <a name="step-8"></a>手順 8.

次に、パブリック IP アドレスにフレンドリ名を付け、 **[OK]**

![Create public IP Address blade][7]

### <a name="step-9"></a>手順 9.

アプリケーション ゲートウェイを作成するときに構成する最後の設定は、リスナー構成です。  **http** が使用される場合は、何も構成する必要がないため、**[OK]** をクリックします。 **https** を利用する場合は、さらに構成が必要になります。

**https**を使用するには、証明書が必要です。 証明書の秘密キーが必要であるため、証明書の .pfx エクスポートとパスワードを指定する必要があります。

### <a name="step-10"></a>手順 10.

**[HTTPS]** をクリックし、**[PFX 証明書のアップロード]** テキスト ボックスの横にある**フォルダー** アイコンをクリックします。
ファイル システム上の .pfx 証明書ファイルに移動します。 そのファイルを選択したら、証明書にフレンドリ名を付け、.pfx ファイルのパスワードを入力します。

完了したら **[OK]** をクリックし、アプリケーション ゲートウェイの設定を確認します。

![Listener Configuration section on Settings blade][9]

### <a name="step-11"></a>手順 11.

[概要] ページを確認し、 **[OK]**をクリックします。  これで、アプリケーション ゲートウェイがキューに登録され、作成されます。

### <a name="step-12"></a>手順 12.

アプリケーション ゲートウェイが作成されたら、ポータルでそのゲートウェイに移動し、アプリケーション ゲートウェイの構成を続けます。

![Application Gateway resource view][10]

これで、リスナー、バックエンド プール、バックエンド http 設定、ルールの既定の設定を持つ基本的なアプリケーション ゲートウェイが作成されます。 プロビジョニングが成功したら、独自のデプロイに合わせて、これらの設定を変更することができます。

## <a name="add-servers-to-backend-pools"></a>バックエンド プールヘのサーバーの追加

アプリケーション ゲートウェイが作成されたら、負荷分散するアプリケーションをホストするシステムを、引き続きアプリケーション ゲートウェイに追加する必要があります。 これらのサーバーの IP アドレスまたは FQDN 値は、バックエンド アドレス プールに追加されます。

### <a name="ip-address-or-fqdn"></a>IP アドレスまたは FQDN

#### <a name="step-1"></a>手順 1

作成したアプリケーション ゲートウェイ、**[バックエンド プール]** の順にクリックし、現在のバックエンド プールを選択します。

![Application Gateway のバックエンド プール][11]

#### <a name="step-2"></a>手順 2.

**[ターゲットの追加]** をクリックして FQDN 値の IP アドレスを追加します。

![Application Gateway のバックエンド プール][11-1]

#### <a name="step-3"></a>手順 3.

バックエンド値がすべて入力されたら、**[保存]** をクリックします。

![Application Gateway のバックエンド プールに値を追加する][12]

この操作により、バックエンド プールに値が保存されます。 アプリケーション ゲートウェイが更新されたら、アプリケーション ゲートウェイに入るトラフィックは、この手順で追加されたバックエンド アドレスにルーティングされます。

### <a name="virtual-machine-and-nic"></a>仮想マシンと NIC

バックエンド プールのメンバーとして、仮想マシンの NIC を追加することもできます。 Application Gateway と同じ仮想ネットワーク内の仮想マシンのみ、ドロップダウン リストで利用可能です。

#### <a name="step-1"></a>手順 1

作成したアプリケーション ゲートウェイ、**[バックエンド プール]** の順にクリックし、現在のバックエンド プールを選択します。

![Application Gateway のバックエンド プール][11]

#### <a name="step-2"></a>手順 2.

**[ターゲットの追加]** をクリックして、新しいバックエンド プール メンバーを追加します。 ドロップダウン ボックスから、仮想マシンと NIC を選択します。

![アプリケーション ゲートウェイ バックエンド プールに NIC を追加][13]

#### <a name="step-3"></a>手順 3.

完了したら、**[保存]** をクリックして NIC をバックエンドのメンバーとして保存します。

![NIC のアプリケーション ゲートウェイ バックエンドを保存][14]

## <a name="next-steps"></a>次のステップ

このシナリオでは、既定のアプリケーション ゲートウェイを作成します。 次の手順として、設定の変更、およびゲートウェイ内のルールの調整を実行して、アプリケーション ゲートウェイを構成します。 これらの手順について詳しくは、次の記事をご覧ください。

[カスタムの正常性プローブの作成](application-gateway-create-probe-portal.md)

[SSL オフロードの構成](application-gateway-ssl-portal.md)

アプリケーション ゲートウェイの機能の 1 つである [Web アプリケーション ファイアウォール](application-gateway-webapplicationfirewall-overview.md)を使用してアプリケーションを保護する方法について学びます。

<!--Image references-->
[1]: ./media/application-gateway-create-gateway-portal/figure1.png
[2]: ./media/application-gateway-create-gateway-portal/figure2.png
[3]: ./media/application-gateway-create-gateway-portal/figure3.png
[4]: ./media/application-gateway-create-gateway-portal/figure4.png
[5]: ./media/application-gateway-create-gateway-portal/figure5.png
[6]: ./media/application-gateway-create-gateway-portal/figure6.png
[7]: ./media/application-gateway-create-gateway-portal/figure7.png
[8]: ./media/application-gateway-create-gateway-portal/figure8.png
[9]: ./media/application-gateway-create-gateway-portal/figure9.png
[10]: ./media/application-gateway-create-gateway-portal/figure10.png
[11]: ./media/application-gateway-create-gateway-portal/figure11.png
[11-1]: ./media/application-gateway-create-gateway-portal/figure11-1.png
[12]: ./media/application-gateway-create-gateway-portal/figure12.png
[13]: ./media/application-gateway-create-gateway-portal/figure13.png
[14]: ./media/application-gateway-create-gateway-portal/figure14.png
[scenario]: ./media/application-gateway-create-gateway-portal/scenario.png

