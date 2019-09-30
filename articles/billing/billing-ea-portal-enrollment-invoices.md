---
title: Azure エンタープライズ登録の請求書
description: この記事では、Azure Enterprise の請求書を管理および操作する方法について説明します。
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/09/2019
ms.topic: conceptual
ms.service: billing
manager: boalcsva
ms.openlocfilehash: 5e3a6249f19df7d16c7051c43eec4275ec6f4131
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71090715"
---
# <a name="azure-enterprise-enrollment-invoices"></a>Azure エンタープライズ登録の請求書

この記事では、Azure Enterprise の請求書を管理および操作する方法について説明します。 請求書は料金明細を表示したものであるため、正確に記載されていることを確認する必要があります。 また、請求書の管理に必要な、その他のタスクについても理解しておく必要があります。

## <a name="incorrect-overage-invoice"></a>正しくない超過分の請求書

超過分の請求書が正しくないと思われる場合は、「[Azure Enterprise Agreement 請求書を理解する](billing-understand-your-bill-ea.md)」を参照して、Azure 利用料金の調整にお役立てください。 Microsoft がお客様に課金した超過分に間違いがあると思われる場合は、サポートにお問い合わせください。

## <a name="change-a-po-number-for-an-overage-invoice"></a>超過分の請求書の PO 番号を変更する

Azure EA Portal では、EA 管理者が請求書作成日より前に番号を設定しない限り、自動的に既定の注文書 (PO) 番号が生成されます。 EA 管理者は、Azure EA Portal にサインインし、 **[レポート]** に移動することで、PO 番号を更新できます。 EA 管理者は、PO 番号を更新できます。 PO 番号が示されている右上のセクションで、鉛筆アイコンをクリックし、情報を更新します。

EA 管理者は、請求書が作成される前に、現在の課金四半期中に PO 番号を更新できます。 また、自動請求書通知メールを受け取ってから 7 日以内に PO 番号を更新することもできます。

EA 管理者が PO 番号を更新しておらず、新しい PO 番号が必要な場合、唯一のオプションは、与信審査の完了と再課金です。 お客様が PO 番号を更新する必要がある場合は、パートナーにお問い合わせください。

## <a name="adjust-billing-frequency"></a>請求頻度を調整する

お客様への請求頻度は、年単位、四半期単位、または月単位です。 請求期間は、お客様が契約に署名する際に決定されます。 月単位の課金が、最小の請求間隔となります。

直接登録の請求期間を年単位から四半期単位に変更するには、エンタープライズ管理者の承認が必要です。 間接登録の場合は、パートナー管理者の承認が必要です。 この変更は、現在の課金四半期の終了時に有効になります。

年単位または四半期単位の請求期間を月単位に変更するには、契約の修正が必要です。  既存の登録請求期間を変更する場合は、エンタープライズ管理者、または "_請求先担当者_" として識別されるユーザーからの承認が必要です。 [Azure EA Portal のサポート](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c)に承認を送信すると、[問題のカテゴリ] で **[課金と請求]** を選択できます。  この変更は、現在の課金四半期の終了時に有効になります。

修正 M503 が署名されている場合は、任意の契約で任意の頻度から月単位の課金に変更することができます。 

## <a name="credits-and-adjustments"></a>クレジットと精算

登録に適用されるすべてのクレジットまたは精算は、[https://ea.azure.com](https://ea.azure.com) の **[レポート]** セクションに表示されます。 実際のクレジット額に関する具体的なご質問がある場合は、[Azure EA Portal のサポート](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c)にお問い合わせください。

## <a name="request-an-invoice-copy"></a>請求書のコピーを要求する

請求書のコピーを要求する場合は、パートナーにお問い合わせください。

## <a name="understand-your-bill"></a>課金内容を確認する

請求書と料金の詳細については、「[Azure Enterprise Agreement 請求書を理解する](billing-understand-your-bill-ea.md)」を参照してください。

## <a name="overage-offset-by-customers"></a>お客様による超過分の相殺

お客様が年額コミットメントで使用する超過分がある場合は、次の条件を満たす必要があります。

- お客様には、発生後に未払いの超過料金があり、課金対象サービスの終了日からまだ 1 年が経過していない必要があります。
- 利用可能な年額コミットメント額が、過去のすべての未払い Azure 請求書を含め、発生したすべての料金を超えている必要があります。
- 完了するように求められている請求期間が、完全に終了している必要があります。 毎月 5 日が過ぎると、課金が完全に終了します。
- 相殺を求めている請求期間が、完全に終了している必要があります。
- ACD の割引は、実際の新しいコミットメントから、以前の消費額に対して計画された資金を差し引いた額に基づきます。 この要件は、発生した超過料金にのみ適用されます。 これは、年額コミットメントを使用するサービスに対してのみ機能します。そのため、Marketplace の料金を対象にすることはできません。 Marketplace の料金は個別に請求されます。
- お客様が超過分の相殺を完了する必要がある場合は、サポート リクエストを開くことができます。 または、アカウント チームがサポート リクエストを開くことができます。 このプロセスを完了するには、お客様の EA 管理者または請求先担当者からのメールによる承認が必要です。

## <a name="view-price-sheet-information"></a>価格シートの情報を表示する

エンタープライズ管理者は、Azure サービスの登録に関連付けられている価格表を表示できます。

現在の価格シートを表示するには、次の手順を実行します。

1. Azure EA Portal で、 **[レポート]** をクリックし、 **[価格シート]** をクリックします。
2. 価格シートを表示するか、 **[ダウンロード]** をクリックします。

![価格シートの情報を示す例](./media/billing-ea-portal-enrollment-invoices/ea-create-view-price-sheet-information.png)

過去の価格表をダウンロードするには、次の手順を実行します。

1. Azure EA Portal で、 **[レポート]** をクリックし、 **[使用状況のダウンロード]** をクリックします。
2. 価格シートをダウンロードします。

![以前の価格シートをダウンロードする方法が示されている例](./media/billing-ea-portal-enrollment-invoices/create-ea-view-price-sheet-download-historical-price-list.png)

価格に違いについて疑問がある場合は、次のような理由があるとお考えください。

以前の登録と新しい登録の間で価格が変更されている可能性があります。 料金の変更が発生するのは、料金が契約の特定の登録の開始日から終了日までについて同意されたものであるためです。 登録が新しい登録に転送された場合、新しい契約の価格に従います。 価格は、お客様の価格シートによって定義されます。 そのため、新しい登録では価格が高くなる可能性があります。

登録が延長期間に入ると、価格も変更されます。 料金は従量課金制の料金に変更されます。

## <a name="request-detailed-usage-information"></a>詳細な使用状況情報を要求する

エンタープライズ管理者は、Azure EA Portal で使用状況データ、消費された年額コミットメント、および追加使用量に関する料金の概要を表示できます。 料金は、すべてのアカウントとサブスクリプションにわたって概要レベルで示されます。

特定のアカウントの詳細な使用状況を表示するには、 **[レポート]**  >  **[使用状況のダウンロード]** の順に移動し、使用状況の詳細レポートをダウンロードできます。 レポートには、適用される税金は含まれていません。 使用量が発生してからレポートに反映されるまでの待ち時間は、最大 8 時間になる場合があります。

間接登録の場合、コスト関連の情報を確認するには、事前にパートナーがマークアップ機能を有効にする必要があります。

## <a name="move-usage-data-to-another-enrollment"></a>使用状況データを別の登録に移動する

移動できるのは、前の転送以降の使用状況データのみです。 使用状況データを登録間で移動するには、次の 2 つのオプションがあります。

- 登録間でのアカウントの転送
- 登録間での登録の転送

どちらの場合も、EA サポート チームに[サポート リクエスト](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c)を送信してサポートを受ける必要があります。 

## <a name="monetary-commitment-and-unbilled-usage"></a>年額コミットメントと未請求の使用量

Azure 年額コミットメントは、Azure サービスに対する前払い金額です。 年額コミットメントは、サービスを使用すると消費されます。 ファーストパーティの Azure サービスでは、年額コミットメントを使用します。 ただし、個別請求の料金と Marketplace サービスについては例外があります。

"_個別請求の料金_" と "_Azure Marketplace サービス_" は、Azure Marketplace プラットフォームからデプロイされるサードパーティのサービスです。 2 つのサービスの主な違いは、サードパーティ発行元との間で確立された契約構造に基づく請求方法にあります。

サードパーティでの使用量は、**個別請求の料金**として請求書に記載されます。 この使用量によって、Azure 年額コミットメント残高が消費されます。 サードパーティーのサービスの一覧を確認するには、「[Azure Marketplace third-party reseller services now use Azure monetary commitment (Azure Marketplace のサードパーティ リセラー サービスで Azure 年額コミットメントの使用が可能に)](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/)」を参照してください。

請求書の **Azure Marketplace** に表示される使用量は、別途課金されます。 年額コミットメントの対象にはなりません。 ただし、Marketplace、超過分、個別請求の料金を含むすべての料金が、1 つの請求書にまとめられます。

Azure ファーストパーティの超過分と Azure サードパーティの Marketplace 購入分については、1 つの請求書を受け取ります。

年額コミットメントが完全に消費されると、使用量は月単位または四半期単位で課金されます。 登録が 2018 年 5 月より前に開始されている場合は、150% の年額コミットメントしきい値ルールが適用され、四半期ごとに超過分に対する課金が行われます。 登録が 2018 年 5 月以降に開始されている場合、すべての超過分が月単位で課金されます。

## <a name="next-steps"></a>次の手順
- 請求書と料金の詳細については、「[Azure Enterprise Agreement 請求書を理解する](billing-understand-your-bill-ea.md)」を参照してください。
- Azure EA Portal の使用を開始するには、「[Get started with the Azure EA portal (Azure EA Portal の概要)](billing-ea-portal-get-started.md)」を参照してください。
