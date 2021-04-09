---
title: Device Update for Azure IoT Hub の一般的な問題をトラブルシューティングする | Microsoft Docs
description: このドキュメントでは、Device Update for IoT Hub で発生する可能性のある多くの問題を解決するために役立つヒントとテクニックの一覧を示します。
author: lichris
ms.author: lichris
ms.date: 2/17/2021
ms.topic: troubleshooting
ms.service: iot-hub-device-update
ms.openlocfilehash: 3c1f60b214397b1f97e0157b5beca32d504102d6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102030632"
---
# <a name="device-update-for-iot-hub-troubleshooting-guide"></a>Device Update for IoT Hub のトラブルシューティング ガイド

このドキュメントでは、デバイス更新ユーザーが報告した一般的な質問と問題のいくつかを一覧表示します。 デバイス更新がパブリック プレビューの過程を経るに従って、このトラブルシューティング ガイドは、新しい質問と解決策によって定期的に更新されます。 このトラブルシューティング ガイドに記載されていない問題が発生した場合は、その状況を文書化するために「[Microsoft サポートへの問い合わせ](#contact)」セクションを参照してください。

## <a name="importing-updates"></a><a name="import"></a>更新プログラムのインポート

### <a name="q-im-having-trouble-connecting-my-device-update-instance-to-my-iot-hub-instance"></a>Q: デバイス更新インスタンスを IoT Hub インスタンスに接続するときに問題が発生しています。
_[デバイス更新リソース](./device-update-resources.md)のドキュメントに従って、IoT Hub メッセージ ルートが正しく構成されていることを確認してください。_

### <a name="q-im-encountering-a-role-related-error-error-message-in-azure-portal-or-a-403-api-error"></a>Q: ロール関連のエラー (Azure portal でのエラー メッセージまたは 403 API エラー) が発生しています。
_アクセス許可が正しく構成されていない可能性があります。[デバイス更新アクセス制御](./device-update-control-access.md)のドキュメントに従って、アクセス許可が正しく構成されていることを確認してください。_

### <a name="q-im-encountering-a-500-type-error-when-importing-content-to-the-device-update-service"></a>Q: コンテンツをデバイス更新サービスにインポートするときに 500 タイプのエラーが発生しています。
_500 の範囲内のエラー コードは、デバイス更新サービスでの問題を示している可能性があります。5 分待ってから再試行してください。引き続き同じエラーが発生する場合は、「[Microsoft サポートへの問い合わせ](#contact)」セクションの手順に従って Microsoft にサポート リクエストを提出してください。_

### <a name="q-im-encountering-an-error-code-when-importing-content-and-would-like-to-parse-it"></a>Q: コンテンツをインポートするときにエラー コードが発生しているため、それを解析したいと考えています。
_エラー コードの解析については、[デバイス更新エラー コード](./device-update-error-codes.md)のドキュメントを参照してください。_

## <a name="device-failures"></a><a name="device-failure"></a>デバイス障害

### <a name="q-how-can-i-ensure-my-device-is-connected-to-device-update-for-iot-hub"></a>Q: デバイスが Device Update for IoT Hub に接続されていることを確認するにはどうすればよいですか?
_デバイスがデバイス更新に接続されていることは、それが Azure portal のコンプライアンス ビューの "グループに属していない" デバイスのセクションに表示されているかどうかをチェックすることによって確認できます。_

### <a name="q-one-or-more-of-my-devices-is-failing-to-update"></a>Q: 1 つ以上のデバイスが更新に失敗しています。
_デバイス更新が失敗する根本原因として多くのものが考えられます。デバイスが 1) IoT Hub インスタンスに接続されていること、2) デバイス更新インスタンスに接続されていること、3) 配信の最適化 (DO) サービスが実行されていることを検証してください。デバイスに 3 つのすべてが当てはまる場合は、「[Microsoft サポートへの問い合わせ](#contact)」セクションの手順に従って Microsoft にサポート リクエストを提出してください。_

## <a name="deploying-an-update"></a><a name="deploy"></a> 更新プログラムのデプロイ

### <a name="q-ive-deployed-an-update-to-my-devices-but-the-compliance-status-says-it-isnt-on-the-latest-update-what-should-i-do"></a>Q: デバイスに更新プログラムをデプロイしましたが、コンプライアンス状態には最新の更新プログラムになっていないことが示されています。 どうすればよいですか。
_デバイスのコンプライアンス状態は、更新されるまでに最大 5 分かかることがあります。しばらく待ってから再確認してください。_
### <a name="q-my-devices-deployment-status-shows-incompatible-what-should-i-do"></a>Q: デバイスのデプロイの状態には互換性がないことが示されてます。どうすればよいですか?
_デバイスを IoT Hub に接続した後に、対象デバイスの製造元とモデルのプロパティが変更されたため、デバイスが現在のデプロイの更新プログラム コンテンツと互換性がないと見なされるようになっている可能性があります。_

_[ADU Core インターフェイス](./device-update-plug-and-play.md)をチェックして、デバイスがデバイス更新サービスにどのような製造元とモデルを報告しているかを確認し、それがデプロイされる更新プログラム コンテンツの [インポート マニフェスト](./import-concepts.md)で指定した製造元とモデルに一致していることを確認してください。特定のデバイスのこれらのプロパティは、[デバイス更新構成ファイル](./device-update-configuration-file.md)を使用して変更できます。_

### <a name="q-i-see-my-deployment-is-in-active-stage-but-none-of-my-devices-are-in-progress-with-the-update-what-should-i-do"></a>Q: デプロイは "アクティブ" ステージと表示されていますが、どのデバイスでも更新プログラムが "進行中" になっていません。 どうすればよいですか。
_デプロイ開始日が将来に設定されていないことを確認してください。新しいデプロイを作成した場合、デプロイ開始日は、明示的に変更しない限り、保護機能として既定で翌日に設定されます。デプロイ開始日に達するまで待つか、または進行中のデプロイをキャンセルし、目的の開始日で新しいデプロイを作成することができます。_

### <a name="q-im-trying-to-group-my-devices-but-i-dont-see-the-tag-in-the-drop-down-when-creating-a-group"></a>Q: デバイスをグループ化しようとしていますが、グループを作成するときに、ドロップダウンにタグが表示されません。
_[デバイス更新リソース](./device-update-resources.md)のドキュメントに従って、IoT Hub でメッセージ ルートを正しく構成していることを確認してください。ルートを構成した後、再びデバイスにタグを付ける必要があります。_

_別の根本原因は、デバイスを Device Update for IoT Hub に接続する前にタグを適用したことである可能性があります。デバイスが既にデバイス更新に接続されていることを確認してください。デバイスが Device Update for IoT Hub に接続されていることは、それがコンプライアンス ビューの "グループに属していない" デバイスに表示されているかどうかをチェックすることによって確認できます。別の値のタグを一時的に追加した後、デバイスが接続されたら、再び目的のタグを追加します。_

_Device Provisioning Service (DPS) を使用している場合は、デバイスの作成プロセス中にではなく、そのデバイスがプロビジョニングされた後にタグを付けるようにしてください。既にデバイスの作成手順中にデバイスにタグを付けている場合は、そのデバイスがプロビジョニングされた後に一時的に別の値でタグを付けた後、再び目的のタグを追加する必要があります。_

### <a name="q-my-deployment-completed-successfully-but-some-devices-failed-to-update"></a>Q: デプロイは正常に完了しましたが、一部のデバイスが更新に失敗しました。
_これは、障害が発生したデバイス上のクライアント側のエラーが原因で発生した可能性があります。このトラブルシューティング ガイドの「デバイス障害」セクションを参照してください。_

### <a name="q-i-encountered-an-error-in-the-ux-when-trying-to-initiate-a-deployment"></a>Q: デプロイを開始しようとしたときに、UX でエラーが発生しました。
_これは、サービスまたは UX のバグか、API のアクセス許可の問題が原因で発生した可能性があります。「[Microsoft サポートへの問い合わせ](#contact)」セクションの手順に従って Microsoft にサポート リクエストを提出してください。_

### <a name="q-i-started-a-deployment-but-it-isnt-reaching-an-end-state"></a>Q: デプロイを開始しましたが、終了状態に達していません。
_これは、サービスのパフォーマンスの問題、サービスのバグ、またはクライアントのバグが原因で発生した可能性があります。10 分経ってから、デプロイを再試行してください。同じ問題が発生した場合は、デバイス ログをプルし、このトラブルシューティング ガイドの「デバイス障害」セクションを参照してください。引き続き同じ問題が発生する場合は、「[Microsoft サポートへの問い合わせ](#contact)」セクションの手順に従って Microsoft にサポート リクエストを提出してください。_

## <a name="downloading-updates-onto-devices"></a><a name="download"></a> デバイスへの更新プログラムのダウンロード

### <a name="q-how-do-i-resume-a-download-when-a-device-has-reconnected-after-a-period-of-disconnection"></a>Q: デバイスが一定の期間切断された後に再接続されたとき、ダウンロードを再開するにはどうすればよいですか?
_接続が 24 時間以内に復元された場合、ダウンロードは自動的に再開されます。24 時間が経過すると、ユーザーがダウンロードを再開する必要があります。_
## <a name="using-microsoft-connected-cache-mcc"></a><a name="mcc"></a> Microsoft 接続キャッシュ (MCC) の使用

### <a name="q-i-am-encountering-an-issue-when-attempting-to-deploy-the-mcc-module-on-my-iot-edge-device"></a>Q: IoT Edge デバイスに MCC モジュールをデプロイしようとするときに問題が発生しています。
_IoT Edge デバイスへの Edge モジュールのデプロイについては、[IoT Edge のドキュメント]()を参照してください。MCC モジュールが IoT Edge デバイス上で正常に実行されているかどうかは、 http://localhost:5100/Summary に移動することによって確認できます。_
### <a name="q-one-of-my-iot-devices-is-attempting-to-download-an-update-through-mcc-but-is-failing"></a>Q: いずれかの IoT デバイスが MCC を使用して更新プログラムをダウンロードしようとしていますが、エラーが発生しています。
_IoT デバイスが MCC に接続できない原因になっている可能性のある問題がいくつかあります。問題を診断するには、障害が発生しているデバイスから DO クライアントおよび Nginx ログを収集してください (クライアント ログを収集する手順については、「[Microsoft サポートへの問い合わせ](#contact)」セクションを参照)。_

_デバイスは、使用している URL が許可されていないために、その MCC モジュールに渡すためのコンテンツをインターネットからプルできない可能性があります。それを判定するには、Azure portal で IoT Edge の環境変数を確認する必要があります。_
## <a name="contacting-microsoft-support"></a><a name="contact"></a> Microsoft サポートへの問い合わせ

上記の FAQ を使用して解決できない問題が発生している場合は、Azure portal インターフェイス経由で Microsoft サポートにサポート リクエストを提出できます。 問題がどのカテゴリに属しているように指定するかによっては、Microsoft サポートがその問題を調査するために役立つ追加データを収集して共有するよう求められることがあります。 

各データ型を収集する方法については、以下を参照してください。 [getDevices]() を使用すると、API のペイロード応答内の追加情報を確認できます。

さらに、問題の根本原因を絞り込むために次の情報が役立つ場合があります。
* どのような種類のデバイスを更新しようとしているか (Azure Percept、IoT Edge ゲートウェイなど)
* どのようなデバイス更新クライアントの種類を使用しているか (イメージ ベース、パッケージ ベース、シミュレーター)
* デバイスでどのような OS 実行しているか
* デバイスのアーキテクチャに関する詳細
* 以前にデバイス更新を正常に使用してデバイスを更新したかどうか

上記の情報のいずれかが使用可能な場合は、それを問題の説明に含めてください。

### <a name="collecting-client-logs"></a>クライアント ログの収集

* Raspberry Pi デバイスでは、2 つのログ セットが次の場所にあります。

    ```markdown
    /adu/logs
    ```

    ```markdown
    /var/cache/do-client-lite/log
    ```

* パッケージ化されたクライアントの場合、ログは次の場所にあります。

    ```markdown
    /var/log/adu
    ```

    ```markdown
    /var/cache/do-client-lite/log
    ```

* シミュレーターの場合、ログは次の場所にあります。

    ```markdown
    /tmp/aduc-logs
    ```

### <a name="error-codes"></a>エラー コード
更新プログラムのインポート、デバイス障害、または更新プログラムのデプロイに関連した問題を報告する場合は、エラー コードを指定するよう求められることがあります。

エラー コードは、[ADUCoreInterface](./device-update-plug-and-play.md) インターフェイスを参照することによって取得できます。 自己診断とトラブルシューティングのためにエラー コードを解析する方法については、[デバイス更新エラー コード](./device-update-error-codes.md)のドキュメントを参照してください。

### <a name="trace-id"></a>トレース ID
更新プログラムのインポートまたはデプロイに関連した問題を報告する場合は、トレース ID を指定するよう求められることがあります。

特定のユーザー アクションのトレース ID は、API 応答内で、または Azure portal ユーザー インターフェイスの [インポート履歴] セクションで見つけることができます。 

現在、デプロイ アクションのトレース ID には API 応答経由でのみアクセスできます。

### <a name="deployment-id"></a>デプロイ ID
更新プログラムのデプロイに関連した問題を報告する場合は、デプロイ ID を指定するよう求められることがあります。

デプロイ ID は、API を呼び出してデプロイを開始するときにユーザーによって作成されます。

現在、Azure portal ユーザー インターフェイスから開始されたデプロイのデプロイ ID は自動的に生成され、ユーザーには表示されません。

### <a name="iot-hub-instance-name"></a>IoT Hub インスタンス名
デバイス障害または更新プログラムのデプロイに関連した問題を報告する場合は、IoT Hub インスタンスの名前を指定するよう求められることがあります。

IoT Hub 名は、最初にプロビジョニングされたときにユーザーによって選択されます。

### <a name="device-update-account-name"></a>デバイス更新アカウント名
更新プログラムのインポート、デバイス障害、または更新プログラムのデプロイに関連した問題を報告する場合は、デバイス更新アカウントの名前を指定するよう求められることがあります。

デバイス更新アカウント名は、最初にサービスにサインアップするときにユーザーによって選択されます。 詳細については、[デバイス更新リソース](./device-update-resources.md)のドキュメントを参照してください。

### <a name="device-update-instance-name"></a>デバイス更新インスタンス名
更新プログラムのインポート、デバイス障害、または更新プログラムのデプロイに関連した問題を報告する場合は、デバイス更新インスタンスの名前を指定するよう求められることがあります。

デバイス更新インスタンス名は、最初にプロビジョニングされたときにユーザーによって選択されます。 詳細については、[デバイス更新リソース](./device-update-resources.md)のドキュメントを参照してください。

### <a name="device-id"></a>Device ID
デバイス障害または更新プログラムのデプロイに関連した問題を報告する場合は、デバイス ID を指定するよう求められることがあります。

デバイス ID は、デバイスが最初にプロビジョニングされたときに顧客によって定義されます。 これはまた、そのデバイスのデバイス ツインからも取得できます。

### <a name="update-id"></a>更新プログラム ID
更新プログラムのデプロイに関連した問題を報告する場合は、更新プログラム ID を指定するよう求められることがあります。

更新プログラム ID は、デプロイを開始するときに顧客によって定義されます。

### <a name="nginx-logs"></a>Nginx ログ
Microsoft 接続キャッシュに関連した問題を報告する場合は、Nginx ログを指定するよう求められることがあります。

### <a name="adu-conftxt"></a>ADU-conf.txt
更新プログラムのデプロイに関連した問題を報告する場合は、デバイス更新構成ファイル ("adu-conf.txt") を指定するよう求められることがあります。

この構成ファイルは省略可能であり、[デバイス更新構成](./device-update-configuration-file.md)のドキュメントの手順に従ってユーザーによって作成されます。

### <a name="import-manifest"></a>インポート マニフェスト
更新プログラムのインポートまたはデプロイに関連した問題を報告する場合は、インポート マニフェスト ファイルを指定するよう求められることがあります。

インポート マニフェストは、更新プログラム コンテンツをデバイス更新サービスにインポートするときに顧客によって作成されるファイルです。

**[次のステップ: デバイス更新エラー コードの詳細について学習する](.\device-update-error-codes.md)**