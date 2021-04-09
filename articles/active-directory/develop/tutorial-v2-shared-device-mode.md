---
title: チュートリアル:Microsoft Authentication Library (MSAL) for Android で共有デバイス モードを使用する | Azure
titleSuffix: Microsoft identity platform
description: このチュートリアルでは、Android デバイスを共有モードで実行できるように準備し、現場作業員向けアプリを実行する方法について説明します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 1/15/2020
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 6a173ed4dae9237d8aae991c943817ed70246eea
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101649057"
---
# <a name="tutorial-use-shared-device-mode-in-your-android-application"></a>チュートリアル:Android アプリケーションで共有デバイス モードを使用する

このチュートリアルでは、Android アプリの共有デバイス モードを有効にするうえで必要なコード、Authenticator アプリ、テナント設定について、Android 開発者および Azure Active Directory (Azure AD) テナント管理者を対象に説明します。

このチュートリアルの内容:

> [!div class="checklist"]
> * コード サンプルをダウンロードする
> * 共有デバイス モードを有効にして検出する
> * アカウント モードが単一か複数かを検出する
> * ユーザーの切り替えを検出し、グローバル サインインとグローバル サインアウトを有効にする
> * Azure portal でテナントを設定し、アプリケーションを登録する
> * 共有デバイス モードで Android デバイスを設定する
> * サンプル アプリを実行する

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="developer-guide"></a>開発者ガイド

このチュートリアル セクションでは、Microsoft Authentication Library (MSAL) を使用して Android アプリケーションに共有デバイス モードを実装するための開発者向けガイダンスを提供します。 MSAL を自分の Android アプリに統合する方法、ユーザーをサインインさせる方法、Microsoft Graph を呼び出す方法、ユーザーをサインアウトさせる方法については、[Android 向けの MSAL チュートリアル](./tutorial-v2-android.md)をご覧ください。

### <a name="download-the-sample"></a>サンプルのダウンロード

GitHub から[サンプル アプリケーション](https://github.com/Azure-Samples/ms-identity-android-java/)を複製します。 このサンプルは、[単一または複数アカウント モード](./single-multi-account.md)で動作できます。

### <a name="add-the-msal-sdk-to-your-local-maven-repository"></a>ローカルの Maven リポジトリに MSAL SDK を追加する

サンプル アプリを使用していない場合は、次のように MSAL ライブラリを依存関係として自分の build.gradle ファイルに追加します。

```gradle
dependencies{
  implementation 'com.microsoft.identity.client.msal:1.0.+'
}
```

### <a name="configure-your-app-to-use-shared-device-mode"></a>共有デバイス モードを使用するようアプリを構成する

構成ファイルの設定の詳細については、[構成に関するドキュメント](./msal-configuration.md)を参照してください。

ご自分の MSAL 構成ファイルで `"shared_device_mode_supported"` を `true` に設定します。

複数アカウント モードのサポートを計画していない場合もあります。 共有デバイスを使用しておらず、ユーザーが同時に複数のアカウントを使用してアプリにサインインできる場合がこれに該当します。 その場合は、`"account_mode"` を `"SINGLE"` に設定します。 これにより、お客様のアプリで常に `ISingleAccountPublicClientApplication` が取得されるようになり、MSAL の統合が大幅に簡素化されます。 `"account_mode"` の既定値は `"MULTIPLE"` であるため、`"single account"` モードを使用する場合は、構成ファイルでこの値を変更することが重要です。

サンプル アプリの **app**>**main**>**res**>**raw** ディレクトリに含まれている auth_config.json ファイルの例を次に示します。

```json
{
 "client_id":"Client ID after app registration at https://aka.ms/MobileAppReg",
 "authorization_user_agent":"DEFAULT",
 "redirect_uri":"Redirect URI after app registration at https://aka.ms/MobileAppReg",
 "account_mode":"SINGLE",
 "broker_redirect_uri_registered": true,
 "shared_device_mode_supported": true,
 "authorities":[
  {
   "type":"AAD",
   "audience":{
     "type": "AzureADandPersonalMicrosoftAccount",
     "tenant_id":"common"
   }
  }
 ]
}
```

### <a name="detect-shared-device-mode"></a>共有デバイス モードを検出する

共有デバイス モードを使用すると、複数の従業員で共有できるように Android デバイスを構成しながら、Microsoft ID に基づくデバイス管理を実現できます。 従業員は自分のデバイスにサインインし、顧客情報にすばやくアクセスできます。 シフトやタスクが終了した従業員は共有デバイスのすべてのアプリからワンクリックでサインアウトでき、そのデバイスは次の従業員がすぐに使用できるようになります。

`isSharedDevice()` を使用すると、アプリが共有デバイス モードのデバイスで実行されているかどうかを特定できます。 お客様のアプリでは、このフラグを使用して、適宜 UX を変更する必要があるかどうかを特定できます。

次のコード スニペットは `isSharedDevice()` の使用方法を示しています。  これは、サンプル アプリの `SingleAccountModeFragment` クラスに含まれています。

```Java
deviceModeTextView.setText(mSingleAccountApp.isSharedDevice() ? "Shared" : "Non-Shared");
```

### <a name="initialize-the-publicclientapplication-object"></a>PublicClientApplication オブジェクトを初期化する

MSAL 構成ファイルで `"account_mode":"SINGLE"` を設定した場合、返されるアプリケーション オブジェクトを `ISingleAccountPublicCLientApplication` として安全にキャストできます。

```java
private ISingleAccountPublicClientApplication mSingleAccountApp;

/*Configure your sample app and save state for this activity*/
PublicClientApplication.create(this.getApplicationCOntext(),
  R.raw.auth_config,
  new PublicClientApplication.ApplicationCreatedListener(){
  @Override
  public void onCreated(IPublicClientApplication application){
  mSingleAccountApp = (ISingleAccountPublicClientApplication)application;
  loadAccount();
  }
  @Override
  public void onError(MsalException exception{
  /*Fail to initialize PublicClientApplication */
  }
});
```

### <a name="detect-single-vs-multiple-account-mode"></a>アカウント モードが単一か複数かを検出する

共有デバイス上で現場従業員のみが使用するアプリを作成している場合は、単一アカウント モードのみをサポートするようにアプリを作成することをお勧めします。 これには、医療記録アプリ、請求書アプリ、大部分の基幹業務アプリなどの、タスクに重点を置いたほとんどのアプリケーションが含まれます。 これにより、SDK の多くの機能に対応する必要がなくなるため、開発が簡単になります。

お客様のアプリが複数アカウントと共有デバイス モードをサポートしている場合は、次に示すように、種類のチェックを実行し、適切なインターフェイスにキャストする必要があります。

```java
private IPublicClientApplication mApplication;

        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        } else if (mApplication instanceOf    ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>サインインしているユーザーを取得し、デバイスでユーザーが変更されたかどうかを特定する

`loadAccount` メソッドでは、サインインしているユーザーのアカウントを取得します。 `onAccountChanged` メソッドでは、サインインしているユーザーが変更されたかどうかを特定し、その場合はクリーンアップします。

```java
private void loadAccount()
{
  mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback()
  {
    @Overide
    public void onAccountLoaded(@Nullable IAccount activeAccount)
    {
      if (activeAccount != null)
      {
        signedInUser = activeAccount;
        mSingleAccountApp.acquireTokenSilentAsync(SCOPES,"http://login.microsoftonline.com/common",getAuthSilentCallback());
      }
    }
    @Override
    public void on AccountChanged(@Nullable IAccount priorAccount, @Nullable Iaccount currentAccount)
    {
      if (currentAccount == null)
      {
        //Perform a cleanup task as the signed-in account changed.
        updateSingedOutUI();
      }
    }
    @Override
    public void onError(@NonNull Exception exception)
    {
    }
  }
}
```

### <a name="globally-sign-in-a-user"></a>ユーザーをグローバルにサインインさせる

Authenticator アプリを使用し、デバイス全体でユーザーを MSAL が使用される別のアプリにサインインさせるには、次のようにします。

```java
private void onSignInClicked()
{
  mSingleAccountApp.signIn(getActivity(), SCOPES, null, getAuthInteractiveCallback());
}
```

### <a name="globally-sign-out-a-user"></a>ユーザーをグローバルにサインアウトさせる

サインインしているアカウントを削除し、キャッシュされているトークンをアプリだけでなく共有デバイス モードのデバイスからもクリアするには、次のようにします。

```java
private void onSignOutClicked()
{
  mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback()
  {
    @Override
    public void onSignOut()
    {
      updateSignedOutUI();
    }
    @Override
    public void onError(@NonNull MsalException exception)
    {
      /*failed to remove account with an exception*/
    }
  });
}
```

## <a name="administrator-guide"></a>管理者ガイド

次の手順では、Azure portal で自分のアプリケーションを設定する方法と、自分のデバイスを共有デバイス モードにする方法について説明します。

### <a name="register-your-application-in-azure-active-directory"></a>アプリケーションを Azure Active Directory に登録する

まず、自分のアプリケーションを所属する組織のテナントに登録します。 次に、アプリケーションが正しく動作するように、auth_config.json に以下の値を指定します。

この方法の詳細については、「[アプリケーションの登録](./tutorial-v2-android.md#register-your-application)」を参照してください。

> [!NOTE]
> アプリを登録する際は、左側にあるクイックスタート ガイドを使用して、 **[Android]** を選択してください。 それにより、ページが表示され、自分のアプリの **パッケージ名** と **署名ハッシュ** を入力するよう求められます。 アプリ構成を確実に機能させるためには、これらが非常に重要です。 次に、自分のアプリに使用できる構成オブジェクトを受け取ります。これは切り取って、自分の auth_config.json ファイルに貼り付けます。

:::image type="content" source="media/tutorial-v2-shared-device-mode/register-app.png" alt-text="Azure portal クイックスタートの Android アプリの構成ページ":::

**[この変更を行う]** を選択し、クイックスタートで要求される値を Azure portal に入力する必要があります。 それが完了すると、必要なすべての構成ファイルが生成されます。

:::image type="content" source="media/tutorial-v2-shared-device-mode/config-info.png" alt-text="Azure portal クイックスタートのプロジェクト構成ページ":::

## <a name="set-up-a-tenant"></a>テナントを設定する

テストを目的として、自分のテナントで少なくとも 2 人の従業員、つまり 1 人のクラウド デバイス管理者と 1 人のグローバル管理者を設定します。 Azure portal で、組織ロールを変更してクラウド デバイス管理者を設定します。 Azure portal で、 **[Azure Active Directory]**  >  **[ロールと管理者]**  >  **[クラウド デバイス管理者]** を選択して、組織ロールにアクセスします。 デバイスを共有モードにすることができるユーザーを追加します。

## <a name="set-up-an-android-device-in-shared-mode"></a>共有モードで Android デバイスを設定する

### <a name="download-the-authenticator-app"></a>Authenticator アプリをダウンロードする

Google Play ストアから Microsoft Authenticator アプリをダウンロードします。 アプリを既にダウンロードしている場合は、それが最新のバージョンであることを確認します。

### <a name="authenticator-app-settings--registering-the-device-in-the-cloud"></a>Authenticator アプリの設定とクラウドでのデバイスの登録

Authenticator アプリを起動し、メイン アカウント ページに移動します。 **[アカウントの追加]** ページが表示されたら、デバイスを共有する準備は整いました。

:::image type="content" source="media/tutorial-v2-shared-device-mode/authenticator-add-account.png" alt-text="Authenticator のアカウントの追加画面":::

右側のメニュー バーを使用して、 **[設定]** ペインに移動します。 **[職場または学校アカウント]** の下にある **[デバイスの登録]** を選択します。

:::image type="content" source="media/tutorial-v2-shared-device-mode/authenticator-settings.png" alt-text="Authenticator 設定画面":::

このボタンをクリックすると、デバイスの連絡先へのアクセスを承認するよう求められます。 これは、デバイスで Android のアカウントが統合されているためです。 **[許可]** を選択します。

:::image type="content" source="media/tutorial-v2-shared-device-mode/authenticator-allow-screen.png" alt-text="Authenticator にアクセスを許可することへの確認画面":::

クラウド デバイス管理者は、 **[または共有デバイスとして登録]** の下で、組織のメール アドレスを入力する必要があります。 次に、 **[共有デバイスとして登録]** ボタンをクリックし、資格情報を入力します。

:::image type="content" source="media/tutorial-v2-shared-device-mode/register-device.png" alt-text="アプリのデバイス登録画面":::

:::image type="content" source="media/tutorial-v2-shared-device-mode/sign-in.png" alt-text="Microsoft サインイン ページを表示するアプリのスクリーンショット":::

これでデバイスが共有モードになりました。

:::image type="content" source="media/tutorial-v2-shared-device-mode/shared-device-mode-screen.png" alt-text="共有デバイス モードが有効であることを示すアプリ画面":::

 デバイスでのサインインとサインアウトは、すべてグローバルになります。つまり、デバイス上の MSAL および Microsoft Authenticator と統合されているすべてのアプリにサインインとサインアウトが適用されます。 これで、共有デバイス モード機能が使用されるデバイスにアプリケーションをデプロイできるようになりました。

## <a name="view-the-shared-device-in-the-azure-portal"></a>Azure portal で共有デバイスを確認する

共有モードにしたデバイスは組織によって把握されるようになり、組織のテナントで追跡されます。 共有デバイスを確認するには、Azure portal の [Azure Active Directory] ブレードにある **[結合の種類]** を参照してください。

:::image type="content" source="media/tutorial-v2-shared-device-mode/registered-device-screen.png" alt-text="Azure portal に表示される [すべてのデバイス] ペイン":::

## <a name="running-the-sample-app"></a>サンプル アプリを実行する

このサンプル アプリケーションは、お客様の組織の Graph API を呼び出す単純なアプリです。 最初の実行では、お客様の従業員アカウントでこのアプリケーションを使用するのが初めてであるため、同意を求めるメッセージが表示されます。

:::image type="content" source="media/tutorial-v2-shared-device-mode/run-app-permissions-requested.png" alt-text="アプリケーション構成情報画面":::

## <a name="next-steps"></a>次のステップ

Microsoft Authentication Library と Android デバイスの共有デバイス モードの詳しい使用方法をご覧ください。

> [!div class="nextstepaction"]
> [Android デバイスの共有デバイス モード](msal-android-shared-devices.md)
