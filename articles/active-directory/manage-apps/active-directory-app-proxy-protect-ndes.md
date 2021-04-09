---
title: NDES サーバー上の AD アプリケーション プロキシとの統合
titleSuffix: Azure Active Directory
description: NDES サーバーを保護するための Azure Active Directory アプリケーション プロキシのデプロイに関するガイダンス。
services: active-directory
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/17/2020
ms.author: kenwith
ms.reviewer: mimart
ms.openlocfilehash: 69193296069765ae6e94ffe97913c136a0d033d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99257898"
---
# <a name="integrate-with-azure-ad-application-proxy-on-a-network-device-enrollment-service-ndes-server"></a>ネットワーク デバイス登録サービス (NDES) サーバー上の Azure AD アプリケーション プロキシとの統合

Azure Active Directory (AD) アプリケーション プロキシを使用すると、ネットワーク内でアプリケーションを発行できます。 これらのアプリケーションは、SharePoint サイト、Microsoft Outlook Web アプリ、その他の Web アプリケーションなどです。 また、Azure 経由でネットワーク外部のユーザーに安全にアクセスすることもできます。

Azure AD アプリケーション プロキシを使用したことがなく、詳細を確認したい場合は、[Azure AD アプリケーション プロキシからのオンプレミス アプリケーションへのリモート アクセス](application-proxy.md)に関する記事を参照してください。

Azure AD アプリケーション プロキシは Azure 上に構築されています。 これは、分散型サービス拒否 (DDOS) 攻撃に対するより強力な保護と優れた可用性を実現するために、大量のネットワーク帯域幅とサーバー インフラストラクチャを提供します。 さらに、オンプレミスのネットワークに対して外部ファイアウォール ポートを開く必要がなく、DMZ サーバーは必要ありません。 すべてのトラフィックは内向きに発信されます。 送信ポートの完全な一覧については、「[チュートリアル: Azure Active Directory のアプリケーション プロキシを使用してリモート アクセスするためのオンプレミス アプリケーションを追加する](./application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)を参照してください。

> Azure AD アプリケーション プロキシは、Azure Active Directory の Premium または Basic エディションを使用している場合にのみ利用できる機能です。 詳細については、「[Azure Active Directory の価格](https://azure.microsoft.com/pricing/details/active-directory/)」をご覧ください。 
> Enterprise Mobility Suite (EMS) ライセンスを所有している場合は、このソリューションを使用することができます。
> Azure AD アプリケーション プロキシ コネクタは、Windows Server 2012 R2 以降にのみインストールされます。 これは、NDES サーバーの要件でもあります。

## <a name="install-and-register-the-connector-on-the-ndes-server"></a>NDES サーバーへのコネクタのインストールおよび登録

1. アプリケーション プロキシを使用するディレクトリのアプリケーション管理者として、[Azure portal](https://portal.azure.com/) にサインインします。 たとえば、テナントのドメインが contoso.com の場合、管理者は admin@contoso.com またはそのドメイン上の他の管理者エイリアスであることが必要です。
1. 右上隅で自分のユーザー名を選択します。 アプリケーション プロキシを使用するディレクトリにサインインしていることを確認します。 ディレクトリを変更する必要がある場合は、 **[ディレクトリの切り替え]** を選択し、アプリケーション プロキシを使用するディレクトリを選択します。
1. 左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。
1. **[管理]** で、 **[アプリケーション プロキシ]** を選択します。
1. **[コネクタ サービスのダウンロード]** を選択します。

    ![コネクタ サービスをダウンロードしてサービス使用条件を表示](./media/active-directory-app-proxy-protect-ndes/application-proxy-download-connector-service.png)

1. サービス利用規約を読みます。 準備ができたら、 **[規約に同意してダウンロード]** を選択します。
1. Azure AD アプリケーション プロキシ コネクタのセットアップ ファイルを NDES サーバーにコピーします。 
   > 企業ネットワーク内の、NDES にアクセスできる任意のサーバーにコネクタをインストールできます。 NDES サーバー自体にインストールする必要はありません。
1. *AADApplicationProxyConnectorInstaller.exe* などのセットアップ ファイルを実行します。 ソフトウェア ライセンス条項に同意します。
1. インストール時に、Azure AD ディレクトリのアプリケーション プロキシにコネクタを登録するように求められます。
   * Azure AD ディレクトリの全体またはアプリケーション管理者の資格情報を指定します。 Azure AD の全体またはアプリケーション管理者の資格情報は、ポータルの Azure 資格情報とは異なる場合があります。

        > [!NOTE]
        > コネクタの登録に使用する全体またはアプリケーション管理者アカウントは、アプリケーション プロキシ サービスを有効にしたのと同じディレクトリに属している必要があります。
        >
        > たとえば、Azure AD ドメインが *contoso.com* の場合、全体/アプリケーション管理者は `admin@contoso.com` か、そのドメインの別の有効なエイリアスである必要があります。

   * コネクタをインストールするサーバーで [Internet Explorer セキュリティ強化の構成] がオンになっていると、登録画面がブロックされることがあります。 アクセスを許可するには、エラー メッセージの指示に従うか、インストールプロセス中に Internet Explorer のセキュリティ強化をオフにします。
   * コネクタの登録が失敗する場合は、[アプリケーション プロキシのトラブルシューティング](application-proxy-troubleshoot.md)に関する記事をご覧ください。
1. セットアップの最後に、送信プロキシを使用している環境に対してメモが表示されます。 送信プロキシ経由で動作するように Azure AD アプリケーション プロキシ コネクタを構成するには、提供されたスクリプト (`C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1` など) を実行します。
1. Azure portal の [アプリケーション プロキシ] ページで、次の例に示すように、新しいコネクタが *[アクティブ]* の状態で一覧表示されます。

    ![Azure portal でアクティブと表示されている新しい Azure AD アプリケーション プロキシ コネクタ](./media/active-directory-app-proxy-protect-ndes/connected-app-proxy.png)

    > [!NOTE]
    > Azure AD アプリケーション プロキシを介して認証を行うアプリケーションの高可用性を実現するために、複数の VM にコネクタをインストールできます。 前のセクションと同じ手順を繰り返して、Azure AD DS マネージド ドメインに参加している他のサーバーにコネクタをインストールします。

1. インストールが正常に完了したら、Azure portal に戻ります。

1. **[エンタープライズ アプリケーション]** を選択します。

   ![適切な利害関係者が担当していることを確認します](./media/active-directory-app-proxy-protect-ndes/azure-active-directory-enterprise-applications.png)

1. **[+ 新しいアプリケーション]** を選択し、 **[オンプレミスのアプリケーション]** を選択します。 

1. **[独自のオンプレミスのアプリケーションの追加]** で、以下のフィールドを構成します。

   * **Name**:アプリケーションの名前を入力します。
   * **内部 URL**:コネクタをインストールした NDES サーバーの内部 URL または FQDN を入力します。
   * **事前認証**: **[パススルー]** を選択します。 いかなる形式の事前認証も使用できません。 証明書要求 (SCEP) に使用されるプロトコルでは、このオプションは提供されません。
   * 提供された **外部 URL** をクリップボードにコピーします。

1. **[+追加]** をクリックして、アプリケーションを保存します。

1. ステップ 15 でコピーしたリンクをブラウザーに貼り付けて、Azure AD アプリケーション プロキシを介して NDES サーバーにアクセスできるかどうかをテストします。 既定の IIS ウェルカム ページが表示されるはずです。

1. 最後のテストとして、前のステップで貼り付けた既存の URL に *mscep.dll* パスを追加します。

   https://scep-test93635307549127448334.msappproxy.net/certsrv/mscep/mscep.dll

1. **HTTP エラー 403 - アクセス不可** の応答が表示されるはずです。

1. デバイスに (Microsoft Intune 経由で) 提供された NDES URL を変更します。 この変更は、Microsoft Endpoint Configuration Manager または Microsoft Endpoint Manager 管理センターのいずれかで行うことができます。

   * Configuration Manager の場合は、証明書登録ポイントに移動して、URL を調整します。 この URL は、デバイスが呼び出して、資格情報の要求を行う URL です。
   * Intune スタンドアロンの場合は、SCEP ポリシーを編集するか新規に作成し、新しい URL を追加します。

## <a name="next-steps"></a>次の手順

NDES と統合された Azure AD アプリケーション プロキシを使用して、ユーザーがアクセスできるようにアプリケーションを発行します。 詳しくは、[Azure AD アプリケーション プロキシを使用したアプリケーションの発行](./application-proxy-add-on-premises-application.md)に関する記事をご覧ください。
