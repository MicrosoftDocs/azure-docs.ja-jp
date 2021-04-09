---
title: Traffic Manager を使用した複数のエンドポイント間でのフェールオーバー
titleSuffix: Azure Content Delivery Network
description: Azure Traffic Manager を使用して、複数の Azure Content Delivery Network エンドポイントでフェールオーバーを構成する方法について説明します。
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: how-to
ms.date: 10/08/2020
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: d2d3bd43a0f17167e855d7e678a96cd79fe42237
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92777743"
---
# <a name="failover-across-multiple-endpoints-with-azure-traffic-manager"></a>Azure Traffic Manager を使用した複数のエンドポイント間でのフェールオーバー

Azure Content Delivery Network (CDN) を構成するときに、お客様のニーズに最適なプロバイダーと価格レベルを選択できます。 

グローバルに分散したインフラストラクチャを備えた Azure CDN は、既定でローカルおよび地理的冗長性とグローバル負荷分散を作成して、サービスの可用性とパフォーマンスを向上させます。 

コンテンツを提供できない場所がある場合、要求は自動的に別の場所にルーティングされます。 各クライアント要求を処理するための最適なポイント オブ プレゼンス (POP) が使用されます。 自動ルーティングは、要求の場所とサーバーの負荷としての要因に基づいています。
 
複数の CDN プロファイルがある場合は、Azure Traffic Manager を使用して可用性とパフォーマンスをさらに向上させることができます。 

Azure CDN で Azure Traffic Manager を使用すると、以下について複数の CDN エンドポイント間で負荷分散を実現できます。
 
* [フェールオーバー]
* geo 負荷分散 

一般的なフェールオーバー シナリオでは、すべてのクライアント要求がプライマリ CDN プロファイルに送られます。 

このプロファイルが使用できない場合、要求はセカンダリ プロファイルに送られます。  プライマリ プロファイルがオンラインに戻ると、要求はこれを使用して再開されます。

この方法で Azure Traffic Manager を使用することで、Web アプリケーションが常に利用可能になります。 

この記事では、次からのプロファイルを使用してフェールオーバーを構成する方法のガイダンスと例を示します。 

* **Azure CDN Standard from Verizon**
* **Azure CDN Standard from Akamai**

**Azure CDN from Microsoft** もサポートされています。

## <a name="create-azure-cdn-profiles"></a>Azure CDN プロファイルを作成する
異なるプロバイダーを使用した 2 つ以上の Azure CDN プロファイルとエンドポイントを作成します。

1. 2 つの CDN プロファイルを作成します。
    * **Azure CDN Standard from Verizon**
    * **Azure CDN Standard from Akamai** 

    「[新しい CDN エンドポイントの作成](cdn-create-new-endpoint.md#create-a-new-cdn-profile)」の手順に従ってプロファイルを作成します。
 
   ![複数の CDN プロファイル](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. 「[新しい CDN エンドポイントの作成](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)」の手順に従って、新しいプロファイルのそれぞれに少なくとも 1 つのエンドポイントを作成します。

## <a name="create-traffic-manager-profile"></a>Traffic Manager プロファイルを作成する
Azure Traffic Manager プロファイルを作成し、CDN エンドポイント間での負荷分散を構成します。 

1. 「[Traffic Manager プロファイルの作成](../traffic-manager/quickstart-create-traffic-manager-profile.md)」の手順に従って、Azure Traffic Manager プロファイルを作成します。 

    * **[ルーティング方法]** で、 **[優先度]** を選択します。

2. 「[Traffic Manager エンドポイントの追加](../traffic-manager/quickstart-create-traffic-manager-profile.md#add-traffic-manager-endpoints)」の手順に従って、Traffic Manager プロファイルに CDN エンドポイントを追加します

    * **[Type]\(種類\)** で、 **[外部エンドポイント]** を選択します。
    * **[優先度]** に数字を入力します。

    たとえば、優先度が **1** の **cdndemo101akamai.azureedge.net** と優先度が **2** の **cdndemo101verizon.azureedge.net** を作成します。

   ![CDN Traffic Manager エンドポイント](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="configure-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Azure CDN と Azure Traffic Manager にカスタム ドメインを構成する
CDN および Traffic Manager プロファイルを構成したら、次の手順に従って DNS マッピングを追加し、カスタム ドメインを CDN エンドポイントに登録します。 この例では、**cdndemo101.dustydogpetcare.online** というカスタム ドメイン名を使用します。

1. カスタム ドメインのドメイン プロバイダー (GoDaddy など) の Web サイトにアクセスし、2 つの DNS CNAME エントリを作成します。 

    a. 最初の CNAME エントリでは、カスタム ドメインを、cdnverify サブドメインを含めて CDN エンドポイントにマップします。 このエントリは、手順 2 で Traffic Manager に追加した CDN エンドポイントにカスタム ドメインを登録するために必要な手順です。

      次に例を示します。 

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. 2 番目の CNAME エントリでは、カスタム ドメインを、cdnverify サブドメインを含めずに CDN エンドポイントにマップします。 このエントリは、カスタム ドメインを Traffic Manager にマップします。 

      次に例を示します。 
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > ドメインが現在ライブ状態で中断できない場合は、この手順を最後に実行してください。 カスタム ドメイン DNS を Traffic Manager に更新する前に、CDN エンドポイントと Traffic Manager ドメインがライブ状態であることを確認してください。
    >


2.  Azure CDN プロファイルから、最初の CDN エンドポイント (Akamai) を選択します。 **[カスタム ドメインの追加]** を選択して、「**cdndemo101.dustydogpetcare.online**」と入力します。 カスタム ドメインを検証するためのチェックマークが緑色で表示されていることを確認します。 

    Azure CDN は、**cdnverify** サブドメインを使用して DNS マッピングを検証し、この登録プロセスを完了します。 詳細については、「[CNAME DNS レコードを作成する](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record)」を参照してください。 この手順により、Azure CDN がカスタム ドメインを認識し、要求に応答できるようになります。
    
    > [!NOTE]
    > **Akamai プロファイルの Azure CDN** で TLS を有効にするには、cname でカスタム ドメインをエンドポイントに直接指定する必要があります。 TLS を有効にするための cdnverify はまだサポートされていません。 
    >

3.  カスタム ドメインのドメイン プロバイダーの Web サイトに戻ります。 作成した最初の DNS マッピングを更新します。 カスタム ドメインを 2 番目の CDN エンドポイントにマップします。
                             
    次に例を示します。 

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. Azure CDN プロファイルから、2 番目の CDN エンドポイント (Verizon) を選択し、手順 2 を繰り返します。 **[カスタム ドメインの追加]** を選択して、「**cdndemo101.dustydogpetcare.online**」と入力します。
 
これらの手順を完了すると、フェールオーバー機能を備えたマルチ CDN サービスが Azure Traffic Manager で構成されます。 

カスタム ドメインからテスト URL にアクセスできます。 

機能をテストするには、プライマリ CDN エンドポイントを無効にして、要求がセカンダリ CDN エンドポイントに正しく渡されることを確認します。 

## <a name="next-steps"></a>次のステップ
他のルーティング方法 (地理的な方法など) を構成して、異なる CDN エンドポイント間で負荷を分散させることができます。 

詳細については、「[Traffic Manager を使用した地理的トラフィック ルーティング方法の構成](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)」を参照してください。