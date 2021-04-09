---
title: チュートリアル:カスタム ドメインをエンドポイントに追加する
titleSuffix: Azure Content Delivery Network
description: このチュートリアルでは、独自のドメイン名を URL に反映するために、Azure Content Delivery Network のエンドポイントにカスタム ドメインを追加します。
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: b0e8f2b14d506eb408660b939a7c925a33215cca
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99537748"
---
# <a name="tutorial-add-a-custom-domain-to-your-endpoint"></a>チュートリアル:カスタム ドメインをエンドポイントに追加する

このチュートリアルでは、カスタム ドメインを Azure Content Delivery Network (CDN) エンドポイントに追加する方法について説明します。 

CDN プロファイル内のエンドポイント名は、azureedge.net のサブドメインです。 既定では、コンテンツを配信する際に、CDN プロファイル ドメインが URL 内に追加されます。

たとえば、**https://contoso.azureedge.net/photo.png** です。

Azure CDN には、カスタム ドメインを CDN エンドポイントと関連付けるオプションが用意されています。 この場合、URL に既定のドメインではなくカスタム ドメインを使用してコンテンツが配信されます。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> - CNAME DNS レコードを作成します。
> - カスタム ドメインを CDN エンドポイントに関連付けます。
> - カスタム ドメインを確認します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

* このチュートリアルの手順を完了するには、最初に CDN プロファイルと少なくとも 1 つの CDN エンドポイントを作成しておいてください。 
    * 詳細については、「[クイック スタート: Azure CDN プロファイルとエンドポイントの作成](cdn-create-new-endpoint.md)」を参照してください。

* カスタム ドメインがない場合は、カスタム ドメインをドメイン プロバイダーから購入する必要があります。 
    * 詳細については、[カスタム ドメイン名の購入](../app-service/manage-custom-dns-buy-domain.md)に関するページを参照してください。

* Azure を使用して [DNS ドメイン](../dns/dns-overview.md)をホストしている場合は、カスタム ドメインを Azure DNS に委任してください。 
    * 詳細については、「[Azure DNS へのドメインの委任](../dns/dns-delegate-domain-azure-dns.md)」を参照してください。

* ドメイン プロバイダーを使用して DNS ドメインを処理している場合は、「[CNAME DNS レコードを作成する](#create-a-cname-dns-record)」に進んでください。


## <a name="create-a-cname-dns-record"></a>CNAME DNS レコードを作成する

カスタム ドメインを Azure CDN エンドポイントと共に使用するためには、最初に CDN エンドポイントを指す正規名 (CNAME) レコードを Azure DNS またはご利用の DNS プロバイダーで作成する必要があります。 

CNAME レコードは、ソース ドメイン名を宛先ドメイン名にマップする DNS レコードです。 

Azure CDN では、ソース ドメイン名はカスタム ドメイン名であり、宛先ドメイン名は CDN エンドポイント ホスト名です。 

CNAME レコードが Azure CDN によって検証されると、ソース カスタム ドメイン宛てのトラフィックは、宛先の CDN エンドポイント ホスト名にルーティングされます。

カスタム ドメインとそのサブドメインは、一度に 1 つのエンドポイントに関連付けることができます。 

異なる Azure サービスに対して、同じカスタム ドメインの異なるサブドメインを使用する場合は、複数の CNAME レコードを使用してください。

異なるサブドメインがあるカスタム ドメインを同じ CDN エンドポイントにマップすることができます。

> [!NOTE]
> このチュートリアルでは、CNAME レコード タイプを使用します。 A または AAAA レコード タイプを使用している場合は、次の同じ手順に従って、選択したレコード タイプで CNAME を置き換えてください。

---
# <a name="azure-dns"></a>[**Azure DNS**](#tab/azure-dns)

Azure DNS では、同じサブスクリプション内の Azure リソースにエイリアス レコードが使用されます。

Azure CDN エンドポイントのエイリアス レコードを追加するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. 左側のメニューから **[すべてのリソース]** を選択し、カスタム ドメインの Azure DNS ゾーンを選択します。

3. カスタム ドメインの DNS ゾーンで、 **[+ レコード セット]** を選択します。

4. **[レコード セットの追加]** で、次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | 名前  | CDN エンドポイントに使用したいエイリアスを入力します。 たとえば **www** にします。 |
    | 型  | **[CNAME]** を選択します。 |
    | エイリアスのレコード セット | **[はい]** を選択します。 |
    | エイリアスの種類 | **[Azure リソース]** を選択します。 |
    | サブスクリプションを選択します。 | サブスクリプションを選択します。 |
    | Azure リソース | Azure CDN エンドポイントを選択します。 |

5. レコードの **TTL** を適切な値に変更します。

6. **[OK]** を選択します。

# <a name="dns-provider"></a>[**DNS プロバイダー**](#tab/dns-provider)

## <a name="map-the-temporary-cdnverify-subdomain"></a>一時 cdnverify サブドメインをマップする

運用環境にある既存のドメインをマップする場合、特別な考慮事項があります。 

Azure portal でカスタム ドメインを登録する際に、一時的にドメインのダウンタイムが発生することがあります。

Web トラフィックの中断を避けるには、Azure **cdnverify** サブドメインを含む CDN エンドポイント ホスト名にカスタム ドメインをマップします。 このプロセスによって、一時的な CNAME マッピングが作成されます。 

この方法を使用すると、ユーザーは、DNS マッピングの実行中に中断することなくドメインにアクセスできます。 

運用環境のダウンタイムを考慮する必要がなければ、カスタム ドメインを直接 CDN エンドポイントにマップしてかまいません。 「[永続的なカスタム ドメインをマップする](#map-the-permanent-custom-domain)」に進んでください。

cdnverify サブドメインを含む CNAME レコードを作成するには:

1. カスタム ドメインの DNS プロバイダーの Web サイトにサインインします。

2. カスタム ドメインの CNAME レコード エントリを作成し、次の表に示すようにフィールドを入力します (フィールド名は異なる場合があります)。

    | source                    | Type  | 宛先                     |
    |---------------------------|-------|---------------------------------|
    | cdnverify.www\.contoso.com | CNAME | cdnverify.contoso.azureedge.net |

    - ソース:cdnverify サブドメインを含めて、カスタム ドメイン名を **cdnverify.\<custom-domain-name>** 形式で入力します。
        - たとえば、**cdnverify. www.contoso.com** とします。

    - 型: **CNAME** を入力または選択します。

    - 変換先:cdnverify サブドメインを含めて、CDN エンドポイントのホスト名を **cdnverify.\<endpoint-name>.azureedge.net** 形式で入力します。 
        - たとえば、**cdnverify.contoso.azureedge.net** とします。

3. 変更を保存します。

## <a name="map-the-permanent-custom-domain"></a>永続的なカスタム ドメインをマップする

このセクションでは、永続的なカスタム ドメインを CDN エンドポイントにマップします。 

カスタム ドメインの CNAME レコードを作成するには:

1. カスタム ドメインのドメイン プロバイダーの Web サイトにサインインします。

2. カスタム ドメインの CNAME レコード エントリを作成し、次の表に示すようにフィールドを入力します (フィールド名は異なる場合があります)。

    | source          | Type  | 宛先           |
    |-----------------|-------|-----------------------|
    | www.contoso.com | CNAME | contoso.azureedge.net |

    - ソース:カスタム ドメイン名を入力します。
        - たとえば、**www.contoso.com** とします。

    - 型: **CNAME** を入力または選択します。

    - 変換先:CDN エンドポイントのホスト名を **\<endpoint-name>.azureedge.net** 形式で入力します。 
        - たとえば、**contoso.azureedge.net** とします。

3. 変更を保存します。

4. 以前に一時 cdnverify サブドメイン CNAME レコードを作成している場合は、それを削除します。 

5. このカスタム ドメインを運用環境で初めて使用する場合は、「[カスタム ドメインを CDN エンドポイントに関連付ける](#associate-the-custom-domain-with-your-cdn-endpoint)」と「[カスタム ドメインを確認する](#verify-the-custom-domain)」の手順に従います。

---
## <a name="associate-the-custom-domain-with-your-cdn-endpoint"></a>カスタム ドメインを CDN エンドポイントに関連付ける

カスタム ドメインを登録したら、それを CDN エンドポイントに追加できます。 


---
# <a name="azure-portal"></a>[**Azure portal**](#tab/azure-portal)

1. [Azure portal](https://portal.azure.com/) にサインインし、カスタム ドメインにマップするエンドポイントを含む CDN プロファイルを参照します。
    
2. **[CDN のプロファイル]** ページで、カスタム ドメインに関連付ける CDN エンドポイントを選択します。

    :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-endpoint-selection.png" alt-text="CDN エンドポイントの選択" border="true":::
    
3. **[+ カスタム ドメイン]** を選択します。 

   :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png" alt-text="カスタム ドメインの追加ボタン" border="true":::

4. **[カスタム ドメインの追加]** の **[エンドポイントのホスト名]** は自動的に入力されます。これは、CDN エンドポイントの URL ( **\<endpoint-hostname>** .azureedge.net) から得られます。 この値は変更しないでください。

5. **[カスタム ホスト名]** に、CNAME レコードのソース ドメインとして使用するカスタム ドメイン (サブドメインを含む) を入力します。 
    1. たとえば、**www.contoso.com** または **cdn.contoso.com** とします。 **cdnverify サブドメイン名は使用しないでください**。

    :::image type="content" source="media/cdn-map-content-to-custom-domain/cdn-add-custom-domain.png" alt-text="カスタム ドメインの追加" border="true":::

6. **[追加]** を選択します。

   入力したカスタム ドメイン名に対する CNAME レコードが存在するかどうかが Azure によって確認されます。 CNAME が正しければ、カスタム ドメインが検証されます。 

   新しいカスタム ドメインの設定がすべての CDN エッジ ノードに反映されるまでに、少し時間がかかる場合があります。 
    - **Azure CDN Standard from Microsoft** プロファイルの場合、通常、反映は 10 分以内で完了します。 
    - **Azure CDN Standard from Akamai** プロファイルの場合、通常、反映は 1 分以内で完了します。 
    - **Azure CDN Standard from Verizon** プロファイルおよび **Azure CDN Premium from Verizon** プロファイルの場合、通常、反映は 10 分で完了します。   

# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell)

1. Azure PowerShell にサインインします。

```azurepowershell-interactive
    Connect-AzAccount

```
2. [New-AzCdnCustomDomain](/powershell/module/az.cdn/new-azcdncustomdomain) を使用して、カスタム ドメインを CDN エンドポイントにマップします。 

    * **myendpoint8675.azureedge.net** をエンドポイント URL に置き換えます。
    * **myendpoint8675** を CDN エンドポイント名に置き換えます。
    * **www.contoso.com** をカスタム ドメイン名に置き換えます。
    * **myCDN** を CDN プロファイル名に置き換えます。
    * **myResourceGroupCDN** をリソース グループ名に置き換えます。

```azurepowershell-interactive
    $parameters = @{
        Hostname = 'myendpoint8675.azureedge.net'
        EndPointName = 'myendpoint8675'
        CustomDomainName = 'www.contoso.com'
        ProfileName = 'myCDN'
        ResourceGroupName = 'myResourceGroupCDN'
    }
    New-AzCdnCustomDomain @parameters
```

入力したカスタム ドメイン名に対する CNAME レコードが存在するかどうかが Azure によって確認されます。 CNAME が正しければ、カスタム ドメインが検証されます。 

   新しいカスタム ドメインの設定がすべての CDN エッジ ノードに反映されるまでに、少し時間がかかる場合があります。 

- **Azure CDN Standard from Microsoft** プロファイルの場合、通常、反映は 10 分以内で完了します。 
- **Azure CDN Standard from Akamai** プロファイルの場合、通常、反映は 1 分以内で完了します。 
- **Azure CDN Standard from Verizon** プロファイルおよび **Azure CDN Premium from Verizon** プロファイルの場合、通常、反映は 10 分で完了します。   


---
## <a name="verify-the-custom-domain"></a>カスタム ドメインを確認する

カスタム ドメインの登録を完了した後は、カスタム ドメインが CDN エンドポイントを参照することを確認してください。
 
1. エンドポイントにキャッシュされているパブリック コンテンツがあることを確認します。 たとえば、CDN エンドポイントがストレージ アカウントに関連付けられている場合、Azure CDN はパブリック コンテナーにコンテンツをキャッシュします。 カスタム ドメインをテストするには、パブリック アクセスを許可するようにコンテナーを設定し、そこに少なくとも 1 つのファイルを格納してください。

2. ブラウザーで、カスタム ドメインを使用してファイルのアドレスに移動します。 たとえば、カスタム ドメインが `www.contoso.com` である場合、キャッシュされたファイルの URL は `http://www.contoso.com/my-public-container/my-file.jpg` のようになります。 **\<endpoint-hostname>** .azureedge.net で直接 CDN エンドポイントにアクセスしたときと同じ結果が表示されることを確認します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

---
# <a name="azure-portal"></a>[**Azure portal**](#tab/azure-portal-cleanup)

エンドポイントをカスタム ドメインに関連付けておく必要がない場合は、次の手順を実行してカスタム ドメインを削除してください。
 
1. CDN プロファイルで、削除するカスタム ドメインがあるエンドポイントを選択します。

2. **[エンドポイント]** ページの [カスタム ドメイン] で、削除するカスタム ドメインを右クリックし、コンテキスト メニューから **[削除]** を選択します。 **[はい]** を選択します。

   カスタム ドメインとエンドポイントの関連付けが解除されます。

# <a name="powershell"></a>[**PowerShell**](#tab/azure-powershell-cleanup)

エンドポイントをカスタム ドメインに関連付けておく必要がない場合は、次の手順を実行してカスタム ドメインを削除してください。

1. [Remove-AzCdnCustomDomain](/powershell/module/az.cdn/remove-azcdncustomdomain) を使用して、エンドポイントからカスタム ドメインを削除します。

    * **myendpoint8675** を CDN エンドポイント名に置き換えます。
    * **www.contoso.com** をカスタム ドメイン名に置き換えます。
    * **myCDN** を CDN プロファイル名に置き換えます。
    * **myResourceGroupCDN** をリソース グループ名に置き換えます。


```azurepowershell-interactive
    $parameters = @{
        CustomDomainName = 'www.contoso.com'
        EndPointName = 'myendpoint8675'
        ProfileName = 'myCDN'
        ResourceGroupName = 'myResourceGroupCDN'
    }
    Remove-AzCdnCustomDomain @parameters
```

---
## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> - CNAME DNS レコードを作成します。
> - カスタム ドメインを CDN エンドポイントに関連付けます。
> - カスタム ドメインを確認します。

次のチュートリアルに進み、Azure CDN カスタム ドメインで HTTPS を構成する方法を学習してください。

> [!div class="nextstepaction"]
> [チュートリアル:Azure CDN カスタム ドメインで HTTPS を構成する](cdn-custom-ssl.md)