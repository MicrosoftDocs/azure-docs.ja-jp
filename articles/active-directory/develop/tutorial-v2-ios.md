---
title: MSAL for iOS & macOS のチュートリアル - Microsoft ID プラットフォーム | Azure
description: iOS と macOS (Swift) アプリで Microsoft ID プラットフォームを使用してアクセス トークンを必要とする API を呼び出す方法について説明します
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 06/16/2020
ms.author: jmprieur
ms.reviewer: oldalton
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 61a3884ce4d1075dbcac61a0c202c3a393d2d943
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88118146"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-or-macos-app"></a>iOS または macOS アプリからユーザーのサインインを行い、Microsoft Graph を呼び出す

このチュートリアルでは、iOS または macOS アプリを Microsoft ID プラットフォームと統合する方法について説明します。 アプリによって、ユーザーがサインインされ、Microsoft Graph API を呼び出すためのアクセス トークンが取得されて、Microsoft Graph API への要求が行われます。  

このガイドを完了すると、アプリケーションは、個人用の Microsoft アカウント (outlook.com、live.com など) と、Azure Active Directory を使用する会社や組織の職場または学校アカウントのサインインを受け入れるようになります。

>[!NOTE]
> Microsoft ID プラットフォームを初めて使用する場合は、「[iOS または macOS アプリからユーザーのサインインを行い、Microsoft Graph API を呼び出す](quickstart-v2-ios.md)」から始めることをお勧めします。

## <a name="how-this-tutorial-works"></a>このチュートリアルのしくみ

![このチュートリアルで生成されたサンプル アプリの動作の紹介](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

このチュートリアルのアプリでは、ユーザーがサインインされ、ユーザーに代わってデータが取得されます。  このデータは、承認を要求し、Microsoft ID プラットフォームによって保護される API (ここでは Microsoft Graph API) を介してアクセスされます。

具体的には次のとおりです。

* アプリがブラウザーまたは Microsoft Authenticator を介してユーザーをサインインします。
* エンド ユーザーが、アプリケーションから要求されたアクセス許可を受け入れます。
* アプリケーションには、Microsoft Graph API 用のアクセス トークンが発行されます。
* アクセス トークンは、Web API への HTTP 要求に含められます。
* Microsoft Graph の応答を処理します。

このサンプルでは、Microsoft Authentication Library (MSAL) を使用して認証が実装されます。 MSAL により、トークンが自動的に更新され、デバイス上の他のアプリとの間のシングル サインオン (SSO) が提供されて、アカウントが管理されます。

このチュートリアルは、iOS アプリと macOS アプリの両方に適用されます。 これら 2 つのプラットフォームでは、いくつかの手順が異なることに注意してください。 

## <a name="prerequisites"></a>前提条件

- このガイドのアプリをビルドするには、XCode バージョン 11.x 以降が必要です。 XCode は、[Mac App Store](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode のダウンロード URL") からダウンロードできます。
- Microsoft Authentication Library ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc))。 依存関係マネージャーを使用するか、ライブラリを手動で追加できます。 以下の手順でその方法を示します。

このチュートリアルでは新しいプロジェクトを作成します。 代わりに完了したチュートリアルをダウンロードする場合は、コードをダウンロードしてください。
- [iOS のサンプル コード](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
- [macOS のサンプル コード](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

## <a name="create-a-new-project"></a>新しいプロジェクトを作成する

1. Xcode を開き、 **[Create a new Xcode project (新しい Xcode プロジェクトを作成)]** を選択します。
2. iOS アプリの場合は、 **[iOS]**  >  **[単一ビュー アプリ]** を選択し、 **[次へ]** を選択します。
3. macOS アプリの場合は、 **[macOS]**  >  **[Cocoa アプリ]** を選択し、 **[次へ]** を選択します。
4. 製品名を指定します。
5. **[言語]** を **[Swift]** に設定し、 **[次へ]** を選択します。
6. アプリを作成するフォルダーを選択し、 **[作成]** をクリックします。

## <a name="register-your-application"></a>アプリケーションの登録

1. [Azure portal](https://aka.ms/MobileAppReg) に移動します
2. [アプリの登録] ブレードを開き、 **[+ 新しい登録]** をクリックします。
3. アプリの **[名前]** を入力します。リダイレクト URI は設定しません。
4. **[サポートされているアカウントの種類]** で、 **[Accounts in any organizational directory (Any Azure AD directory - Multitenant) and personal Microsoft accounts (e.g. Skype, Xbox)]\(任意の組織ディレクトリ内のアカウント (任意の Azure AD ディレクトリ - マルチテナント) と、個人用の Microsoft アカウント (Skype、Xbox など)\)** を選択します。
5. **[登録]** をクリックします。
6. 表示されたウィンドウの **[管理]** セクションで、 **[認証]** を選択します。

7. 画面の上部付近にある **[新しいエクスペリエンスを試す]** をクリックして新しいアプリの登録エクスペリエンスを開き、 **[+ 新規登録]**  >  **[+ プラットフォームを追加]**  >  **[iOS/macOS]** をクリックします。
    - プロジェクトのバンドル ID を入力します。 コードをダウンロードした場合は `com.microsoft.identitysample.MSALiOS` です。 独自のプロジェクトを作成している場合は、Xcode でそのプロジェクトを選択し、 **[全般]** タブを開きます。 **[ID]** セクションにバンドル ID が表示されます。
8. [`Configure`] をクリックし、後でアプリを構成するときに入力できるように、 **[MSAL 構成]** ページに表示される **MSAL の構成**を保存しておきます。  **[Done]** をクリックします。

## <a name="add-msal"></a>MSAL の追加

次のいずれかの方法を選択して、アプリに MSAL ライブラリをインストールします。

### <a name="cocoapods"></a>CocoaPods

1. [CocoaPods](https://cocoapods.org/) を使用している場合は、まずプロジェクトの `.xcodeproj` ファイルと同じフォルダーに `podfile` という名前の空のファイルを作成してから、`MSAL` をインストールします。 次を `podfile` に追加します。

   ```
   use_frameworks!
   
   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

2. `<your-target-here>` を自分のプロジェクト名に置き換えます。
3. ターミナル ウィンドウで、作成した `podfile` を含むフォルダーに移動し、`pod install` を実行して MSAL ライブラリをインストールします。
4. Xcode を閉じて `<your project name>.xcworkspace` を開き、Xcode にプロジェクトを再度読み込みます。

### <a name="carthage"></a>Carthage

[Carthage](https://github.com/Carthage/Carthage) を使用している場合は、`Cartfile` に追加することで `MSAL` をインストールします。

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

ターミナル ウィンドウから、更新された `Cartfile` と同じディレクトリで次のコマンドを実行して、Carthage でのプロジェクトの依存関係を更新します。

iOS:

```bash
carthage update --platform iOS
```

macOS:

```bash
carthage update --platform macOS
```

### <a name="manually"></a>手動

Git サブモジュールを使用するか、最新のリリースをチェックアウトして、アプリケーションでフレームワークとして使用することもできます。

## <a name="add-your-app-registration"></a>アプリ登録の追加

次に、アプリ登録をコードに追加します。 

まず、次の import ステートメントを `ViewController.swift` と、`AppDelegate.swift` または `SceneDelegate.swift` のファイルの先頭に追加します。

```swift
import MSAL
```

次に、`ViewController.swift` (`viewDidLoad()` の前) に次のコードを追加します。

```swift
// Update the below to your client ID you received in the portal. The below is for running the demo only
let kClientID = "Your_Application_Id_Here"
let kGraphEndpoint = "https://graph.microsoft.com/" // the Microsoft Graph endpoint
let kAuthority = "https://login.microsoftonline.com/common" // this authority allows a personal Microsoft account and a work or school account in any organization’s Azure AD tenant to sign in
    
let kScopes: [String] = ["user.read"] // request permission to read the profile of the signed-in user
    
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
var webViewParameters : MSALWebviewParameters?
var currentAccount: MSALAccount?
```

上記で変更する必要がある値は、`kClientID` に割り当てる[アプリケーション ID](./developer-glossary.md#application-id-client-id) の値だけです。 この値は、このチュートリアルの最初の Azure portal でアプリケーションを登録する手順で保存した、MSAL 構成データの一部です。

## <a name="configure-xcode-project-settings"></a>Xcode プロジェクト設定の構成

新しいキーチェーン グループをプロジェクトの **[Signing & Capabilities]\(署名と機能\)** に追加します。 このキーチェーン グループは、iOS の場合は `com.microsoft.adalcache`、macOS の場合は `com.microsoft.identity.universalstorage` にする必要があります。

![キーチェーン グループの設定方法が表示されている Xcode UI](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro-keychainShare.png)

## <a name="for-ios-only-configure-url-schemes"></a>iOS のみ: URL スキームを構成する

この手順では、サインイン後にユーザーがアプリにリダイレクトできるように、`CFBundleURLSchemes` を登録します。 なお、`LSApplicationQueriesSchemes` では、アプリでの Microsoft Authenticator の使用も許可されます。

Xcode で、`Info.plist` をソース コード ファイルとして開き、`<dict>` セクション内に以下を追加します。 `[BUNDLE_ID]` を、Azure portal で使用した値に置き換えます。コードをダウンロードした場合は `com.microsoft.identitysample.MSALiOS` です。 独自のプロジェクトを作成している場合は、Xcode でそのプロジェクトを選択し、 **[全般]** タブを開きます。 **[ID]** セクションにバンドル ID が表示されます。

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msauth.[BUNDLE_ID]</string>
        </array>
    </dict>
</array>
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>msauthv2</string>
    <string>msauthv3</string>
</array>
```

## <a name="for-macos-only-configure-app-sandbox"></a>macOS のみ: App Sandboxを構成する

1. [Xcode プロジェクトの設定] > **[機能] タブ** >  **[App Sandbox]** に移動します。
2. **[発信接続 (クライアント)]** チェックボックスをオンにします。 

## <a name="create-your-apps-ui"></a>アプリの UI の作成

ここで、次のコードを `ViewController` クラスに追加して、Microsoft Graph API を呼び出すボタン、サインアウトするボタン、および出力を表示するためのテキスト ビューを含む UI を作成します。

### <a name="ios-ui"></a>iOS UI

```swift
var loggingText: UITextView!
var signOutButton: UIButton!
var callGraphButton: UIButton!
var usernameLabel: UILabel!

func initUI() {
        
    usernameLabel = UILabel()
    usernameLabel.translatesAutoresizingMaskIntoConstraints = false
    usernameLabel.text = ""
    usernameLabel.textColor = .darkGray
    usernameLabel.textAlignment = .right
        
    self.view.addSubview(usernameLabel)
        
    usernameLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
    usernameLabel.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -10.0).isActive = true
    usernameLabel.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
    usernameLabel.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        
    // Add call Graph button
    callGraphButton  = UIButton()
    callGraphButton.translatesAutoresizingMaskIntoConstraints = false
    callGraphButton.setTitle("Call Microsoft Graph API", for: .normal)
    callGraphButton.setTitleColor(.blue, for: .normal)
    callGraphButton.addTarget(self, action: #selector(callGraphAPI(_:)), for: .touchUpInside)
    self.view.addSubview(callGraphButton)
        
    callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 120.0).isActive = true
    callGraphButton.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
    callGraphButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        
    // Add sign out button
    signOutButton = UIButton()
    signOutButton.translatesAutoresizingMaskIntoConstraints = false
    signOutButton.setTitle("Sign Out", for: .normal)
    signOutButton.setTitleColor(.blue, for: .normal)
    signOutButton.setTitleColor(.gray, for: .disabled)
    signOutButton.addTarget(self, action: #selector(signOut(_:)), for: .touchUpInside)
    self.view.addSubview(signOutButton)
        
    signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
    signOutButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
    signOutButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        
    let deviceModeButton = UIButton()
    deviceModeButton.translatesAutoresizingMaskIntoConstraints = false
    deviceModeButton.setTitle("Get device info", for: .normal);
    deviceModeButton.setTitleColor(.blue, for: .normal);
    deviceModeButton.addTarget(self, action: #selector(getDeviceMode(_:)), for: .touchUpInside)
    self.view.addSubview(deviceModeButton)
        
    deviceModeButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    deviceModeButton.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
    deviceModeButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
    deviceModeButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        
    // Add logging textfield
    loggingText = UITextView()
    loggingText.isUserInteractionEnabled = false
    loggingText.translatesAutoresizingMaskIntoConstraints = false
        
    self.view.addSubview(loggingText)
        
    loggingText.topAnchor.constraint(equalTo: deviceModeButton.bottomAnchor, constant: 10.0).isActive = true
    loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
    loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: -10.0).isActive = true
    loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: 10.0).isActive = true
}

func platformViewDidLoadSetup() {
                
    NotificationCenter.default.addObserver(self,
                        selector: #selector(appCameToForeGround(notification:)),
                        name: UIApplication.willEnterForegroundNotification,
                        object: nil)
        
}
    
@objc func appCameToForeGround(notification: Notification) {
    self.loadCurrentAccount()
}

```

### <a name="macos-ui"></a>macOS UI

```swift

var callGraphButton: NSButton!
var loggingText: NSTextView!
var signOutButton: NSButton!
    
var usernameLabel: NSTextField!

func initUI() {
        
    usernameLabel = NSTextField()
    usernameLabel.translatesAutoresizingMaskIntoConstraints = false
    usernameLabel.stringValue = ""
    usernameLabel.isEditable = false
    usernameLabel.isBezeled = false
    self.view.addSubview(usernameLabel)
        
    usernameLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 30.0).isActive = true
    usernameLabel.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -10.0).isActive = true
        
    // Add call Graph button
    callGraphButton  = NSButton()
    callGraphButton.translatesAutoresizingMaskIntoConstraints = false
    callGraphButton.title = "Call Microsoft Graph API"
    callGraphButton.target = self
    callGraphButton.action = #selector(callGraphAPI(_:))
    callGraphButton.bezelStyle = .rounded
    self.view.addSubview(callGraphButton)
        
    callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
    callGraphButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true
        
    // Add sign out button
    signOutButton = NSButton()
    signOutButton.translatesAutoresizingMaskIntoConstraints = false
    signOutButton.title = "Sign Out"
    signOutButton.target = self
    signOutButton.action = #selector(signOut(_:))
    signOutButton.bezelStyle = .texturedRounded
    self.view.addSubview(signOutButton)
        
    signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
    signOutButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true
    signOutButton.isEnabled = false
        
    // Add logging textfield
    loggingText = NSTextView()
    loggingText.translatesAutoresizingMaskIntoConstraints = false
        
    self.view.addSubview(loggingText)
        
    loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
    loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
    loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: -10.0).isActive = true
    loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: -10.0).isActive = true
    loggingText.widthAnchor.constraint(equalToConstant: 500.0).isActive = true
    loggingText.heightAnchor.constraint(equalToConstant: 300.0).isActive = true
}

func platformViewDidLoadSetup() {}

```

次に、`ViewController` クラス内でも、`viewDidLoad()` メソッドを次のように置き換えます。

```swift
    override func viewDidLoad() {

        super.viewDidLoad()

        initUI()
        
        do {
            try self.initMSAL()
        } catch let error {
            self.updateLogging(text: "Unable to create Application Context \(error)")
        }
        
        self.loadCurrentAccount()
        self.platformViewDidLoadSetup()
    }
```

## <a name="use-msal"></a>MSAL の使用

### <a name="initialize-msal"></a>MSAL の初期化

`ViewController` クラスに次の `initMSAL` メソッドを追加します。

```swift
    func initMSAL() throws {
        
        guard let authorityURL = URL(string: kAuthority) else {
            self.updateLogging(text: "Unable to create authority URL")
            return
        }
        
        let authority = try MSALAADAuthority(url: authorityURL)
        
        let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
        self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
        self.initWebViewParams()
    }
```

`ViewController` クラスの `initMSAL` メソッドの後ろに以下を追加します。

### <a name="ios-code"></a>iOS コード:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters(authPresentationViewController: self)
    }
```

### <a name="macos-code"></a>macOS コード:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters()
    }
```

### <a name="for-ios-only-handle-the-sign-in-callback"></a>iOS のみ: サインイン コールバックを処理する

`AppDelegate.swift` ファイルを開きます。 サインイン後のコールバックを処理するには、次のように `appDelegate` クラスに `MSALPublicClientApplication.handleMSALResponse` を追加します。

```swift
// Inside AppDelegate...
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}

```

**Xcode 11 を使用している場合は**、代わりに MSAL コールバックを `SceneDelegate.swift` に配置する必要があります。
以前の iOS との互換性を保持するために UISceneDelegate と UIApplicationDelegate の両方をサポートしている場合は、MSAL コールバックを両方のファイルに配置する必要があります。

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```

#### <a name="acquire-tokens"></a>トークンの取得

これで、アプリケーションの UI 処理ロジックを実装し、MSAL を介して対話的にトークンを取得できるようになりました。

MSAL では、トークンを取得するための主要なメソッドとして `acquireTokenSilently()` と `acquireTokenInteractively()` が公開されています。 

- `acquireTokenSilently()` では、ユーザーのサインインが行われ、アカウントが存在すればユーザーとの対話なしにトークンが取得されます。 `acquireTokenSilently()` では、いずれかの MSAL アカウント列挙 API を使用して取得できる有効な `MSALAccount` を指定する必要があります。 このサンプルでは、`applicationContext.getCurrentAccount(with: msalParameters, completionBlock: {})` を使用して現在のアカウントを取得します。 

- ユーザーをサインインしようとすると、`acquireTokenInteractively()` によって常に UI が表示されます。 ブラウザーでセッション Cookie を使用するか、または Microsoft 認証システムでアカウントを使用して、対話型の SSO エクスペリエンスが提供される場合もあります。

以下のコードを `ViewController` クラスに追加します。

```swift
    func getGraphEndpoint() -> String {
        return kGraphEndpoint.hasSuffix("/") ? (kGraphEndpoint + "v1.0/me/") : (kGraphEndpoint + "/v1.0/me/");
    }

    @objc func callGraphAPI(_ sender: AnyObject) {
        
        self.loadCurrentAccount { (account) in
            
            guard let currentAccount = account else {
                
                // We check to see if we have a current logged in account.
                // If we don't, then we need to sign someone in.
                self.acquireTokenInteractively()
                return
            }
            
            self.acquireTokenSilently(currentAccount)
        }
    }

    typealias AccountCompletion = (MSALAccount?) -> Void

    func loadCurrentAccount(completion: AccountCompletion? = nil) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        let msalParameters = MSALParameters()
        msalParameters.completionBlockQueue = DispatchQueue.main
                
        applicationContext.getCurrentAccount(with: msalParameters, completionBlock: { (currentAccount, previousAccount, error) in
            
            if let error = error {
                self.updateLogging(text: "Couldn't query current account with error: \(error)")
                return
            }
            
            if let currentAccount = currentAccount {
                
                self.updateLogging(text: "Found a signed in account \(String(describing: currentAccount.username)). Updating data for that account...")
                
                self.updateCurrentAccount(account: currentAccount)
                
                if let completion = completion {
                    completion(self.currentAccount)
                }
                
                return
            }
            
            self.updateLogging(text: "Account signed out. Updating UX")
            self.accessToken = ""
            self.updateCurrentAccount(account: nil)
            
            if let completion = completion {
                completion(nil)
            }
        })
    }
```

#### <a name="get-a-token-interactively"></a>対話形式でのユーザー トークンの取得

下記のコードでは、`MSALInteractiveTokenParameters` オブジェクトを作成して `acquireToken` を呼び出すことによって、トークンを初めて取得します。 次に、以下を行うコードを追加します。

1. スコープを指定して `MSALInteractiveTokenParameters` を作成する。
2. 作成されたパラメーターを使用して `acquireToken()` を呼び出す。
3. エラーを処理する。 詳細については、[iOS および macOS 用の MSAL のエラー処理に関するガイド](msal-handling-exceptions.md)を参照してください。
4. 正常なケースを処理する。

次のコードを `ViewController` クラスに追加します。

```swift
func acquireTokenInteractively() {
        
    guard let applicationContext = self.applicationContext else { return }
    guard let webViewParameters = self.webViewParameters else { return }
        
    // #1
    let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: webViewParameters)
    parameters.promptType = .selectAccount
        
    // #2
    applicationContext.acquireToken(with: parameters) { (result, error) in
            
        // #3
        if let error = error {
                
            self.updateLogging(text: "Could not acquire token: \(error)")
            return
        }
            
        guard let result = result else {
                
            self.updateLogging(text: "Could not acquire token: No result returned")
            return
        }
            
        // #4
        self.accessToken = result.accessToken
        self.updateLogging(text: "Access token is \(self.accessToken)")
        self.updateCurrentAccount(account: result.account)
        self.getContentWithToken()
    }
}    
```


#### <a name="get-a-token-silently"></a>トークンの自動取得

更新されたトークンを自動で取得するには、次のコードを `ViewController` クラスに追加します。 これにより、`MSALSilentTokenParameters` オブジェクトが作成され、`acquireTokenSilent()` が呼び出されます。

```swift
    
    func acquireTokenSilently(_ account : MSALAccount!) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        /**
         
         Acquire a token for an existing account silently
         
         - forScopes:           Permissions you want included in the access token received
         in the result in the completionBlock. Not all scopes are
         guaranteed to be included in the access token returned.
         - account:             An account object that we retrieved from the application object before that the
         authentication flow will be locked down to.
         - completionBlock:     The completion block that will be called when the authentication
         flow completes, or encounters an error.
         */
        
        let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)
        
        applicationContext.acquireTokenSilent(with: parameters) { (result, error) in
            
            if let error = error {
                
                let nsError = error as NSError
                
                // interactionRequired means we need to ask the user to sign-in. This usually happens
                // when the user's Refresh Token is expired or if the user has changed their password
                // among other possible reasons.
                
                if (nsError.domain == MSALErrorDomain) {
                    
                    if (nsError.code == MSALError.interactionRequired.rawValue) {
                        
                        DispatchQueue.main.async {
                            self.acquireTokenInteractively()
                        }
                        return
                    }
                }
                
                self.updateLogging(text: "Could not acquire token silently: \(error)")
                return
            }
            
            guard let result = result else {
                
                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }
            
            self.accessToken = result.accessToken
            self.updateLogging(text: "Refreshed Access token is \(self.accessToken)")
            self.updateSignOutButton(enabled: true)
            self.getContentWithToken()
        }
    }
```

### <a name="call-the-microsoft-graph-api"></a>Microsoft Graph API を呼び出す 

トークンを取得したら、アプリでそれを HTTP ヘッダーで使用して、許可された要求を Microsoft Graph に対して行うことができます。

| ヘッダー キー    | value                 |
| ------------- | --------------------- |
| 承認 | Bearer \<access-token> |

以下のコードを `ViewController` クラスに追加します。

```swift
    func getContentWithToken() {
        
        // Specify the Graph API endpoint
        let graphURI = getGraphEndpoint()
        let url = URL(string: graphURI)
        var request = URLRequest(url: url!)
        
        // Set the Authorization header for the request. We use Bearer tokens, so we specify Bearer + the token we got from the result
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")
        
        URLSession.shared.dataTask(with: request) { data, response, error in
            
            if let error = error {
                self.updateLogging(text: "Couldn't get graph result: \(error)")
                return
            }
            
            guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {
                
                self.updateLogging(text: "Couldn't deserialize result JSON")
                return
            }
            
            self.updateLogging(text: "Result from Graph: \(result))")
            
            }.resume()
    }
```

Microsoft Graph API の詳細については、[Microsoft Graph API](https://graph.microsoft.com) に関するページを参照してください。

### <a name="use-msal-for-sign-out"></a>サインアウトに MSAL を使用する

次に、サインアウトのサポートを追加します。

> [!Important]
> MSAL でサインアウトすると、ユーザーに関する既知の情報がすべてアプリケーションから削除されるほか、デバイス構成で許可されている場合、デバイスのアクティブなセッションも削除されます。 必要に応じて、ブラウザーからユーザーをサインアウトさせることもできます。

サインアウト機能を追加するには、`ViewController` クラスの内部に次のコードを追加します。 

```swift 
@objc func signOut(_ sender: AnyObject) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache
             */
            
            let signoutParameters = MSALSignoutParameters(webviewParameters: self.webViewParameters!)
            signoutParameters.signoutFromBrowser = false // set this to true if you also want to signout from browser or webview
            
            applicationContext.signout(with: account, signoutParameters: signoutParameters, completionBlock: {(success, error) in
                
                if let error = error {
                    self.updateLogging(text: "Couldn't sign out account with error: \(error)")
                    return
                }
                
                self.updateLogging(text: "Sign out completed successfully")
                self.accessToken = ""
                self.updateCurrentAccount(account: nil)
            })
            
        }
    }
```

### <a name="enable-token-caching"></a>トークンのキャッシュの有効化

MSAL では、既定でアプリのトークンが iOS または macOS のキーチェーンにキャッシュされます。 

トークンのキャッシュを有効化するには:
1. アプリケーションが正しく署名されていることを確認します。
2. Xcode プロジェクトの設定 > **[機能] タブ** >  **[Enable Keychain Sharing]\(キーチェーン共有を有効にする\)** に移動します。
3. [ **+** ] をクリックし、次の **[キーチェーン グループ]** エントリを入力します。3.a iOS の場合は `com.microsoft.adalcache` を入力します。3.b macOS の場合は `com.microsoft.identity.universalstorage` を入力します。

### <a name="add-helper-methods"></a>ヘルパー メソッドの追加
次のヘルパー メソッドを `ViewController` クラスに追加して、サンプルを完成させます。

### <a name="ios-ui"></a>iOS UI:

``` swift
    
    func updateLogging(text : String) {
        
        if Thread.isMainThread {
            self.loggingText.text = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.text = text
            }
        }
    }
    
    func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }
    
    func updateAccountLabel() {
        
        guard let currentAccount = self.currentAccount else {
            self.usernameLabel.text = "Signed out"
            return
        }
        
        self.usernameLabel.text = currentAccount.username
    }
    
    func updateCurrentAccount(account: MSALAccount?) {
        self.currentAccount = account
        self.updateAccountLabel()
        self.updateSignOutButton(enabled: account != nil)
    }
```

### <a name="macos-ui"></a>macOS UI:

```swift
    func updateLogging(text : String) {
        
        if Thread.isMainThread {
            self.loggingText.string = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.string = text
            }
        }
    }
    
    func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }
    
     func updateAccountLabel() {

         guard let currentAccount = self.currentAccount else {
            self.usernameLabel.stringValue = "Signed out"
            return
        }

        self.usernameLabel.stringValue = currentAccount.username ?? ""
        self.usernameLabel.sizeToFit()
     }

     func updateCurrentAccount(account: MSALAccount?) {
        self.currentAccount = account
        self.updateAccountLabel()
        self.updateSignOutButton(enabled: account != nil)
    }
```

### <a name="for-ios-only-get-additional-device-information"></a>iOS の場合のみ: 追加のデバイス情報を取得する

デバイスが共有として構成されているかどうかなどの現在のデバイス構成を読み取るには、次のコードを使用します。

```swift
    @objc func getDeviceMode(_ sender: AnyObject) {
        
        if #available(iOS 13.0, *) {
            self.applicationContext?.getDeviceInformation(with: nil, completionBlock: { (deviceInformation, error) in
                
                guard let deviceInfo = deviceInformation else {
                    self.updateLogging(text: "Device info not returned. Error: \(String(describing: error))")
                    return
                }
                
                let isSharedDevice = deviceInfo.deviceMode == .shared
                let modeString = isSharedDevice ? "shared" : "private"
                self.updateLogging(text: "Received device info. Device is in the \(modeString) mode.")
            })
        } else {
            self.updateLogging(text: "Running on older iOS. GetDeviceInformation API is unavailable.")
        }
    }
```

### <a name="multi-account-applications"></a>マルチアカウント アプリケーション

このアプリは、シングル アカウントのシナリオを対象にビルドされています。 MSAL ではマルチアカウントのシナリオもサポートされますが、その場合はアプリで追加の作業が必要となります。 トークンを必要とする各アクションに使用するアカウントをユーザーが選択するための UI を作成する必要があります。 あるいは、MSAL のすべてのアカウントに対してクエリを実行することで、使用するアカウントを選択するヒューリスティックをアプリで実装することもできます。 例については、`accountsFromDeviceForParameters:completionBlock:` [API](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplication.html#/c:objc(cs)MSALPublicClientApplication(im)accountsFromDeviceForParameters:completionBlock:) を参照してください。

## <a name="test-your-app"></a>アプリをテストする

### <a name="run-locally"></a>ローカルで実行する

アプリをビルドし、テスト デバイスまたはシミュレーターに展開します。 サインインして、Azure AD または個人用 Microsoft アカウントのトークンを取得できるようになります。

ユーザーは、アプリに初めてサインインするときに、Microsoft Identity から、要求されたアクセス許可に同意するよう求められます。  ほとんどのユーザーは同意できますが、一部の Azure AD テナントではユーザーによる同意が無効になっており、全ユーザーに代わって管理者が同意を行う必要があります。 このシナリオをサポートするには、Azure portal でアプリのスコープを登録します。

サインインした後、Microsoft Graph の `/me` エンドポイントから返されたデータがアプリに表示されます。

## <a name="get-help"></a>ヘルプの参照

このチュートリアルまたは Microsoft ID プラットフォームで問題が発生した場合は、[ヘルプとサポート](./developer-support-help-options.md)に関する記事をご覧ください。