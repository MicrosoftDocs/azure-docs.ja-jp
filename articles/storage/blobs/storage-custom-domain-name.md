---
title: "Azure Blob Storage エンドポイントのカスタム ドメイン名の構成 | Microsoft Docs"
description: "Azure ポータルを使用して、独自の正規名 (CNAME) を Azure Storage アカウントの BLOB ストレージ エンドポイントにマッピングします。"
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: aaafd8c5-eacb-49dc-8c8b-3f7011ad5e92
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: tamram
ms.openlocfilehash: cbc8654bf1755826afa2cf83e5476e88903e0854
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-custom-domain-name-for-your-blob-storage-endpoint"></a>BLOB ストレージ エンドポイントのカスタム ドメイン名の構成

Azure ストレージ アカウントの BLOB データにアクセスするためのカスタム ドメインを構成できます。 BLOB ストレージの既定のエンドポイントは `<storage-account-name>.blob.core.windows.net`です。 **www.contoso.com** などのカスタム ドメインおよびサブドメインをストレージ アカウントの BLOB エンドポイントにマッピングしている場合、ユーザーはそのドメインを使って、ストレージ アカウントの BLOB データにアクセスできます。

> [!IMPORTANT]
> Azure Storage では、カスタム ドメインでの HTTPS はまだネイティブでサポートされていません。 現在、[カスタム ドメインを用いた BLOB にAzure CDN から HTTPS 経由でアクセスする](storage-https-custom-domain-cdn.md)ことができます。
>

次の表は、 **mystorageaccount** というストレージ アカウントにある BLOB データのサンプル URL を示します。 ストレージ アカウントに登録されているカスタム ドメインは **www.contoso.com**です。

| リソースの種類 | 既定の URL | カスタム ドメイン URL |
| --- | --- | --- |
| ストレージ アカウント | http://mystorageaccount.blob.core.windows.net | http://www.contoso.com |
| BLOB |http://mystorageaccount.blob.core.windows.net/mycontainer/myblob | http://www.contoso.com/mycontainer/myblob |
| ルート コンテナー | http://mystorageaccount.blob.core.windows.net/myblob or http://mystorageaccount.blob.core.windows.net/$root/myblob| http://www.contoso.com/myblob または http://www.contoso.com/$root/myblob |

## <a name="direct-vs-intermediary-domain-mapping"></a>直接ドメイン マッピングと中間ドメイン マッピング

カスタム ドメインでストレージ アカウントの BLOB エンドポイントを指す方法には、直接 CNAME マッピングと、*asverify* 中間サブドメインの使用の 2 つがあります。

### <a name="direct-cname-mapping"></a>直接 CNAME マッピング

最も簡単な一番の方法は、カスタム ドメインおよびサブドメインを BLOB エンドポイントに直接マッピングする正規名 (CNAME) レコードを作成する方法です。 CNAME レコードは、ソース ドメインを宛先ドメインにマッピングするドメイン ネーム システム (DNS) 機能です。 この場合、ソース ドメインは、*www.contoso.com* などの独自のカスタム ドメインおよびサブドメインです。宛先ドメインは、*mystorageaccount.blob.core.windows.net* などの Blob service エンドポイントです。

直接マッピングの方法については、[カスタム ドメインの登録](#register-a-custom-domain)を参照してください。

### <a name="intermediary-mapping-with-asverify"></a>*asverify* を使用した中間マッピング

2 番目の方法も CNAME レコードを使用しますが、ダウンタイムを避けるために、Azure で認識される **asverify** という特殊なサブドメインを最初に使用します。

カスタム ドメインを BLOB エンドポイントにマッピングする処理によって、[Azure portal](https://portal.azure.com) でのドメインの登録中にドメインが短時間ダウンする場合があります。 カスタム ドメインが現在、ダウンタイムが発生しないことを要求するサービス レベル アグリーメント (SLA) のアプリケーションをサポートしている場合は、Azure *asverify* サブドメインを中間登録ステップとして使用できます。 この中間ステップにより、ユーザーは、DNS マッピングの実行中、ドメインにアクセスすることができます。

中間マッピングの方法については、[*asverify* サブドメインを使用したカスタム ドメインの登録](#register-a-custom-domain-using-the-asverify-subdomain)を参照してください。

## <a name="register-a-custom-domain"></a>カスタム ドメインの登録
ユーザーが短時間、ドメインを使用できなくても問題ではない場合、またはカスタム ドメインが現在、アプリケーションのホストでない場合は、この手順でカスタム ドメインを登録します。

カスタム ドメインが現在、ダウンタイムの発生が許容されないアプリケーションをサポートしている場合は、[*asverify* サブドメインを使用したカスタム ドメインの登録](#register-a-custom-domain-using-the-asverify-subdomain)で説明している手順に従ってください。

カスタム ドメイン名を構成するには、DNS で新しい CNAME レコードを作成する必要があります。 CNAME レコードによって、ドメイン名のエイリアスが指定されます。 この場合、カスタム ドメインのアドレスが、ストレージ アカウントの BLOB ストレージ エンドポイントにマッピングされます。

通常、ドメインの DNS 設定は、ドメイン レジストラーの Web サイトで管理できます。 それぞれのレジストラーでは CNAME レコードを指定するための同様の (多少異なる) 手段が用意されていますが、その概念は同じです。 一部の基本ドメイン登録パッケージには DNS 構成が用意されていないため、CNAME レコードを作成するには、事前にドメイン登録パッケージのアップグレードが必要である可能性があります。

1. [Azure Portal](https://portal.azure.com) のストレージ アカウントに移動します。
1. メニュー ブレードの "**BLOB SERVICE**" で、"**Custom domain**" を選択して "*Custom domain*" ブレードを開きます。
1. ドメイン レジストラーの Web サイトにログオンし、DNS の管理ページに移動します。 これは、"**ドメイン名**"、"**DNS**"、"**ネーム サーバー管理**" などのセクションにあります。
1. CNAME 管理セクションを見つけます。 詳細設定ページに進み、"**CNAME**"、"**エイリアス**"、"**サブドメイン**" などの語句を探します。
1. 新しい CNAME レコードを作成し、"**www**" や "**photos**" などのサブドメイン エイリアスを指定します。 次に、**mystorageaccount.blob.core.windows.net** (*mystorageaccount* はストレージ アカウントの名前です) という形式でホスト名を指定します。これは Blob service エンドポイントです。 使用するホスト名が、[Azure Portal](https://portal.azure.com) の "*Custom domain*" ブレードの項目 1 に表示されます。
1. [Azure Portal](https://portal.azure.com) の "*Custom domain*" ブレードのテキスト ボックスに、サブドメインを含むカスタム ドメインの名前を入力します。 たとえば、ドメインが **contoso.com** でサブドメイン エイリアスが **www** の場合、「**www.contoso.com**」と入力します。サブドメインが **photos** の場合、「**photos.contoso.com**」と入力します。サブドメインは*必須*です。
1. "*Custom domain*" ブレードの "**Save**" を選択して、カスタム ドメインを登録します。 正常に登録された場合は、ストレージ アカウントが正常に更新されたことを示すポータルの通知が表示されます。

DNS を介して新しい CNAME レコードが反映されると、ユーザーは、適切なアクセス許可がある限り、カスタム ドメインを使用して BLOB データを表示できます。

## <a name="register-a-custom-domain-using-the-asverify-subdomain"></a>*asverify* サブドメインを使用したカスタム ドメインの登録
カスタム ドメインが現在、ダウンタイムが発生しないことが SLA で要求されているアプリケーションをサポートしている場合は、この手順でカスタム ドメインを登録します。 `asverify.<subdomain>.<customdomain>` から `asverify.<storageaccount>.blob.core.windows.net` を指す CNAME を作成することで、ドメインを Azure に事前登録できます。 そして、`<subdomain>.<customdomain>` から `<storageaccount>.blob.core.windows.net` を指す、もう 1 つの CNAME を作成します。このポイントから、カスタム ドメインへのトラフィックが BLOB エンドポイントにダイレクトされます。

**asverify** サブドメインは、Azure で認識される特殊なサブドメインです。 自分のサブドメインの前に `asverify` を付けると、ドメインの DNS レコードを変更しなくても、カスタム ドメインが Azure に認識されます。 ドメインの DNS レコードを変更すると、ダウンタイムが発生することなく、ドメインが BLOB エンドポイントにマッピングされます。

1. [Azure Portal](https://portal.azure.com) のストレージ アカウントに移動します。
1. メニュー ブレードの "**BLOB SERVICE**" で、"**Custom domain**" を選択して "*Custom domain*" ブレードを開きます。
1. DNS プロバイダーの Web サイトにログオンし、DNS の管理ページに移動します。 これは、"**ドメイン名**"、"**DNS**"、"**ネーム サーバー管理**" などのセクションにあります。
1. CNAME 管理セクションを見つけます。 詳細設定ページに進み、"**CNAME**"、"**エイリアス**"、"**サブドメイン**" などの語句を探します。
1. 新しい CNAME レコードを作成し、*asverify* サブドメインを含むサブドメイン エイリアスを指定します。 たとえば、**asverify.www** または **asverify.photos** と指定します。 そして、**asverify.mystorageaccount.blob.core.windows.net** (**mystorageaccount** はストレージ アカウントの名前です) という形式でホスト名を指定します。これは Blob service エンドポイントです。 使用するホスト名が、[Azure Portal](https://portal.azure.com) の "*Custom domain*" ブレードの項目 2 に表示されます。
1. [Azure Portal](https://portal.azure.com) の "*Custom domain*" ブレードのテキスト ボックスに、サブドメインを含むカスタム ドメインの名前を入力します。 *asverify* は含めないでください。 たとえば、ドメインが **contoso.com** でサブドメイン エイリアスが **www** の場合、「**www.contoso.com**」と入力します。サブドメインが **photos** の場合、「**photos.contoso.com**」と入力します。サブドメインは必須です。
1. **Use indirect CNAME validation** チェックボックスをオンにします。
1. "*Custom domain*" ブレードの "**Save**" を選択して、カスタム ドメインを登録します。 正常に登録された場合は、ストレージ アカウントが正常に更新されたことを示すポータルの通知が表示されます。 この時点で、カスタム ドメインは Azure によって確認されていますが、ドメインへのトラフィックは、まだストレージ アカウントへはルーティングされません。
1. DNS プロバイダーの Web サイトに戻り、サブドメインを Blob service エンドポイントにマッピングする別の CNAME レコードを作成します。 たとえば、**www** または **photos** というサブドメインと (*asverify* は含めません)、**mystorageaccount.blob.core.windows.net** (**mystorageaccount** はストレージ アカウントの名前です) というホスト名を指定します。 この手順で、カスタム ドメインの登録が完了します。
1. 最後に、**asverify** サブドメインを含めて作成した CNAME レコードを削除します。このレコードは、中間の手順としてだけ必要であったためです。

DNS を介して新しい CNAME レコードが反映されると、ユーザーは、適切なアクセス許可がある限り、カスタム ドメインを使用して BLOB データを表示できます。

## <a name="test-your-custom-domain"></a>カスタム ドメインのテスト

カスタム ドメインが実際に Blob service エンドポイントにマッピングされていることを確認するために、ストレージ アカウントのパブリック コンテナーで BLOB を作成します。 そして、Web ブラウザーで、次の形式の URI を使って BLOB にアクセスします。

`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

たとえば、**photos.contoso.com** カスタム サブドメインにある **myforms** コンテナーの Web フォームにアクセスするには、次の URI を使用します。

`http://photos.contoso.com/myforms/applicationform.htm`

## <a name="deregister-a-custom-domain"></a>カスタム ドメインの登録解除

BLOB ストレージ エンドポイントのカスタム ドメインを登録解除するには、次のいずれかの手順に従います。

### <a name="azure-portal"></a>Azure ポータル

Azure Portal で次の手順を実行して、カスタム ドメイン設定を削除します。

1. [Azure Portal](https://portal.azure.com) のストレージ アカウントに移動します。
1. メニュー ブレードの "**BLOB SERVICE**" で、"**Custom domain**" を選択して "*Custom domain*" ブレードを開きます。
1. カスタム ドメイン名を含むテキスト ボックスの内容を消去します。
1. **[保存]** を選択します。

カスタム ドメインが正常に削除されると、ストレージ アカウントが正常に更新されたことを伝えるポータルの通知が表示されます。

### <a name="azure-cli-20"></a>Azure CLI 2.0

[az ストレージ アカウント更新](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_update) CLI コマンドを使用して、`--custom-domain` 引数値に空の文字列 (`""`) を指定し、カスタム ドメインの登録を削除します。

* コマンド形式:

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* コマンド例:

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```

### <a name="powershell"></a>PowerShell

[Set-AzureRmStorageAccount](/powershell/module/azurerm.storage/set-azurermstorageaccount) PowerShell コマンドレットを使用して、`-CustomDomainName` 引数値に空の文字列 (`""`) を指定し、カスタム ドメインの登録を削除します。

* コマンド形式:

  ```powershell
  Set-AzureRmStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* コマンド例:

  ```powershell
  Set-AzureRmStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```

## <a name="next-steps"></a>次のステップ
* [Azure Content Delivery Network (CDN) エンドポイントへのカスタム ドメインのマップ](../../cdn/cdn-map-content-to-custom-domain.md)
* [カスタム ドメインを用いた BLOB にAzure CDN から HTTPS 経由でアクセスする](storage-https-custom-domain-cdn.md)
