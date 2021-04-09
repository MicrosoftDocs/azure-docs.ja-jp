---
title: チュートリアル - 開発者ポータルへのアクセスとそのカスタマイズ - Azure API Management | Microsoft Docs
description: API Management 開発者ポータルは、自動的に生成され、完全にカスタマイズ可能な、API のドキュメントが含まれる Web サイトです。このチュートリアルでは、API Management 開発者ポータルをカスタマイズする方法について説明します。
services: api-management
author: mikebudzynski
ms.service: api-management
ms.topic: tutorial
ms.date: 11/16/2020
ms.author: apimpm
ms.openlocfilehash: 90544fbafe7393630c3f3fbc694ae367eccb7f90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96012981"
---
# <a name="tutorial-access-and-customize-the-developer-portal"></a>チュートリアル:開発者ポータルへのアクセスとそのカスタマイズ

"*開発者ポータル*" は、自動的に生成され、完全にカスタマイズ可能な、API のドキュメントが含まれる Web サイトです。 API コンシューマーは、ここで API の検出、使用方法の学習、およびアクセスの要求を行うことができます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 開発者ポータルのマネージド バージョンにアクセスする
> * 管理インターフェイスを操作する
> * コンテンツをカスタマイズする
> * 変更を発行する
> * 発行されたポータルを表示する

開発者ポータルの詳細については、「[Azure API Management 開発者ポータルの概要](api-management-howto-developer-portal.md)」を参照してください。

:::image type="content" source="media/api-management-howto-developer-portal-customize/cover.png" alt-text="API Management 開発者ポータル - 管理者モード" border="false":::

## <a name="prerequisites"></a>前提条件

- 次のクイック スタートを完了すること:[Azure API Management インスタンスを作成する](get-started-create-service-instance.md)
- Azure API Management インスタンスをインポートして発行します。 詳細については、[インポートと発行](import-and-publish.md)に関するページを参照してください。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>ポータルに管理者としてアクセスする

マネージド バージョンのポータルにアクセスするには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) で、API Management インスタンスに移動します。
1. 上部のナビゲーション バーにある **[開発者ポータル]** ボタンを選択します。 ブラウザーの新しいタブで、管理バージョンのポータルが開きます。

## <a name="understand-the-portals-administrative-interface"></a>ポータルの管理インターフェイスの概要

### <a name="default-content"></a>既定のコンテンツ 

ポータルに初めてアクセスする場合、バックグラウンドで既定のコンテンツが自動的にプロビジョニングされます。 既定のコンテンツは、ポータルの機能を紹介し、ポータルをカスタマイズするために必要なカスタマイズを最小限に抑えるように設計されています。 ポータル コンテンツに含まれる内容の詳細については、「[Azure API Management 開発者ポータルの概要](api-management-howto-developer-portal.md)」を参照してください。

### <a name="visual-editor"></a>ビジュアル エディター

ビジュアル エディターを使用して、ポータルのコンテンツをカスタマイズできます。 
* 左側のメニュー セクションでは、ページ、メディア、レイアウト、メニュー、スタイル、または Web サイトの設定を作成または変更できます。 
* 下部にあるメニュー項目を使用すると、ビューポート (モバイルやデスクトップなど) を切り替えることができます。また、認証されたユーザーまたは匿名ユーザーに表示されるポータルの要素を確認し、操作を保存または元に戻すこともできます。
* ページに行を追加するには、プラス記号の付いた青いアイコンをクリックします。 
* ウィジェット (テキスト、画像、API の一覧など) を追加するには、プラス記号の付いた灰色のアイコンをクリックします。
* ドラッグ アンド ドロップ操作でページ内の項目の配置を変更します。 

### <a name="layouts-and-pages"></a>レイアウトとページ

:::image type="content" source="media/api-management-howto-developer-portal-customize/pages-layouts.png" alt-text="ページとレイアウト" border="false":::

レイアウトでは、ページの表示方法を定義します。 たとえば、既定のコンテンツには、2 つのレイアウトがあります。1 つはホーム ページに、もう 1 つは残りのすべてのページに適用されます。

レイアウトは、URL テンプレートをページの URL に一致させることによってページに適用されます。 たとえば、URL テンプレートが `/wiki/*` のレイアウトは、URL に `/wiki/getting-started` や `/wiki/styles` などの `/wiki/` セグメントを含むすべてのページに適用されます。

上の画像では、レイアウトに属するコンテンツは青色でマークされていますが、ページは赤色でマークされています。 メニュー セクションにはそれぞれマークが付けられています。

### <a name="styling-guide"></a>スタイル設定ガイド

:::image type="content" source="media/api-management-howto-developer-portal-customize/styling-guide.png" alt-text="スタイル設定ガイド" border="false":::

スタイル設定ガイドは、デザイナーを念頭に置いて作成されたパネルです。 ポータルですべてのビジュアル要素を監視し、スタイルを設定することができます。 スタイルは階層構造であり、多くの要素は他の要素からプロパティを継承します。 たとえば、ボタン要素には、テキストと背景に色が使用されます。 ボタンの色を変更するには、元の色のバリアントを変更する必要があります。

バリアントを編集するには、その変数を選択し、その上に表示されている鉛筆アイコンを選択します。 ポップアップ ウィンドウで変更を行ったら、閉じます。

### <a name="save-button"></a>[保存] ボタン

:::image type="content" source="media/api-management-howto-developer-portal-customize/save-button.png" alt-text="[保存] ボタン" border="false":::

ポータルで変更を行うたびに、下部にあるメニューの **[保存]** ボタンを選択するか Ctrl キーを押しながら S キーを押して手動で保存する必要があります。 変更内容を保存すると、変更したコンテンツは API Management サービスに自動的にアップロードされます。

## <a name="customize-the-portals-content"></a>ポータルのコンテンツをカスタマイズする

訪問者がポータルを利用できるようにする前に、自動的に生成されたコンテンツをカスタマイズする必要があります。 推奨される変更には、レイアウト、スタイル、およびホーム ページのコンテンツなどがあります。

> [!NOTE]
> 統合に関する考慮事項があるため、`/404`、`/500`、`/captcha`、`/change-password`、`/config.json`、`/confirm/invitation`、`/confirm-v2/identities/basic/signup`、`/confirm-v2/password`、`/internal-status-0123456789abcdef`、`/publish`、`/signin`、`/signin-sso`、`/signup` の各ページを削除したり、別の URL に移動したりすることはできません。

### <a name="home-page"></a>ホーム ページ

既定の **[ホーム]** ページには、プレースホルダーのコンテンツが入力されています。 このコンテンツを含むセクション全体を削除するか、構造を維持し、要素を 1 つずつ調整することができます。 生成されたテキストと画像を独自のものに置き換え、リンクが目的の場所を指していることを確認します。

### <a name="layouts"></a>レイアウト

ナビゲーション バーに自動的に生成されたロゴを独自の画像に置き換えます。

### <a name="styling"></a>スタイル設定

スタイルの調整は必須ではありませんが、特定の要素の調整を検討することをお勧めします。 たとえば、ブランドの色に合わせてプライマリ色を変更します。

### <a name="customization-example"></a>カスタマイズの例

次のビデオでは、ポータルのコンテンツを編集する方法、Web サイトの外観をカスタマイズする方法、変更を発行する方法を示します。

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a><a name="publish"></a> ポータルを発行する

ポータルとその最新の変更を訪問者が利用できるようにするには、"*発行*" する必要があります。 ポータルは、その管理インターフェイス内または Azure portal から発行することができます。

### <a name="publish-from-the-administrative-interface"></a>管理インターフェイスから発行する

1. **[保存]** アイコンを選択して、変更を確実に保存します。
1. メニューの **[操作]** セクションで、 **[Publish website]\(Web サイトの発行\)** を選択します。 この操作には数分かかることがあります。  

    :::image type="content" source="media/api-management-howto-developer-portal-customize/publish-portal.png" alt-text="ポータルを発行する" border="false":::

### <a name="publish-from-the-azure-portal"></a>Azure portal から発行する

1. [Azure portal](https://portal.azure.com) で、API Management インスタンスに移動します。
1. 左側のメニューの **[開発者ポータル]** で、 **[Portal overview]\(ポータルの概要\)** を選択します。
1. **[Portal overview]\(ポータルの概要\)** ウィンドウで **[発行]** を選択します。

    :::image type="content" source="media/api-management-howto-developer-portal-customize/pubish-portal-azure-portal.png" alt-text="Azure portal からポータルを発行する":::

> [!NOTE]
> API Management サービスの構成を変更した後は、ポータルを再発行する必要があります。 カスタム ドメインの割り当て、ID プロバイダーの更新、委任の設定、サインイン条件や製品の使用条件の指定といった作業を行ったら、ポータルを再発行してください。


## <a name="visit-the-published-portal"></a>発行されたポータルにアクセスする

ポータルを発行した後は、管理パネルと同じ URL (`https://contoso-api.developer.azure-api.net` など) でアクセスできます。 それを外部の訪問者として別のブラウザー セッションで (シークレット ブラウズ モードまたはプライベート ブラウズ モードを使用して) 表示します。

## <a name="apply-the-cors-policy-on-apis"></a>API に CORS ポリシーを適用する

ポータルの訪問者が組み込みの対話型コンソールを使用して API をテストできるようにするには、API で CORS (クロスオリジン リソース共有) を有効にします。 詳細については、[Azure API Management 開発者ポータルの概要](api-management-howto-developer-portal.md#cors)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

開発者ポータルの詳細については、次を参照してください。

- [Azure API Management 開発者ポータルの概要](api-management-howto-developer-portal.md)
- 非推奨のレガシ ポータルから[新しい開発者ポータルへの移行](developer-portal-deprecated-migration.md)。