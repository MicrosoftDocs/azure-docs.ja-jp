---
title: Azure Traffic Manager を使用した複数の Azure CDN エンドポイント間でのフェールオーバーの設定 | Microsoft Docs
description: Azure Traffic Manager を Azure CDN エンドポイントと共に設定する方法について説明します。
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: magattus
ms.custom: ''
ms.openlocfilehash: afadef8b29927f909af5be1e1204180724258b74
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58167067"
---
# <a name="set-up-failover-across-multiple-azure-cdn-endpoints-with-azure-traffic-manager"></a>Azure Traffic Manager を使用した複数の Azure CDN エンドポイント間でのフェールオーバーの設定

Azure Content Delivery Network (CDN) を構成するときに、お客様のニーズに最適なプロバイダーと価格レベルを選択できます。 グローバルに分散したインフラストラクチャを備えた Azure CDN は、既定でローカルおよび地理的冗長性とグローバル負荷分散を作成して、サービスの可用性とパフォーマンスを向上させます。 コンテンツを提供するために場所を利用できない場合、要求は自動的に別の場所にルーティングされ、(要求の場所やサーバーの負荷などの要因に基づいて) 最適な POP が各クライアント要求を処理するために使用されます。 
 
複数の CDN プロファイルがある場合は、Azure Traffic Manager を使用して可用性とパフォーマンスをさらに向上させることができます。 Azure CDN で Azure Traffic Manager を使用すると、フェールオーバー、geo 負荷分散、およびその他のシナリオで複数の CDN エンドポイント間で負荷分散を実現できます。 一般的なフェールオーバーのシナリオでは、すべてのクライアント要求が最初にプライマリ CDN プロファイルに転送されます。プロファイルを使用できない場合、プライマリ CDN プロファイルがオンラインに戻るまで、要求はセカンダリ CDN プロファイルに渡されます。 この方法で Azure Traffic Manager を使用することで、Web アプリケーションが常に利用可能になります。 

この記事では、**Azure CDN Standard from Verizon** と **Azure CDN Standard from Akamai** のプロファイルを使用してフェールオーバーを設定する方法に関するガイダンスと例を示します。

## <a name="set-up-azure-cdn"></a>Azure CDN を設定する 
異なるプロバイダーを使用した 2 つ以上の Azure CDN プロファイルとエンドポイントを作成します。

1. 「[新しい CDN プロファイルを作成する](cdn-create-new-endpoint.md#create-a-new-cdn-profile)」の手順に従って、**Azure CDN Standard from Verizon** および **Azure CDN Standard from Akamai** プロファイルを作成します。
 
   ![複数の CDN プロファイル](./media/cdn-traffic-manager/cdn-multiple-profiles.png)

2. 「[新しい CDN エンドポイントの作成](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint)」の手順に従って、新しいプロファイルのそれぞれに少なくとも 1 つのエンドポイントを作成します。

## <a name="set-up-azure-traffic-manager"></a>Azure Traffic Manager を設定する
Azure Traffic Manager プロファイルを作成し、CDN エンドポイント間での負荷分散を設定します。 

1. 「[Traffic Manager プロファイルの作成](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile)」の手順に従って、Azure Traffic Manager プロファイルを作成します。 

    **[ルーティング方法]** で、**[優先度]** を選択します。

2. 「[Traffic Manager エンドポイントの追加](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-create-profile#add-traffic-manager-endpoints)」の手順に従って、Traffic Manager プロファイルに CDN エンドポイントを追加します

    **[Type]\(種類\)** で、**[外部エンドポイント]** を選択します。 **[優先度]** に数字を入力します。

    たとえば、優先度が *1* の *cdndemo101akamai.azureedge.net* と優先度が *2* の *cdndemo101verizon.azureedge.net* を作成します。

   ![CDN Traffic Manager エンドポイント](./media/cdn-traffic-manager/cdn-traffic-manager-endpoints.png)


## <a name="set-up-custom-domain-on-azure-cdn-and-azure-traffic-manager"></a>Azure CDN と Azure Traffic Manager にカスタム ドメインを設定する
CDN および Traffic Manager プロファイルを設定したら、次の手順に従って DNS マッピングを追加し、カスタム ドメインを CDN エンドポイントに登録します。 この例では、*cdndemo101.dustydogpetcare.online* というカスタム ドメイン名を使用します。

1. カスタム ドメインのドメイン プロバイダー (GoDaddy など) の Web サイトにアクセスし、2 つの DNS CNAME エントリを作成します。 

    a. 最初の CNAME エントリでは、カスタム ドメインを、cdnverify サブドメインを含めて CDN エンドポイントにマップします。 このエントリは、手順 2 で Traffic Manager に追加した CDN エンドポイントにカスタム ドメインを登録するために必要な手順です。

      例:  

      `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101akamai.azureedge.net`  

    b. 2 番目の CNAME エントリでは、カスタム ドメインを、cdnverify サブドメインを含めずに CDN エンドポイントにマップします。 このエントリは、カスタム ドメインを Traffic Manager にマップします。 

      例:  
      
      `cdndemo101.dustydogpetcare.online  CNAME  cdndemo101.trafficmanager.net`   

    > [!NOTE]
    > ドメインが現在ライブ状態で中断できない場合は、この手順を最後に実行してください。 カスタム ドメイン DNS を Traffic Manager に更新する前に、CDN エンドポイントと Traffic Manager ドメインがライブ状態であることを確認してください。
    >


2.  Azure CDN プロファイルから、最初の CDN エンドポイント (Akamai) を選択します。 **[カスタム ドメインの追加]** を選択して、「*cdndemo101.dustydogpetcare.online*」と入力します。 カスタム ドメインを検証するためのチェックマークが緑色で表示されていることを確認します。 

    Azure CDN は、*cdnverify* サブドメインを使用して DNS マッピングを検証し、この登録プロセスを完了します。 詳細については、「[CNAME DNS レコードを作成する](cdn-map-content-to-custom-domain.md#create-a-cname-dns-record)」を参照してください。 この手順により、Azure CDN がカスタム ドメインを認識し、要求に応答できるようになります。

3.  カスタム ドメインのドメイン プロバイダーの Web サイトに戻り、作成した最初の DNS マッピングを更新して、カスタム ドメインが 2 番目の CDN エンドポイントにマップされるようにします。
                             
    例:  

    `cdnverify.cdndemo101.dustydogpetcare.online  CNAME  cdnverify.cdndemo101verizon.azureedge.net`  

4. Azure CDN プロファイルから、2 番目の CDN エンドポイント (Verizon) を選択し、手順 2 を繰り返します。 **[カスタム ドメインの追加]** を選択して、「*cdndemo101.dustydogpetcare.online*」と入力します。
 
これらの手順を完了すると、フェールオーバー機能を備えたマルチ CDN サービスが Azure Traffic Manager で設定されます。 カスタム ドメインからテスト URL にアクセスできます。 機能をテストするには、プライマリ CDN エンドポイントを無効にして、要求がセカンダリ CDN エンドポイントに正しく渡されることを確認します。 

## <a name="next-steps"></a>次の手順
他のルーティング方法 (地理的な方法など) を設定して、異なる CDN エンドポイント間で負荷を分散させることもできます。 詳細については、「[Traffic Manager を使用した地理的トラフィック ルーティング方法の構成](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method)」を参照してください。



