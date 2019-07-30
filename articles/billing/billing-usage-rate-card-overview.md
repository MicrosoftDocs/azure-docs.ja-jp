---
title: Azure Billing API による Azure の使用状況の取得 | Microsoft Docs
description: Azure Billing Usage API と RateCard API について説明します。これらの API を使用すると、Azure のリソース消費と傾向を把握できます。
services: ''
documentationcenter: ''
author: tonguyen
manager: mumami
editor: ''
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 5/10/2018
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 4756c7abcb354e0b72c8a95c9d2df4bb3a14671a
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443032"
---
# <a name="use-azure-billing-apis-to-programmatically-get-insight-into-your-azure-usage"></a>Azure Billing API を使用して、Azure の使用状況に関する詳細なデータをプログラムによって取得する
Azure Billing API を使用すると、使用状況やリソースに関するデータを、お使いのデータ分析ツールで取得できます。 Azure Resource Usage API と Azure Resource RateCard API は、コストを正確に予測して管理するうえで役立ちます。 これらの API は、Azure Resource Manager が公開している API ファミリに含まれ、リソース プロバイダーとして実装されています。  

## <a name="azure-invoice-download-api-preview"></a>Azure Invoice Download API (プレビュー)
[オプトインが完了したら](billing-manage-access.md#opt-in)、プレビュー版の [Invoice API](/rest/api/billing) を使用して請求書をダウンロードします。 機能は、次のとおりです。

* **Azure ロールベースのアクセス制御** - [Azure Portal](https://portal.azure.com) または [Azure PowerShell コマンドレット](/powershell/azure/overview)を使用して、アクセス ポリシーを構成し、サブスクリプションの使用状況データにアクセスできるユーザーやアプリケーションを指定できます。 呼び出し元は、認証に Azure Active Directory トークンを使用する必要があります。 また、呼び出し元が特定の Azure サブスクリプションの使用状況データにアクセスするには、請求閲覧者、閲覧者、所有者、共同作成者のいずれかのロールに呼び出し元を追加します。
* **日付のフィルタリング** - `$filter` パラメーターを使用して、新しい日付から順に請求期間終了日まですべての請求書を取得します。

> [!NOTE]
> この機能は最初のプレビュー版です。下位互換性を保つために変更される可能性があります。 現在、特定のサブスクリプション オファー (EA、CSP、AIO ではサポートされない) と Azure Germany では使用できません。

## <a name="azure-resource-usage-api-preview"></a>Azure Resource Usage API (プレビュー)
[Azure Resource Usage API](/previous-versions/azure/reference/mt219003(v=azure.100)) を使用すると、Azure の推定消費量データを取得できます。 この API には次の要素が含まれています。

* **Azure ロールベースのアクセス制御** - [Azure Portal](https://portal.azure.com) または [Azure PowerShell コマンドレット](/powershell/azure/overview)を使用して、アクセス ポリシーを構成し、サブスクリプションの使用状況データにアクセスできるユーザーやアプリケーションを指定できます。 呼び出し元は、認証に Azure Active Directory トークンを使用する必要があります。 また、呼び出し元が特定の Azure サブスクリプションの使用状況データにアクセスするには、請求閲覧者、閲覧者、所有者、共同作成者のいずれかのロールに呼び出し元を追加します。
* **時間単位または日単位の集計** - 呼び出し元は、時間単位のバケットまたは日単位のバケットのどちらで Azure 使用状況データを取得するかを指定できます。 既定値は日単位です。
* **インスタンス メタデータ (リソース タグなど)** - 完全修飾リソース URI (/subscriptions/{subscription-id}/..) などのインスタンスレベルの詳細情報、リソース グループ情報、リソース タグを取得できます。 これらのメタデータを利用すると、クロス課金のようなユースケースにおいて、プログラムでタグを使って確定的に使用状況を割り当てることができます。
* **リソース メタデータ** - 呼び出し元は、測定名、測定カテゴリ、測定サブカテゴリ、単位、リージョンなどのリソースの詳細情報をもとに、消費内容をより詳しく理解できます。 また、エクスペリエンス全体でデータを関連付けることができるように、現在、Azure Portal、Azure 使用状況 CSV、EA 課金 CSV など、一般公開されているエクスペリエンス全体でリソース メタデータの用語の調整を進めています。
* **さまざまなプラン タイプの使用状況** - 従量課金制、MSDN、料金コミットメント、料金クレジット、EA などのプラン タイプについて使用状況データを取得できます。ただし、[CSP](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-invoice#retrieve-usage-data-for-a-specific-subscription) の場合は対象外となります。

## <a name="azure-resource-ratecard-api-preview"></a>Azure Resource RateCard API (プレビュー)
[Azure Resource RateCard API](/previous-versions/azure/reference/mt219005(v=azure.100)) を使用して、使用可能な Azure リソースの一覧と、それぞれの推定料金情報を取得できます。 この API には次の要素が含まれています。

* **Azure ロールベースのアクセス制御** - [Azure Portal](https://portal.azure.com) または [Azure PowerShell コマンドレット](/powershell/azure/overview)を使用して、アクセス ポリシーを構成し、RateCard データにアクセスできるユーザーやアプリケーションを指定できます。 呼び出し元は、認証に Azure Active Directory トークンを使用する必要があります。 また、呼び出し元が特定の Azure サブスクリプションの使用状況データにアクセスするには、リーダー、所有者、共同作成者のいずれかのロールに呼び出し元を追加します。
* **従量課金制、MSDN、料金コミットメント、料金クレジット プランのサポート (EA および [CSP](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-pricelist#get-prices-by-using-the-azure-rate-card) はサポートされていません)** - この API は、Azure のプラン レベルの料金情報を提供します。  この API の呼び出し元は、プラン情報を渡してリソースの詳細と料金を取得する必要があります。 EA プランには登録ごとにカスタマイズされた料金があるため、現在は EA 料金を提供できません。

## <a name="scenarios"></a>シナリオ
Usage API と RateCard API を組み合わせて実現できるシナリオ例を次に示します。

* **月間の Azure 使用量** - Usage API と RateCard API を組み合わせて使うと、月間のクラウドの使用状況をより詳しく把握できます。 時間単位や日単位でバケットの使用状況と料金の推定値を分析できます。
* **アラートの設定** - Usage API と RateCard API を使用してクラウドの推定消費量と推定料金を取得し、リソースベースまたは料金ベースのアラートを設定することができます。
* **請求額の予測** – 推定の消費量とクラウド使用量を取得し、Machine Learning アルゴリズムを適用して、課金サイクルの終了時に請求される金額を予測することができます。
* **消費前のコスト分析** - RateCard API を使用して、ワークロードを Azure に移行した場合の想定使用量に対する請求金額を予測できます。 他のクラウドまたはプライベート クラウドに既存のワークロードがある場合、その使用状況を Azure の料金に対応付けることで、より正確な Azure の使用量を取得できます。 この情報をもとに、プランを決め、従量課金制以外のプラン タイプ (料金コミットメントや料金クレジットなど) との比較を行うことができます。 また、この API を使用してリージョンごとのコストの差異を確認し、what if コスト分析を実施して、デプロイに関する決定に役立てることができます。
* **what-if 分析** -

  * Azure リソースのリージョンや構成を変えた場合に、コスト効率が高くなるかどうかを判断できます。 Azure リソースのコストは、使用している Azure リージョンによって異なります。
  * 別の Azure プラン タイプの方が Azure リソースの料金が安くなるかどうかについても判断できます。


## <a name="next-steps"></a>次の手順
* GitHub でコード サンプルを確認します。
  * [Invoice API のコード サンプル](https://go.microsoft.com/fwlink/?linkid=845124)

  * [Usage API のコード サンプル](https://github.com/Azure-Samples/billing-dotnet-usage-api)

  * [RateCard API のコード サンプル](https://github.com/Azure-Samples/billing-dotnet-ratecard-api)

* Azure Resource Manager の詳細については、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md)」を参照してください。
