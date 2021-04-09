---
title: Microsoft コマーシャル マーケットプレースで SaaS オファーの対象ユーザーを追加する方法
description: Microsoft パートナーセンターでサービスとしてのソフトウェア (SaaS) オファーの対象ユーザーを追加する方法について説明します。
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 041df9b77e85f09d2cf680773edc995c6288acaa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96746422"
---
# <a name="how-to-add-a-preview-audience-for-your-saas-offer"></a>SaaS オファーのプレビュー対象ユーザーを追加する方法

パートナー センターでサービスとしてのソフトウェア (SaaS) オファーを作成する際は、オファー リストを公開する前にレビューできる対象ユーザーを定義する必要があります。 この記事では、プレビュー対象ユーザーを構成する方法について説明します。

> [!NOTE]
> トランザクションを個別に処理することを選択した場合、このオプションは表示されません。 代わりに、「[SaaS オファーを販売する方法](create-new-saas-offer-marketing.md)」に進んでください。

## <a name="define-a-preview-audience"></a>プレビュー対象ユーザーを定義する

**[プレビュー対象ユーザー]** タブでは、マーケットプレースの幅広い対象ユーザーに公開する前に SaaS オファーをレビューできる限られた対象ユーザーを定義できます。 Microsoft アカウント (MSA) または Azure Active Directory (Azure AD) の電子メール アドレスに招待状を送信できます。 手動で最小 1 個で最大 10 個のメール アドレスを追加するか、.csv ファイルを使用して最大 20 個をインポートします。 プレビュー対象ユーザー リストは、いつでも更新できます。

> [!NOTE]
> プレビュー対象ユーザーはプライベート対象ユーザーとは異なります。 プレビュー対象ユーザーは、オンライン ストアで公開される前にオファーにアクセスできます。 また、プランを作成してそれをプライベート対象ユーザーだけに公開することもできます。 プライベート プランについては、「[SaaS オファーのプランを作成する方法](create-new-saas-offer-plans.md)」を参照してください。

### <a name="add-email-addresses-manually"></a>メール アドレスを手動で追加する

1. **[プレビュー]** ページで表示されるボックスに、1 つの Azure AD または MSA メール アドレスと、説明 (省略可能) を追加します。
1. 別のメール アドレスを追加するには、 **[別のメール アドレスを追加]** リンクを選択します。
1. **[下書きの保存]** を選択してから、次の **[技術的な構成]** タブに進みます。
1. 「[SaaS オファーの技術的な詳細を追加する方法](create-new-saas-offer-technical.md)」に進みます。

### <a name="add-email-addresses-using-the-csv-file"></a>CSV ファイルを使用してメール アドレスを追加する

1. **[プレビュー対象ユーザー]** ページで、 **[対象者のエクスポート (csv)]** リンクを選択します。
1. Microsoft Excel などのアプリケーションで .CSV ファイルを開きます。
1. .CSV ファイルの **[ID]** 列に、プレビュー対象ユーザーに追加するメール アドレスを入力します。
1. **[説明]** 列では、メール アドレスごとに説明を追加できます (省略可能)。
1. **[タイプ]** 列で、メール アドレスを含む各行に **MixedAadMsaId** を追加します。
1. ファイルを .CSV ファイルとして保存します。
1. **[プレビュー対象ユーザー]** ページで、 **[対象者のインポート (csv)]** リンクを選択します。
1. **[確認**] ダイアログ ボックスで、 **[はい]** を選択します。
1. .CSV ファイルを選択して、 **[開く]** を選択します。
1. **[下書きの保存]** を選択してから、次の **[技術的な構成]** タブに進みます。

## <a name="next-steps"></a>次のステップ

- [SaaS オファーの技術的な詳細を追加する方法](create-new-saas-offer-technical.md)
