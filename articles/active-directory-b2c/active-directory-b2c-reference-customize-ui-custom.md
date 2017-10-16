---
title: "Azure Active Directory B2C: リファレンス: カスタム ポリシーでユーザー体験の UI をカスタマイズする | Microsoft Docs"
description: "Azure Active Directory B2C のカスタム ポリシーに関するトピック"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/25/2017
ms.author: joroja
ms.openlocfilehash: 68f40aa638a687398512278a0b77d1ba392859cf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="customize-the-ui-of-a-user-journey-with-custom-policies"></a>カスタム ポリシーでユーザー体験の UI をカスタマイズする

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

> [!NOTE]
> この記事では、Identity Experience Framework を使用して UI をカスタマイズする方法と B2C のカスタム ポリシーを有効にする方法について、詳細に説明します。


シームレスなユーザー エクスペリエンスは、企業-消費者間のソリューションにおいて鍵となります。 シームレスなユーザー エクスペリエンスとは、デバイスでもブラウザーでも、Microsoft のサービスでのユーザー体験が、ユーザーが使用している顧客サービスのユーザー体験と変わらないことを意味します。

## <a name="understand-the-cors-way-for-ui-customization"></a>UI のカスタマイズに CORS を使用する方法について

Azure AD B2C では、さまざまなページでのユーザー エクスペリエンス (UX) の外観をカスタマイズすることができます。これらのページは、カスタム ポリシーを通じて Azure AD B2C で配信および表示することができます。

この目的のために、Azure AD B2C はコンシューマーのブラウザーでコードを実行すると共に、[クロス オリジン リソース共有 (CORS)](http://www.w3.org/TR/cors/) と呼ばれる最新の標準手法を使用して、HTML5/CSS テンプレートを指すようにカスタム ポリシーで指定した特定の URL からカスタム コンテンツを読み込みます。 CORS とは、フォントなどの Web ページ上の制限されたリソースを、リソースの提供元のドメインとは別のドメインに対して要求できるようにするメカニズムです。

従来の方法では、テンプレート ページはソリューションが所有していたため、提供できるテキストと画像に制限があったほか、外観の制御にも限界があり、シームレスなエクスペリエンスの実現は一筋縄ではいきませんでした。これに対して、CORS を使う方法では HTML5 と CSS がサポートされており、次のことが可能となっています。

- コンテンツをホストし、ソリューションがクライアント側のスクリプトを使用してコントロールを挿入する。
- 外観のすべてのピクセルを完全に制御する。

必要に応じて HTML5/CSS ファイルを作成することで、コンテンツ ページを必要な数だけ作成できます。

> [!NOTE]
> セキュリティ上の理由から、現時点ではカスタマイズのための JavaScript の使用はブロックされています。 JavaScript のブロックを解除するには、Azure AD B2C テナントのカスタム ドメイン名を使用する必要があります。

各 HTML5/CSS テンプレートでは、"*アンカー*" 要素を指定します。この要素は、以降で示すとおり、HTML またはコンテンツ ページ内の必須の `<div id=”api”>` 要素と対応します。 Azure AD B2C では、すべてのコンテンツ ページにこの特定の div が存在している必要があります。

```
<!DOCTYPE html>
<html>
  <head>
    <title>Your page content’s tile!</title>
  </head>
  <body>
    <div id="api"></div>
  </body>
</html>
```

ページの Azure AD B2C 関連コンテンツはこの div に挿入されますが、ページの他の部分は自分で制御できます。 Azure AD B2C の JavaScript コードは、コンテンツを取り込んで、この特定の div 要素に HTML を挿入します。 Azure AD B2C は、必要に応じてコントロールを挿入します。コントロールの種類は、アカウント セレクター コントロール、ログイン コントロール、多要素コントロール (現在は電話ベース)、属性コレクション コントロールです。 Azure AD B2C では、すべてのコントロールが HTML5 に準拠し、アクセス可能であるほか、コントロールのスタイルを自在に編集することができます。また、コントロールが古いバージョンに戻ることはありません。

結合されたコンテンツは、最終的には動的なドキュメントとしてコンシューマーに表示されます。

以上の動作を想定どおりに実現するには、次のことを行う必要があります。

- コンテンツが HTML5 に準拠しており、アクセス可能であることを確認する。
- コンテンツ サーバーで CORS が有効になっていることを確認する。
- HTTPS を使用してコンテンツを配信する。
- すべてのリンクと CSS コンテンツで、https://yourdomain/content などの絶対 URL を使用する。

> [!TIP]
> コンテンツをホストしているサイトで CORS が有効になっていることを確認し、CORS 要求をテストするには、サイト http://test-cors.org/ を使用します。 このサイトでは、CORS 要求をリモート サーバーに送信することも (CORS がサポートされているかどうかをテストするため)、CORS 要求をテスト サーバーに送信することも (CORS の特定の機能を使ってみるため)、簡単に行えます。

> [!TIP]
> サイト http://enable-cors.org/ では、CORS の非常に有用なリソースが構成されています。

この CORS ベースのアプローチにより、エンド ユーザーはアプリケーションと Azure AD B2C によって提供されたページの間で、一貫したエクスペリエンスを得ることができます。

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成

前提として、ストレージ アカウントを作成する必要があります。 Azure BLOB ストレージ アカウントを作成するには、Azure サブスクリプションが必要です。 [Azure Web サイト](https://azure.microsoft.com/en-us/pricing/free-trial/)で無料試用版にサインアップできます。

1. ブラウズ セッションを開いて [Azure Portal](https://portal.azure.com) に移動します。
2. 管理者の資格情報でサインインします。
3. **[新規]** > **[データ + ストレージ]** > **[ストレージ アカウント]** の順にクリックします。  **[ストレージ アカウントの作成]** ブレードが開きます。
4. **[名前]** に、*contoso369b2c* などのストレージ アカウントの名前を指定します。 この値は後で *storageAccountName* として参照されます。
5. [価格レベル]、[リソース グループ]、[サブスクリプション] でそれぞれ該当する項目を選択します。 **[スタート画面にピン留めする]** がオンになっていることを確認します。 **Create** をクリックしてください。
6. スタート画面に戻り、作成したストレージ アカウントをクリックします。
7. **[サービス]** セクションで、**[BLOB]** をクリックします。 **[Blob service] ブレード**が開きます。
8. **[+ コンテナー]** をクリックします。
9. **[名前]** に、*b2c* などのコンテナーの名前を指定します。 この値は後で *containerName* として参照されます。
9. **[アクセスの種類]** として **[BLOB]** を選択します。 **Create** をクリックしてください。
10. 作成したコンテナーは、**[Blob service] ブレード**に一覧表示されます。
11. **[BLOB]** ブレードを閉じます。
12. **[ストレージ アカウント] ブレード**で、**キー** アイコンをクリックします。 **[アクセス キー] ブレード**が開きます。  
13. **key1** の値を書き留めます。 この値は後で *key1* として参照されます。

## <a name="downloading-the-helper-tool"></a>ヘルパー ツールをダウンロードする

1.  [GitHub](https://github.com/azureadquickstarts/b2c-azureblobstorage-client/archive/master.zip) からヘルパー ツールをダウンロードします。
2.  *B2C-AzureBlobStorage-Client-master.zip* ファイルをローカル コンピューターに保存します。
3.  B2C-AzureBlobStorage-Client-master.zip ファイルのコンテンツをローカル ディスクに展開します (**UI-Customization-Pack** フォルダーの直下など)。 これにより、その下に *B2C-AzureBlobStorage-Client-master* フォルダーが作成されます。
4.  このフォルダーを開き、その中にある *B2CAzureStorageClient.zip* というアーカイブ ファイルのコンテンツを展開します。

## <a name="upload-the-ui-customization-pack-sample-files"></a>UI-Customization-Pack サンプル ファイルをアップロードする

1.  エクスプローラーを使用して、前のセクションで作成した *UI-Customization-Pack* フォルダーの下にある *B2C-AzureBlobStorage-Client-master* フォルダーに移動します。
2.  *B2CAzureStorageClient.exe* ファイルを実行します。 このプログラムは単に、指定されたディレクトリ内のすべてのファイルをストレージ アカウントにアップロードし、それらのファイルに対する CORS アクセスを有効にします。
3.  メッセージが表示されたら、次のとおり指定します。a.   ストレージ アカウントの名前である *storageAccountName* (*contoso369b2c* など)。
    b.  Azure BLOB ストレージのプライマリ アクセス キーである *key1* (*contoso369b2c* など)。
    c.  ストレージの BLOB ストレージ コンテナーの名前である *containerName* (*b2c* など)。
    d.  *Starter-Pack* サンプル ファイルのパス (*..\B2CTemplates\wingtiptoys* など)。

上記の手順を実行した場合、架空の企業である **wingtiptoys** 用の *UI-Customization-Pack* の HTML5 および CSS ファイルは、ストレージ アカウントを指すようになります。  関連するコンテナーのブレードを Azure Portal で開くことで、コンテンツが正常にアップロードされていることを確認できます。 別の方法として、ブラウザーでページにアクセスしてみることでも、コンテンツが正常にアップロードされていることを確認できます。 詳細については、「[Azure Active Directory B2C: ページのユーザー インターフェイス (UI) カスタマイズ機能を試すために使用するヘルパー ツール](active-directory-b2c-reference-ui-customization-helper-tool.md)」を参照してください。

## <a name="ensure-the-storage-account-has-cors-enabled"></a>ストレージ アカウントで CORS が有効になっていることを確認する

Azure AD B2C Premium でコンテンツの読み込みに使用するエンドポイントでは、CORS (クロス オリジン リソース共有) が有効になっている必要があります。 これは、ページの配信元の Azure AD B2C Premium ドメインとは異なる、別のドメインでコンテンツがホストされるためです。

コンテンツをホストしているストレージで CORS が有効になっていることを確認するために、次の手順に進みます。

1. ブラウズ セッションを開き、*unified.html* ページに移動します。その際、そのページのストレージ アカウント内での場所を指す完全な URL である `https://<storageAccountName>.blob.core.windows.net/<containerName>/unified.html` を使用します。 たとえば、https://contoso369b2c.blob.core.windows.net/b2c/unified.html などです。
2. http://test-cors.org に移動します。このサイトでは、使用しているページで CORS が有効になっていることを確認できます。  
<!--
![test-cors.org](../../media/active-directory-b2c-customize-ui-of-a-user-journey/test-cors.png)
-->

3. **[リモート URL]** に、unified.html のコンテンツの完全な URL を入力し、**[要求の送信]** をクリックします。
4. **[結果]** セクションの出力に *XHR status: 200* が含まれていることを確認します。 これは、CORS が有効になっていることを示します。
<!--
![CORS enabled](../../media/active-directory-b2c-customize-ui-of-a-user-journey/cors-enabled.png)
-->
これで、ストレージ アカウントに、ここで説明した *b2c* という BLOB コンテナーが含まれるようになりました。このコンテナーには、*Starter-Pack* 内の次の wingtiptoys テンプレートが含まれます。

<!--
![Correctly configured storage account](../../articles/active-directory-b2c/media/active-directory-b2c-reference-customize-ui-custom/storage-account-final.png)
-->

次の表に、上記の HTML5 ページの目的を示します。

| HTML5 テンプレート | Description |
|----------------|-------------|
| *phonefactor.html* | このページは、多要素認証ページのテンプレートとして使用できます。 |
| *resetpassword.html* | このページは、パスワードを忘れた場合のページのテンプレートとして使用できます。 |
| *selfasserted.html* | このページは、ソーシャル アカウントのサインアップ ページ、ローカル アカウントのサインアップ ページ、ローカル アカウントのサインイン ページのテンプレートとして使用できます。 |
| *unified.html* | このページは、統合されたサインアップ ページまたはサインイン ページのテンプレートとして使用できます。 |
| *updateprofile.html* | このページは、プロファイルの更新ページのテンプレートとして使用できます。 |

## <a name="add-a-link-to-your-html5css-templates-to-your-user-journey"></a>HTML5/CSS テンプレートへのリンクをユーザー体験に追加する

カスタム ポリシーを直接編集することで、HTML5/CSS テンプレートへのリンクをユーザー体験に追加できます。

ユーザー体験で使用するカスタム HTML5/CSS テンプレートは、そのユーザー体験内で使用できるコンテンツ定義の一覧で指定されている必要があります。 このため、カスタム ポリシー XML ファイルの *<BuildingBlocks>* セクションで、省略可能な *<ContentDefinitions>* XML 要素を宣言する必要があります。

次の表に、Azure AD B2C Identity Experience Engine で検出されたコンテンツ定義 ID のセットと、それに関連するページの種類を示します。

| コンテンツ定義 ID | Description |
|-----------------------|-------------|
| *api.error* | **エラー ページ**。 このページは、例外またはエラーが発生した場合に表示されます。 |
| *api.idpselections* | **ID プロバイダーの選択ページ**。 このページには、サインイン時にユーザーが選択できる ID プロバイダーの一覧が含まれます。 ID プロバイダーは、エンタープライズ ID プロバイダー、ソーシャル ID プロバイダー (Facebook や Google+ など)、ローカル アカウント (メール アドレスやユーザー名を使用するもの) のいずれかです。 |
| *api.idpselections.signup* | **サインアップのための ID プロバイダーの選択**。 このページには、サインアップ時にユーザーが選択できる ID プロバイダーの一覧が含まれます。 ID プロバイダーは、エンタープライズ ID プロバイダー、ソーシャル ID プロバイダー (Facebook や Google+ など)、ローカル アカウント (メール アドレスやユーザー名を使用するもの) のいずれかです。 |
| *api.localaccountpasswordreset* | **パスワードを忘れた場合**。 このページには、ユーザーがパスワードのリセットを開始するために入力する必要があるフォームが含まれます。  |
| *api.localaccountsignin* | **ローカル アカウントのサインイン ページ**。 このページには、ユーザーがメール アドレスやユーザー名に基づくローカル アカウントでサインインするときに入力が必要になるサインイン フォームが含まれます。 このフォームには、テキスト入力ボックスとパスワード入力ボックスを含めることができます。 |
| *api.localaccountsignup* | **ローカル アカウントのサインアップ ページ**。 このページには、ユーザーが電子メールアドレスやユーザー名を使ったローカル アカウントにサインアップするときに入力が必要になるサインアップ フォームが含まれます。 このフォームには、テキスト入力ボックス、パスワード入力ボックス、ラジオ ボタン、単一選択ドロップダウン ボックス、複数選択チェック ボックスなどのさまざまな入力コントロールを含めることができます。 |
| *api.phonefactor* | **多要素認証ページ**。 このページでは、ユーザーがサインアップやサインインをするときに、電話番号を (文字や音声を使用して) 確認することができます。 |
| *api.selfasserted* | **ソーシャル アカウントのサインアップ ページ**。 このページには、Facebook や Google+ などのソーシャル ID プロバイダーから提供された既存のアカウントを使用してサインアップするときに、ユーザーが入力する必要のあるサインアップ フォームが含まれます。 このページは、パスワード入力フィールドを除いて、上のソーシャル アカウントのサインアップ ページと似ています。 |
| *api.selfasserted.profileupdate* | **プロファイルの更新ページ**。 このページには、ユーザーがプロファイルの更新に使用できるフォームが含まれます。 このページは、パスワード入力フィールドを除いて、上のソーシャル アカウントのサインアップ ページと似ています。 |
| *api.signuporsignin* | **統合されたサインアップ ページまたはサインイン ページ**。  このページは、ユーザーのサインアップとサインインの両方を処理します。ユーザーは、エンタープライズ ID プロバイダー、ソーシャル ID プロバイダー (Facebook や Google+ など)、またはローカル アカウントを使用することができます。

## <a name="next-steps"></a>次のステップ
[リファレンス: B2C の Identity Experience Framework を利用してカスタム ポリシーを操作する方法について](active-directory-b2c-reference-custom-policies-understanding-contents.md)
