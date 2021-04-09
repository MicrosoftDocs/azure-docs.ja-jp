---
title: チュートリアル - Azure portal を使用してネットワーク間の通信に関する問題を診断する
titleSuffix: Azure Network Watcher
description: このチュートリアルでは、Azure 仮想ネットワークと、Azure 仮想ネットワーク ゲートウェイを介して接続されているオンプレミスまたはその他の仮想ネットワークの間の通信に関する問題を、Network Watcher の VPN の診断機能を使用して診断する方法を説明します。
services: network-watcher
documentationcenter: na
author: damendo
Customer intent: I need to determine why resources in a virtual network can't communicate with resources in a different network.
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.custom: mvc
ms.openlocfilehash: 1a556ea66bb54f994349ce9ba4d12fb486827edf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98013297"
---
# <a name="tutorial-diagnose-a-communication-problem-between-networks-using-the-azure-portal"></a>チュートリアル:Azure portal を使用してネットワーク間の通信に関する問題を診断する

仮想ネットワーク ゲートウェイは、Azure 仮想ネットワーク をオンプレミスやその他の仮想ネットワークに接続します。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Network Watcher の VPN 診断機能で仮想ネットワーク ゲートウェイの問題を診断する
> * ゲートウェイ接続の問題を診断する
> * ゲートウェイの問題を解決する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

VPN 診断を使用するには、既存の動作している VPN ゲートウェイが必要です。 診断する既存の VPN ゲートウェイがない場合は、[PowerShell スクリプト](../vpn-gateway/scripts/vpn-gateway-sample-site-to-site-powershell.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)を使用してゲートウェイを 1 つデプロイすることができます。 次の場所から PowerShell スクリプトを実行できます。
- **ローカルにインストールされている PowerShell**: このスクリプトは Azure PowerShell `Az` モジュールを必要とします。 インストールされているバージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell のインストール](/powershell/azure/install-Az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。
- **Azure Cloud Shell**: [Azure Cloud Shell](https://shell.azure.com/powershell) には最新バージョンの PowerShell がインストールされ構成されており、ユーザーは Azure にログインされます。

このスクリプトで VPN ゲートウェイを作成するのに、約 1 時間かかります。 残りの手順は、診断しようとしているゲートウェイが、このスクリプトによってデプロイされたものであることが前提となっています。 そうではなく、既存のゲートウェイを診断する場合は、異なる結果となります。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="enable-network-watcher"></a>Network Watcher を有効にする

米国東部リージョンで既に Network Watcher を有効にしている場合は、「[ゲートウェイを診断する](#diagnose-a-gateway)」に進んでください。

1. ポータルで **[すべてのサービス]** を選択します。 **[フィルター]** ボックスに、「*Network Watcher*」と入力します。 結果に **[Network Watcher]** が表示されたら、それを選択します。
2. 次の図に示すように、 **[リージョン]** を選択して展開し、 **[米国東部]** の右側の **[...]** を選択します。

    ![Network Watcher を有効にする](./media/diagnose-communication-problem-between-networks/enable-network-watcher.png)

3. **[Network Watcher の有効化]** を選択します。

## <a name="diagnose-a-gateway"></a>ゲートウェイを診断する

1. ポータルの左側にある **[その他のサービス]** を選びます。
2. **[フィルター]** ボックスに「*network watcher*」の入力を始めます。 検索結果に **[Network Watcher]** が表示されたら、それを選択します。
3. **[ネットワーク診断ツール]** で、 **[VPN Diagnostics (VPN 診断)]** を選択します。
4. **[ストレージ アカウント]** を選択し、診断情報の書き込み先にするストレージ アカウントを選択します。
5. **[ストレージ アカウント]** の一覧から、使用するストレージ アカウントを選択します。 既存のストレージ アカウントを持っていない場合は、 **[+ ストレージ アカウント]** を選択し、必要な情報を入力または選択して、 **[作成]** を選択してストレージ アカウントを作成します。 [前提条件](#prerequisites)で、スクリプトを使用して VPN ゲートウェイを作成した場合は、同じリソース グループの *TestRG1* 内に、ゲートウェイとしてストレージ アカウントを作成することをお勧めします。
6. **[コンテナー]** の一覧から、使用するコンテナーを選択し、 **[選択]** を選択します。 コンテナーがない場合は、 **[+ コンテナー]** を選択し、コンテナーの名前を入力してから、 **[OK]** を選択します。
7. ゲートウェイを選択し、 **[トラブルシューティングの開始]** を選択します。 次の図に示すように、テストは **Vnet1GW** という名前のゲートウェイに対して実行されます。

    ![VPN 診断](./media/diagnose-communication-problem-between-networks/vpn-diagnostics.png)

8. テストの実行中には、前の図のように、 **[Not started (未開始)]** が表示されている **[TROUBLESHOOTING STATUS (トラブルシューティングの状態)]** 列に、 **[Running (実行中)]** と表示されます。 このテストの実行には数分かかることがあります。
9. 完了したテストの状態を表示します。 次の図は、完了した診断テストの状態の結果を示しています。

    ![概要と詳細を含む、診断テストの状態の結果 (この例では異常) を示すスクリーンショット。](./media/diagnose-communication-problem-between-networks/status.png)

    **[TROUBLESHOOTING STATUS (トラブルシューティングの状態)]** が **[異常]** であるのを確認できるのと同時に、 **[状態]** タブで問題の **[概要]** と **[Detail (詳細)]** を確認できます。
10. **[アクション]** タブを選択すると、VPN 診断から追加の情報が提供されます。 この例では、次の図に示すように、VPN 診断により、各接続の正常性を調べる必要があることがわかります。

    ![追加情報を提供する [アクション] タブを示すスクリーンショット。](./media/diagnose-communication-problem-between-networks/action.png)

## <a name="diagnose-a-gateway-connection"></a>ゲートウェイ接続を診断する

ゲートウェイは、ゲートウェイ接続を介して他のネットワークに接続されています。 仮想ネットワークと、接続されるネットワークの間で正常な通信が行われるには、ゲートウェイとゲートウェイ接続の両方が正常な状態である必要があります。

1. 今回は接続を選択して、「[ゲートウェイを診断する](#diagnose-a-gateway)」の手順 7. を完了します。 次の例では、**VNet1toSite1** という名前の接続がテストされます。

    ![選択した接続のトラブルシューティングを開始する方法を示すスクリーンショット。](./media/diagnose-communication-problem-between-networks/connection.png)

    テストは数分間実行されます。
2. 接続のテストが完了した後は、 **[状態]** タブと **[アクション]** タブに、以下の図に示すような結果が表示されます。

    ![[接続状態]](./media/diagnose-communication-problem-between-networks/connection-status.png)

    ![接続のアクション](./media/diagnose-communication-problem-between-networks/connection-action.png)

    VPN 診断では、 **[状態]** タブでどのような問題があるかが通知され、 **[アクション]** タブで、何が問題を引き起こしている可能性があるか、いくつかの候補が示されます。

    テストしたゲートウェイが、「[前提条件](#prerequisites)」の [スクリプト](../vpn-gateway/scripts/vpn-gateway-sample-site-to-site-powershell.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)によってデプロイされたものの場合、 **[状態]** タブの問題と、 **[アクション]** タブの最初の 2 つの項目が実際の問題です。 スクリプトは、オンプレミスの VPN ゲートウェイ デバイスのために、プレース ホルダー IP アドレス 23.99.221.164 を構成します。

    この問題を解決するには、オンプレミス VPN ゲートウェイが[正しく構成されている](../vpn-gateway/vpn-gateway-about-vpn-devices.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)ことを確認し、ローカル ネットワーク ゲートウェイのためにスクリプトによって構成された IP アドレスを、オンプレミス VPN ゲートウェイの実際のパブリック アドレスに変更する必要があります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルを完了するためだけに[前提条件](#prerequisites)のスクリプトを使用して VPN ゲートウェイを作成した場合は、それが不要になったら、リソース グループと、それに含まれるすべてのリソースを削除します。

1. ポータル上部の **[検索]** ボックスに「*TestRG1*」と入力します。 検索結果に **TestRG1** が表示されたら選択します。
2. **[リソース グループの削除]** を選択します。
3. **[TYPE THE RESOURCE GROUP NAME:]\(リソース グループ名を入力してください:)** に「*TestRG1*」と入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、仮想ネットワーク ゲートウェイに関する問題を診断する方法について学習しました。 異常がないかログを確認できるように、VM との間のネットワーク通信をログに記録することをお勧めします。 その方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [VM との間のネットワーク トラフィックのログを記録する](network-watcher-nsg-flow-logging-portal.md)
