---
title: Apache Kafka for Confluent Cloud を作成する - Azure パートナー ソリューション
description: この記事では、Azure portal を使用して Apache Kafka for Confluent Cloud のインスタンスを作成する方法について説明します。
ms.service: partner-services
ms.topic: quickstart
ms.date: 01/15/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: f4c6dacf63b1be44e826fe6841c87ccec4bf9b1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98253691"
---
# <a name="quickstart-get-started-with-apache-kafka-for-confluent-cloud"></a>クイック スタート: Apache Kafka for Confluent Cloud の概要

このクイックスタートでは、Azure portal を使用して Apache Kafka for Confluent Cloud のインスタンスを作成します。

## <a name="prerequisites"></a>前提条件

- Azure アカウント。 有効な Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/)を作成してください。
- Azure サブスクリプションの "_所有者_" ロールまたは "_共同作成者_" ロールが必要です。 Azure と Confluent の統合を設定できるのは、"_所有者_" または "_共同作成者_" のアクセス権を持つユーザーだけです。 作業を始める前に、[適切なアクセス権があることを確認](../../role-based-access-control/check-access.md)してください。

## <a name="find-offer"></a>プランを探す

Azure portal を使用して Apache Kafka for Confluent Cloud アプリケーションを探します。

1. Web ブラウザーで、[Azure portal](https://portal.azure.com/) に移動してサインインします。

1. 最近のセッションで **Marketplace** にアクセスした場合は、表示されているオプションからそのアイコンを選択します。 それ以外の場合は、_Marketplace_ を検索します。

    :::image type="content" source="media/marketplace.png" alt-text="Marketplace アイコン。":::

1. **[Marketplace]** ページには、使用したいプランの種類に応じて選択できるオプションが 2 つあります。 従量課金制プランまたはコミットメント プランにサインアップすることができます。 従量課金制は一般公開されています。 コミットメント プランは、プライベート プランが承認されているユーザーが利用できます。

   - **従量課金制** をご利用の場合は、_Apache Kafka on Confluent Cloud_ を検索します。 Apache Kafka on Confluent Cloud のプランを選択します。

     :::image type="content" source="media/search-pay-as-you-go.png" alt-text="Azure Marketplace プランの検索。":::

   - **コミットメント** をご利用の場合は、 **[View Private offers]\(プライベート プランを表示\)** リンクを選択します。 コミットメントを利用するには、最低支出額にサインアップする必要があります。 このオプションは、このサービスが長期間必要であることがわかっている場合にのみ使用してください。

     :::image type="content" source="media/view-private-offers.png" alt-text="プライベート プランの表示。":::

     _Apache Kafka on Confluent Cloud_ を探します。

     :::image type="content" source="media/select-from-private-offers.png" alt-text="プライベート プランの選択。":::

## <a name="create-resource"></a>リソースを作成する

Apache Kafka on Confluent Cloud のプランを選択したら、アプリケーションを設定する準備は完了です。

1. 前のセクションでプライベート プランを選択した場合は、プランの種類に関して次の 2 つのオプションがあります。

    - Confluent Cloud - 従量課金制
    - コミットメント - コミット プラン

   プライベート プランを選択しなかった場合は、従量課金制オプションだけが表示されます。

   使用するプランを選択し、 **[Set up + subscribe]\(設定 + 登録\)** を選択します。

    :::image type="content" source="media/setup-subscribe.png" alt-text="設定と登録。":::

1. **[Create Confluent Cloud Resource]\(Confluent クラウド リソースの作成\)** の [基本] ページで、次の値を指定します。 完了したら、 **[Next: tags]\(次へ: タグ** を選択します。

    :::image type="content" source="media/setup-basics.png" alt-text="Confluent Cloud リソースを設定するためのフォーム。":::

    | プロパティ | 説明 |
    | ---- | ---- |
    | **サブスクリプション** | ドロップダウン メニューから、デプロイ先の Azure サブスクリプションを選択します。 "_所有者_" または "_共同作成者_" のアクセス権が必要です。 |
    | **リソース グループ** | 新しいリソース グループを作成するか、既存のリソース グループを使用するかを指定します。 リソース グループは、Azure ソリューションの関連するリソースを保持するコンテナーです。 詳しくは、[Azure リソース グループの概要](../../azure-resource-manager/management/overview.md)に関するページをご覧ください。 |
    | **Confluent organization name (Confluent 組織名)** | SaaS (サービスとしてのソフトウェア) リソースに名前を付けます。 |
    | **リージョン** | ドロップダウン メニューから次のいずれかのリージョンを選択します。 <br/><br/> オーストラリア東部、カナダ中部、米国中部、米国東部、米国東部 2、フランス中部、北ヨーロッパ、東南アジア、英国南部、米国中西部、西ヨーロッパ、米国西部 2 |
    | **プラン** | **[従量課金制]** または **[コミットメント]** を選択します。 |
    | **請求期間** | 選択した課金プランに応じてあらかじめ入力されています。 |
    | **価格** | 選択した Confluent プランに応じてあらかじめ入力されています。 |

1. **[タグ]** で、リソースに適用するタグの **名前** と **値** のペアを指定します。 タグを入力したら、 **[確認と作成]** を選択します。

    :::image type="content" source="media/setup-tags.png" alt-text="プロジェクト タグの追加。":::

1. 指定した設定を確認します。 準備ができたら、 **[作成]** を選択します。

1. リソースの作成には数分かかります。 **[通知]** でデプロイの状態を確認できます。 デプロイが完了したら、リソースを選択して **[概要]** ページを表示します。

    :::image type="content" source="media/deployment-status.png" alt-text="デプロイの状態。":::

   エラーが表示された場合は、「[Apache Kafka for Confluent Cloud ソリューションのトラブルシューティング](troubleshoot.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Confluent Cloud リソースを管理する](manage.md)
