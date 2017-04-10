---
title: "カスタム ドメインへの Azure CDN コンテンツのマッピング | Microsoft Docs"
description: "Azure CDN コンテンツをカスタム ドメインにマッピングする方法について説明します。"
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 289f8d9e-8839-4e21-b248-bef320f9dbfc
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 079986e9457a4185bae273818fbc852307974638
ms.lasthandoff: 03/04/2017


---
# <a name="map-azure-cdn-content-to-a-custom-domain"></a>カスタム ドメインへの Azure CDN コンテンツのマッピング
カスタム ドメインを CDN エンドポイントにマッピングすると、azureedge.net のサブドメインを使用せずに、キャッシュ済みコンテンツへの URL に独自のドメイン名を使用することができます。

カスタム ドメインを CDN エンドポイントにマッピングするには、次の&2; つの方法があります。

1. [ドメイン レジストラーで CNAME レコードを作成し、カスタム ドメインおよびサブドメインを CDN エンドポイントにマップする](#register-a-custom-domain-for-an-azure-cdn-endpoint)
   
    CNAME レコードは、`www.contosocdn.com` や `cdn.contoso.com` のようにソース ドメインを目的のドメインにマッピングする DNS 機能です。 この場合、ソース ドメインは、カスタム ドメインおよびサブドメインです (**www** や **cdn** のようにサブドメインは常に必要です)。 宛先ドメインは、使用する CDN エンドポイントです。  
   
    ただし、カスタム ドメインを CDN エンドポイントにマッピングする処理によって、Azure Portal でのドメインの登録中にドメインが短時間ダウンする場合があります。
2. [**cdnverify** で中間登録ステップを追加する](#register-a-custom-domain-for-an-azure-cdn-endpoint-using-the-intermediary-cdnverify-subdomain)
   
    カスタム ドメインが現在、ダウンタイムが発生しないことを要求するサービス レベル アグリーメント (SLA) のアプリケーションをサポートしている場合は、DNS マッピングの実行中にユーザーがドメインにアクセスできるように、Azure **cdnverify** サブドメインを使用して中間登録ステップを提供できます。  

上記のいずれかの手順を使用してカスタム ドメインを登録しら、 [カスタム サブドメインが CDN エンドポイントを参照することを確認する](#verify-that-the-custom-subdomain-references-your-cdn-endpoint)必要があります。

> [!NOTE]
> ドメインを CDN エンドポイントにマッピングするには、ドメイン レジストラーで CNAME レコードを作成する必要があります。 CNAME レコードは、`www.contoso.com` や `cdn.contoso.com` などの特定のサブドメインをマッピングします。 CNAME レコードを `contoso.com`などのルート ドメインにマップすることはできません。
> 
> サブドメインは&1; つの CDN エンドポイントのみに関連付けできます。 CNAME レコードを作成することで、サブドメイン宛てのすべてのトラフィックが指定されたエンドポイントにルーティングされます。  たとえば、 `www.contoso.com` を CDN エンドポイントに関連付けた場合、ストレージ アカウント エンドポイントやクラウド サービス エンドポイントなど、他の Azure エンドポイントに関連付けることはできません。 ただし、同じドメインの別のサブドメインを別のサービス エンドポイントに使用することはできます。 また、複数のサブドメインを同じ CDN エンドポイントにマップすることもできます。
> 
> **Azure CDN from Verizon** (Standard と Premium) エンドポイントの場合、カスタム ドメインの変更が CDN エッジ ノードに反映されるまでに最大 **90 分**かかります。
> 
> 

## <a name="register-a-custom-domain-for-an-azure-cdn-endpoint"></a>Azure CDN エンドポイント用のカスタム ドメインの登録
1. [Azure Portal](https://portal.azure.com/)にログインします。
2. **[参照]**、**[CDN プロファイル]** の順にクリックし、カスタム ドメインにマップするエンドポイントを含む CDN プロファイルをクリックします。  
3. **[CDN プロファイル]** ブレードで、サブドメインを関連付ける CDN エンドポイントをクリックします。
4. エンドポイント ブレードの上部にある **[Add Custom Domain (カスタム ドメインの追加)]** をクリックします。  **[Add a custom domain (カスタム ドメインの追加)]** ブレードに、CDN エンドポイントから派生したエンドポイント ホスト名が表示されます。このホスト名を使用して新しい CNAME レコードを作成します。 ホスト名のアドレスは、**&lt;EndpointName>.azureedge.net** の形式で表示されます。  このホスト名をコピーして、CNAME レコードの作成に使用できます。  
5. ドメイン レジストラーの Web サイトに移動して、DNS レコードを作成するセクションを探します。 これは、"**ドメイン名**"、"**DNS**"、"**ネーム サーバー管理**" などのセクションにあります。
6. CNAME 管理セクションを見つけます。 詳細設定ページに進み、"CNAME"、"エイリアス"、"サブドメイン" などの語句を探します。
7. 選択したサブドメイン (**www**、**cdn** など) を **[Add a custom domain (カスタム ドメインの追加)]** ブレードに表示されたホスト名にマップする新しい CNAME レコードを作成します。 
8. **[カスタム ドメインの追加]** ブレードに戻り、サブドメインを含むカスタム ドメインをダイアログ ボックスに入力します。 たとえば、`www.contoso.com` や `cdn.contoso.com` という形式でドメイン名を入力します。   
   
   入力したドメイン名に対する CNAME レコードが存在するかどうかが Azure によって確認されます。 CNAME が正しければ、カスタム ドメインが検証されます。  **Azure CDN from Verizon** (Standard と Premium) エンドポイントの場合、カスタム ドメインの設定がすべての CDN エッジ ノードに反映されるまでに最大 90 分かかることがあります。  
   
   CNAME レコードがインターネット上のネーム サーバーに反映されるまでに時間がかかる場合があります。 入力した CNAME レコードが正しいのにドメインがすぐに検証されない場合は、数分間待ってからやり直してください。

## <a name="register-a-custom-domain-for-an-azure-cdn-endpoint-using-the-intermediary-cdnverify-subdomain"></a>中継 cdnverify サブドメインを使用した Azure CDN エンドポイント用のカスタム ドメインの登録
1. [Azure Portal](https://portal.azure.com/)にログインします。
2. **[参照]**、**[CDN プロファイル]** の順にクリックし、カスタム ドメインにマップするエンドポイントを含む CDN プロファイルをクリックします。  
3. **[CDN プロファイル]** ブレードで、サブドメインを関連付ける CDN エンドポイントをクリックします。
4. エンドポイント ブレードの上部にある **[Add Custom Domain (カスタム ドメインの追加)]** をクリックします。  **[Add a custom domain (カスタム ドメインの追加)]** ブレードに、CDN エンドポイントから派生したエンドポイント ホスト名が表示されます。このホスト名を使用して新しい CNAME レコードを作成します。 ホスト名のアドレスは、**&lt;EndpointName>.azureedge.net** の形式で表示されます。  このホスト名をコピーして、CNAME レコードの作成に使用できます。
5. ドメイン レジストラーの Web サイトに移動して、DNS レコードを作成するセクションを探します。 これは、"**ドメイン名**"、"**DNS**"、"**ネーム サーバー管理**" などのセクションにあります。
6. CNAME 管理セクションを見つけます。 詳細設定ページに進み、"**CNAME**"、"**エイリアス**"、"**サブドメイン**" などの語句を探します。
7. 新しい CNAME レコードを作成し、 **cdnverify** サブドメインを含むサブドメインの別名を指定します。 たとえば、**cdnverify.www** や **cdnverify.cdn** の形式でサブドメインを指定します。 次に、ホスト名 (CDN エンドポイント) を **cdnverify.&lt;EndpointName>.azureedge.net** の形式で指定します。 DNS マッピングは、次のようになります。`cdnverify.www.consoto.com   CNAME   cdnverify.consoto.azureedge.net`  
8. **[カスタム ドメインの追加]** ブレードに戻り、サブドメインを含むカスタム ドメインをダイアログ ボックスに入力します。 たとえば、`www.contoso.com` や `cdn.contoso.com` という形式でドメイン名を入力します。 この段階では、サブドメインの前に **cdnverify** を付ける必要はありません。  
   
    入力した cdnverify ドメイン名に対する CNAME レコードが存在するかどうかが Azure によって確認されます。
9. この時点で Azure によってカスタム ドメインが検証されますが、ドメインへのトラフィックは CDN エンドポイントにまだルーティングされません。 カスタム ドメインの設定が CDN エッジ ノードに反映されるまで十分な待ってから (**Azure CDN from Verizon** の場合は 90 分、**Azure CDN from Akamai** の場合は 1 ～ 2 分)、DNS レジストラーの Web サイトに戻り、サブドメインを CDN エンドポイントにマッピングする追加の CNAME レコードを作成します。 たとえば、サブドメインに **www** や **cdn** を指定し、ホスト名に **&lt;EndpointName>.azureedge.net** を指定します。 この手順で、カスタム ドメインの登録が完了します。
10. **cdnverify** を使用して作成した CNAME レコードは中継手順としてのみ必要でした。最後に CNAME レコードを削除してかまいません。  

## <a name="verify-that-the-custom-subdomain-references-your-cdn-endpoint"></a>カスタム サブドメインが CDN エンドポイントを参照することを確認する
* 使用するカスタム ドメインの登録が完了したら、そのカスタム ドメインを使用して、CDN エンドポイントにキャッシュされているコンテンツにアクセスできます。
  まず、エンドポイントにキャッシュされているパブリック コンテンツがあることを確認します。 たとえば、CDN エンドポイントがストレージ アカウントに関連付けられている場合は、CDN はパブリック BLOB コンテナーにコンテンツをキャッシュします。 カスタム ドメインをテストするには、コンテナーでパブリック アクセスが許可され、少なくとも&1; つの BLOB がコンテナーに格納されていることを確認します。
* ブラウザーでカスタム ドメインを使用して BLOB のアドレスに移動します。 カスタム ドメインが `cdn.contoso.com` の場合は、キャッシュされた BLOB の URL は http://cdn.contoso.com/mypubliccontainer/acachedblob.jpg のようになります

## <a name="see-also"></a>関連項目
[Azure の Content Delivery Network (CDN) を有効にする方法](cdn-create-new-endpoint.md)  


