---
title: 認証と権限承認
description: アプリまたはサービスの Azure Spatial Anchors に対する認証のさまざまな実行方法と、Spatial Anchors へのアクセスを制限する必要がある制御レベルについて説明します。
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 01065f9ac26599d26d6e2a6979eae1e559a82854
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "97722965"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Azure Spatial Anchors に対する認証と承認

この記事では、アプリまたは Web サービスから Azure Spatial Anchors に対する認証を実行できるさまざまな方法について説明します。 Azure Active Directory (Azure AD) で Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、Spatial Anchors アカウントへのアクセスを制御する方法についても説明します。

## <a name="overview"></a>概要

![Azure Spatial Anchors に対する認証の概要を示す図。](./media/spatial-anchors-authentication-overview.png)

特定の Azure Spatial Anchors アカウントにアクセスするには、まずクライアントで Azure Mixed Reality セキュリティ トークン サービス (STS) からアクセス トークンを取得する必要があります。 STS から取得したトークンは、24 時間有効です。 それには Spatial Anchors サービスでアカウントの承認決定を行うために使用する情報が含まれており、承認されたプリンシパルのみがアカウントにアクセスできるようにします。

アクセス トークンは、アカウント キーまたは Azure AD によって発行されたトークンのいずれかと引き換えに取得できます。

アカウント キーを使用すると、Azure Spatial Anchors サービスの使用をすぐに開始できます。 ただし、アプリケーションを運用環境にデプロイする前に、Azure AD 認証を使用するようにアプリを更新することをお勧めします。

Azure AD 認証トークンは、2 つの方法で取得できます。

- エンタープライズ アプリケーションをビルドしていて、会社の ID システムとして Azure AD を使用している場合は、アプリ内でユーザーベースの Azure AD 認証を使用できます。 その後、既存の Azure AD セキュリティ グループを使用して、Spatial Anchors アカウントにアクセス権を付与します。 組織内のユーザーに直接アクセス権を付与することもできます。
- それ以外の場合は、アプリをサポートしている Web サービスから Azure AD トークンを取得することをお勧めします。 Azure Spatial Anchors にアクセスするための資格情報をクライアント アプリケーションに埋め込むことを回避できるため、運用アプリケーションではこの方法をお勧めします。

## <a name="account-keys"></a>アカウント キー

最も簡単に始める方法は、アカウント キーを使用して自分の Azure Spatial Anchors アカウントにアクセスすることです。 Azure portal で、自分のアカウント キーを取得できます。 自分のアカウントに移動し、 **[キー]** タブを選択します。

![[プライマリ キー] の [コピー] ボタンが強調表示されている [キー] タブを示すスクリーンショット。](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)

2 つのキーを利用できます。 どちらも Spatial Anchors アカウントにアクセスするために同時に有効になっています。 アカウントへのアクセスに使用するキーを定期的に更新することをお勧めします。 2 つの独立した有効なキーを用意することで、ダウンタイムなしで更新を行うことができます。 必要なのは、プライマリ キーとセカンダリ キーを交互に更新することだけです。

SDK には、アカウント キーを使用した認証のサポートが組み込まれています。 必要なのは、`cloudSession` オブジェクトに `AccountKey` プロパティを設定することだけです。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

そのプロパティを設定すると、SDK によって、アクセス トークンのためのアカウント キーの交換とアプリで必要なトークンのキャッシュが処理されるようになります。

> [!WARNING]
> クイック オンボードではアカウント キーを使用することをお勧めしますが、これは開発/プロトタイプ作成中のみ行うことをお勧めします。 アカウント キーが組み込まれたアプリケーションを運用環境で使用することはお勧めしません。 代わりに、次に説明するユーザーベースまたはサービスベースの Azure AD 認証方法を使用してください。

## <a name="azure-ad-user-authentication"></a>Azure AD ユーザー認証

Azure Active Directory ユーザーを対象とするアプリケーションでは、ユーザー用の Azure AD トークンを使用することをお勧めします。 このトークンは、[MSAL](../../active-directory/develop/msal-overview.md) を使用することで取得できます。 [アプリの登録に関するクイックスタート](../../active-directory/develop/quickstart-register-app.md)の手順に従います。以下の手順が含まれます。

**Azure portal で**
1.    アプリケーションをネイティブ アプリケーションとして Azure AD に登録します。 登録の一環として、アプリケーションをマルチテナントにする必要があるかどうかを決める必要があります。 アプリケーションで許可されるリダイレクト URL も指定する必要があります。
1.  **[API のアクセス許可]** タブに移動します。
2.  **[アクセス許可の追加]** を選択します。
    1.  **[所属する組織で使用している API]** タブの **[Mixed Reality Resource Provider]\(Mixed Reality リソース プロバイダー\)** を選択します。
    2.  **[委任されたアクセス許可]** を選択します。
    3.  **[mixedreality]** の下にある **[mixedreality.signin]** を選択します。
    4.  **[アクセス許可の追加]** を選択します.
3.  **[管理者の同意の付与]** を選択します。

2. アプリケーションまたはユーザーにご使用のリソースへのアクセス権を付与します。
   1.    Azure portal で、ご使用の Spatial Anchors リソースに移動します。
   2.    **[アクセス制御 (IAM)]** タブに移動します。
   3.    **[ロールの割り当ての追加]** を選択します。
   1.    [ロールを選択します](#azure-role-based-access-control)。
   2.    **[選択]** ボックスに、アクセス権を割り当てるユーザー、グループ、またはアプリケーションの名前を入力します。
   3.    **[保存]** を選択します。

**コードで**
1.    MSAL の **client ID** と **RedirectUri** パラメーターに自分の Azure AD アプリケーションのアプリケーション ID とリダイレクト URI を使用することを確認します。
2.    テナント情報を設定します。
        1.    自分のアプリケーションで **[所属する組織のみ]** がサポートされる場合は、この値を自分の **テナント ID** または **テナント名** に置き換えます。 例: contoso.microsoft.com。
        2.    自分のアプリケーションで **[任意の組織のディレクトリ内のアカウント]** がサポートされる場合は、この値を **organizations** に置き換えます。
        3.    自分のアプリケーションで **[すべての Microsoft アカウント ユーザー]** がサポートされる場合は、この値を **Common** に置き換えます。
3.    トークン要求で **[スコープ]** を **"`https://sts.<account-domain>//.default`"** に設定します。`<account-domain>` は Azure Spatial Anchors アカウントの **アカウント ドメイン** に置換されます。 米国東部 2 アカウント ドメインの Azure Spatial Anchors アカウントのスコープ例は **"`https://sts.mixedreality.azure.com//.default`"** です。 このスコープでは、アプリケーション上で Mixed Reality セキュリティ トークン サービス (STS) に対してトークンを要求していることを Azure AD に示します。

これらの手順を完了すると、アプリケーションで MSAL から Azure AD トークンを取得できるようになります。 その Azure AD トークンを、`authenticationToken` としてクラウド セッション構成オブジェクトに設定できます。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Azure AD サービス認証

Azure Spatial Anchors を使用するアプリを運用環境にデプロイするには、認証要求を仲介するバックエンド サービスを使用することをお勧めします。 プロセスの概要を以下に示します。

![Azure Spatial Anchors に対する認証の概要を示す図。](./media/spatial-anchors-aad-authentication.png)

ここでは、自分のアプリで独自のメカニズムを使用して、バックエンド サービスに対する認証を行うことを前提としています (たとえば、Microsoft アカウント、PlayFab、Facebook、Google ID、カスタム ユーザー名とパスワードなど)。ユーザーがバックエンド サービスに対して認証された後、そのサービスで Azure AD トークンを取得し、それを Azure Spatial Anchors 用のアクセス トークンと交換してクライアント アプリケーションに返すことができます。

Azure AD アクセス トークンは、[MSAL](../../active-directory/develop/msal-overview.md) を介して取得されます。 [アプリの登録のクイックスタート](../../active-directory/develop/quickstart-register-app.md)に関するページの手順に従います。以下の手順が含まれます。

**Azure portal で**
1.    Azure AD でアプリケーションを登録します。
        1.    Azure portal で、 **[Azure Active Directory]** を選択し、 **[アプリの登録]** を選択します。
        2.    **[新規登録]** を選択します。
        3.    アプリケーションの名前を入力し、アプリケーションの種類として **[Web アプリ/API]** を選択し、サービスの認証 URL を入力します。 **［作成］** を選択します
2.    アプリケーションで、 **[設定]** を選択し、 **[証明書とシークレット]** タブを選択します。新しいクライアント シークレットを作成し、期間を選択し、 **[追加]** を選択します。 シークレット値を必ず保存してください。 それを Web サービスのコードに含める必要があります。
3.    アプリケーションまたはユーザー (および両方) にリソースへのアクセス権を付与します。
        1.    Azure portal で、ご使用の Spatial Anchors リソースに移動します。
        2.    **[アクセス制御 (IAM)]** タブに移動します。
        3.    **[ロールの割り当ての追加]** を選択します。
        4.    [ロールを選択します](#azure-role-based-access-control)。
        5.    **[選択]** ボックスに、アクセス権を割り当てるアプリケーションの名前を入力します (1 つまたは複数)。 アプリのユーザーに Spatial Anchors アカウントとは異なるロールを割り当てる場合は、複数のアプリケーションを Azure AD に登録し、それぞれに別のロールを割り当てます。 次に、ユーザーに適切なロールを使用するために承認ロジックを実装します。

              > [!NOTE]
              > **[ロールの割り当ての追加]** ペインで、 **[アクセスの割り当て先]** から **[Azure AD のユーザー、グループ、サービス プリンシパル]** を選択します。

        6.    **[保存]** を選択します。

**コードで**

>[!NOTE]
> [Spatial Anchors のサンプル アプリ](https://github.com/Azure/azure-spatial-anchors-samples)の一部として利用可能な[サービス サンプル](https://github.com/Azure/azure-spatial-anchors-samples/tree/master/Sharing/SharingServiceSample)を使用できます。

1.    自分の Azure AD アプリケーションのアプリケーション ID、アプリケーション シークレット、およびリダイレクト URI を、MSAL の **client ID**、**secret**、および **RedirectUri** の各パラメーターとして使用することを確認します。
2.    自分の Azure AD テナント ID に対する tenant ID を、MSAL の **authority** パラメーターに設定します。
3.    トークン要求で **[スコープ]** を **"`https://sts.<account-domain>//.default`"** に設定します。`<account-domain>` は Azure Spatial Anchors アカウントの **アカウント ドメイン** に置換されます。 米国東部 2 アカウント ドメインの Azure Spatial Anchors アカウントのスコープ例は **"`https://sts.mixedreality.azure.com//.default`"** です。

これらの手順を完了すると、バックエンド サービスによって Azure AD トークンを取得できます。 その後、クライアントに返される MR トークンに交換できます。 Azure AD トークンを使用した MR トークンの取得は、REST 呼び出しで行われます。 呼び出しの例を次に示します。

```
GET https://sts.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: sts.mixedreality.azure.com
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

Authorization ヘッダーは、次のように書式設定されます。`Bearer <Azure_AD_token>`

応答には、プレーン テキストの MR トークンが含まれます。

その MR トークンは、その後、クライアントに返されます。 クライアント アプリで、それをアクセス トークンとしてクラウド セッション構成に設定できます。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="cwinrt"></a>[C++/WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="azure-role-based-access-control"></a>Azure ロールベースのアクセス制御

アプリケーション、サービス、またはサービスの Azure AD ユーザーに付与されるアクセスのレベルを制御するために、必要に応じて、ご使用の Azure Spatial Anchors アカウントに次の既存のロールを割り当てることができます。

- **Spatial Anchors アカウント所有者**。 このロールが割り当てられているアプリケーションまたはユーザーは、空間アンカーの作成、それらのクエリ、およびそれらの削除を実行できます。 アカウント キーを使用してアカウントを認証すると、認証されたプリンシパルに Spatial Anchors アカウント所有者ロールが割り当てられます。
- **Spatial Anchors アカウント共同作成者**。 このロールが割り当てられているアプリケーションまたはユーザーは、空間アンカーの作成とそれらのクエリを実行できますが、それらを削除することはできません。
- **Spatial Anchors アカウント閲覧者**。 このロールが割り当てられているアプリケーションまたはユーザーは、空間アンカーのクエリのみを実行できます。 新規作成、既存のものの削除、またはそれらのメタデータの更新を行うことはできません。 このロールは、通常は、一部のユーザーが環境をキュレートし、他のユーザーは環境に既に配置されているアンカーの呼び戻しのみを実行できる環境で使用されます。

## <a name="next-steps"></a>次の手順

Azure Spatial Anchors を使用する初めてのアプリを作成します。

> [!div class="nextstepaction"]
> [Unity (HoloLens)](../quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity (iOS)](../quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Unity (Android)](../quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin (Android)](../quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin (iOS)](../quickstarts/get-started-xamarin-ios.md)
