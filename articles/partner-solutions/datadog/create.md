---
title: Datadog を作成する - Azure パートナー ソリューション
description: この記事では、Azure portal を使用して Datadog のインスタンスを作成する方法について説明します。
ms.service: partner-services
ms.topic: quickstart
ms.date: 02/19/2021
author: tfitzmac
ms.author: tomfitz
ms.custom: references_regions
ms.openlocfilehash: 7af8b82c5da6c60527b45b6e8e292b9f067016ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101747587"
---
# <a name="quickstart-get-started-with-datadog"></a>クイックスタート: Datadog の使用を開始する

このクイックスタートでは、Datadog のインスタンスを作成します。 新しい Datadog 組織を作成することも、既存の Datadog 組織にリンクすることもできます。

## <a name="pre-requisites"></a>前提条件

Azure Datadog 統合を設定するには、Azure サブスクリプションに対する **所有者** アクセス権が必要です。 設定を開始する前に、適切なアクセス権があることをご確認ください。

Datadog リソース内で Security Assertion Markup Language (SAML) シングル サインオン (SSO) 機能を使用するには、エンタープライズ アプリケーションを設定する必要があります。 エンタープライズ アプリケーションを追加するには、グローバル管理者、クラウド アプリケーション管理者、アプリケーション管理者、サービス プリンシパル所有者のいずれかのロールが必要です。

エンタープライズ アプリケーションを設定するには、次の手順を実行します。

1. [Azure ポータル](https://portal.azure.com)に移動します。 **[Azure Active Directory]** を選択します。
1. 左側のウィンドウで、**[エンタープライズ アプリケーション]** を選択します。
1. **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** で、「*Datadog*」を検索します。 検索結果を選択し、 **[追加]** を選択します。

   :::image type="content" source="media/create/datadog-azure-ad-app-gallery.png" alt-text="AAD エンタープライズ ギャラリー内の Datadog アプリケーション。" border="true":::

1. アプリが作成されたら、サイド パネルからプロパティに移動します。 **[ユーザーの割り当てが必要ですか?]** を **[いいえ]** に設定し、 **[保存]** を選択します。

   :::image type="content" source="media/create/user-assignment-required.png" alt-text="Datadog アプリケーションのプロパティを設定する" border="true":::

1. サイド パネルから **[シングル サインオン]** に移動します。 **[SAML]** を選択します。

   :::image type="content" source="media/create/saml-sso.png" alt-text="SAML 認証。" border="true":::

1. **シングル サインオン設定を保存** するかどうかを確認するメッセージが表示されたら、 **[はい]** を選択します。

   :::image type="content" source="media/create/save-sso.png" alt-text="Datadog アプリのシングル サインオンを保存する" border="true":::

1. これで、シングル サインオンの設定が完了しました。

## <a name="find-offer"></a>プランを探す

Datadog を検索するには、Azure portal を使用します。

1. [Azure Portal](https://portal.azure.com/) にアクセスしてサインインします。

1. 最近のセッションで **Marketplace** にアクセスした場合は、表示されているオプションからそのアイコンを選択します。 それ以外の場合は、_Marketplace_ を検索します。

    :::image type="content" source="media/create/marketplace.png" alt-text="Marketplace アイコン。":::

1. Marketplace で、「**Datadog**」を検索します。

1. プランの概要画面で、 **[Set up + subscribe]\(設定 + 登録\)** を選択します。

   :::image type="content" source="media/create/datadog-app.png" alt-text="Azure Marketplace 内の Datadog アプリケーション。":::

## <a name="create-a-datadog-resource-in-azure"></a>Azure に Datadog リソースを作成する

Datadog リソースを作成するためのフォームがポータルに表示されます。

:::image type="content" source="media/create/datadog-create-resource.png" alt-text="[Create Datadog resource]\(Datadog リソースの作成\)" border="true":::

次の値を指定します。

|プロパティ | 説明
|:-----------|:-------- |
| サブスクリプション | Datadog リソースの作成に使用する Azure サブスクリプションを選択します。 所有者アクセス権が必要です。 |
| リソース グループ | 新しいリソース グループを作成するか、既存のリソース グループを使用するかを指定します。 [リソース グループ](../../azure-resource-manager/management/overview.md#resource-groups)は、Azure ソリューション用に関連するリソースを保持するコンテナーです。 |
| リソース名 | Datadog リソースの名前を指定します。 新しい Datadog 組織を作成する場合は、この名前が新しい Datadog 組織の名前となります。 |
| 場所 | [米国西部 2] を選択します。 現在、サポートされているリージョンは、米国西部 2 のみです。 |
| Datadog 組織 | 新しい Datadog 組織を作成するには、 **[新規]** を選択します。 既存の Datadog 組織にリンクするには、 **[既存]** を選択します。 |
| 料金プラン | 新しい組織を作成する場合は、利用可能な Datadog プランの一覧から選択します。 |
| [請求期間] | 月単位。 |

既存の Datadog 組織にリンクする場合は、次のセクションを参照してください。 それ以外の場合は、 **[Next: Metrics and logs]\(次へ: メトリックとログ\)** を選択し、次のセクションをスキップします。

## <a name="link-to-existing-datadog-organization"></a>既存の Datadog 組織にリンクする

Azure 内の新しい Datadog リソースを既存の Datadog 組織にリンクすることができます。

[データ組織] で **[既存]** を選択し、 **[Link to Datadog org]\(Datadog 組織にリンク\)** を選択します。

:::image type="content" source="media/create/link-to-existing.png" alt-text="既存の Datadog 組織にリンクする。" border="true":::

このリンクを通じて、Datadog 認証ウィンドウが開きます。 Datadog にサインインします。

既定では、Azure によって現在の Datadog 組織が自分の Datadog リソースにリンクされます。 別の組織にリンクする場合は、次に示すように、認証ウィンドウで適切な組織を選択します。

:::image type="content" source="media/create/select-datadog-organization.png" alt-text="リンクする適切な Datadog 組織を選択する" border="true":::

## <a name="configure-metrics-and-logs"></a>メトリックとログを構成する

Azure リソース タグを使用して、どのメトリックとログを Datadog に送信するかを構成します。 特定のリソースのメトリックとログを含めたり除外したりすることができます。

**メトリック** を送信するためのタグ ルールは次のとおりです。

- 既定では、仮想マシン、仮想マシン スケール セット、および App Service プランを除く、すべてのリソースのメトリックが収集されます。
- *Include* タグが適用された仮想マシン、仮想マシン スケール セット、および App Service プランの場合は、メトリックが Datadog に送信されます。
- *Exclude* タグが適用された仮想マシン、仮想マシン スケール セット、および App Service プランの場合、メトリックは Datadog に送信されません。
- 含めるルールと除外するルールの間に競合がある場合は、除外するルールが優先されます

**ログ** を送信するためのタグ ルールは次のとおりです。

- 既定では、すべてのリソースのログが収集されます。
- *Include* タグが適用された Azure リソースの場合は、ログが Datadog に送信されます。
- *Exclude* タグが適用された Azure リソースの場合、ログは Datadog に送信されません。
- 含めるルールと除外するルールの間に競合がある場合は、除外するルールが優先されます。

たとえば、次のスクリーンショットは、*Datadog = True* とタグ付けされた仮想マシン、仮想マシン スケール セット、および App Service プランでのみ、Datadog にメトリックを送信するタグ ルールを示しています。

:::image type="content" source="media/create/config-metrics-logs.png" alt-text="ログとメトリックを構成する" border="true":::

Azure から Datadog に出力できるログには 2 種類あります。

1. **サブスクリプション レベルのログ** - [コントロール プレーン](../../azure-resource-manager/management/control-plane-and-data-plane.md)でのリソースの操作に関する分析情報を提供します。 サービス正常性イベントの更新も含まれます。 アクティビティ ログを使用すると、いつ、だれが、どのような書き込み操作 (PUT、POST、DELETE) をしたかを確認できます。 Azure サブスクリプションごとに 1 つのアクティビティ ログがあります。

1. **Azure リソース ログ** - [データ プレーン](../../azure-resource-manager/management/control-plane-and-data-plane.md)で Azure リソースに対して行われた操作に関する分析情報を提供します。 たとえば、キー コンテナーからシークレットを取得することは、データ プレーン操作です。 また、データベースに対して要求を行うことも、データ プレーン操作です。 リソース ログの内容は、Azure サービスとリソースの種類によって異なります。

サブスクリプション レベルのログを Datadog に送信するには、 **[Send subscription activity logs]\(サブスクリプションのアクティビティ ログを送信する\)** を選択します。 このオプションをオフのままにすると、サブスクリプション レベルのログは Datadog に送信されません。

Azure リソース ログを Datadog に送信するには、 **[Send Azure resource logs for all defined resources]\(すべての定義済みリソースについて Azure リソース ログを送信する\)** を選択します。 Azure リソース ログの種類は、[[Azure Monitor Resource Log categories]\(Azure Monitor リソース ログのカテゴリ\)](../../azure-monitor/essentials/resource-logs-categories.md) に一覧表示されます。  Datadog にログを送信する Azure リソースのセットをフィルター処理するには、Azure リソース タグを使用します。  

Datadog に送信されるログには、Azure によって課金されます。 詳細については、Azure Marketplace パートナーに送信される[プラットフォーム ログの価格](https://azure.microsoft.com/pricing/details/monitor/)をご確認ください。

メトリックとログの構成が完了したら、 **[Next: Single sign-on]\(次へ: シングル サインオン\)** を選択します。

## <a name="configure-single-sign-on"></a>Configure single sign-on

組織で Azure Active Directory を ID プロバイダーとして使用している場合は、Azure portal から Datadog へのシングル サインオンを確立できます。 組織で別の ID プロバイダーを使用している場合、またはこの時点でシングル サインオンを確立しない場合は、このセクションをスキップできます。

Datadog リソースを既存の Datadog 組織にリンクする場合は、この手順でシングル サインオンを設定することはできません。 代わりに、Datadog リソースを作成した後でシングル サインオンを設定します。 詳細については、「[シングル サインオンを再構成する](manage.md#reconfigure-single-sign-on)」を参照してください。

:::image type="content" source="media/create/linking-sso.png" alt-text="既存の Datadog 組織にリンクする場合のシングル サインオン。" border="true":::

Azure Active Directory でシングル サインオンを確立するには、 **[Azure Active Directory を使用したシングル サインオンを有効にする]** のチェック ボックスをオンにします。

Azure portal によって、Azure Active Directory から適切な Datadog アプリケーションが取得されます。 このアプリは、前の手順で指定したエンタープライズ アプリと一致します。

Datadog アプリの名前を選択します。

:::image type="content" source="media/create/sso.png" alt-text="Datadog へのシングル サインオンを有効にする。" border="true":::

**タグ** を選択します。

## <a name="add-custom-tags"></a>カスタム タグを追加する

新しい Datadog リソースにカスタム タグを指定できます。 Datadog リソースに適用するタグの名前と値のペアを指定します。

:::image type="content" source="media/create/tags.png" alt-text="Datadog リソースにカスタム タグを追加する。" border="true":::

タグの追加が完了したら、 **[次へ: レビューと作成]** を選択します。

## <a name="review--create-datadog-resource"></a>Datadog リソースを確認および作成する

選択内容と利用規約を確認します。 検証が完了した後、 **[作成]** を選択します。

:::image type="content" source="media/create/review-create.png" alt-text="Datadog リソースを確認および作成する。" border="true":::

Azure によって Datadog リソースがデプロイされます。

プロセスが完了したら、 **[リソースに移動]** を選択して、Datadog リソースを表示します。

:::image type="content" source="media/create/go-to-resource.png" alt-text="Datadog リソースのデプロイ。" border="true":::

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Datadog リソースを管理する](manage.md)
