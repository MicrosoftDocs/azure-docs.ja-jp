---
title: ユーザー VPN 接続用に Azure AD 認証を構成する:Virtual WAN
description: ユーザー VPN 用に Azure Active Directory 認証を構成する方法について説明します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 10/14/2020
ms.author: alzam
ms.openlocfilehash: 9cc68eb60096c4431acfc988c87ca9bf99f1f045
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93043398"
---
# <a name="configure-azure-active-directory-authentication-for-user-vpn"></a>ユーザー VPN 用に Azure Active Directory 認証を構成する

この記事では、Virtual WAN のユーザー VPN 用に Azure AD 認証を構成し、OpenVPN VPN 接続経由で Azure のリソースに接続する方法について説明します。 Azure Active Directory 認証は、OpenVPN プロトコルを使用するゲートウェイと、Windows を実行するクライアントでのみ使用できます。

この種類の接続では、クライアント コンピューターでクライアントを構成する必要があります。 Virtual WAN の詳細については、[Virtual WAN の概要](virtual-wan-about.md)に関するページを参照してください。

この記事では、次のことについて説明します。

* Virtual WAN を作成する
* 仮想ハブを作成する
* ユーザー VPN 構成を作成する
* Virtual WAN ユーザー VPN プロファイルをダウンロードする
* ユーザー VPN 構成を仮想ハブへ適用する
* VNet を仮想ハブに接続する
* ユーザー VPN クライアント構成をダウンロードして適用する
* Virtual WAN を表示する

![Virtual WAN のダイアグラム](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>開始する前に

構成を開始する前に、以下の条件を満たしていることを確認します。

* 接続先の仮想ネットワークが用意されていること。 オンプレミス ネットワークのどのサブネットも接続先の仮想ネットワークと重複していないことを確認してください。 Azure portal で仮想ネットワークを作成するには、[クイックスタート](../virtual-network/quick-create-portal.md)を参照してください。

* 仮想ネットワークに仮想ネットワーク ゲートウェイが存在しないこと。 仮想ネットワークにゲートウェイ (VPN または ExpressRoute のどちらか) が存在する場合は、すべてのゲートウェイを削除する必要があります。 代わりに、この構成では、仮想ネットワークが Virtual WAN ハブ ゲートウェイに接続されている必要があります。

* ハブ リージョンの IP アドレス範囲を取得します。 このハブは、Virtual WAN によって作成および使用される仮想ネットワークです。 ハブに指定するアドレス範囲が、接続先の既存の仮想ネットワークと重複することはできません。 さらに、オンプレミスで接続するアドレス範囲と重複することもできません。 オンプレミス ネットワーク構成に含まれている IP アドレス範囲になじみがない場合は、それらの詳細を提供できるだれかと調整してください。

* Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Virtual WAN を作成する

ブラウザーから [Azure ポータル](https://portal.azure.com) に移動し、Azure アカウントでサインインします。

1. [仮想 WAN] ページに移動します。 ポータルで **+ [リソースの作成]** をクリックします。 検索ボックスに「**Virtual WAN**」と入力し、Enter キーを押します。
2. 結果から **[Virtual WAN]** を選択します。 [Virtual WAN] (仮想 WAN) ページで、 **[作成]** をクリックして [WAN の作成] ページを開きます。
3. **[WAN の作成]** ページの **[基本]** タブで、次のフィールドに入力します。

   ![Virtual WAN](./media/virtual-wan-point-to-site-azure-ad/vwan.png)

   * **[サブスクリプション]** - 使用するサブスクリプションを選択します。
   * **[リソース グループ]** - 新規作成するか、または既存のものを使用します。
   * **[リソース グループの場所]** - ドロップダウンからリソースの場所を選択します。 WAN はグローバルなリソースであり、特定のリージョンに存在するものではありません。 ただし、作成する WAN リソースをより簡単に管理および検索するために、リージョンを選択する必要があります。
   * **[名前]** - WAN に付ける名前を入力します。
   * **種類:** Standard。 Basic WAN を作成する場合は、Basic ハブのみを作成できます。 Basic ハブでは、VPN サイト間接続のみが可能です。
4. フィールドへの入力を完了したら、 **[確認および作成]** を選択します。
5. 検証に合格したら、 **[作成]** を選択して仮想 WAN を作成します。

## <a name="create-an-empty-virtual-hub"></a><a name="site"></a>空の仮想ハブを作成する

1. Virtual WAN で [ハブ] を選択し、 **[+ 新しいハブ]** をクリックします。

   ![[Hubs configuration]\(ハブの構成\) ダイアログ ボックスを示すスクリーンショット。[新しいハブ] が選択されています。](media/virtual-wan-point-to-site-azure-ad/hub1.jpg)
2. [仮想ハブを作成する] ページで、次のフィールドに入力します。

   **[リージョン]** - 仮想ハブをデプロイするリージョンを選択します。

   **[名前]** - 仮想ハブの呼び出しに使う名前を入力します。

   **[ハブ プライベート アドレス空間]** - CIDR 表記のハブのアドレス範囲。

   ![[仮想ハブを作成する] ウィンドウを示すスクリーンショット。ここでは、値を入力できます。](media/virtual-wan-point-to-site-azure-ad/hub2.jpg)  
3. **[Review + create]\(レビュー + 作成\)** をクリックします。
4. **[検証に成功しました]** ページで **[作成]** をクリックします。

## <a name="create-a-new-user-vpn-configuration"></a><a name="site"></a>新しいユーザー VPN 構成を作成する

ユーザー VPN 構成には、リモート クライアントを接続するためのパラメーターが定義されています。

1. 仮想 WAN の下で、 **[User VPN configurations]\(ユーザー VPN の構成\)** を選択します。

   ![[ユーザー VPN 構成] メニュー項目が選択されていることを示すスクリーンショット。](media/virtual-wan-point-to-site-azure-ad/aadportal1.jpg)

2. **[+Create user VPN config]\(+ ユーザー VPN 構成の作成\)** をクリックします。

   ![[Create user VPN config]\(ユーザー VPN 構成の作成\) リンクを示すスクリーンショット。](media/virtual-wan-point-to-site-azure-ad/aadportal2.jpg)

3. 情報を入力して **[作成]** をクリックします。

   * **構成名** - ユーザー VPN 構成に付ける名前を入力します。
   * **トンネルの種類** - OpenVPN を選択します。
   * **認証方法**: [Azure Active Directory] を選択します。
   * **対象ユーザー** - Azure AD テナントに登録されている [Azure VPN](openvpn-azure-ad-tenant.md) エンタープライズ アプリケーションのアプリケーション ID を入力します。 
   * **発行者** - `https://sts.windows.net/<your Directory ID>/`
   * **AAD テナント** - `https://login.microsoftonline.com/<your Directory ID>`
  
   ![[Create new user VPN config]\(新しいユーザー VPN 構成の作成\) ウィンドウを示すスクリーンショット。ここでは、値を入力できます。](media/virtual-wan-point-to-site-azure-ad/aadportal3.jpg)

## <a name="edit-hub-assignment"></a><a name="hub"></a>ハブの割り当てを編集する

1. 仮想 WAN の下にある **[ハブ]** ブレードに移動します。
2. VPN サーバーの構成を関連付けるハブを選択し、省略記号 (...) をクリックします。

   ![メニューで [仮想ハブを編集する] が選択されていることを示すスクリーンショット。](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. **[仮想ハブを編集する]** をクリックします。
4. **[ポイント対サイト ゲートウェイを含める]** チェック ボックスをオンにし、必要な **[ゲートウェイ スケール ユニット]** を選択します。

   ![[仮想ハブを編集する] ダイアログ ボックスを示すスクリーンショット。ここでは、ゲートウェイのスケール ユニットを選択できます。](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. VPN クライアントの IP アドレスの割り当て元となる **[アドレス プール]** を入力します。
6. **[Confirm]\(確認\)** をクリックします。
7. この操作は、完了するまで最大 30 分かかることがあります。

## <a name="download-user-vpn-profile"></a><a name="device"></a>ユーザー VPN プロファイルをダウンロードする

VPN プロファイルを使用してクライアントを構成します。

1. Virtual WAN のページで、 **[ユーザー VPN 構成]** をクリックします。
2. ページの上部にある **[Download user VPN config]\(ユーザー VPN 構成をダウンロードする\)** をクリックします。
3. ファイルの作成が完了したら、リンクをクリックしてファイルをダウンロードできます。
4. プロファイル ファイルを使用して VPN クライアントを構成します。

## <a name="configure-user-vpn-clients"></a>ユーザー VPN クライアントを構成する

接続するには、Azure VPN Client (プレビュー) をダウンロードし、前の手順でダウンロードした VPN クライアント プロファイルを、VNet に接続する各コンピューターでインポートします。

> [!NOTE]
> Azure AD 認証は、OpenVPN&reg; プロトコル接続でのみサポートされています。
>

#### <a name="to-download-the-azure-vpn-client"></a>Azure VPN Client をダウンロードするには

この[リンク](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab)を使用して、Azure VPN Client をダウンロードします。

#### <a name="to-import-a-client-profile"></a><a name="import"></a>クライアント プロファイルをインポートするには

1. ページ上で、 **[インポート]** を選択します。

    ![プラスのメニューで [インポート] が選択されていることを示すスクリーンショット。](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. プロファイル xml ファイルを参照し、選択します。 ファイルが選択された状態で、 **[開く]** を選択します。

    ![[開く] ダイアログ ボックスを示すスクリーンショット。ここでは、ファイルを選択できます。](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. プロファイルの名前を指定し、 **[保存]** を選択します。

    ![[接続名] が追加され、[保存] ボタンが選択されていることを示すスクリーンショット。](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. **[接続]** を選択して VPN に接続します。

    ![作成したばかりの接続用の [接続] ボタンを示すスクリーンショット。](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. 接続されると、アイコンが緑色に変わり、 **[接続済み]** と表示されます。

    ![[接続済み] 状態の接続と [切断] オプションを示すスクリーンショット。](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="to-delete-a-client-profile"></a><a name="delete"></a>クライアント プロファイルを削除するには

1. 削除するクライアント プロファイルの横にある省略記号 (...) を選択します。 **[削除]** を選択します。

    ![メニューで [削除] が選択されていることを示すスクリーンショット。](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. **[削除]** を選択して削除します。

    ![[確認] ダイアログ ボックスと [削除] オプションまたは [キャンセル] オプションを示すスクリーンショット。](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose-connection-issues"></a><a name="diagnose"></a>接続の問題を診断する

1. 接続の問題を診断するには、**診断** ツールを使用します。 診断する VPN 接続の横にある省略記号 (...) を選択して、メニューを表示します。 次に、 **[診断]** を選択します。

    ![メニューで [診断] が選択されていることを示すスクリーンショット。](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. **[接続プロパティ]** ページで、 **[Run Diagnosis]\(診断の実行\)** を選択します。

    ![接続の [Run Diagnosis]\(診断の実行\) ボタンを示すスクリーンショット。](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. 自分の資格情報でサインインします。

    ![このアクションの [サインイン] ダイアログ ボックスを示すスクリーンショット。](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. 診断結果を確認します。

    ![診断の結果を示すスクリーンショット。](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>仮想 WAN を表示する

1. 仮想 WAN に移動します。
2. [概要] ページのマップ上の各ポイントは、ハブを表します。
3. ハブと接続のセクションでは、ハブの状態、サイト、リージョン、VPN 接続の状態、および入出力バイト数を表示できます。

## <a name="clean-up-resources"></a><a name="cleanup"></a>リソースをクリーンアップする

これらのリソースが不要になったら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) を使用して、リソース グループとその中のすべてのリソースを削除できます。 "myResourceGroup" をリソース グループの名前に置き換えて、次の PowerShell コマンドを実行します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>次のステップ

Virtual WAN の詳細については、[Virtual WAN の概要](virtual-wan-about.md)に関するページを参照してください。
