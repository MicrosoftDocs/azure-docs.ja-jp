---
title: "Blob Storage エンドポイントのドメイン名の構成 | Microsoft Docs"
description: "Azure クラシック ポータルでカスタム ユーザー ドメインを Azure ストレージ アカウントの BLOB ストレージ エンドポイントにマッピングする方法について説明します。"
services: storage
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: aaafd8c5-eacb-49dc-8c8b-3f7011ad5e92
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2016
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: fe4b9c356e5f7d56cb7e1fa62344095353d0b699
ms.openlocfilehash: 65ecd654cf6f1558a8f9ad6d6718ae5396fb52d0


---
# <a name="configure-a-custom-domain-name-for-your-blob-storage-endpoint"></a>BLOB ストレージ エンドポイントのカスタム ドメイン名の構成
## <a name="overview"></a>概要
Azure ストレージ アカウントの BLOB データにアクセスするためのカスタム ドメインを構成できます。 BLOB ストレージの既定のエンドポイントは `<storage-account-name>.blob.core.windows.net`です。 **www.contoso.com** などのカスタム ドメインおよびサブドメインをストレージ アカウントの BLOB エンドポイントにマッピングしている場合、ユーザーはそのドメインを使って、ストレージ アカウントの BLOB データにもアクセスできます。

> [!IMPORTANT]
> Azure Storage では、カスタム ドメインでの HTTPS はまだサポートしていません。 Microsoft では、お客様がこの機能に関心があることを認識しているので、将来のリリースで提供する予定です。
>
>

ストレージ アカウントの BLOB エンドポイントをカスタム ドメインで参照するには、2 つの方法があります。 最も簡単な方法は、CNAME レコードを作成して、カスタム ドメインおよびサブドメインを BLOB エンドポイントにマッピングすることです。 CNAME レコードは、ソース ドメインを目的のドメインにマッピングする DNS 機能です。 この場合、ソース ドメインは、カスタム ドメインおよびサブドメインです。サブドメインは常に必要であることに注意してください。 目的のドメインは、BLOB サービス エンドポイントです。

ただし、カスタム ドメインを BLOB エンドポイントにマッピングする処理によって、 [Azure クラシック ポータル](https://manage.windowsazure.com)でのドメインの登録中にドメインが短時間ダウンする場合があります。 カスタム ドメインが現在、ダウンタイムが発生しないことを要求するサービス レベル アグリーメント (SLA) のアプリケーションをサポートしている場合は、DNS マッピングの実行中にユーザーがドメインにアクセスできるように、Azure **asverify** サブドメインを使用して中間登録ステップを提供できます。

次の表は、 **mystorageaccount**というストレージ アカウントの BLOB データにアクセスするためのサンプル URL を示します。 ストレージ アカウントに登録されているカスタム ドメインは **www.contoso.com**です。

| リソースの種類 | URL の形式 |
| --- | --- |
| ストレージ アカウント |**既定の URL**: http://mystorageaccount.blob.core.windows.net<p>**カスタム ドメインの URL**: http://www.contoso.com</td> |
| BLOB |**既定の URL**: http://mystorageaccount.blob.core.windows.net/mycontainer/myblob<p>**カスタム ドメインの URL**: http://www.contoso.com/mycontainer/myblob |
| ルート コンテナー |**既定の URL**: http://mystorageaccount.blob.core.windows.net/myblob or http://mystorageaccount.blob.core.windows.net/$root/myblob<p>**カスタム ドメインの URL**: http://www.contoso.com/myblob or http://www.contoso.com/$root/myblob |

## <a name="register-a-custom-domain-for-your-storage-account"></a>ストレージ アカウントのカスタム ドメインの登録
ユーザーが短時間、ドメインを使用できなくても問題ではない場合、またはカスタム ドメインが現在、アプリケーションのホストでない場合は、この手順でカスタム ドメインを登録します。

カスタム ドメインが現在、ダウンタイムの発生が許容されないアプリケーションをサポートしている場合は、「<a href="#register-a-custom-domain-for-your-storage-account-using-the-intermediary-asverify-subdomain">中間 asverify サブドメインを使用したストレージ アカウントのカスタム ドメインの登録</a>」で説明している手順に従ってください。

カスタム ドメイン名を構成するには、ドメイン レジストラーで新しい CNAME レコードを作成する必要があります。 CNAME レコードによって、ドメイン名のエイリアスが指定されます。この場合、カスタム ドメインのアドレスが、ストレージ アカウントの BLOB ストレージ エンドポイントにマッピングされます。

それぞれのレジストラーでは CNAME レコードを指定するための同様の (多少異なる) 手段が用意されていますが、その概念は同じです。 多くの基本ドメイン登録パッケージには DNS 構成が用意されていないため、CNAME レコードを作成するには、事前にドメイン登録パッケージのアップグレードが必要である可能性があります。

1. [Azure クラシック ポータル](https://manage.windowsazure.com)で **[ストレージ]** タブに移動します。
2. **[ストレージ]** タブで、カスタム ドメインをマッピングするストレージ アカウントの名前をクリックします。
3. [ **構成** ] タブをクリックします。
4. 画面の下部で、**[ドメインの管理]** をクリックして **[カスタム ドメインの管理]** ダイアログを表示します。 ダイアログ上部のテキストには、CNAME レコードの作成方法に関する情報が含まれています。 この手順では、 **asverify** サブドメインに関するテキストは無視してください。
5. DNS レジストラーの Web サイトにログオンし、DNS の管理ページに移動します。 これは、"**ドメイン名**"、"**DNS**"、"**ネーム サーバー管理**" などのセクションにあります。
6. CNAME 管理セクションを見つけます。 詳細設定ページに進み、"**CNAME**"、"**エイリアス**"、"**サブドメイン**" などの語句を探します。
7. 新しい CNAME レコードを作成し、"**www**" や "**photos**" などのサブドメイン エイリアスを指定します。 次に、**mystorageaccount.blob.core.windows.net** (**mystorageaccount** はストレージ アカウントの名前です) という形式でホスト名を指定します。これは BLOB サービス エンドポイントです。 使用するホスト名は、**[カスタム ドメインの管理]** ダイアログ内のテキストに含まれています。
8. 新しい CNAME レコードを作成したら、**[カスタム ドメインの管理]** ダイアログに戻り、サブドメインを含むカスタム ドメイン名を **[カスタム ドメイン名]** フィールドに入力します。 たとえば、ドメインが "**contoso.com**" で、サブドメインが "**www**" の場合は、"**www.contoso.com**" と入力します。サブドメインが "**photos**" の場合は、"**photos.contoso.com**" と入力します。 サブドメインは必須であることに注意してください。
9. **[登録]** ボタンをクリックしてカスタム ドメインを登録します。

登録が成功すると、 **"カスタム ドメインはアクティブです"**というメッセージが表示されます。 これで、ユーザーは、適切なアクセス許可を持っている限り、カスタム ドメインの BLOB データを表示できます。

## <a name="register-a-custom-domain-for-your-storage-account-using-the-intermediary-asverify-subdomain"></a>中間 asverify サブドメインを使用したストレージ アカウントのカスタム ドメインの登録
カスタム ドメインが現在、ダウンタイムが発生しないことが SLA で要求されているアプリケーションをサポートしている場合は、この手順でカスタム ドメインを登録します。 asverify.&lt;subdomain&gt;.&lt;customdomain&gt; から asverify.&lt;storageaccount&gt;.blob.core.windows.net を指す CNAME を作成すると、ドメインを Azure に事前登録できます。 そして、&lt;subdomain&gt;.&lt;customdomain&gt; から &lt;storageaccount&gt;.blob.core.windows.net を指す、もう 1 つの CNAME を作成します。このポイントから、カスタム ドメインへのトラフィックが BLOB エンドポイントにダイレクトされます。

asverify サブドメインは、Azure で認識される特殊なサブドメインです。 自分のサブドメインの前に **asverify** を付けると、ドメインの DNS レコードを変更しなくても、カスタム ドメインが Azure に認識されます。 ドメインの DNS レコードを変更すると、ダウンタイムが発生することなく、ドメインが BLOB エンドポイントにマッピングされます。

1. [Azure クラシック ポータル](https://manage.windowsazure.com)で **[ストレージ]** タブに移動します。
2. **[ストレージ]** タブで、カスタム ドメインをマッピングするストレージ アカウントの名前をクリックします。
3. [ **構成** ] タブをクリックします。
4. 画面の下部で、**[ドメインの管理]** をクリックして **[カスタム ドメインの管理]** ダイアログを表示します。 ダイアログ上部のテキストには、 **asverify** サブドメインを使用して CNAME レコードを作成する方法に関する情報が含まれます。
5. DNS レジストラーの Web サイトにログオンし、DNS の管理ページに移動します。 これは、"**ドメイン名**"、"**DNS**"、"**ネーム サーバー管理**" などのセクションにあります。
6. CNAME 管理セクションを見つけます。 詳細設定ページに進み、"**CNAME**"、"**エイリアス**"、"**サブドメイン**" などの語句を探します。
7. 新しい CNAME レコードを作成し、asverify サブドメインを含むサブドメイン エイリアスを指定します。 たとえば、指定するサブドメインは、"**asverify.www**" または "**asverify.photos**" という形式になります。 そして、**asverify.mystorageaccount.blob.core.windows.net** (**mystorageaccount** はストレージ アカウントの名前です) という形式でホスト名を指定します。これは BLOB サービス エンドポイントです。 使用するホスト名は、**[カスタム ドメインの管理]** ダイアログ内のテキストに含まれています。
8. 新しい CNAME レコードを作成したら、**[カスタム ドメインの管理]** ダイアログに戻り、カスタム ドメイン名を **[カスタム ドメイン名]** フィールドに入力します。 たとえば、ドメインが "**contoso.com**" で、サブドメインが "**www**" の場合は、"**www.contoso.com**" と入力します。サブドメインが "**photos**" の場合は、"**photos.contoso.com**" と入力します。 サブドメインは必須であることに注意してください。
9. **[高度: 'asverify' サブドメインを使ってカスタム ドメインを事前登録する]**チェックボックスをクリックします。
10. **[登録]** ボタンをクリックしてカスタム ドメインを事前登録します。

    事前登録が成功すると、 **"カスタム ドメインはアクティブです"**というメッセージが表示されます。
11. この時点で、カスタム ドメインは Azure によって確認されていますが、ドメインへのトラフィックは、まだストレージ アカウントへはルーティングされません。 処理を完了するには、DNS レジストラーの Web サイトに戻り、サブドメインを BLOB サービス エンドポイントにマッピングする別の CNAME レコードを作成します。 たとえば、**www** または **photos** というサブドメインと、**mystorageaccount.blob.core.windows.net** (**mystorageaccount** はストレージ アカウントの名前です) というホスト名を指定します。 この手順で、カスタム ドメインの登録が完了します。
12. 最後に、**asverify** を使って作成した CNAME レコードを削除します。このレコードは、中間の手順としてだけ必要であったためです。

これで、ユーザーは、適切なアクセス許可を持っている限り、カスタム ドメインの BLOB データを表示できます。

## <a name="verify-that-the-custom-domain-references-your-blob-service-endpoint"></a>カスタム ドメインが BLOB サービス エンドポイントを参照することの確認
カスタム ドメインが実際に BLOB サービス エンドポイントにマッピングされていることを確認するために、ストレージ アカウントのパブリック コンテナーで BLOB を作成します。 そして、Web ブラウザーで、次の形式の URI を使って BLOB にアクセスします。

    http://<*subdomain.customdomain*>/<*mycontainer*>/<*myblob*>

たとえば、**myforms** コンテナー内の BLOB にマッピングされている **photos.contoso.com** カスタム サブドメイン経由で Web フォームにアクセスするには、次の URI を使用します。

    http://photos.contoso.com/myforms/applicationform.htm

## <a name="unregister-a-custom-domain-from-your-storage-account"></a>ストレージ アカウントからのカスタム ドメインの登録解除
カスタム ドメインを登録解除するには、次の手順に従います。

1. [Azure クラシック ポータル](https://manage.windowsazure.com)にサインインします。
2. ナビゲーション ウィンドウで **[ストレージ]**をクリックします。
3. **[ストレージ]** ページで、ストレージ アカウントの名前をクリックしてダッシュボードを表示します。
4. リボンの **[ドメインの管理]**をクリックします。
5. **[カスタム ドメインの管理]** ダイアログ ボックスで、**[登録解除]** をクリックします。

## <a name="additional-resources"></a>その他のリソース
* [Content Delivery Network (CDN) エンドポイントにカスタム ドメインをマップする方法](../cdn/cdn-map-content-to-custom-domain.md)




<!--HONumber=Nov16_HO4-->


