---
title: Azure Virtual WAN を使用して Azure へのポイント対サイト接続を作成する | Microsoft Docs
description: このチュートリアルでは、Azure Virtual WAN を使用して Azure へのポイント対サイト VPN 接続を作成する方法を学習します。
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 02/27/2019
ms.author: alzam
Customer intent: As someone with a networking background, I want to connect remote users to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: 9fe0c7f7ae0c19833421b647449f0e4100904f5b
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226234"
---
# <a name="tutorial-create-a-point-to-site-connection-using-azure-virtual-wan-preview"></a>チュートリアル:Azure Virtual WAN (プレビュー) を使用してポイント対サイト接続を作成する

このチュートリアルでは、Virtual WAN を使用して IPsec/IKE (IKEv2) または OpenVPN VPN 接続経由で Azure のリソースに接続する方法を示します。 この種類の接続では、クライアント コンピューターでクライアントを構成する必要があります。 Virtual WAN の詳細については、[Virtual WAN の概要](virtual-wan-about.md)に関するページを参照してください。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * WAN を作成する
> * P2S の構成を作成する
> * ハブを作成する
> * P2S の構成をハブに適用する
> * VNet をハブに接続する
> * VPN クライアント構成をダウンロードして適用する
> * 仮想 WAN を表示する
> * リソースの正常性を表示する
> * 接続を監視する

> [!IMPORTANT]
> このパブリック プレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することは避けてください。 特定の機能はサポート対象ではなく、機能が制限されることがあるか、Azure の場所によっては利用できない場合があります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。
>

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="register"></a>この機能を登録する

Azure Cloud Shell を使用してこの機能を簡単に登録するには、**[TryIt]** をクリックします。 PowerShell をローカルで実行する場合は、最新バージョンであることを確認し、**Connect-AzAccount** および **Select-AzSubscription** コマンドを使用してサインインしてください。

>[!NOTE]
>この機能を登録しないと、それを使用できなくなるか、またはポータルで表示できなくなります。
>
>

**[TryIt]** をクリックした後、Azure Cloud Shell を開くには、次のコマンドをコピーして貼り付けます。

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowP2SCortexAccess
```
 
```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowP2SCortexAccess
```

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```

機能が登録済みと表示されたら、サブスクリプションを Microsoft.Network 名前空間に登録します。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="vnet"></a>1.仮想ネットワークの作成

[!INCLUDE [Create a virtual network](../../includes/virtual-wan-tutorial-vnet-include.md)]

## <a name="openvwan"></a>2.仮想 WAN を作成する

ブラウザーから、[Azure Portal (プレビュー)](https://aka.ms/azurevirtualwanpreviewfeatures) に移動し、Azure アカウントでサインインします。

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="hub"></a>3.ハブを作成する

> [!NOTE]
> この手順では、[ポイント対サイト ゲートウェイを含める] 設定をオンにしないでください。
>

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="site"></a>4.P2S の構成を作成する

P2S 構成には、リモート クライアントを接続するためのパラメーターが定義されています。

1. **[すべてのリソース]** に移動します。
2. 作成した仮想 WAN をクリックします。
3. **[Virtual WAN architecture]\(仮想 WAN アーキテクチャ\)** で、**[ポイント対サイト構成]** をクリックします。
4. ページの上部にある **[+ ポイント対サイトの構成を追加]** をクリックし、**[ポイント対サイトの構成の新規作成]** ページを開きます。
5. **[ポイント対サイトの構成の新規作成]** ページで次のフィールドを入力します。

   *  **構成名**: 構成の呼称です。
   *  **トンネルの種類**: トンネルに使用するプロトコル。
   *  **アドレス プール**: クライアントが割り当てられる IP アドレス プールです。
   *  **ルート証明書名**: 証明書のわかりやすい名前。
   *  **ルート証明書データ**: Base-64 でエンコードされた X.509 証明書データです。

6. **[作成]** をクリックして構成を作成します。

## <a name="hub"></a>5.ハブの割り当てを編集する

1. 仮想 WAN のページで、**[ハブ]** をクリックします。
2. ポイント対サイト構成の割り当て先となるハブを選択します。
3. **[...]** をクリックして、**[仮想ハブを編集する]** を選択します。
4. **[ポイント対サイト ゲートウェイを含める]** チェック ボックスをオンにします。
5. ドロップダウン リストから、**[ゲートウェイ スケール ユニット]** を選択します。
6. ドロップダウン リストから、作成した**ポイント対サイトの構成**を選択します。
7. クライアントの**アドレス プール**を構成します。
8. **[Confirm]\(確認\)** をクリックします。 この操作は、完了するまで最大 30 分かかることがあります。

## <a name="vnet"></a>6.VNet をハブに接続する

この手順では、ハブと VNet の間にピアリング接続を作成します。 接続する VNet ごとにこれらの手順を繰り返します。

1. 仮想 WAN のページで、**[仮想ネットワーク接続]** をクリックします。
2. 仮想ネットワーク接続のページで、**[+ 接続の追加]** をクリックします。
3. **[接続の追加]** ページで、次のフィールドに入力します。

    * **[接続名]** - 接続に名前を付けます。
    * **[ハブ]** - この接続に関連付けるハブを選択します。
    * **[サブスクリプション]** - サブスクリプションを確認します。
    * **[仮想ネットワーク]** - このハブに接続する仮想ネットワークを選択します。 仮想ネットワークに既存の仮想ネットワーク ゲートウェイを設定することはできません。
4. **[OK]** をクリックして接続を追加します。

## <a name="device"></a>7.VPN プロファイルをダウンロードする

VPN プロファイルを使用してクライアントを構成します。

1. 仮想 WAN のページで、**[ハブ]** をクリックします。
2. プロファイルをダウンロードするハブを選択します。
3. **[...]** をクリックして、**[プロファイルのダウンロード]** を選択します。 
4. ファイルの作成が完了したら、リンクをクリックしてファイルをダウンロードできます。
4. プロファイル ファイルを使用してポイント対サイト クライアントを構成します。

## <a name="device"></a>8.ポイント対サイト クライアントを構成する
ダウンロードしたプロファイルを使用して、リモート アクセス クライアントを構成します。 オペレーティング システムごとに手順は異なります。以下の正しい手順を実行してください。

### <a name="microsoft-windows"></a>Microsoft Windows
#### <a name="openvpn"></a>OpenVPN

1.  公式 Web サイトから OpenVPN クライアントをダウンロードしてインストールします。
2.  ゲートウェイの VPN プロファイルをダウンロードします。 この操作は、Azure portal の [ポイント対サイト構成] タブまたは PowerShell の New-AzVpnClientConfiguration で実行できます。
3.  プロファイルを展開します。 メモ帳で OpenVPN フォルダーの vpnconfig.ovpn 構成ファイルを開きます。
4.  P2S クライアント証明書セクションに、base64 の P2S クライアント証明書の公開キーを指定します。 PEM 形式の証明書の場合、.cer ファイルを開き、証明書ヘッダー間にある base64 キーを上書きしてコピーします。 証明書をエクスポートしてエンコードされた公開キーを取得する方法については、こちらを参照してください。
5.  秘密キー セクションに、base64 の P2S クライアント証明書の秘密キーを指定します。 秘密キーを抽出する方法については、こちらを参照してください。
6.  その他のフィールドは変更しないでください。 クライアント入力に入力された構成を使用して VPN に接続します。
7.  vpnconfig.ovpn ファイルを C:\Program Files\OpenVPN\config フォルダーにコピーします。
8.  システム トレイの OpenVPN アイコンを右クリックし、[接続] をクリックします。

#### <a name="ikev2"></a>IKEv2

1. Windows コンピューターのアーキテクチャに対応する VPN クライアント構成ファイルを選択します。 64 ビットのプロセッサ アーキテクチャの場合は、"VpnClientSetupAmd64" インストーラー パッケージを選択します。 32 ビットのプロセッサ アーキテクチャの場合は、"VpnClientSetupX86" インストーラー パッケージを選択します。
2. パッケージをダブルクリックしてインストールします。 SmartScreen ポップアップが表示された場合は、[詳細]、[実行] の順にクリックします。
3. クライアント コンピューターで [ネットワークの設定] に移動し、[VPN] をクリックします。 VPN 接続により、その接続先の仮想ネットワークの名前が表示されます。
4. 接続を試行する前に、クライアント コンピューターにクライアント証明書をインストール済みであることを確認します。 ネイティブ Azure 証明書の認証タイプを使用する場合、認証にはクライアント証明書が必要です。 証明書の生成の詳細については、証明書の生成に関するページを参照してください。 クライアント証明書のインストール方法については、クライアント証明書のインストールに関するページを参照してください。

### <a name="mac-os-x"></a>Mac (OS X)
#### <a name="openvpn"></a>OpenVPN

1.  TunnelBlik などの OpenVPN クライアントを https://tunnelblick.net/downloads.html からダウンロードしてインストールします。 
2.  ゲートウェイの VPN プロファイルをダウンロードします。 この操作は、Azure portal の [ポイント対サイト構成] タブまたは PowerShell の New-AzVpnClientConfiguration で実行できます。
3.  プロファイルを展開します。 メモ帳で OpenVPN フォルダーの vpnconfig.ovpn 構成ファイルを開きます。
4.  P2S クライアント証明書セクションに、base64 の P2S クライアント証明書の公開キーを指定します。 PEM 形式の証明書の場合、.cer ファイルを開き、証明書ヘッダー間にある base64 キーを上書きしてコピーします。 証明書をエクスポートしてエンコードされた公開キーを取得する方法については、こちらを参照してください。
5.  秘密キー セクションに、base64 の P2S クライアント証明書の秘密キーを指定します。 秘密キーを抽出する方法については、こちらを参照してください。
6.  その他のフィールドは変更しないでください。 クライアント入力に入力された構成を使用して VPN に接続します。
7.  プロファイル ファイルをダブルクリックして tunnelblik にプロファイルを作成します
8.  アプリケーション フォルダーから Tunnelblik を起動します
9.  システム トレイの Tunnelblik アイコンをクリックし、[接続] を選択します

#### <a name="ikev2"></a>IKEv2

Azure では、ネイティブの Azure 証明書の認証用の mobileconfig ファイルは提供されません。 Azure に接続するすべての Mac で、ネイティブの IKEv2 VPN クライアントを手動で構成する必要があります。 Generic フォルダーには、構成に必要な情報がすべて揃っています。 ダウンロードに、Generic フォルダーが表示されない場合は、IKEv2 がトンネルの種類として選択されていない可能性があります。 IKEv2 を選択したら、もう一度 zip ファイルを生成して、Generic フォルダーを取得します。 Generic フォルダーには、次のファイルが含まれています。

- VpnSettings.xml。サーバー アドレスやトンネルの種類など、重要な設定が含まれています。
- VpnServerRoot.cer。P2S 接続の設定中に Azure VPN Gateway を検証するために必要なルート証明書が含まれています。

## <a name="viewwan"></a>9.仮想 WAN を表示する

1. 仮想 WAN に移動します。
2. [概要] ページのマップ上の各ポイントは、ハブを表します。 任意のポイントにカーソルを置くと、ハブの正常性の概要が表示されます。
3. ハブと接続のセクションでは、ハブの状態、サイト、リージョン、VPN 接続の状態、および入出力バイト数を表示できます。

## <a name="viewhealth"></a>10.リソースの正常性を表示する

1. WAN に移動します。
2. WAN のページの **[サポート + トラブルシューティング]** セクションで、**[正常性]** をクリックしてリソースを表示します。

## <a name="connectmon"></a>11.接続を監視する

Azure VM とリモート サイト間の通信を監視するための接続を作成します。 接続モニターを設定する方法については、[ネットワーク通信の監視](~/articles/network-watcher/connection-monitor.md)に関するページを参照してください。 ソース フィールドは Azure の VM IP で、宛先 IP はサイト IP です。

## <a name="cleanup"></a>12.リソースのクリーンアップ

これらのリソースが不要になったら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) を使用して、リソース グループとその中のすべてのリソースを削除できます。 "myResourceGroup" をリソース グループの名前に置き換えて、次の PowerShell コマンドを実行します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * WAN を作成する
> * サイトを作成する
> * ハブを作成する
> * ハブをサイトに接続する
> * VNet をハブに接続する
> * VPN デバイス構成をダウンロードして適用する
> * 仮想 WAN を表示する
> * リソースの正常性を表示する
> * 接続を監視する

Virtual WAN の詳細については、[Virtual WAN の概要](virtual-wan-about.md)に関するページを参照してください。
