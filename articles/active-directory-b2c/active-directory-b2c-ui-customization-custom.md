---
title: Azure Active Directory B2C でカスタム ポリシーを使用してアプリケーションのユーザー インターフェイスをカスタマイズする | Microsoft Docs
description: Azure Active Directory B2C でカスタム ポリシーを使用してユーザー インターフェイスをカスタマイズする方法について説明します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/18/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0a051b0e853b60dfc1f5b6c3453d9ed8361f1748
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67438816"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でカスタム ポリシーを使用してアプリケーションのユーザー インターフェイスをカスタマイズする

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事を完了すると、独自のブランドと外観を備えたサインアップおよびサインイン カスタム ポリシーが完成します。 Azure Active Directory B2C (Azure AD B2C) を使用すると、ユーザーに表示される HTML および CSS コンテンツをほぼ完全に制御できます。 カスタム ポリシーを使用する場合、UI のカスタマイズは Azure Portal のコントロールではなく XML を使用して構成します。 

## <a name="prerequisites"></a>前提条件

[カスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)に関するページの手順を完了します。 ローカル アカウントでのサインアップとサインインのために作業用カスタム ポリシーを持つ必要があります。

## <a name="page-ui-customization"></a>ページ UI のカスタマイズ

ページ UI のカスタマイズ機能を使用することで、任意のカスタム ポリシーの外観をカスタマイズできます。 さらに、アプリケーションと Azure AD B2C との間で、ブランドと視覚的な一貫性を維持することもできます。

しくみは次のとおりです。Azure AD B2C によって、顧客のブラウザーでコードが実行され、[クロス オリジン リソース共有 (CORS)](https://www.w3.org/TR/cors/) と呼ばれる最新の手法が使用されます。 最初に、カスタマイズされた HTML コンテンツを含むカスタム ポリシーで URL を指定します。 Azure AD B2C により、UI 要素が URL から読み込まれた HTML コンテンツとマージされ、ユーザーにページが表示されます。

## <a name="create-your-html5-content"></a>HTML5 のコンテンツの作成

タイトルに製品のブランド名を使用した HTML コンテンツを作成します。

1. 次の HTML スニペットをコピーします。 これは、 *\<body\>* タグ内に *\<div id="api"\>\</div\>* という空の要素が配置されている適切な形式の HTML5 です。 この要素は、Azure AD B2C コンテンツが挿入される場所を示します。

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Product Brand Name</title>
   </head>
   <body>
       <div id="api"></div>
   </body>
   </html>
   ```

2. コピーしたスニペットをテキスト エディターに貼り付け、*customize-ui.html* という名前を付けてファイルを保存します。

## <a name="create-an-azure-blob-storage-account"></a>Azure BLOB ストレージ アカウントを作成する

>[!NOTE]
> この記事では、Azure Blob Storage を使用してコンテンツをホストします。 コンテンツを Web サーバーにホストすることもできますが、[Web サーバーで CORS を有効にする](https://enable-cors.org/server.html)必要があります。

この HTML コンテンツを Blob Storage にホストするには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. **ハブ** メニューで、 **[新規]**  >  **[ストレージ]**  >  **[ストレージ アカウント]** の順にクリックします。
3. ストレージ アカウント用に一意の**名前**を入力します。
4. **[デプロイ モデル]** は **[リソース マネージャー]** のまま残してかまいません。
5. **[Account Kind]** (アカウントの種類) を **[Blob Storage]** に変更します。
6. **[パフォーマンス]** は **[標準]** のまま残してかまいません。
7. **[レプリケーション]** は **[RA-GRS]** のまま残してかまいません。
8. **[アクセス レベル]** は **[ホット]** のまま残してかまいません。
9. **[ストレージ サービスの暗号化]** は **[無効]** のまま残してかまいません。
10. ストレージ アカウントの**サブスクリプション**を選択します。
11. **リソース グループ**を作成するか、既存のリソース グループを選択します。
12. ストレージ アカウントの**地理的な場所**を選択します。
13. **[作成]** をクリックしてストレージ アカウントを作成します。  
    デプロイが完了したら、**ストレージ アカウント**のブレードが自動的に開きます。

## <a name="create-a-container"></a>コンテナーを作成する

Blob Storage 内にパブリック コンテナーを作成するには、次の手順を実行します。

1. 左側のメニューの **[Blob service]** で **[BLOB]** を選択します。
2. **[+ コンテナー]** をクリックします。
3. **[名前]** に「*root*」と入力します。 ここには任意の名前 (*wingtiptoys* など) を指定できますが、この例ではわかりやすくするために *root* を使用します。
4. **[パブリック アクセス レベル]** で **[BLOB]** を選択し、 **[OK]** を選択します。
5. **root** をクリックして、新しいコンテナーを開きます。
6. **[アップロード]** をクリックします。
7. **[ファイルの選択]** の横にあるフォルダー アイコンをクリックします。
8. 先ほど「ページ UI のカスタマイズ」セクションで作成した **customize-ui.html** に移動し、これを選択します。
9. サブフォルダーにアップロードする場合、 **[詳細設定]** を展開し、 **[アップロード先のフォルダー]** にフォルダー名を入力します。
10. **[アップロード]** を選択します。
11. アップロードした **customize-ui.html** BLOB を選択します。
12. **[URL]** テキスト ボックスの右側にある **[クリップボードにコピー]** アイコンを選択して、URL をクリップボードにコピーします。
13. Web ブラウザーで、コピーした URL に移動して、アップロードした BLOB にアクセスできることを確認します。 アクセスできない場合 (たとえば、`ResourceNotFound` エラーが発生した場合)、コンテナーのアクセスの種類が **[BLOB]** に設定されていることを確認します。

## <a name="configure-cors"></a>CORS を構成する

次の手順を実行して、クロス オリジン リソース共有用に Blob Storage を構成します。

1. メニューで **[CORS]** を選択します。
2. **[許可されるオリジン]** には、`https://your-tenant-name.b2clogin.com` を入力します。 `your-tenant-name`を Azure AD B2C テナントの名前に置き換えます。 たとえば、「 `https://fabrikam.b2clogin.com` 」のように入力します。 テナント名を入力するときは、すべて小文字を使用する必要があります。
3. **[許可されたメソッド]** に、`GET` と `OPTIONS` を両方選択します。
4. **[許可されたヘッダー]** に、アスタリスク (*) を入力します。
5. **[公開されるヘッダー]** に、アスタリスク (*) を入力します。
6. **[最長有効期間]** には「200」と入力します。
7. **[Save]** をクリックします。

## <a name="test-cors"></a>CORS のテスト

準備が整ったことを検証するには、次の手順を実行します。

1. [www.test-cors.org](https://www.test-cors.org/) Web サイトに移動し、目的の URL を **[リモート URL]** ボックスに貼り付けます。
2. **[Send Request]\(要求を送信する\)** をクリックします。  
    エラーが発生した場合、[CORS の設定](#configure-cors)が正しいことを確認します。 場合によっては、ブラウザーのキャッシュをクリアするか、Ctrl + Shift + P キーを押してプライベート ブラウズ セッションを開く必要もあります。

## <a name="modify-the-extensions-file"></a>拡張ファイルを変更する

UI のカスタマイズを構成するには、**ContentDefinition** とその子要素を基本ファイルから拡張ファイルにコピーします。

1. ポリシーの基本ファイルを開きます。 たとえば、*TrustFrameworkBase.xml* などです。
2. **ContentDefinitions** 要素を検索し、その内容全体をコピーします。
3. 拡張ファイルを開きます。 たとえば、*TrustFrameworkExtensions.xml* です。 **BuildingBlocks** 要素を検索します。 要素が存在しない場合は追加します。
4. コピーした **ContentDefinitions** 要素の内容全体を **BuildingBlocks** 要素の子として貼り付けます。 
5. コピーした XML で `Id="api.signuporsignin"` を含む **ContentDefinition** 要素を検索します。
6. **LoadUri** の値を、ストレージにアップロードした HTML ファイルの URL に変更します。 たとえば、「 `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html` 」のように入力します。
    
    カスタム ポリシーは次のようになります。

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

7. 拡張ファイルを保存します。

## <a name="upload-your-updated-custom-policy"></a>更新したカスタム ポリシーをアップロードします。

1. お使いの Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いのテナントを含むディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
4. **[Identity Experience Framework]** を選択します。
2. **[All Policies]** (すべてのポリシー) をクリックします。
3. **[ポリシーのアップロード]** をクリックします。
4. 前に変更した拡張ファイルをアップロードします。

## <a name="test-the-custom-policy-by-using-run-now"></a>**[今すぐ実行]** を使用したカスタム ポリシーのテスト

1. **[Azure AD B2C]** ブレードで、 **[すべてのポリシー]** に移動します。
2. アップロードしたカスタム ポリシーを選択し、 **[Run now]** (今すぐ実行) ボタンをクリックします。
3. メール アドレスを使用してサインアップできることを確認します。

## <a name="reference"></a>リファレンス

### <a name="sample-templates"></a>サンプル テンプレート
ここでは、UI カスタマイズのサンプル テンプレートを示します。

```
git clone https://github.com/azureadquickstarts/b2c-azureblobstorage-client
```

sample_templates/wingtip フォルダーには、次の HTML ファイルが含まれています。

| HTML5 テンプレート | 説明 |
|----------------|-------------|
| *phonefactor.html* | このファイルは、多要素認証ページのテンプレートとして使用します。 |
| *resetpassword.html* | このファイルは、パスワードを忘れた場合のページのテンプレートとして使用します。 |
| *selfasserted.html* | このファイルは、ソーシャル アカウントのサインアップ ページ、ローカル アカウントのサインアップ ページ、またはローカル アカウントのサインイン ページのテンプレートとして使用します。 |
| *unified.html* | このファイルは、統合されたサインアップ ページまたはサインイン ページのテンプレートとして使用します。 |
| *updateprofile.html* | このファイルは、プロファイルの更新ページのテンプレートとして使用します。 |

サンプルを使用するための手順を次に示します。 
1. ローカル コンピューターにリポジトリを複製します。 sample_templates の下にあるテンプレート フォルダーを選択します。 `wingtip` または `contoso` を使用できます。
2. 前のセクションの説明に従い、`css`、`fonts`、および `images` フォルダーの下にあるすべてのファイルを BLOB ストレージにアップロードします。 
3. 次に、`wingtip` または `contoso` (最初の手順で選択した方) のルートにある各 \*.html ファイルを開き、"http://localhost" のすべての出現箇所を、手順 2 でアップロードした css、images、および fonts ファイルの URL に置き換えます。
4. \*.html ファイルを保存し、それらを BLOB ストレージにアップロードします。
5. 次に、先ほど「[拡張ファイルを変更する](#modify-the-extensions-file)」で説明したように、拡張ファイルを変更します。
6. fonts、images、または css が欠落している場合、拡張ポリシーおよび \*.html ファイルで参照を確認してください。

### <a name="content-defintion-ids"></a>コンテンツ定義 ID

「サインアップまたはサインイン カスタム ポリシーを変更する」セクションでは、`api.idpselections` のコンテンツ定義を構成しました。 次の表には、Azure AD B2C Identity Experience Framework で検出されるすべてのコンテンツ定義 ID とその説明を示します。

| コンテンツ定義 ID | 説明 | 
|-----------------------|-------------|
| *api.error* | **エラー ページ**。 このページは、例外またはエラーが発生した場合に表示されます。 |
| *api.idpselections* | **ID プロバイダーの選択ページ**。 このページには、サインイン時にユーザーが選択できる ID プロバイダーの一覧が含まれます。 ID プロバイダーは、エンタープライズ ID プロバイダー、ソーシャル ID プロバイダー (Facebook や Google+ など)、ローカル アカウントのいずれかです。 |
| *api.idpselections.signup* | **サインアップのための ID プロバイダーの選択**。 このページには、サインアップ時にユーザーが選択できる ID プロバイダーの一覧が含まれます。 ID プロバイダーは、エンタープライズ ID プロバイダー、ソーシャル ID プロバイダー (Facebook や Google+ など)、ローカル アカウントのいずれかです。 |
| *api.localaccountpasswordreset* | **パスワードを忘れた場合**。 このページには、ユーザーがパスワードのリセットを開始するために入力する必要があるフォームが含まれます。  |
| *api.localaccountsignin* | **ローカル アカウントのサインイン ページ**。 このページには、メール アドレスまたはユーザー名に基づいたローカル アカウントでサインインするためのサインイン フォームが含まれます。 このフォームには、テキスト入力ボックスとパスワード入力ボックスを含めることができます。 |
| *api.localaccountsignup* | **ローカル アカウントのサインアップ ページ**。 このページには、メール アドレスまたはユーザー名に基づいたローカル アカウントのサインアップに使用されるサインアップ フォームが含まれます。 このフォームには、テキスト入力ボックス、パスワード入力ボックス、ラジオ ボタン、単一選択ドロップダウン ボックス、複数選択チェック ボックスなど、さまざまな入力コントロールを含めることができます。 |
| *api.phonefactor* | **多要素認証ページ**。 このページでは、ユーザーがサインアップまたはサインインするときに、(文字または音声を使用して) 電話番号を確認することができます。 |
| *api.selfasserted* | **ソーシャル アカウントのサインアップ ページ**。 このページには、Facebook や Google+ などのソーシャル ID プロバイダーから提供された既存のアカウントを使用してサインアップするときに、ユーザーが入力する必要のあるサインアップ フォームが含まれます。 このページは、パスワード入力フィールドを除いて、上記のソーシャル アカウントのサインアップ ページに似ています。 |
| *api.selfasserted.profileupdate* | **プロファイルの更新ページ**。 このページには、ユーザーがプロファイルの更新に使用できるフォームが含まれます。 このページは、パスワード入力フィールドを除いて、ソーシャル アカウントのサインアップ ページに似ています。 |
| *api.signuporsignin* | **統合されたサインアップ ページまたはサインイン ページ**。 このページは、ユーザーのサインアップとサインインの両方を処理します。ユーザーは、エンタープライズ ID プロバイダー、ソーシャル ID プロバイダー (Facebook や Google+ など)、またはローカル アカウントを使用することができます。  |

## <a name="next-steps"></a>次の手順

カスタマイズできる UI 要素の詳細については、[組み込みポリシーにおける UI カスタマイズのリファレンス ガイド](active-directory-b2c-reference-ui-customization.md)を参照してください。
