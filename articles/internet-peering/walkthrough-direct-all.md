---
title: Direct ピアリングのチュートリアル
titleSuffix: Azure
description: Direct ピアリングのチュートリアル
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: 6a1fa7f582511a2089bab2694f9f5ec47634ff54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97592383"
---
# <a name="direct-peering-walkthrough"></a>Direct ピアリングのチュートリアル

このセクションでは、Direct ピアリングを設定および管理するために従う必要がある手順について説明します。

## <a name="create-a-direct-peering"></a>Direct ピアリングを作成する
> [!div class="mx-imgBorder"]
> ![Direct ピアリングのワークフローと接続の状態](./media/direct-peering.png)

Direct ピアリングをプロビジョニングするには、次の手順に従う必要があります。
1. Direct ピアリングの要件を理解するには、Microsoft [ピアリング ポリシー](https://peering.azurewebsites.net/peering)を確認してください。
1. [Direct ピアリングを作成または変更する](howto-direct-powershell.md)手順に従って、ピアリング要求を送信します。
1. ピアリング要求を送信した後、Microsoft は、登録されたメール アドレスを使用して LOA (委任状) または他の情報を提供します。
1. ピアリング要求が承認されると、接続状態は ProvisioningStarted に変わります。
1. 以下を実行する必要があります。
    1. LOA に従って接続を完了します
    1. (省略可能) 169.254.0.0/16 を使用してリンク テストを実行します
    1. BGP セッションを構成してから、Microsoft にお知らせください。
1. Microsoft では、"すべて拒否" ポリシーを使用して BGP セッションをプロビジョニングし、エンドツーエンドで検証します。
1. 成功すると、ピアリングの接続状態がアクティブであることを示す通知が表示されます。
1. その後、新しいピアリングによってトラフィックが許可されます。

接続状態は、標準の [BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol) セッション状態と混同しないよう注意してください。

## <a name="convert-a-legacy-direct-peering-to-azure-resource"></a>従来の Direct ピアリングを Azure リソースに変換する
従来の Direct ピアリングを Azure リソースに変換するには、次の手順に従う必要があります。
1. 「[従来の Direct ピアリングを Azure リソースに変換する](howto-legacy-direct-powershell.md)」の手順に従います
1. 変換要求を送信すると、Microsoft が要求を確認し、必要に応じてお客様に連絡します。
1. 承認されると、接続状態が "アクティブ" の Direct ピアリングが表示されます。

## <a name="deprovision-direct-peering"></a>Direct ピアリングをプロビジョニング解除する
Direct ピアリングをプロビジョニング解除するには、[Microsoft ピアリング](mailto:peering@microsoft.com) チームにお問い合わせください。

Direct ピアリングがプロビジョニング解除用に設定されている場合、接続状態は **PendingRemove** として表示されます

> [!NOTE]
> 接続状態が ProvisioningStarted または ProvisioningCompleted のときに、PowerShell コマンドレットを実行して Direct ピアリングを削除すると、操作は失敗します。

## <a name="next-steps"></a>次のステップ

* [Microsoft とのピアリングを設定するための前提条件](prerequisites.md)を確認します。
