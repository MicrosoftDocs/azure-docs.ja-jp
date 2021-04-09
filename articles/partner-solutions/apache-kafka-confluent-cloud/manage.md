---
title: Confluent Cloud を管理する - Azure パートナー ソリューション
description: この記事では、Azure portal での Confluent Cloud の管理について説明します。 シングル サインオンをセットアップし、Confluent 組織を削除し、サポートを受ける方法。
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/08/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: f8a54096ecda4729f7070120a02be3055f933cea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99989114"
---
# <a name="manage-the-confluent-cloud-resource"></a>Confluent Cloud リソースを管理する

この記事では、Azure 上で Apache Kafka for Confluent Cloud のインスタンスを管理する方法について説明します。 シングル サインオン (SSO) の設定、Confluent 組織の削除、およびサポート要求の作成方法について説明します。

## <a name="single-sign-on"></a>シングル サインオン

組織に SSO を実装するために、テナント管理者はギャラリー アプリケーションをインポートできます。 この手順は省略可能です。 アプリケーションをインポートする方法については、「[クイックスタート:Azure Active Directory (Azure AD) テナントにアプリケーションを追加する](../../active-directory/manage-apps/add-application-portal.md)」を参照してください。 テナント管理者がアプリケーションをインポートするとき、ユーザーは、Confluent Cloud ポータルへのアクセスを許可することに明示的に同意する必要はありません。

SSO を有効にするには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. Confluent Cloud リソースのインスタンスの **[概要]** に移動します。
1. **[Manage on Confluent Cloud]\(Confluent Cloud の管理\)** のリンクを選択します。

   :::image type="content" source="media/sso-link.png" alt-text="Confluent ポータルのシングル サインオン。":::

1. SSO への同意のためのギャラリー アプリケーションをテナント管理者がインポートしていない場合、アクセス許可と同意を付与します。 この手順は、 **[Manage on Confluent Cloud]\(Confluent Cloud の管理\)** のリンクに初めてアクセスするときにのみ必要です。
1. Confluent Cloud ポータルにシングル サインオンするための Azure AD アカウントを選択します。
1. 同意を指定すると、Confluent Cloud ポータルにリダイレクトされます。

## <a name="set-up-cluster"></a>クラスターをセットアップする

クラスターのセットアップの詳細については、[Confluent Cloud でのクラスターの作成に関する Confluent のドキュメント](https://docs.confluent.io/cloud/current/clusters/create-cluster.html)を参照してください。

## <a name="delete-confluent-organization"></a>Confluent 組織を削除する

Confluent Cloud リソースが不要になった場合は、Azure と Confluent Cloud 内のリソースを削除します。

Azure 内のリソースを削除するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[すべてのリソース]** を選択し、Confluent Cloud リソースの名前または **リソースの種類** "_Confluent 組織_" によって **フィルター処理** します。 または Azure portal で、リソース名を検索します。
1. リソースの **[概要]** で、 **[削除]** を選択します。

    :::image type="content" source="media/delete-resources-icon.png" alt-text="リソースの削除アイコン。":::

1. 削除を確認するには、リソース名を入力して **[削除]** を選択します。

    :::image type="content" source="media/delete-resources-prompt.png" alt-text="リソースの削除の確認を求めるメッセージ。":::

Confluent Cloud 上のリソースを削除するには、[Confluent Cloud 環境に関する Confluent のドキュメント](https://docs.confluent.io/current/cloud/using/environments.html)と [Confluent Cloud の基礎に関する Confluent のドキュメント](https://docs.confluent.io/current/cloud/using/cloud-basics.html)を参照してください。

クラスターとクラスター内のすべてのデータが完全に削除されます。 契約にデータ保持条項が含まれている場合、Confluent は[サービス使用条件に関する Confluent のドキュメント](https://www.confluent.io/confluent-cloud-tos)に指定されている期間、データを保持します。

クラスターを削除した時点までの日割りの使用量に対して課金されます。 クラスターが完全に削除されると、Confluent から確認用の電子メールが送信されます。

## <a name="get-support"></a>サポートを受ける

サポート要求を Confluent に送信するには、次に示すように [Confluent サポート](https://support.confluent.io)に問い合わせるか、ポータルから要求を送信します。

> [!NOTE]
> 初めて使用するユーザーの場合、Confluent サポート ポータルにサインインする前にパスワードをリセットします。 Confluent Cloud のアカウントを持っていない場合は、`cloud-support@confluent.io` にメールを送信して、支援を受けてください。

ポータルでは、Azure の [ヘルプとサポート] を通じて要求を送信することも、Azure 上の Apache Kafka for Confluent Cloud のインスタンスから直接それを送信することもできます。

Azure の [ヘルプとサポート] を通じて要求を送信するには、次のようにします。

1. **[ヘルプとサポート]** を選択します。
1. **[サポート リクエストの作成]** を選択します。
1. フォーム内で、 **[問題の種類]** として **[技術]** を選択します。 サブスクリプションを選択します。 サービスの一覧で、 **[Confluent on Azure]\(Azure での Confluent\)** を選択します。

    :::image type="content" source="media/support-request-help.png" alt-text="ヘルプからサポート リクエストを作成する。":::

リソースから要求を送信するには、次の手順を実行します。

1. Azure portal で、Confluent 組織を選択します。
1. 画面の左側にあるメニューから、 **[新しいサポート リクエスト]** を選択します。
1. サポート リクエストを作成するには、**Confluent ポータル** へのリンクを選択します。

    :::image type="content" source="media/support-request.png" alt-text="インスタンスからサポート リクエストを作成する。":::

## <a name="next-steps"></a>次の手順

トラブルシューティングの支援については、「[Apache Kafka for Confluent Cloud ソリューションのトラブルシューティング](troubleshoot.md)」を参照してください。
