---
title: Arkose Labs と Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Azure AD B2C 認証を Arkose Labs と統合して、ボット攻撃、アカウント乗っ取り攻撃、および不正なアカウント開設を防ぐ方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a4d8174cd0bfdb2297099b403fb836210c5529ac
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86170226"
---
# <a name="tutorial-for-configuring-arkose-labs-with-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して Arkose Labs を構成するためのチュートリアル

このチュートリアルでは、Azure AD B2C 認証を Arkose Labs と統合する方法について説明します。 Arkose Labs は、組織がボット攻撃、アカウント乗っ取り攻撃、および不正なアカウント開設を防ぐのを支援します。  

## <a name="prerequisites"></a>前提条件

開始するには、以下が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* お使いの Azure サブスクリプションにリンクされている [Azure AD B2C テナント](tutorial-create-tenant.md)。

## <a name="scenario-description"></a>シナリオの説明

次の図は、Arkose Labs を Azure AD B2C と統合する方法を示しています。

![Arkose Labs のアーキテクチャの図](media/partner-arkose-labs/arkose-architecture-diagram.png)

| 手順  | 説明 |
|---|---|
|1     | ユーザーが、以前に作成したアカウントを使用してサインインします。 ユーザーが [送信] を選択すると、Arkose Labs 適用チャレンジが表示されます。 ユーザーがチャレンジを完了すると、そのステータスが Arkose Labs に送信されてトークンが生成されます。        |
|2     |  Arkose Labs からトークンが Azure AD B2C に送り返されます。       |
|3     |  サインイン フォームが送信される前に、トークンが検証のために Arkose Labs に送信されます。       |
|4     |  Arkose が、チャレンジから成功または失敗の結果を送り返します。       |
|5     |  チャレンジが正常に完了した場合、サインイン フォームが Azure AD B2C に送信され、Azure AD B2C 認証が完了します。       |
|   |   |

## <a name="onboard-with-arkose-labs"></a>Arkose Labs を使用してオンボードする

1. 最初に、[Arkose Labs](https://www.arkoselabs.com/book-a-demo/) にアクセスし、アカウントを作成します。

2. アカウントが作成されたら、 https://dashboard.arkoselabs.com/login に移動します。

3. ダッシュボード内でサイトの設定に移動して、公開キーと秘密キーを検索します。 この情報は、後で Azure AD B2C を構成するために必要になります。

## <a name="integrate-with-azure-ad-b2c"></a>Azure AD B2C との統合

### <a name="part-1--create-blob-storage-to-store-the-custom-html"></a>パート 1 – カスタム HTML を格納する BLOB ストレージを作成する

ストレージ アカウントを作成する場合は、次の手順に従います。  

1. Azure portal にサインインします。

2. Azure サブスクリプションが含まれるディレクトリを必ず使用してください。 上部メニューで  **[ディレクトリ + サブスクリプション]** フィルターを選択し、ご利用のサブスクリプションが含まれるディレクトリを選択します。 このディレクトリは、Azure B2C テナントが含まれるディレクトリとは異なります。

3. Azure portal の左上隅の **[すべてのサービス]** を選択し、 **[ストレージ アカウント]** を検索して選択します。

4.  **[追加]** を選択します。

5.  **[リソース グループ]** の下で  **[新規作成]** を選択し、新しいリソース グループの名前を入力し、 **[OK]** を選択します。

6. ストレージ アカウントの名前を入力します。 選択する名前は、Azure 全体で一意であり、長さは 3 ～ 24 文字でなければならず、数字と小文字のみを使用できます。

7. ストレージ アカウントの場所を選択するか、既定の場所をそのまま使用します。

8. その他の既定値をそのまま使用し、  **[確認および作成]**  >  **[作成]** の順に選択します。

9. ストレージ アカウントの作成後、 **[リソースに移動]** を選択します。

#### <a name="create-a-container"></a>コンテナーを作成する

1. ストレージ アカウントの [概要] ページで   **[BLOB]** を選択します。

2.   **[コンテナー]** を選択し、コンテナーの名前を入力し、  **[BLOB (BLOB 専用の匿名読み取りアクセス)]** を選択し、 **[OK]** を選択します。

#### <a name="enable-cross-origin-resource-sharing-cors"></a>クロスオリジン リソース共有 (CORS) を有効にする

ブラウザーの Azure AD B2C コードでは、最新かつ標準の手法を利用し、ユーザー フローに指定された URL からカスタム コンテンツを読み込みます。 CORS によって、Web ページ上の制限付きリソースを他のドメインから要求することが許可されます。

1. メニューで   **[CORS]** を選択します。

2.   **[許可されたオリジン]** には、「 `https://your-tenant-name.b2clogin.com`」を入力します。 your-tenant-name を Azure AD B2C テナントの名前に置き換えます。 例:  `https://fabrikam.b2clogin.com`。 テナント名を入力するときは、すべて小文字を使用します。

3.  **[許可されたメソッド]** で、 **[GET]** 、 **[PUT]** 、および  **[OPTIONS]** を選択します。

4. **[許可されたヘッダー]** に、アスタリスク (*) を入力します。

5.  **[公開されるヘッダー]** に、アスタリスク (*) を入力します。

6. **[最長有効期間]** には「200」と入力します。

   ![Arkose Labs のサインアップとサインイン](media/partner-arkose-labs/signup-signin-arkose.png)

7. **[保存]** を選択します。

### <a name="part-2--set-up-a-back-end-server"></a>パート 2 – バックエンド サーバーを設定する

Git Bash をダウンロードし、次の手順に従います。

1. 指示に従って [Web アプリを作成](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-php)すると、"おめでとうございます。 App Service に初めての PHP アプリをデプロイしました。" というメッセージが表示されます。

2. ローカル フォルダーを開き、**index.php** ファイルの名前を **verify-token.php** に変更します。

3. 新しく名前を変更したファイル verify-token.php ファイルを開き、次のようにします。

   a. 内容を [GitHub リポジトリ](https://github.com/ArkoseLabs/Azure-AD-B2C)にある verify-token.php ファイルの内容に置き換えます。

   b. 3 行目の <private_key> を、Arkose Labs ダッシュボードから取得した秘密キーに置き換えます。

4. ローカルのターミナル ウィンドウで、変更を Git にコミットし、Git Bash で次のように入力してコード変更を Azure にプッシュします。

   ``git commit -am "updated output"``

   ``git push azure master``  

### <a name="part-3---final-setup"></a>パート 3 – 最終的な設定

#### <a name="store-the-custom-html"></a>カスタム HTML を格納する

1. [GitHub リポジトリ](https://github.com/ArkoseLabs/Azure-AD-B2C)に格納されている index.html ファイルを開きます。

2. `<tenantname>` のすべてのインスタンスを実際の b2c テナント名 (つまり、`<tenantname>.b2clogin.com`) に置き換えます 。 全部で 4 つのインスタンスがあるはずです。

3. `<appname>` を、パート 2 の手順 1 で作成したアプリケーション名に置き換えます。

   ![Arkose Labs Azure CLI を示すスクリーンショット](media/partner-arkose-labs/arkose-azure-cli.png)

4. 64 行目の `<public_key>` を、Arkose Labs ダッシュボードから取得した公開キーに置き換えます。

5. index.html ファイルを、上記で作成した BLOB ストレージにアップロードします。

6. **[ストレージ]**  >  **[コンテナー]**  >  **[アップロード]** の順に移動します。

#### <a name="set-up-azure-ad-b2c"></a>Azure AD B2C を設定する

> [!NOTE]
> まだ持っていない場合は、[お使いの Azure サブスクリプションにリンクされている Azure AD B2C テナント](tutorial-create-tenant.md)を作成します。

1. [こちら](tutorial-create-user-flows.md)の情報に基づいてユーザー フローを作成します。 **[ユーザー フローをテストする]** セクションに達したら、停止します。

2. [ユーザー フロー](user-flow-javascript-overview.md)で JavaScript を有効にします。

3. 同じユーザー フロー ページで、カスタム ページの URL を有効にします。 **[ユーザー フロー]**  >  **[ページ レイアウト]**  >  **[カスタム ページ コンテンツの使用]**  =  **[はい]**  >  **[insert custom page URL] (カスタム ページ URL の挿入)** の順に移動します。
このカスタム ページ URL は、BLOB ストレージ内の index.html ファイルの場所から取得されます  

   ![Arkose Labs ストレージ URL を示すスクリーンショット](media/partner-arkose-labs/arkose-storage-url.png)

## <a name="test-the-user-flow"></a>ユーザー フローをテストする

1. Azure AD B2C テナントを開いて、 **[ポリシー]** で **[ユーザー フロー]** を選択します。

2. 以前に作成したユーザー フローを選択します。

3. **[ユーザー フローを実行します]** を選択し、設定を選択します。

   a. **[アプリケーション]** - 登録済みのアプリを選択します (サンプルは JWT)。

   b. **[応答 URL]** : リダイレクト URL を選択します。

   c. **[ユーザー フローを実行します]** を選択します。

4. サインアップ フローを実行し、アカウントを作成します。

5. サインアウトします。

6. サインアップ フローを実行します。

7. **[続行]** を選択すると、Arkose Labs パズルが表示されます。

## <a name="next-steps"></a>次のステップ

追加情報については、次の記事を参照してください。

- [Azure AD B2C のカスタム ポリシー](custom-policy-overview.md)

- [Azure AD B2C のカスタム ポリシーの概要](custom-policy-get-started.md?tabs=applications)
