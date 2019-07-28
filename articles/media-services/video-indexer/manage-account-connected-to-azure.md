---
title: Video Indexer アカウントの管理
titlesuffix: Azure Media Services
description: この記事では、Azure に接続された Video Indexer アカウントを管理する方法について説明します。
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 37aef7c0ae06aefc69b8a72a80c8ade3951e8b0d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799518"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>Azure に接続された Video Indexer アカウントを管理する

この記事では、Azure サブスクリプションと Azure Media Services アカウントに接続する Video Indexer アカウントを管理する方法について説明します。

> [!NOTE]
> このトピックで説明するアカウント構成の調整を行うには、Video Indexer アカウントの所有者であることが必要です。

## <a name="prerequisites"></a>前提条件

[Azure への接続](connect-to-azure.md)に関するページを参照して、自分の Video Indexer アカウントで Azure に接続します。 

この記事の「[前提条件](connect-to-azure.md#prerequisites)」に従い、「[考慮事項](connect-to-azure.md#considerations)」を確認してください。

## <a name="examine-account-settings"></a>アカウント設定の調査

このセクションでは、Video Indexer アカウントの設定を調べます。

設定を表示するには:

1. 右上隅にあるユーザー アイコンをクリックし、 **[設定]** を選択します。

    ![設定](./media/manage-account-connected-to-azure/select-settings.png)

2. **[設定]** ページで **[アカウント]** タブを選択します。

Videos Indexer アカウントが Azure に接続されている場合は、次の内容が表示されます。

* 基になる Azure Media Services アカウントの名前
* 実行中およびキューに入っているインデックス作成ジョブの数
* 割り当てられている占有ユニットの数と種類

アカウントの調整が必要な場合には、アカウント構成に関するエラーや警告が **[設定]** ページに表示されます。 メッセージには、Azure Portal 内で変更を行う必要がある正確な箇所を示すリンクが含まれています。 詳しくは、後の「[エラーと警告](#errors-and-warnings)」セクションをご覧ください。

## <a name="auto-scale-reserved-units"></a>占有ユニットの自動スケール

**[設定]** ページでは、メディア占有ユニット (RU) の自動スケールを設定できます。 オプションが **[オン]** の場合は、最大数の RU を割り当ててることができ、Video Indexer が確実に RU の停止と開始を自動で行うことができます。 このオプションでは、アイドル時間に余分な費用はかかりませんが、インデックス作成の負荷が高いときは、インデックス作成ジョブが完了するまで長時間待機しません。

自動スケールでは、1 RU 未満または Media Services アカウントの既定の上限を超える拡大縮小は行われません。 制限値を上げるには、サービス要求を作成してください。 クォータと制限の詳細、サポート チケットを開く方法については、「 [Quotas and limitations (クォータと制限)](../../media-services/previous/media-services-quotas-and-limitations.md)」をご覧ください

![サインアップ](./media/manage-account-connected-to-azure/autoscale-reserved-units.png)

## <a name="errors-and-warnings"></a>エラーと警告

アカウントの調整が必要な場合には、アカウント構成に関するエラーや警告が **[設定]** ページに表示されます。 メッセージには、Azure Portal 内で変更を行う必要がある正確な箇所を示すリンクが含まれています。 このセクションでは、エラーおよび警告メッセージに関する詳細を提供します。

* Event Grid

    Azure Portal を使用して EventGrid リソース プロバイダーを登録する必要があります。 [Azure Portal](https://portal.azure.com/) で、 **[サブスクリプション]** > [<サブスクリプション>] > **[ResourceProviders]**  >  **[Microsoft.EventGrid]** に移動します。 **[登録済み]** 状態でない場合は、 **[登録]** をクリックします。 登録には数分かかります。 

* ストリーミング エンドポイント

    基になる Media Services アカウントに既定の**ストリーミング エンドポイント**があり、開始状態になっていることを確認します。 そうなっていない場合は、この Media Services アカウントから、または Video Indexer でビデオを視聴できません。

* メディア占有ユニット 

    ビデオにインデックスを付けるには、Media Service リソースにメディア占有ユニットを割り当てる必要があります。 インデックス作成のパフォーマンスを最適にするには、少なくとも 10 以上の S3 占有ユニットを割り当てることをお勧めします。 料金について詳しくは、[Media Services の価格詳細](https://azure.microsoft.com/pricing/details/media-services/)ページの FAQ のセクションをご覧ください。   

## <a name="next-steps"></a>次の手順

試用アカウント、Azure に接続されている Video Indexer アカウント、またその両方とプログラムでやり取りするには、[API の使用](video-indexer-use-apis.md)に関するページの手順を参照してください。

Azure への接続時に使用したものと同じ Azure AD ユーザーを使用する必要があります。
