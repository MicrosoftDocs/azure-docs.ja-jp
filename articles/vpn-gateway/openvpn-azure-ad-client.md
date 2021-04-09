---
title: 'VPN Gateway: P2S OpenVPN プロトコル接続用の VPN クライアントAzure AD 認証'
description: ポイント対サイト VPN と Azure Active Directory 認証を使用して仮想ネットワークに接続するように VPN クライアントを構成する方法について説明します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/15/2020
ms.author: alzam
ms.openlocfilehash: 02ce8e1809c5dd404e7afa25178acf37e7346cab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102548412"
---
# <a name="azure-active-directory-authentication-configure-a-vpn-client-for-p2s-openvpn-protocol-connections"></a>Azure Active Directory 認証: P2S OpenVPN プロトコル接続用に VPN クライアントを構成する

この記事では、VPN クライアントからポイント対サイト VPN と Azure Active Directory 認証を使用して仮想ネットワークに接続する際の構成について説明します。 Azure AD を使用して接続と認証を行うには、まず Azure AD テナントを構成する必要があります。 詳細については、「[Azure AD テナントの構成](openvpn-azure-ad-tenant.md)」を参照してください。

[!INCLUDE [Windows 10 and OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

## <a name="working-with-client-profiles"></a><a name="profile"></a>クライアント プロファイルの操作

接続するには、VNet への接続を必要とするすべてのコンピューターで Azure VPN クライアントをダウンロードしたうえで、それぞれに VPN クライアント プロファイルを構成する必要があります。 1 つのコンピューターでクライアント プロファイルを作成した後、それをエクスポートして、別のコンピューターにインポートすることもできます。

### <a name="to-download-the-azure-vpn-client"></a>Azure VPN Client をダウンロードするには

この[リンク](https://go.microsoft.com/fwlink/?linkid=2117554)を使用して、Azure VPN Client をダウンロードします。 Azure VPN クライアントにバックグラウンドで実行するためのアクセス許可があることを確認してください。 アクセス許可を確認または有効にするには、次の手順に従います。

1. [スタート] にアクセスし、[設定]-> [プライバシー]-> [バックグラウンド アプリ] を選択します。
2. [バックグラウンド アプリ] で、 **[アプリのバックグラウンド実行を許可する]** がオンになっていることを確認します。
3. [バックグラウンドでの実行を許可するアプリを選んでください] で、Azure VPN クライアントの設定を **[オン]** にします。

  ![権限 (permission)](./media/openvpn-azure-ad-client/backgroundpermission.png)

### <a name="to-create-a-certificate-based-client-profile"></a><a name="cert"></a>証明書ベースのクライアント プロファイルを作成するには

証明書ベースのプロファイルを使用する場合は、クライアント コンピューターに適切な証明書がインストールされていることを確認してください。 証明書の詳細については、「[クライアント証明書のインストール](point-to-site-how-to-vpn-client-install-azure-cert.md)」を参照してください。

  ![cert](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="to-create-a-radius-client-profile"></a><a name="radius"></a>RADIUS クライアント プロファイルを作成するには

  ![radius](./media/openvpn-azure-ad-client/create/create-radius1.jpg)
  
> [!NOTE]
> サーバー シークレットは、P2S VPN クライアント プロファイルでエクスポートできます。  クライアント プロファイルをエクスポートする手順については、[こちら](about-vpn-profile-download.md)を参照してください。
>

### <a name="to-export-and-distribute-a-client-profile"></a><a name="export"></a>クライアント プロファイルをエクスポートして配布するには

使用するプロファイルを作成した後、それを他のユーザーに配布する必要がある場合は、次の手順に従ってプロファイルをエクスポートできます。

1. エクスポートする VPN クライアント プロファイルを強調表示し、 **[...]** を選択して、 **[エクスポート]** を選択します。

    ![[Azure VPN クライアント] ページを示すスクリーンショット。省略記号が選択され、[エクスポート] が強調表示されています。](./media/openvpn-azure-ad-client/export/export1.jpg)

2. このプロファイルを保存する場所を選択し、ファイル名はそのままで、 **[保存]** を選択して xml ファイルを保存します。

    ![エクスポート](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="to-import-a-client-profile"></a><a name="import"></a>クライアント プロファイルをインポートするには

1. ページ上で、 **[インポート]** を選択します。

    ![[追加] ボタンが選択され、[インポート] 操作がウィンドウの左下で強調表示されていることを示すスクリーンショット。](./media/openvpn-azure-ad-client/import/import1.jpg)

2. プロファイル xml ファイルを参照し、選択します。 ファイルが選択された状態で、 **[開く]** を選択します。

    ![プロファイル xml ファイルが選択されていることを示すスクリーンショット。](./media/openvpn-azure-ad-client/import/import2.jpg)

3. プロファイルの名前を指定し、 **[保存]** を選択します。

    ![[接続名] が強調表示され、[保存] ボタンが選択されていることを示すスクリーンショット。](./media/openvpn-azure-ad-client/import/import3.jpg)

4. **[接続]** を選択して VPN に接続します。

    ![VPN と [接続] ボタンが選択されていることを示すスクリーンショット。](./media/openvpn-azure-ad-client/import/import4.jpg)

5. 接続されると、アイコンが緑色に変わり、 **[接続済み]** と表示されます。

    ![import](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="to-delete-a-client-profile"></a><a name="delete"></a>クライアント プロファイルを削除するには

1. 削除するクライアント プロファイルの横にある省略記号を選択します。 **[削除]** を選択します。

    ![省略記号と [削除] オプションが選択されていることを示すスクリーンショット。](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. **[削除]** を選択して削除します。

    ![delete](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="create-a-connection"></a><a name="connection"></a>接続を作成する

1. ページ上で、 **[+]** を選択し、 **[追加 +]** を選択します。

    ![[追加] ボタンが選択されていることを示すスクリーンショット。](./media/openvpn-azure-ad-client/create/create1.jpg)

2. 接続情報を入力します。 値が不明な場合は、管理者に問い合わせてください。 値を入力したら、 **[保存]** を選択します。

    ![[VPN 接続] プロパティが強調表示され、[保存] ボタンがオンになっていることを示すスクリーンショット。](./media/openvpn-azure-ad-client/create/create2.jpg)

3. **[接続]** を選択して VPN に接続します。

    ![[接続] ボタンが選択されていることを示すスクリーンショット。](./media/openvpn-azure-ad-client/create/create3.jpg)

4. 適切な資格情報を選択し、 **[続行]** を選択します。

    ![資格情報の例が強調表示され、[続行] ボタンが選択されていることを示すスクリーンショット。](./media/openvpn-azure-ad-client/create/create4.jpg)

5. 正常に接続されると、アイコンが緑色に変わり、 **[接続済み]** と表示されます。

    ![connection](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="to-connect-automatically"></a><a name="autoconnect"></a>自動的に接続するには

Always-on を使用して自動的に接続するように構成するには、次の手順に従います。

1. VPN クライアントのホームページで、 **[VPN 設定]** を選択します。

    ![VPN ホームページのスクリーンショット。[VPN 設定] が選択されています。](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. アプリ切り替えのダイアログ ボックスで、 **[はい]** を選択します。

    ![[アプリを切り替えますか?] ダイアログのスクリーンショット。 [はい] ボタンが選択されています。](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. 設定する接続が既に接続済みでないことを確認してから、プロファイルを強調表示し、 **[自動的に接続する]** チェック ボックスを オンにします。

    ![[設定] ウィンドウのスクリーンショット。[自動的に接続する] チェックボックスがオンになっています。](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. **[接続]** を選択して接続を開始します。

    ![自動](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose-connection-issues"></a><a name="diagnose"></a>接続の問題を診断する

1. 接続の問題を診断するには、**診断** ツールを使用します。 診断する VPN 接続の横にある **[...]** を選択して、メニューを表示します。 次に、 **[診断]** を選択します。

    ![省略記号と [診断] が選択されていることを示すスクリーンショット。](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. **[接続プロパティ]** ページで、 **[Run Diagnosis]\(診断の実行\)** を選択します。

    ![[接続プロパティ] ページを示すスクリーンショット。[Run Diagnosis]\(診断の実行\) が選択されています。](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. 自分の資格情報でサインインします。

    ![[サインインしましょう] ダイアログを示すスクリーンショット。[職場または学校アカウント] が選択されています。](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. 診断結果を確認します。

    ![診断](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="faq"></a>よく寄せられる質問

### <a name="is-the-azure-vpn-client-supported-with-windows-fips-mode"></a>Azure VPN クライアントは、Windows FIPS モードでサポートされていますか?

はい。[KB4577063](https://support.microsoft.com/help/4577063/windows-10-update-kb4577063) 修正プログラムを使用してください。

### <a name="how-do-i-add-dns-suffixes-to-the-vpn-client"></a>VPN クライアントに DNS サフィックスを追加する方法

ダウンロードしたプロファイル XML ファイルを変更して、 **\<dnssuffixes>\<dnssufix> \</dnssufix>\</dnssuffixes>** タグを追加することができます

```
<azvpnprofile>
<clientconfig>

    <dnssuffixes>
          <dnssuffix>.mycorp.com</dnssuffix>
          <dnssuffix>.xyz.com</dnssuffix>
          <dnssuffix>.etc.net</dnssuffix>
    </dnssuffixes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-add-custom-dns-servers-to-the-vpn-client"></a>VPN クライアントにカスタム DNS サーバーを追加する方法

ダウンロードしたプロファイル XML ファイルを変更して、 **\<dnsservers>\<dnsserver> \</dnsserver>\</dnsservers>** タグを追加することができます

```
<azvpnprofile>
<clientconfig>

    <dnsservers>
        <dnsserver>x.x.x.x</dnsserver>
        <dnsserver>y.y.y.y</dnsserver>
    </dnsservers>
    
</clientconfig>
</azvpnprofile>
```

> [!NOTE]
> OpenVPN Azure AD クライアントは、DNS 名前解決ポリシー テーブル (NRPT) エントリを利用します。したがって、DNS サーバーは `ipconfig /all` の出力には一覧表示されません。 使用中の DNS 設定を確認するには、PowerShell で [Get-DnsClientNrptPolicy](/powershell/module/dnsclient/get-dnsclientnrptpolicy) を参照してください。
>

### <a name="how-do-i-add-custom-routes-to-the-vpn-client"></a>VPN クライアントにカスタム ルートを追加する方法

ダウンロードしたプロファイル XML ファイルを変更して、 **\<includeroutes>\<route>\<destination>\<mask> \</destination>\</mask>\</route>\</includeroutes>** タグを追加することができます

```
<azvpnprofile>
<clientconfig>

    <includeroutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </includeroutes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="how-do-i-block-exclude-routes-from-the-vpn-client"></a>VPN クライアントからルートをブロック (除外) する方法

ダウンロードしたプロファイル XML ファイルを変更して、 **\<excluderoutes>\<route>\<destination>\<mask> \</destination>\</mask>\</route>\</excluderoutes>** タグを追加することができます

```
<azvpnprofile>
<clientconfig>

    <excluderoutes>
        <route>
            <destination>x.x.x.x</destination><mask>24</mask>
        </route>
    </excluderoutes>
    
</clientconfig>
</azvpnprofile>
```

### <a name="can-i-import-the-profile-from-a-command-line-prompt"></a>コマンド ライン プロンプトからプロファイルをインポートできますか?

ダウンロードした **azurevpnconfig.xml** ファイルを **%userprofile%\AppData\Local\Packages\Microsoft.AzureVpn_8wekyb3d8bbwe\LocalState** フォルダーに置き、次のコマンドを実行することにより、コマンド ライン プロンプトからプロファイルをインポートできます。

```
azurevpn -i azurevpnconfig.xml 
```
強制的にインポートするには、 **-f** スイッチも使用します


## <a name="next-steps"></a>次のステップ

詳細については、[Azure AD 認証を使用する P2S Open VPN 接続用の Azure Active Directory テナントの作成](openvpn-azure-ad-tenant.md)に関するページを参照してください。