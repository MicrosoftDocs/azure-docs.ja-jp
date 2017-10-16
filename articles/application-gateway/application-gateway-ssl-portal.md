---
title: "SSL オフロードの構成 - Azure Application Gateway - Azure Portal | Microsoft Docs"
description: "この記事では、Azure ポータルを使用して、SSL オフロード用のアプリケーション ゲートウェイを作成する方法について説明します"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 8373379a-a26a-45d2-aa62-dd282298eff3
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: davidmu
ms.openlocfilehash: 2f7f5d4132e28c8c192d90d5f4bfb2a9034f8b8c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-azure-portal"></a>Azure ポータルを使用して SSL オフロード用のアプリケーション ゲートウェイを構成する

> [!div class="op_single_selector"]
> * [Azure ポータル](application-gateway-ssl-portal.md)
> * [Azure Resource Manager の PowerShell](application-gateway-ssl-arm.md)
> * [Azure クラシック PowerShell](application-gateway-ssl.md)
> * [Azure CLI 2.0](application-gateway-ssl-cli.md)

Azure Application Gateway をゲートウェイでの Secure Sockets Layer (SSL) セッションを停止するように構成し、Web ファーム上で発生するコストのかかる SSL 暗号化解除タスクを回避することができます。 また、SSL オフロードはフロントエンド サーバーのセットアップと Web アプリケーションの管理も簡素化します。

## <a name="scenario"></a>シナリオ

次のシナリオでは、既存のアプリケーション ゲートウェイに、SSL オフロードを構成します。 このシナリオでは、[アプリケーション ゲートウェイを作成する](application-gateway-create-gateway-portal.md)ための手順を既に実行したことを前提としています。

## <a name="before-you-begin"></a>開始する前に

アプリケーション ゲートウェイで SSL オフロードを構成するには、証明書が必要です。 この証明書は、アプリケーション ゲートウェイに読み込まれ、SSL 経由で送信されるトラフィックの暗号化と暗号化解除に使用されます。 証明書は、Personal Information Exchange (.pfx) 形式である必要があります。 このファイル形式により、アプリケーション ゲートウェイがトラフィックの暗号化および暗号化解除を実行する際に必要な秘密キーをエクスポートできます。

## <a name="add-an-https-listener"></a>HTTPS リスナーの追加

HTTPS リスナーは、構成に基づいてトラフィックを検出し、バックエンド プールへのトラフィックのルーティングをサポートします。 HTTPS リスナーを追加するには、次の手順を実行します。

   1. Azure ポータルに移動し、既存のアプリケーション ゲートウェイを選択します。

   2. **[リスナー]** を選択し、**[追加]** ボタンをクリックしてリスナーを追加します。

   ![Application Gateway の概要ウィンドウ][1]


   3. リスナーに必要な次の情報を入力し、.pfx 証明書をアップロードします。
      - **名前**: リスナーのフレンドリ名。

      - **フロントエンド IP 構成**: リスナーで使用されるフロントエンド IP 構成。

      - **フロントエンド ポート (名前/ポート)**: アプリケーション ゲートウェイのフロントエンドで使用されるポートのフレンドリ名と、実際に使用されるポート。

      - **プロトコル**: フロントエンドに https と http のどちらを使用するかを決定するスイッチ。

      - **証明書 (名前/パスワード)**: SSL オフロードを使用する場合、この設定には .pfx 証明書が必要です。 フレンドリ名とパスワードも必要です。

   4. **[OK]**を選択します。

![リスナー追加ウィンドウ][2]

## <a name="create-a-rule-and-associate-it-to-the-listener"></a>ルールの作成とリスナーへの関連付け

ここまでで、リスナーが作成されました。 次に、リスナーからのトラフィックを処理するルールを作成します。 ルールは、複数の構成設定に基づいてバックエンド プールにトラフィックをルーティングする方法を定義します。 これらの設定には、プロトコル、ポート、正常性プローブ、および cookie ベースのセッション アフィニティの使用の有無が含まれます。 ルールを作成してリスナーに関連付けるには、次の手順に従います。


   1. アプリケーション ゲートウェイの **[ルール]** をクリックし、**[追加]** を選択します。

   ![アプリケーション ゲートウェイの [ルール] ウィンドウ][3]


   2. **[基本規則の追加]**で、**[名前]** フィールドにルールのフレンドリ名を入力し、前の手順で作成した**リスナー**を選択します。 適切な**バックエンド プール**と **HTTP 設定**を選択し、**[OK]** を選択します。

   ![HTTPS 設定ウィンドウ][4]

これで、設定がアプリケーション ゲートウェイに保存されます。 これらの設定の保存処理には時間がかかる場合があります。この処理が完了すると、これらの設定はポータルまたは PowerShell で表示できるようになます。 保存後は、アプリケーション ゲートウェイがトラフィックの暗号化と暗号化解除を処理します。 アプリケーション ゲートウェイとバックエンド Web サーバーの間のすべてのトラフィックは HTTP 経由で処理されます。 クライアントに対する通信は、HTTPS 経由で開始された場合、暗号化されてクライアントに返されます。

## <a name="next-steps"></a>次のステップ

Azure Application Gateway でカスタムの正常性プローブを構成する方法について学習するには、 [カスタムの正常性プローブの作成](application-gateway-create-gateway-portal.md)に関するページを参照してください。

[1]: ./media/application-gateway-ssl-portal/figure1.png
[2]: ./media/application-gateway-ssl-portal/figure2.png
[3]: ./media/application-gateway-ssl-portal/figure3.png
[4]: ./media/application-gateway-ssl-portal/figure4.png

