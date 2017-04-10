---
title: "Azure Active Directory B2C: ページの UI カスタマイズ ヘルパー ツール | Microsoft Docs"
description: "Azure Active Directory B2C のページの UI カスタマイズ機能を試すために使用するヘルパー ツール"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: ae935d52-3520-4a94-b66e-b35bb40e7514
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: f9c1e41c423cc0d638d5c1d273d58b2fe713fa1c
ms.openlocfilehash: 2aeb1f20255264ca97b8aff6bd506b5fa1832e28


---
# <a name="azure-active-directory-b2c-a-helper-tool-used-to-demonstrate-the-page-user-interface-ui-customization-feature"></a>Azure Active Directory B2C: ページのユーザー インターフェイス (UI) カスタマイズ機能を試すために使用するヘルパー ツール
この記事は、Azure Active Directory (Azure AD) B2C での [UI のカスタマイズに関するメインの記事](active-directory-b2c-reference-ui-customization.md) に付随するものです。 以下の手順では、用意されているサンプルの HTML および CSS の内容を使用して、ページの UI カスタマイズ機能を試してみる方法について説明します。

## <a name="get-an-azure-ad-b2c-tenant"></a>Azure AD B2C テナントを取得する
Azure AD B2C テナントがまだない場合は、カスタマイズを始める前に、 [Azure AD B2C テナントを取得](active-directory-b2c-get-started.md) する必要があります。

## <a name="create-a-sign-up-or-sign-in-policy"></a>サインアップまたはサインイン ポリシーを作成する
用意されているサンプル コンテンツを使用すると、[サインアップまたはサインイン ポリシー](active-directory-b2c-reference-policies.md)の 2 つのページ、[統合サインイン ページ](active-directory-b2c-reference-ui-customization.md)と[セルフ アサート属性ページ](active-directory-b2c-reference-ui-customization.md)をカスタマイズできます。 [サインアップまたはサインイン ポリシーを作成する](active-directory-b2c-reference-policies.md#create-a-sign-up-or-sign-in-policy)際は、ローカル アカウント (電子メール アドレス)、Facebook、Google、および Microsoft を **ID プロバイダー**として追加します。 サンプルの HTML コンテンツが受け付ける IDP はこれらだけです。  必要に応じて、これら IDP のサブセットを追加することもできます。

## <a name="register-an-application"></a>アプリケーションを登録する
B2C テナントに、ポリシーを実行するために使用できる [アプリケーションを登録する](active-directory-b2c-app-registration.md) 必要があります。 アプリケーションを登録した後、サインアップ ポリシーを実際に実行するにはいくつかのオプションを使用できます。

* 「 [アプリケーションにコンシューマーをサインアップおよびサインインする](active-directory-b2c-overview.md#get-started)」の「作業開始」セクションの一覧にある Azure AD B2C クイック スタート アプリケーションのいずれかをビルドします。
* ビルド済みの [Azure AD B2C プレイグラウンド](https://aadb2cplayground.azurewebsites.net) アプリケーションを使用します。 プレイグラウンドを使用する場合は、**リダイレクト URI** `https://aadb2cplayground.azurewebsites.net/` を使用して B2C テナントにアプリケーションを登録する必要があります。
* **Azure Portal** で、ポリシーの [[今すぐ実行]](https://portal.azure.com/)ボタンを使用します。

## <a name="customize-your-policy"></a>ポリシーをカスタマイズする
ポリシーの外観と操作性をカスタマイズするには、最初に Azure AD B2C の固有の規則を使用して HTML および CSS ファイルを作成する必要があります。 その後は、静的なコンテンツを、Azure AD B2C がアクセスできる公開されている場所にアップロードできます。 そのような場所としては、独自の専用 Web サーバー、Azure BLOB ストレージ、Azure Content Delivery Network、その他の静的リソース ホスティング プロバイダーを使用できます。 唯一の要件は、コンテンツが HTTPS 経由で使用でき、CORS を使用してアクセスできることです。 静的コンテンツを Web で公開した後は、この場所を参照してユーザーにそのコンテンツを表示するようにポリシーを編集できます。 Azure AD B2Cカスタマイズ機能の動作の詳細については、 [UI のカスタマイズに関するメインの記事](active-directory-b2c-reference-ui-customization.md) を参照してください。

このチュートリアルでは、既にいくつかのサンプル コンテンツを作成し、Azure BLOB ストレージでホストしてあります。 サンプル コンテンツは、架空の会社 "Wingtip Toys" のテーマでの非常に基本的なカスタマイズです。 独自のポリシーで試してみるには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com/) でテナントにサインインし、B2C 機能ブレードに移動します。
2. **[Sign-up or sign-in policies (サインアップまたはサインイン ポリシー)]** をクリックし、自分のポリシー ("b2c\_1\_sign\_up\_sign\_in" など) をクリックします。
3. **[Page UI customization (ページ UI のカスタマイズ)]**、**[Unified sign-up or sign-in page (統合されたサインアップまたはサインイン ページ)]** の順にクリックします。
4. **[Use custom page (カスタム ページを使用する)]** を **[はい]** に切り替えます。 **[カスタム ページの URI]** フィールドに、「`https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/unified.html`」と入力します。 **[OK]**をクリックします。
5. **[ローカル アカウントのサインアップ ページ]**をクリックします。 **[カスタム テンプレートを使用する]** を **[はい]** に切り替えます。 **[カスタム ページの URI]** フィールドに、「`https://wingtiptoysb2c.blob.core.windows.net/b2c/wingtip/selfasserted.html`」と入力します。
6. **[Social account sign-up page (ソーシャル アカウントのサインアップ ページ)]**についても同じ手順を繰り返します。
   **[OK]** を 2 回クリックして、UI カスタマイズ ブレードを閉じます。
7. **[保存]**をクリックします。

カスタマイズしたポリシーを試すことができるようになります。 独自のアプリケーションまたは Azure AD B2C プレイグラウンドを使用することもできますが、単にポリシー ブレードで **[今すぐ実行]** コマンドをクリックするのでもかまいません。 ドロップダウン ボックスでアプリケーションを選び、適切なリダイレクト URI を選びます。 **[今すぐ実行]** ボタンをクリックします。 新しいブラウザー タブが開き、アプリケーションへのサインアップについて、新しいコンテンツが所定の場所に置かれた状態のユーザー エクスペリエンスを確認できます。

## <a name="upload-the-sample-content-to-azure-blob-storage"></a>サンプル コンテンツを Azure BLOB ストレージにアップロードする
Azure BLOB ストレージを使用してページのコンテンツをホストする場合は、独自のストレージ アカウントを作成し、B2C ヘルパー ツールを使用してファイルをアップロードできます。

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成
1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. **[+ 新規]** > **[データ + ストレージ]** > **[ストレージ アカウント]** の順にクリックします。 Azure BLOB ストレージ アカウントを作成するには、Azure サブスクリプションが必要です。 [Azure Web サイト](https://azure.microsoft.com/pricing/free-trial/)で無料試用版にサインアップできます。
3. ストレージ アカウントの**名前** (例: "contoso") を入力し、**[価格レベル]**、**[リソース グループ]**、**[サブスクリプション]** でそれぞれ該当する項目を選択します。 **[スタート画面にピン留めする]** がオンになっていることを確認します。 **[作成]**をクリックします。
4. スタート画面に戻り、作成したストレージ アカウントをクリックします。
5. **[概要]** セクションで、**[コンテナー]**、**[+ 追加]** の順にクリックします。
6. コンテナーの **[名前]** ("b2c" など) を指定し、**[アクセスの種類]** として **[BLOB]** を選択します。 **[OK]**をクリックします。
7. 作成したコンテナーは、 **[BLOB]** ブレードに一覧表示されます。 コンテナーの URL ( `https://contoso.blob.core.windows.net/b2c`のような形式) をメモしておきます。 **[BLOB]** ブレードを閉じます。
8. [ストレージ アカウント] のブレードで、**[キー]** をクリックし、**[ストレージ アカウント名]** および **[プライマリ アクセス キー]** フィールドの値をメモしておきます。

> [!NOTE]
> **プライマリ アクセス キー** は、重要なセキュリティ資格情報です。
> 
> 

### <a name="download-the-helper-tool-and-sample-files"></a>ヘルパー ツールとサンプル ファイルをダウンロードする
[Azure Blob Storage ヘルパー ツールとサンプル ファイルを .zip ファイルとして](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip)ダウンロードできます。また、次のように GitHub から複製することもできます。

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

このリポジトリには `sample_templates\wingtip` ディレクトリが含まれており、そこには HTML、CSS、イメージのサンプルが含まれています。 これらのテンプレートで独自の Azure BLOB ストレージ アカウントを参照するには、HTML ファイルを編集する必要があります。 `unified.html` および `selfasserted.html` を開き、`https://localhost` のすべてのインスタンスを、前の手順でメモした独自のコンテナーの URL に置き換えます。 この場合、HTML は Azure AD によってドメイン `https://login.microsoftonline.com`で提供されるので、HTML ファイルの絶対パスを使用する必要があります。

### <a name="upload-the-sample-files"></a>サンプル ファイルをダウンロードする
同じリポジトリで、`B2CAzureStorageClient.zip` を解凍し、その中にある `B2CAzureStorageClient.exe` ファイルを実行します。 このプログラムは単に、指定されたディレクトリ内のすべてのファイルをストレージ アカウントにアップロードし、それらのファイルに対する CORS アクセスを有効にします。 上記の手順を実行した場合、HTML および CSS ファイルはストレージ アカウントを指すようになります。 ストレージ アカウントの名前は、`blob.core.windows.net` の前の部分 (`contoso` など) です。 ブラウザーで `https://{storage-account-name}.blob.core.windows.net/{container-name}/wingtip/unified.html` にアクセスしてみることで、コンテンツが正常にアップロードされていることを確認できます。 また、 [http://test-cors.org/](http://test-cors.org/) を使用して、コンテンツが CORS に対応していることを確認します (結果で "XHR status: 200" を探します。 )

### <a name="customize-your-policy-again"></a>再びポリシーをカスタマイズする
独自のストレージ アカウントにサンプル コンテンツをアップロードしたので、それを参照するようにサインアップ ポリシーを編集する必要があります。 前述の「[ポリシーをカスタマイズする](#customize-your-policy)」の手順を繰り返します。ただし、今回は独自のストレージ アカウントの URL を使用します。 たとえば、`unified.html` ファイルの場所は `<url-of-your-container>/wingtip/unified.html` になります。

これで、 **[今すぐ実行]** ボタンまたは独自のアプリケーションを使用して、ポリシーを再度実行できるようになります。 どちらの場合も同じサンプルの HTML と CSS を使用したので、結果はほぼ同じになるはずです。 ただし今度は、ポリシーは Azure BLOB ストレージの独自のインスタンスを参照しているので、自由にファイルを編集して再びアップロードできます。 HTML および CSS のカスタマイズの詳細については、 [UI のカスタマイズに関するメインの記事](active-directory-b2c-reference-ui-customization.md)を参照してください。




<!--HONumber=Dec16_HO5-->


