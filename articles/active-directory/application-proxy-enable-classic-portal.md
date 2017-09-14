---
title: "Azure AD アプリケーション プロキシをクラシック ポータルで有効にする | Microsoft ドキュメント"
description: "Azure クラシック ポータルでアプリケーション プロキシを有効にして、リバース プロキシ用のコネクタをインストールします。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: c7186f98-dd80-4910-92a4-a7b8ff6272b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/02/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro; oldportal
ms.translationtype: HT
ms.sourcegitcommit: 7bf5d568e59ead343ff2c976b310de79a998673b
ms.openlocfilehash: ea97fdc8d146ed524a932018b572ceda0982738b
ms.contentlocale: ja-jp
ms.lasthandoff: 08/01/2017

---

# <a name="enable-application-proxy-in-the-classic-portal-and-download-connectors"></a>クラシック ポータルでアプリケーション プロキシを有効にして、コネクタをダウンロードする
この記事では、Microsoft Azure AD アプリケーション プロキシを Azure AD のクラウド ディレクトリに対して有効にする手順について説明します。

アプリケーション プロキシが何に役立つのかよくわからない場合は、「 [オンプレミス アプリケーションへの安全なリモート アクセスを実現する方法](active-directory-application-proxy-get-started.md)」で詳細を確認してください。

## <a name="application-proxy-prerequisites"></a>アプリケーション プロキシの前提条件
アプリケーション プロキシ サービスを有効にして使用するためには、次の条件を満たしておく必要があります。

* [Microsoft Azure AD の Basic または Premium サブスクリプション](active-directory-editions.md) に加え、自分が全体管理者となっている Azure AD ディレクトリ。
* アプリケーション プロキシ コネクタをインストールできる、Windows Server 2012 R2 または 2016 が実行されているサーバー。 このサーバーは、クラウド内のアプリケーション プロキシ サービスに要求を送信します。また、発行するアプリケーションには HTTP または HTTPS で接続する必要があります。
  * 発行したアプリケーションへのシングル サインオンを実行するには、そのアプリケーションと同じ AD ドメインにこのコンピューターが参加済みであることが必要です。 詳細については、「[アプリケーション プロキシを使用したシングル サインオン](active-directory-application-proxy-sso-using-kcd.md)」を参照してください。
* 組織でインターネットへの接続にプロキシ サーバーを使用している場合、構成方法の詳細については、「[既存のオンプレミス プロキシ サーバーと連携する](application-proxy-working-with-proxy-servers.md)」を参照してください。

## <a name="open-your-ports"></a>ポートを開く

Azure AD アプリケーション プロキシの環境を準備するには、まず Azure データ センターとの通信を有効にする必要があります。 経路上にファイアウォールがある場合、コネクタからアプリケーション プロキシに HTTPS (TCP) 要求を送信できるように、ファイアウォールを開放する必要があります。

1. 以下の各ポートを**送信**トラフィックに対して開放します。

   | ポート番号 | 用途 |
   | --- | --- |
   | 80 | SSL 証明書を検証する際に証明書失効リスト (CRL) をダウンロードする |
   | 443 | アプリケーション プロキシ サービスとのすべての送信通信 |

   ご利用のファイアウォールが送信元ユーザーに応じてトラフィックを監視している場合は、Network Service として実行されている Windows サービスを送信元とするトラフィックに対してこれらのポートを開放します。

   > [!IMPORTANT]
   > テーブルには、バージョン 1.5.132.0 以降のコネクタのポート要件が反映されます。 コネクタのバージョンがこれより古い場合は、5671、8080、9090、9091、9350、9352、および 10100 - 10120 ポートも有効にする必要があります。
   >
   >コネクタを最新バージョンに更新する方法の詳細については、「[Azure AD アプリケーション プロキシ コネクタを理解する](application-proxy-understand-connectors.md#automatic-updates)」を参照してください。

2. ファイアウォールまたはプロキシが DNS ホワイトリストを許可している場合は、msappproxy.net と servicebus.windows.net への接続をホワイトリストに登録できます。 そうでない場合は、毎週更新される [Azure DataCenter IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)へのアクセスを許可する必要があります。

3. [Azure AD アプリケーション プロキシ コネクタ ポート テスト ツール](https://aadap-portcheck.connectorporttest.msappproxy.net/)を使った、コネクタがアプリケーション プロキシ サービスにアクセスできることを確認します。 少なくとも、米国中部リージョンと自分に最も近いリージョンにすべて緑色のチェックマークが表示されていることを確認します。 その他の場合は、緑色のチェックマークが多いほど、リカバリ性が高いことを意味します。

## <a name="enable-application-proxy-in-azure-ad"></a>Azure AD でのアプリケーション プロキシを有効にする
1. [Azure クラシック ポータル](https://manage.windowsazure.com/)に管理者としてサインインします。
2. Active Directory に移動し、アプリケーション プロキシを有効にするディレクトリを選択します。

    ![Active Directory - icon](./media/active-directory-application-proxy-enable/ad_icon.png)
3. ディレクトリ ページから **[構成]** を選択し、下へスクロールして **[アプリケーション プロキシ]** まで移動します。
4. **[このディレクトリに対してアプリケーション プロキシ サービスを有効にする]** を **[有効]** に切り替えます。

    ![アプリケーション プロキシを有効にする](./media/active-directory-application-proxy-enable/app_proxy_enable.png)
5. **[今すぐダウンロード]**を選択します。 **Azure AD アプリケーション プロキシ コネクタのダウンロード** ページが開きます。 ライセンス条項を読んで同意し、 **[ダウンロード]** をクリックして、コネクタの Windows インストーラー ファイル (.exe) を保存します。

## <a name="install-and-register-the-connector"></a>コネクタのインストールおよび登録
1. 前提条件に従って用意したサーバーで、 **AADApplicationProxyConnectorInstaller.exe** を実行します。
2. ウィザードの指示に従ってインストールします。
3. インストール時に、Azure AD テナントのアプリケーション プロキシにコネクタを登録するように求められます。

   * Azure AD グローバル管理者の資格情報を指定します。 グローバル管理者のテナントは、Microsoft Azure の資格情報とは異なる場合があります。
   * コネクタを登録する管理者が属しているディレクトリは、アプリケーション プロキシ サービスを有効にしたディレクトリと同じになるようにしてください。 たとえば、テナントのドメインが contoso.com の場合、管理者は admin@contoso.com またはそのドメイン上の他のエイリアスであることが必要です。
   * サーバーで **[IE セキュリティ強化の構成]** を **[オン]** に設定している場合、登録画面がブロックされることがあります。 アクセスを許可するには、エラー メッセージに示された指示に従ってください。 Internet Explorer セキュリティ強化の構成が無効になっていることを確認します。
   * コネクタの登録が成功しない場合は、「 [アプリケーション プロキシのトラブルシューティング](active-directory-application-proxy-troubleshoot.md)」をご覧ください。  
4. インストールが完了すると、次に示す 2 つの新しいサービスがサーバーに追加されます。

   * **Microsoft AAD アプリケーション プロキシ コネクタ** : 接続を有効にします。

     * **Microsoft AAD アプリケーション プロキシ コネクタ アップデーター**は自動更新サービスです。 定期的にコネクタの新しいバージョンをチェックし、必要に応じてコネクタを更新します。

     ![App Proxy Connector services - screenshot](./media/active-directory-application-proxy-enable/app_proxy_services.png)
5. インストール ウィンドウで、 **[完了]** をクリックします。

コネクタの詳細については、「[Understand Azure AD Application Proxy connectors (Azure AD アプリケーション プロキシ コネクタについて)](application-proxy-understand-connectors.md)」を参照してください。

高可用性を確保するには、コネクタを少なくとも 2 つデプロイする必要があります。 さらに多くのコネクタをデプロイするには、手順 2 および 3 を繰り返してください。 各コネクタは個別に登録する必要があります。

コネクタをアンインストールする場合は、コネクタ サービスと更新サービスの両方をアンインストールします。 コンピューターを再起動して、サービスを完全に削除します。

## <a name="next-steps"></a>次のステップ
[アプリケーション プロキシを使用してアプリケーションを発行](active-directory-application-proxy-publish.md)する準備がこれで整いました。

別のネットワークや異なる場所にアプリケーションがある場合、コネクタ グループを使用すると、さまざまなコネクタで論理ユニットを編成することができます。 アプリケーション プロキシ コネクタの使用方法の詳細については、 [こちら](active-directory-application-proxy-connectors.md)を参照してください。

