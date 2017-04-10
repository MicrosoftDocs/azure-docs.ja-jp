---
title: "Cloud Services のカスタム ドメイン名を構成する | Microsoft Docs"
description: "DNS 設定を構成して、カスタム ドメインで Azure のアプリケーションやデータを公開する方法について説明します。"
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 6a62c2b7-ea47-4cce-9d6a-0cca38357f42
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 2b020004762125aad201cf7eb454da5cf73288ae
ms.openlocfilehash: bc8b93d59b53e17eff4b7448f5c6b445894f47e9


---
# <a name="configuring-a-custom-domain-name-for-an-azure-cloud-service"></a>Azure クラウド サービスのカスタム ドメイン名の構成
> [!div class="op_single_selector"]
> * [Azure ポータル](cloud-services-custom-domain-name-portal.md)
> * [Azure クラシック ポータル](cloud-services-custom-domain-name.md)
> 
> 

クラウド サービスを作成するときに、Azure は cloudapp.net のサブドメインにそのアプリを割り当てます。 たとえば、クラウド サービスの名前が "contoso" の場合、ユーザーは http://contoso.cloudapp.net のような URL でアプリケーションにアクセスできます。 また Azure によって仮想 IP アドレスも割り当てられます。

ただし、contoso.com のような独自のドメイン名を使用してアプリケーションを公開することもできます。 この記事では、クラウド サービス Web ロールのカスタム ドメイン名を予約または構成する方法について説明します

CNAME レコードと A レコードについてすでに理解している場合は、 [説明を読まずに次に進みます](#add-a-cname-record-for-your-custom-domain)。

> [!NOTE]
> 作業を手早く進めましょう。 Azure の [チュートリアル ガイド](http://support.microsoft.com/kb/2990804)をご利用ください。 Azure Cloud Services または Azure Websites を使用したカスタム ドメイン名の関連付けおよび通信 (SSL) のセキュリティ保護がすばやく行えます。
> 
> 

<p/>

> [!NOTE]
> このタスクの手順は、Azure Cloud Services に適用されます。 App Services については、 [こちら](../app-service-web/web-sites-custom-domain-name.md)をご覧ください。 ストレージ アカウントについては、 [こちら](../storage/storage-custom-domain-name.md)をご覧ください。
> 
> 

## <a name="understand-cname-and-a-records"></a>CNAME レコードと A レコードについて
CNAME レコード (またはエイリアス レコード) および A レコードはどちらもドメイン名を特定のサーバー (この場合、またはサービス) に関連付けることができますが、機能は異なります。 また、Azure のクラウド サービスで A レコードを使用する場合には固有の考慮事項もいくつかあるため、どちらのレコードを使用するかを決定する前に、これらの点を検討しておく必要があります。

### <a name="cname-or-alias-record"></a>CNAME レコードまたはエイリアス レコード
CNAME レコードは、 *contoso.com* や **contoso.com** or **特定の**ドメインを正規のドメイン名にマップします。 この場合、正規のドメイン名は Azure ホステッド アプリケーションの **[myapp].cloudapp.net** ドメイン名です。 作成すると、CNAME は **[myapp].cloudapp.net**のエイリアスを作成します。 CNAME エントリは **[myapp].cloudapp.net** サービスの IP アドレスを自動的に解決するため、クラウド サービスの IP アドレスが変更されても、特別な対応をする必要はありません。

> [!NOTE]
> いくつかのドメイン レジストラーでは、CNAME レコードを使用する場合にマップすることが許可されるのは、ルート名 (contoso.com など) ではなく、サブドメイン (www.contoso.com など) のみです。 CNAME レコードの詳細については、レジストラーが提供するドキュメント、[CNAME レコードに関するウィキペディアの項目](http://en.wikipedia.org/wiki/CNAME_record)、または [IETF ドメイン名 - 実装と仕様書](http://tools.ietf.org/html/rfc1035)を参照してください。
> 
> 

### <a name="a-record"></a>A レコード
A レコードは、ドメイン (**contoso.com**、**www.contoso.com** など) または "*ワイルドカード ドメイン*" (**\*.contoso.com** など) を IP アドレスにマップします。 Azure のクラウド サービスの場合は、サービスの仮想 IP です。 CNAME レコードと比較したときの A レコードの主な利点は、エントリにワイルドカードを使用できる (\***.contoso.com** など) ため、複数のサブドメイン (**mail.contoso.com**、**login.contoso.com**、**www.contso.com** など) の要求を処理できることです。

> [!NOTE]
> A レコードは静的 IP にマップされるため、変更をクラウド サービスの IP アドレスに自動的に解決することはできません。 クラウド サービスによって使用される IP アドレスは、空のスロット (運用またはステージング) に初めてデプロイしたときに割り当てられます。スロットのデプロイを削除すると、IP アドレスは Azure によって解放され、そのスロットへの今後のデプロイは新しい IP アドレスに与えられます。
> 
> 都合が良いのは、ステージング デプロイと運用デプロイとの間のスワッピングまたは既存のデプロイのインプレース アップグレードを実行する場合に、所定のデプロイ スロット (運用またはステージング) の IP アドレスが保持されることです。 これらの操作の実行の詳細については、「 [How to manage cloud services (クラウド サービスの管理方法)](cloud-services-how-to-manage.md)」を参照してください。
> 
> 

## <a name="add-a-cname-record-for-your-custom-domain"></a>カスタム ドメインの CNAME レコードの追加
CNAME レコードを作成するには、レジストラーから提供されるツールを使用して、カスタム ドメイン用の DNS テーブルに新しいエントリを追加する必要があります。 それぞれのレジストラーでは CNAME レコードを指定するための同様の (多少異なる) 手段が用意されていますが、その概念は同じです。

1. これらの手段のいずれかを使用して、クラウド サービスに割り当てられた **.cloudapp.net** ドメイン名を見つけます。
   
   * [Azure クラシック ポータル]にログインし、クラウド サービスを選択して、**[ダッシュボード]** を選択し、**[概要]** セクションの **[サイトの URL]** エントリを見つけます。
     
       ![サイトの URL を表示する [概要] セクション][csurl]
     
       **OR**  
   * [Azure Powershell](/powershell/azureps-cmdlets-docs)をインストールして構成し、次のコマンドを使用します。
     
       ```powershell
       Get-AzureDeployment -ServiceName yourservicename | Select Url
       ```
     
     CNAME レコードを作成する場合に必要になるため、いずれかの方法で返された URL で使用されているドメイン名を保存します。
2. DNS レジストラーの Web サイトにログオンし、DNS の管理ページに移動します。 **[ドメイン名]**、**[DNS]**、**[ネーム サーバー管理]** というラベルが付いたサイトのリンクまたは領域を探します。
3. ここで CNAME レコードを選択または入力する場所を探します。 一覧からレコードの種類を選択するか、詳細設定ページに進まなければならない場合があります。 "**CNAME**"、"**エイリアス**"、または "**サブドメイン**" という単語を探します。
4. また、**www.customdomain.com** のエイリアスを作成する場合は、CNAME のドメインまたはサブドメイン エイリアス (**www** など) を指定する必要があります。 ルート ドメインのエイリアスを作成する場合は、レジストラーの DNS ツールに '**@**' シンボルとして示される場合があります。
5. 次に、正規のホスト名 (ここではアプリケーションの **cloudapp.net** ドメイン) を指定します。

たとえば、次の CNAME レコードでは、すべてのトラフィックが **www.contoso.com** から、デプロイされたアプリケーションのカスタム DNS 名である **contoso.cloudapp.net** に転送されます。

| エイリアス/ホスト名/サブドメイン | 正規のドメイン |
| --- | --- |
| www |contoso.cloudapp.net |

**www.contoso.com** の訪問者が本当のホスト (contoso.cloudapp.net) を識別することはないため、転送プロセスはエンド ユーザーから見えなくなります。

> [!NOTE]
> 上の例は、 **www** サブドメインのトラフィックのみに該当します。 CNAME レコードにはワイルドカードを使用できないため、各ドメインおよびサブドメインに 1 つの CNAME を作成する必要があります。 サブドメイン (\*.contoso.com など) からトラフィックを cloudapp.net アドレスに転送するには、DNS 設定の **URL リダイレクト** エントリまたは **URL 転送**エントリを構成するか、または A レコードを作成します。
> 
> 

## <a name="add-an-a-record-for-your-custom-domain"></a>カスタム ドメインの A レコードの追加
A レコードを作成するには、まず、クラウド サービスの仮想 IP アドレスを見つける必要があります。 次に、レジストラーから提供されるツールを使用して、カスタム ドメイン用の DNS テーブルに新しいエントリを追加します。 それぞれのレジストラーでは A レコードを指定するための同様の (多少異なる) 手段が用意されていますが、その概念は同じです。

1. 次の手段のいずれかを使用して、クラウド サービスの IP アドレスを取得します。
   
   * [Azure クラシック ポータル]にログインし、クラウド サービスを選択して、**[ダッシュボード]** を選択し、**[概要]** セクションの **[パブリック仮想 IP (VIP) アドレス]** エントリを見つけます。
     
       ![VIP を表示する [概要] セクション][vip]
     
       **OR**  
   * [Azure Powershell](/powershell/azureps-cmdlets-docs)をインストールして構成し、次のコマンドを使用します。
     
       ```powershell
       get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
       ```
     
     複数のエンドポイントがクラウド サービスに関連付けられている場合は、IP アドレスを含んだ複数の行を受け取りますが、すべての行に同じアドレスが表示されます。
     
     A レコードを作成する場合に必要になるため、IP アドレスを保存します。
2. DNS レジストラーの Web サイトにログオンし、DNS の管理ページに移動します。 **[ドメイン名]**、**[DNS]**、**[ネーム サーバー管理]** というラベルが付いたサイトのリンクまたは領域を探します。
3. ここで A レコードを選択または入力する場所を探します。 一覧からレコードの種類を選択するか、詳細設定ページに進まなければならない場合があります。
4. この A レコードを使用するドメインまたはサブドメインを選択または入力します。 たとえば、**www.customdomain.com** のエイリアスを作成する場合は **www** を選択します。 すべてのサブドメインのワイルドカード エントリを作成する場合は、「__*__」と入力します。 これは、**mail.customdomain.com**、**login.customdomain.com**、**www.customdomain.com** などすべてのサブドメインを対象とします。
   
    ルート ドメインに A レコードを作成する場合は、レジストラーの DNS ツールに '**@**' シンボルとして示される場合があります。
5. 表示されたフィールドのクラウド サービスの IP アドレスを入力します。 これによって、A レコードで使用されるドメイン エントリがクラウド サービスのデプロイの IP アドレスに関連付けられます。

たとえば、次の A レコードでは、すべてのトラフィックが **contoso.com** から、デプロイされたアプリケーションの IP アドレス名である **137.135.70.239** に転送されます。

| ホスト名/サブドメイン | IP アドレス |
| --- | --- |
| @ |137.135.70.239 |

この例では、ルート ドメインの A レコードを作成する方法を示します。 すべてのサブドメインを対象とするワイルドカードを作成する場合は、サブドメインとして「__*__」と入力します。

> [!WARNING]
> Azure の IP アドレスは、既定では動的です。 自分の IP アドレスが変更されないようにするために、 [予約済み IP アドレス](../virtual-network/virtual-networks-reserved-public-ip.md) を使用すると便利です。
> 
> 

## <a name="next-steps"></a>次のステップ
* [Cloud Services の管理方法](cloud-services-how-to-manage.md)
* [CDN コンテンツをカスタム ドメインにマッピングする方法](../cdn/cdn-map-content-to-custom-domain.md)
* [クラウド サービスの一般的な構成](cloud-services-how-to-configure.md)
* 方法: [クラウド サービスをデプロイする](cloud-services-how-to-create-deploy.md)
* [SSL 証明書を構成する](cloud-services-configure-ssl-certificate.md)

[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: http://msdn.microsoft.com/library/ee517253.aspx
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[Azure クラシック ポータル]: https://manage.windowsazure.com
[Validate Custom Domain dialog box]: http://i.msdn.microsoft.com/dynimg/IC544437.jpg
[vip]: ./media/cloud-services-custom-domain-name/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name/csurl.png



<!--HONumber=Dec16_HO3-->


