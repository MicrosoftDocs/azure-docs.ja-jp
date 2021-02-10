---
title: Azure Communication Services - Web 通話サンプル
titleSuffix: An Azure Communication Services sample
description: Communication Services の Web 通話サンプルについて説明します。
author: chriswhilar
manager: mariusu-msft
services: azure-communication-services
ms.author: mariusu
ms.date: 10/15/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: a00f319c55665695879aac3c8ba711ce13fd5d4e
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763047"
---
# <a name="get-started-with-the-web-calling-sample"></a>Web 通話サンプルの概要

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

> [!IMPORTANT]
> [このサンプルは GitHub で入手できます](https://github.com/Azure-Samples/communication-services-web-calling-tutorial/)。

Azure Communication Services の **Web 通話サンプル** では、Communication Services の通話クライアント ライブラリを使用して、JavaScript で通話エクスペリエンスを構築する方法を示します。

このサンプルのクイックスタートでは、サンプルをローカル コンピューターで実行する前に、サンプルがどのように動作するかを学習します。 次に、独自の Azure Communication Services リソースを使用して、サンプルを Azure にデプロイします。

## <a name="overview"></a>概要

Web 通話サンプルは、Communication Services の Web 通話クライアント ライブラリが提供するさまざまな機能の詳細なチュートリアルとして機能する Web アプリケーションです。 

このサンプルは開発者向けに構築されたもので、これにより Communication Services を簡単に使い始めることができます。 そのユーザー インターフェイスは複数のセクションに分かれており、それぞれに [Show code]\(コードの表示\) ボタンが用意されています。これを使用すると、ブラウザーから自分の Communication Services アプリケーションに直接コードをコピーすることができます。

[Web 通話サンプル](https://github.com/Azure-Samples/communication-services-web-calling-tutorial)をマシンで実行していると、次のランディング ページが表示されます。

:::image type="content" source="./media/web-calling-tutorial-page-1.png" alt-text="Web 通話のチュートリアル 1" lightbox="./media/web-calling-tutorial-page-1.png":::

:::image type="content" source="./media/web-calling-tutorial-page-2.png" alt-text="Web 通話のチュートリアル 2" lightbox="./media/web-calling-tutorial-page-2.png":::


## <a name="user-provisioning-and-sdk-initialization"></a>ユーザーのプロビジョニングと SDK の初期化 

このデモの使用を開始するには、お使いの [Communication Services リソース](../quickstarts/create-communication-resource.md)の接続文字列を `config.json` に入力します。 これは、通話 SDK を初期化できるように[ユーザー アクセス トークン](../concepts/authentication.md)をプロビジョニングするために使用されます。

ユーザー ID の入力には自分の個人識別子を入力します。 ここに何も指定しないと、ランダムなユーザー ID が生成されます。 

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
- [API リファレンス ドキュメント](/javascript/api/azure-communication-services/@azure/communication-calling/?view=azure-communication-services-js)を確認する
- [Contoso Med App](https://github.com/Azure-Samples/communication-services-contoso-med-app) サンプルを確認する

## <a name="additional-reading"></a>その他の情報

- [Azure Communication GitHub](https://github.com/Azure/communication) - 他の例や情報は公式 GitHub ページで見つかります。
- [Redux](https://redux.js.org/) - クライアント側の状態管理
- [FluentUI](https://aka.ms/fluent-ui) - Microsoft が提供する UI ライブラリ
- [React](https://reactjs.org/) - ユーザー インターフェイスを構築するためのライブラリ
- [ASP.NET Core](/aspnet/core/introduction-to-aspnet-core?preserve-view=true&view=aspnetcore-3.1) - Web アプリケーションを構築するためのフレームワーク