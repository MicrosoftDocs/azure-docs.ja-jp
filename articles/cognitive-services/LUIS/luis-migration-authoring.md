---
title: Azure リソース オーサリング キーに移行する
titleSuffix: Azure Cognitive Services
description: この記事では、Language Understanding (LUIS) オーサリング認証を電子メール アカウントから Azure リソースに移行する方法について説明します。
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.custom: seodec18, contperf-fy21q2
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 12/14/2020
ms.openlocfilehash: 3ff48ff5a3f46d8ec0fbf81b4cd20d20c217344b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98787639"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Azure リソース オーサリング キーに移行する

> [!IMPORTANT]
>  12 月 3 日以降、既存の LUIS ユーザーが LUIS アプリケーションのオーサリングを続行するには、移行プロセスを完了する必要があります。

Language Understanding (LUIS) のオーサリング認証が、メール アカウントから Azure リソースに変更されました。 ご使用のアカウントの移行がまだ済んでいない場合は、この記事で移行の方法をご確認ください。  


## <a name="what-is-migration"></a>移行とは。

移行とは、オーサリング認証をメール アカウントから Azure リソースに変更するプロセスです。 移行後、お使いのアカウントは Azure サブスクリプションと Azure オーサリング リソースにリンクされます。

移行は、[LUIS ポータル](https://www.luis.ai)から実行する必要があります。 たとえば、LUIS CLI を使用してオーサリング キーを作成する場合、移行プロセスは LUIS ポータルで完了する必要があります。 移行後もアプリケーションに共同作成者を指定することはできますが、これらはアプリケーション レベルではなく Azure リソース レベルで追加されます。 アカウントの移行を元に戻すことはできません。

> [!Note]
> * 予測ランタイム リソースを作成する必要がある場合は、それを作成するための[別のプロセス](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal)があります。
> * アプリケーションと共同作成者に対する影響に関する詳細については、次の「[移行に関する注意事項](#migration-notes)」セクションを参照してください。 
> * LUIS アプリの作成は無料 (F0 レベル) です。 価格レベルの詳細については[こちら](luis-limits.md#key-limits)をご覧ください。

## <a name="migration-prerequisites"></a>移行の前提条件

* 有効な Azure サブスクリプション。 サブスクリプションに追加してもらうようテナント管理者に依頼するか、[無料でサインアップ](https://azure.microsoft.com/free/cognitive-services)してください。
* LUIS ポータルまたは [Azure portal](https://portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) の LUIS Azure オーサリング リソース。 
    * LUIS ポータルからオーサリング リソースを作成することは、次のセクションで説明されている移行プロセスの一部です。
* アプリケーションのコラボレーターの場合、アプリケーションは自動的には移行されません。 移行フローの実行中に、これらのアプリをエクスポートするように求めるメッセージが表示されます。 また、[エクスポート API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) を使用することもできます。 移行後に、アプリを LUIS に再度インポートすることができます。 インポート プロセスにより、新しいアプリ ID を使用して新しいアプリが作成され、自身がその所有者になります。        
* アプリケーションの所有者である場合、アプリは自動的に移行されるため、これらをエクスポートする必要はありません。 アプリケーションごとにすべてのコラボレーターの一覧が記載されたメール テンプレートが提供されるため、各自は移行プロセスの通知を受けることができます。

## <a name="migration-steps"></a>移行の手順

1. [LUIS ポータル](https://www.luis.ai)にサインインすると、Azure 移行ウィンドウが開き、移行の手順が表示されます。 これを無視すると、LUIS アプリケーションのオーサリングに進むことができなくなります。表示される唯一の操作は、移行を続行することです。

    > [!div class="mx-imgBorder"]
    > ![移行の紹介ウィンドウ](./media/migrate-authoring-key/notify-azure-migration.png)

2. いずれかのアプリにコラボレーターがいる場合は、自分が所有しているアプリケーション名の一覧が、各アプリケーションのオーサリング リージョンおよびコラボレーターのメールと共に表示されます。 アプリケーション名の左側にある **[送信]** 記号ボタンをクリックして、移行について通知するメールをコラボレーターに送信することをお勧めします。
アプリケーションに割り当てられた予測リソースをコラボレーターが所有している場合は、アプリケーション名の横に `*` 記号が表示されます。 移行後も、これらのアプリにはこれらの予測リソースが割り当てられていますが、コラボレーターにはアプリケーションをオーサリングするためのアクセス権がありません。 ただし、予測リソースの所有者が Azure portal から[キーを再生成](./luis-how-to-azure-subscription.md#regenerate-an-azure-key)した場合、この割り当ては解除されます。  

   > [!div class="mx-imgBorder"]
   > ![コラボレーターへの通知](./media/migrate-authoring-key/notify-azure-migration-collabs.png)


   コラボレーターとアプリごとに、既定のメール アプリケーションが開き、簡単な書式が設定されたメールが表示されます。 このメールは送信前に編集できます。 メール テンプレートには、正確なアプリ ID とアプリ名が含まれています。

   ```html
   Dear Sir/Madam,

   I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

   App Id: <app-ID-omitted>
   App name: Human Resources

   Thank you
   ```
   > [!Note]
   > アカウントを Azure に移行すると、コラボレーターはアプリを利用できなくなります。

3. 自分がいずれかのアプリのコラボレーターである場合は、自分が共有先となっているアプリケーション名の一覧が、各アプリケーションのオーサリング リージョンおよび所有者のメールと共に表示されます。 アプリケーション名の左側にあるエクスポート ボタンをクリックして、アプリのコピーをエクスポートすることをお勧めします。 これらのアプリは、自動的には移行されないため、移行後に再びインポートすることができます。
アプリケーションに割り当てられた予測リソースを自分が所有している場合は、アプリケーション名の横に `*` 記号が表示されます。 移行後も、自分の予測リソースはこれらのアプリケーションに割り当てられていますが、これらのアプリをオーサリングするためのアクセス権はなくなります。 予測リソースとアプリケーションの間の割り当てを解除する場合は、Azure portal にアクセスして、[キーを再生成する](./luis-how-to-azure-subscription.md#regenerate-an-azure-key)必要があります。

   > [!div class="mx-imgBorder"]
   > ![アプリケーションのエクスポート。](./media/migrate-authoring-key/migration-export-apps.png)


4. リージョンを移行するためのウィンドウでは、オーサリングしたのと同じリージョンの Azure リソースにアプリケーションを移行するように求められます。 LUIS には 3 つのオーサリング リージョン[とポータル](./luis-reference-regions.md#luis-authoring-regions)があります。 このウィンドウには、所有しているアプリケーションがオーサリングされたリージョンが表示されます。 表示される移行リージョンは、使用するリージョン ポータルとオーサリングしたアプリに応じて異なる場合があります。 

   > [!div class="mx-imgBorder"]
   > ![複数リージョンへの移行。](./media/migrate-authoring-key/migration-regional-flow.png)

5. 各リージョンについて、新しい LUIS オーサリング リソースを作成するか、既存のものに移行するかをボタンを使用して選択します。

   > [!div class="mx-imgBorder"]
   > ![オーサリング リソースを作成するか、既存のものにするかを選択する](./media/migrate-authoring-key/migration-multiregional-resource.png)

   次の情報を指定します。

   * **[テナント名]** : Azure サブスクリプションが関連付けられているテナント。 既定では、これは現在使用しているテナントに設定されます。 テナントを切り替えるには、このウィンドウを閉じて、画面の右上にあるイニシャルの含まれたアバターを選択してください。 **[Azure に移行する]** をクリックして、ウィンドウを再び開きます。
   * **Azure サブスクリプション名**:リソースに関連付けられるサブスクリプション。 テナントに属しているサブスクリプションが複数ある場合は、ドロップダウン リストから目的のサブスクリプションを選択します。
   * **オーサリング リソース名**:選択したカスタム名。 オーサリングおよび予測エンドポイント クエリの URL の一部として使用されます。 新しいオーサリング リソースを作成している場合は、リソース名に英数字と `-` のみが含まれていること、および先頭または末尾に `-` を使用していないことを確認してください。 名前に他の記号が含まれている場合は、リソースの作成と移行に失敗します。
   * **[Azure リソース グループ名]** : ドロップダウン リストから選択したカスタム リソース グループの名前。 リソース グループを使用すると、アクセスと管理のために Azure リソースをグループ化できます。 現在サブスクリプション内にリソース グループがない場合、LUIS ポータルでそれを作成することはできません。 [Azure portal](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup) にアクセスしてそれを作成してから、LUIS にアクセスしてサインイン プロセスを続行します。

6. すべてのリージョンで移行が完了したら、[完了] をクリックします。 これで、アプリケーションにアクセスできるようになります。 ポータル内のすべてのリージョンで、すべてのアプリケーションのオーサリングと保守を続けることができます。

## <a name="migration-notes"></a>移行に関する注意事項

* 移行前は、共同作成者は LUIS アプリ レベルで "_コラボレーター_" と呼ばれます。 移行後は、同じ機能に対して、Azure リソース レベルで "_共同作成者_" の Azure ロールが使用されます。
* 複数の [LUIS リージョン ポータル](./luis-reference-regions.md#luis-authoring-regions)にサインインしている場合は、一度に複数のリージョンで移行するように求められます。
* アプリケーションの所有者である場合、アプリケーションは一緒に自動的に移行されます。 アプリケーションのコラボレーターである場合、アプリケーションは一緒に移行されません。 ただし、コラボレーターは、必要なアプリをエクスポートするように求められます。
* アプリケーションの所有者は、移行するアプリのサブセットを選択することができません。また、コラボレーターが移行したかどうかを、所有者が知る方法はありません。
* 移行によって自動的にコラボレーターが Azure オーサリング リソースに移動されたり、追加されたりすることはありません。 アプリの所有者が、移行後にこの手順を実行する必要があります。 この手順では、[Azure オーサリング リソースへのアクセス許可](./luis-how-to-collaborate.md)が必要です。
* 共同作成者は、Azure リソースに割り当てられたら、アプリケーションにアクセスする前に移行を済ませておく必要があります。 そうしないと、アプリケーションをオーサリングするためのアクセス権が付与されません。


## <a name="using-apps-after-migration"></a>移行後のアプリの使用

移行プロセスが完了すると、自分が所有しているすべての LUIS アプリが、1 つの LUIS オーサリング リソースに割り当てられるようになります。
**[マイ アプリ]** の一覧に、新しいオーサリング リソースに移行されたアプリが表示されます。 アプリにアクセスする前に **[別のオーサリング リソースを選択]** を選択すると、サブスクリプションとオーサリング リソースを選択して、オーサリングできるアプリを表示できます。

> [!div class="mx-imgBorder"]
> ![サブスクリプションとオーサリング リソースを選択する](./media/migrate-authoring-key/select-sub-and-resource.png)


プログラムによってアプリを編集する場合は、オーサリング キーの値が必要です。 これらの値を表示するには、LUIS ポータルの画面上部にある **[管理]** をクリックしてから **[Azure リソース]** を選択します。 これらは、Azure portal にあるそのリソースの **[キーとエンドポイント]** ページでも確認できます。 さらに多くのオーサリング リソースを作成し、同じページから割り当てることもできます。

## <a name="adding-contributors-to-authoring-resources"></a>オーサリング リソースへの共同作成者の追加

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

オーサリング リソースに[共同作成者を追加する方法](luis-how-to-collaborate.md)について説明します。 共同作成者は、そのリソースのすべてのアプリケーションにアクセスできます。

**Azure portal** のオーサリング リソースの [Access Control (IAM)] ページから、そのリソースの共同作成者を追加できます。 詳細については、「[アプリに共同作成者を追加する](luis-how-to-collaborate.md)」を参照してください。

> [!Note]
> LUIS アプリの所有者が移行し、Azure リソースの共同作成者としてコラボレーターを追加した場合、コラボレーターも移行しないと、アプリにアクセスできません。

## <a name="troubleshooting-the-migration-process"></a>移行プロセスに関するトラブルシューティング

ドロップダウン リストに Azure サブスクリプションが見つからない場合:
* Cognitive Services リソースの作成が承認されている有効な Azure サブスクリプションがあることを確認します。 [Azure portal](https://ms.portal.azure.com) にアクセスして、サブスクリプションの状態を確認します。 お持ちでない場合は、[無料の Azure アカウントを作成](https://azure.microsoft.com/free/cognitive-services/)してください。
* 有効なサブスクリプションに関連付けられている、適切なテナント内であることを確認します。 テナントを切り替えるには、画面の右上にあるイニシャルの含まれたアバターを選択してください。

  > [!div class="mx-imgBorder"]
  > ![ディレクトリを切り替えるためのページ](./media/migrate-authoring-key/switch-directories.png)

既存のオーサリング リソースがあるのに、 **[既存のオーサリング リソースを使用する]** オプションを選択しても見つからない場合:
* そのリソースは、移行しようとしているリージョンとは異なるリージョンに作成されたことが考えられます。
* 代わりに、LUIS ポータルから新しいリソースを作成します。

**[新しいオーサリング リソースを作成する]** オプションを選択したが、"Failed retrieving user's Azure information, retry again later" (ユーザーの Azure 情報を取得できませんでした。後でもう一度やり直してください) というエラー メッセージが表示されて移行が失敗した場合:
* サブスクリプションには、サブスクリプションあたりのリージョンごとに 10 個以上のオーサリング リソースを含めることができます。 この場合、新しいオーサリング リソースを作成することはできません。
* **[既存のオーサリング リソースを使用する]** オプションを選択し、ご利用のサブスクリプションにある既存のリソースのいずれかを選択して移行します。

## <a name="create-new-support-request"></a>新しいサポート リクエストの作成

移行に関連してトラブルシューティングのセクションで扱われていない問題が発生した場合は、[サポート トピックを作成](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)し、次のフィールドに以下の情報を入力してください。

   * **問題の種類**: 技術
   * **サブスクリプション**:ドロップダウン リストからサブスクリプションを選びます
   * **サービス**:検索して "Cognitive Services" を選択します
   * **リソース**:既存のものがある場合は、LUIS リソースを選択します。 ない場合は、[全般的な質問] を選択します。

## <a name="next-steps"></a>次のステップ

* [オーサリングおよびランタイム キーの概念](luis-how-to-azure-subscription.md)について確認する
* [キーを割り当てる](luis-how-to-azure-subscription.md)方法と[共同作成者を追加](luis-how-to-collaborate.md)する方法について確認する