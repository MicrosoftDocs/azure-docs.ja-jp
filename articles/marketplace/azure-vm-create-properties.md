---
title: Azure Marketplace で仮想マシン オファーのプロパティを構成する
description: Azure Marketplace で仮想マシン オファーのプロパティを構成する方法について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 281553782774c31ec8cfaf614542fd739c4d3dd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94629531"
---
# <a name="how-to-configure-virtual-machine-offer-properties"></a>仮想マシン オファーのプロパティを構成する方法

**[プロパティ]** ページで (パートナー センターの左ナビゲーション メニューから選択)、Azure Marketplace で仮想マシン (VM) オファーをグループ化するのに使用されるカテゴリ、アプリケーションのバージョン、オファーをサポートする法的契約を定義します。

## <a name="select-a-category"></a>[カテゴリの選択]

オファーを適切な Azure Marketplace 検索領域に配置するために、カテゴリとサブカテゴリを選択します。 オファーでこれらのカテゴリがどのようにサポートされるかを、後で必ずオファーの説明に記述してください。

- プライマリ カテゴリを選択してください。
- 2 つ目のオプション カテゴリ (セカンダリ) を追加するには、 **[+ カテゴリ]** リンクを選択します。
- プライマリおよびセカンダリ カテゴリにそれぞれ最大 2 つのサブカテゴリを選択します。 オファーに適用できるサブカテゴリがない場合は、 **[該当なし]** を選択します。 Ctrl キーを押しながらクリックし、2 つ目のサブカテゴリを選択します。

「[オファーの掲載のベスト プラクティス](gtm-offer-listing-best-practices.md)」でカテゴリとサブカテゴリの完全な一覧を参照してください。 仮想マシンのオファーは、Azure Marketplace では常に **[計算]** カテゴリに表示されます。

## <a name="provide-terms-and-conditions"></a>使用条件を指定する

**[法的情報]** で、オファーの使用条件を指定します。 2 つのオプションがあります。

- [省略可能な変更ありの標準契約を使用する](#use-the-standard-contract)
- [独自の使用条件を使用する](#use-your-own-terms-and-conditions)

標準契約と省略可能な変更の詳細については、「[Microsoft コマーシャル マーケットプレースの標準契約](standard-contract.md)」を参照してください。 [標準契約](https://go.microsoft.com/fwlink/?linkid=2041178) PDF をダウンロードできます (ポップアップ ブロッカーをオフにしてください)。

### <a name="use-the-standard-contract"></a>標準契約を使用する

顧客の調達プロセスを簡素化し、ソフトウェア ベンダーの法務の複雑さを軽減するため、Microsoft では、コマーシャル マーケットプレースでオファーに使用できる標準契約を用意しています。 標準契約の下でソフトウェアを提供すると、顧客はそれを読んで一度承諾するだけで済み、提供元は独自の使用条件を作成する必要はありません。

1. **[Microsoft コマーシャル マーケットプレース向けの標準契約を使用する]** チェックボックスを選択します。

   ![[Microsoft コマーシャル マーケットプレース向けの標準契約を使用する] チェックボックスの画像。](partner-center-portal/media/use-standard-contract.png)

1. **[確認]** ダイアログ ボックスで **[承諾]** を選択します。 画面のサイズによっては、上にスクロールして表示する必要があります。
1. 続行する前に、 **[下書きの保存]** を選択します。

   > [!NOTE]
   > コマーシャル マーケットプレースの標準契約を使用してオファーを発行した後に、独自のカスタム使用条件を使用することはできません。 ソリューションは、省略可能な変更ありの標準契約または独自の使用条件の下で指定します。

#### <a name="add-amendments-to-the-standard-contract-optional"></a>標準契約に変更を追加する (省略可能)

可能な変更には、*ユニバーサル* と *カスタム* の 2 種類があります。

##### <a name="add-universal-amendment-terms"></a>ユニバーサル変更条件を追加する

**[Microsoft のコマーシャル マーケットプレース向け標準契約へのユニバーサル変更条件]** ボックスには、ユニバーサル変更条件を入力します。 このボックスに入力できる文字数は無制限です。 これらの条項は、AppSource、Azure Marketplace、Azure portal で、発見と購入のフロー中に顧客に表示されます。

##### <a name="add-one-or-more-custom-amendments"></a>1 つ以上のカスタム変更を追加する

1. **[Microsoft のコマーシャル マーケットプレース向け標準契約のカスタム変更条件]** で、 **[カスタム変更条件の追加 (最大 10)]** リンクを選択します。
2. **カスタム修正条項** をボックスに入力します。
3. **テナント ID** をボックスに入力します。 これらのカスタム条件に関連付けられたテナント ID の顧客にのみ、Azure portal でのオファーの購入フローでそれらの条件が表示されます。

   > [!TIP]
   > テナント ID で Azure の顧客が識別されます。 この ID は顧客に問い合わせることができ、顧客は [ **https://portal.azure.com**](https://portal.azure.com) >  **[Azure Active Directory]**  >  **[プロパティ]** にアクセスして ID を確認できます。 ディレクトリ ID の値はテナント ID (`50c464d3-4930-494c-963c-1e951d15360e` など) です。 [[What is my Microsoft Azure and Office 365 tenant ID?]\(Microsoft Azure および Office 365 テナント ID の確認\)](https://www.whatismytenantid.com/) で、顧客のドメイン名 URL を使用して顧客の組織のテナント ID を検索することもできます。

4. 任意で、テナント ID のわかりやすい **説明** を入力します。 この説明は、変更の対象となる顧客を識別する際に役立ちます。
5. 別のテナント ID を追加するには、 **[Add a customer's tenant ID (Max 10)]\(顧客のテナント ID の追加 (最大 10)\)** リンクを選択し、手順 3 と 4 を繰り返します。 最大 20 個のテナント ID を追加できます。
6. 別の変更条件を追加するには、手順 1 ～ 5 を繰り返します。 オファーごとに 10 件までのカスタム修正条項を提供できます。
7. 続行する前に、 **[下書きの保存]** を選択します。

### <a name="use-your-own-terms-and-conditions"></a>独自の使用条件を使用する

標準契約ではなく、独自の使用条件を指定することもできます。 顧客は、オファーを試す前にこれらの条件に同意する必要があります。

1. **[法的情報]** で **[Microsoft コマーシャル マーケットプレース向けの標準契約を使用する]** チェックボックスの選択を解除します。
1. **[使用条件]** ボックスに、最大 10,000 万文字のテキストを入力します。

   > [!NOTE]
   > さらに長い説明が必要な場合は、使用条件を確認できる場所を指し示す 1 つの Web アドレスを入力します。 これはアクティブなリンクとして顧客に表示されます。

1. 左側のナビゲーション メニューの次のタブである **[オファー登録情報]** に進む前に、 **[下書きの保存]** を選択します。

## <a name="next-steps"></a>次のステップ

- [VM オファー登録情報を構成する](azure-vm-create-listing.md)
