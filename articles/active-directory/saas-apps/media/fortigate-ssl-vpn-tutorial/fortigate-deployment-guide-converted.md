---
title: FortiGate デプロイ ガイド | Microsoft Docs
description: 次世代のファイアウォール製品である Fortinet FortiGate を設定して使用します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: jeedes
ms.openlocfilehash: cdaa6a9601452100ab90ef8b0f2191002f256b74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95025463"
---
# <a name="fortigate-azure-virtual-machine-deployment-guide"></a>FortiGate Azure 仮想マシン デプロイ ガイド

このデプロイ ガイドを使用して、Azure 仮想マシンとしてデプロイされた次世代ファイアウォール製品 Fortinet FortiGate を設定して操作する方法を学習します。 さらに、FortiGate SSL VPN Azure AD ギャラリー アプリを構成して、Azure Active Directory を通じた VPN 認証を実現します。

## <a name="redeem-the-fortigate-license"></a>FortiGate ライセンスを引き換える

Fortinet FortiGate は次世代のファイアウォール製品であり、Azure IaaS (サービスとしてのインフラストラクチャ) の仮想マシンとして使用できます。 この仮想マシンには、従量課金制とライセンス持ち込み (BYOL) の 2 種類のライセンス モードがあります。

BYOL 仮想マシン デプロイ オプションで使用するために Fortinet から FortiGate ライセンスを購入した場合は、Fortinet の製品アクティブ化ページ (https://support.fortinet.com ) でそれを引き換えてください。 結果として得られるライセンス ファイルのファイル拡張子は .lic になります。

## <a name="download-firmware"></a>ファームウェアをダウンロードする

このドキュメントの執筆時点では、Fortinet FortiGate Azure VM には、SAML 認証に必要なファームウェア バージョンが付属していません。 最新バージョンを Fortinet から取得する必要があります。

1. https://support.fortinet.com/ でサインインします。
2. **[Download]\(ダウンロード\)**  >  **[Firmware Images]\(ファームウェア イメージ\)** に移動します。
3. **[Release Notes]\(リリース ノート\)** の右側にある **[Download]\(ダウンロード\)** を選択します。
4. **[v6.00]**  >  **[6.4]**  >  **[6.4.2]** の順に選択します。
5. 同じ行の **[HTTPS]** リンクを選択して、**FGT_VM64_AZURE-v6-build1723-FORTINET.out** をダウンロードします。
6. 後で使用するためにファイルを保存します。

## <a name="deploy-the-fortigate-vm"></a>FortiGate VM をデプロイする

1. Azure portal に移動し、FortiGate 仮想マシンをデプロイするサブスクリプションにサインインします。
2. 新しいリソース グループを作成するか、FortiGate 仮想マシンをデプロイするリソース グループを開きます。
3. **[追加]** を選択します。
4. **[Marketplace を検索]** に「*Forti*」と入力します。 **[Fortinet FortiGate Next-Generation Firewall]\(Fortinet FortiGate 次世代ファイアウォール\)** を選択します。
5. ソフトウェア プランを選択します (ライセンスを持っている場合はライセンス持ち込み、それ以外の場合は従量課金制)。 **［作成］** を選択します
6. VM の構成を設定します。

    ![[仮想マシンの作成] のスクリーンショット。](virtual-machine.png)

7. **[認証の種類]** を **[パスワード]** に設定し、VM の管理者資格情報を指定します。
8. **[確認および作成]** > **[作成]** の順に選択します。
9. VM のデプロイが完了するまで待ちます。


### <a name="set-a-static-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>静的パブリック IP アドレスを設定して完全修飾ドメイン名を割り当てる

一貫したユーザー エクスペリエンスにするには、FortiGate VM に割り当てられるパブリック IP アドレスを静的に割り当てるように設定します。 さらに、それを完全修飾ドメイン名 (FQDN) にマップします。

1. Azure portal に移動し、FortiGate VM の設定を開きます。
2. **[概要]** 画面で、パブリック IP アドレスを選択します。

    ![FortiGate SSL VPN のスクリーンショット。](public-ip-address.png)

3. **[静的]**  >  **[保存]** を選択します。

FortiGate VM がデプロイされる環境に対してパブリックにルーティング可能なドメイン名を所有している場合は、VM のホスト (A) レコードを作成します。 このレコードは、静的に割り当てられた前のパブリック IP アドレスにマップされます。

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port-8443"></a>TCP ポート 8443 の新しい受信ネットワーク セキュリティ グループ規則を作成する

1. Azure portal に移動し、FortiGate VM の設定を開きます。
2. 左側のメニューで **[ネットワーク]** を選択します。 ネットワーク インターフェイスの一覧が表示され、受信ポート規則が示されます。
3. **[受信ポートの規則を追加する]** を選択します。
4. TCP 8443 に対する新しい受信ポート規則を作成します。

    ![[受信セキュリティ規則の追加] のスクリーンショット。](port-rule.png)

5. **[追加]** を選択します。

## <a name="create-a-second-virtual-nic-for-the-vm"></a>VM の 2 つ目の仮想 NIC を作成する

内部リソースをユーザーが使用できるようにするには、FortiGate VM に 2 つ目の仮想 NIC を追加する必要があります。 仮想 NIC が存在する Azure の仮想ネットワークには、内部リソースへのルーティング可能な接続が必要です。

1. Azure portal に移動し、FortiGate VM の設定を開きます。
2. FortiGate VM がまだ停止されていない場合は、 **[停止]** を選択し、VM がシャットダウンするまで待ちます。
3. 左側のメニューで **[ネットワーク]** を選択します。
4. **[ネットワーク インターフェイスの接続]** を選択します。
5. **[Create and attach network interface]\(ネットワーク インターフェイスの作成と接続\)** を選択します。
6. 新しいネットワーク インターフェイスのプロパティを構成し、 **[作成]** を選択します。

    ![[ネットワーク インターフェイスの作成] のスクリーンショット。](new-network-interface.png)

7. FortiGate VM を起動します。


## <a name="configure-the-fortigate-vm"></a>FortiGate VM を構成する

以下のセクションでは、FortiGate VM を設定する方法について説明します。

### <a name="install-the-license"></a>ライセンスをインストールする

1. `https://<address>` にアクセスします。 `<address>` は、FortiGate VM に割り当てられている FQDN またはパブリック IP アドレスです。

2. 証明書エラーを無視して続行します。
3. FortiGate VM のデプロイ中に指定した管理者資格情報を使用してサインインします。
4. デプロイでライセンス持ち込みモデルが使用されている場合は、ライセンスのアップロードを求めるメッセージが表示されます。 前に作成したライセンス ファイルを選択してアップロードします。 **[OK]** を選択し、FortiGate VM を再起動します。

    ![FortiGate VM ライセンスのスクリーンショット。](license.png)

5. 再起動後に、ライセンスを検証するために管理者の資格情報を使用してもう一度サインインします。

### <a name="update-firmware"></a>ファームウェアを更新する

1. `https://<address>` にアクセスします。 `<address>` は、FortiGate VM に割り当てられている FQDN またはパブリック IP アドレスです。

2. 証明書エラーを無視して続行します。
3. FortiGate VM のデプロイ中に指定した管理者資格情報を使用してサインインします。
4. 左側のメニューで、 **[System]\(システム\)**  >  **[Firmware]\(ファームウェア\)** を選択します。
5. **[Firmware Management]\(ファームウェアの管理\)** で **[Browse]\(参照\)** を選択して、先ほどダウンロードしたファームウェア ファイルを選択します。
6. 警告を無視し、 **[Backup config and upgrade]\(構成をバックアップしてアップグレード\)** を選択します。

    ![ファームウェアの管理のスクリーンショット。](backup-configure-upgrade.png)

7. **[続行]** をクリックします。
8. FortiGate の構成を (.conf ファイルとして) 保存するように求められたら、 **[Save]\(保存\)** を選択します。
9. ファームウェアがアップロードされて適用されるまで待ちます。 FortiGate VM が再起動されるまで待ちます。
10. FortiGate VM が再起動した後、管理者の資格情報を使用してもう一度サインインします。
11. ダッシュボードの設定を求めるメッセージが表示されたら、 **[Later]\(後で\)** を選択します。
12. チュートリアル ビデオが開始したら、 **[OK]** を選択します。

### <a name="change-the-management-port-to-tcp-8443"></a>管理ポートを TCP 8443 に変更する

1. `https://<address>` にアクセスします。 `<address>` は、FortiGate VM に割り当てられている FQDN またはパブリック IP アドレスです。

2. 証明書エラーを無視して続行します。
3. FortiGate VM のデプロイ中に指定した管理者資格情報を使用してサインインします。
4. 左側のメニューで、 **[System]\(システム\)** を選択します。
5. **[Administration Settings]\(管理設定\)** で HTTPS ポートを **8443** に変更し、 **[Apply]\(適用\)** を選択します。
6. 変更が適用されると、ブラウザーで管理ページの再読み込みが試みられますが、失敗します。 これ以降、管理ページのアドレスは `https://<address>:8443` になります。

    ![リモート証明書アップロードのスクリーンショット。](certificate.png)

### <a name="upload-the-azure-ad-saml-signing-certificate"></a>Azure AD SAML 署名証明書をアップロードする

1. `https://<address>:8443` にアクセスします。 `<address>` は、FortiGate VM に割り当てられている FQDN またはパブリック IP アドレスです。

2. 証明書エラーを無視して続行します。
3. FortiGate VM のデプロイ中に指定した管理者資格情報を使用してサインインします。
4. 左側のメニューで、 **[System]\(システム\)**  >  **[Certificates]\(証明書\)** を選択します。
5. **[Import]\(インポート\)**  >  **[Remote Certificate]\(リモート証明書\)** の順に選択します。
6. Azure テナントの FortiGate カスタム アプリのデプロイからダウンロードした証明書を参照します。 それを選択して、 **[OK]** を選択します。

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>カスタム SSL 証明書をアップロードして構成する

使用している FQDN をサポートする独自の SSL 証明書を使用して FortiGate VM を構成することもできます。 .PFX 形式の秘密キーでパッケージ化された SSL 証明書にアクセスできる場合、この目的でこれを使用できます。

1. `https://<address>:8443` にアクセスします。 `<address>` は、FortiGate VM に割り当てられている FQDN またはパブリック IP アドレスです。

2. 証明書エラーを無視して続行します。
3. FortiGate VM のデプロイ中に指定した管理者資格情報を使用してサインインします。
4. 左側のメニューで、 **[System]\(システム\)**  >  **[Certificates]\(証明書\)** を選択します。
5. **[Import]\(インポート\)**  >  **[Local Certificate]\(ローカル証明書\)**  >  **[PKCS #12 Certificate]\(PKCS #12 証明書\)** を選択します。
6. SSL 証明書と秘密キーが含まれる .PFX ファイルを参照します。
7. .PFX パスワードと、証明書のわかりやすい名前を指定します。 **[OK]** をクリックします。
8. 左側のメニューで、 **[System]\(システム\)**  >  **[Settings]\(設定\)** を選択します。
9. **[Administration Settings]\(管理設定\)** で、 **[HTTPS server certificate]\(HTTPS サーバー証明書\)** の隣にある一覧を展開し、前にインポートした SSL 証明書を選択します。
10. **[適用]** を選択します。
11. ブラウザー ウィンドウを閉じて、`https://<address>:8443` に移動します。
12. FortiGate 管理者の資格情報でサインインします。 正しい SSL 証明書が使用されていることがわかります。

### <a name="configure-authentication-timeout"></a>認証タイムアウトを構成する

1. Azure portal に移動し、FortiGate VM の設定を開きます。
2. 左側のメニューで **[シリアル コンソール]** を選択します。
3. FortiGate VM 管理者の資格情報を使用してシリアル コンソールにサインインします。
4. シリアル コンソールで、次のコマンドを実行します。

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```

### <a name="ensure-network-interfaces-are-obtaining-ip-addresses"></a>ネットワーク インターフェイスで IP アドレスが取得されていることを確認する

1. `https://<address>:8443` にアクセスします。 `<address>` は、FortiGate VM に割り当てられている FQDN またはパブリック IP アドレスです。

2. FortiGate VM のデプロイ中に指定した管理者資格情報を使用してサインインします。
3. 左側のメニューで **[ネットワーク]** を選択します。
4. [ネットワーク] で **[インターフェイス]** を選択します。
5. port1 (外部インターフェイス) と port2 (内部インターフェイス) を調べて、正しい Azure サブネットから IP アドレスが取得されていることを確認します。
    a. いずれかのポートで (DHCP 経由で) サブネットから IP アドレスが取得されていない場合は、そのポートを右クリックし、 **[編集]** を選択します。
    b. [Addressing Mode]\(アドレス指定モード\) の横で、 **[DHCP]** が選択されていることを確認します。
    c. **[OK]** を選択します。

    ![ネットワーク インターフェイスのアドレス指定のスクリーンショット。](addressing.png)

### <a name="ensure-fortigate-vm-has-correct-route-to-on-premises-corporate-resources"></a>FortiGate VM でオンプレミスの企業リソースへの正しいルートが設定されていることを確認する

マルチホームの Azure VM では、すべてのネットワーク インターフェイスが同じ仮想ネットワーク上にあります (ただし、サブネットは異なる場合があります)。 これは多くの場合、FortiGate を介して公開されているオンプレミスの企業リソースに両方のネットワーク インターフェイスが接続していることを意味します。 そのため、オンプレミスの企業リソースが要求されたときにトラフィックが正しいインターフェイスから送出されるように、カスタム ルート エントリを作成する必要があります。

1. `https://<address>:8443` にアクセスします。 `<address>` は、FortiGate VM に割り当てられている FQDN またはパブリック IP アドレスです。

2. FortiGate VM のデプロイ中に指定された管理者資格情報を使用してサインインします。
3. 左側のメニューで **[ネットワーク]** を選択します。
4. [ネットワーク] で **[静的ルート]** を選択します。
5. **[新規作成]** を選択します。
6. [宛先] の横で、 **[サブネット]** を選択します。
7. [サブネット] で、オンプレミスの企業リソースが存在するサブネットの情報を指定します (例: 10.1.0.0/255.255.255.0)。
8. [ゲートウェイ アドレス] の横で、port2 が接続されている Azure サブネット上のゲートウェイを指定します (これは通常、10.6.1.1 のように 1 で終わります)。
9. [インターフェイス] の横で、内部ネットワーク インターフェイス port2 を選択します。
10. **[OK]** を選択します。

    ![ルートの構成のスクリーンショット。](route.png)

## <a name="configure-fortigate-ssl-vpn"></a>FortiGate SSL VPN を構成する

https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial に記載されている手順に従います。
