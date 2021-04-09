---
title: Power BI アプリの作成の概要 - Microsoft AppSource
description: この記事では、Power BI アプリを Microsoft AppSource に公開するためのおおまかな手順について説明します。 商用マーケットプレースに公開するために Power BI アプリで満たす必要がある技術的およびビジネス要件も記載されています。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: navits09
ms.author: navits
ms.date: 04/02/2020
ms.openlocfilehash: b02c5bbf2e6accbea9c7a33dc26c4e89e46bb697
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97679144"
---
# <a name="power-bi-app-creation-overview"></a>Power BI アプリの作成の概要

この記事では、Power BI アプリを Microsoft [AppSource](https://appsource.microsoft.com/) に公開する方法について説明します。 Power BI アプリでは、データセット、レポート、およびダッシュボードを含むカスタマイズ可能なコンテンツがパッケージ化されます。 その後、AppSource を使ってアプリを他の Power BI プラットフォームで使用し、開発者によって許可されている調整とカスタマイズを実行し、それをご自身のデータに接続できます。

## <a name="publishing-benefits"></a>公開の利点

コマーシャル マーケットプレースに公開する利点:

- Microsoft のブランドを使用して、会社の宣伝ができる。
- AppSource 上では 1 億を超える Microsoft 365 および Dynamics 365 のユーザーに、さらに Azure Marketplace を通じて 20 万を超える組織に接触できる可能性がある。
- これらのマーケットプレースから質の高いリードを得ることができる。
- Microsoft のフィールドおよびテレセールス チームによりサービスを宣伝してもらえる。

## <a name="overview"></a>概要

:::image type="content" source="media/power-bi-app-publishing-steps.png" alt-text="Power BI アプリを公開する手順の概要" border="false":::

これらは主な公開手順です。

1. Power BI でアプリケーションを作成します。 パッケージのインストール リンクを受け取ります。これは、オファーの主な技術資産です。 パートナー センターでオファーを作成する前に、テスト パッケージを運用前環境に送信します。 詳細については、[Power BI アプリ](/power-bi/service-template-apps-overview)に関するページをご覧ください
2. 公式名、説明、ロゴなどのマーケティング資料を追加します。
3. 使用条件、プライバシー ポリシー、サポート ポリシー、ユーザー ヘルプなど、オファーの法的およびサポート ドキュメントを含めます。
4. オファーを作成する - パートナー センターを使用して、オファーの説明、マーケティング資料、法的情報、サポート情報、資産の仕様などの詳細を編集します。
5. それを公開するために送信します。
6. AppSource オンボーディング チームによって、アプリのテスト、検証、および認定が行われる、パートナー センターでプロセスを監視します。
7. 認定された後、アプリをそのテスト環境で確認してからリリースします。 これで、AppSource に一覧表示されます ("一般公開" される)。
8. Power BI で、パッケージを運用環境に送信します。 詳細については、[Power BI アプリ リリースの管理](/power-bi/service-template-apps-create#manage-the-template-app-release)に関する記述を参照してください。

## <a name="before-you-begin"></a>開始する前に

テンプレート、ヒント、およびサンプルを提供する、以下のリンクを確認してください。

- [Power BI アプリを作成する](/power-bi/service-template-apps-create)
- [Power BI アプリの作成に関するヒント](/power-bi/service-template-apps-tips)
- [サンプル](/power-bi/service-template-apps-samples)

## <a name="requirements"></a>必要条件

商用マーケットプレースで公開するには、Power BI アプリ オファーが次の技術的およびビジネス要件を満たしている必要があります。

### <a name="technical-requirements"></a>技術的な要件

必要となる主な技術資産は [Power BI アプリ](/power-bi/connect-data/service-template-apps-overview)です。 これは、プライマリ データセット、レポート、またはダッシュボードのコレクションです。 オプションの接続済みサービスと埋め込みデータセットも含まれています。これは、以前は[コンテンツ パック](/power-bi/service-organizational-content-pack-introduction)と呼ばれていました。 この種類のアプリの開発について詳しくは、[Power BI アプリ](/power-bi/connect-data/service-template-apps-overview)に関するページを参照してください。

#### <a name="get-an-installation-web-address"></a>インストール Web アドレスを取得する

Power BI アプリをビルドできるのは、[Power BI](https://powerbi.microsoft.com/) 環境内のみです。

1. [Power BI Pro ライセンス](/power-bi/service-admin-purchasing-power-bi-pro)でサインインします。
2. Power BI でアプリを作成してテストします。
3. アプリのインストール Web アドレスを受け取ったら、パートナー センターの **[技術的な構成]** ページに追加します。

Power BI でアプリが作成され、テストされた後、アプリケーションのインストール Web アドレスを保存します。これは [Power BI アプリ オファーを作成する](create-power-bi-app-offer.md)際に必要になるためです。

### <a name="business-requirements"></a>ビジネス要件

ビジネス要件には、手続き、契約、法律に関する義務が含まれます。 次の手順が必要です。

- 登録済みの商用マーケットプレース パブリッシャーになります。 登録していない場合は、「[パートナー センターでコマーシャル マーケットプレース アカウントを作成する](create-account.md)」の手順に従ってください。
- AppSource にオファーが掲載されるための条件を満たすコンテンツを提供します。 詳細については、[AppSource にアプリを掲載する方法](https://appsource.microsoft.com/blogs/have-an-app-to-list-on-appsource-here-s-how)に関するページをご覧ください。
- [Microsoft のプライバシーに関する声明](https://privacy.microsoft.com/privacystatement)に同意して従います。

## <a name="next-step"></a>次のステップ

- [パートナー センターで Power BI アプリ オファーを作成する](create-power-bi-app-offer.md)
