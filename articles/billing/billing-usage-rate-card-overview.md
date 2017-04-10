---
title: Azure Billing API | Microsoft Docs
description: "Azure Billing Usage API と RateCard API について説明します。これらの API を使用すると、Azure のリソース消費と傾向を把握できます。"
services: 
documentationcenter: 
author: BryanLa
manager: ruchic
editor: 
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 03/25/2017
ms.author: mobandyo;bryanla
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 869734ad4eb2d7a408cc0d33d1492b50b1b8dc0f
ms.lasthandoff: 03/29/2017


---
# <a name="use-azure-billing-apis-to-programmatically-get-insight-into-your-azure-usage"></a>Azure Billing API を使用して、Azure の使用状況に関する詳細なデータをプログラムによって取得する
Azure Billing API を使用すると、使用状況やリソースに関するデータを、お使いのデータ分析ツールで取得できます。 Azure Resource Usage API と Azure Resource RateCard API は、コストを正確に予測して管理するうえで役立ちます。 これらの API は、Azure Resource Manager が公開している API ファミリに含まれ、リソース プロバイダーとして実装されています。  

## <a name="azure-resource-usage-api-preview"></a>Azure Resource Usage API (プレビュー)
Azure Resource Usage API を使用すると、Azure の推定消費量データを取得できます。 この API には次の要素が含まれています。

* **Azure ロールベースのアクセス制御** - [Azure Portal](https://portal.azure.com) または [Azure PowerShell コマンドレット](/powershell/azureps-cmdlets-docs)を使用して、アクセス ポリシーを構成し、サブスクリプションの使用状況データにアクセスできるユーザーやアプリケーションを指定できます。 呼び出し元は、認証に Azure Active Directory トークンを使用する必要があります。 また、呼び出し元が特定の Azure サブスクリプションの使用状況データにアクセスするには、リーダー、所有者、共同作成者のいずれかのロールに呼び出し元を追加します。
* **時間単位または日単位の集計** - 呼び出し元は、時間単位のバケットまたは日単位のバケットのどちらで Azure 使用状況データを取得するかを指定できます。 既定値は日単位です。
* **インスタンス メタデータ (リソース タグなど)** - 完全修飾リソース URI (/subscriptions/{subscription-id}/..) などのインスタンスレベルの詳細情報、リソース グループ情報、リソース タグを取得できます。 これらのメタデータを利用すると、クロス課金のようなユースケースにおいて、プログラムでタグを使って確定的に使用状況を割り当てることができます。
* **リソース メタデータ** - 呼び出し元は、測定名、測定カテゴリ、測定サブカテゴリ、単位、リージョンなどのリソースの詳細情報をもとに、消費内容をより詳しく理解できます。 また、エクスペリエンス全体でデータを関連付けることができるように、現在、Azure Portal、Azure 使用状況 CSV、EA 課金 CSV など、一般公開されているエクスペリエンス全体でリソース メタデータの用語の調整を進めています。
* **すべてのプラン タイプの使用状況** - 従量課金制、MSDN、料金コミットメント、料金クレジット、EA など、すべてのプラン タイプについて使用状況データを取得できます。

## <a name="azure-resource-ratecard-api-preview"></a>Azure Resource RateCard API (プレビュー)
Azure Resource RateCard API を使用して、使用可能な Azure リソースの一覧と、それぞれの推定料金情報を取得できます。 この API には次の要素が含まれています。

* **Azure ロールベースのアクセス制御** - [Azure Portal](https://portal.azure.com) または [Azure PowerShell コマンドレット](/powershell/azureps-cmdlets-docs)を使用して、アクセス ポリシーを構成し、RateCard データにアクセスできるユーザーやアプリケーションを指定できます。 呼び出し元は、認証に Azure Active Directory トークンを使用する必要があります。 また、呼び出し元が特定の Azure サブスクリプションの使用状況データにアクセスするには、リーダー、所有者、共同作成者のいずれかのロールに呼び出し元を追加します。
* **従量課金制、MSDN、料金コミットメント、料金クレジット プランのサポート (EA はサポートされていません)** - この API は、Azure のプラン レベルの料金情報を提供します。  この API の呼び出し元は、プラン情報を渡してリソースの詳細と料金を取得する必要があります。 EA プランには登録ごとにカスタマイズされた料金があるため、現在は EA 料金を提供できません。 

## <a name="scenarios"></a>シナリオ
Usage API と RateCard API を組み合わせて実現できるシナリオ例を次に示します。

* **月間の Azure 使用量** - Usage API と RateCard API を組み合わせて使うと、月間のクラウドの使用状況をより詳しく把握できます。 時間単位や日単位でバケットの使用状況と料金の推定値を分析できます。
* **アラートの設定** - Usage API と RateCard API を使用してクラウドの推定消費量と推定料金を取得し、リソースベースまたは料金ベースのアラートを設定することができます。
* **請求額の予測** – 推定の消費量とクラウド使用量を取得し、Machine Learning アルゴリズムを適用して、課金サイクルの終了時に請求される金額を予測することができます。
* **消費前のコスト分析** - RateCard API を使用して、ワークロードを Azure に移行した場合の想定使用量に対する請求金額を予測できます。 他のクラウドまたはプライベート クラウドに既存のワークロードがある場合、その使用状況を Azure の料金に対応付けることで、より正確な Azure の使用量を取得できます。 この情報をもとに、プランを決め、従量課金制以外のプラン タイプ (料金コミットメントや料金クレジットなど) との比較を行うことができます。 また、この API を使用してリージョンごとのコストの差異を確認し、what if コスト分析を実施して、デプロイに関する決定に役立てることができます。
* **what-if 分析** -
  
  * Azure リソースのリージョンや構成を変えた場合に、コスト効率が高くなるかどうかを判断できます。 Azure リソースのコストは、使用している Azure リージョンによって異なります。
  * 別の Azure プラン タイプの方が Azure リソースの料金が安くなるかどうかについても判断できます。

## <a name="partner-solutions"></a>パートナー ソリューション
「[Cloudyn は Microsoft Azure Usage API と RateCard API を使用してユーザー向けに ITFM を提供](billing-usage-rate-card-partner-solution-cloudyn.md)」では、Azure Billing API パートナーの [Cloudyn](https://www.cloudyn.com/microsoft-azure/) が提供した統合エクスペリエンスについて説明しています。 この記事では、エクスペリエンスについて解説すると共に、Cloudyn と Azure Billing API を使用して Azure 消費データを把握する方法についての短いビデオも用意されています。

「[Cloud Cruiser と Microsoft Azure Billing API の統合](billing-usage-rate-card-partner-solution-cloudcruiser.md)」では、[Cloud Cruiser の Express for Azure Pack](http://www.cloudcruiser.com/partners/microsoft/) を Windows Azure Pack (WAP) ポータルから直接利用する方法について説明しています。 この方法では、Microsoft Azure のプライベート クラウドまたはホスト型パブリック クラウドの運用面と料金面の両方を 1 つのユーザー インターフェイスでシームレスに管理できます。   

## <a name="next-steps"></a>次のステップ
* 両方の API の詳細については、「[Azure Billing REST API Reference (Azure Billing REST API リファレンス)](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c)」を参照してください。
* サンプル コードをすぐに確認するには、「 [Azure コード サンプル](https://azure.microsoft.com/documentation/samples/?term=billing)」の「Microsoft Azure Billing API コード サンプル」を参照してください。

## <a name="learn-more"></a>詳細情報
* 「[Azure リソース マネージャーの概要](../azure-resource-manager/resource-group-overview.md)」をご覧ください。
* クラウドの使用量を把握するために必要なツール群の詳細については、Gartner の記事「[Market Guide for IT Financial Management (ITFM) Tools (IT 財務管理 (ITFM) ツールの市場ガイド)](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb)」をご覧ください。


