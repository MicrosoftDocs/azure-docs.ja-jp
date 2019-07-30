---
title: Azure portal を使用したオンプレミス エンコーダーでのライブ ストリーミング | Microsoft Docs
description: このチュートリアルでは、パススルー配信用に構成されたチャネルを作成する手順を紹介します。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 6f4acd95-cc64-4dd9-9e2d-8734707de326
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 9a8ab024443744f50482dd2ca1cfb33db43359e9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "61463374"
---
# <a name="perform-live-streaming-with-on-premises-encoders-using-azure-portal"></a>Azure portal を使用してオンプレミス エンコーダーでライブ ストリーミングを実行する方法
> [!div class="op_single_selector"]
> * [ポータル](media-services-portal-live-passthrough-get-started.md)
> * [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/channel)
> 
> 

> [!NOTE]
> Media Services v2 には新機能は追加されません。 <br/>最新のバージョンである [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/) をご確認ください。 また、[v2 から v3 への移行ガイダンス](../latest/migrate-from-v2-to-v3.md)を参照してください。

このチュートリアルでは、Azure ポータルを使用して、パススルー配信用に構成された **チャネル** を作成する手順を紹介します。 

## <a name="prerequisites"></a>前提条件
チュートリアルを完了するには次のものが必要です。

* Azure アカウント。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。 
* Media Services アカウント。 Media Services アカウントを作成するには、「 [Media Services アカウントの作成方法](media-services-portal-create-account.md)」を参照してください。
* Web カメラ。 たとえば、 [Telestream Wirecast エンコーダー](https://www.telestream.net/wirecast/overview.htm)。

次の記事の確認を強くお勧めします。

* [Azure Media Services RTMP サポートおよびライブ エンコーダー](https://azure.microsoft.com/blog/2014/09/18/azure-media-services-rtmp-support-and-live-encoders/)
* [Azure Media Services を使用したライブ ストリーミングの概要](media-services-manage-channels-overview.md)
* [マルチビットレートのストリームを作成するオンプレミス エンコーダーを使用したライブ ストリーミング](media-services-live-streaming-with-onprem-encoders.md)

## <a id="scenario"></a>一般的なライブ ストリーミング シナリオ

次の手順では、パススルー配信用に構成されたチャネルを使用する、一般的なライブ ストリーミング アプリケーションの作成に関連するタスクについて説明します。 このチュートリアルでは、パススルー チャネルとライブ イベントを作成、管理する方法について説明します。

> [!NOTE]
> コンテンツのストリーミング元のストリーミング エンドポイントが**実行中**状態であることを確認してください。 
    
1. ビデオ カメラをコンピューターに接続します。 <br/>設定のアイデアについては、「[Simple and portable event video gear setup (シンプルでポータブルなイベント ビデオ機器の設定)]( https://link.medium.com/KNTtiN6IeT)」を参照してください。
1. マルチビットレート RTMP またはフラグメント化 MP4 ストリームを出力するオンプレミスのライブ エンコーダーを起動して構成します。 詳しくは、「 [Azure Media Services RTMP サポートおよびライブ エンコーダー](https://go.microsoft.com/fwlink/?LinkId=532824)」をご覧ください。<br/>また、ブログ「[Live streaming production with OBS (OBS を使用したライブ ストリーミングの製作)](https://link.medium.com/ttuwHpaJeT)」も参照してください。
   
    この手順は、チャネルを作成した後でも実行できます。
1. パススルー チャネルを作成し、開始します。
1. チャネルの取り込み URL を取得します。 
   
    取り込み URL は、ライブ エンコーダーがチャネルにストリームを送信する際に使用されます。
1. チャネルのプレビュー URL を取得します。 
   
    この URL を使用して、チャネルがライブ ストリームを正常に受信できることを確認します。
1. ライブ イベントまたはライブ プログラムを作成します。 
   
    Azure ポータルを使用する場合、ライブ イベントを作成すると資産も作成されます。 

1. ストリーミングとアーカイブを開始する準備ができたら、イベントまたはプログラムを開始します。
1. 必要に応じて、ライブ エンコーダーは、広告の開始を信号通知できます。 広告が出力ストリームに挿入されます。
1. イベントのストリーミングとアーカイブを停止するには、任意のタイミングでイベントまたはプログラムを停止します。
1. イベントまたはプログラムを削除し、必要に応じてアセットも削除します。     

> [!IMPORTANT]
> オンプレミスのエンコーダーとパススルー チャネルを使用したライブ ストリーミングに関する概念と考慮事項については、「[マルチビットレートのストリームを作成するオンプレミス エンコーダーを使用したライブ ストリーミング](media-services-live-streaming-with-onprem-encoders.md)」を参照してください。
> 
> 

## <a name="to-view-notifications-and-errors"></a>通知とエラーを表示するには
Azure ポータルからの通知とエラーを表示するには、通知アイコンをクリックします。

![通知](./media/media-services-portal-passthrough-get-started/media-services-notifications.png)

## <a name="create-and-start-pass-through-channels-and-events"></a>パススルー チャネルとイベントの作成と開始
チャネルは、ライブ ストリームのセグメントの発行と保存を管理できるイベントまたはプログラムに関連付けられています。 イベントはチャネルによって管理されます。 

プログラムの **アーカイブ ウィンドウ** の長さを設定することで、録画されたコンテンツの保持時間を指定できます。 この値は、最小 5 分から最大 25 時間までの範囲で設定できます。 クライアントが現在のライブ位置からさかのぼって検索できる最長時間も、Archive Window (アーカイブ ウィンドウ)の長さによって決まります。 イベントは、指定された時間の長さまでは放送できますが、アーカイブ ウィンドウの長さを過ぎたコンテンツは絶えず破棄されていきます。 さらに、このプロパティの値によって、クライアント マニフェストが肥大した場合の最大サイズも決まります。

各イベントは資産に関連付けられています。 イベントを発行するには、関連付けられた資産の OnDemand ロケーターを作成する必要があります。 このロケーターを作成すると、ストリーミング URL を構築してクライアントに提供できます。

チャネルは、最大 3 つの同時実行イベントをサポートするので、同じ受信ストリームのアーカイブを複数作成できます。 これにより、1 つのイベントのさまざまな部分を必要に応じて発行したりアーカイブしたりできます。 たとえば、ビジネス要件によって 1 つのプログラムの 6 時間分をアーカイブする一方、最後の 10 分間のみをブロードキャストする場合があります。 これを実現するには、2 つの同時実行プログラムを作成する必要があります。 1 つのプログラムは 6 時間分のイベントをアーカイブするように設定しますが、プログラムは発行されません。 もう 1 つのプログラムは 10 分間のアーカイブを行うように設定します。このプログラムは発行されます。

既存のライブ イベントの再利用はしないでください。 代わりに、イベントごとに新しいイベントを作成し、開始します。

ストリーミングとアーカイブを開始する準備ができたら、イベントを開始します。 イベントのストリーミングとアーカイブを停止するときにプログラムを停止します。 

アーカイブ済みコンテンツを削除するには、イベントを停止して削除したうえで、関連付けられた資産を削除します。 イベントがアセットを使用している場合はアセットを削除できません。まずイベントを削除する必要があります。 

イベントを停止して削除した後も、アセットを削除していなければ、アーカイブ済みコンテンツをオンデマンドでのビデオとしてストリーミングできます。

アーカイブ済みコンテンツを保持したいが、ストリーミングには使用したくない場合は、ストリーミング ロケーターを削除します。

### <a name="to-use-the-portal-to-create-a-channel"></a>ポータルを使用してチャネルを作成するには
このセクションでは、 **簡易作成** オプションを使用してパススルー チャネルを作成する方法について説明します。

パススルー チャネルの詳細については、「[マルチビットレートのストリームを作成するオンプレミス エンコーダーを使用したライブ ストリーミング](media-services-live-streaming-with-onprem-encoders.md)」を参照してください。

1. [Azure Portal](https://portal.azure.com/) で Azure Media Services アカウントを選択します。
2. **[設定]** ウィンドウで、 **[ライブ ストリーミング]** をクリックします。 
   
    ![使用の開始](./media/media-services-portal-passthrough-get-started/media-services-getting-started.png)
   
    **[Live streaming (ライブ ストリーミング)]** ウィンドウが表示されます。
3. **[簡易作成]** をクリックして、RTMP 取り込みプロトコルを備えたパススルー チャネルを作成します。
   
    **[CREATE A NEW CHANNEL (新しいチャネルの作成)]** ウィンドウが表示されます。
4. 新しいチャネルに名前を付け、 **[作成]** をクリックします。 
   
    これで、RTMP 取り込みプロトコルを備えたパススルー チャネルが作成されます。

## <a name="create-events"></a>イベントの作成
1. イベントを追加するチャネルを選択します。
2. **[ライブ イベント]** をクリックします。

![Event](./media/media-services-portal-passthrough-get-started/media-services-create-events.png)

## <a name="get-ingest-urls"></a>取り込み URL の取得
チャネルが作成されると、ライブ エンコーダーに提供する取り込み URL を取得できます。 エンコーダーは、これらの URL を使用して、ライブ ストリームを入力します。

![作成されたもの](./media/media-services-portal-passthrough-get-started/media-services-channel-created.png)

## <a name="watch-the-event"></a>イベントの視聴
イベントを視聴するには、Azure Portal で **[Watch (視聴)]** をクリックするか、ストリーミング URL をコピーして任意のプレーヤーを使用します。 

![作成されたもの](./media/media-services-portal-passthrough-get-started/media-services-default-event.png)

ライブ イベントは、停止するとオンデマンド コンテンツに自動的に変換されます。

## <a name="clean-up"></a>クリーンアップ
パススルー チャネルの詳細については、「[マルチビットレートのストリームを作成するオンプレミス エンコーダーを使用したライブ ストリーミング](media-services-live-streaming-with-onprem-encoders.md)」を参照してください。

* チャネルを停止できるのは、チャネルのすべてのイベントまたはプログラムが停止している場合のみです。  チャネルが停止すると、いかなる課金も発生しません。 もう一度開始する必要がある場合、取り込み URL は同一になるため、エンコーダーを再構成する必要はありません。
* チャネルを削除できるのは、チャネルのすべてのライブ イベントが削除されている場合のみです。

## <a name="view-archived-content"></a>アーカイブ済みコンテンツを視聴する
イベントを停止して削除した後も、アセットを削除していなければ、アーカイブ済みコンテンツをオンデマンドでのビデオとしてストリーミングできます。 イベントがアセットを使用している場合はアセットを削除できません。まずイベントを削除する必要があります。 

アセットを管理するには、 **[設定]** を選択し、 **[アセット]** をクリックします。

![資産](./media/media-services-portal-passthrough-get-started/media-services-assets.png)

## <a name="next-step"></a>次のステップ
Media Services のラーニング パスを確認します。

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

