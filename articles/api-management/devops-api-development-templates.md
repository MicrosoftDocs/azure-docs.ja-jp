---
title: ARM テンプレートを使用した Azure API Management での CI/CD
description: CI/CD パイプラインで API のデプロイを管理するための、Azure Resource Manager テンプレートを使用した Azure API Management での API DevOps の概要
services: api-management
author: miaojiang
ms.service: api-management
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: apimpm
ms.openlocfilehash: 62f163b9ce649cd5ddb52b4325682570633dfb92
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96183160"
---
# <a name="cicd-for-api-management-using-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用した API Management 用の CI/CD

この記事では、Azure Resource Manager テンプレートを使用して、Azure API Management で API DevOps を使用する方法について説明します。 API の戦略的価値を受けて、継続的インテグレーションおよび継続的デプロイ (CI/CD) パイプラインが API 開発の重要な側面になってきました。 これにより、組織は、エラーが発生しやすい手動の手順を使わずに API 変更のデプロイを自動化し、問題を初期段階で検出して、最終的に短期間でユーザーに価値を届けることができます。 

この記事で説明されている DevOps アプローチを実装するための詳細情報、ツール、およびコード サンプルについては、GitHub に含まれるオープンソースの [Azure API Management DevOps リソース キット](https://github.com/Azure/azure-api-management-devops-resource-kit)をご覧ください。 顧客からさまざまなエンジニアリング カルチャや既存の自動化ソリューションが持ち込まれるため、このアプローチはすべてに対応できる万能なソリューションではありません。

## <a name="the-problem"></a>問題

現在、組織は複数のデプロイ環境 (開発、テスト、運用など) を備え、環境ごとに個別の API Management インスタンスを使用することが普通になっています。 一部のインスタンスは、リリース周期の異なるさまざまな API を担当する複数の開発チームで共有されています。

その結果、顧客は次のような課題に直面しています。

* API Management への API のデプロイを自動化する方法
* ある環境から別の環境に構成を移行する方法
* 同じ API Management インスタンスを共有するさまざまな開発チーム間の干渉を回避する方法

## <a name="manage-configurations-in-resource-manager-templates"></a>Resource Manager テンプレートで構成を管理する

次の図は、提案された方法を示しています。 

:::image type="content" source="media/devops-api-development-templates/apim-devops.png" alt-text="API Management を使用した DevOps を示す図。":::

この例では、デプロイ環境が 2 つあります。"*開発*" と "*運用*" です。 それぞれに、独自の API Management インスタンスがあります。 

* API 開発者は、開発インスタンスにアクセスし、それを使用して自分の API の開発とテストを行うことができます。 
* "*API 発行者*" と呼ばれる指定のチームが運用インスタンスを管理します。

この提案されたアプローチの鍵となるのは、API Management のすべての構成を [Azure Resource Manager テンプレート](../azure-resource-manager/templates/template-syntax.md)に保存しておくことです。 組織は、これらのテンプレートを Git などのソース管理システムで保持する必要があります。 図に示すように、発行者リポジトリに、テンプレート コレクション内の運用 API Management インスタンスのすべての構成が含まれています。

|テンプレート  |説明  |
|---------|---------|
|サービス テンプレート     | 価格レベルやカスタム ドメインなど、API Management インスタンスのサービス レベルの構成。         |
|共有テンプレート     |  グループ、製品、ロガーなど、API Management インスタンス全体の共有リソース。    |
|API テンプレート     |  API とそのサブリソース (操作、ポリシー、診断設定) の構成。        |
|マスター (メイン) テンプレート     |   すべてのテンプレートに[リンクし](../azure-resource-manager/templates/linked-templates.md)、それらを順番にデプロイすることで、すべてを 1 つにまとめます。 すべての構成を 1 つの API Management インスタンスにデプロイする場合は、メイン テンプレートをデプロイします。 各テンプレートを個別にデプロイすることもできます。       |

API 開発者は、発行者リポジトリを開発者リポジトリにフォークし、自分の API に関する変更を処理します。 ほとんどの場合、自分の API 用の API テンプレートに専念し、共有またはサービス テンプレートを変更する必要はありません。

## <a name="migrate-configurations-to-templates"></a>構成をテンプレートに移行する
API 開発者は、Resource Manager テンプレートの操作中に次のような課題に直面します。

* API 開発者は [OpenAPI 仕様](https://github.com/OAI/OpenAPI-Specification)を頻繁に使用して作業しますが、Resource Manager スキーマについてはよく理解していない可能性があります。 テンプレートを手動で作成すると、エラーが発生しやすくなる恐れがあります。 

   リソース キット内の [Creator](https://github.com/Azure/azure-api-management-devops-resource-kit/blob/master/src/APIM_ARMTemplate/README.md#Creator) と呼ばれるツールを使用すると、Open API 仕様ファイルに基づいた API テンプレートの作成を自動化できます。 また、開発者は API 用の API Management ポリシーを XML 形式で提供できます。 

* API Management を既に使用している顧客には、既存の構成を抽出して Resource Manager テンプレートに移すというもう 1 つの課題があります。 そのような顧客の場合、リソース キット内の [Extractor](https://github.com/Azure/azure-api-management-devops-resource-kit/blob/master/src/APIM_ARMTemplate/README.md#extractor) と呼ばれるツールを使用すると、API Management インスタンスから構成を抽出してテンプレートを生成できます。  

## <a name="workflow"></a>ワークフロー

* API 開発者が API の開発とテストを完了すると、API テンプレートが生成されたら、プル要求を送信して変更を発行者リポジトリにマージできます。 

* API 発行者は、プル要求を検証し、変更が安全で準拠していることを確認できます。 たとえば、API との通信が HTTPS だけに許可されているかどうかを確認できます。 ほとんどの検証は、CI/CD パイプラインでの手順として自動化できます。

* 変更が承認され、正常にマージされたら、API 発行者はスケジュールに基づいて、または必要に応じて、それらを運用インスタンスにデプロイすることを選択できます。 テンプレートのデプロイは、[GitHub Actions](https://github.com/Azure/apimanagement-devops-samples)、[Azure Pipelines](/azure/devops/pipelines)、[Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)、[Azure CLI](../azure-resource-manager/templates/deploy-cli.md) などのツールを使用して自動化できます。


この方法で、組織は API の変更を API Management インスタンスにデプロイする処理を自動化できるため、ある環境から別の環境に変更を簡単に昇格できます。 異なる API 開発チームが異なる API テンプレートやファイルのセットに取り組むことになるため、これにより異なるチーム間の干渉を防ぐことができます。

## <a name="video"></a>ビデオ

> [!VIDEO https://www.youtube.com/embed/4Sp2Qvmg6j8]

## <a name="next-steps"></a>次のステップ

- 追加情報、ツール、およびサンプル テンプレートについては、オープンソースの [Azure API Management DevOps リソース キット](https://github.com/Azure/azure-api-management-devops-resource-kit)をご覧ください。