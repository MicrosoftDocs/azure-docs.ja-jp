---
title: Azure Communication Services の UI フレームワークの概要
titleSuffix: An Azure Communication Services conceptual document
description: Azure Communication Services の UI フレームワークについて説明します
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 47a32815ded5809edfde856a38c69ec7c6fd6fdf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493358"
---
# <a name="azure-communication-services-ui-framework"></a>Azure Communication Services の UI フレームワーク

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

:::image type="content" source="../media/ui-framework/pre-and-custom-composite.png" alt-text="あらかじめ構築された複合とカスタム複合の比較":::

Azure Communication Services UI フレームワークを使用すると、最新のコミュニケーション用ユーザー エクスペリエンスを簡単に構築できます。 運用環境ですぐに使用できる UI コンポーネントのライブラリが用意されており、アプリケーションに手軽にドロップできます。

- **複合コンポーネント** - これらのコンポーネントは、一般的な通信シナリオを実装するためのターンキー ソリューションです。 アプリケーションにビデオ通話やチャットのエクスペリエンスをすばやく追加できます。 複合とは、基本コンポーネントを使用して構築された、オープンソースのコンポーネントです。
- **基本コンポーネント** - これらのコンポーネントは、カスタム通信エクスペリエンスを構築できる、オープンソースの構成要素です。 コンポーネントは、エクスペリエンスの構成のために組み合わせることのできる通話とチャットの機能の両方を対象に提供されます。 

これらの UI クライアント ライブラリはすべて、[Microsoft の Fluent 設計言語](https://developer.microsoft.com/fluentui/)と資産を使用しています。 Fluent UI とは、Microsoft 製品全体にわたってテストされている UI フレームワークの基本レイヤーです。

## <a name="differentiating-components-and-composites"></a>**コンポーネントと複合の区別**

**基本コンポーネント** は、Azure Communication Services のコア クライアント ライブラリをベースに構築されたものであり、コア クライアント ライブラリの初期化、ビデオのレンダリング、ミュートやビデオのオンまたはオフのユーザー コントロールなどの基本的なアクションを実装します。これらの **基本コンポーネント** を使用することで、運用環境ですぐに使用可能な、あらかじめ構築された通信コンポーネントを使った、独自のカスタム レイアウト エクスペリエンスを構築できます。

:::image type="content" source="../media/ui-framework/component-overview.png" alt-text="UI フレームワークのコンポーネントの概要":::

**複合コンポーネント** は、複数の **基本コンポーネント** を組み合わせることで、通信エクスペリエンスの完成度を高めるものです。 これらの上位レベルのコンポーネントを既存のアプリに簡単に統合できるので、ゼロから構築しなくても十分完成した通信エクスペリエンスをドロップできます。 開発者は、通信の複雑な部分については複合コンポーネントに任せて、アプリに組み込む周辺のエクスペリエンスやフローの構築に専念することができます。

:::image type="content" source="../media/ui-framework/composite-overview.png" alt-text="UI フレームワークの複合の概要":::

## <a name="what-ui-framework-is-best-for-my-project"></a>自分のプロジェクトに最適な UI フレームワークとは

次の要件を把握することで、適切なクライアント ライブラリを選択するのに役立ちます。

- **どの程度のカスタマイズが必要か。** Azure Communication コア クライアント ライブラリには UX が存在しておらず、任意の UX を構築できるように設計されています。 UI フレームワークのコンポーネントでは、カスタマイズの自由度は低くなりますが、UI 資産が提供されます。
- **会議機能は必要か。** 会議システムには、現在 Azure Communication Services のコア クライアント ライブラリには用意されていない独自機能がいくつかあります。たとえば、背景のぼかしや挙手などです。
- **対象とするプラットフォームは何か。** プラットフォームによって、機能は異なります。

さまざまな [UI SDK で利用できる機能の詳細についてはこちら](ui-sdk-features.md)をご覧ください。ただし、主なトレードオフについては以下にまとめます。

|クライアント ライブラリまたは SDK|実装の複雑さ|    カスタマイズ性|  呼び出し| チャット| [Teams との相互運用](./../teams-interop.md)
|---|---|---|---|---|---|---|
|複合コンポーネント|低|低|✔|✔|✕
|基本コンポーネント|Medium|Medium|✔|✔|✕
|コア クライアント ライブラリ|高|高|✔|✔ |✔

## <a name="cost"></a>コスト

Azure UI フレームワークを使用する際、Azure の費用またはメータリングが余分に生じることはありません。 同じ通話、チャット、PSTN のメーターを使用して、基になるサービスの使用量に対してのみ課金されます。

## <a name="supported-use-cases"></a>サポートされているユース ケース

通話:

- グループ ID を使用して Azure Communication Services の通話に参加する

チャット:

- スレッド ID を使用して Azure Communication Services のチャットに参加する

## <a name="supported-identities"></a>サポートされている ID:

UI フレームワークを初期化し、サービスに対して認証を行うには、Azure Communication Services ID が必要です。 認証の詳細については、[認証](../authentication.md)と[アクセス トークン](../../quickstarts/access-tokens.md)に関するページを参照してください。


## <a name="recommended-architecture"></a>推奨アーキテクチャ 

:::image type="content" source="../media/ui-framework/framework-architecture.png" alt-text="クライアント サーバー アーキテクチャを使用した、UI フレームワークの推奨アーキテクチャ":::

複合コンポーネントと基本コンポーネントは、Azure Communication Services のアクセス トークンを使用して初期化されます。 アクセス トークンは、管理対象の信頼できるサービスを介して Azure Communication Services から調達する必要があります。 「[クイック スタート:詳細については、アクセストークンの作成に関するクイックスタート](../../quickstarts/access-tokens.md)と[信頼できるサービスのチュートリアル](../../tutorials/trusted-service-tutorial.md)に関するページを参照してください。

これらのクライアント ライブラリでは、参加する通話またはチャットのコンテキストも必要になります。 ユーザー アクセス トークンと同様、このコンテキストも信頼できる独自サービスを介してクライアントに配布する必要があります。 次の一覧は、運用化する必要がある初期化機能とリソース管理機能をまとめたものです。

| Contoso の役割                                 | UI フレームワークの役割                         |
|----------------------------------------------------------|-----------------------------------------------------------------|
| Azure からアクセス トークンを提供する                    | 与えられたアクセス トークンをパススルーしてコンポーネントを初期化する        |
| 更新機能を提供する                                 | 開発者から提供された機能を使用してアクセス トークンを更新する          |
| 通話またはチャットの参加情報を取得する、または渡す          | 通話とチャットの情報をパススルーしてコンポーネントを初期化する |
| 任意のカスタム データ モデルのユーザー情報を取得する、または渡す | レンダリングの対象となるコンポーネントにカスタム データ モデルをパススルーする          |
