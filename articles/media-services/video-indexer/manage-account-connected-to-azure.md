---
title: Video Indexer アカウントの管理
titleSuffix: Azure Media Services
description: Azure に接続された Video Indexer アカウントを管理する方法について説明します。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/14/2021
ms.author: juliako
ms.openlocfilehash: 25025efe5254442c4862563ce329cfae69e7d033
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98898445"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>Azure に接続された Video Indexer アカウントを管理する

この記事では、Azure サブスクリプションと Azure Media Services アカウントに接続する Video Indexer アカウントを管理する方法について説明します。

> [!NOTE]
> このトピックで説明するアカウント構成の調整を行うには、Video Indexer アカウントの所有者であることが必要です。

## <a name="prerequisites"></a>前提条件

[Azure への接続](connect-to-azure.md)に関するページを参照して、自分の Video Indexer アカウントで Azure に接続します。

この記事の「[前提条件](connect-to-azure.md#prerequisites-for-connecting-to-azure)」に従い、「[考慮事項](connect-to-azure.md#azure-media-services-considerations)」を確認してください。

## <a name="examine-account-settings"></a>アカウント設定の調査

このセクションでは、Video Indexer アカウントの設定を調べます。

設定を表示するには:

1. 右上隅にあるユーザー アイコンをクリックし、 **[設定]** を選択します。

    ![Video Indexer の設定](./media/manage-account-connected-to-azure/select-settings.png)

2. **[設定]** ページで **[アカウント]** タブを選択します。

Videos Indexer アカウントが Azure に接続されている場合は、次の内容が表示されます。

* 基になる Azure Media Services アカウントの名前
* 実行中およびキューに入っているインデックス作成ジョブの数
* 割り当てられている占有ユニットの数と種類

アカウントの調整が必要な場合には、アカウント構成に関するエラーや警告が **[設定]** ページに表示されます。 メッセージには、Azure Portal 内で変更を行う必要がある正確な箇所を示すリンクが含まれています。 詳しくは、後の「[エラーと警告](#errors-and-warnings)」セクションをご覧ください。

## <a name="repair-the-connection-to-azure"></a>Azure への接続を修復する

[Video Indexer](https://www.videoindexer.ai/) ページの **[Update connection to Azure Media Services]\(Azure Media Services への接続の更新\)** ダイアログで、次の設定の値を指定するように求められます。

|設定|説明|
|---|---|
|Azure サブスクリプション ID|サブスクリプション ID は Azure portal から取得できます。 左側のウィンドウで **[すべてのサービス]** をクリックし、「サブスクリプション」を検索します。 **[サブスクリプション]** を選択して、サブスクリプションの一覧から目的の ID を選択します。|
|Azure Media Services のリソース グループ名|Media Services アカウントを作成したリソース グループの名前。|
|アプリケーション ID|この Video Indexer アカウントに対して作成した (指定された Media Services アカウントのアクセス許可を持つ) Azure AD アプリケーション ID。 <br/><br/>アプリ ID を取得するには、Azure portal に移動します。 Media Services アカウントで、アカウントを選択し、**[API アクセス]** に移動します。 **[サービス プリンシパルを使って Media Services API に接続する]**  ->  **[Azure AD アプリ]** を選択します。 関連するパラメーターをコピーします。|
|アプリケーション キー|上で指定した Media Services アカウントに関連付けられている Azure AD アプリケーション キー。 <br/><br/>アプリ キーを取得するには、Azure portal に移動します。 Media Services アカウントで、アカウントを選択し、**[API アクセス]** に移動します。 **[サービス プリンシパルを使って Media Services API に接続する]**  ->  **[アプリケーションの管理]**  ->  **[証明書とシークレット]** を選択します。 関連するパラメーターをコピーします。|

## <a name="autoscale-reserved-units"></a>占有ユニットの自動スケール

**[設定]** ページでは、メディア占有ユニット (RU) の自動スケールを設定できます。 オプションが **[オン]** の場合は、最大数の RU を割り当ててることができ、Video Indexer が確実に RU の停止と開始を自動で行うことができます。 このオプションでは、アイドル時間に余分な費用はかかりませんが、インデックス作成の負荷が高いときは、インデックス作成ジョブが完了するまで長時間待機しません。

自動スケーリングでは、1 RU 未満または Media Services アカウントの既定の上限を超えるスケーリングは行われません。 制限値を上げるには、サービス要求を作成してください。 クォータと制限の詳細、サポート チケットを開く方法については、「 [Quotas and limitations (クォータと制限)](../../media-services/previous/media-services-quotas-and-limitations.md)」をご覧ください

![Video Indexer での占有ユニットの自動スケール](./media/manage-account-connected-to-azure/autoscale-reserved-units.png)

## <a name="errors-and-warnings"></a>エラーと警告

アカウントの調整が必要な場合には、アカウント構成に関するエラーや警告が **[設定]** ページに表示されます。 メッセージには、Azure Portal 内で変更を行う必要がある正確な箇所を示すリンクが含まれています。 このセクションでは、エラーおよび警告メッセージに関する詳細を提供します。

* EventGrid

    Azure Portal を使用して EventGrid リソース プロバイダーを登録する必要があります。 [Azure Portal](https://portal.azure.com/) で、 **[サブスクリプション]** > [<サブスクリプション>] > **[ResourceProviders]**  >  **[Microsoft.EventGrid]** に移動します。 **[登録済み]** 状態でない場合は、 **[登録]** を選択します。 登録には数分かかります。

* ストリーミング エンドポイント

    基になる Media Services アカウントに既定の **ストリーミング エンドポイント** があり、開始状態になっていることを確認します。 そうなっていない場合は、この Media Services アカウントから、または Video Indexer でビデオを視聴できません。

* メディア占有ユニット

    ビデオにインデックスを付けるには、Media Service リソースにメディア占有ユニットを割り当てる必要があります。 インデックス作成のパフォーマンスを最適にするには、少なくとも 10 以上の S3 占有ユニットを割り当てることをお勧めします。 料金について詳しくは、[Media Services の価格詳細](https://azure.microsoft.com/pricing/details/media-services/)ページの FAQ のセクションをご覧ください。

## <a name="next-steps"></a>次のステップ

[API の使用](video-indexer-use-apis.md)に関するページの手順に従って、試用アカウント、または Azure に接続されている Video Indexer アカウントとプログラムでやり取りすることができます。

Azure への接続時に使用したものと同じ Azure AD ユーザーを使用します。
