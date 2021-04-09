---
title: クイック スタート:Azure DNS ゾーンとレコードを作成する - Azure CLI
titleSuffix: Azure DNS
description: クイック スタート - Azure DNS で DNS ゾーンとレコードを作成する方法について説明します。 これは、Azure CLI を使用して最初の DNS ゾーンとレコードを作成して管理するためのステップバイステップ ガイドです。
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: rohink
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1929cd512d18d7fd234aff1f55814c423455e63b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94561371"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-azure-cli"></a>クイック スタート:Azure CLI を使用して Azure DNS ゾーンとレコードを作成する

この記事では、Windows、Mac、Linux で使用できる Azure CLI を使用して、最初の DNS ゾーンとレコードを作成する手順について説明します。 これらの手順は、[Azure portal](dns-getstarted-portal.md) または [Azure PowerShell](dns-getstarted-powershell.md) を使用して実行することもできます。

DNS ゾーンは、特定のドメインの DNS レコードをホストするために使用されます。 Azure DNS でドメインのホストを開始するには、そのドメイン名用に DNS ゾーンを作成する必要があります。 ドメインの DNS レコードはすべて、この DNS ゾーン内に作成されます。 最後に、DNS ゾーンをインターネットに公開するには、ドメインのネーム サーバーを構成する必要があります。 ここでは、その手順について説明します。

Azure DNS は、プライベート DNS ゾーンもサポートします。 プライベート DNS ゾーンの詳細については、「[Using Azure DNS for private domains (プライベート ドメインでの Azure DNS の使用)](private-dns-overview.md)」をご覧ください。 プライベート DNS ゾーンを作成する例については、[CLI で Azure DNS プライベート ゾーンを使用するための基礎](./private-dns-getstarted-cli.md)に関するページを参照してください。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0.4 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

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

   次に例を示します。

   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```

   次のような画面が表示されます。

   ![n s lookup コマンドと、Server、Address、Name、Address の値を表示するコマンド プロンプト ウィンドウを示すスクリーンショット。](media/dns-getstarted-portal/nslookup.PNG)

ホスト名 **www\.contoso.xyz** は、構成したとおり、**10.10.10.10** に名前解決されています。 この結果で、名前解決が正常に機能していることを確認できます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になった場合、リソース グループを削除することで、このクイック スタートで作成したすべてのリソースを削除できます。

```azurecli
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>次のステップ

これで、Azure CLI を使用して最初の DNS ゾーンとレコードを作成できました。カスタム ドメインで Web アプリのレコードを作成できます。

> [!div class="nextstepaction"]
> [カスタム ドメインにおける Web アプリの DNS レコードの作成](./dns-web-sites-custom-domain.md)
