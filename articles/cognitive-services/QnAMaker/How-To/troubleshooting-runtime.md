---
title: トラブルシューティング - QnAMaker
titleSuffix: Azure Cognitive Services
description: QnA Maker は、ユーザーの Azure アカウントでホストされるコンポーネントで構成されます。 デバッグを行うには、ユーザーは QnA Maker Azure リソースを操作するか、QnA Maker サポート チームに設定に関する追加情報を提供する必要がある場合があります。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 08/20/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b58a08c4cfa97356be4064456b2e9ec5cceccfbc
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876337"
---
# <a name="troubleshooting-tips-to-support-the-qna-maker-service-and-runtime"></a>QnA Maker のサービスとランタイムをサポートするためのトラブルシューティングのヒント

QnA Maker は、ユーザーの Azure サブスクリプションでホストされるリソースで構成されます。 デバッグを行うには、ユーザーは Azure QnA Maker リソースを操作するか、QnA Maker サポート チームに設定に関する追加情報を提供する必要がある場合があります。

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>QnA Maker ランタイムの最新の更新プログラムを取得する方法

QnA Maker ランタイムは、Azure portal で [QnA Maker サービスを作成する](./set-up-qnamaker-service-azure.md)ときにデプロイされる Azure App Service の一部です。 ランタイムの更新は定期的に行われます。 QnA Maker App Service は、2019 年 4 月のサイト拡張リリース (バージョン 5+) 以降は自動更新モードになります。 これは、アップグレード中のダウンタイムがゼロになるように既に設計されています。 

[https://www.qnamaker.ai/UserSettings](https://www.qnamaker.ai/UserSettings ) で現在のバージョンを確認できます。 ご使用のバージョンがバージョン 5.x よりも古い場合、最新の更新プログラムを適用するために、App Service を再起動する必要があります。

1. [Azure portal](https://portal.azure.com) で QnA Maker サービス (リソース グループ) に移動します。

    ![QnA Maker Azure リソース グループ](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. App Service をクリックして、[概要] セクションを開きます。

     ![QnA Maker App Service](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. App Service を再起動します。 数秒以内に完了します。 この再起動中に、エンド ユーザーはこの QnA Maker サービスを使用する依存アプリケーションやボットを使用できないことに注意してください。

    ![QnA Maker App Service の再起動](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>QnA Maker サービスのホスト名を取得する方法
QnA Maker サービスのホスト名は、デバッグ目的で QnA Maker サポートや UserVoice に問い合わせる場合に役立ちます。 ホスト名は、 https:// *{ホスト名}* .azurewebsites.net という形式の URL です。
    
1. [Azure portal](https://portal.azure.com) で QnA Maker サービス (リソース グループ) に移動します。

    ![QnAMaker の Azure リソース グループ (Azure portal)](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. QnA Maker リソースに関連付けられている App Service を選択します。 通常、名前は同じです。

     ![QnAMaker の App Service を選択する](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. ホスト名 URL は [概要] セクションで確認できます。

    ![QnA Maker のホスト名](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [アクティブ ラーニングを使用してナレッジ ベースを改善する](./improve-knowledge-base.md)
