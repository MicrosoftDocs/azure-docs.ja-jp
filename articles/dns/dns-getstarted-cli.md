---
title: クイック スタート - Azure CLI を使用して Azure DNS ゾーンとレコードを作成する
description: クイック スタート - Azure DNS で DNS ゾーンとレコードを作成する方法について説明します。 これは、Azure CLI を使用して最初の DNS ゾーンとレコードを作成して管理するためのステップバイステップ ガイドです。
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 3/11/2019
ms.author: victorh
ms.openlocfilehash: 7a2c300e30050e7e46a2b2c724258539df85e410
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2019
ms.locfileid: "66111332"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-cli"></a>クイック スタート: Azure CLI を使用して Azure DNS ゾーンとレコードを作成する

この記事では、Windows、Mac、Linux で使用できる Azure CLI を使用して、最初の DNS ゾーンとレコードを作成する手順について説明します。 これらの手順は、[Azure portal](dns-getstarted-portal.md) または [Azure PowerShell](dns-getstarted-powershell.md) を使用して実行することもできます。

DNS ゾーンは、特定のドメインの DNS レコードをホストするために使用されます。 Azure DNS でドメインのホストを開始するには、そのドメイン名用に DNS ゾーンを作成する必要があります。 ドメインの DNS レコードはすべて、この DNS ゾーン内に作成されます。 最後に、DNS ゾーンをインターネットに公開するには、ドメインのネーム サーバーを構成する必要があります。 ここでは、その手順について説明します。

Azure DNS では、プライベート DNS ゾーンもサポートされるようになりました (現在はパブリック プレビュー段階です)。 プライベート DNS ゾーンの詳細については、「[Using Azure DNS for private domains (プライベート ドメインでの Azure DNS の使用)](private-dns-overview.md)」をご覧ください。 プライベート DNS ゾーンを作成する例については、[CLI で Azure DNS プライベート ゾーンを使用するための基礎](./private-dns-getstarted-cli.md)に関するページを参照してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-the-resource-group"></a>リソース グループの作成

DNS ゾーンを作成する前に、DNS ゾーンが含まれるリソース グループを作成します。

```azurecli
az group create --name MyResourceGroup --location "East US"
```

## <a name="create-a-dns-zone"></a>DNS ゾーンの作成

DNS ゾーンは、`az network dns zone create` コマンドを使用して作成します。 このコマンドのヘルプを表示するには、「`az network dns zone create -h`」と入力します。

次の例では、*MyResourceGroup* というリソース グループに *contoso.xyz* という DNS ゾーンを作成します。 この例の値を実際の値に置き換えて、DNS ゾーンを作成できます。

```azurecli
az network dns zone create -g MyResourceGroup -n contoso.xyz
```

## <a name="create-a-dns-record"></a>DNS レコードの作成

DNS レコードを作成するには、`az network dns record-set [record type] add-record` コマンドを使用します。 A レコードのヘルプ情報については、`azure network dns record-set A add-record -h` を参照してください。

下の例では、リソース グループ "MyResourceGroup" で DNS ゾーン "contoso.xyz" に相対名 "www" を持つレコードを作成します。 レコード セットの完全修飾名は、"www.contoso.xyz" になります。 レコードの種類は "A"、IP アドレスは "10.10.10.10"、既定の TTL として 3,600 秒 (1 時間) が使用されます。

```azurecli
az network dns record-set a add-record -g MyResourceGroup -z contoso.xyz -n www -a 10.10.10.10
```

## <a name="view-records"></a>レコードの表示

ゾーン内の DNS レコードを一覧表示するには、次のコマンドを実行します。

```azurecli
az network dns record-set list -g MyResourceGroup -z contoso.xyz
```

## <a name="test-the-name-resolution"></a>名前解決をテストする

テスト DNS ゾーンを作成し、その中にテスト "A" レコードを含めたので、*nslookup* という名前のツールを使用して、名前解決をテストできます。 

**DNS 名前解決をテストするには:**

1. 次のコマンドレットを実行して、ゾーン用のネーム サーバーの一覧を取得します。

   ```azurecli
   az network dns record-set ns show --resource-group MyResourceGroup --zone-name contoso.xyz --name @
   ```

1. 前の手順の出力から、いずれかのネーム サーバーの名前をコピーします。

1. コマンド プロンプトを開いて、次のコマンドを実行します。

   ```
   nslookup www.contoso.xyz <name server name>
   ```

   例:

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   次のような画面が表示されます。

   ![nslookup](media/dns-getstarted-portal/nslookup.PNG)

ホスト名 **www\.contoso.xyz** は、構成したとおり、**10.10.10.10** に名前解決されています。 この結果で、名前解決が正常に機能していることを確認できます。

## <a name="delete-all-resources"></a>すべてのリソースの削除

不要になった場合、リソース グループを削除することで、このクイック スタートで作成したすべてのリソースを削除できます。

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>次の手順

これで、Azure CLI を使用して最初の DNS ゾーンとレコードを作成できました。カスタム ドメインで Web アプリのレコードを作成できます。

> [!div class="nextstepaction"]
> [カスタム ドメインにおける Web アプリの DNS レコードの作成](./dns-web-sites-custom-domain.md)
