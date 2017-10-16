---
title: "System for Cross-Domain Identity Management を使用して Azure Active Directory からユーザーとグループをアプリケーションに自動的にプロビジョニングする | Microsoft Docs"
description: "Azure Active Directory では、SCIM プロトコル仕様のインターフェイスを持つ Web サービスによってアクセスされるアプリケーションや ID ストアにユーザーやグループを自動的にプロビジョニングできます"
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: 4d86f3dc-e2d3-4bde-81a3-4a0e092551c0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2017
ms.author: asmalser
ms.reviewer: asmalser
ms.custom: aaddev;it-pro;oldportal
ms.openlocfilehash: 91978cee88d55c99bcb63c63cdaf01581ae84668
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="using-system-for-cross-domain-identity-management-to-automatically-provision-users-and-groups-from-azure-active-directory-to-applications"></a>System for Cross-Domain Identity Management を使用して Azure Active Directory からユーザーとグループをアプリケーションに自動的にプロビジョニングする

## <a name="overview"></a>概要
Azure Active Directory (Azure AD) では、[Cross-Domain Identity Management (SCIM) 2.0 プロトコル仕様](https://tools.ietf.org/html/draft-ietf-scim-api-19)のインターフェイスを持つ Web サービスによってアクセスされるアプリケーションや ID ストアにユーザーやグループを自動的にプロビジョニングできます。 Azure Active Directory は、割り当てられたユーザーとグループを Web サービスに対して作成、変更、または削除するための要求を送信できます。 Web サービスは、これらの要求を、ターゲット ID ストアに対する操作に変換できます。 

> [!IMPORTANT]
> Microsoft では、この記事で言及している Azure クラシック ポータルを使用するのではなく、Azure Portal の [Azure AD 管理センター](https://aad.portal.azure.com)を使用して Azure AD を管理することをお勧めしています。 



![][0]
*図 1: Azure Active Directory から Web サービスを介して ID ストアにプロビジョニングする*

この機能を Azure AD の "アプリケーションの持ち込み" 機能と組み合わせることで、SCIM Web サービスが提供またはアクセスするアプリケーションに対して、シングル サインオンと自動ユーザー プロビジョニングを実現できます。

Azure Active Directory には、次の 2 つの SCIM のユース ケースがあります。

* **SCIM をサポートするアプリケーションにユーザーとグループをプロビジョニングする** - SCIM 2.0 をサポートし、認証に OAuth ベアラー トークンを使用するアプリケーションは、構成なしで Azure AD と連携します。
* **他の API ベースのプロビジョニングをサポートするアプリケーション用に独自のプロビジョニング ソリューションを構築する** - SCIM 以外のアプリケーションの場合は、ユーザー プロビジョニング用に、SCIM のエンドポイントを作成して、Azure AD SCIM のエンドポイントと、アプリケーションがサポートする API との間で変換を行うことができます。 SCIM エンドポイントの開発を支援するために、SCIM エンドポイントを用意して SCIM メッセージを変換する方法を示すコード サンプルと共に、共通言語基盤 (CLI: Common Language Infrastructure) ライブラリが用意されています。  

## <a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>SCIM をサポートするアプリケーションにユーザーとグループをプロビジョニングする
Azure AD は、[System for Cross-domain Identity Management 2 (SCIM)](https://tools.ietf.org/html/draft-ietf-scim-api-19) Web サービスを実装し、認証用に OAuth ベアラー トークンを受け取るアプリケーションに対し、割り当て済みのユーザーとグループを自動的にプロビジョニングするように構成できます。 SCIM 2.0 仕様の中で、アプリケーションは次の要件を満たす必要があります。

* SCIM プロトコルのセクション 3.3 に従って、ユーザーとグループの作成をサポートする。  
* SCIM プロトコルのセクション 3.5.2 に従って、修正要求によるユーザーとグループの変更をサポートする。  
* SCIM プロトコルのセクション 3.4.1 に従って、既知のリソースの取得をサポートする。  
* SCIM プロトコルのセクション 3.4.2 に従って、ユーザーとグループの照会をサポートする。  既定では、ユーザーの照会には externalId、グループの照会には displayName が使用されます。  
* SCIM プロトコルのセクション 3.4.2 に従って、ID と管理者によるユーザーの照会をサポートする。  
* SCIM プロトコルのセクション 3.4.2 に従って、ID とメンバーによるグループの照会をサポートする。  
* SCIM プロトコルのセクション 2.1 に従って、承認用の OAuth ベアラー トークンを受け取る。

これらの要件との互換性に関する記述については、アプリケーション プロバイダー、またはアプリケーション プロバイダーのドキュメントを確認してください。

### <a name="getting-started"></a>使用の開始
この記事で説明した SCIM プロファイルをサポートするアプリケーションは、Azure AD アプリケーション ギャラリーの "ギャラリー以外のアプリケーション" 機能を使用して Azure Active Directory に接続できます。 接続が完了すると、Azure AD は 20 分ごとに同期処理を実行します。この処理では、割り当て済みのユーザーとグループについてアプリケーションの SCIM エンドポイントに照会し、割り当ての詳細に従ってユーザーとグループを作成または変更します。

**SCIM をサポートするアプリケーションを接続するには:**

1. [Azure ポータル](https://portal.azure.com)にサインインします。 
2. **[Azure Active Directory] > [エンタープライズ アプリケーション] を参照し、**[新しいアプリケーション] > [すべて] > [ギャラリー以外のアプリケーション]** を選択します。
3. アプリケーションの名前を入力し、**[追加]** アイコンをクリックしてアプリ オブジェクトを作成します。
    
  ![][1]
  *図 2: Azure AD アプリケーション ギャラリーの使用*
    
4. 結果の画面で、左側の列の **[プロビジョニング]** タブを選択します。
5. **[プロビジョニング モード]** メニューの **[自動]** を選択します。
    
  ![][2]
  *図 3: Azure ポータルでのプロビジョニングの構成*
    
6. **[テナント URL]** フィールドに、アプリケーションの SCIM エンドポイントの URL を入力します。 例: https://api.contoso.com/scim/v2/
7. SCIM エンドポイントで、Azure AD 以外の発行者からの OAuth ベアラー トークンを必要とする場合は、必要な OAuth ベアラー トークンをオプションの **[シークレット トークン]** フィールドにコピーします。 このフィールドを空白のままにすると、Azure AD は各要求に Azure AD から発行された OAuth ベアラー トークンを含めます。 ID プロバイダーとして Azure AD を使用するアプリケーションは、この Azure AD が発行したトークンを検証できます。
8. **[テスト接続]** ボタンをクリックして、Azure Active Directory による SCIM エンドポイントへの接続を試みます。 試みが失敗した場合は、エラー情報が表示されます。  
9. アプリケーションへの接続の試みが成功した場合は、**[保存]** をクリックして管理者資格情報を保存します。
10. **[マッピング]** セクションには、選択可能な 2 つの属性マッピングのセットがあります。片方はユーザー オブジェクト用であり、他方はグループ オブジェクト用です。 Azure Active Directory からアプリに同期されている属性を確認するには、それぞれを選択します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理でアプリ内のユーザーとアカウントを照合するために使用されます。 [保存] ボタンをクリックして変更をコミットします。

    >[!NOTE]
    >必要に応じて [グループ] マッピングを無効にすることで、グループ オブジェクトの同期を無効にできます。 

11. **[設定]** の **[スコープ]** フィールドは、同期するユーザーまたはグループを定義します。 [Sync only assigned users and groups]\(割り当てたユーザーとグループのみ同期する\) (推奨) を選択すると、**[ユーザーとグループ]** タブに割り当てられたユーザーとグループのみが同期されます。
12. 構成が完了したら、**[プロビジョニング状態]** を **[オン]** に変更します。
13. Azure AD のプロビジョニング サービスを開始するには、**[保存]** をクリックします。 
14. 割り当てられたユーザーとグループのみを同期する (推奨) 場合は、**[ユーザーとグループ]** タブを選択し、同期するユーザーとグループを割り当てます。

初期同期が開始されたら、**[監査ログ]** タブを使用して進行状況を監視できます。このタブには、プロビジョニング サービスがアプリに対して実行しているすべてのアクションが表示されます。 Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-provisioning-reporting)」をご覧ください。

>[!NOTE]
>初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、サービスが実行されている限り約 20 分ごとに実行されます。 


## <a name="building-your-own-provisioning-solution-for-any-application"></a>任意のアプリケーション用の独自のプロビジョニング ソリューションの構築
Azure Active Directory を接続して動作させる SCIM Web サービスを作成することにより、REST または SOAP というユーザー プロビジョニング API を提供するほとんどすべてのアプリケーションのシングル サインオンと自動ユーザー プロビジョニングを実現することができます。

そのしくみは次のとおりです。

1. Azure AD には [Microsoft.SystemForCrossDomainIdentityManagement](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/)という共通言語基盤ライブラリが用意されています。 システム インテグレーターや開発者は、このライブラリを使用して、Azure AD を任意のアプリケーションの ID ストアに接続できる SCIM ベースの Web サービス エンドポイントを作成し、デプロイできます。
2. 標準化されたユーザー スキーマをアプリケーションで必要なユーザー スキーマやプロトコルに対応付けるためのマッピングが Web サービスに実装されます。
3. エンドポイント URL が、Azure AD のアプリケーション ギャラリーにカスタム アプリケーションの一部として登録されます。
4. ユーザーとグループが、Azure AD 内でこのアプリケーションに割り当てられます。 割り当ての際、ユーザーとグループは、対象アプリケーションと同期するためのキューに配置されます。 キューを処理する同期プロセスは、20 分ごとに実行されます。

### <a name="code-samples"></a>コード サンプル
このプロセスをわかりやすくするために、SCIM Web サービス エンドポイントを作成し、自動プロビジョニングの実例を示す [コード サンプル](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) を用意しました。 サンプルの 1 つは、ユーザーとグループを表すコンマ区切り値の行が含まれたファイルを保持するプロバイダーです。  もう 1 つは、アマゾン ウェブ サービス Identity and Access Management サービスで動作するプロバイダーです。  

**前提条件**

* Visual Studio 2013 以降
* [Azure SDK for .NET](https://azure.microsoft.com/downloads/)
* SCIM エンドポイントとして使用される ASP.NET framework 4.5 をサポートしている Windows コンピューター。 このマシンにクラウドからアクセスできる必要があります。
* [無料試用版またはライセンス版の Azure AD Premium が付随した Azure サブスクリプション](https://azure.microsoft.com/services/active-directory/)
* Amazon AWS サンプルには、 [AWS Toolkit for Visual Studio](http://docs.aws.amazon.com/AWSToolkitVS/latest/UserGuide/tkv_setup.html)のライブラリが必要です。 詳細については、サンプルに含まれている README ファイルを参照してください。

### <a name="getting-started"></a>Getting Started (概要)
Azure AD からのプロビジョニング要求を受信できる SCIM エンドポイントを実装する最も簡単な方法は、プロビジョニングするユーザーをコンマ区切り値 (CSV) ファイルに出力するコード サンプルをビルドしてデプロイすることです。

**サンプルの SCIM エンドポイントを作成するには:**

1. [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
2. パッケージを解凍し、Windows コンピューターの C:\AzureAD-BYOA-Provisioning-Samples\ などの場所に配置します。
3. このフォルダーから、Visual Studio で FileProvisioningAgent ソリューションを起動します。
4. **[ツール]、[ライブラリ パッケージ マネージャー]、[パッケージ マネージャー コンソール]** の順に選択し、FileProvisioningAgent プロジェクトに対して下記のコマンドを実行して、ソリューションの参照を解決します。
  ```` 
   Install-Package Microsoft.SystemForCrossDomainIdentityManagement
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   Install-Package Microsoft.Owin.Diagnostics
   Install-Package Microsoft.Owin.Host.SystemWeb
  ````
5. FileProvisioningAgent プロジェクトをビルドします。
6. Windows のコマンド プロンプト アプリケーションを (管理者として) 起動し、**cd** コマンドを使用してディレクトリを **\AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug** フォルダーに変更します。
7. 次のコマンドを実行します。その際、<ip-address> を Windows コンピューターの IP アドレスまたはドメイン名に置き換えます。
  ````   
   FileAgnt.exe http://<ip-address>:9000 TargetFile.csv
  ````
8. **[Windows の設定] の [ネットワークとインターネット]** 設定で、**[Windows ファイアウォール]、[詳細設定]** の順に選択し、ポート 9000 への入力方向のアクセスを許可する**受信規則**を作成します。
9. Windows コンピューターがルーターの内側にある場合は、インターネットに公開されているルーターのポート 9000 と Windows コンピューターのポート 9000 の間でネットワーク アクセス変換を実行するようにルーターを構成する必要があります。 これは、Azure AD がクラウド上にあるこのエンドポイントにアクセスするために必要な措置です。

**Azure AD にサンプルの SCIM エンドポイントを登録するには:**

1. [Azure ポータル](https://portal.azure.com)にサインインします。 
2. **[Azure Active Directory] > [エンタープライズ アプリケーション] を参照し、**[新しいアプリケーション] > [すべて] > [ギャラリー以外のアプリケーション]** を選択します。
3. アプリケーションの名前を入力し、**[追加]** アイコンをクリックしてアプリ オブジェクトを作成します。 作成されるアプリケーション オブジェクトは、単なる SCIM エンドポイントではなく、シングル サインオンのプロビジョニングと実装の対象であるアプリケーションを表しています。
4. 結果の画面で、左側の列の **[プロビジョニング]** タブを選択します。
5. **[プロビジョニング モード]** メニューの **[自動]** を選択します。
    
  ![][2]
  *図 4: Azure ポータルでのプロビジョニングの構成*
    
6. **[テナント URL]** フィールドに、インターネットに公開されている URL と SCIM エンドポイントのポートを入力します。 入力内容は、http://testmachine.contoso.com:9000 または http://<IP アドレス>: 9000/ のようになります。<IP アドレス> は、インターネットに公開されている IP アドレスです。  
7. SCIM エンドポイントで、Azure AD 以外の発行者からの OAuth ベアラー トークンを必要とする場合は、必要な OAuth ベアラー トークンをオプションの **[シークレット トークン]** フィールドにコピーします。 このフィールドを空白のままにすると、Azure AD は各要求に Azure AD から発行された OAuth ベアラー トークンを含めます。 ID プロバイダーとして Azure AD を使用するアプリケーションは、この Azure AD が発行したトークンを検証できます。
8. **[テスト接続]** ボタンをクリックして、Azure Active Directory による SCIM エンドポイントへの接続を試みます。 試みが失敗した場合は、エラー情報が表示されます。  
9. アプリケーションへの接続の試みが成功した場合は、**[保存]** をクリックして管理者資格情報を保存します。
10. **[マッピング]** セクションには、選択可能な 2 つの属性マッピングのセットがあります。片方はユーザー オブジェクト用であり、他方はグループ オブジェクト用です。 Azure Active Directory からアプリに同期されている属性を確認するには、それぞれを選択します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理でアプリ内のユーザーとアカウントを照合するために使用されます。 [保存] ボタンをクリックして変更をコミットします。
11. **[設定]** の **[スコープ]** フィールドは、同期するユーザーまたはグループを定義します。 [Sync only assigned users and groups]\(割り当てたユーザーとグループのみ同期する\) (推奨) を選択すると、**[ユーザーとグループ]** タブに割り当てられたユーザーとグループのみが同期されます。
12. 構成が完了したら、**[プロビジョニング状態]** を **[オン]** に変更します。
13. Azure AD のプロビジョニング サービスを開始するには、**[保存]** をクリックします。 
14. 割り当てられたユーザーとグループのみを同期する (推奨) 場合は、**[ユーザーとグループ]** タブを選択し、同期するユーザーとグループを割り当てます。

初期同期が開始されたら、**[監査ログ]** タブを使用して進行状況を監視できます。このタブには、プロビジョニング サービスがアプリに対して実行しているすべてのアクションが表示されます。 Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-provisioning-reporting)」をご覧ください。

サンプルを確認する最後の手順として、Windows コンピューターの \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug フォルダーにある TargetFile.csv ファイルを開きます。 プロビジョニング プロセスが完了すると、割り当てられ、プロビジョニングされたすべてのユーザーとグループに関する詳細がこのファイルに表示されます。

### <a name="development-libraries"></a>開発ライブラリ
SCIM 仕様に準拠する独自の Web サービスを開発するにあたっては、開発プロセスの時間を短縮できるように、まずは Microsoft が提供する以下のライブラリについて理解してください。 

1. 共通言語基盤 (CLI) ライブラリは、C# など、この基盤をベースにした言語で使用できます。 これらのライブラリの 1 つである [Microsoft.SystemForCrossDomainIdentityManagement.Service](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/) は、次の図に示すインターフェイス Microsoft.SystemForCrossDomainIdentityManagement.IProvider を宣言します。このライブラリを使用する開発者は、通常はプロバイダーとして参照できるクラスを使用してこのインターフェイスを実装します。 開発者は、これらのライブラリを使用して、SCIM 仕様に準拠する Web サービスをデプロイできます。 Web サービスは、インターネット インフォメーション サービス内でホストすることも、実行可能な共通言語基盤アセンブリ内でホストすることもできます。 要求は、プロバイダーのメソッドの呼び出しに変換されます。開発者は、これを任意の ID ストアに作用するようにプログラムできます。
  
  ![][3]
  
2. [Express ルート ハンドラー](http://expressjs.com/guide/routing.html)は、node.js Web サービスに対する (SCIM 仕様で定義された) 呼び出しを表す node.js 要求オブジェクトの解析に使用できます。   

### <a name="building-a-custom-scim-endpoint"></a>カスタム SCIM エンドポイントの構築
開発者は、CLI ライブラリを使用して、共通言語基盤の実行可能アセンブリ内、またはインターネット インフォメーション サービス内にサービスをホストできます。 次に示すのは、http://localhost:9000 のアドレスにある実行可能アセンブリ内にサービスをホストするためのサンプル コードです。 

    private static void Main(string[] arguments)
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
      new DevelopersMonitor();
    Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
      new DevelopersProvider(arguments[1]);
    Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
    try
    {
        webService = new WebService(monitor, provider);
        webService.Start("http://localhost:9000");

        Console.ReadKey(true);
    }
    finally
    {
        if (webService != null)
        {
            webService.Dispose();
            webService = null;
        }
    }
    }

    public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
    {
    private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
    private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

    public WebService(
      Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
      Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
    {
        this.monitor = monitoringBehavior;
        this.provider = providerBehavior;
    }

    public override IMonitor MonitoringBehavior
    {
        get
        {
            return this.monitor;
        }

        set
        {
            this.monitor = value;
        }
    }

    public override IProvider ProviderBehavior
    {
        get
        {
            return this.provider;
        }

        set
        {
            this.provider = value;
        }
    }
    }

このサービスには、HTTP アドレスと、ルート証明機関が次のいずれかであるサーバー認証証明書が設定されている必要があります。 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* Verisign
* WoSign

サーバー認証証明書は、ネットワーク シェル ユーティリティを使用して Windows ホスト上のポートにバインドできます。 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  

ここで、certhash 引数に指定されている値は証明書の拇印で、appid 引数に指定されている値は任意のグローバル一意識別子です。  

インターネット インフォメーション サービス内でサービスをホストする場合は、アセンブリの既定の名前空間に、Startup という名前のクラスを使用して、CLA コード ライブラリ アセンブリをビルドします。  以下に示すのは、このクラスのサンプルです。 

    public class Startup
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

    public Startup()
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
          new DevelopersMonitor();
        Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
          new DevelopersProvider();
        this.starter = 
          new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
            provider, 
            monitor);
    }

    public void Configuration(
      Owin.IAppBuilder builder) // Defined in in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }

### <a name="handling-endpoint-authentication"></a>エンドポイント認証の処理
Azure Active Directory からの要求には、OAuth 2.0 のベアラー トークンが含まれます。   要求を受信するサービスでは、Azure Active Directory の Graph Web サービスへのアクセスに対して、発行者が本来の Azure Active Directory テナントを代理する Azure Active Directory であることを認証する必要があります。  このトークンでは、発行者は、"iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/" のような iss 要求で識別されます。  この例では、要求値のベース アドレス https://sts.windows.net によって発行者が Azure Active Directory であることが識別されているのに対して、相対アドレス セグメント cbb1a5ac-f33b-45fa-9bf5-f37db0fed422 はトークンが代理で発行された Azure Active Directory テナントの一意識別子を示しています。  Azure Active Directory の Graph Web サービスにアクセスするためにトークンが発行された場合は、そのサービスの識別子 00000002-0000-0000-c000-000000000000 がトークンの aud 要求の値に含まれている必要があります。  

Microsoft が提供する CLA ライブラリを使用して SCIM サービスを作成する場合、開発者は、次の手順に従って、Microsoft.Owin.Security.ActiveDirectory パッケージを使用して、Azure Active Directory からの要求を認証できます。 

1. プロバイダーに、Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior プロパティを実装します。そのために、サービスが開始されるたびに呼び出されるメソッドを返すように設定します。 

  ````
    public override Action\<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration\> StartupBehavior
    {
      get
      {
        return this.OnServiceStartup;
      }
    }

    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
      System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
    {
    }
  ````

2. そのメソッドに次のコードを追加します。このコードによって、Azure AD の Graph Web サービスにアクセスするために、指定されたテナントの代わりに Azure Active Directory によって発行されたトークンが、サービスのエンドポイントに対するすべての要求に含まれていると認証されます。 

  ````
    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
      System.Web.Http.HttpConfiguration HttpConfiguration configuration)
    {
      // IFilter is defined in System.Web.Http.dll.  
      System.Web.Http.Filters.IFilter authorizationFilter = 
        new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

      // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
      // System.IdentityModel.Token.Jwt.dll.
      SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
        new TokenValidationParameters()
        {
          ValidAudience = "00000002-0000-0000-c000-000000000000"
        };

      // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
      // Microsoft.Owin.Security.ActiveDirectory.dll
      Microsoft.Owin.Security.ActiveDirectory.
      WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
        TokenValidationParameters = tokenValidationParameters,
        Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                      // identifier for this one.  
      };

      applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
    }
  ````


## <a name="user-and-group-schema"></a>ユーザーとグループのスキーマ
Azure Active Directory は、2 種類のリソースを SCIM Web サービスにプロビジョニングできます。  その 2 種類のリソースは、ユーザーとグループです。  

ユーザー リソースは、スキーマ識別子 urn:ietf:params:scim:schemas:extension:enterprise:2.0:User で識別されます。この識別子については、このプロトコル仕様 (http://tools.ietf.org/html/draft-ietf-scim-core-schema) に記載されています。  Azure Active Directory 内のユーザーの属性を urn:ietf:params:scim:schemas:extension:enterprise:2.0:User リソースの属性に対応付ける既定のマッピングを下の表 1 に示します。  

グループ リソースは、スキーマ識別子 http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group で識別されます。  Azure Active Directory 内のグループの属性を http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group リソースの属性に対応付ける既定のマッピングを下の表 2 に示します。  

### <a name="table-1-default-user-attribute-mapping"></a>表 1: 既定のユーザー属性マッピング
| Azure Active Directory ユーザー | urn:ietf:params:scim:schemas:extension:enterprise:2.0:User |
| --- | --- |
| IsSoftDeleted |active |
| displayName |displayName |
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |title |
| mail |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |manager |
| mobile |phoneNumbers[type eq "mobile"].value |
| objectId |id |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |

### <a name="table-2-default-group-attribute-mapping"></a>表 2: 既定のグループ属性マッピング
| Azure Active Directory グループ | http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group |
| --- | --- |
| displayName |externalId |
| mail |emails[type eq "work"].value |
| mailNickname |displayName |
| members |members |
| objectId |id |
| proxyAddresses |emails[type eq "other"].Value |

## <a name="user-provisioning-and-de-provisioning"></a>ユーザーのプロビジョニングとプロビジョニング解除
次の図は、別の ID ストア内にあるユーザーのライフサイクルを管理するために Azure Active Directory から SCIM サービスに送信されるメッセージを示しています。 また、この図は、そのようなサービスの作成用に Microsoft が提供する CLI ライブラリを使用して実装された SCIM サービスが、要求をプロバイダーのメソッドの呼び出しに変換する方法も示しています。  

![][4]
*図 5: ユーザーのプロビジョニングとプロビジョニング解除のシーケンス*

1. Azure Active Directory は、Azure AD 内のユーザーの mailNickname 属性値に一致する externalId 属性値を持つユーザーをサービスに照会します。 クエリは次の例のようなハイパーテキスト転送プロトコル (HTTP) 要求として表現されます。jyoung は Azure Active Directory 内のユーザーの mailNickname 例です。 
  ````
    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...
  ````
  サービスの作成時に SCIM サービスの実装用に Microsoft が提供する共通言語基盤ライブラリを使用した場合、要求はサービスのプロバイダーの Query メソッドの呼び出しに変換されます。  このメソッドのシグネチャを次に示します。 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);
  ````
  次に示すのは、Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters インターフェイスの定義です。 
  ````
    public interface IQueryParameters: 
      Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
        System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
        { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
      system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
      { get; }
      System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
      { get; }
      string SchemaIdentifier 
      { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
          { get; set; }
        string AttributePath 
          { get; } 
        Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
          { get; }
        string ComparisonValue 
          { get; }
    }

    public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
    {
        Equals
    }
  ````
  externalId 属性に特定の値を持つユーザーを照会する次の例では、Query メソッドに渡される引数の値は次のようになります。 
  * parameters.AlternateFilters.Count: 1
  * parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
  * parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
  * parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"
  * correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

2. ユーザーの mailNickname 属性値に一致する externalId 属性値を持つユーザーを Web サービスに照会したときに、応答でユーザーが返されなかった場合、Azure Active Directory は、Azure Active Directory 内のユーザーに対応するユーザーをプロビジョニングするようにサービスに要求します。  このような要求の例を次に示します。 
  ````
    POST https://.../scim/Users HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas":
      [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
      "externalId":"jyoung",
      "userName":"jyoung",
      "active":true,
      "addresses":null,
      "displayName":"Joy Young",
      "emails": [
        {
          "type":"work",
          "value":"jyoung@Contoso.com",
          "primary":true}],
      "meta": {
        "resourceType":"User"},
       "name":{
        "familyName":"Young",
        "givenName":"Joy"},
      "phoneNumbers":null,
      "preferredLanguage":null,
      "title":null,
      "department":null,
      "manager":null}
  ````
  要求は、SCIM サービスの実装用に Microsoft が提供する共通言語基盤ライブラリによって、サービスのプロバイダーの Create メソッドの呼び出しに変換されます。  Create メソッドには、次のようなシグネチャがあります。 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
      Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
      string correlationIdentifier);
  ````
  ユーザーをプロビジョニングする要求では、resource 引数の値は、Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser クラスのインスタンスになります。これは Microsoft.SystemForCrossDomainIdentityManagement.Schemas ライブラリに定義されています。  ユーザーをプロビジョニングする要求が成功すると、メソッドの実装は、Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser クラスのインスタンスを返し、Identifier プロパティの値に新たにプロビジョニングされたユーザーの一意識別子を設定すると想定されています。  

3. SCIM によってアクセスされる ID ストアに存在することがわかっているユーザーを更新するために、Azure Active Directory は、次のような要求を使用して、そのユーザーの現在の状態をサービスに要求して処理を続行します。 
  ````
    GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
  ````
  サービスの作成時に SCIM サービスの実装用に Microsoft が提供する共通言語基盤ライブラリを使用した場合、要求はサービスのプロバイダーの Retrieve メソッドの呼び出しに変換されます。  Retrieve メソッドのシグネチャを次に示します。 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
    // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
       Retrieve(
         Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
           parameters, 
           string correlationIdentifier);

    public interface 
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        IRetrievalParameters
        {
          Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
            ResourceIdentifier 
              { get; }
    }
    public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
    {
        string Identifier 
          { get; set; }
        string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
          { get; set; }
    }
  ````
  ユーザーの現在の状態を取得する要求の例では、parameters 引数の値として指定されたオブジェクトのプロパティ値は次のようになります。 
  
  * Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

4. 参照属性を更新する場合、Azure Active Directory は、サービスによってアクセスされる ID ストア内の参照属性の現在の値が Azure Active Directory 内のその属性の値と既に一致しているかどうかを判別するために、サービスにクエリを実行します。 ユーザーの場合、この方法で現在の値を照会する属性は、manager 属性のみです。 特定のユーザー オブジェクトの manager 属性に特定の値があるかどうかを判別する要求の例を次に示します。 
  ````
    GET ~/scim/Users?filter=id eq 54D382A4-2050-4C03-94D1-E769F1D15682 and manager eq 2819c223-7f76-453a-919d-413861904646&attributes=id HTTP/1.1
    Authorization: Bearer ...
  ````
  属性のクエリ パラメーター id は、filter クエリ パラメーターの値として指定された式を満たすユーザー オブジェクトが存在する場合は、サービスが (そのリソースの id 属性の値のみが含まれた) urn:ietf:params:scim:schemas:core:2.0:User リソースまたは urn:ietf:params:scim:schemas:extension:enterprise:2.0:User リソースで応答すると想定されていることを示しています。  **id** 属性の値は要求元には既知です。 それは filter クエリ パラメーターの値に含まれています。これを要求する目的は、このようなオブジェクトが存在するかどうかを示す指標としてフィルター式を満たすリソースの最小表現を要求することにあります。   

  サービスの作成時に SCIM サービスの実装用に Microsoft が提供する共通言語基盤ライブラリを使用した場合、要求はサービスのプロバイダーの Query メソッドの呼び出しに変換されます。 parameters 引数の値として指定されたオブジェクトのプロパティ値は次のようになります。 
  
  * parameters.AlternateFilters.Count: 2
  * parameters.AlternateFilters.ElementAt(x).AttributePath: "id"
  * parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
  * parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
  * parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
  * parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
  * parameters.RequestedAttributePaths.ElementAt(0): "id"
  * parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

  上記のインデックスの値は、filter クエリ パラメーターの式の順序に応じて、x の値が 0、y の値が 1 になるか、x の値が 1、y の値が 0 になります。   

5. Azure Active Directory から SCIM サービスに対するユーザーを更新する要求の例を次に示します。 
  ````
    PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas": 
      [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
      "Operations":
      [
        {
          "op":"Add",
          "path":"manager",
          "value":
            [
              {
                "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
  ````
  要求は、SCIM サービスの実装用に Microsoft 共通言語基盤ライブラリによって、サービスのプロバイダーの Update メソッドの呼び出しに変換されます。 Update メソッドのシグネチャを次に示します。 
  ````
    // System.Threading.Tasks.Tasks and 
    // System.Collections.Generic.IReadOnlyCollection<T>
    // are defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
    // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

    System.Threading.Tasks.Task Update(
      Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
      string correlationIdentifier);

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
    {
    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
      PatchRequest 
        { get; set; }
    Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
      ResourceIdentifier 
        { get; set; }        
    }

    public class PatchRequest2: 
      Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
    {
    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
        Operations
        { get;}

    public void AddOperation(
      Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
    }

    public class PatchOperation
    {
    public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
      Name
      { get; set; }

    public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
      Path
      { get; set; }

    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
      { get; }

    public void AddValue(
      Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
    }

    public enum OperationName
    {
      Add,
      Remove,
      Replace
    }

    public interface IPath
    {
      string AttributePath { get; }
      System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
      Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
    }

    public class OperationValue
    {
      public string Reference
      { get; set; }

      public string Value
      { get; set; }
    }
  ````
    ユーザーを更新する要求の例では、patch 引数の値として指定されたオブジェクトのプロパティ値は次のようになります。 
  
  * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
  * (PatchRequest as PatchRequest2).Operations.Count: 1
  * (PatchRequest as PatchRequest2).Operations.ElementAt(0).OperationName: OperationName.Add
  * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Path.AttributePath: "manager"
  * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.Count: 1
  * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Reference: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
  * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Value: 2819c223-7f76-453a-919d-413861904646

6. SCIM サービスによってアクセスされる ID ストアからユーザーのプロビジョニングを解除するために、Azure AD は次のような要求を送信します。 
  ````
    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
  ````
  サービスの作成時に SCIM サービスの実装用に Microsoft が提供する共通言語基盤ライブラリを使用した場合、要求はサービスのプロバイダーの Delete メソッドの呼び出しに変換されます。   このメソッドのシグネチャを次に示します。 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
  ````
  ユーザーのプロビジョニングを解除する要求の例では、resourceIdentifier 引数の値として指定されたオブジェクトのプロパティ値は次のようになります。 
  
  * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="group-provisioning-and-de-provisioning"></a>グループのプロビジョニングとプロビジョニング解除
次の図は、別の ID ストア内にあるグループのライフサイクルを管理するために Azure AD から SCIM サービスに送信されるメッセージを示しています。  これらのメッセージは、ユーザーの場合のメッセージと次の 3 つの点で異なっています。 

* グループ リソースのスキーマは、http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group として識別されます。  
* グループを取得する要求では、要求に対する応答で提供されるリソースから members 属性を除外するよう指定されます。  
* 参照属性に特定の値があるかどうかを判別する要求は、members 属性に関する要求になります。  

![][5]
*図 6: グループのプロビジョニングとプロビジョニング解除のシーケンス*

## <a name="related-articles"></a>関連記事
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)
* [Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](active-directory-saas-app-provisioning.md)
* [ユーザーのプロビジョニング用の属性マッピングのカスタマイズ](active-directory-saas-customizing-attribute-mappings.md)
* [属性マッピングの式の書き方](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [ユーザーのプロビジョニング用のフィルターのスコープ](active-directory-saas-scoping-filters.md)
* [アカウント プロビジョニング通知](active-directory-saas-account-provisioning-notifications.md)
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)

<!--Image references-->
[0]: ./media/active-directory-scim-provisioning/scim-figure-1.PNG
[1]: ./media/active-directory-scim-provisioning/scim-figure-2a.PNG
[2]: ./media/active-directory-scim-provisioning/scim-figure-2b.PNG
[3]: ./media/active-directory-scim-provisioning/scim-figure-3.PNG
[4]: ./media/active-directory-scim-provisioning/scim-figure-4.PNG
[5]: ./media/active-directory-scim-provisioning/scim-figure-5.PNG
