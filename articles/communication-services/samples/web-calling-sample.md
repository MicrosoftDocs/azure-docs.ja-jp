---
title: Azure Communication Services - Web 通話サンプル
titleSuffix: An Azure Communication Services sample
description: Communication Services の Web 通話サンプルについて説明します。
author: chriswhilar
manager: mariusu-msft
services: azure-communication-services
ms.author: mariusu
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 824fd19e8acfed75ab3d64048a00f579b70286d2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103496237"
---
# <a name="get-started-with-the-web-calling-sample"></a>Web 通話サンプルの概要

Web 通話サンプルは、Communication Services の Web 通話クライアント ライブラリが提供するさまざまな機能の詳細なチュートリアルとして機能する Web アプリケーションです。

このサンプルは開発者向けに構築されたもので、これにより Communication Services を簡単に使い始めることができます。 そのユーザー インターフェイスは複数のセクションに分かれており、それぞれに [Show code]\(コードの表示\) ボタンが用意されています。これを使用すると、ブラウザーから自分の Communication Services アプリケーションに直接コードをコピーすることができます。

## <a name="get-started-with-the-web-calling-sample"></a>Web 通話サンプルの概要

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


> [!IMPORTANT]
> [このサンプルは GitHub で入手できます](https://github.com/Azure-Samples/communication-services-web-calling-tutorial/)。

/Project/readme.md に従ってプロジェクトを設定し、お使いのマシンでローカルに実行します。
[Web 通話サンプル](https://github.com/Azure-Samples/communication-services-web-calling-tutorial)をマシンで実行すると、次のランディング ページが表示されます。

:::image type="content" source="./media/web-calling-tutorial-page-1.png" alt-text="Web 通話のチュートリアル 1" lightbox="./media/web-calling-tutorial-page-1.png":::

:::image type="content" source="./media/web-calling-tutorial-page-2.png" alt-text="Web 通話のチュートリアル 2" lightbox="./media/web-calling-tutorial-page-2.png":::

## <a name="user-provisioning-and-sdk-initialization"></a>ユーザーのプロビジョニングと SDK の初期化

[Provisioning user and initialize SDK]\(ユーザーのプロビジョニングと SDK の初期化\) をクリックすると、バックエンドのトークン プロビジョニング サービスによってプロビジョニングされたトークンを使用して SDK が初期化されます。 このバックエンド サービスは `/project/webpack.config.js` にあります。

[Show code]\(コードの表示\) ボタンをクリックすると、独自のソリューションで使用できるサンプル コードが表示されます。

SDK が初期化されると、次のように表示されます。

:::image type="content" source="./media/user-provisioning.png" alt-text="ユーザー プロビジョニング" lightbox="./media/user-provisioning.png":::

これで、Communication Services リソースを使用して電話をかける準備ができました。

## <a name="placing-and-receiving-calls"></a>通話の発信と受信

Communication Services の Web 通話 SDK により、**1 対 1**、**1 対 N**、および **グループ** 通話が可能になります。

1 対 1 または 1 対 N の発信通話では、コンマ区切り値を使用して、通話先となる Communication Services のユーザー ID を複数指定できます。 また、コンマ区切り値を使用して、通話先となる従来の (PSTN) 電話番号を指定することもできます。

PSTN 電話番号に電話をかける際は、代替の発信者番号を指定してください。 発信通話を行うには、[Place call]\(通話\) ボタンをクリックします。

:::image type="content" source="./media/place-a-call.png" alt-text="通話を行う" lightbox="./media/place-a-call.png":::

グループ通話に参加するには、その通話を識別する GUID を入力し、[Join group]\(グループへの参加\) ボタンをクリックします。

:::image type="content" source="./media/join-a-group-call.png" alt-text="グループ通話に参加する" lightbox="./media/join-a-group-call.png":::

[Show code]\(コードの表示\) ボタンをクリックすると、通話の発信、通話の受信、およびグループ通話への参加のためのサンプル コードが表示されます。

実際の通話は次のようになります。

:::image type="content" source="./media/group-call.png" alt-text="グループ通話" lightbox="./media/group-call.png":::

このサンプルには、次の機能のためのコード スニペットも用意されています。

  - ビデオ アイコンをクリックして、ビデオ カメラのオンとオフを切り替える
  - マイク アイコンをクリックして、マイクのオンとオフを切り替える
  - 再生アイコンをクリックして、通話を保留または保留解除する
  - 画面アイコンをクリックして、画面の共有を開始または停止する
  - 人アイコンをクリックして、通話に参加者を追加する
  - 参加者名簿の [Remove participant]\(参加者を削除\) をクリックして、通話から特定の参加者を削除する


## <a name="next-steps"></a>次の手順

>[!div class="nextstepaction"]
>[GitHub からサンプルをダウンロードする](https://github.com/Azure-Samples/communication-services-web-calling-tutorial/)

詳細については、次の記事を参照してください。

- [Calling クライアント ライブラリ](../quickstarts/voice-video-calling/calling-client-samples.md)について理解する
- [通話のしくみ](../concepts/voice-video-calling/about-call-types.md)の詳細について確認する
- [API リファレンス ドキュメント](/javascript/api/azure-communication-services/@azure/communication-calling/)を確認する
- [Contoso Med App](https://github.com/Azure-Samples/communication-services-contoso-med-app) サンプルを確認する

## <a name="additional-reading"></a>その他の情報

- [サンプル](./overview.md) - その他のサンプルと例については、「サンプルの概要」ページを参照してください。
- [Redux](https://redux.js.org/) - クライアント側の状態管理
- [FluentUI](https://aka.ms/fluent-ui) - Microsoft が提供する UI ライブラリ
- [React](https://reactjs.org/) - ユーザー インターフェイスを構築するためのライブラリ
- [ASP.NET Core](/aspnet/core/introduction-to-aspnet-core?preserve-view=true&view=aspnetcore-3.1) - Web アプリケーションを構築するためのフレームワーク
