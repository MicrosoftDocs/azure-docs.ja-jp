---
title: インクルード ファイル
description: Microsoft 商業マーケットプレース標準契約のテキストのインクルード ファイル
documentationcenter: partner-center-commercial-marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
ms.date: 01/22/2020
ms.custom: include file
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: d899db8b5f6f3b7c0ccef4e44c814f1e126c626a
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87326675"
---
Microsoft では、商業マーケットプレース向けに標準契約テンプレートを提供しています。

- **[Use the Standard Contract for the Microsoft commercial marketplace?]\(Microsoft 商業マーケットプレース向け標準契約を使用しますか?\)**

お客様の調達プロセスを簡素化し、ソフトウェア ベンダーの法的な複雑さを軽減するために、Microsoft では、マーケットプレースでの取引の促進に役立つ Microsoft 商業マーケットプレースの標準契約を用意しています。 商業マーケットプレース パブリッシャーは、カスタムの使用条件を作成するのではなく、標準契約の下で各自のソフトウェアを提供することを選択でき、お客様はそれを詳細に調べて 1 回同意するだけで済みます。 標準契約はこちら (https://go.microsoft.com/fwlink/?linkid=2041178 ) で見つけることができます。

[Use the Standard Contract for the commercial marketplace] (商業マーケットプレース向け標準契約を使用する) チェック ボックスを選択することで、独自のカスタムの使用条件を提供する代わりに標準契約を使用することを選択できます。

![標準契約チェック ボックスの使用](./media/use-standard-contract.png)

> [!NOTE]
> Microsoft 商業マーケットプレースの標準契約を使用してオファーを発行した後に、独自のカスタムの使用条件を使用することはできません。 これは "または" のシナリオです。 ソリューションは、標準契約**または**独自の使用条件のどちらかの下で提供されます。 標準契約の使用条件を変更したい場合は、Standard Contract Amendments (標準契約の修正) を使用して行うことができます。

**Standard Contract Amendments (標準契約の修正)**

Standard Contract Amendments (標準契約の修正) を使用すると、発行元は簡易さのために標準契約条件を選択し、各自の製品またはビジネスに合わせて使用条件をカスタマイズすることができます。 Microsoft 標準契約を既に確認し、同意している場合、お客様に必要なことは契約の修正の確認のみです。

商業マーケットプレース パブリッシャーが使用できる修正には、次の 2 種類があります。

- Universal Amendments (ユニバーサル修正):これらの修正は、すべてのお客様の標準契約に例外なく適用されます。 ユニバーサル修正は、購入フローにあるオファーのすべてのお客様に示されます。 お客様は、提供されるオファーを使用する前に、標準契約の使用条件とこの修正に同意する必要があります。
- Custom Amendments (カスタム修正):これらの修正は、Azure テナント ID を通して特定のお客様のみを対象にした、標準契約への特殊な修正です。 発行元は、対象にするテナントを選択できます。 カスタム修正の使用条件は、オファーの購入フローでこのテナントのお客様にのみ示されます。  お客様は、提供されるオファーを使用する前に、標準契約の使用条件とこの修正に同意する必要があります。

>[!NOTE]
> これらの 2 種類の修正は、互いに重なり合っています。 カスタム修正の対象となるお客様には、購入中に標準契約へのユニバーサル修正も示されます。

**[Microsoft のコマーシャル マーケットプレース向け標準契約へのユニバーサル変更条件]** : このボックスには、ユニバーサルな修正条項を入力します。 オファーごとに 1 つのユニバーサル修正を提供できます。 このボックスに入力できる文字数は無制限です。 これらの条項は、AppSource、Azure Marketplace、Azure portal で、発見と購入のフロー中に顧客に表示されます。

**[Custom amendment terms to the Standard Contract for Microsoft's commercial marketplace]\(Microsoft の商業マーケットプレース向け標準契約のカスタム修正条項\)** : まず、 **[Add custom amendment terms]\(カスタム修正条項の追加\)** を選択します。 オファーごとに 10 件までのカスタム修正条項を提供できます。

- **[Custom amendment terms]\(カスタム修正条項\)** : カスタム修正条項をカスタム修正条項ボックスに入力します。 このボックスに入力できる文字数は無制限です。 Azure portal では、これらのカスタム修正条項に対して指定したテナント ID の顧客にのみ、オファーの購入フローでカスタム修正条項が提示されます。  
- **[テナント ID]** (必須): それぞれのカスタム修正で対象にできるテナント ID は 20 個までです。 カスタム修正を追加する場合、少なくとも 1 つのテナント ID を指定する必要があります。 テナント ID は Azure で顧客を識別します。 この ID は顧客に問い合わせることができ、顧客は portal.azure.com > [Azure Active Directory] > [プロパティ] にアクセスして ID を確認できます。 ディレクトリ ID の値はテナント ID です (例: 50c464d3-4930-494c-963c-1e951d15360e)。 [[What is my Microsoft Azure and Office 365 tenant ID?]\(Microsoft Azure および Office 365 テナント ID の確認\)](https://www.whatismytenantid.com) で、顧客のドメイン名 URL を使用して顧客の組織のテナント ID を検索することもできます。
- **[説明]** (省略可能): 必要に応じて、修正の対象となる顧客を特定しやすくするために、テナント ID のわかりやすい説明を入力します。

**使用条件**

独自のカスタムの使用条件を指定する場合は、使用条件フィールドに入力することもできます。 このフィールドには最大で 10,000 文字のテキストを入力できます。 それよりも長い説明が使用条件に必要な場合は、使用条件を確認できるサイトの URL リンクのみをこのフィールドに入力します。 これはアクティブなリンクとして顧客に表示されます。

顧客は、オファーを試す前にこれらの条件を承諾する必要があります。

続行する前に、 **[下書きの保存]** を選択します。
