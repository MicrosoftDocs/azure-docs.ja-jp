---
title: Exchange ピアリングのチュートリアル
titleSuffix: Azure
description: Exchange ピアリングのチュートリアル
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: 97430d86cba1107ef67264a181925d2a4d7c12c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97586824"
---
# <a name="exchange-peering-walkthrough"></a>Exchange ピアリングのチュートリアル

このセクションでは、Exchange ピアリングを設定および管理するために従う必要がある手順について説明します。

## <a name="create-an-exchange-peering"></a>Exchange ピアリングを作成する
> [!div class="mx-imgBorder"]
> ![Exchange ピアリングのワークフローと接続の状態](./media/exchange-peering.png)

Exchange ピアリングをプロビジョニングするには、次の手順に従う必要があります。
1. Exchange ピアリングの要件を理解するには、Microsoft [ピアリング ポリシー](https://peering.azurewebsites.net/peering)を確認してください。
1. [PeeringDB](https://www.peeringdb.com/net/694) で Microsoft ピアリングの場所とピアリング ファシリティ ID を検索する
1. 詳細については、[PowerShell を使用した Exchange ピアリングの作成および変更](howto-exchange-powershell.md)に関する記事の手順に従って、ピアリングの場所の Exchange ピアリングを要求してください。
1. ピアリング要求を送信すると、Microsoft が要求を確認し、必要に応じてお客様に連絡します。
1. 承認されると、接続状態が [承認済み] に変わる
1. ユーザー側で BGP セッションを構成して Microsoft に通知する
1. DENY ALL ポリシーを使用して BGP セッションをプロビジョニングし、エンドツーエンドで検証します。
1. 成功すると、ピアリングの接続状態がアクティブであることを示す通知が表示されます。
1. その後、新しいピアリングによってトラフィックが許可されます。

接続状態は、標準の [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) セッション状態と混同しないよう注意してください。

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>従来の Exchange ピアリングを Azure リソースに変換する
従来の Exchange ピアリングを Azure リソースに変換するには、次の手順に従う必要があります。
1. 「[従来の Exchange ピアリングを Azure リソースに変換する](howto-legacy-exchange-powershell.md)」の手順に従います
1. 変換要求を送信すると、Microsoft が要求を確認し、必要に応じてお客様に連絡します。
1. 承認されると、接続状態が [アクティブ] になっている Exchange ピアリングが表示されます。

## <a name="deprovision-exchange-peering"></a>Exchange ピアリングをプロビジョニング解除する
Exchange ピアリングをプロビジョニング解除するには、[Microsoft ピアリング](mailto:peering@microsoft.com)に問い合わせてください。

Exchange ピアリングがプロビジョニング解除用に設定されている場合、接続状態は **PendingRemove** として表示されます

> [!NOTE]
> 接続状態が ProvisioningStarted または ProvisioningCompleted のときに、PowerShell コマンドレットを実行して Exchange ピアリングを削除すると、操作は失敗します。

## <a name="next-steps"></a>次のステップ

* [Microsoft とのピアリングを設定するための前提条件](prerequisites.md)を確認します。
