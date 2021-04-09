---
title: Web ページでのリアル ユーザー測定 - Azure Traffic Manager
description: この記事では、Azure Traffic Manager にリアル ユーザー測定を送信するように Web ページを設定する方法について説明します。
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: duau
ms.custom: ''
ms.openlocfilehash: 2bb104fd9cfc3c4ffddb82e4cf442e94c4650550
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98184560"
---
# <a name="how-to-send-real-user-measurements-to-azure-traffic-manager-using-web-pages"></a>Web ページを使用して Azure Traffic Manager に Real User Measurements を送信する方法

Real User Measurements (RUM) キーを取得し、生成されたコードを Web ページに埋め込むことで、Real User Measurements を Traffic Manager に送信するように Web ページを構成することができます。

## <a name="obtain-a-real-user-measurements-key"></a>Real User Measurements キーの取得

測定値を取得し、クライアント アプリケーションから Traffic Manager に送信すると、その測定値は、**Real User Measurements (RUM) キー** と呼ばれる一意の文字列を使用して、サービスによって識別されます。 RUM キーを取得するには、Azure Portal、REST API、PowerShell、または Azure CLI を使用します。

Azure Portal を使用して RUM キーを取得するには:
1. ブラウザーから Azure Portal にサインインします。 まだアカウントを持っていない場合は、1 か月間の無料試用版にサインアップできます。
2. ポータルの検索バーで、変更の対象となる Traffic Manager プロファイル名を検索し、表示された結果内で Traffic Manager プロファイルをクリックします。
3. Traffic Manager プロファイル ブレードで、**[設定]** の下の **[Real User Measurements]\(Real User Measurements\)** をクリックします。
4. **[キーの生成]** をクリックして、新しい RUM キーを作成します。
 
   ![Real User Measurements キーの生成](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **図 1: Real User Measurements キーの生成**

5. ブレードには、生成された RUM キーと、HTML ページに埋め込む必要がある JavaScript コード スニペットが表示されます。
 
    ![Real User Measurements キーの JavaScript コード](./media/traffic-manager-create-rum-web-pages/rum-javascript-code.png)

    **図 2: Real User Measurements キーと測定 JavaScript**
 
6. **[コピー]** をクリックして、JavaScript コードをコピーします。 

>[!IMPORTANT]
> 正常に機能させるには、Real User Measurements 機能用に生成された JavaScript を使用します。 このスクリプトまたは Real User Measurements で使用されるスクリプトを変更すると、予期しないビヘイビアーにつながることがあります。

## <a name="embed-the-code-to-an-html-web-page"></a>HTML Web ページへのコードの埋め込み

RUM キーを取得した後、次の手順は、エンドユーザーがアクセスする HTML ページにこのコピーされた JavaScript を埋め込むことです。 HTML の編集は、多くの方法で行うことができ、さまざまなツールとワークフローを使用できます。 この例では、HTML ページを更新してこのスクリプトを追加する方法を示します。 このガイドを使用し、HTML ソース管理ワークフローに適応できます。

1.  テキスト エディターで HTML ページを開きます
2.  HTML の BODY セクションに、前の手順でコピーしておいた JavaScript コードを貼り付けます (コピーしたコードは、8 行目と 9 行目にあります。図 3 をご覧ください)。
 
    ![Real User Measurements の Web ページへの JavaScript コードの埋め込み](./media/traffic-manager-create-rum-web-pages/real-user-measurement-embed-script.png)  

    **図 3: Real User Measurements JavaScript が埋め込まれた単純な HTML**

3.  HTML ファイルを保存して、インターネットに接続されている Web サーバーでホストします。 
4. Web ブラウザーでこのページを次に表示したときに、参照される JavaScript がダウンロードされ、スクリプトは測定とレポートの操作を実行します。


## <a name="next-steps"></a>次のステップ
- [Real User Measurements](traffic-manager-rum-overview.md) について確認する
- [Traffic Manager のしくみ](traffic-manager-overview.md)
- Traffic Manager でサポートされている [トラフィック ルーティング方法](traffic-manager-routing-methods.md) の詳細を確認する。
- [Traffic Manager プロファイルの作成](./quickstart-create-traffic-manager-profile.md)