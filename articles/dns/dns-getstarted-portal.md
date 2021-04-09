---
title: クイック スタート:DNS ゾーンとレコードの作成 - Azure portal
titleSuffix: Azure DNS
description: このステップごとのクイック スタート ガイドでは、Azure portal を使用して Azure DNS ゾーンおよびレコードを作成する方法を学びます。
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: rohink
ms.openlocfilehash: be76a7902c2747c7a8dc1bfc21d58ae88f3ff343
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92282151"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-the-azure-portal"></a>クイック スタート:Azure portal を使用して Azure DNS ゾーンおよびレコードを作成する

パブリック ドメイン内のホスト名を解決するように Azure DNS を構成できます。 たとえば、*contoso.xyz* というドメイン名をドメイン名レジストラーから購入した場合、*contoso.xyz* ドメインをホストし、 *`www.contoso.xyz`* を Web サーバーまたは Web アプリの IP アドレスに解決するように Azure DNS を構成できます。

このクイック スタートでは、テスト ドメインを作成し、*www* を IP アドレス *10.10.10.10* に解決するアドレス レコードを作成します。

>[!IMPORTANT]
>このクイック スタートのすべての名前と IP アドレスは例であり、実際のシナリオに対応するものではありません。

<!---
You can also perform these steps using [Azure PowerShell](dns-getstarted-powershell.md) or the cross-platform [Azure CLI](dns-getstarted-cli.md).
--->

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

ポータルでのすべての手順では、[Azure portal](https://portal.azure.com) にサインインします。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

Azure アカウントで [Azure Portal](https://portal.azure.com) にサインインします。

## <a name="create-a-dns-zone"></a>DNS ゾーンの作成

DNS ゾーンにはドメインの DNS エントリが含まれています。 Azure DNS でドメインのホストを開始するには、そのドメイン名用に DNS ゾーンを作成します。 

**DNS ゾーンを作成するには:**

1. 左上で、 **[リソースの作成]** 、 **[ネットワーク]** 、 **[DNS ゾーン]** の順に選択します。

1. **[DNS ゾーンの作成]** ページで、以下の値を入力または選択します。

   - **Name**:このクイック スタートのサンプルでは「*contoso.xyz*」と入力します。 DNS ゾーン名には、Azure DNS サーバーに既に構成されていない任意の値を指定できます。 実際の値は、ドメイン名レジストラーから購入したドメインになります。
   - **[リソース グループ]** : **[新規作成]** を選択し、「*MyResourceGroup*」と入力して、 **[OK]** を選択します。 Azure サブスクリプション内で一意となるリソース グループ名を使用してください。 

1. **［作成］** を選択します

   ![[DNS ゾーン]](./media/dns-getstarted-portal/openzone650.png)

ゾーンの作成には数分かかることがあります。

## <a name="create-a-dns-record"></a>DNS レコードの作成

DNS ゾーン内にドメインの DNS エントリまたはレコードを作成します。 ホスト名を IPv4 アドレスに解決する新しいアドレス レコードつまり "A" レコードを作成します。

**"A" レコードを作成するには:**

1. Azure portal の **[すべてのリソース]** の下で、**MyResourceGroup** リソース グループの **contoso.xyz** DNS ゾーンを開きます。 **[名前でフィルター]** ボックスに「*contoso.xyz*」を入力すると簡単に見つけることができます。

1. **[DNS ゾーン]** ページの上部にある **[+ レコード セット]** を選択します。

1. **[レコード セットの追加]** ページで、以下の値を入力または選択します。

   - **Name**:「*www*」と入力します。 このレコード名は、特定の IP アドレスに解決するホスト名です。
   - **[種類]** : **[A]** を選択します。"A" レコードが最もよく使用されますが、他の種類のレコードもあります。たとえば、メール サーバーの場合は "MX"、IP v6 アドレスの場合は "AAAA" です。 
   - **TTL**: 「*1*」と入力します。 DNS 要求の *Time-to-live* は、DNS サーバーおよびクライアントが応答をキャッシュに入れておくことができる時間を指定します。
   - **TTL の単位**: **[時間]** を選択します。 これは、 **[TTL]** 値の既定の時間単位です。 
   - **IP アドレス**: このクイック スタートの例では、「*10.10.10.10*」と入力します。 この値は、レコード名が解決される IP アドレスです。 実際の例では、Web サーバーのパブリック IP アドレスを入力します。

このクイック スタートは簡単なテストを目的としているため、ドメイン名レジストラーで Azure DNS ネーム サーバーを構成する必要はありません。 実際の運用ドメインの場合は、Web サーバーまたはアプリに接続するために、インターネット上のだれかにホスト名を解決してもらう必要があります。 ドメイン名レジストラーにアクセスして、ネーム サーバー レコードを Azure DNS ネーム サーバーで置き換えてもらいます。 詳細については、「[チュートリアル:Azure DNS でドメインをホストする](dns-delegate-domain-azure-dns.md#delegate-the-domain)」を参照してください。

## <a name="test-the-name-resolution"></a>名前解決をテストする

テスト DNS ゾーンを作成し、その中にテスト "A" レコードを含めたので、*nslookup* という名前のツールを使用して、名前解決をテストできます。 

**DNS 名前解決をテストするには:**

1. Azure portal の **[すべてのリソース]** の下で、**MyResourceGroup** リソース グループの **contoso.xyz** DNS ゾーンを開きます。 **[名前でフィルター]** ボックスに「*contoso.xyz*」を入力すると簡単に見つけることができます。

1. **[概要]** ページのネーム サーバー一覧からネーム サーバー名の 1 つをコピーします。 

   ![ゾーン](./media/dns-getstarted-portal/viewzonens500.png)

1. コマンド プロンプトを開いて、次のコマンドを実行します。

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   次に例を示します。

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   次のような画面が表示されます。

   ![n s lookup コマンドと、Server、Address、Name、Address の値を表示するコマンド プロンプト ウィンドウを示すスクリーンショット。](media/dns-getstarted-portal/nslookup.PNG)

ホスト名 **www\.contoso.xyz** は、構成したとおり、**10.10.10.10** に名前解決されています。 この結果で、名前解決が正常に機能していることを確認できます。 

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイック スタートで作成したリソースが必要なくなったら、**MyResourceGroup** リソース グループを削除することで削除できます。 **MyResourceGroup** リソース グループを開き、 **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [カスタム ドメインにおける Web アプリの DNS レコードの作成](./dns-web-sites-custom-domain.md)
