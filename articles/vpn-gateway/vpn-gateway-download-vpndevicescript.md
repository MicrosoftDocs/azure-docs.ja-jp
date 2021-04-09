---
title: S2S VPN 接続用の VPN デバイス構成スクリプトをダウンロードする
description: この記事では、Azure Resource Manager を使って Azure VPN ゲートウェイで S2S VPN 接続用の VPN デバイス構成スクリプトをダウンロードする手順を説明します。
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: d2593c656e17310c5afb4a897b94cc8c12a618f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879496"
---
# <a name="download-vpn-device-configuration-scripts-for-s2s-vpn-connections"></a>S2S VPN 接続用の VPN デバイス構成スクリプトをダウンロードする

この記事では、Azure Resource Manager を使って Azure VPN ゲートウェイで S2S VPN 接続用の VPN デバイス構成スクリプトをダウンロードする手順を説明します。 次の図はワークフローの概要です。

![download-script](./media/vpn-gateway-download-vpndevicescript/downloaddevicescript.png)

以下のデバイスには、使用可能なスクリプトがあります。

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="about-vpn-device-configuration-scripts"></a><a name="about"></a>VPN デバイス構成スクリプトについて

クロスプレミスの VPN 接続は、Azure VPN ゲートウェイとオンプレミスの VPN デバイスおよび両者を接続する IPsec S2S VPN トンネルで構成されます。 一般的なワークフローには次の手順が含まれます。

1. Azure VPN ゲートウェイ (仮想ネットワーク ゲートウェイ) を作成して構成します
2. オンプレミスのネットワークと VPN デバイスを表す Azure ローカル ネットワーク ゲートウェイを作成して構成します
3. Azure VPN ゲートウェイとローカル ネットワーク ゲートウェイの間に Azure VPN 接続を作成して構成します
4. ローカル ネットワーク ゲートウェイで表されるオンプレミスの VPN デバイスを構成して、Azure VPN ゲートウェイとの実際の S2S VPN トンネルを確立します

ステップ 1 ～ 3 は、Azure [Portal](./tutorial-site-to-site-portal.md)、[PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)、または [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md) を使って実行できます。 最後のステップには、Azure の外部でオンプレミスの VPN デバイスを構成する作業が含まれます。 この機能では、Azure VPN ゲートウェイ、仮想ネットワーク、オンプレミス ネットワークの各アドレス プレフィックスや、VPN 接続のプロパティなどに対応する値が既に設定されている、お使いの VPN デバイス用の構成スクリプトをダウンロードできます。 スクリプトを基にして使い始めることも、構成コンソールでオンプレミスの VPN デバイスにスクリプトを直接適用することもできます。

> [!IMPORTANT]
> * VPN デバイスごとに構成スクリプトの構文は異なり、モデルとファームウェアのバージョンに大きく依存します。 使用可能なテンプレートに対するデバイスのモデルとバージョンの情報に特に注意してください。
> * 一部のパラメーター値はデバイスに固有である必要があり、デバイスにアクセスしないで決定することはできません。 Azure 生成の構成スクリプトではこれらの値が事前に設定されていますが、提供されている値がお使いのデバイスで有効であることを確認する必要があります。 次に例を示します。
>    * インターフェイス番号
>    * アクセス制御リスト番号
>    * ポリシーの名前または番号など
> * スクリプトを適用する前に確認する必要があるパラメーターを探すには、スクリプトに埋め込まれているキーワード "**REPLACE**" を検索してください。
> * 一部のテンプレートには、構成を削除するために適用できる "**CLEANUP**" セクションが含まれます。 クリーンアップ セクションは、既定ではコメントになっています。

## <a name="download-the-configuration-script-from-azure-portal"></a>Azure Portal から構成スクリプトをダウンロードする

Azure VPN ゲートウェイ、ローカル ネットワーク ゲートウェイ、およびそれらを接続する接続リソースを作成します。 次のページではその手順が説明されています。

* [Azure Portal でサイト間接続を作成する](./tutorial-site-to-site-portal.md)

接続リソースを作成した後、以下の手順に従って VPN デバイス構成スクリプトをダウンロードします。

1. ブラウザーから [Azure Portal](https://portal.azure.com) に移動し、必要な場合は Azure アカウントでサインインします。
2. 作成した接続リソースに移動します。 [すべてのサービス]、[ネットワーク]、[接続] の順にクリックすると、すべての接続リソースの一覧が表示されます。

    ![connection-list](./media/vpn-gateway-download-vpndevicescript/connectionlist.png)

3. 構成する接続をクリックします。

    ![connection-overview](./media/vpn-gateway-download-vpndevicescript/connectionoverview.png)

4. 接続の概要ページの赤で強調した [構成のダウンロード] リンクをクリックします。[構成のダウンロード] ページが開きます。

    ![download-script-1](./media/vpn-gateway-download-vpndevicescript/downloadscript-1.png)

5. お使いの VPN デバイスのモデル ファミリとファームウェア バージョンを選び、[構成のダウンロード] ボタンをクリックします。

    ![download66-script-2](./media/vpn-gateway-download-vpndevicescript/downloadscript-2.PNG)

6. ブラウザーでダウンロードしたスクリプト (テキスト ファイル) を保存するように求められます。
7. 構成スクリプトをダウンロードした後、テキスト エディターでそれを開いて、キーワード "REPLACE" を検索し、置き換えが必要な可能性のあるパラメーターを探して調べます。

    ![edit-script](./media/vpn-gateway-download-vpndevicescript/editscript.png)

## <a name="download-the-configuration-script-using-azure-powershell"></a>Azure PowerShell を使用して構成スクリプトをダウンロードする



次の例で示すように、Azure PowerShell を使って構成スクリプトをダウンロードすることもできます。

```azurepowershell-interactive
$RG          = "TestRG1"
$GWName      = "VNet1GW"
$Connection  = "VNet1toSite1"

# List the available VPN device models and versions
Get-AzVirtualNetworkGatewaySupportedVpnDevice -Name $GWName -ResourceGroupName $RG

# Download the configuration script for the connection
Get-AzVirtualNetworkGatewayConnectionVpnDeviceConfigScript -Name $Connection -ResourceGroupName $RG -DeviceVendor Juniper -DeviceFamily Juniper_SRX_GA -FirmwareVersion Juniper_SRX_12.x_GA
```

## <a name="apply-the-configuration-script-to-your-vpn-device"></a>VPN デバイスに構成スクリプトを適用する

構成スクリプトをダウンロードして検証した後は、お使いの VPN デバイスにスクリプトを適用します。 実際の手順は、お使いの VPN デバイスの製造元とモデルによって異なります。 お使いの VPN デバイスの操作マニュアルまたは説明ページをご覧ください。

## <a name="next-steps"></a>次のステップ

引き続き[サイト間接続](./tutorial-site-to-site-portal.md)を構成してください。