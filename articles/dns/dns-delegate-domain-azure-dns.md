---
title: チュートリアル - Azure DNS でドメインとサブドメインをホストする
description: このチュートリアルでは、Azure DNS を構成して DNS ゾーンをホストする方法を示します。
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: c0c5c5fe899c9b9b898973a88c7dac4256959ee4
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57779778"
---
# <a name="tutorial-host-your-domain-in-azure-dns"></a>チュートリアル:Azure DNS でドメインをホストする

Azure DNS を使用して DNS ドメインをホストし、DNS レコードを管理することができます。 Azure でドメインをホストすることで、その他の Azure サービスと同じ資格情報、API、ツール、課金情報を使用して DNS レコードを管理できます。

ドメイン contoso.net をドメイン名登録業者から購入し、その後 Azure DNS で contoso.net という名前のゾーンを作成するとします。 自分がドメインの所有者であるので、ドメインのネーム サーバー (NS) レコードを構成するオプションが登録業者から提供されます。 登録業者は NS レコードを .net の親ゾーンに格納します。 インターネット ユーザーが世界のどこにいても、contoso.net 内の DNS レコードを解決しようとした時点で、Azure DNS ゾーン内のドメインに転送されます。


このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * DNS ゾーンの作成。
> * ネーム サーバーの一覧の取得。
> * ドメインの委任。
> * 委任が機能していることの確認。


Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="prerequisites"></a>前提条件

Azure DNS 内でホストすることができ、テストに使用できるドメイン名が必要です。 このドメインに対するフル コントロールが必要となります。 フル コントロールには、このドメインのネーム サーバー (NS) レコードを設定する権限が含まれます。

このチュートリアルで使用するドメインの例は contoso.net ですが、独自のドメイン名を使用してください。

## <a name="create-a-dns-zone"></a>DNS ゾーンの作成

1. Azure ポータルにサインインします。
1. 左上で、**[リソースの作成]** > **[ネットワーキング]** > **[DNS ゾーン]** の順に選択し、**[DNS ゾーンの作成]** ページを開きます。

   ![[DNS ゾーン]](./media/dns-delegate-domain-azure-dns/openzone650.png)

1. **[DNS ゾーンの作成]** ページで次の値を入力してから、**[作成]** を選択します。

   | **設定** | **値** | **詳細** |
   |---|---|---|
   |**Name**|[ドメイン名] |購入したドメイン名。 このチュートリアルでは、例として contoso.net を使用します。|
   |**サブスクリプション**|<該当するサブスクリプション>|ゾーンの作成先となるサブスクリプションを選択します。|
   |**リソース グループ**|**[新規作成]**: contosoRG|リソース グループを作成します。 選択したサブスクリプション内で一意となるリソース グループ名を使用してください。<br>リソース グループの場所は、DNS ゾーンには影響しません。 DNS ゾーンの場所は常に "グローバル" であり、それは表示されません。|
   |**場所**|米国東部||

## <a name="retrieve-name-servers"></a>ネーム サーバーの取得

DNS ゾーンを Azure DNS に委任するには、ゾーンのネーム サーバーを把握する必要があります。 Azure DNS は、ゾーンが作成されるたびに、プールからネーム サーバーを割り当てます。

1. DNS ゾーンが作成されたら、Azure Portal の **[お気に入り]** ウィンドウで **[すべてのリソース]** を選択します。 **[すべてのリソース]** ページで DNS ゾーンを選択します。 選択したサブスクリプションに既存のリソースがいくつかある場合は、**[名前でフィルター]** ボックスに自身のドメイン名を入力すると、アプリケーション ゲートウェイに簡単にアクセスできます。 

1. [DNS ゾーン] ページでネーム サーバーを取得します。 この例では、ゾーン contoso.net に、ネーム サーバー *ns1-01.azure-dns.com*、*ns2-01.azure-dns.net*、*ns3-01.azure-dns.org*、*ns4-01.azure-dns.info* が割り当てられています。

   ![ネーム サーバーの一覧](./media/dns-delegate-domain-azure-dns/viewzonens500.png)

割り当てられたネーム サーバーのゾーンに、権限のある NS レコードが Azure DNS によって自動的に作成されます。

## <a name="delegate-the-domain"></a>ドメインの委任

DNS ゾーンを作成してネーム サーバーを取得したところで、Azure DNS ネーム サーバーを使用して親ドメインを更新する必要があります。 各レジストラーは独自の DNS 管理ツールを所有していて、ドメインのネーム サーバー レコードを変更します。 

1. 登録業者の DNS 管理ページで、NS レコードを編集し、その NS レコードを、Azure DNS のネーム サーバーに置き換えます。

1. ドメインを Azure DNS に委任する場合、Azure DNS によって提供されるネーム サーバーを使用する必要があります。 対象のドメインの名前に関係なく、4 つのネーム サーバーすべてを使用します。 ドメインの委任では、対象のドメインと同じトップレベル ドメインがネーム サーバーに使用される必要はありません。

> [!NOTE]
> 各ネーム サーバーのアドレスをコピーするとき、アドレスの末尾にあるピリオドをコピーしてください。 末尾のピリオドは、完全修飾ドメイン名の終わりを示します。 NS 名の末尾にピリオドがない場合、一部のレジトスラーではピリオドが追加されます。 DNS RFC に準拠するには、末尾のピリオドを含めます。

独自のゾーンのネーム サーバー ("*バニティ ネーム サーバー*" と呼ばれることもあります) を使用した委任は、現在、Azure DNS ではサポートされていません。

## <a name="verify-the-delegation"></a>委任の確認

委任が完了したら、*nslookup* などのツールを使用してゾーンの Start of Authority (SOA) レコードを照会することで、それが動作していることを確認できます。 SOA レコードは、ゾーンの作成時に自動的に作成されます。 委任が完了した後、機能していることを確認する前に 10 分以上待つ必要がある場合があります。 DNS システムに変更が反映されるまで少し時間がかかる場合があります。

Azure DNS ネーム サーバーを指定する必要はありません。 委任が正しく設定されている場合は、通常の DNS 解決プロセスで自動的にネーム サーバーが検出されます。

1. コマンド プロンプトで、次の例のような nslookup コマンドを入力します。

   ```
   nslookup -type=SOA contoso.net
   ```

1. 応答が次の nslookup 出力のようであることを確認します。

   ```
   Server: ns1-04.azure-dns.com
   Address: 208.76.47.4

   contoso.net
   primary name server = ns1-04.azure-dns.com
   responsible mail addr = msnhst.microsoft.com
   serial = 1
   refresh = 900 (15 mins)
   retry = 300 (5 mins)
   expire = 604800 (7 days)
   default TTL = 300 (5 mins)
   ```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

次のチュートリアルに進む場合は、**contosoRG** リソース グループをそのまま保持できます。 それ以外の場合は、**contosoRG** リソース グループを削除してこのチュートリアルで作成したリソースを削除します。

- **contosoRG** リソース グループを選択し、**[リソース グループの削除]** を選択します。 

## <a name="next-steps"></a>次の手順

このチュートリアルでは、ドメインの DNS ゾーンを作成し、それを Azure DNS に委任しました。 Azure DNS と Web アプリについて詳しくは、Web アプリのチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [カスタム ドメインにおける Web アプリの DNS レコードの作成](./dns-web-sites-custom-domain.md)