---
title: Azure Stream Analytics でのデータ保護
description: この記事では、Azure Stream Analytics ジョブによって使用されるプライベート データを暗号化する方法について説明します。
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: 102b03ad4fe247ae0abc4e2312d7027c6170333f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019467"
---
# <a name="data-protection-in-azure-stream-analytics"></a>Azure Stream Analytics でのデータ保護 

Azure Stream Analytics は、フル マネージドなサービスとしてのプラットフォームであり、リアルタイム分析パイプラインを構築することができます。 クラスターのプロビジョニング、使用量に合わせたノードのスケーリング、内部チェックポイントの管理などの時間がかかる処理は、すべてバックグラウンドで管理されます。

## <a name="private-data-assets-that-are-stored"></a>格納されるプライベート データ資産

Azure Stream Analytics によって、次のメタデータとデータが実行目的で保持されます。 

* クエリ定義とそれに関連する構成  

* ユーザー定義の関数または集計  

* Stream Analytics ランタイムで必要とされるチェックポイント

* 参照データのスナップショット 

* Stream Analytics ジョブによって使用されるリソースの接続詳細

規制されている業界や環境におけるコンプライアンス義務を果たすために、[Microsoft のコンプライアンス認証](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)に関するページを参照してください。 

## <a name="in-region-data-residency"></a>リージョン内のデータ所在地
Azure Stream Analytics には、前に説明した顧客データとその他のメタデータが格納されます。 顧客データは、Azure Stream Analytics によって 1 つのリージョンに既定で格納されるため、このサービスは、[セキュリティ センター](https://azuredatacentermap.azurewebsites.net/)に指定されているものも含めて、リージョンのデータ所在地の要件を自動的に満たします。
さらに、ストリーム分析ジョブに関連するすべてのデータ資産 (顧客データやその他のメタデータ) を、選択したストレージ アカウントで暗号化して単一のリージョンに保存することもできます。

## <a name="encrypt-your-data"></a>データを暗号化する

Stream Analytics は、データの暗号化とセキュリティ保護のために、最高クラスの暗号化規格をインフラストラクチャ全体で自動的に使用します。 Stream Analytics を信頼するだけで、インフラストラクチャの管理について心配する必要なく、すべてのデータを安全に格納できます。

カスタマー マネージド キーを使用してデータを暗号化する場合は、独自のストレージ アカウント (汎用 V1 または V2) を使用して、Stream Analytics ランタイムに必要なプライベート データ資産を格納できます。 ストレージ アカウントは、必要に応じて暗号化できます。 プライベート データ資産が、Stream Analytics インフラストラクチャによって永続的に保存されることはありません。 

この設定は Stream Analytics ジョブの作成時に構成する必要があり、ジョブのライフ サイクル全体を通して変更することはできません。 Stream Analytics によって使用されているストレージの変更または削除は推奨されません。 ストレージ アカウントを削除すると、すべてのプライベート データ資産が完全に削除されるため、ジョブが失敗します。 

Stream Analytics ポータルを使用しても、ストレージ アカウントに対するキーの更新またはローテーションを行うことはできません。 REST API を使用して、キーを更新できます。


### <a name="configure-storage-account-for-private-data"></a>プライベート データ用のストレージ アカウントを構成する 

ストレージ アカウントを暗号化してすべてのデータを保護し、プライベート データの場所を明示的に選択します。 

規制されている業界や環境におけるコンプライアンス義務を果たすために、[Microsoft のコンプライアンス認証](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)に関するページを参照してください。 

プライベート データ資産用のストレージ アカウントを構成するには、次の手順に従います。 この構成は、ストレージ アカウントからではなく、Stream Analytics ジョブから作られます。

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. Azure Portal の左上隅にある **[リソースの作成]** を選択します。 

1. 結果の一覧で、 **[分析]**  > **[Stream Analytics ジョブ]**   の順に選択します。 

1. [Stream Analytics ジョブ] ページで、名前、リージョン、スケールなどの必要な詳細情報を入力します。 

1. *[Secure all private data assets needed by this job in my Storage account]\(このジョブに必要なすべてのプライベート データ資産を自分のストレージ アカウントに保管してセキュリティで保護する\)* チェック ボックスをオンにします。

1. サブスクリプションからストレージ アカウントを選択します。 この設定は、ジョブのライフ サイクル全体を通して変更できないことに注意してください。 また、このオプションは、ジョブの作成後に追加することができません。

1. 接続文字列を使用して認証するには、[認証モード] ドロップダウンから **[接続文字列]** を選択します。 ストレージ アカウント キーは、サブスクリプションから自動的に設定されます。

   ![プライベート データ ストレージ アカウントの設定](./media/data-protection/storage-account-create.png)

1. マネージド ID (プレビュー) を使用して認証するには、[認証モード] ドロップダウンから **[マネージド ID]** を選択します。 マネージド ID を選択した場合は、*ストレージ BLOB データ共同作成者* ロール で Stream Analytics ジョブをストレージ アカウントのアクセス制御リストに追加する必要があります。 ジョブにアクセス権を付与しない場合は、そのジョブではどのような操作も実行できなくなります。 アクセス権を付与する方法に関する詳細については、「[Azure RBAC を使用して他のリソースにマネージド ID アクセスを割り当てる](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md#use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource)」を参照してください。

   :::image type="content" source="media/data-protection/storage-account-create-msi.png" alt-text="マネージド ID の認証を使用したプライベート データ ストレージ アカウントの設定":::

## <a name="private-data-assets-that-are-stored-by-stream-analytics"></a>Stream Analytics によって格納されるプライベート データ資産

Stream Analytics で存続する必要があるプライベート データは、ストレージ アカウントに格納されます。 プライベート データ資産の例を次に示します。 

* 作成したクエリとそれに関連する構成  

* ユーザー定義関数 

* Stream Analytics ランタイムで必要とされるチェックポイント

* 参照データのスナップショット 

Stream Analytics ジョブによって使用される、リソースの接続詳細も保存されます。 ストレージ アカウントを暗号化して、すべてのデータを保護します。 

規制されている業界や環境におけるコンプライアンス義務を果たすために、[Microsoft のコンプライアンス認証](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)に関するページを参照してください。 

## <a name="enables-data-residency"></a>データ所在地を有効にする 
この機能を使用すると、必要に応じてストレージ アカウントを指定することによって、データ所在地要件を強制的に適用できます。

## <a name="known-issues"></a>既知の問題
マネージド ID を使用して入力または出力を認証するときに、カスタマー マネージド キーを使用するジョブが失敗するという既知の問題があります。 

## <a name="next-steps"></a>次のステップ

* [Azure ストレージ アカウントの作成](../storage/common/storage-account-create.md)
* [Azure Stream Analytics の入力について](stream-analytics-add-inputs.md)
* [Azure Stream Analytics ジョブでのチェックポイントと再生の概念](stream-analytics-concepts-checkpoint-replay.md)
* [Stream Analytics での参照に参照データを使用する](stream-analytics-use-reference-data.md)
