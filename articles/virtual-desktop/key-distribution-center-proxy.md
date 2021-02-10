---
title: Kerberos キー配布センター プロキシ Windows Virtual Desktop のセットアップ - Azure
description: Kerberos キー配布センター プロキシを使用するように Windows Virtual Desktop ホスト プールを設定する方法について説明します。
author: Heidilohr
ms.topic: how-to
ms.date: 01/30/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 102ddc1c8937c66a92416ddb6d5f2d25f2a3c349
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2021
ms.locfileid: "99219657"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy-preview"></a>Kerberos キー配布センター プロキシを構成する (プレビュー)

> [!IMPORTANT]
> 現在、この機能はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されており、運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

この記事では、ホスト プールに対して Kerberos キー配布センター (KDC) プロキシ (プレビュー) を構成する方法について説明します。 このプロキシにより、組織は企業の境界外で Kerberos を使用して認証を行うことができます。 たとえば、KDC プロキシを使用して、外部クライアントのスマートカード認証を有効にすることができます。

## <a name="how-to-configure-the-kdc-proxy"></a>KDC プロキシを構成する方法

KDC プロキシを構成するには、次の手順を実行します。

1. Azure Portal に管理者としてサインインします。

2. [Windows Virtual Desktop] ページにアクセスします。

3. KDC プロキシを有効にするホスト プールを選択し、 **[RDP プロパティ]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![[Azure portal] ページのスクリーンショット。ホスト プールを選択したユーザー、ホスト プールの例の名前、RDP プロパティの順に表示されます。](media/rdp-properties.png)

4. **[詳細設定]** タブを選択し、次の形式でスペースなしで値を入力します。

    > kdcproxyname:s:\<fqdn\>

    > [!div class="mx-imgBorder"]
    > ![選択した [詳細設定] タブが表示されたスクリーンショット。手順 4 で説明したように値を入力します。](media/advanced-tab-selected.png)

5. **[保存]** を選択します。

6. これで、選択したホスト プールで、入力した kdcproxyname フィールドを含む RDP 接続ファイルの発行が開始されます。

## <a name="next-steps"></a>次のステップ

リモート デスクトップ サービスの RDGateway ロールには、KDC プロキシ サービスが含まれています。 Windows Virtual Desktop のターゲットに設定する方法については、「[Windows Virtual Desktop で RD ゲートウェイのロールをデプロイする](rd-gateway-role.md)」を参照してください。
