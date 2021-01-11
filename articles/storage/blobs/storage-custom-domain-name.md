---
title: カスタム ドメインを Azure Blob Storage エンドポイントにマップする
titleSuffix: Azure Storage
description: カスタム ドメインを Azure ストレージ アカウントの Blob Storage または Web エンドポイントにマップします。
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 01/23/2020
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 903413b6ca00600e15ac3af0a93b98a8d67a1c28
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88053628"
---
# <a name="map-a-custom-domain-to-an-azure-blob-storage-endpoint"></a>カスタム ドメインを Azure Blob Storage エンドポイントにマップする

カスタム ドメインは、BLOB サービス エンドポイントまたは[静的な Web サイト](storage-blob-static-website.md)のエンドポイントにマップできます。 

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

> [!NOTE] 
> このマッピングは、サブドメイン (例: `www.contoso.com`) に対してのみ機能します。 Web エンドポイントをルート ドメイン (例: `contoso.com`) で使用できるようにする場合、Azure CDN を使用する必要があります。 ガイダンスについては、この記事の「[HTTPS が有効になっているカスタム ドメインをマップする](#enable-https)」セクションを参照してください。 この記事のそのセクションに移動してカスタム ドメインのルート ドメインを有効にするため、そのセクション内の HTTPS を有効にする手順は省略可能です。 

<a id="enable-http"></a>

## <a name="map-a-custom-domain-with-only-http-enabled"></a>HTTP のみが有効になっているカスタム ドメインをマップする

この方法は簡単ですが、HTTP アクセスのみが有効になります。 ストレージ アカウントが HTTPS 経由の[セキュリティで保護された転送を必要とする](../common/storage-require-secure-transfer.md)ように構成されている場合は、カスタム ドメインの HTTPS アクセスを有効にする必要があります。 

HTTPS アクセスを有効にするには、この記事の「[HTTPS が有効になっているカスタム ドメインをマップする](#enable-https)」セクションを参照してください。 

<a id="map-a-domain"></a>

### <a name="map-a-custom-domain"></a>カスタム ドメインをマップする

> [!IMPORTANT]
> 構成を完了している間、ユーザーはカスタム ドメインを一時的に使用できなくなります。 ドメインが現在、ダウンタイムなしのサービス レベル アグリーメント (SLA) を締結しているアプリケーションをサポートしている場合は、この記事の「[カスタム ドメインをダウンタイムなしでマップする](#zero-down-time)」セクションの手順に従って、DNS マッピングの実行中にユーザーがドメインにアクセスできるようにします。

ユーザーがドメインを短時間利用できなくても構わない場合は、次の手順を実行します。

:heavy_check_mark:手順 1:ストレージ エンドポイントのホスト名を取得する。

:heavy_check_mark:手順 2:ドメイン プロバイダーで正規名 (CNAME) レコードを作成する。

:heavy_check_mark:手順 3:カスタム ドメインを Azure に登録する。 

:heavy_check_mark:手順 4:カスタム ドメインをテストする。

<a id="endpoint"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>手順 1:ストレージ エンドポイントのホスト名を取得する 

ホスト名は、プロトコル識別子と末尾のスラッシュを含まないストレージ エンドポイントの URL です。 

1. [Azure portal](https://portal.azure.com) で、ストレージ アカウントに移動します。

2. メニュー ウィンドウの **[設定]** で、 **[プロパティ]** を選択します。  

3. **プライマリ BLOB サービス エンドポイント**または**プライマリ静的 Web サイト エンドポイント**の値をテキスト ファイルにコピーします。 

4. その文字列からプロトコル識別子 (*例:* HTTPS など) と末尾のスラッシュを削除します。 次の表に例を示します。

   | エンドポイントの種類 |  endpoint | ホスト名 |
   |------------|-----------------|-------------------|
   |BLOB サービス  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |静的な Web サイト  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   この値は、後で使用するために取っておいてください。

<a id="create-cname-record"></a>

#### <a name="step-2-create-a-canonical-name-cname-record-with-your-domain-provider"></a>手順 2:ドメイン プロバイダーで正規名 (CNAME) レコードを作成する

ホスト名を指す CNAME レコードを作成します。 CNAME レコードは、ソース ドメイン名を宛先ドメイン名にマップする DNS レコードの一種です。

1. ドメイン レジストラーの Web サイトにサインインし、DNS 設定を管理するためのページに移動します。

   このページは、**Domain Name**、**DNS**、または **Name Server Management** というセクションにある可能性があります。

2. CNAME レコードの管理セクションを見つけます。 

   詳細設定ページに進み、**CNAME**、**Alias**、または **Subdomains** などを見つける必要があります。

3. CNAME レコードを作成します。 そのレコードの一部として、次の項目を指定します。 

   - `www` や `photos` などのサブドメイン エイリアス。 サブドメインは必須であり、ルート ドメインはサポートされていません。 
      
   - この記事の前半で既に説明した「[ストレージ エンドポイントのホスト名を取得する](#endpoint)」セクションで取得したホスト名。 

<a id="register"></a>

#### <a name="step-3-register-your-custom-domain-with-azure"></a>手順 3:カスタム ドメインを Azure に登録する

1. [Azure portal](https://portal.azure.com) で、ストレージ アカウントに移動します。

2. メニュー ウィンドウの **[Blob service]** で、 **[カスタム ドメイン]** を選択します。  

   ![カスタム ドメイン オプション](./media/storage-custom-domain-name/custom-domain-button.png "カスタム ドメイン")

   **[カスタム ドメイン]** ウィンドウが開きます。

3. **[ドメイン名]** テキスト ボックスに、サブドメインを含むカスタム ドメインの名前を入力します。  
   
   たとえば、ドメインが *contoso.com* でサブドメイン エイリアスが *www* の場合、「`www.contoso.com`」と入力します。 サブドメインが *photos* の場合、「`photos.contoso.com`」と入力します。

4. カスタム ドメインを登録するには、 **[保存]** ボタンを選択します。

   CNAME レコードがドメイン ネーム サーバー (DNS) を介して反映された後、ユーザーが適切なアクセス許可を持っている場合は、カスタム ドメインを使用して BLOB データを表示できます。

#### <a name="step-4-test-your-custom-domain"></a>手順 4:カスタム ドメインのテスト

カスタム ドメインが Blob service エンドポイントにマッピングされていることを確認するために、ストレージ アカウント内のパブリック コンテナーに BLOB を作成します。 次に、Web ブラウザーで、次の形式の URI を使って BLOB にアクセスします。`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

たとえば、*photos.contoso.com* カスタム サブドメインにある *myforms* コンテナー内の Web フォームにアクセスするには、次の URI を使用します。`http://photos.contoso.com/myforms/applicationform.htm`

<a id="zero-down-time"></a>

### <a name="map-a-custom-domain-with-zero-downtime"></a>ダウンタイムなしでカスタム ドメインをマップする

> [!NOTE]
> ユーザーがドメインを短時間使用できなくても構わない場合は、この記事の「[カスタム ドメインをマップする](#map-a-domain)」セクションの手順に従うことを検討してください。 これは、手順が少なくなっている、より簡単な方法です。  

ドメインが現在、ダウンタイムなしのサービス レベル アグリーメント (SLA) を締結しているアプリケーションをサポートしている場合は、次の手順に従って、DNS マッピングの実行中にユーザーがドメインにアクセスできるようにします。 

:heavy_check_mark:手順 1:ストレージ エンドポイントのホスト名を取得する。

:heavy_check_mark:手順 2:ドメイン プロバイダーで中間正規名 (CNAME) レコードを作成する。

:heavy_check_mark:手順 3:カスタム ドメインを Azure に事前登録する。

:heavy_check_mark:手順 4:ドメイン プロバイダーで CNAME レコードを作成する。

:heavy_check_mark:手順 5:カスタム ドメインをテストする。

<a id="endpoint-2"></a>

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>手順 1:ストレージ エンドポイントのホスト名を取得する 

ホスト名は、プロトコル識別子と末尾のスラッシュを含まないストレージ エンドポイントの URL です。 

1. [Azure portal](https://portal.azure.com) で、ストレージ アカウントに移動します。

2. メニュー ウィンドウの **[設定]** で、 **[プロパティ]** を選択します。  

3. **プライマリ BLOB サービス エンドポイント**または**プライマリ静的 Web サイト エンドポイント**の値をテキスト ファイルにコピーします。 

4. その文字列からプロトコル識別子 (*例:* HTTPS など) と末尾のスラッシュを削除します。 次の表に例を示します。

   | エンドポイントの種類 |  endpoint | ホスト名 |
   |------------|-----------------|-------------------|
   |BLOB サービス  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |静的な Web サイト  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   この値は、後で使用するために取っておいてください。

#### <a name="step-2-create-a-intermediary-canonical-name-cname-record-with-your-domain-provider"></a>手順 2:ドメイン プロバイダーで中間正規名 (CNAME) レコードを作成する

ホスト名を指す一時的な CNAME レコードを作成します。 CNAME レコードは、ソース ドメイン名を宛先ドメイン名にマップする DNS レコードの一種です。

1. ドメイン レジストラーの Web サイトにサインインし、DNS 設定を管理するためのページに移動します。

   このページは、**Domain Name**、**DNS**、または **Name Server Management** というセクションにある可能性があります。

2. CNAME レコードの管理セクションを見つけます。 

   詳細設定ページに進み、**CNAME**、**Alias**、または **Subdomains** などを見つける必要があります。

3. CNAME レコードを作成します。 そのレコードの一部として、次の項目を指定します。 

   - `www` や `photos` などのサブドメイン エイリアス。 サブドメインは必須であり、ルート ドメインはサポートされていません。

     `asverify` サブドメインをエイリアスに追加します。 たとえば、`asverify.www` や `asverify.photos` などです。
       
   - この記事の前半で既に説明した「[ストレージ エンドポイントのホスト名を取得する](#endpoint)」セクションで取得したホスト名。 

     サブドメイン `asverify` をホスト名に追加します。 (例: `asverify.mystorageaccount.blob.core.windows.net`)。

4. カスタム ドメインを登録するには、 **[保存]** ボタンを選択します。

   正常に登録された場合は、portal からストレージ アカウントが正常に更新されたことが通知されます。 カスタム ドメインは Azure によって検証されていますが、ドメインへのトラフィックは、まだストレージ アカウントにルーティングされません。

#### <a name="step-3-pre-register-your-custom-domain-with-azure"></a>手順 3:カスタム ドメインを Azure に事前登録する

カスタム ドメインを Azure に事前登録すると、ドメインの DNS レコードを変更しなくても、カスタム ドメインを Azure に認識させることができます。 このようにすると、ドメインの DNS レコードを変更したときにダウンタイムなしで BLOB エンドポイントにマッピングされます。

1. [Azure portal](https://portal.azure.com) で、ストレージ アカウントに移動します。

2. メニュー ウィンドウの **[Blob service]** で、 **[カスタム ドメイン]** を選択します。  

   ![カスタム ドメイン オプション](./media/storage-custom-domain-name/custom-domain-button.png "カスタム ドメイン")

   **[カスタム ドメイン]** ウィンドウが開きます。

3. **[ドメイン名]** テキスト ボックスに、サブドメインを含むカスタム ドメインの名前を入力します。  
   
   たとえば、ドメインが *contoso.com* でサブドメイン エイリアスが *www* の場合、「`www.contoso.com`」と入力します。 サブドメインが *photos* の場合、「`photos.contoso.com`」と入力します。

4. **[Use indirect CNAME validation]\(間接 CNAME 検証を使用する\)** チェックボックスをオンにします。

5. カスタム ドメインを登録するには、 **[保存]** ボタンを選択します。
  
   CNAME レコードがドメイン ネーム サーバー (DNS) を介して反映された後、ユーザーが適切なアクセス許可を持っている場合は、カスタム ドメインを使用して BLOB データを表示できます。

#### <a name="step-4-create-a-cname-record-with-your-domain-provider"></a>手順 4:ドメイン プロバイダーで CNAME レコードを作成する

ホスト名を指す一時的な CNAME レコードを作成します。

1. ドメイン レジストラーの Web サイトにサインインし、DNS 設定を管理するためのページに移動します。

   このページは、**Domain Name**、**DNS**、または **Name Server Management** というセクションにある可能性があります。

2. CNAME レコードの管理セクションを見つけます。 

   詳細設定ページに進み、**CNAME**、**Alias**、または **Subdomains** などを見つける必要があります。

3. CNAME レコードを作成します。 そのレコードの一部として、次の項目を指定します。 

   - `www` や `photos` などのサブドメイン エイリアス。 サブドメインは必須であり、ルート ドメインはサポートされていません。
      
   - この記事で既に説明した「[ストレージ エンドポイントのホスト名を取得する](#endpoint-2)」セクションで取得したホスト名。 

#### <a name="step-5-test-your-custom-domain"></a>手順 5:カスタム ドメインのテスト

カスタム ドメインが Blob service エンドポイントにマッピングされていることを確認するために、ストレージ アカウント内のパブリック コンテナーに BLOB を作成します。 次に、Web ブラウザーで、次の形式の URI を使って BLOB にアクセスします。`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

たとえば、*photos.contoso.com* カスタム サブドメインにある *myforms* コンテナー内の Web フォームにアクセスするには、次の URI を使用します。`http://photos.contoso.com/myforms/applicationform.htm`

### <a name="remove-a-custom-domain-mapping"></a>カスタム ドメインのマッピングを削除する

カスタム ドメインのマッピングを削除するには、カスタム ドメインの登録を解除します。 次の手順のいずれかを使用します。

#### <a name="portal"></a>[ポータル](#tab/azure-portal)

カスタム ドメイン設定を削除するには、次の操作を行います。

1. [Azure portal](https://portal.azure.com) で、ストレージ アカウントに移動します。

2. メニュー ウィンドウの **[Blob service]** で、 **[カスタム ドメイン]** を選択します。  
   **[カスタム ドメイン]** ウィンドウが開きます。

3. カスタム ドメイン名を含むテキスト ボックスの内容を消去します。

4. **[保存]** を選択します。

カスタム ドメインが正常に削除されたら、ストレージ アカウントが正常に更新されたことを伝えるポータルの通知が表示されます。

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

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

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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
---

<a id="enable-https"></a>

## <a name="map-a-custom-domain-with-https-enabled"></a>HTTPS が有効になっているカスタム ドメインをマップする

この方法では、さらに多くの手順が必要になりますが、HTTPS アクセスが可能になります。 

ユーザーが HTTPS を使用して BLOB または Web コンテンツにアクセスする必要がない場合は、この記事の「[HTTP のみが有効になっているカスタム ドメインをマップする](#enable-http)」セクションを参照してください。 

カスタム ドメインをマップし、HTTPS アクセスを有効にするには、次の手順を実行します。

1. BLOB または Web エンドポイントで [Azure CDN](../../cdn/cdn-overview.md) を有効にします。 

   BLOB ストレージ エンドポイントについては、「[Azure ストレージ アカウントと Azure CDN との統合](../../cdn/cdn-create-a-storage-account-with-cdn.md)」を参照してください。 

   静的な Web サイトのエンドポイントについては、[静的な Web サイトと Azure CDN との統合](static-website-content-delivery-network.md)に関する記事を参照してください。

2. [Azure CDN コンテンツをカスタム ドメインにマッピングします](../../cdn/cdn-map-content-to-custom-domain.md)｡

3. [Azure CDN カスタム ドメインで HTTPS を有効にします](../../cdn/cdn-custom-ssl.md)｡

   > [!NOTE] 
   > 静的 Web サイトを更新するときは、CDN エンドポイントを消去して、CDN エッジ サーバー上のキャッシュされたコンテンツを必ず消去してください。 詳細については、「[Azure CDN エンドポイントの消去](../../cdn/cdn-purge-endpoint.md)」を参照してください。

4. (省略可能) 次のガイダンスを確認します。

   * [Azure CDN での Shared Access Signature (SAS) トークン](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#shared-access-signatures)。

   * [Azure CDN での HTTP から HTTPS へのリダイレクト](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#http-to-https-redirection)。

   * [Azure CDN で Blob Storage を使用する場合の価格と課金](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#http-to-https-redirection)。

## <a name="next-steps"></a>次のステップ

* [Azure Blob Storage での静的な Web サイトのホスティングについて確認します](storage-blob-static-website.md)
