---
title: FortiGate デプロイ ガイド | Microsoft Docs
description: Azure Active Directory と FortiGate SSL VPN の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 18a3d9d5-d81c-478c-be7e-ef38b574cb88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 999e19ffad1d18e163881c844cbf30f8b7fef574
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88657164"
---
# <a name="fortigate-deployment-guide"></a>FortiGate デプロイ ガイド

## <a name="contents"></a>内容

- FortiGate ライセンスの引き替え
- ファームウェアをダウンロードする
- FortiGate VM をデプロイする
   - 静的パブリック IP アドレスを設定して完全修飾ドメイン名を割り当てる
   - TCP ポートの新しい受信ネットワーク セキュリティ グループ規則を作成する
- FortiGate のカスタム Azure アプリを作成する
- グループ マッチングのための準備を行う
   - ユーザーのグループを作成する
- FortiGate VM を構成する
   - ライセンスをインストールする
   - ファームウェアを更新する
   - 管理ポートを TCP に変更する
   - Azure Active Directory SAML 署名証明書をアップロードする
   - カスタム SSL 証明書をアップロードして構成する
   - コマンド ラインの構成を実行する
   - VPN ポータルとファイアウォール ポリシーを作成する
- Azure を使用してサインインをテストする

## <a name="redeeming-the-fortigate-license"></a>FortiGate ライセンスの引き替え

Fortinet FortiGate 次世代ファイアウォール製品は、Azure IaaS の仮想マシンとして使用できます。 この仮想マシンには 2 つのライセンス モードがあります。

- 従量課金制 (PAYG)
- ライセンスを持ち込む (BYOL)

Fortinet と提携してセキュリティで保護されたハイブリッド アクセス (SHA) のガイダンスを提供していますが、Fortinet は Azure AD の実稼働環境移行の SHA チームのメンバーにライセンスを提供できます。 ライセンスが提供されていない場合、PAYG デプロイも機能します。

ライセンスが発行されている場合、Fortinet はオンラインで引き替える必要がある登録コードを提供します

![FortiGate SSL VPN](registration-code.png)

1. https://support.fortinet.com/ で登録します
2. 登録後、 https://support.fortinet.com/ にサインインします
3. **[Asset]\(アセット\)** - > **[Register/Activate]\(登録/アクティブ化\)** に移動します
4. Fortinet によって提供された登録コードを入力します
5. 登録コードを指定し、 **[The product will be used by a non-government user]\(製品を政府以外のユーザーが使用する\)** を選択して **[Next]\(次へ\)** をクリックします
6. 製品の説明 (例: FortiGate) を入力し、[Fortinet Partner]\(Fortinet パートナー\) を **[Other]\(その他\)** - > **[Microsoft]** として設定して、 **[Next]\(次へ\)** をクリックします
7. **[Fortinet Product Registration Agreement]\(Fortinet 製品登録契約\)** に同意し、 **[Next]\(次へ\)** をクリックします
8. **[Terms]\(使用条件\)** に同意し **[Confirm]\(確認\)** をクリックします
9. **[License File Download]\(ライセンス ファイルのダウンロード\)** をクリックし、後で使用するためにライセンスを保存します


## <a name="download-firmware"></a>ファームウェアをダウンロードする

このドキュメントの執筆時点では、Fortinet FortiGate Azure VM には、SAML 認証に必要なファームウェア バージョンが付属していません。 最新バージョンを Fortinet から取得する必要があります。

1. https://support.fortinet.com/ にサインインします
2. **[Download]\(ダウンロード\)** - > **[Firmware Images]\(ファームウェア イメージ\)** に移動します
3. **[Release Notes]\(リリース ノート\)** の右側にある **[Download]\(ダウンロード\)** をクリックします
4. **[v6]** をクリックします。
5. **[6]** をクリックします。
6. **[6.4]** をクリックします。
7. 同じ行の **[HTTPS]** リンクをクリックして、**FGT_VM64_AZURE-v6-build1637-FORTINET.out** をダウンロードします
8. 後で使用するためにファイルを保存します


## <a name="deploy-the-fortigate-vm"></a>FortiGate VM をデプロイする

1. https://portal.azure.com に移動し、FortiGate 仮想マシンをデプロイするサブスクリプションにサインインします
2. 新しいリソース グループを作成するか、FortiGate 仮想マシンをデプロイするリソース グループを開きます
3. **[追加]** をクリックします。
4. **[Search the Marketplace]\(マーケットプレースを検索\)** ダイアログボックスに「Forti」と入力し、 **[Fortinet FortiGate Next-** **Generation Firewall]\(Fortinet FortiGate 次世代ファイアウォール\)** を選択します
5. ソフトウェア プランを選択し (ライセンスがある場合は BYOL、ない場合は PAYG)、 **[作成]** をクリックします
6. VM 構成を入力します

    ![FortiGate SSL VPN](virtual-machine.png)

7. 認証の種類を **[パスワード]** に設定し、VM の管理者資格情報を指定します
8. **[確認と作成]** をクリックします
9. **[作成]**
10. VM のデプロイが完了するまで待ちます


### <a name="set-a-statuc-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>静的パブリック IP アドレスを設定して完全修飾ドメイン名を割り当てる

一貫したユーザー エクスペリエンスを実現するには、FortiGate VM に割り当てられるパブリック IP アドレスを静的に割り当てるように設定することをお勧めします。 さらに、これを完全修飾ドメイン名にマッピングすることも同じ理由のために役立ちます。

"_静的パブリック IP アドレスを設定する_"

1. https://portal.azure.com に移動し、FortiGate VM の設定を開きます
2. **[概要]** 画面で、パブリック IP アドレスをクリックします

    ![FortiGate SSL VPN](public-ip-address.png)

3. **[静的]** をクリックしてから、 **[保存]** をクリックします

"_完全修飾ドメイン名を割り当てる_"

FortiGate VM がデプロイされる環境に対してパブリックにルーティング可能なドメイン名を所有している場合は、上記で静的に割り当てられたパブリック IP アドレスにマップされる VM のホスト (A) レコードを作成します。

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port"></a>TCP ポートの新しい受信ネットワーク セキュリティ グループ規則を作成する

1. https://portal.azure.com に移動し、FortiGate VM の設定を開きます
2. 左側のメニューで、 **[ネットワーク]** をクリックします。 ネットワーク インターフェイスが一覧表示され、受信ポートの規則が表示されます
3. **[受信ポートの規則を追加する]** をクリックします
4. TCP 8443 の新しい受信ポートの規則を作成します

    ![FortiGate SSL VPN](port-rule.png)

5. **[追加]** をクリックします。


## <a name="create-a-custom-azure-app-for-fortigate"></a>FortiGate のカスタム Azure アプリを作成する

1. https://portal.azure.com に移動し、FortiGate サインインの ID を提供するテナントの Azure Active Directory ブレードを開きます
2. 左側のメニューで **[エンタープライズ アプリケーション]** をクリックします
3. **[新しいアプリケーション]** をクリックします
4. **[ギャラリー以外のアプリケーション]** をクリックします
5. 名前 (例: FortiGate) を指定し、 **[追加]** をクリックします
6. 左側のメニューで **[ユーザーとグループ]** をクリックします
7. サインインできるユーザーを追加して **[割り当て]** をクリックします
8. 左側のメニューの **[シングル サインオン]** をクリックします
9. **[SAML]** をクリックします
10. **[基本的な SAML 構成]** で、鉛筆をクリックして構成を編集します
11. 構成
    - 識別子 (エンティティ ID) を `https://<address>/remote/saml/metadata` にします
    - 応答 URL (Assertion Consumer Service URL) を `https://<address>/remote/saml/login` にします
    - ログアウト URL を `https://<address>/remote/saml/logout` にします

    ここで `address` は、FortiGate VM に割り当てられている FQDN またはパブリック IP アドレスです

    これらの各 URL を後で使用するために記録します

    - エンティティ ID
    - [応答 URL]
    - ログアウト URL
12. **[保存]**
13. [基本的な SAML 構成] を閉じます
14. **[3 - SAML 署名証明書]** の下で、 **[証明書 (Base64)]** をダウンロードし、後で使用するために保存します
15. **4 - 設定 (アプリ名)** で、Azure ログイン URL、Azure AD 識別子、Azure ログアウト URL をコピーし、後で使用するために保存します
    - Azure ログイン URL
    - Azure AD 識別子
    - Azure ログアウト URL
16. **[2 - ユーザー属性と要求]** で、鉛筆をクリックして構成を編集します
17. **[新しい要求の追加]** をクリックします
18. 名前を **username** に設定します
19. ソース属性を **user.userprincipalname** に設定します
20. **[保存]**
21. **[グループ要求を追加する]** をクリックします
22. **[すべてのグループ]** を選択します
23. **[グループ要求の名前をカスタマイズする]** をオンにします
24. 名前を **group** に設定します
25. **[保存]**


## <a name="prepare-for-group-matching"></a>グループ マッチングのための準備を行う

FortiGate では、グループ メンバーシップに基づいて、サインイン後に異なるユーザーのポータル エクスペリエンスを使用できます。 たとえば、マーケティング グループ用に 1 つのエクスペリエンスがあり、財務グループ用に別のエクスペリエンスがある場合もあります。

次に示すように構成します

### <a name="create-groups-for-users"></a>ユーザーのグループを作成する

1. https://portal.azure.com に移動し、FortiGate サインインの ID を提供するテナントの Azure Active Directory ブレードを開きます
2. **[グループ]** をクリックします
3. **[新しいグループ]** をクリックします
4. 次の設定でグループを作成します
    - グループの種類 = セキュリティ
    - グループ名 = `a meaningful name`
    - グループの説明 = `a meaningful description for the group`
    - メンバーシップの種類 = 割り当て済み
    - メンバー = `users for the user experience that will map to this group`
5. 追加のユーザー エクスペリエンスを使用する場合は、手順 3 と 4 を繰り返します
6. グループが作成されたら、各グループを選択し、それぞれのオブジェクト ID を記録します
7. これらのオブジェクト ID とグループ名を後で使用するために保存します


## <a name="configure-the-fortigate-vm"></a>FortiGate VM を構成する

### <a name="install-the-license"></a>ライセンスをインストールする

1. `https://<address>` に移動します

    ここで `address` は、FortiGate VM に割り当てられている FQDN またはパブリック IP アドレスです

2. 証明書エラーを無視して続行します
3. FortiGate VM のデプロイ中に指定された管理者資格情報を使用してサインインします
4. デプロイで BYOL モデルが使用されている場合は、ライセンスをアップロードするためのプロンプトが表示されます。 先ほど作成したライセンス ファイルを選択してアップロードし、 **[OK]** をクリックして FortiGate VM を再起動します

    ![FortiGate SSL VPN](license.png)

5. 再起動後に、ライセンスを検証するための管理者の資格情報を使用してもう一度サインインします

### <a name="update-firmware"></a>ファームウェアを更新する

1. `https://<address>` に移動します

    ここで `address` は、FortiGate VM に割り当てられている FQDN またはパブリック IP アドレスです

2. 証明書エラーを無視して続行します
3. FortiGate VM のデプロイ中に指定された管理者資格情報を使用してサインインします
4. 左側のメニューで、 **[System]\(システム\)** をクリックします
5. 左側のメニューの [System]\(システム\) で、 **[Firmware]\(ファームウェア\)** をクリックします
6. [Firmware Management]\(ファームウェア管理\) ページで **[Browse]\(参照\)** をクリックして、先ほどダウンロードしたファームウェア ファイルを選択します
7. 警告を無視し、 **[Backup config and upgrade]\(構成をバックアップしてアップグレード\)** をクリックします

    ![FortiGate SSL VPN](backup-configure-upgrade.png)

8. **[Continue]\(続行\)** をクリックします
9. FortiGate 構成を (.conf ファイルとして) 保存するように求められたら、 **[Save]\(保存\)** をクリックします
10. ファームウェアがアップロードされて適用され、FortiGate VM が再起動するまで待機します
11. FortiGate VM 再起動後に、管理者の資格情報を使用してもう一度サインインします
12. ダッシュボードのセットアップを実行するように求められたら、 **[Later]\(後で\)** をクリックします
13. チュートリアル ビデオが開始したら、 **[OK]** をクリックします

### <a name="change-the-management-port-to-tcp"></a>管理ポートを TCP に変更する

1. `https://<address>` に移動します

    ここで `address` は、FortiGate VM に割り当てられている FQDN またはパブリック IP アドレスです

2. 証明書エラーを無視して続行します
3. FortiGate VM のデプロイ中に指定された管理者資格情報を使用してサインインします
4. 左側のメニューで、 **[System]\(システム\)** をクリックします
5. [Administration Settings]\(管理設定\) で、HTTPS ポートを **8443** に変更します
6. **[適用]** をクリックします
7. 変更が適用されると、ブラウザーでは管理ページの再読み込みが試行されますが、失敗します。 これ以降、管理ページのアドレスは `https://<address>` になります

    ![FortiGate SSL VPN](certificate.png)

### <a name="upload-the-azure-active-directory-saml-signing-certificate"></a>Azure Active Directory SAML 署名証明書をアップロードする

1. `https://<address>` に移動します

    ここで `address` は、FortiGate VM に割り当てられている FQDN またはパブリック IP アドレスです

2. 証明書エラーを無視して続行します
3. FortiGate VM のデプロイ中に指定された管理者資格情報を使用してサインインします
4. 左側のメニューで、 **[System]\(システム\)** をクリックします
5. [System]\(システム\) で **[Certificates]\(証明書\)** をクリックします
6. **[Import]\(インポート\)** - > **[Remote Certificate]\(リモート証明書\)** をクリックします
7. Azure テナントの FortiGate カスタム アプリのデプロイからダウンロードした証明書を参照して選択し、 **[OK]** をクリックします

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>カスタム SSL 証明書をアップロードして構成する

使用している FQDN をサポートする独自の SSL 証明書を使用して FortiGate VM を構成することもできます。 .PFX 形式の秘密キーでパッケージ化された SSL 証明書にアクセスできる場合、この目的でこれを使用できます

1. `https://<address>` に移動します

    ここで `address` は、FortiGate VM に割り当てられている FQDN またはパブリック IP アドレスです

2. 証明書エラーを無視して続行します
3. FortiGate VM のデプロイ中に指定された管理者資格情報を使用してサインインします
4. 左側のメニューで、 **[System]\(システム\)** をクリックします
5. [System]\(システム\) で **[Certificates]\(証明書\)** をクリックします
6. **[Import]\(インポート\)** - > **[Local Certificate]\(ローカル証明書\)** をクリックします
7. **[PKCS #12 Certificate]\(PKCS #12 証明書\)** をクリックします
8. SSL 証明書と秘密キーを含む .PFX ファイルを参照します
9. .PFX パスワードを入力します
10. 証明書にわかりやすい名前を付けます
11. **[OK]**
12. 左側のメニューで、 **[System]\(システム\)** をクリックします
13. [System]\(システム\) で **[Settings]\(設定\)** をクリックします
14. [Administration Settings]\(管理設定\) で、HTTPS サーバー証明書の横にあるドロップダウンを展開し、上記でインポートした SSL 証明書を選択します
15. **[適用]** をクリックします
16. ブラウザー ウィンドウを閉じ、`https://<address>` にもう一度移動します
17. FortiGate 管理者の資格情報でサインインし、正しい SSL 証明書が使用されていることを確認します


### <a name="perform-command-line-configuration"></a>コマンド ラインの構成を実行する

"_SAML 認証用のコマンド ラインの構成を実行する_"

1. https://portal.azure.com に移動し、FortiGate VM の設定を開きます
2. 左側のメニューで、 **[シリアル コンソール]** をクリックします
3. FortiGate VM 管理者の資格情報を使用してシリアル コンソールにサインインします

    次の手順では、先ほど記録した URL が必要です。 つまり次の項目です

    - エンティティ ID
    - [応答 URL]
    - ログアウト URL
    - Azure ログイン URL
    - Azure AD 識別子
    - Azure ログアウト URL
4. シリアル コンソールで、次のコマンドを実行します

    ```
    config user saml
    edit azure
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert REMOTE_Cert_
    set user-name username
    set group-name group
    end
    ```
    > [!NOTE]
    > Azure ログアウト URL には文字 "?" が含まれている必要があります。 これは、FortiGate シリアル コンソールに正しく提供されるために、特殊なキー シーケンスを必要とします。 通常、URL は次のようになります

    `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1`

    シリアル コンソールにこれを提供するには、次のように入力します

    ```
    set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation
    ```
    次に、CTRL + V を押します

    次に、残りの URL を貼り付けて、行を完成させます

    ```
    set idp-single-logout-url
    https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.
    ```

5. 構成を確認するには、次を実行します

    ```
    show user saml
    ```

"_グループ マッチング用のコマンド ラインの構成を実行する_"

1. https://portal.azure.com に移動し、FortiGate VM の設定を開きます
2. 左側のメニューで、 **[シリアル コンソール]** をクリックします
3. FortiGate VM 管理者の資格情報を使用してシリアル コンソールにサインインします
4. シリアル コンソールで、次のコマンドを実行します

    ```
    config user group
    edit <group 1 name>
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <group 1 Object Id>
    next
    end
    next
    ```

    これらのコマンドを "edit `group 1 name`" から繰り返します。ただし、FortiGate でポータル エクスペリエンスが異なる各グループについて行います

"_認証タイムアウト用の構成を実行する_"

1. https://portal.azure.com に移動し、FortiGate VM の設定を開きます
2. 左側のメニューで、 **[シリアル コンソール]** をクリックします
3. FortiGate VM 管理者の資格情報を使用してシリアル コンソールにサインインします
4. シリアル コンソールで、次のコマンドを実行します

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```
### <a name="create-vpn-portals-and-firewall-policy"></a>VPN ポータルとファイアウォール ポリシーを作成する

1. `https://<address>` に移動します

    ここで `address` は、FortiGate VM に割り当てられている FQDN またはパブリック IP アドレスです

2. FortiGate VM のデプロイ中に指定された管理者資格情報を使用してサインインします
3. 左側のメニューで、 **[VPN]** をクリックします
4. [VPN] で **[SSL-VPN Portals]\(SSL-VPN ポータル\)** をクリックします
5. **[Create New]\(新規作成\)** をクリックします
6. 名前を指定します (通常は、カスタム ポータル エクスペリエンスを提供するために使用される Azure グループと一致させます)
7. [Source IP Pools]\(ソース IP プール\) の横にあるプラス記号 ( **+** ) をクリックし、既定のプールを選択して **[Close]\(閉じる\)** をクリックします
8. このグループのエクスペリエンスをカスタマイズします。 テストの場合、これはポータル メッセージとテーマのカスタマイズです。 ここでは、ユーザーを内部リソースに誘導するカスタム ブックマークを作成することもできます
9. **[OK]**
10. カスタムのポータル エクスペリエンスを持つことになる Azure グループごとに手順 5 から 9 を繰り返します
11. [VPN] で **[SSL-VPN Settings]\(SSL-VPN 設定\)** をクリックします
12. [Listen on Interfaces]\(インターフェイスでリッスンする\) の横にあるプラス記号 ( **+** ) をクリックします
13. **[Port1]** を選択し、 **[Close]\(閉じる\)** をクリックします


14. カスタム SSL 証明書を以前にインストールした場合は、ドロップダウン メニューでサーバー証明書を変更して、カスタム SSL 証明書を使用するようにします
15. [Authentication/Portal Mapping]\(認証/ポータルマッピング\) で、 **[Create New]\(新規作成\)** をクリックします
16. 最初の Azure グループを選択し、これを同じ名前のポータルと一致させます
17. **[OK]**
18. Azure グループ/ポータルのペアごとに手順 15 から 17 を繰り返します
19. [Authentication/Portal Mapping]\(認証/ポータル マッピング\) で、 **[All Other Users/Groups]\(他のすべてのユーザー/グループ \)** を編集します
20. ポータルを **[full-access]\(フルアクセス\)** に設定します
21. **[OK]**
22. **[適用]** をクリックします
23. [SSL-VPN Setting]\(SSL-VPN 設定\) ページの一番上までスクロールし、 **[No SSL-VPN policies**
    **exist. Click here to create a new SSL-VPN policy using these settings]\(SSL-VPN ポリシーが存在しません。ここをクリックしてこれらの設定を使用して新しい SSL-VPN ポリシーを作成します\) **という警告をクリックします
24. 名前 (**VPN Grp** など) を付けます
25. [Outgoing Interface]\(送信インターフェイス\) を **[port]\(ポート\)** に設定します
26. **[Source]\(ソース\)** をクリックします
27. [Address]\(アドレス\) で **[all]\(すべて\)** を選択します
28. [User]\(ユーザー\) で、最初の Azure グループを選択します
29. [閉じる****] をクリックします。
30. **[Destination]\(宛先\)** をクリックします
31. [Address]\(アドレス\) は、通常は内部ネットワークです。 テスト用の login.microsoft.com を選択します
32. [閉じる****] をクリックします。
33. **[Service]\(サービス\)** をクリックします
34. **[All]\(すべて\)** をクリックします
35. [閉じる****] をクリックします。
36. **[OK]**
37. 左側のメニューで、 **[Policy & Objects]\(ポリシーとオブジェクト\)** をクリックします
38. [Policy & Objects]\(ポリシーとオブジェクト\) で、 **[Firewall Policy]\(ファイアウォール ポリシー\)** をクリックします
39. **[SSL-VPN tunnel interface (ssl.root)]\(SSL-VPN トンネル インターフェイス (ssl.root)\) -> [port]\(ポート\)** を展開します
40. 先ほど作成した VPN ポリシー (**VPN Grp 1**) を右クリックし、 **[Copy]\(コピー\)** を選択します
41. VPN ポリシーの下で右クリックし、 **[Paste]\(貼り付け\)** - > **[Below]\(下\)** を選択します
42. 新しいポリシーを編集して、別の名前 (たとえば **VPN Grp2**) を指定し、適用されるグループを変更します (別の Azure グループ)
43. 新しいポリシーを右クリックし、状態を **[Enabled]\(有効\)** に設定します


## <a name="test-sign-in-using-azure"></a>Azure を使用してサインインをテストする

1. プライベート ブラウザー セッションを使用して、`https://<address>` に移動します 
2. サインインでは、サインイン用の Azure Active Directory にリダイレクトされます
3. Azure テナントで FortiGate アプリに割り当てられているユーザーの資格情報を指定すると、適切なユーザー ポータルが表示されます

    ![FortiGate SSL VPN](test-sign-in.png)
