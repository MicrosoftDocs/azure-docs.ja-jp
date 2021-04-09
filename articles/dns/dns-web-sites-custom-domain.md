---
title: チュートリアル - Web アプリのカスタム Azure DNS レコードの作成
description: このチュートリアルでは、Azure DNS を使用して Web アプリのカスタム ドメイン DNS レコードを作成します。
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: rohink
ms.openlocfilehash: 369c7dab174f0269797b10635882a6821ade8311
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94952915"
---
# <a name="tutorial-create-dns-records-in-a-custom-domain-for-a-web-app"></a>チュートリアル: カスタム ドメインにおける Web アプリの DNS レコードの作成 

Azure DNS を構成して、Web アプリ用にカスタム ドメインをホストすることができます。 たとえば、Azure Web アプリを作成し、www\.contoso.com または contoso.com を完全修飾ドメイン名 (FQDN) として使用してユーザーがアクセスできるようにすることができます。

> [!NOTE]
> このチュートリアルでは、contoso.com を例として使用します。 contoso.com を独自のドメイン名に置き換えてください。

これを行うには、3 つのレコードを作成する必要があります。

* contoso.com を指すルート "A" レコード
* 検証用のルート "TXT" レコード
* その A レコードを指す www 名の "CNAME" レコード

Azure の Web アプリ用に A レコードを作成する場合、Web アプリの基になる IP アドレスが変更されると、A レコードを手動で更新する必要があることに注意してください。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * カスタム ドメインの A および TXT レコードの作成
> * カスタム ドメインの CNAME レコードの作成
> * 新しいレコードのテスト
> * カスタム ホスト名の Web アプリへの追加
> * カスタム ホスト名のテスト

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Azure DNS 内でホストすることができ、テストに使用できるドメイン名が必要です。 このドメインに対するフル コントロールが必要となります。 フル コントロールには、このドメインのネーム サーバー (NS) レコードを設定する権限が含まれます。
* [App Service アプリを作成する](../app-service/quickstart-html.md)か、別のチュートリアルで作成したアプリを使用します。

* Azure DNS に DNS ゾーンを作成し、レジストラーのゾーンを Azure DNS に委任します。

   1. DNS ゾーンを作成するには、「 [DNS ゾーンの作成](./dns-getstarted-powershell.md)」の手順に従います。
   2. ゾーンを Azure DNS に委任するには、[DNS ドメインの委任](dns-delegate-domain-azure-dns.md)に関する記事の手順に従います。

ゾーンを作成し、それを Azure DNS に委任したら、カスタム ドメインのレコードを作成できます。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-a-record-and-txt-record"></a>A レコードと TXT レコードの作成

A レコードは、名前をその IP アドレスに対応付けるために使用されます。 次の例では、Web アプリの IPv4 アドレスを使用して "\@" を A レコードとして割り当てます。 通常、\@ はルート ドメインを表します。

### <a name="get-the-ipv4-address"></a>IPv4 アドレスの取得

Azure portal の App Services ページの左側のナビゲーションで、 **[カスタム ドメイン]** を選択します。 

![[カスタム ドメイン] メニュー](../app-service/./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

**[カスタム ドメイン]** ページで、アプリの IPv4 アドレスをコピーします。

![Azure アプリへのポータル ナビゲーション](../app-service/./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="create-the-a-record"></a>A レコードを作成する

```azurepowershell
New-AzDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" `
 -ResourceGroupName "MyAzureResourceGroup" -Ttl 600 `
 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "<your web app IP address>")
```

### <a name="create-the-txt-record"></a>TXT レコードの作成

App Services は、このレコードを、カスタム ドメインの所有者であることを検証するために構成時にのみ使用します。 App Service でカスタム ドメインが検証されて構成された後は、この TXT レコードを削除できます。

> [!NOTE]
> ドメイン名を確認し、運用環境トラフィックを Web アプリにルーティングしない場合は、この検証手順に TXT レコードを指定するだけで済みます。  検証には、TXT レコードのほかに A レコードや CNAME レコードを追加する必要はありません。

```azurepowershell
New-AzDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup `
 -Name "@" -RecordType "txt" -Ttl 600 `
 -DnsRecords (New-AzDnsRecordConfig -Value  "contoso.azurewebsites.net")
```

## <a name="create-the-cname-record"></a>CNAME レコードを作成する

ドメインが既に Azure DNS で管理されている場合 ( [DNS ドメインの委任](dns-domain-delegation.md)に関するページを参照)、次の例を使用して contoso.azurewebsites.net の CNAME レコードを作成できます。

Azure PowerShell を開き、新しい CNAME レコードを作成します。 この例では、Web アプリの別名 contoso.azurewebsites.net を使用して、"contoso.com" という名前の DNS ゾーンに 600 秒の "Time to Live" でレコード セット型 CNAME を作成します。

### <a name="create-the-record"></a>レコードの作成

```azurepowershell
New-AzDnsRecordSet -ZoneName contoso.com -ResourceGroupName "MyAzureResourceGroup" `
 -Name "www" -RecordType "CNAME" -Ttl 600 `
 -DnsRecords (New-AzDnsRecordConfig -cname "contoso.azurewebsites.net")
```

次の例は応答です。

```
    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {contoso.azurewebsites.net}
    Tags              : {}
```

## <a name="test-the-new-records"></a>新しいレコードのテスト

次のように nslookup を使用して "www.contoso.com" と "contoso.com" にクエリを実行することによって、レコードが正しく作成されたかどうかを検証できます。

```
PS C:\> nslookup
Default Server:  Default
Address:  192.168.0.1

> www.contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    <instance of web app service>.cloudapp.net
Address:  <ip of web app service>
Aliases:  www.contoso.com
contoso.azurewebsites.net
<instance of web app service>.vip.azurewebsites.windows.net

> contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    contoso.com
Address:  <ip of web app service>

> set type=txt
> contoso.com

Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
contoso.com text =

        "contoso.azurewebsites.net"
```
## <a name="add-custom-host-names"></a>カスタム ホスト名の追加

ここで、Web アプリにカスタム ホスト名を追加できます。

```azurepowershell
set-AzWebApp `
 -Name contoso `
 -ResourceGroupName MyAzureResourceGroup `
 -HostNames @("contoso.com","www.contoso.com","contoso.azurewebsites.net")
```
## <a name="test-the-custom-host-names"></a>カスタム ホスト名のテスト

ブラウザーを開き、`http://www.<your domainname>` と `http://<you domain name>` を参照します。

> [!NOTE]
> 必ず、`http://` プレフィックスを含めます。そうしないと、ブラウザーが URL の予測を試みることがあります。

両方の URL で同じページが表示されます。 次に例を示します。

![Contoso アプリ サービス](media/dns-web-sites-custom-domain/contoso-app-svc.png)


## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルで作成したリソースが不要になったら、**myresourcegroup** リソース グループを削除できます。

## <a name="next-steps"></a>次のステップ

Azure DNS プライベート ゾーンを作成する方法について学習します。

> [!div class="nextstepaction"]
> [PowerShell で Azure DNS プライベート ゾーンの使用を開始する](private-dns-getstarted-powershell.md)