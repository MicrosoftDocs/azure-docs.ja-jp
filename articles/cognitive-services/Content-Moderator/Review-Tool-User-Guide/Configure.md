---
title: レビュー ツールの設定を構成する - Content Moderator
titleSuffix: Azure Cognitive Services
description: Content Moderator 用のチーム、タグ、コネクタ、ワークフロー、および資格情報を構成または取得するには、レビュー ツールを使用します。
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pafarley
ms.openlocfilehash: 8812fd1e6c1efb2aa44c77573bc4b8f1c099834d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "92912023"
---
# <a name="configure-the-review-tool"></a>レビュー ツールの構成

[レビュー ツール](https://contentmoderator.cognitive.microsoft.com)には、ダッシュボードの **[設定]** メニュー経由でアクセスできるいくつかの重要な機能があります。

![Content Moderator レビュー ツールの [設定] メニュー](images/settings-1.png)

## <a name="manage-team-and-subteams"></a>チームとサブチームを管理する

**[チーム]** タブを使用すると、特定の [目視レビュー](../review-api.md#reviews)が開始されたときに通知できるチームとサブチーム (ユーザーのグループ) を管理できます。 作成できるチームは (レビュー ツールへのサインアップ時に作成する) 1 つだけですが、複数のサブチームを作成できます。 チーム管理者はメンバーを招待し、そのアクセス許可を設定し、そのメンバーを異なるサブチームに割り当てることができます。

![レビュー ツールのチームの設定](images/settings-2-team.png)

サブチームは、エスカレーション チームまたはコンテンツの特定のカテゴリのレビュー専用のチームの作成に役立ちます。 たとえば、成人向けコンテンツをさらにレビューするために別のチームに送信できます。

このセクションでは、サブチームを作成し、その場でレビューをすばやく割り当てる方法について説明します。 ただし、[ワークフロー](workflows.md)を使用して、特定の条件に基づいてレビューを割り当てることができます。

### <a name="create-a-subteam"></a>サブチームを作成する

**[Subteams]\(サブチーム)** セクションに移動し、 **[サブチームの追加]** をクリックします。 ダイアログにサブチーム名を入力し、 **[保存]** をクリックします。

![サブチーム名](images/1-Teams-2.PNG)

#### <a name="invite-teammates"></a>チームメイトを招待する

まだ既定のチームのメンバーではないユーザーをサブチームに割り当てることはできないため、まず既定のチームにレビュー担当者を追加する必要があります。 **[チーム]** タブで **[Invite]\(招待)** をクリックします。

![ユーザーの招待](images/invite-users.png)

#### <a name="assign-teammates-to-subteam"></a>サブチームにチームメイトを割り当てる

**[メンバーの追加]** ボタンをクリックして、既定のチームから 1 つ以上のサブチームにメンバーを割り当てます。 サブチームには既存のユーザーのみを追加することができます。 レビュー ツールに含まれていない新しいユーザーを追加するには、[チーム設定] ページで、[招待] ボタンを使用して、それらのユーザーを招待します。

![サブチーム メンバーの割り当て](images/1-Teams-3.PNG)

### <a name="assign-reviews-to-subteams"></a>サブチームにレビューを割り当てる

サブチームを作成し、メンバーを割り当てたら、これらのサブチームへのコンテンツ [レビュー](../review-api.md#reviews)の割り当てを開始できます。 これは、サイトの **[レビュー]** タブから実行されます。
サブチームにコンテンツを割り当てるには、右上隅にある省略記号をクリックし、 **[Move to]\(移動先)** を選択してからサブチームを選択します。

![画像のレビューをサブチームに割り当てる](images/3-review-image-subteam-1.png)

### <a name="switch-between-subteams"></a>サブチーム間の切り替え

複数のサブチームのメンバーである場合は、これらのサブチームを切り替えて、表示されるコンテンツ レビューを変更できます。 **[レビュー]** タブで、 **[既定値]** というラベルの付いたドロップダウン メニューを選択し、 **[Choose Subteam]\(サブチームの選択)** を選択します。 自分がメンバーになっているサブチームのコンテンツ レビューのみを表示できます。

![サブチーム間の切り替え](images/3-review-image-subteam-2.png)

## <a name="tags"></a>Tags

**[Tags]\(タグ)** タブを使用すると、2 つの既定のモデレーション タグである **isadult** (**a**) と **isracy** (**r**) に加えて、カスタム モデレーション タグを定義できます。 カスタム タグを作成すると、それがレビューで既定のタグと共に使用可能になります。 各タグの可視性の設定を切り替えることにより、レビューで表示されるタグを変更できます。

![[Is visible]\(表示) チェックボックスを含む [タグ] ビュー](images/tags-4-disable.png)

### <a name="create-custom-tags"></a>カスタム タグを作成する

新しいタグを作成するには、各フィールドに短いコード、名前、および説明を入力する必要があります。

- **[Short code]\(短いコード)** : タグの 2 文字のコードを入力します。 例: **cb**。
- **Name**:短く、わかりやすいタグ名をスペースなしの小文字で入力します。 例: **isbullying**。
- **[説明]** : (省略可能) そのタグの対象とするコンテンツの種類の説明を入力します。 例:**ネットいじめの説明または例**。

**[追加]** をクリックしてタグを追加し、タグの作成を完了したら **[保存]** をクリックします。

![レビュー ツールの新しいタグの作成ダイアログ](images/settings-3-tags.png)

### <a name="delete-tags"></a>タグを削除する

[タグ] 一覧の各エントリの横にあるごみ箱アイコンを選択することによってカスタム タグを削除できますが、既定のタグは削除できません。

## <a name="connectors"></a>Connectors

**[コネクタ]** タブを使用すると、コンテンツ [ワークフロー](../review-api.md#workflows)の一部としてさまざまな方法でコンテンツを処理できるサービス固有のプラグインであるコネクタを管理できます。

ワークフローを作成するときの既定のコネクタは、コンテンツを **adult** または **racy** としてマークしたり、不適切な表現を見つけたりできる Content Moderator コネクタです。 ただし、対応するサービスの資格情報を持っていれば、次に一覧表示されている他のコネクタを使用できます (たとえば、Face コネクタを使用するには、[Face](../../face/overview.md) サブスクリプション キーを取得する必要があります)。

[レビュー ツール](./human-in-the-loop.md)には、次のコネクタが含まれています。

- Emotion
- Face
- PhotoDNA Cloud Service
- Text Analytics

### <a name="add-a-connector"></a>コネクタを追加する

コネクタを追加する (さらに、それをコンテンツ [ワークフロー](../review-api.md#workflows)で使用できるようにする) には、該当する **[接続]** ボタンを選択します。 次のダイアログで、そのサービスのサブスクリプション キーを入力します。 完了すると、新しいコネクタがページの上部に表示されます。

![Content Moderator のコネクタ設定](images/settings-4-connectors.png)

## <a name="workflows"></a>Workflows

**[workflows]\(ワークフロー)** タブを使用すると、[ワークフロー](../review-api.md#workflows)を管理できます。 ワークフローは、コンテンツ用のクラウド ベースのフィルターであり、コネクタと連携して動作してコンテンツをさまざまな方法で並べ替えたり、適切なアクションを実行したりします。 ここでは、ワークフローを定義、編集、およびテストできます。 これを行う方法に関するガイダンスについては、[ワークフローの定義および使用](Workflows.md)に関するページを参照してください。

![Content Moderator のワークフロー設定](images/settings-5-workflows.png)

## <a name="credentials"></a>資格情報

**[資格情報]** タブを使用すると、REST 呼び出しまたはクライアント SDK からいずれかのモデレーション サービスにアクセスするために必要な Content Moderator サブスクリプション キーにすばやくアクセスできます。

![Content Moderator 資格情報](images/settings-6-credentials.png)

### <a name="use-external-credentials-for-workflows"></a>ワークフローで外部の資格情報を使用する

[レビュー ツール](https://contentmoderator.cognitive.microsoft.com)は、サインアップ時に Azure Content Moderator サービスの無料試用版キーを生成しますが、それを Azure アカウントの既存のキーを使用するように構成することもできます。 無料試用版キーには厳密な使用制限 ([料金と制限](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/)) があるため、これは大規模なシナリオに推奨されます。

Azure で [Content Moderator リソース](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)を作成している場合は、Azure Portal でそのリソースに移動し、 **[キー]** ブレードを選択します。 いずれかのキーをコピーします。

![Azure portal での Content Moderator キー](images/credentials-azure-portal-keys.PNG)

[レビュー ツール](https://contentmoderator.cognitive.microsoft.com)の **[資格情報]** タブで、 **[Workflow Settings]\(ワークフロー設定)** ウィンドウに移動して **[編集]** を選択し、 **[Ocp-Apim-Subscription-Key]** フィールドにキーを貼り付けます。 これで、モデレーション API を呼び出すワークフローは Azure 資格情報を使用します。

> [!NOTE]
> **[Workflow Settings]\(ワークフロー設定)** ウィンドウ内の他の 2 つのフィールドは、カスタム用語とカスタム イメージの一覧のためです。 これらの詳細については、[カスタム用語](../try-terms-list-api.md)または[カスタム イメージ](../try-image-list-api.md)のガイドを参照してください。

### <a name="use-your-azure-account-with-the-review-apis"></a>レビュー API で Azure アカウントを使用する

レビュー API で Azure キーを使用するには、リソース ID を取得する必要があります。 Azure Portal で Content Moderator リソースに移動し、 **[プロパティ]** ブレードを選択します。 [リソース ID] の値をコピーし、それをレビュー ツールの **[資格情報]** タブの **[Whitelisted Resource Id(s)]\(ホワイトリスト リソース ID)** フィールドに貼り付けます。

![Azure portal での Content Moderator リソース ID](images/credentials-azure-portal-resourceid.PNG)

両方の場所にサブスクリプション キーを入力した場合、レビュー ツール アカウントに付属する試用版キーは使用されなくなりますが、引き続き使用できます。

## <a name="next-steps"></a>次のステップ

[レビュー ツールのクイック スタート](../quick-start.md)に従って、コンテンツ モデレーションのシナリオでレビュー ツールの使用を開始してください。