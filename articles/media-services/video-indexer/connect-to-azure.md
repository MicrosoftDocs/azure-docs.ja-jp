---
title: Azure Portal で Video Indexer アカウントを作成する
titlesuffix: Azure Media Services
description: この記事では、Azure Portal で Video Indexer アカウントを作成する方法について説明します。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 01/12/2019
ms.author: juliako
ms.openlocfilehash: affa6f9a808543401b7d57812c7d2bef4324a83c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59796547"
---
# <a name="create-a-video-indexer-account-connected-to-azure"></a>Azure に接続された Video Indexer アカウントを作成する

Video Indexer アカウントを作成する場合、無料試用アカウント (一定分数の無料インデックス作成を利用可能) または有料オプション (クォータによる制限がありません) を選択できます。 無料試用アカウントで Video Indexer 使用すると、Web サイト ユーザーは最大 600 分間の無料インデックス作成、API ユーザーは最大 2,400 分間の無料インデックス作成を利用できます。 有料オプションでは、Azure サブスクリプションと Azure Media Services アカウントに接続する Video Indexer アカウントを作成します。 Media アカウント関連の料金と同様に、インデックス作成時間 (分単位) の料金がかかります。 

この記事では、Azure サブスクリプションと Azure Media Services アカウントにリンクされた Video Indexer アカウントを作成する方法について説明します。 このトピックでは、自動 (既定) フローを使用して Azure に接続する手順について説明します。 また、手動で Azure に接続する方法 (上級) についても説明します。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。

    Azure サブスクリプションがまだない場合は、[Azure 無料試用版](https://azure.microsoft.com/free/)にサインアップしてください。

* Azure Active Directory (AD) ドメイン。

    Azure AD ドメインがない場合は、Azure サブスクリプションを使用して作成してください。 詳細については、「[Azure Active Directory のカスタム ドメイン名の管理](../../active-directory/users-groups-roles/domains-manage.md)」を参照してください。

* Azure AD ドメインのユーザーとメンバー。 このメンバーは、Video Indexer アカウントを Azure に接続するときに使用します。

    このユーザーは、outlook.com、live.com、hotmail.com などの個人用アカウントではなく、職場または学校アカウントを持つ Azure AD ユーザーである必要があります。

    ![すべての AAD ユーザー](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>自動フローのその他の前提条件

Azure AD ドメインのユーザーとメンバー。 このメンバーは、Video Indexer アカウントを Azure に接続するときに使用します。

このユーザーは、**所有者**ロール、または**共同作成者**ロールと**ユーザー アクセス管理者**ロールの両方が割り当てられた Azure サブスクリプションのメンバーである必要があります。 1 人のユーザーが 2 つのロールを持つ場合は 2 回追加できます。 共同作成者ロールで 1 回、ユーザー アクセス管理者ロールで 1 回です。

![アクセスの制御](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>手動フローのその他の前提条件

Azure portal を使用して EventGrid リソース プロバイダーを登録します。

[Azure portal](https://portal.azure.com/) で **[サブスクリプション]**、[subscription]、**[ResourceProviders]** に移動します。 

**Microsoft.Media** と **Microsoft.EventGrid** を探します。 "登録済み" 状態でない場合は、**[登録]** をクリックします。 登録には数分かかります。

![EventGrid](./media/create-account/event-grid.png)

## <a name="connect-to-azure"></a>Azure への接続

1. [Video Indexer](https://www.videoindexer.ai/) Web サイトに移動してサインインします。

2. **[アカウントの新規作成]** ボタンをクリックします。

    ![Azure への接続](./media/create-account/connect-to-azure.png)

3. サブスクリプション一覧が表示されたら、使用するサブスクリプションを選択します。

    ![Video Indexer を Azure に接続する](./media/create-account/connect-vi-to-azure-subscription.png)

4. サポートされている場所から Azure リージョンを選択します。米国西部 2、北ヨーロッパ、東アジアです。
5. **[Azure Media Services アカウント]** で、次のいずれかのオプションを選択します。

    * 新しい Media Services アカウントを作成するには、**[新規リソース グループの作成]** を選択します。 リソース グループの名前を入力します。

        新しい Azure Storage アカウントを含め、新しいアカウントが Azure のサブスクリプションに作成されます。 新しい Media Services アカウントには、1 つのストリーミング エンドポイントと 10 個の S3 予約ユニットという既定の初期構成があります。
    * 既存の Media Services アカウントを使用するには、**[既存のリソースの使用]** を選択します。 アカウント一覧からアカウントを選択します。

        Media Services アカウントのリージョンは Video Indexer アカウントと同じである必要があります。 

        > [!NOTE]
        > インデックス作成時間を最小限に抑え、スループットを低くするために、Media Services アカウントの[予約ユニット](../previous/media-services-scale-media-processing-overview.md )の種類と数を **10 個の S3 予約ユニット**に調整することを強くお勧めします。 [ポータルを使用して予約ユニットを変更する方法](../previous/media-services-portal-scale-media-processing.md)に関する記事を参照してください。

    * 接続を手動で構成するには、**[手動構成に切り替える]** リンクをクリックします。

        詳細については、後述する「[Azure に手動で接続する](#connect-to-azure-manually-advanced-option) (上級者向けオプション)」を参照してください。
6. 完了したら、**[接続]** を選択します。 この操作が完了するまでに数分かかる場合があります。 

    Azure への接続が完了すると、新しい Video Indexer アカウントがアカウント一覧に表示されます。

    ![新しいアカウント](./media/create-account/new-account.png)

7. 新しいアカウントを表示します。

## <a name="connect-to-azure-manually-advanced-option"></a>Azure に手動で接続する (上級者向けオプション)

Azure への接続に失敗する場合は、手動で接続して問題の解決を試みることができます。

> [!NOTE]
> Video Indexer アカウント、それと接続している Media Services アカウント、同じ Media Services アカウントに接続されている Azure ストレージ アカウントの 3 つのアカウントは、同じリージョン内に置くことを強くお勧めします。

### <a name="create-and-configure-a-media-services-account"></a>Media Services アカウントの作成と構成

1. [アカウントの作成](../previous/media-services-portal-create-account.md)に関するページに従って、[Azure](https://portal.azure.com/) portal を使用して Azure Media Services アカウントを作成します。

    Media Services アカウント用にストレージ アカウントを作成する場合は、アカウントの種類に **StorageV2**、レプリケーション フィールドに**地理冗長 (RGS)** を選択します。

    ![新しい AMS アカウント](./media/create-account/create-ams-account1.png)

    > [!NOTE]
    > Media Services リソースとアカウント名を書き留めてください。 次のセクションの手順で必要になります。

2. 作成した Media Services アカウントの[予約ユニット](../previous/media-services-scale-media-processing-overview.md )の種類と数を **10 個の S3 予約ユニット**に調整します。 [ポータルを使用して予約ユニットを変更する方法](../previous/media-services-portal-scale-media-processing.md)に関する記事を参照してください。
3. Video Indexer Web アプリケーションでビデオを再生するには、新しい Media Services アカウントの既定の**ストリーミング エンドポイント**を起動しておく必要があります。

    新しい Media Services アカウントで **[ストリーミング エンドポイント]** をクリックします。 ストリーミング エンドポイントを選択し、[開始] を押します。

    ![新しい AMS アカウント](./media/create-account/create-ams-account2.png)

4. Video Indexer が Media Services API を使用して認証するには、AD アプリケーションを作成する必要があります。 次に、「[Azure ポータルで Azure AD 認証を開始する](../previous/media-services-portal-get-started-with-aad.md)」で説明されている Azure AD 認証プロセスについて、手順を追って説明します。

    1. 新しい Media Services アカウントで **[API アクセス]** をクリックします。
    2. [サービス プリンシパルの認証方法](../previous/media-services-portal-get-started-with-aad.md#service-principal-authentication)を選択します。
    3. 「[クライアント ID とクライアント シークレットを取得する](../previous/media-services-portal-get-started-with-aad.md#get-the-client-id-and-client-secret)」セクションの説明に従って、クライアント ID とクライアント シークレットを取得します。

        **[設定]**->**[キー]** を選択してから **[説明]** を追加し、**[保存]** を押すと、キー値が設定されます。

        キーの有効期限が切れると、アカウント所有者は Video Indexer のサポートに連絡してキーを更新する必要があります。

        > [!NOTE]
        > 必ずキー値とアプリケーション ID を書き留めてください。 次のセクションの手順で必要になります。

### <a name="connect-manually"></a>手動で接続する

[[Video Indexer]](https://www.videoindexer.ai/) ページの **[Video Indexer を Azure サブスクリプションに接続]** ダイアログで **[手動構成に切り替える]** リンクを選択します。

このダイアログに次の情報を入力します。

|Setting|説明|
|---|---|
|Video Indexer アカウントのリージョン|Video Indexer アカウントのリージョンの名前。 パフォーマンスの向上とコストの削減を実現するために、Azure Media Services リソースと Azure ストレージ アカウントがあるリージョンの名前を指定するよう強くお勧めします。 |
|Azure Active Directory (AAD) テナント|Azure AD テナントの名前 (例: "contoso.onmicrosoft.com")。 テナント情報は Azure Portal から取得できます。 右上隅のサインインしているユーザーの名前にカーソルを合わせます。 **[ドメイン]** の右側に名前が表示されます。|
|サブスクリプション ID|この接続を作成先にする Azure サブスクリプション。 サブスクリプション ID は Azure portal から取得できます。 左側のウィンドウで **[すべてのサービス]** をクリックし、「サブスクリプション」と検索します。 **[サブスクリプション]** を選択して、サブスクリプションの一覧から目的の ID を選択します。|
|Azure Media Services のリソース グループ名|Media Services アカウントを作成したリソース グループの名前。|
|Media Services リソース名|前のセクションで作成した Azure Media Services アカウントの名前。|
|アプリケーション ID|前のセクションで作成した (指定した Media Services アカウントのアクセス許可を持つ) Azure AD アプリケーション ID。|
|アプリケーション キー|前のセクションで作成した Azure AD アプリケーション キー。 |

## <a name="considerations"></a>考慮事項

Azure Media Services に関する次の考慮事項が適用されます。

* 自動的に接続されると、Azure サブスクリプションに新しいリソース グループ、Media Services アカウント、ストレージ アカウントが表示されます。
* 自動的に接続されると、Video Indexer によってメディアの **[予約ユニット]** は 10 S3 ユニットに設定されます。

    ![Media Services の予約ユニット](./media/create-account/ams-reserved-units.png)

* 既存の Media Services アカウントに接続した場合、Video Indexer で既存メディアの **[予約ユニット]** の構成は変更されません。

   予想される負荷に従って、メディアの [予約ユニット] の種類と数を調整する必要があります。 負荷が高く、ユニットや速度が十分ではない場合は、ビデオ処理でタイムアウト エラーが発生する可能性がある点に注意してください。

* 新しい Media Services アカウントに接続した場合、Video Indexer は、既定の**ストリーミング エンドポイント**を自動的に起動します。

    ![Media Services ストリーミング エンドポイント](./media/create-account/ams-streaming-endpoint.png)

    ストリーミング エンドポイントの起動には時間がかかります。 そのため、アカウントを Azure に接続してから Video Indexer Web アプリケーションでビデオをストリーミングして視聴できるようになるまでに数分かかることがあります。

* 既存の Media Services アカウントに接続した場合、Video Indexer で既定のストリーミング エンドポイントの構成は変更されません。 実行中の**ストリーミング エンドポイント**がない場合、この Media Services アカウントから、または Video Indexer でビデオを視聴できません。

## <a name="next-steps"></a>次の手順

試用アカウント、Azure に接続されている Video Indexer アカウント、またその両方とプログラムでやり取りするには、[API の使用](video-indexer-use-apis.md)に関するページの手順を参照してください。

Azure への接続時に使用したものと同じ Azure AD ユーザーを使用する必要があります。


