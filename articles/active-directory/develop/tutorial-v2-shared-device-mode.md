---
title: MSAL Android での共有デバイス モードの使用 | Azure
description: Android デバイスを共有モードで実行できるように準備し、現場作業員向けアプリを実行する方法について説明します。
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
ms.openlocfilehash: f49a5703b19a76095c8eafe358742b442725d3d0
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88118248"
---
# <a name="tutorial-use-shared-device-mode-in-your-android-application"></a>チュートリアル:Android アプリケーションで共有デバイス モードを使用する

> [!NOTE]
> この機能はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="developer-guide"></a>開発者ガイド

このガイドでは、Microsoft Authentication Library (MSAL) を使用して Android アプリケーションに共有デバイス モードを実装するための開発者向けガイダンスを提供します。 MSAL を自分の Android アプリに統合する方法、ユーザーをサインインさせる方法、Microsoft Graph を呼び出す方法、ユーザーをサインアウトさせる方法については、[Android 向けの MSAL チュートリアル](./tutorial-v2-android.md)をご覧ください。

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
deviceModeTextView.setText(mSingleAccountApp.isSharedDevice() ?"Shared" :"Non-Shared");
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
> アプリを登録する際は、左側にあるクイックスタート ガイドを使用して、 **[Android]** を選択してください。 それにより、ページが表示され、自分のアプリの**パッケージ名**と**署名ハッシュ**を入力するよう求められます。 アプリ構成を確実に機能させるためには、これらが非常に重要です。 次に、自分のアプリに使用できる構成オブジェクトを受け取ります。これは切り取って、自分の auth_config.json ファイルに貼り付けます。

![アプリの登録画面](media/tutorial-v2-shared-device-mode/register-app.png) **[この変更を行う]** を選択し、クイックスタートで要求される値を Azure portal に入力する必要があります。 それが完了すると、必要なすべての構成ファイルが生成されます。

![アプリの構成情報画面](media/tutorial-v2-shared-device-mode/config-info.png)

## <a name="set-up-a-tenant"></a>テナントを設定する

テストを目的として、自分のテナントで少なくとも 2 人の従業員、つまり 1 人のクラウド デバイス管理者と 1 人のグローバル管理者を設定します。 Azure portal で、組織ロールを変更してクラウド デバイス管理者を設定します。 Azure portal で、 **[Azure Active Directory]**  >  **[ロールと管理者]**  >  **[クラウド デバイス管理者]** を選択して、組織ロールにアクセスします。 デバイスを共有モードにすることができるユーザーを追加します。

## <a name="set-up-an-android-device-in-shared-mode"></a>共有モードで Android デバイスを設定する

### <a name="download-the-authenticator-app"></a>Authenticator アプリをダウンロードする

Google Play ストアから Microsoft Authenticator アプリをダウンロードします。 アプリを既にダウンロードしている場合は、それが最新のバージョンであることを確認します。

### <a name="authenticator-app-settings--registering-the-device-in-the-cloud"></a>Authenticator アプリの設定とクラウドでのデバイスの登録

Authenticator アプリを起動し、メイン アカウント ページに移動します。 **[アカウントの追加]** ページが表示されたら、デバイスを共有する準備は整いました。

![Authenticator のアカウントの追加画面](media/tutorial-v2-shared-device-mode/authenticator-add-account.png)

 右側のメニュー バーを使用して、 **[設定]** ペインに移動します。 **[職場または学校アカウント]** の下にある **[デバイスの登録]** を選択します。

 ![Authenticator のアカウントの追加画面](media/tutorial-v2-shared-device-mode/authenticator-settings.png)

 このボタンをクリックすると、デバイスの連絡先へのアクセスを承認するよう求められます。 これは、デバイスで Android のアカウントが統合されているためです。 **[許可]** を選択します。

 ![Authenticator のアカウントの追加画面](media/tutorial-v2-shared-device-mode/authenticator-allow-screen.png)

クラウド デバイス管理者は、 **[または共有デバイスとして登録]** の下で、組織のメール アドレスを入力する必要があります。 次に、 **[共有デバイスとして登録]** ボタンをクリックし、資格情報を入力します。

![デバイスの登録画面](media/tutorial-v2-shared-device-mode/register-device.png)

![サインイン](media/tutorial-v2-shared-device-mode/sign-in.png)

これでデバイスが共有モードになりました。

![デバイスの登録画面](media/tutorial-v2-shared-device-mode/shared-device-mode-screen.png)

 デバイスでのサインインとサインアウトは、すべてグローバルになります。つまり、デバイス上の MSAL および Microsoft Authenticator と統合されているすべてのアプリにサインインとサインアウトが適用されます。 これで、共有デバイス モード機能が使用されるデバイスにアプリケーションをデプロイできるようになりました。

## <a name="view-the-shared-device-in-the-azure-portal"></a>Azure portal で共有デバイスを確認する

共有モードにしたデバイスは組織によって把握されるようになり、組織のテナントで追跡されます。 共有デバイスを確認するには、Azure portal の [Azure Active Directory] ブレードにある **[結合の種類]** を参照してください。

![Azure portal の [すべてのデバイス] ブレード](media/tutorial-v2-shared-device-mode/registered-device-screen.png)

## <a name="running-the-sample-app"></a>サンプル アプリを実行する

このサンプル アプリケーションは、お客様の組織の Graph API を呼び出す単純なアプリです。 最初の実行では、お客様の従業員アカウントでこのアプリケーションを使用するのが初めてであるため、同意を求めるメッセージが表示されます。

![アプリの構成情報画面](media/tutorial-v2-shared-device-mode/run-app-permissions-requested.png)

## <a name="next-steps"></a>次のステップ

共有モードの詳細については、「[Android デバイスの共有デバイス モード](msal-android-shared-devices.md)」を参照してください