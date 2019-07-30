---
title: Azure Media Services を使用してライブ ストリーミングを実行し、Azure Portal でマルチビットレートのストリームを作成する | Microsoft Docs
description: このチュートリアルでは、Azure ポータルを利用し、シングル ビットレートのライブ ストリームを受信してマルチ ビットレート ストリームにエンコードするチャネルを作成する手順について説明します。
services: media-services
documentationcenter: ''
author: anilmur
manager: femila
editor: ''
ms.assetid: 504f74c2-3103-42a0-897b-9ff52f279e23
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: daab3c3b2a5b756686a4867350478faaa1142279
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "64726864"
---
# <a name="perform-live-streaming-using-media-services-to-create-multi-bitrate-streams-with-azure-portal"></a>Media Services を使用してライブ ストリーミングを実行し、Azure portal でマルチビットレートのストリームを作成する  
> [!div class="op_single_selector"]
> * [ポータル](media-services-portal-creating-live-encoder-enabled-channel.md)
> * [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
> * [REST API](https://docs.microsoft.com/rest/api/media/operations/channel)
> 

> [!NOTE]
> Media Services v2 には新機能は追加されません。 <br/>最新のバージョンである [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/) をご確認ください。 また、[v2 から v3 への移行ガイダンス](../latest/migrate-from-v2-to-v3.md)をご覧ください

このチュートリアルでは、シングル ビットレートのライブ ストリームを受信してマルチ ビットレート ストリームにエンコードする **チャネル** を作成する手順について説明します。

ライブ エンコード対応のチャネルに関連する概念の詳細情報については、「 [Live streaming using Azure Media Services to create multi-bitrate streams (Azure Media Services を使用したライブ ストリーミングによるマルチビットレートのストリームの作成)](media-services-manage-live-encoder-enabled-channels.md)」を参照してください。

## <a name="common-live-streaming-scenario"></a>一般的なライブ ストリーミング シナリオ
以下に、一般的なライブ ストリーミング アプリケーションを作成する場合に関係する標準的な手順を示します。

> [!NOTE]
> 現在、ライブ イベントの最大推奨時間は 8 時間です。 チャネルを長時間実行する必要がある場合は、amslived@microsoft.com にお問い合わせください。

1. ビデオ カメラをコンピューターに接続します。 <br/>設定のアイデアについては、「[Simple and portable event video gear setup (シンプルでポータブルなイベント ビデオ機器の設定)]( https://link.medium.com/KNTtiN6IeT)」を参照してください。

    カメラにアクセスできない場合は、[Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) などのツールを使用して、ビデオ ファイルからライブ フィードを生成できます。
1. オンプレミスのライブ エンコーダーを起動して構成します。このエンコーダーでは、シングル ビットレート ストリームを次のいずれかのプロトコルで出力できます:RTMP またはスムーズ ストリーミング。 詳しくは、「 [Azure Media Services RTMP サポートおよびライブ エンコーダー](https://go.microsoft.com/fwlink/?LinkId=532824)」をご覧ください。 <br/>また、ブログ「[Live streaming production with OBS (OBS を使用したライブ ストリーミングの製作)](https://link.medium.com/ttuwHpaJeT)」も参照してください。

    この手順は、チャネルを作成した後でも実行できます。
1. チャネルを作成し、起動します。 
1. チャネルの取り込み URL を取得します。 

    取り込み URL は、ライブ エンコーダーがチャネルにストリームを送信する際に使用されます。
1. チャネルのプレビュー URL を取得します。 

    この URL を使用して、チャネルがライブ ストリームを正常に受信できることを確認します。
1. イベントまたはプログラムを作成します (その際、資産も作成します)。 
1. イベントを発行します (関連付けられた資産の OnDemand ロケーターも作成します)。    
1. ストリーミングとアーカイブを開始する準備ができたら、イベントを開始します。
1. 必要に応じて、ライブ エンコーダーは、広告の開始を信号通知できます。 広告が出力ストリームに挿入されます。
1. イベントのストリーミングとアーカイブを停止するには、任意のタイミングでイベントを停止します。
1. イベントを削除し、必要に応じて資産を削除します。   

## <a name="prerequisites"></a>前提条件

チュートリアルを完了するには次のものが必要です。

* このチュートリアルを完了するには、Azure アカウントが必要です。 アカウントがない場合は、無料試用アカウントを数分で作成することができます。 
  詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。
* Media Services アカウント。 Media Services アカウントを作成するには、「[アカウントの作成](media-services-portal-create-account.md)」を参照してください。
* シングル ビットレートのライブ ストリームを送信できる Web カメラとエンコーダー。

## <a name="create-a-channel"></a>チャネルの作成

1. [Azure Portal](https://portal.azure.com/) で [Media Services] を選択し、Media Services アカウント名をクリックします。
2. **[Live Streaming (ライブ ストリーミング)]** を選択します。
3. **[カスタム作成]** を選択します。 このオプションを使用すると、ライブ エンコードが有効なチャネルを作成できます。

    ![チャネルの作成](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel.png)
4. **[設定]** をクリックします。

   1. チャネルの種類に **Live Encoding** を選択します。 この種類の場合、ライブ エンコードが有効なチャネルが作成されます。 つまり、受信シングル ビットレート ストリームはチャネルに送信され、指定のライブ エンコーダー設定によってマルチ ビットレート ストリームにエンコードされます。 詳細については、「 [Live streaming using Azure Media Services to create multi-bitrate streams (Azure Media Services を使用したライブ ストリーミングによるマルチビットレートのストリームの作成)](media-services-manage-live-encoder-enabled-channels.md)」を参照してください。 [OK] をクリックします。
   2. チャネルの名前を指定します。
   3. ページの下部にある [OK] をクリックします。
5. **[Ingest (取り込み)]** タブを選択します。

   1. このページでストリーミング プロトコルを選択できます。 チャネルの種類 **Live Encoding** で有効なプロトコル オプションは、次のとおりです。

      * シングル ビットレート Fragmented MP4 (スムーズ ストリーミング)
      * シングル ビットレート RTMP

        各プロトコルの詳しい説明については、「 [Live streaming using Azure Media Services to create multi-bitrate streams (Azure Media Services を使用したライブ ストリーミングによるマルチビットレートのストリームの作成)](media-services-manage-live-encoder-enabled-channels.md)」を参照してください。

        チャネルや、チャネルに関連付けられたイベントまたはプログラムの実行中は、プロトコル オプションを変更できません。 別のプロトコルが必要な場合は、ストリーミング プロトコルごとに別のチャネルを作成する必要があります。  
   2. 取り込みに関する IP 制限を適用できます。 

       このチャネルへのビデオの取り込みが許可される IP アドレスを定義できます。 許可された IP アドレスは、1 つの IP アドレス (例: "10.0.0.1")、IP アドレスと CIDR のサブネット マスクを使用した IP 範囲 (例: "10.0.0.1/22")、または IP アドレスとピリオド区切りのサブネット マスクを使用した IP 範囲 (例: "10.0.0.1(255.255.252.0)") のいずれかの形式で指定できます。

       IP アドレスが指定されておらず、規則の定義もない場合は、どの IP アドレスも許可されません。 すべての IP アドレスを許可するには、規則を作成し、0.0.0.0/0 に設定します。
6. **[プレビュー]** タブで、プレビューに関する IP 制限を適用します。
7. **[エンコード]** タブで、エンコード プリセットを指定します。 

    現時点で選択可能なシステム プリセットは、 **既定 720p**のみです。 カスタム プリセットを指定するには、Microsoft サポート チケットを開きます。 その後、作成されたプリセットの名前を入力します。 

> [!NOTE]
> 現時点では、チャネルの開始に最大 30 分ほどかかる場合があります。 チャネルのリセットには最大 5 分かかります。
> 
> 

チャネルを作成すると、チャネルをクリックして **[設定]** を選択できるようになります。ここで、チャネル構成を確認できます。 

詳細については、「 [Live streaming using Azure Media Services to create multi-bitrate streams (Azure Media Services を使用したライブ ストリーミングによるマルチビットレートのストリームの作成)](media-services-manage-live-encoder-enabled-channels.md)」を参照してください。

## <a name="get-ingest-urls"></a>取り込み URL の取得
チャネルが作成されると、ライブ エンコーダーに提供する取り込み URL を取得できます。 エンコーダーは、これらの URL を使用して、ライブ ストリームを入力します。

![取り込み URL](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-ingest-urls.png)

## <a name="create-and-manage-events"></a>イベントの作成と管理

### <a name="overview"></a>概要
チャネルは、ライブ ストリームのセグメントの発行と保存を管理できるイベントまたはプログラムに関連付けられています。 イベントとプログラムはチャネルによって管理されます。 チャネルとプログラムの関係は、従来のメディアとよく似ています。チャネルが絶えずコンテンツのストリームを配信するのに対し、プログラムは、そのチャネル上で決まった時間に生じるイベントです。

イベントの **アーカイブ ウィンドウ** の長さを設定することで、録画されたコンテンツの保持時間を指定できます。 この値は、最小 5 分から最大 25 時間までの範囲で設定できます。 クライアントが現在のライブ位置からさかのぼって検索できる最長時間も、Archive Window (アーカイブ ウィンドウ)の長さによって決まります。 イベントは、指定された時間の長さまでは放送できますが、アーカイブ ウィンドウの長さを過ぎたコンテンツは絶えず破棄されていきます。 さらに、このプロパティの値によって、クライアント マニフェストが肥大した場合の最大サイズも決まります。

各イベントは資産に関連付けられています。 イベントを発行するには、関連付けられた資産の OnDemand ロケーターを作成する必要があります。 このロケーターを作成すると、ストリーミング URL を構築してクライアントに提供できます。

チャネルは、最大 3 つの同時実行イベントをサポートするので、同じ受信ストリームのアーカイブを複数作成できます。 これにより、1 つのイベントのさまざまな部分を必要に応じて発行したりアーカイブしたりできます。 たとえば、ビジネス要件によって 1 つのイベントの 6 時間分をアーカイブする一方、最後の 10 分間のみをブロードキャストする場合があります。 これを実現するには、2 つの同時実行イベントを作成する必要があります。 一方のイベントは 6 時間分のイベントをアーカイブするように設定しますが、プログラムは発行されません。 もう一方のイベントは 10 分間のアーカイブを行うように設定します。このプログラムは発行されます。

新しいイベントには既存のプログラムを再使用できません。 代わりに、イベントごとに新しいプログラムを作成し、起動します。

ストリーミングとアーカイブを開始する準備ができたら、イベントまたはプログラムを開始します。 イベントのストリーミングとアーカイブを停止するには、任意のタイミングでイベントを停止します。 

アーカイブ済みコンテンツを削除するには、イベントを停止して削除したうえで、関連付けられたアセットを削除します。 イベントが資産を使用している場合は資産を削除できません。まずイベントを削除する必要があります。 

イベントを停止して削除した後も、資産を削除していなければ、アーカイブ済みコンテンツをオンデマンドでのビデオとしてストリーミングできます。

アーカイブ済みコンテンツを保持したいが、ストリーミングには使用したくない場合は、ストリーミング ロケーターを削除します。

### <a name="createstartstop-events"></a>イベントの作成、開始、停止
ストリームがチャネルに流れ始めると、資産、プログラム、およびストリーミング ロケーターを作成することにより、ストリーミング イベントを開始できます。 これにより、ストリームがアーカイブされ、ストリーミング エンドポイントを介して視聴者がストリームを使用できるようになります。 

>[!NOTE]
>AMS アカウントの作成時に、**既定**のストリーミング エンドポイントが自分のアカウントに追加され、**停止**状態になっています。 コンテンツのストリーミングを開始し、ダイナミック パッケージと動的暗号化を活用するには、コンテンツのストリーミング元のストリーミング エンドポイントが**実行中**状態である必要があります。 

イベントを開始するには、次の 2 つの方法があります。 

1. **[チャネル]** ページで **[ライブ イベント]** をクリックし、新しいイベントを追加します。

    イベント名、資産名、アーカイブ ウィンドウ、暗号化オプションを指定します。

    ![プログラムの作成](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-program.png)

    **[Publish this live event now (このライブ イベントを今すぐ発行)]** をオンにしたままにすると、URL の発行イベントが作成されます。

    イベントをストリーミングする準備ができたら、いつでも **[開始]** をクリックできます。

    イベントを開始すると、 **[Watch (視聴)]** をクリックしてコンテンツの再生を開始できます。
2. この方法の代わりに、ショートカットを使用して、 **[チャネル]** ページの **[起動]** ボタンをクリックすることもできます。 これにより、既定の資産、プログラム、ストリーミング ロケーターが作成されます。

    イベント名は **default** となり、アーカイブ ウィンドウは 8 時間に設定されます。

**[ライブ イベント]** ページで、公開されたイベントを視聴できます。 

**[Off Air (放送中止)]** をクリックすると、すべてのライブ イベントが停止します。 

## <a name="watch-the-event"></a>イベントの視聴
イベントを視聴するには、Azure Portal で **[Watch (視聴)]** をクリックするか、ストリーミング URL をコピーして任意のプレーヤーを使用します。 

![作成されたもの](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-play-event.png)

ライブ イベントが停止すると、イベントがオンデマンド コンテンツに自動的に変換されます。

## <a name="clean-up"></a>クリーンアップ
ストリーミング イベントが完了し、以前にプロビジョニングされたリソースをクリーンアップする場合は、次の手順に従います。

* エンコーダーからのストリームのプッシュを停止します。
* チャネルを停止します。 チャネルが停止すると、いかなる課金も発生しません。 もう一度開始する必要がある場合、取り込み URL は同一になるため、エンコーダーを再構成する必要はありません。
* ライブ イベントのアーカイブをオンデマンド ストリームとして提供し続けるのでない限り、ストリーミング エンドポイントを停止できます。 チャネルが停止状態の場合は、いかなる課金も発生しません。

## <a name="view-archived-content"></a>アーカイブ済みコンテンツを視聴する
イベントを停止して削除した後も、アセットを削除していなければ、アーカイブ済みコンテンツをオンデマンドでのビデオとしてストリーミングできます。 イベントがアセットを使用している場合はアセットを削除できません。まずイベントを削除する必要があります。 

アセットを管理するには、 **[設定]** を選択し、 **[アセット]** をクリックします。

![アセット](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-assets.png)

## <a name="considerations"></a>考慮事項
* 現在、ライブ イベントの最大推奨時間は 8 時間です。 チャネルを長時間実行する必要がある場合は、amslived@microsoft.com にお問い合わせください。
* コンテンツのストリーミング元のストリーミング エンドポイントが**実行中**状態であることを確認してください。

## <a name="next-step"></a>次のステップ
Media Services のラーニング パスを確認します。

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

