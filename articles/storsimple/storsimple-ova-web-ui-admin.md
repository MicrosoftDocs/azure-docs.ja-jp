---
title: StorSimple Virtual Array の Web UI による管理 | Microsoft Docs
description: StorSimple Virtual Array の Web UI を使用して、基本的なデバイス管理タスクを実行する方法について説明します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: ea65b4c7-a478-43e6-83df-1d9ea62916a6
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 12/1/2016
ms.author: alkohli
ms.openlocfilehash: 6bea9c9b7807ffb32b6071c968d3186965477009
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000931"
---
# <a name="use-the-web-ui-to-administer-your-storsimple-virtual-array"></a>Web UI を使用した StorSimple Virtual Array の管理
![セットアップ プロセス フロー](./media/storsimple-ova-web-ui-admin/manage4.png)

## <a name="overview"></a>概要
この記事のチュートリアルは、2016 年 3 月の一般公開 (GA) リリースを実行する Microsoft Azure StorSimple Virtual Array (StorSimple オンプレミス仮想デバイスとも呼ばれます) に適用されます。 この記事では、StorSimple Virtual Array で実行できる一部の複雑なワークフローと管理タスクについて説明します。 StorSimple Virtual Array は、StorSimple Manager サービス UI (ポータル UI とも呼ばれます) とデバイスのローカル Web UI を使用して管理できます。 この記事では、Web UI を使用して実行できるタスクについて説明します。

この記事には次のチュートリアルが含まれています。

* サービス データ暗号化キーの取得
* Web UI のセットアップ エラーのトラブルシューティング
* ログ パッケージの生成
* デバイスのシャットダウンと再起動

## <a name="get-the-service-data-encryption-key"></a>サービス データ暗号化キーの取得
StorSimple Manager サービスに最初のデバイスを登録すると、サービス データ暗号化キーが生成されます。 このキーは、StorSimple Manager サービスに追加のデバイスを登録する際にサービス登録キーと共に必要になります。

サービス データ暗号化キーを紛失し、取得する必要がある場合は、サービスに登録されているデバイスのローカル Web UI で次の手順を実行します。

#### <a name="to-get-the-service-data-encryption-key"></a>サービス データ暗号化キーを取得するには
1. ローカル Web UI に接続します。 **[構成]**  >  **[クラウド設定]** に移動します。
2. ページの下部にある **[サービス データ暗号化キーの取得]** をクリックします。 キーが表示されます。 このキーをコピーして保存します。
   
    ![サービス データ暗号化キーの取得 1](./media/storsimple-ova-web-ui-admin/image27.png)

## <a name="troubleshoot-web-ui-setup-errors"></a>Web UI のセットアップ エラーのトラブルシューティング
ローカル Web UI を使用してデバイスを構成する際に、エラーが発生する場合があります。 このようなエラーの診断とトラブルシューティングを行うには、診断テストを実行します。

#### <a name="to-run-the-diagnostic-tests"></a>診断テストを実行するには
1. ローカル Web UI で、 **[トラブルシューティング]**  >  **[診断テスト]** に移動します。
   
    ![診断の実行 1](./media/storsimple-ova-web-ui-admin/image29.png)
2. ページの下部にある **[診断テストの実行]** をクリックします。 これにより、ネットワーク、デバイス、Web プロキシ、時刻、またはクラウドの設定で発生する可能性のある問題を診断するためのテストが開始されます。 デバイスでテストを実行中であることが通知されます。
3. テストが完了すると、結果が表示されます。 次の例は、診断テストの結果を示しています。 このデバイスでは Web プロキシ設定が構成されていないため、Web プロキシのテストは実行されていません。 ネットワーク設定、DNS サーバー、および時刻設定を調べる他のテストはすべて正常に完了しています。
   
    ![診断の実行 2](./media/storsimple-ova-web-ui-admin/image30.png)

## <a name="generate-a-log-package"></a>ログ パッケージの生成
ログ パッケージは、Microsoft サポートがデバイスの問題のトラブルシューティングを行う際に役立つすべての関連ログで構成されます。 このリリースでは、ログ パッケージはローカル Web UI を使用して生成できます。

#### <a name="to-generate-the-log-package"></a>ログ パッケージを生成するには
1. ローカル Web UI で、 **[トラブルシューティング]**  >  **[システム ログ]** に移動します。
   
    ![ログ パッケージの生成 1](./media/storsimple-ova-web-ui-admin/image31.png)
2. ページの下部にある **[ログ パッケージの作成]** をクリックします。 システム ログのパッケージが作成されます。 作成には数分かかります。
   
    ![ログ パッケージの生成 2](./media/storsimple-ova-web-ui-admin/image32.png)
   
    パッケージが正常に作成されると通知されます。ページが更新され、パッケージの作成日時が示されます。
   
    ![ログ パッケージの生成 3](./media/storsimple-ova-web-ui-admin/image33.png)
3. **[ログ パッケージのダウンロード]** をクリックします。 圧縮されたパッケージがシステムにダウンロードされます。
   
    ![ログ パッケージの生成 4](./media/storsimple-ova-web-ui-admin/image34.png)
4. ダウンロードしたログ パッケージを解凍すると、システム ログ ファイルを表示できます。

## <a name="shut-down-and-restart-your-device"></a>デバイスのシャットダウンと再起動
ローカル Web UI を使用して、仮想デバイスをシャットダウンおよび再起動できます。 再起動する前に、ホストのボリュームや共有をオフラインにしてから、デバイスをオフラインにすることをお勧めします。 これにより、データの破損の可能性を最小限にします。 

#### <a name="to-shut-down-your-virtual-device"></a>仮想デバイスをシャットダウンするには
1. ローカル Web UI で、 **[メンテナンス]**  >  **[電源設定]** に移動します。
2. ページの下部にある **[シャット ダウン]** をクリックします。
   
    ![デバイスのシャットダウン 1](./media/storsimple-ova-web-ui-admin/image36.png)
3. デバイスをシャットダウンすると、実行中のすべての IO が中断され、ダウンタイムが発生することを示す警告が表示されます。 操作を完了するには、チェック マーク アイコン ![チェック マーク アイコン](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![デバイスのシャットダウンの警告](./media/storsimple-ova-web-ui-admin/image37.png)
   
    シャットダウンが開始されたことが通知されます。
   
    ![デバイスのシャットダウンの開始](./media/storsimple-ova-web-ui-admin/image38.png)
   
    デバイスがシャットダウンされます。 デバイスを起動する場合は、Hyper-V マネージャーを使用してこれを実行する必要があります。

#### <a name="to-restart-your-virtual-device"></a>仮想デバイスを再起動するには
1. ローカル Web UI で、 **[メンテナンス]**  >  **[電源設定]** に移動します。
2. ページの下部にある **[再起動]** をクリックします。
   
    ![デバイスの再起動](./media/storsimple-ova-web-ui-admin/image36.png)
3. デバイスを再起動すると、実行中のすべての IO が中断され、ダウンタイムが発生することを示す警告が表示されます。 操作を完了するには、チェック マーク アイコン ![チェック マーク アイコン](./media/storsimple-ova-web-ui-admin/image3.png).
   
    ![再起動の警告](./media/storsimple-ova-web-ui-admin/image37.png)
   
    再起動が開始されたことが通知されます。
   
    ![再起動の開始](./media/storsimple-ova-web-ui-admin/image39.png)
   
    再起動の実行中は UI に接続できなくなります。 UI を定期的に更新することで、再起動を監視できます。 また、Hyper-V マネージャーを使用して、デバイスの再起動の状態を監視することもできます。

## <a name="next-steps"></a>次のステップ
[StorSimple Manager サービスを使用してデバイスを管理する](storsimple-virtual-array-manager-service-administration.md)方法を確認します。

