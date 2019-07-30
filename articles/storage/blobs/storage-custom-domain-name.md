---
title: Azure Storage アカウントのカスタム ドメイン名の構成 | Microsoft Docs
description: Azure portal を使用して、独自の正規名 (CNAME) を Azure Storage アカウントの Blob ストレージまたは Web エンドポイントにマッピングします。
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 06/26/2018
ms.author: normesta
ms.reviewer: seguler
ms.subservice: blobs
ms.openlocfilehash: c0fb3551b14a2239f26c54f639b90400277fab25
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67501935"
---
# <a name="configure-a-custom-domain-name-for-your-azure-storage-account"></a>Azure Storage アカウントのカスタム ドメイン名の構成

Azure ストレージ アカウントの BLOB データにアクセスするためのカスタム ドメインを構成できます。 Azure Blob Storage の既定のエンドポイントは *\<storage-account-name>.blob.core.windows.net* です。 また、[静的な Web サイト機能](storage-blob-static-website.md)の一部として生成された Web エンドポイントを使用することもできます。 *www\.contoso.com* などのカスタム ドメインおよびサブドメインをストレージ アカウントの BLOB エンドポイントまたは Web エンドポイントにマッピングしている場合、ユーザーはそのドメインを使って、ストレージ アカウントの BLOB データにアクセスできます。

> [!IMPORTANT]
> Azure Storage では、カスタム ドメインでの HTTPS はまだネイティブでサポートされていません。 現在、[HTTPS 経由でカスタム ドメインを使用して Azure CDN から BLOB にアクセスする](storage-https-custom-domain-cdn.md)ことができます。
> 
> 
> [!NOTE]
> 現在、ストレージ アカウントでは、アカウントあたり 1 つのカスタム ドメインしかサポートされていません。 Web サービス エンドポイントと Blob service エンドポイントの両方にカスタム ドメイン名をマッピングすることはできません。
> 
> [!NOTE]
> このマッピングは、サブドメイン (たとえば、www\.contoso.com) でのみ機能します。 Web エンドポイントをルート ドメイン (たとえば、contoso.com) で使用可能にしたい場合は、[カスタム ドメインをもつ Azure CDN を使用する](storage-https-custom-domain-cdn.md)必要があります。

次の表は、*mystorageaccount* というストレージ アカウントにある BLOB データのサンプル URL を示します。 ストレージ アカウントに登録されているカスタム ドメインは *www\.contoso.com* です。

| リソースの種類 | 既定の URL | カスタム ドメイン URL |
| --- | --- | --- |
| ストレージ アカウント | http:\//mystorageaccount.blob.core.windows.net | http:\//www.contoso.com |
| Blob |http:\//mystorageaccount.blob.core.windows.net/mycontainer/myblob | http:\//www.contoso.com/mycontainer/myblob |
| ルート コンテナー | http:\//mystorageaccount.blob.core.windows.net/myblob または http:\//mystorageaccount.blob.core.windows.net/$root/myblob | http:\//www.contoso.com/myblob または http:\//www.contoso.com/$root/myblob |
| Web |  http:\//mystorageaccount.[zone].web.core.windows.net/$web/[indexdoc] または http:\//mystorageaccount.[zone].web.core.windows.net/[indexdoc] または http:\//mystorageaccount.[zone].web.core.windows.net/$web または http:\//mystorageaccount.[zone].web.core.windows.net/ | http:\//www.contoso.com/$web または http:\//www.contoso.com/ または http:\//www.contoso.com/$web/[indexdoc] または http:\//www.contoso.com/[indexdoc] |

> [!NOTE]  
> 以下のセクションに示すように、Blob service エンドポイントの例はすべて、Web サービス エンドポイントにも適用されます。

## <a name="direct-vs-intermediary-cname-mapping"></a>直接 CNAME マッピングと中間 CNAME マッピング

次の 2 つの方法のいずれかを使用して、サブドメインのプレフィックスが付いたカスタム ドメイン (例、www\.contoso.com) にストレージ アカウントの BLOB エンドポイントを参照させることができます。 
* 直接 CNAME マッピングを使用します。
* *asverify* 中間サブドメインを使用します。

### <a name="direct-cname-mapping"></a>直接 CNAME マッピング

最も簡単な一番の方法は、カスタム ドメインおよびサブドメインを BLOB エンドポイントに直接マッピングする正規名 (CNAME) レコードを作成する方法です。 CNAME レコードは、ソース ドメインを宛先ドメインにマッピングするドメイン ネーム システム (DNS) 機能です。 この例で、ソース ドメインは、独自のカスタム ドメインおよびサブドメイン (*www\.contoso.com* など) です。 宛先ドメインは、Blob service エンドポイント (*mystorageaccount.blob.core.windows.net* など) です。

直接の方法については、「カスタム ドメインの登録」セクションを参照してください。

### <a name="intermediary-mapping-with-asverify"></a>*asverify* を使用した中間マッピング

2 番目の方法も、CNAME レコードを使用します。 ただし、ダウンタイムを避けるために、まず Azure によって認識される特殊なサブドメイン *asverify* を採用します。

カスタム ドメインを BLOB エンドポイントにマッピングすると、[Azure portal](https://portal.azure.com) でのドメインの登録中に短時間のダウンタイムが発生する可能性があります。 ドメインが現在、ダウンタイムが発生しないことを必要とするサービス レベル アグリーメント (SLA) があるアプリケーションをサポートしている場合は、Azure *asverify* サブドメインを中間登録ステップとして使用できます。 このステップにより、ユーザーは、DNS マッピングの実行中、ドメインにアクセスすることができます。

中間の方法については、「*asverify* サブドメインを使用したカスタム ドメインの登録」を参照してください。

## <a name="register-a-custom-domain"></a>カスタム ドメインの登録
次のステートメントが当てはまる場合、このセクションの手順を使用して、ドメインを登録します。
* ユーザーがドメインに短時間使用できなくても構わない。
* カスタム ドメインでは、現在アプリケーションをホストしていない。 

Azure DNS を使用して、Azure BLOB のカスタム DNS 名を構成できます。 詳細については、「[Azure DNS を使用して Azure サービス用のカスタム ドメイン設定を提供する](https://docs.microsoft.com/azure/dns/dns-custom-domain#blob-storage)」をご覧ください。

カスタム ドメインが現在、いかなるダウンタイムの発生も許容されないアプリケーションをサポートしている場合は、「*asverify* サブドメインを使用したカスタム ドメインの登録」にある手順を使用してください。

カスタム ドメイン名を構成するには、DNS で新しい CNAME レコードを作成します。 CNAME レコードによって、ドメイン名のエイリアスが指定されます。 この例では、カスタム ドメインのアドレスが、ストレージ アカウントの Blob ストレージ エンドポイントにマッピングされます。

通常、ドメインの DNS 設定は、ドメイン レジストラーの Web サイトで管理できます。 それぞれのレジストラーでは CNAME レコードを指定するための同様の (多少異なる) 手段が用意されていますが、その概念は同じです。 一部の基本ドメイン登録パッケージには DNS 構成が用意されていないため、CNAME レコードを作成するには、事前にドメイン登録パッケージのアップグレードが必要である可能性があります。

1. [Azure portal](https://portal.azure.com) で、ストレージ アカウントに移動します。

1. メニュー ウィンドウの **[Blob service]** で、 **[カスタム ドメイン]** を選択します。  
   **[カスタム ドメイン]** ウィンドウが開きます。

1. ドメイン レジストラーの Web サイトにサインインし、DNS の管理ページに移動します。  
   このページは、**Domain Name**、**DNS**、または **Name Server Management** というセクションにある可能性があります。

1. CNAME 管理セクションを見つけます。  
   詳細設定ページに進み、**CNAME**、**Alias**、または **Subdomains** などを見つける必要があります。

1. 新しい CNAME レコードを作成し、**www** や **photos** などのサブドメイン別名を入力して (サブドメインは必須で、ルート ドメインはサポートされていません)、ホスト名を指定します。  
   ホスト名は BLOB サービス エンドポイントです。 その形式は *\<mystorageaccount>.blob.core.windows.net* で、ここで *mystorageaccount* はストレージ アカウントの名前です。 使用するホスト名が、[Azure portal](https://portal.azure.com) の **[カスタム ドメイン]** ウィンドウの項目 1 に表示されます。 

1. **[カスタム ドメイン]** ウィンドウのテキスト ボックスに、サブドメインを含むカスタム ドメインの名前を入力します。  
   たとえば、ドメインが *contoso.com* でサブドメイン 別名が *www* の場合、「**www\.contoso.com**」と入力します。 サブドメインが *photos* の場合、「**photos.contoso.com**」と入力します。

1. カスタム ドメインを登録するには、 **[保存]** を選択します。  
   正常に登録された場合は、portal からストレージ アカウントが正常に更新されたことが通知されます。

DNS を介して新しい CNAME レコードが反映されると、ユーザーは、適切なアクセス許可があれば、カスタム ドメインを使用して BLOB データを表示できます。

## <a name="register-a-custom-domain-by-using-the-asverify-subdomain"></a>*asverify* サブドメインを使用したカスタム ドメインの登録
カスタム ドメインが現在、ダウンタイムが発生しないことが SLA で要求されているアプリケーションをサポートしている場合は、このセクションの手順を使用して、カスタム ドメインを登録します。 *asverify.\<subdomain>.\<customdomain>* から *asverify.\<storageaccount>.blob.core.windows.net* を指す CNAME を作成すると、ドメインを Azure に事前登録できます。 次に、 *\<subdomain>.\<customdomain>* から *\<storageaccount>.blob.core.windows.net* を指す、もう 1 つの CNAME を作成します。これにより、カスタム ドメインへのトラフィックが BLOB エンドポイントに転送されます。

*asverify* サブドメインは、Azure で認識される特殊なサブドメインです。 自分のサブドメインの前に *asverify* を付けると、ドメインの DNS レコードを変更する必要なく、カスタム ドメインを Azure に認識させることができます。 ドメインの DNS レコードを変更すると、ダウンタイムが発生することなく、ドメインが BLOB エンドポイントにマッピングされます。

1. [Azure portal](https://portal.azure.com) で、ストレージ アカウントに移動します。

1. メニュー ウィンドウの **[Blob service]** で、 **[カスタム ドメイン]** を選択します。  
   **[カスタム ドメイン]** ウィンドウが開きます。

1. DNS プロバイダーの Web サイトにサインインし、DNS の管理ページに移動します。  
   このページは、**Domain Name**、**DNS**、または **Name Server Management** というセクションにある可能性があります。

1. CNAME 管理セクションを見つけます。  
   詳細設定ページに進み、**CNAME**、**Alias**、または **Subdomains** などを見つける必要があります。

1. 新しい CNAME レコードを作成して、**asverify.www** や **asverify.photos** などの *asverify* サブドメインを含むサブドメインの別名を指定し、さらにホスト名を指定します。  
   ホスト名は BLOB サービス エンドポイントです。 その形式は *asverify.\<mystorageaccount>.blob.core.windows.net* で、ここで *mystorageaccount* はストレージ アカウントの名前です。 使用するホスト名が、[Azure portal](https://portal.azure.com) の *[カスタム ドメイン]* ウィンドウの項目 2 に表示されます。

1. **[カスタム ドメイン]** ウィンドウのテキスト ボックスに、サブドメインを含むカスタム ドメインの名前を入力します。  
   *asverify* は含めないでください。 たとえば、ドメインが *contoso.com* でサブドメイン 別名が *www* の場合、「**www\.contoso.com**」と入力します。 サブドメインが *photos* の場合、「**photos.contoso.com**」と入力します。

1. **[Use indirect CNAME validation]\(間接 CNAME 検証を使用する\)** チェックボックスをオンにします。

1. カスタム ドメインを登録するには、 **[保存]** を選択します。  
   正常に登録された場合は、portal からストレージ アカウントが正常に更新されたことが通知されます。 カスタム ドメインは Azure によって検証されていますが、ドメインへのトラフィックは、まだストレージ アカウントにルーティングされません。

1. DNS プロバイダーの Web サイトに戻り、サブドメインを Blob service エンドポイントにマッピングする別の CNAME レコードを作成します。  
   たとえば、サブドメインを *www* または *photos* と指定し (*asverify* を含めません)、ホスト名を *\<mystorageaccount.blob.core.windows.net* と指定します。ここで *mystorageaccount* はストレージ アカウントの名前です。 この手順で、カスタム ドメインの登録が完了します。

1. 最後に、*asverify* サブドメインを含む新しく作成した CNAME レコードを削除できます。これは、中間の手順としてのみ必要でした。

DNS を介して新しい CNAME レコードが反映されると、ユーザーは、適切なアクセス許可があれば、カスタム ドメインを使用して BLOB データを表示できます。

## <a name="test-your-custom-domain"></a>カスタム ドメインのテスト

カスタム ドメインが Blob service エンドポイントにマッピングされていることを確認するために、ストレージ アカウント内のパブリック コンテナーに BLOB を作成します。 次に、Web ブラウザーで、次の形式の URI を使って BLOB にアクセスします。`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

たとえば、*photos.contoso.com* カスタム サブドメインにある *myforms* コンテナー内の Web フォームにアクセスするには、次の URI を使用します。`http://photos.contoso.com/myforms/applicationform.htm`

## <a name="deregister-a-custom-domain"></a>カスタム ドメインの登録解除

BLOB ストレージ エンドポイントのカスタム ドメインを登録解除するには、次のいずれかの手順に従います。

### <a name="azure-portal"></a>Azure ポータル

カスタム ドメイン設定を削除するには、次の操作を行います。

1. [Azure portal](https://portal.azure.com) で、ストレージ アカウントに移動します。

1. メニュー ウィンドウの **[Blob service]** で、 **[カスタム ドメイン]** を選択します。  
   **[カスタム ドメイン]** ウィンドウが開きます。

1. カスタム ドメイン名を含むテキスト ボックスの内容を消去します。

1. **[保存]** を選択します。

カスタム ドメインが正常に削除されたら、ストレージ アカウントが正常に更新されたことを伝えるポータルの通知が表示されます。

### <a name="azure-cli"></a>Azure CLI

カスタム ドメインの登録を削除するには、[az storage account update](https://docs.microsoft.com/cli/azure/storage/account) CLI コマンドを使用して、`--custom-domain` 引数値に空の文字列 (`""`) を指定します。

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

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

カスタム ドメインの登録を削除するには、[Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) PowerShell コマンドレットを使用して、`-CustomDomainName` 引数値に空の文字列 (`""`) を指定します。

* コマンド形式:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* コマンド例:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```

## <a name="next-steps"></a>次の手順
* [Azure Content Delivery Network (CDN) エンドポイントへのカスタム ドメインのマップ](../../cdn/cdn-map-content-to-custom-domain.md)
* [Azure CDN を使用し、HTTPS 経由でカスタム ドメインを使用して BLOB にアクセスする](storage-https-custom-domain-cdn.md)
* [Azure Blob Storage での静的な Web サイトのホスティング (プレビュー)](storage-blob-static-website.md)
