---
title: チュートリアル - カスタム ドメインを Azure Front Door の構成に追加する
description: このチュートリアルでは、Azure Front Door にカスタム ドメインをオンボードする方法を説明します。
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/24/2020
ms.author: duau
ms.openlocfilehash: e153edd807dcb119c34f60dc34e33fed510916bb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011525"
---
# <a name="tutorial-add-a-custom-domain-to-your-front-door"></a>チュートリアル:Front Door にカスタム ドメインを追加する
このチュートリアルでは、Front Door にカスタム ドメインを追加する方法を説明します。 アプリケーション デリバリーに Azure Front Door を使用している場合、独自のドメイン名がエンド ユーザーの要求で示されるようにしたいときは、カスタム ドメインが必要です。 見てわかるドメイン名を使用することは、顧客にとって便利であり、ブランド化の目的にも役立ちます。

Front Door を作成すると、`azurefd.net` のサブドメインである既定のフロントエンド ホストが、バックエンドから Front Door コンテンツを配信するための URL に、既定で含まれるようになります (例: https:\//contoso-frontend.azurefd.net/activeusers.htm)。 便宜を図るため、Azure Front Door には、カスタム ドメインを既定のホストと関連付けるオプションが用意されています。 このオプションを使用すると、コンテンツ配信の URL に、Front Door 所有のドメイン名ではなく、カスタム ドメインが含まれるようになります (例: https:\//www.contoso.com/photo.png)。 

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> - CNAME DNS レコードを作成します。
> - カスタム ドメインを Front Door と関連付けます。
> - カスタム ドメインを確認します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Front Door は [punycode](https://en.wikipedia.org/wiki/Punycode) 文字を持つカスタム ドメインをサポートして **いません**。 

## <a name="prerequisites"></a>前提条件

* このチュートリアルの手順を完了するには、最初に Front Door を作成する必要があります。 詳細については、「[クイック スタート: Front Door の作成](quickstart-create-front-door.md)」を参照してください。

* カスタム ドメインがまだない場合は、最初にカスタム ドメインをドメイン プロバイダーから購入する必要があります。 たとえば、[カスタム ドメイン名の購入](../app-service/manage-custom-dns-buy-domain.md)に関するページを参照してください。

* Azure を使用して [DNS ドメイン](../dns/dns-overview.md) をホストしている場合は、ドメイン プロバイダーのドメイン ネーム システム (DNS) を Azure DNS に委任する必要があります。 詳細については、「[Azure DNS へのドメインの委任](../dns/dns-delegate-domain-azure-dns.md)」を参照してください。 また、ドメイン プロバイダーを使用して DNS ドメインを処理している場合は、「[CNAME DNS レコードを作成する](#create-a-cname-dns-record)」に進んでください。


## <a name="create-a-cname-dns-record"></a>CNAME DNS レコードを作成する

カスタム ドメインを Front Door で使用するためには、最初に Front Door の既定のフロントエンド ホスト (contose.azurefd.net など) を指す正規名 (CNAME) レコードをドメイン プロバイダーで作成する必要があります。 CNAME レコードは、ソース ドメイン名を宛先ドメイン名にマップする DNS レコードの一種です。 Azure Front Door では、ソース ドメイン名はカスタム ドメイン名であり、宛先ドメイン名は Front Door の既定のホスト名です。 作成した CNAME レコードが After Front Door によって検証されると、ソース カスタム ドメイン (www\.contoso.com など) 宛てのトラフィックは、指定された宛先の Front Door デフォルト フロントエンド ホスト (contoso-frontend.azurefd.net など) にルーティングされます。 

カスタム ドメインとそのサブドメインは、一度に 1 つの Front Door にのみ関連付けることができます。 ただし、複数の CNAME レコードを使用して、異なる Front Door に対して同じカスタム ドメインの異なるサブドメインを使用することができます。 また、異なるサブドメインがあるカスタム ドメインを同じ Front Door にマップすることもできます。


## <a name="map-the-temporary-afdverify-subdomain"></a>一時的な afdverify サブドメインをマップする

運用環境にある既存のドメインをマップする場合、特別な考慮事項があります。 Azure Portal にカスタム ドメインを登録している間、ドメインが短時間ダウンする場合があります。 Web トラフィックの中断を避けるには、Azure afdverify サブドメインを含む Front Door の既定のフロントエンド ホストにカスタム ドメインをマップして、一時的な CNAME マッピングを作成します。 この方法を使用すると、ユーザーは、DNS マッピングの実行中に中断することなくドメインにアクセスできます。

それ以外の場合、初めてカスタム ドメインを使用していて、まだ運用トラフィックが発生していないときは、カスタム ドメインを Front Door に直接マップできます。 「[永続的なカスタム ドメインをマップする](#map-the-permanent-custom-domain)」に進みます。

afdverify サブドメインを含む CNAME レコードを作成するには:

1. カスタム ドメインのドメイン プロバイダーの Web サイトにサインインします。

2. プロバイダーの資料を調べるか、 **[ドメイン名]** 、 **[DNS]** 、または **[ネーム サーバー管理]** という名前の付けられた Web サイトの領域を探して、DNS レコードを管理するためのページを見つけます。 

3. カスタム ドメインの CNAME レコード エントリを作成し、次の表に示すようにフィールドを入力します (フィールド名は異なる場合があります)。

    | source                    | Type  | 宛先                     |
    |---------------------------|-------|---------------------------------|
    | afdverify. www.contoso.com | CNAME | afdverify.contoso-frontend.azurefd.net |

    - ソース:afdverify サブドメインを含めて、カスタム ドメイン名を afdverify. _&lt;カスタム ドメイン名&gt;_ の形式で入力します。 たとえば、afdverify. www.contoso.com などです。

    - 型: 「*CNAME*」と入力します。

    - 変換先:afdverify サブドメインを含む既定の Front Door フロントエンド ホストを、 _&lt;エンドポイント名&gt;_ .azurefd.net の形式で入力します。 たとえば、afdverify.contoso-frontend.azurefd.net などです。

4. 変更を保存します。

たとえば、GoDaddy ドメイン レジストラーの場合の手順は次のとおりです。

1. サインインし、使用するカスタム ドメインを選択します。

2. [Domains]\(ドメイン\) セクションで **[Manage All]\(すべてを管理\)** を選択し、次に **[DNS]**  |  **[Manage Zones]\(ゾーンを管理\)** を選択します。

3. **[Domain Name]\(ドメイン名\)** にカスタム ドメインを入力し、 **[Search]\(検索\)** を選択します。

4. **[DNS Management]\(DNS 管理\)** ページで **[Add]\(追加\)** を選択し、 **[Type]\(タイプ\)** リストで **[CNAME]** を選択します。

5. CNAME エントリの次のフィールドに入力します。

    - 型: *[CNAME]* を選択したままにします。

    - [Host]\(ホスト\):afdverify サブドメイン名を含めて、使用するカスタム ドメインのサブドメインを入力します。 たとえば、afdverify.www などです。

    - [Points to]\(ポイント先\):afdverify のサブドメイン名を含む、既定の Front Door フロントエンド ホストのホスト名を入力します。 たとえば、afdverify.contoso-frontend.azurefd.net などです。 

    - TTL: *[one Hour]\(1 時間\)* を選択したままにします。

6. **[保存]** を選択します。
 
    CNAME エントリが DNS レコード テーブルに追加されます。


## <a name="associate-the-custom-domain-with-your-front-door"></a>カスタム ドメインを Front Door と関連付ける

カスタム ドメインを登録したら、それを Front Door に追加できます。

1. [Azure portal](https://portal.azure.com/) にサインインし、カスタム ドメインにマップするフロントエンド ホストを含む Front Door を参照します。
    
2. **Front Door デザイナー** のページで、[+] をクリックしてカスタム ドメインを追加します。
    
3. **[Custom domain]\(カスタム ドメイン\)** を指定します。 

4. **[Frontend host]\(フロントエンド ホスト\)** には、CNAME レコードのターゲット ドメインとして使用するフロントエンド ホストが事前入力されていて、Front Door から派生されています ( *&lt;既定のホスト名&gt;* .azurefd.net)。 この値は変更しないでください。

5. **[カスタム ホスト名]** に、CNAME レコードのソース ドメインとして使用するカスタム ドメイン (サブドメインを含む) を入力します。 たとえば、www\.contoso.com または cdn.contoso.com とします。 afdverify サブドメイン名は使用しないでください。

6. **[追加]** を選択します。

   入力したカスタム ドメイン名に対する CNAME レコードが存在するかどうかが Azure によって確認されます。 CNAME が正しければ、カスタム ドメインが検証されます。

>[!WARNING]
> Front Door 内の各フロントエンド ホスト (カスタム ドメインを含む) に、既定のパス ("/\*") が関連付けられたルーティング規則があることを、確認する **必要があります**。 つまり、すべてのルーティング規則について、既定のパス ("/\*") で定義された各フロントエンド ホストに対するルーティング規則が少なくとも 1 つは存在する必要があります。 そうなっていないと、エンド ユーザーのトラフィックが正しくルーティングされない可能性があります。

## <a name="verify-the-custom-domain"></a>カスタム ドメインを確認する

カスタム ドメインの登録を完了した後は、カスタム ドメインが既定の Front Door フロントエンド ホストを参照することを確認してください。
 
ブラウザーで、カスタム ドメインを使用してファイルのアドレスに移動します。 たとえば、カスタム ドメインが robotics.contoso.com の場合、キャッシュされたファイルの URL は http:\//robotics.contoso.com/my-public-container/my-file.jpg のようになります。 *&lt;Front Door host&gt;* .azurefd.net で Front Door に直接アクセスするときと同じ結果になることを確認します。


## <a name="map-the-permanent-custom-domain"></a>永続的なカスタム ドメインをマップする

afdverify サブドメインが Front Door に正常にマップされていることを確認できた場合 (または運用環境にない新しいカスタム ドメインを使用している場合) は、カスタム ドメインを既定の Front Door フロントエンド ホストに直接マップできます。

カスタム ドメインの CNAME レコードを作成するには:

1. カスタム ドメインのドメイン プロバイダーの Web サイトにサインインします。

2. プロバイダーの資料を調べるか、 **[ドメイン名]** 、 **[DNS]** 、または **[ネームサーバー管理]** という名前のつけられた Web サイトの領域を探して、DNS レコードを管理するためのページを見つけます。 

3. カスタム ドメインの CNAME レコード エントリを作成し、次の表に示すようにフィールドを入力します (フィールド名は異なる場合があります)。

    | source          | Type  | 宛先           |
    |-----------------|-------|-----------------------|
    | <www.contoso.com> | CNAME | contoso-frontend.azurefd.net |

   - ソース:カスタム ドメイン名 (例: www\.contoso.com) を入力します。

   - 型: 「*CNAME*」と入力します。

   - 変換先:既定の Front Door フロントエンド ホストを入力します。 名前は、 _&lt;ホスト名&gt;_ .azurefd.net の形式である必要があります。 たとえば、contoso-frontend.azurefd.net などです。

4. 変更を保存します。

5. 以前に一時 afdverify サブドメイン CNAME レコードを作成している場合は、それを削除します。 

6. このカスタム ドメインを運用環境で初めて使用する場合は、「[カスタム ドメインを Front Door に関連付ける](#associate-the-custom-domain-with-your-front-door)」および「[カスタム ドメインを確認する](#verify-the-custom-domain)」の手順に従います。

たとえば、GoDaddy ドメイン レジストラーの場合の手順は次のとおりです。

1. サインインし、使用するカスタム ドメインを選択します。

2. [Domains]\(ドメイン\) セクションで **[Manage All]\(すべてを管理\)** を選択し、次に **[DNS]**  |  **[Manage Zones]\(ゾーンを管理\)** を選択します。

3. **[Domain Name]\(ドメイン名\)** にカスタム ドメインを入力し、 **[Search]\(検索\)** を選択します。

4. **[DNS Management]\(DNS 管理\)** ページで **[Add]\(追加\)** を選択し、 **[Type]\(タイプ\)** リストで **[CNAME]** を選択します。

5. CNAME エントリのフィールドに入力します。

    - 型: *[CNAME]* を選択したままにします。

    - [Host]\(ホスト\):使用するカスタム ドメインのサブドメインを入力します。 たとえば、www または profile とします。

    - [Points to]\(ポイント先\):Front Door の既定のホスト名を入力します。 たとえば、contoso.azurefd.net などです。 

    - TTL: *[one Hour]\(1 時間\)* を選択したままにします。

6. **[保存]** を選択します。
 
    CNAME エントリが DNS レコード テーブルに追加されます。

7. afdverify CNAME レコードがある場合は、その横の鉛筆アイコンを選択し、ごみ箱アイコンを選択します。

8. **[Delete]\(削除\)** を選択して CNAME レコードを削除します。


## <a name="clean-up-resources"></a>リソースをクリーンアップする

上記の手順では、カスタム ドメインを Front Door に追加しました。 Front Door をカスタム ドメインに関連付けておく必要がない場合は、次の手順を実行してカスタム ドメインを削除できます。
 
1. Front Door デザイナーで、削除するカスタム ドメインを選択します。

2. カスタム ドメインのコンテキスト メニューから [削除] をクリックします。  

   カスタム ドメインとエンドポイントの関連付けが解除されます。


## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

* CNAME DNS レコードを作成します。
* カスタム ドメインを Front Door と関連付けます。
* カスタム ドメインを確認します。

カスタム ドメインの HTTPS を有効にする方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [カスタム ドメインに対する HTTPS の有効化](front-door-custom-domain-https.md)