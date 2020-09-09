---
title: ルール エンジンを使用してセキュリティ ヘッダーを追加する - Azure Front Door
description: この記事では、Azure Front Door でルール エンジンを使用し、セキュリティ ヘッダーを構成する方法について説明します
services: frontdoor
documentationcenter: ''
author: megan-beatty
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 6/22/2020
ms.author: mebeatty
ms.openlocfilehash: 5f07a57e0d41a5e7c546bc89ca7a0b5ac5fce5de
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86512233"
---
# <a name="add-security-headers-with-rules-engine"></a>ルール エンジンを使用してセキュリティ ヘッダーを追加する

HTTP Strict-Transport-Security (HSTS)、X-XSS-Protection、Content-Security-Policy、X-Frame-Options など、ブラウザーベースの脆弱性を防ぐためにセキュリティ ヘッダーを実装します。 セキュリティベースの属性は、Cookie でも定義できます。

次の例は、ルール エンジン構成が関連付けられているルートに定義されているパスに一致するあらゆる受信要求に Content-Security-Policy ヘッダーを追加する方法を示しています。 ここでは、信頼できるサイト **https://apiphany.portal.azure-api.net** からのスクリプトにのみ、アプリケーション上での実行を許可します。

## <a name="add-a-content-security-policy-header-in-azure-portal"></a>Azure portal で Content-Security-Policy ヘッダーを追加する

1. AFD またはルール エンジン機能を使用するのが初めてであれば、この特定のルールを作成する前に、[フロント ドアの作成](quickstart-create-front-door.md)方法や[ルール エンジンの作成](front-door-tutorial-rules-engine.md)方法を確認してください。

2. **[追加]** をクリックして新しいルールを追加します。 ルールに名前を付け、 **[アクションの追加]** 、 **[応答ヘッダー]** の順にクリックします。

3. [演算子] を **[追加]** に設定し、このルートへのすべての受信要求に応答としてこのヘッダーを追加します。

4. ヘッダー名として「**Content-Security-Policy**」を追加し、このヘッダーで受け入れる値を定義します。 このシナリオでは、 *"script-src 'self' https://apiphany.portal.azure-api.net"* を選択します。

5. 構成に追加するルールをすべて追加したら、必ず優先ルートに移動し、ルール エンジン構成をルート ルールに関連付けてください。 この手順は、ルールを機能させるために必須です。 

![ポータル サンプル](./media/front-door-rules-engine/rules-engine-security-header-example.png)

> [!NOTE]
> このシナリオでは、ルールに[一致条件](front-door-rules-engine-match-conditions.md)を追加しませんでした。 ルート ルールに定義されているパスに一致する受信要求にはすべて、このルールが適用されます。 これらの要求の一部にのみ適用する場合、必ず特定の一致条件をこのルールに追加してください。


## <a name="next-steps"></a>次のステップ

- [AFD ルール エンジン](front-door-rules-engine.md)の詳細を確認します。 
- [フロント ドアの作成](quickstart-create-front-door.md)方法について学習します。
- [Front Door のしくみ](front-door-routing-architecture.md)について学習します。
- ルール エンジンの[一致条件](front-door-rules-engine-match-conditions.md)の詳細を確認します
- AFD ルール エンジンの [CLI リファレンス](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door/rules-engine?view=azure-cli-latest)で詳細を確認します。 
- AFD ルール エンジンの [PowerShell リファレンス](https://docs.microsoft.com/powershell/module/az.frontdoor/?view=azps-3.8.0)で詳細を確認します。 
