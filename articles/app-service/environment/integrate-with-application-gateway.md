---
title: Application Gateway との統合
description: このエンド ツー エンドのチュートリアルでは、ILB App Service Environment のアプリを Application Gateway と統合する方法について説明します。
author: ccompy
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.topic: article
ms.date: 03/03/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 3b73d528802a8aa33c6122eaf5edfa9d046b6753
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88962079"
---
# <a name="integrate-your-ilb-app-service-environment-with-the-azure-application-gateway"></a>ILB App Service Environment を Azure Application Gateway と統合する #

[App Service Environment](./intro.md) は、ユーザーの Azure 仮想ネットワークのサブネットに Azure App Service をデプロイしたものです。 これは、アプリにアクセスするためのパブリック エンドポイントまたはプライベート エンドポイントを使用してデプロイできます。 プライベート エンドポイント (つまり、内部ロード バランサー) を使う App Service Environment の展開は、ILB App Service Environment と呼ばれます。  

Web アプリケーション ファイアウォール は、着信する Web トラフィックを検査して、SQL インジェクション、クロスサイト スクリプティング、マルウェアのアップロード、アプリケーション DDoS、およびその他の攻撃をブロックすることにより、Web アプリケーションのセキュリティを確保するのに役立ちます。 さらに、データ損失防止 (DLP) のためにバックエンド Web サーバーからの応答を検査します。 Azure Marketplace から WAF デバイスを取得することも、[Azure Application Gateway][appgw] を使用することもできます。

Azure Application Gateway は、レイヤー 7 負荷分散、TLS/SSL オフロード、および Web アプリケーション ファイアウォール (WAF) 保護を提供する仮想アプライアンスです。 これは、パブリック IP アドレスでリッスンし、トラフィックをアプリケーション エンドポイントにルーティングします。 以下では、WAF で構成されたアプリケーション ゲートウェイを ILB App Service Environment のアプリと統合する方法について説明します。  

アプリケーション ゲートウェイと ILB App Service Environment の統合は、アプリ レベルで行われます。 アプリケーション ゲートウェイを ILB App Service Environment で構成する場合、ILB App Service Environment の特定のアプリに対して行うことになります。 この手法を使うと、単一の ILB App Service Environment で、セキュリティ保護されたマルチテナント アプリケーションをホストできます。  

![ILB App Service Environment 上のアプリを指すアプリケーション ゲートウェイ][1]

このチュートリアルでは次を行います。

* Azure Application Gateway を作成します。
* ILB App Service Environment 上のアプリを指すように Application Gateway を構成します。
* カスタム ドメイン名を優先するようにアプリを構成します。
* アプリケーション ゲートウェイを指すパブリック DNS ホスト名を編集します。

## <a name="prerequisites"></a>前提条件

Application Gateway を ILB App Service Environment と統合するには、次のものが必要です。

* ILB App Service Environment。
* ILB App Service Environment で実行するアプリ。
* ILB App Service Environment のアプリで使うインターネット ルーティング可能なドメイン名。
* ILB App Service Environment が使う ILB アドレス。 この情報は、App Service Environment ポータルの **[設定]**  >  **[IP アドレス]** にあります。

    ![ILB App Service Environment で使われる IP アドレスの一覧の例][9]
    
* 後で Application Gateway を指すために使われるパブリック DNS 名。 

ILB App Service Environment の作成方法について詳しくは、[ILB App Service Environment の作成と使用][ilbase]に関するページをご覧ください。

この記事では、App Service Environment が展開されているのと同じ Azure 仮想ネットワークに Application Gateway を作成するものとします。 Application Gateway の作成を始める前に、ゲートウェイをホストするために使うサブネットを選択または作成します。 

GatewaySubnet という名前ではないサブネットを使う必要があります。 Application Gateway を GatewaySubnet に配置した場合、後で仮想ネットワーク ゲートウェイを作成できなくなります。 

また、ILB App Service Environment が使っているサブネットにゲートウェイを配置することもできません。 このサブネット内に存在できるのは App Service Environment だけです。

## <a name="configuration-steps"></a>構成の手順 ##

1. Azure Portal で、 **[新規]**  >  **[ネットワーク]**  >  **[アプリケーション ゲートウェイ]** に移動します。

2. **[基本]** 領域で次のように設定します。

   a. **[名前]** に、Application Gateway の名前を入力します。

   b. **[レベル]** で、 **[WAF]** を選びます。

   c. **[サブスクリプション]** で、App Service Environment の仮想ネットワークが使っているものと同じサブスクリプションを選びます。

   d. **[リソース グループ]** で、リソース グループを作成または選択します。

   e. **[場所]** で、App Service Environment 仮想ネットワークの場所を選びます。

   ![新しい Application Gateway の作成の基礎][2]

3. **[設定]** 領域で次のように設定します。

   a. **[仮想ネットワーク]** で、App Service Environment の仮想ネットワークを選びます。

   b. **[サブネット]** で、Application Gateway を展開する必要があるサブネットを選びます。 GatewaySubnet は使わないでください。VPN ゲートウェイを作成できなくなります。

   c. **[IP アドレスの種類]** で、 **[パブリック]** を選びます。

   d. **[パブリック IP アドレス]** で、パブリック IP アドレスを選びます。 パブリック IP アドレスがない場合は、ここで作成します。

   e. **[プロトコル]** で、 **[HTTP]** または **[HTTPS]** を選びます。 HTTPS を構成する場合は、PFX 証明書を提供する必要があります。

   f. **[Web アプリケーション ファイアウォール]** では、ファイアウォールを有効にすることができ、必要に応じて **[検出]** または **[防止]** に設定することもできます。

   ![新しい Application Gateway の作成の設定][3]
    
4. **[概要]** セクションで設定を確認し、 **[OK]** を選びます。 Application Gateway のセットアップが完了するまでに、30 分少々かかる可能性があります。  

5. Application Gateway のセットアップが完了したら、Application Gateway ポータルに移動します。 **[バックエンド プール]** を選択します。 ILB App Service Environment の ILB アドレスを追加します。

   ![バックエンド プールを構成する][4]

6. バックエンド プールの構成処理が完了した後、 **[正常性プローブ]** を選びます。 アプリに使うドメイン名の正常性プローブを作成します。 

   ![正常性プローブを構成する][5]
    
7. 正常性プローブの構成処理が完了した後、 **[HTTP 設定]** を選びます。 既存の設定を編集し、 **[カスタム プローブの使用]** を選んで、構成したプローブを選びます。

   ![HTTP 設定を構成する][6]
    
8. Application Gateway の **[概要]** セクションに移動し、Application Gateway が使うパブリック IP アドレスをコピーします。 その IP アドレスをアプリ ドメイン名の A レコードとして設定するか、CNAME レコードでそのアドレスの DNS 名を使用します。 パブリック IP アドレスを選んで、それをパブリック IP アドレスの UI にコピーするほうが、Application Gateway の **[概要]** セクションのリンクからコピーするより簡単です。 

   ![Application Gateway ポータル][7]

9. アプリのカスタム ドメイン名を ILB App Service Environment に設定します。 ポータルでアプリに移動し、 **[設定]** の **[カスタム ドメイン]** を選びます。

   ![アプリでカスタム ドメイン名を設定する][8]

Web アプリのカスタム ドメイン名の設定について詳しくは、[Web アプリのカスタム ドメイン名の設定][custom-domain]に関するページをご覧ください。 ただし、ILB App Service Environment 内のアプリの場合は、ドメイン名についての検証は何もありません。 アプリ エンドポイントを管理する DNS を所有しているため、必要なものをすべて自由に配置できます。 ここで追加するカスタム ドメイン名は、DNS 内に配置する必要はありませんが、アプリで構成する必要はあります。 

セットアップが完了した後、DNS の変更が反映されるまでしばらく待つと、作成したカスタム ドメイン名を使ってアプリにアクセスできます。 


<!--IMAGES-->
[1]: ./media/integrate-with-application-gateway/appgw-highlevel.png
[2]: ./media/integrate-with-application-gateway/appgw-createbasics.png
[3]: ./media/integrate-with-application-gateway/appgw-createsettings.png
[4]: ./media/integrate-with-application-gateway/appgw-backendpool.png
[5]: ./media/integrate-with-application-gateway/appgw-healthprobe.png
[6]: ./media/integrate-with-application-gateway/appgw-httpsettings.png
[7]: ./media/integrate-with-application-gateway/appgw-publicip.png
[8]: ./media/integrate-with-application-gateway/appgw-customdomainname.png
[9]: ./media/integrate-with-application-gateway/appgw-iplist.png

<!--LINKS-->
[appgw]: ../../application-gateway/overview.md
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[ilbase]: ./create-ilb-ase.md
