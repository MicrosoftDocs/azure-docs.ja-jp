---
title: "アプリケーション プロキシ アプリケーション用として動作するコネクタ グループが見つからない | Microsoft Docs"
description: "Azure AD アプリケーション プロキシを使用したアプリケーション用のコネクタ グループ内に動作するコネクタがない場合に発生する可能性のある問題に対処します"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 4945958deedc8a1d9989ff901192c03a5363b4dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="no-working-connector-group-found-for-an-application-proxy-application"></a>アプリケーション プロキシ アプリケーション用として動作するコネクタ グループが見つからない

この記事では、Azure Active Directory に統合されたアプリケーション プロキシ アプリケーションに対してコネクタが検出されない場合に発生する一般的な問題の解決方法を説明します。

## <a name="overview-of-steps"></a>手順の概要
アプリケーション用のコネクタ グループ内に動作するコネクタがない場合、以下に示すいくつかの方法で問題を解決できます。

-   グループ内にコネクタがない場合は、以下のように操作します。

    -   適切なオンプレミスのサーバーに新しいコネクタをダウンロードし、このグループに割り当てる

    -   アクティブなコネクタをグループ内に移動する

-   グループ内にアクティブなコネクタがない場合は、以下のように操作します。

    -   コネクタがアクティブになっていない理由を特定し、それを解決する

    -   アクティブなコネクタをグループ内に移動する

どちらの問題が該当するかを調べるには、アプリケーションの [アプリケーション プロキシ] メニューを開き、[コネクタ グループ] の警告メッセージを確認します。 このメッセージには、グループに少なくとも 1 つのコネクタが必要である (グループ内にコネクタが 1 つもない) ことか、またはアクティブなコネクタがない (ただし、おそらくアクティブではないコネクタがある) ことが示されます。

   ![Azure Portal でのコネクタ グループの選択](./media/application-proxy-connectivity-no-working-connector/no-active-connector.png)

これらの各オプションの詳細については、以下の対応するセクションを参照してください。 各セクションでは、コネクタ管理ページから操作を開始していることを前提としています。 上記のエラー メッセージが表示される場合は、警告メッセージをクリックすることで、このページにアクセスできます。 それ以外には、**Azure Active Directory** にアクセスし、**[エンタープライズ アプリケーション]**、**[アプリケーション プロキシ]** の順にクリックすることで、このページを確認できます。

   ![Azure Portal でのコネクタ グループの管理](./media/application-proxy-connectivity-no-working-connector/app-proxy.png)

## <a name="download-a-new-connector"></a>新しいコネクタをダウンロードする

新しいコネクタをダウンロードするには、ページの上部にある [コネクタのダウンロード] ボタンを使用します。

コネクタは、バックエンド アプリケーションに直接アクセスできるコンピューターにインストールする必要があり、通常はアプリケーションと同じサーバーに配置されます。 ダウンロード後、コネクタがこのメニュー内に表示されます。 コネクタをクリックし、[コネクタ グループ] ドロップダウンを使用して、そのコネクタが正しいグループに属すようにします。 変更を保存します。

   ![Azure Portal からコネクタをダウンロードする](./media/application-proxy-connectivity-no-working-connector/download-connector.png)
   
## <a name="move-an-active-connector"></a>アクティブなコネクタを移動する

グループに属し、ターゲットのバックエンド アプリケーションにアクセスできるアクティブなコネクタがある場合は、割り当てられているグループにそのコネクタを移動できます。 移動するには、コネクタをクリックします。 [コネクタ グループ] フィールドで、ドロップダウンを使用して正しいグループを選択し、[保存] をクリックします。

## <a name="resolve-an-inactive-connector"></a>アクティブではないコネクタを解決する

グループ内にアクティブではないコネクタしか存在しない場合、それらのコネクタは、必要なすべてのポートのブロック解除が行われていないコンピューター上に配置されている可能性があります。

この問題の調査方法の詳細については、ポートのトラブルシューティングに関するドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ
[Azure AD アプリケーション プロキシ コネクタについて](application-proxy-understand-connectors.md)


