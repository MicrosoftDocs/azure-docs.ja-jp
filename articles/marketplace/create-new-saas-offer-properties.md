---
title: Microsoft パートナー センターで SaaS オファーのプロパティを構成する方法
description: パートナー センターでサービスとしてのソフトウェア (SaaS) Microsoft コマーシャル マーケットプレース オファーのプロパティを構成する方法について説明します。
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 7d90c9b9b2c9aa97083e17d5ab7d20fc6b471658
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94492125"
---
# <a name="how-to-configure-your-saas-offer-properties"></a>SaaS オファーのプロパティを構成する方法

この記事では、Microsoft マーケットプレースでサービスとしてのソフトウェア (SaaS) オファーのプロパティを構成する方法について説明します。

**[プロパティ]** タブで、オファー、アプリのバージョン、および法的契約に適用されるカテゴリと業界を定義します。 このページでは、オファーに関する正確な詳細を漏れなく指定してください。そうすれば、オファーが適切に表示され、適切な顧客に提供されます。

## <a name="select-a-category-for-your-offer"></a>オファーのカテゴリを選択する

**[カテゴリ]** で、少なくとも 1 つで最大 2 つのカテゴリを選択して、オファーを適切なマーケットプレース検索領域にグループ化します。 選択したカテゴリに基づいて、オファーを一覧表示するオンライン ストア (Azure Marketplace、Microsoft AppSource、またはその両方) を決定します。

## <a name="select-industries-optional"></a>業界を選択する (省略可能)

**[業界]** では、業界ごとに最大 2 つの業界と最大 2 つのサブ業界 (業種とも呼ばれます) を選択できます。 これらの業界は、お客様がオンライン ストアで業界やサブ業界で検索を絞り込むときにオファーを表示するために使用されます。

> [!NOTE]
> オファーが業界に固有でない場合は、このセクションを空白のままにします。

1. **[業界]** で、 **[+ 業界]** リンクを選択します。
1. **[業界]** リストから業界を選択します。
1. **[サブ業界]** リストから、少なくとも 1 つで最大 2 つの業種を選択します。 複数のサブ業界を選択するには、Ctrl キーを使用します。
1. 別の業界と業種を追加するには、 **[+ 業界]** を選択し、手順 1 ～ 3 を繰り返します。

## <a name="specify-an-app-version-optional"></a>アプリのバージョンを指定する (省略可能)

 **[アプリのバージョン]** ボックスにバージョン番号を入力します。 アプリのバージョンは、オファーのバージョン番号を識別するために AppSource マーケットプレースで使用されます。

## <a name="provide-terms-and-conditions"></a>使用条件を指定する

**[法的情報]** で、オファーの使用条件を指定します。 2 つのオプションがあります。

- [省略可能な変更ありの標準契約を使用する](#use-the-standard-contract)
- [独自の使用条件を使用する](#use-your-own-terms-and-conditions)

標準契約と省略可能な変更の詳細については、「[Microsoft コマーシャル マーケットプレースの標準契約](standard-contract.md)」を参照してください。 [標準契約](https://go.microsoft.com/fwlink/?linkid=2041178) PDF をダウンロードできます (ポップアップ ブロッカーをオフにしてください)。

### <a name="use-the-standard-contract"></a>標準契約を使用する

顧客の調達プロセスを簡素化し、ソフトウェア ベンダーの法務の複雑さを軽減するため、Microsoft では、コマーシャル マーケットプレースでオファーに使用できる標準契約を用意しています。 標準契約の下でソフトウェアを提供すると、顧客はそれを読んで一度承諾するだけで済み、提供元は独自の使用条件を作成する必要はありません。

1. **[Microsoft コマーシャル マーケットプレース向けの標準契約を使用する]** チェックボックスを選択します。

   ![[Microsoft コマーシャル マーケットプレース向けの標準契約を使用する] チェックボックスの画像。](partner-center-portal/media/use-standard-contract.png)
1. **[確認]** ダイアログ ボックスで **[承諾]** を選択します。 上にスクロールしないと見えない場合があります。
1. 続行する前に、 **[下書きの保存]** を選択します。

   > [!NOTE]
   > コマーシャル マーケットプレースの標準契約を使用してオファーを発行した後に、独自のカスタム使用条件を使用することはできません。 ソリューションは、省略可能な変更ありの標準契約または独自の使用条件の下で指定します。

### <a name="add-amendments-to-the-standard-contract-optional"></a>標準契約に変更を追加する (省略可能)

可能な変更には、*ユニバーサル* と *カスタム* の 2 種類があります。

#### <a name="add-universal-amendment-terms"></a>ユニバーサル変更条件を追加する

**[Microsoft のコマーシャル マーケットプレース向け標準契約へのユニバーサル変更条件]** ボックスには、ユニバーサル変更条件を入力します。 このボックスに入力できる文字数は無制限です。 これらの条項は、AppSource、Azure Marketplace、Azure portal で、発見と購入のフロー中に顧客に表示されます。

#### <a name="add-one-or-more-custom-amendments"></a>1 つ以上のカスタム変更を追加する

1. **[Microsoft のコマーシャル マーケットプレース向け標準契約のカスタム変更条件]** で、 **[カスタム変更条件の追加 (最大 10)]** リンクを選択します。
1. **[カスタム変更条件]** ボックスに変更条件を入力します。
1. **[テナント ID]** ボックスにテナント ID を入力します。 これらのカスタム条件に関連付けられたテナント ID の顧客にのみ、Azure portal でのオファーの購入フローでそれらの条件が表示されます。
   > [!TIP]
   > テナント ID で Azure の顧客が識別されます。 この ID は顧客に問い合わせることができ、顧客は [ **https://portal.azure.com**](https://portal.azure.com) >  **[Azure Active Directory]**  >  **[プロパティ]** にアクセスして ID を確認できます。 ディレクトリ ID の値はテナント ID (`50c464d3-4930-494c-963c-1e951d15360e` など) です。 [[What is my Microsoft Azure and Office 365 tenant ID?]\(Microsoft Azure および Office 365 テナント ID の確認\)](https://www.whatismytenantid.com/) で、顧客のドメイン名 URL を使用して顧客の組織のテナント ID を検索することもできます。
1. 必要に応じて、 **[説明]** ボックスにテナント ID のわかりやすい説明を入力します。 この説明は、変更の対象となる顧客を識別する際に役立ちます。
1. 別のテナント ID を追加するには、 **[Add a customer's tenant ID]\(顧客のテナント ID の追加\)** リンクを選択し、手順 3 と 4 を繰り返します。 最大 20 個のテナント ID を追加できます。
1. 別の変更条件を追加するには、手順 1 ～ 5 を繰り返します。 オファーごとに 10 件までのカスタム修正条項を提供できます。 
2. 続行する前に、 **[下書きの保存]** を選択します。

### <a name="use-your-own-terms-and-conditions"></a>独自の使用条件を使用する

標準契約ではなく、独自の使用条件を指定することもできます。 顧客は、オファーを試す前にこれらの条件に同意する必要があります。

1. **[法的情報]** で、 **[Microsoft コマーシャル マーケットプレース向けの標準契約を使用する]** チェックボックスがクリアされていることを確認します。
1. **[使用条件]** ボックスに、最大 10,000 万文字のテキストを入力します。
1. **[下書きの保存]** を選択してから、次の **[Offer listing]\(オファー登録情報\)** に進みます。

## <a name="next-steps"></a>次のステップ

- [SaaS オファー登録情報の詳細を構成する方法](create-new-saas-offer-listing.md)
