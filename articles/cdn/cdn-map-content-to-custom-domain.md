---
title: チュートリアル - カスタム ドメインを Azure CDN エンドポイントに追加する | Microsoft Docs
description: このチュートリアルでは、Azure CDN エンドポイントのコンテンツをカスタム ドメインにマップします。
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/11/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 81db1a7dc01b3d60ee6384f2026ed5ce692ff140
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67666068"
---
# <a name="tutorial-add-a-custom-domain-to-your-azure-cdn-endpoint"></a>チュートリアル:カスタム ドメインを Azure CDN エンドポイントに追加する
このチュートリアルでは、カスタム ドメインを Azure Content Delivery Network (CDN) エンドポイントに追加する方法について説明します。 CDN エンドポイントを使用してコンテンツを配信するときに独自のドメイン名を CDN URL に表示するには、カスタム ドメインが必要です。 見てわかるドメイン名を使用することは、顧客にとって便利であり、ブランド化の目的にも役立ちます。 

プロファイルに CDN エンドポイントを作成すると、azureedge.net のサブドメインであるエンドポイント名が、CDN コンテンツを配信するための URL に既定で含まれます (たとえば、https:\//contoso.azureedge.net/photo.png)。 便宜を図るため、Azure CDN には、カスタム ドメインを CDN エンドポイントと関連付けるオプションが用意されています。 このオプションを使用すると、URL にエンドポイント名の代わりにカスタム ドメインを使用してコンテンツを配信できます (たとえば、https:\//www.contoso.com/photo.png)。 

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> - CNAME DNS レコードを作成します。
> - カスタム ドメインを CDN エンドポイントに関連付けます。
> - カスタム ドメインを確認します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルの手順を完了するには、最初に CDN プロファイルと少なくとも 1 つの CDN エンドポイントを作成する必要があります。 詳細については、「[クイック スタート: Azure CDN プロファイルとエンドポイントの作成](cdn-create-new-endpoint.md)」を参照してください。

カスタム ドメインがまだない場合は、最初にカスタム ドメインをドメイン プロバイダーから購入する必要があります。 たとえば、[カスタム ドメイン名の購入](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain)に関するページを参照してください。

Azure を使用して [DNS ドメイン](https://docs.microsoft.com/azure/dns/dns-overview) をホストしている場合は、ドメイン プロバイダーのドメイン ネーム システム (DNS) を Azure DNS に委任する必要があります。 詳細については、「[Azure DNS へのドメインの委任](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns)」を参照してください。 また、ドメイン プロバイダーを使用して DNS ドメインを処理している場合は、「[CNAME DNS レコードを作成する](#create-a-cname-dns-record)」に進んでください。


## <a name="create-a-cname-dns-record"></a>CNAME DNS レコードを作成する

カスタム ドメインを Azure CDN エンドポイントと共に使用するためには、最初に CDN エンドポイントを指す正規名 (CNAME) レコードをドメイン プロバイダーで作成する必要があります。 CNAME レコードは、ソース ドメイン名を宛先ドメイン名にマップする DNS レコードの一種です。 Azure CDN では、ソース ドメイン名はカスタム ドメイン名であり、宛先ドメイン名は CDN エンドポイント ホスト名です。 作成した CNAME レコードが Azure CDN によって検証されると、ソース カスタム ドメイン (\.contoso.com など) 宛てのトラフィックは、指定された宛先 CDN エンドポイント ホスト名 (contoso.azureedge.net など) にルーティングされます。 

カスタム ドメインとそのサブドメインは、一度に 1 つのエンドポイントにのみ関連付けることができます。 ただし、複数の CNAME レコードを使用して、異なる Azure サービス エンドポイントに対して同じカスタム ドメインの異なるサブドメインを使用することができます。 また、異なるサブドメインがあるカスタム ドメインを同じ CDN エンドポイントにマップすることもできます。

> [!NOTE]
> ドメイン プロバイダーとして Azure DNS を使用している場合は、任意のエイリアス レコード タイプをカスタム ドメインに使用できます。 このチュートリアルでは、CNAME レコード タイプを使用します。 A または AAAA レコード タイプを使用している場合は、次の同じ手順に従って、選択したレコード タイプで CNAME を置き換えてください。 ルート ドメインをカスタム ドメインとして追加するためにエイリアス レコードを使用していて、SSL を有効にする場合は、[こちらの記事](https://docs.microsoft.com/azure/cdn/cdn-custom-ssl?tabs=option-1-default-enable-https-with-a-cdn-managed-certificate#custom-domain-is-not-mapped-to-your-cdn-endpoint)の説明に従って手動による検証を使用する必要があります。 詳細については、「[ゾーンの頂点から Azure CDN エンドポイントをポイントする](https://docs.microsoft.com/azure/dns/dns-alias#point-zone-apex-to-azure-cdn-endpoints)」を参照してください。

## <a name="map-the-temporary-cdnverify-subdomain"></a>一時 cdnverify サブドメインをマップする

運用環境にある既存のドメインをマップする場合、特別な考慮事項があります。 Azure Portal にカスタム ドメインを登録している間、ドメインが短時間ダウンする場合があります。 Web トラフィックの中断を避けるには、Azure cdnverify サブドメインを含む CDN エンドポイント ホスト名にカスタム ドメインをマップして、一時的な CNAME マッピングを作成します。 この方法を使用すると、ユーザーは、DNS マッピングの実行中に中断することなくドメインにアクセスできます。 

それ以外の場合、初めてカスタム ドメインを使用していて、まだ運用トラフィックが発生していないときは、カスタム ドメインを CDN エンドポイントに直接マップできます。 「[永続的なカスタム ドメインをマップする](#map-the-permanent-custom-domain)」に進みます。

cdnverify サブドメインを含む CNAME レコードを作成するには:

1. カスタム ドメインのドメイン プロバイダーの Web サイトにサインインします。

2. プロバイダーの資料を調べるか、 **[ドメイン名]** 、 **[DNS]** 、または **[ネーム サーバー管理]** という名前の付けられた Web サイトの領域を探して、DNS レコードを管理するためのページを見つけます。 

3. カスタム ドメインの CNAME レコード エントリを作成し、次の表に示すようにフィールドを入力します (フィールド名は異なる場合があります)。

    | source                    | Type  | 宛先                     |
    |---------------------------|-------|---------------------------------|
    | cdnverify.www.contoso.com | CNAME | cdnverify.contoso.azureedge.net |

    - ソース:cdnverify サブドメインを含めて、カスタム ドメイン名を cdnverify.&lt;カスタム ドメイン名&gt; 形式で入力します。 たとえば、cdnverify.www.contoso.com とします。

    - 型: 「*CNAME*」と入力します。

    - 変換先:cdnverify サブドメインを含めて、CDN エンドポイントのホスト名を cdnverify. _&lt;エンドポイント名&gt;_ .azureedge.net 形式で入力します。 たとえば、cdnverify.contoso.azureedge.net とします。

4. 変更を保存します。

たとえば、GoDaddy ドメイン レジストラーの場合の手順は次のとおりです。

1. サインインし、使用するカスタム ドメインを選択します。

2. [Domains]\(ドメイン\) セクションで **[Manage All]\(すべてを管理\)** を選択し、次に **[DNS]**  |  **[Manage Zones]\(ゾーンを管理\)** を選択します。

3. **[Domain Name]\(ドメイン名\)** にカスタム ドメインを入力し、 **[Search]\(検索\)** を選択します。

4. **[DNS Management]\(DNS 管理\)** ページで **[Add]\(追加\)** を選択し、 **[Type]\(タイプ\)** リストで **[CNAME]** を選択します。

5. CNAME エントリの次のフィールドに入力します。

    ![CNAME エントリ](./media/cdn-map-content-to-custom-domain/cdn-cdnverify-cname-entry.png)

    - 型: *[CNAME]* を選択したままにします。

    - [Host]\(ホスト\):cdnverify サブドメイン名を含めて、使用するカスタム ドメインのサブドメインを入力します。 たとえば、cdnverify.www とします。

    - [Points to]\(ポイント先\):cdnverify サブドメイン名を含めて、CDN エンドポイントのホスト名を入力します。 たとえば、cdnverify.contoso.azureedge.net とします。 

    - [TTL]: *[1 Hour]\(1 時間\)* を選択したままにします。

6. **[保存]** を選択します。
 
    CNAME エントリが DNS レコード テーブルに追加されます。

    ![DNS レコード テーブル](./media/cdn-map-content-to-custom-domain/cdn-cdnverify-dns-table.png)


## <a name="associate-the-custom-domain-with-your-cdn-endpoint"></a>カスタム ドメインを CDN エンドポイントに関連付ける

カスタム ドメインを登録したら、それを CDN エンドポイントに追加できます。 

1. [Azure portal](https://portal.azure.com/) にサインインし、カスタム ドメインにマップするエンドポイントを含む CDN プロファイルを参照します。
    
2. **[CDN のプロファイル]** ページで、カスタム ドメインに関連付ける CDN エンドポイントを選択します。

   **[エンドポイント]** ページが開きます。
    
3. **[カスタム ドメイン]** を選択します。 

   ![CDN の [カスタム ドメイン] ボタン](./media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png)

   **[カスタム ドメインの追加]** ページが開きます。

4. **[エンドポイントのホスト名]** には、CNAME レコードの宛先ドメインとして使用するエンドポイントのホスト名が事前入力されますが、これは CDN エンドポイントの URL ( *&lt;エンドポイントのホスト名&gt;* .azureedge.net) から得られます。 この値は変更しないでください。

5. **[カスタム ホスト名]** に、CNAME レコードのソース ドメインとして使用するカスタム ドメイン (サブドメインを含む) を入力します。 たとえば、www\.contoso.com または cdn.contoso.com とします。 cdnverify サブドメイン名は使用しないでください。

   ![CDN カスタム ドメイン ダイアログ](./media/cdn-map-content-to-custom-domain/cdn-add-custom-domain.png)

6. **[追加]** を選択します。

   入力したカスタム ドメイン名に対する CNAME レコードが存在するかどうかが Azure によって確認されます。 CNAME が正しければ、カスタム ドメインが検証されます。 

   新しいカスタム ドメインの設定がすべての CDN エッジ ノードに反映されるまでに、少し時間がかかる場合があります。 
    - **Azure CDN Standard from Microsoft** プロファイルの場合、通常、反映は 10 分以内で完了します。 
    - **Azure CDN Standard from Akamai** プロファイルの場合、通常、反映は 1 分以内で完了します。 
    - **Azure CDN Standard from Verizon** プロファイルおよび **Azure CDN Premium from Verizon** プロファイルの場合、通常、反映は 10 分で完了します。   


## <a name="verify-the-custom-domain"></a>カスタム ドメインを確認する

カスタム ドメインの登録を完了した後は、カスタム ドメインが CDN エンドポイントを参照することを確認してください。
 
1. エンドポイントにキャッシュされているパブリック コンテンツがあることを確認します。 たとえば、CDN エンドポイントがストレージ アカウントに関連付けられている場合、Azure CDN はパブリック コンテナーにコンテンツをキャッシュします。 カスタム ドメインをテストするには、コンテナーでパブリック アクセスが許可され、少なくとも 1 つのファイルが格納されていることを確認します。

2. ブラウザーで、カスタム ドメインを使用してファイルのアドレスに移動します。 たとえば、カスタム ドメインが www.contoso.com の場合、キャッシュされたファイルの URL は http:\//www.contoso.com/my-public-container/my-file.jpg のようになります。 *&lt;エンドポイントのホスト名&gt;* .azureedge.net で直接 CDN エンドポイントにアクセスしたときと同じ結果が表示されることを確認します。


## <a name="map-the-permanent-custom-domain"></a>永続的なカスタム ドメインをマップする

cdnverify サブドメインがエンドポイントに正常にマップされていることを確認できた場合 (または運用環境にない新しいカスタム ドメインを使用している場合) は、カスタム ドメインを CDN エンドポイント ホスト名に直接マップできます。

カスタム ドメインの CNAME レコードを作成するには:

1. カスタム ドメインのドメイン プロバイダーの Web サイトにサインインします。

2. プロバイダーの資料を調べるか、 **[ドメイン名]** 、 **[DNS]** 、または **[ネームサーバー管理]** という名前のつけられた Web サイトの領域を探して、DNS レコードを管理するためのページを見つけます。 

3. カスタム ドメインの CNAME レコード エントリを作成し、次の表に示すようにフィールドを入力します (フィールド名は異なる場合があります)。

    | source          | Type  | 宛先           |
    |-----------------|-------|-----------------------|
    | <www.contoso.com> | CNAME | contoso.azureedge.net |

   - ソース:カスタム ドメイン名 (例: www\.contoso.com) を入力します。

   - 型: 「*CNAME*」と入力します。

   - 変換先:CDN エンドポイントのホスト名を入力します。 名前は _&lt;エンドポイント名&gt;_ .azureedge.net 形式である必要があります。 たとえば、contoso.azureedge.net とします。

4. 変更を保存します。

5. 以前に一時 cdnverify サブドメイン CNAME レコードを作成している場合は、それを削除します。 

6. このカスタム ドメインを運用環境で初めて使用する場合は、「[カスタム ドメインを CDN エンドポイントに関連付ける](#associate-the-custom-domain-with-your-cdn-endpoint)」と「[カスタム ドメインを確認する](#verify-the-custom-domain)」の手順に従います。

たとえば、GoDaddy ドメイン レジストラーの場合の手順は次のとおりです。

1. サインインし、使用するカスタム ドメインを選択します。

2. [Domains]\(ドメイン\) セクションで **[Manage All]\(すべてを管理\)** を選択し、次に **[DNS]**  |  **[Manage Zones]\(ゾーンを管理\)** を選択します。

3. **[Domain Name]\(ドメイン名\)** にカスタム ドメインを入力し、 **[Search]\(検索\)** を選択します。

4. **[DNS Management]\(DNS 管理\)** ページで **[Add]\(追加\)** を選択し、 **[Type]\(タイプ\)** リストで **[CNAME]** を選択します。

5. CNAME エントリのフィールドに入力します。

    ![CNAME エントリ](./media/cdn-map-content-to-custom-domain/cdn-cname-entry.png)

    - 型: *[CNAME]* を選択したままにします。

    - [Host]\(ホスト\):使用するカスタム ドメインのサブドメインを入力します。 たとえば、www または cdn とします。

    - [Points to]\(ポイント先\):CDN エンドポイントのホスト名を入力します。 たとえば、contoso.azureedge.net とします。 

    - [TTL]: *[1 Hour]\(1 時間\)* を選択したままにします。

6. **[保存]** を選択します。
 
    CNAME エントリが DNS レコード テーブルに追加されます。

    ![DNS レコード テーブル](./media/cdn-map-content-to-custom-domain/cdn-dns-table.png)

7. cdnverify CNAME レコードがある場合は、その横の鉛筆アイコンを選択し、ごみ箱アイコンを選択します。

8. **[Delete]\(削除\)** を選択して CNAME レコードを削除します。


## <a name="clean-up-resources"></a>リソースのクリーンアップ

上記の手順では、カスタム ドメインを CDN エンドポイントに追加しました。 エンドポイントをカスタム ドメインに関連付けておく必要がない場合は、次の手順を実行してカスタム ドメインを削除できます。
 
1. CDN プロファイルで、削除するカスタム ドメインがあるエンドポイントを選択します。

2. **[エンドポイント]** ページの [カスタム ドメイン] で、削除するカスタム ドメインを右クリックし、コンテキスト メニューから **[削除]** を選択します。  

   カスタム ドメインとエンドポイントの関連付けが解除されます。


## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> - CNAME DNS レコードを作成します。
> - カスタム ドメインを CDN エンドポイントに関連付けます。
> - カスタム ドメインを確認します。

次のチュートリアルに進み、Azure CDN カスタム ドメインで HTTPS を構成する方法を学習してください。

> [!div class="nextstepaction"]
> [チュートリアル:Azure CDN カスタム ドメインで HTTPS を構成する](cdn-custom-ssl.md)


