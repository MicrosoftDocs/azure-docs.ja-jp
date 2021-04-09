---
title: Windows Virtual Desktop (クラシック) のサービス アラートを設定する - Azure
description: Windows Virtual Desktop (クラシック) のサービス通知を受信する Azure Service Health の設定方法。
author: Heidilohr
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 01f4f52acaa5b461e2b058f112f26dafda86dcb7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95025029"
---
# <a name="tutorial-set-up-service-alerts-for-windows-virtual-desktop-classic"></a>チュートリアル:Windows Virtual Desktop (クラシック) のサービス アラートを設定する

>[!IMPORTANT]
>これは、Azure Resource Manager Windows Virtual Desktop オブジェクトをサポートしない Windows Virtual Desktop (クラシック) を対象としたコンテンツです。 Azure Resource Manager Windows Virtual Desktop オブジェクトを管理しようとしている場合は、[こちらの記事](../set-up-service-alerts.md)を参照してください。

Azure Service Health を使用すると、Windows Virtual Desktop のサービスの問題と正常性に関するアドバイザリを監視できます。 Azure Service Health によって、さまざまな種類のアラート (メールや SMS など) の通知を受け取り、問題の影響を把握し、問題が解決したときに最新の情報を得ることができます。 さらに、Azure Service Health は、ダウンタイムを軽減し、お使いのリソースの可用性に影響を与える可能性がある計画的なメンテナンスや変更に備えるためにも役立ちます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * サービス アラートを作成および構成する。

Azure Service Health の詳細については、[Azure Health のドキュメント](../../service-health/index.yml)を参照してください。

## <a name="prerequisites"></a>前提条件

- [チュートリアル:Windows Virtual Desktop でテナントを作成する](tenant-setup-azure-active-directory.md)
- [チュートリアル:PowerShell を使用してサービス プリンシパルとロールの割り当てを作成する](create-service-principal-role-powershell.md)
- [チュートリアル:](create-host-pools-azure-marketplace-2019.md)Azure Marketplace を使用してホスト プールを作成する

## <a name="create-service-alerts"></a>サービス アラートを作成する

このセクションでは、Azure Service Health を構成する方法と、Azure portal でアクセスできる通知を設定する方法について説明します。 さまざまな種類のアラートを設定し、それらを適切なタイミングで通知するようにスケジュールすることができます。

### <a name="recommended-service-alerts"></a>推奨されるサービス アラート

次の正常性イベントの種類に対してサービス アラートを作成することをお勧めします。

- **サービスの問題:** ユーザーとサービス、または Windows Virtual Desktop テナントを管理する機能との接続に影響を与える主な問題に関する通知を受け取ります。
- **正常性に関するアドバイザリ:** 注意が必要な通知を受け取ります。 この種の通知の例を次に示します。
    - オープン ポート 3389 として安全に構成されていない仮想マシン (VM)
    - 機能の廃止

### <a name="configure-service-alerts"></a>サービス アラートを構成する

サービス アラートを構成するには:

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. **[Service Health]** を選択します。
3. 「[Create activity log alerts on service notifications (サービス通知に関するアクティビティ ログ アラートを作成する)](../../service-health/alerts-activity-log-service-notifications-portal.md?toc=%2fazure%2fservice-health%2ftoc.json)」の手順を使用してアラートと通知を設定します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Service Health を設定および使用して Windows Virtual Desktop のサービスの問題と正常性に関するアドバイザリを監視する方法について学習しました。 Windows Virtual Desktop にサインインする方法について詳しくは、引き続き Windows Virtual Desktop への接続方法に関するページを参照してください。

> [!div class="nextstepaction"]
> [Windows 7 および Windows 10 上でリモート デスクトップ クライアントに接続する](connect-windows-7-10-2019.md)