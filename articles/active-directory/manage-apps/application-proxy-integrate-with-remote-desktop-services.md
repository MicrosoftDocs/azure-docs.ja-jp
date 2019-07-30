---
title: Azure AD アプリ プロキシを使用したリモート デスクトップの発行 | Microsoft Docs
description: Azure AD アプリケーション プロキシ コネクタの基本について説明します。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mimart
ms.custom: it-pro
ms.reviewer: harshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6ca64e2de5734c567173fc735776074f4c87fbc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108459"
---
# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>Azure AD アプリケーション プロキシを使用したリモート デスクトップの発行

リモート デスクトップ サービスと Azure AD アプリケーション プロキシが連携し、企業ネットワークの外にいるユーザーの生産性を向上させます。 

この記事の対象読者は次のとおりです。
- リモート デスクトップ サービスを通じてオンプレミスのアプリケーションを発行し、エンド ユーザーにより多くのアプリケーションを提供することを希望している、現在アプリケーション プロキシを使用しているお客様。
- Azure AD アプリケーション プロキシを使用してデプロイの攻撃対象領域を削減することを希望している、現在リモート デスクトップ サービスを使用しているお客様。 このシナリオでは、2 段階検証と条件付きアクセス制御の限定セットを RDS に付与します。

## <a name="how-application-proxy-fits-in-the-standard-rds-deployment"></a>アプリケーション プロキシが RDS の標準デプロイにどのように適合するか

RDS の標準デプロイには、Windows Server で実行されるさまざまなリモート デスクトップ ロール サービスが含まれています。 [リモート デスクトップ サービスのアーキテクチャ](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture)を見ると、複数のデプロイ オプションがあります。 他の RDS デプロイ オプションとは異なり、[Azure AD アプリケーション プロキシを使用した RDS デプロイ](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) (次の図に示します) には、コネクタ サービスを実行しているサーバーからの永続的な送信接続があります。 その他のデプロイでは、ロード バランサーを介した着信接続が開いたままになります。

![アプリケーション プロキシは RDS VM とパブリック インターネットの間に位置する](./media/application-proxy-integrate-with-remote-desktop-services/rds-with-app-proxy.png)

RDS デプロイでは、RD Web ロールと RD ゲートウェイ ロールはインターネットに接続されたコンピューターで実行されます。 これらのエンドポイントは次の理由で公開されています。
- RD Web は、アクセスできるさまざまなオンプレミスのアプリケーションとデスクトップにサインインして表示するためのパブリック エンドポイントをユーザーに提供します。 リソースを選ぶと、OS 上のネイティブ アプリを使用して RDP 接続が作成されます。
- ユーザーが RDP 接続を起動すると、RD ゲートウェイが関与するようになります。 RD ゲートウェイは、インターネット経由で送信される暗号化された RDP トラフィックを処理し、ユーザーが接続しているオンプレミスのサーバーに渡します。 このシナリオでは、RD ゲートウェイの受信トラフィックは Azure AD アプリケーション プロキシからのものです。

>[!TIP]
>これまでに RDS をデプロイしていないか、開始する前に詳細情報が必要な場合は、[Azure Resource Manager と Azure Marketplace で RDS をシームレスにデプロイする](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure)方法をご確認ください。

## <a name="requirements"></a>必要条件

- Web クライアントはアプリケーション プロキシをサポートしていないため、リモート デスクトップ Web クライアント以外のクライアントを使用します。

- RD Web と RD ゲートウェイの両方のエンドポイントが同じコンピューター上にあり、ルートが共通である必要があります。 RD Web と RD ゲートウェイはアプリケーション プロキシで単一のアプリケーションとして発行されるため、2 つのアプリケーション間でシングル サインオン エクスペリエンスを実現できます。

- [RDS をデプロイ](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure)し、[アプリケーション プロキシを有効にしている](application-proxy-add-on-premises-application.md)必要があります。

- このシナリオでは、エンド ユーザーが、RD Web ページを通じて接続する Windows 7 または Windows 10 上の Internet Explorer にアクセスすることを前提としています。 その他のオペレーティング システムに対応する必要がある場合は、「[その他のクライアント構成のサポート](#support-for-other-client-configurations)」をご覧ください。

- RD Web を発行するときは、内部 FQDN と外部 FQDN を同じにすることをお勧めします。 内部 FQDN と外部 FQDN が異なる場合は、クライアントが無効なリンクを受け取るのを避けるため、要求ヘッダー変換を無効にする必要があります。 

- Internet Explorer で RDS ActiveX アドオンを有効にしてください。

- Azure AD の事前認証フローでは、ユーザーは **[RemoteApp およびデスクトップ]** ウィンドウで自分に公開されているリソースにのみ接続できます。 ユーザーは **[リモート PC に接続]** ウィンドウを使用してデスクトップに接続できません。

## <a name="deploy-the-joint-rds-and-application-proxy-scenario"></a>RDS とアプリケーション プロキシの共同デプロイのシナリオ

RDS と Azure AD アプリケーション プロキシを自分の環境用に設定した後、2 つのソリューションを結合する手順に従います。 これらの手順では、Web に接続された 2 つの RDS エンドポイント (RD Web と RD ゲートウェイ) をアプリケーションとして発行し、RDS 上のトラフィックをアプリケーション プロキシに転送する方法を説明します。

### <a name="publish-the-rd-host-endpoint"></a>RD ホスト エンドポイントを発行する

1. 次の値で[新しいアプリケーション プロキシ アプリケーションを発行](application-proxy-add-on-premises-application.md)します。
   - [内部 URL]: `https://\<rdhost\>.com/`。 `\<rdhost\>` は、RD Web と RD ゲートウェイが共有する共通のルートです。
   - 外部 URL:このフィールドは、アプリケーションの名前に基づいて自動的に設定されますが、変更することもできます。 ユーザーは、RDS にアクセスするときにこの URL に移動します。
   - [事前認証方法]:Azure Active Directory
   - [ヘッダーの URL を変換する]:いいえ
2. 発行した RD アプリケーションにユーザーを割り当てます。 すべてのユーザーが RDS へのアクセス権を持っていることもご確認ください。
3. アプリケーションのシングル サインオン方式は、 **[Azure AD シングル サインオンが無効]** のままにします。 ユーザーは、Azure AD に対して 1 回と RD Web に対して 1 回認証を求められますが、RD ゲートウェイに対してはシングル サインオンを使用できます。
4. **[Azure Active Directory]** を選択してから、 **[アプリの登録]** を選択します。 一覧からアプリを選択します。
5. **[管理]** 下にある **[ブランド]** を選択します。
6. RD Web エンドポイント (`https://\<rdhost\>.com/RDWeb` など) を参照するように **[ホーム ページ URL]** フィールドを更新します。

### <a name="direct-rds-traffic-to-application-proxy"></a>RDS トラフィックをアプリケーション プロキシに転送する

RDS デプロイに管理者として接続し、デプロイの RD ゲートウェイ サーバーの名前を変更します。 この構成により、Azure AD アプリケーション プロキシ サービスを介して接続が行われるようになります。

1. RD 接続ブローカーのロールを実行している RDS サーバーに接続します。
2. **サーバー マネージャー**を起動します。
3. 左側のペインで **[リモート デスクトップ サービス]** を選択します。
4. **[概要]** を選択します。
5. [展開の概要] セクションで、ドロップダウン メニューを選択し、 **[展開プロパティの編集]** を選択します。
6. [RD ゲートウェイ] タブで、 **[サーバー名]** フィールドを、アプリケーション プロキシで RD ホスト エンドポイントに対して設定した外部 URL に変更します。
7. **[ログオン方法]** フィールドを **[パスワード認証]** に変更します。

   ![RDS の [展開プロパティ] 画面](./media/application-proxy-integrate-with-remote-desktop-services/rds-deployment-properties.png)

8. 各コレクションに対してこのコマンドを実行します。 *\<yourcollectionname\>* と *\<proxyfrontendurl\>* は、実際の情報に置き換えてください。 このコマンドは、RD Web と RD ゲートウェイの間のシングル サインオンを有効にし、パフォーマンスを最適化します。

   ```
   Set-RDSessionCollectionConfiguration -CollectionName "<yourcollectionname>" -CustomRdpProperty "pre-authentication server address:s:<proxyfrontendurl>`nrequire pre-authentication:i:1"
   ```

   **例:**
   ```
   Set-RDSessionCollectionConfiguration -CollectionName "QuickSessionCollection" -CustomRdpProperty "pre-authentication server address:s:https://remotedesktoptest-aadapdemo.msappproxy.net/`nrequire pre-authentication:i:1"
   ```
   >[!NOTE]
   >上記のコマンドでは、"`nrequire" でバッククォートを使用しています。

9. カスタム RDP プロパティの変更を検証するには、またはこのコレクションの RDWeb からダウンロードされる RDP ファイルの内容を表示するには、次のコマンドを実行します。
    ```
    (get-wmiobject -Namespace root\cimv2\terminalservices -Class Win32_RDCentralPublishedRemoteDesktop).RDPFileContents
    ```

リモート デスクトップを構成したため、Azure AD アプリケーション プロキシはインターネットに接続している RDS のコンポーネントを引き継ぎます。 RD Web と RD ゲートウェイのコンピューター上のインターネットに接続しているその他のパブリック エンドポイントは削除できます。

## <a name="test-the-scenario"></a>シナリオをテストする

Windows 7 または 10 のコンピューターで Internet Explorer を使用してシナリオをテストします。

1. 設定する外部 URL に移動するか、[MyApps パネル](https://myapps.microsoft.com)でアプリケーションを検索します。
2. Azure Active Directory に対する認証を求められます。 アプリケーションに割り当てたアカウントを使用します。
3. RD Web に対する認証を求められます。
4. RDS 認証が成功すると、目的のデスクトップまたはアプリケーションを選び、作業を開始できます。

## <a name="support-for-other-client-configurations"></a>その他のクライアント構成のサポート

この記事で説明する構成は、Internet Explorer および RDS ActiveX アドオンを備えた Windows 7 または Windows 10 ユーザーを対象としています。 ただし、必要に応じて、その他のオペレーティング システムまたはブラウザーを使用することもできます。 異なる点は、使用する認証方法です。

| 認証方法 | サポートされているクライアント構成 |
| --------------------- | ------------------------------ |
| 事前認証    | Internet Explorer と RDS ActiveX アドオンを使用する Windows 7 または Windows 10 |
| パススルー | Microsoft リモート デスクトップ アプリケーションをサポートするその他の任意のオペレーティング システム |

事前認証フローでは、パススルー フローよりも高い安全性が提供されます。 事前認証では、オンプレミスのリソースにシングル サインオン、条件付きアクセス、2 段階認証などの Azure AD 認証機能を使用できます。 また、認証されたトラフィックのみが、ネットワークに到達できます。

この記事に記載された手順を 2 か所変更するだけで、パススルー認証を使用できます。
1. [RD ホスト エンドポイントの発行](#publish-the-rd-host-endpoint)の手順 1 の事前認証方法を**パススルー**に設定します。
2. [RDS トラフィックをアプリケーション プロキシに転送する](#direct-rds-traffic-to-application-proxy)手順で、手順 8 全体をスキップします。

## <a name="next-steps"></a>次の手順

[Azure AD アプリケーション プロキシによる SharePoint へのリモート アクセスの有効化](application-proxy-integrate-with-sharepoint-server.md)  
[Azure AD アプリケーション プロキシを使用したアプリへのリモート アクセス時のセキュリティに関する注意事項](application-proxy-security.md)
