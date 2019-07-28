---
title: Azure Media Services のパッケージ化と配信元エラー | Microsoft Docs
description: このトピックでは、Azure Media Services ストリーミング エンドポイント (配信元) サービスから通知されることがあるエラーについて説明します。
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: juliako
ms.openlocfilehash: ebcda6026f79bc88df91471d8be88316ba57bfc6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65411370"
---
# <a name="streaming-endpoint-origin-errors"></a>ストリーミング エンドポイント (配信元) エラー 

このトピックでは、Azure Media Services [ストリーミング エンドポイント サービス](streaming-endpoint-concept.md)から通知されることがあるエラーについて説明します。

## <a name="400-bad-request"></a>400 Bad Request

要求に無効な情報が含まれており、エラー コードと次のいずれかの理由で拒否されています。

|エラー コード|16 進値 |エラーの説明|
|---|---|---|
|MPE_BAD_URL_SYNTAX |0x80890201|URL 構文または形式エラー。 例として、無効な型の要求、無効なフラグメント、または無効なトラックなどがあります。 |
|MPE_ENC_ENCRYPTION_NOT_SPECIFIED_IN_URL |0x8088024C|要求の URL に暗号化タグが含まれていません。 CMAF 要求では、URL 内に暗号化タグが必要です。 1 つ以上の暗号化の種類で構成されているその他のプロトコルでも、あいまいさを排除するために暗号化タグが必要です。 |
|MPE_STORAGE_BAD_URL_SYNTAX |0x808900E9|要求を満たすためのストレージに対する要求が、無効な要求エラーで失敗しました。 |

## <a name="403-forbidden"></a>403 許可されていません

次のいずれかの理由で要求が受け入れられません。

|エラー コード|16 進値 |エラーの説明|
|---|---|---|
|MPE_STORAGE_AUTHENTICATION_FAILED |0x808900EA|要求を満たすためのストレージに対する要求が、認証エラーで失敗しました。 これは、ストレージ キーがローテーションされ、サービスでストレージ キーを同期できなかった場合に発生する可能性があります。 <br/><br/>Azure portal の [[ヘルプとサポート]](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) に移動し、Azure サポートに連絡してください。|
|MPE_STORAGE_INSUFFICIENT_ACCOUNT_PERMISSIONS |0x808900EB |ストレージ操作エラー。アカウントの権限不足のためにアクセスに失敗しました。 |
|MPE_STORAGE_ACCOUNT_IS_DISABLED |0x808900EC |ストレージ アカウントが無効なため、要求を満たすためのストレージに対する要求が失敗しました。 |
|MPE_STORAGE_AUTHENTICATION_FAILURE |0x808900F3 |ストレージ操作エラー。一般的なエラーのためにアクセスに失敗しました。 |
|MPE_OUTPUT_FORMAT_BLOCKED |0x80890207 |StreamingPolicy の構成により、出力形式がブロックされています。 |
|MPE_ENC_ENCRYPTION_REQUIRED |0x8088021E |コンテンツの暗号化が必要です。出力形式用の配信ポリシーが必要です。 |
|MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY |0x8088024D |配信ポリシーの設定に暗号化が設定されていません。 |

## <a name="404-not-found"></a>404 見つかりません

もう存在していないリソースを操作しようとしています。 たとえば、リソースは既に削除されている可能性があります。

|エラー コード|16 進値 |エラーの説明|
|---|---|---|
|MPE_EGRESS_TRACK_NOT_FOUND |0x80890209 |要求されたトラックが見つかりません。 |
|MPE_RESOURCE_NOT_FOUND |0x808901F9 |要求されたリソースが見つかりません。 |
|MPE_UNAUTHORIZED |0x80890244 |アクセスが承認されていません。 |
|MPE_EGRESS_TIMESTAMP_NOT_FOUND |0x8089020A |要求されたタイムスタンプが見つかりません。 |
|MPE_EGRESS_FILTER_NOT_FOUND |0x8089020C |要求された動的マニフェスト フィルターが見つかりません。 |
|MPE_FRAGMENT_BY_INDEX_NOT_FOUND |0x80890252 |要求されたフラグメント インデックスが有効な範囲を超えています。 |
|MPE_LIVE_MEDIA_ENTRIES_NOT_FOUND |0x80890254 |moov バッファーを取得するためのライブ メディア エントリが見つかりません。 |
|MPE_FRAGMENT_TIMESTAMP_NOT_FOUND |0x80890255 |特定のトラックで要求された時間のフラグメントが見つかりません。<br/><br/>そのフラグメントがストレージに存在していない可能性があります。 フラグメントが存在する可能性があるプレゼンテーションの別のレイヤーを試してください。 |
|MPE_MANIFEST_MEDIA_ENTRY_NOT_FOUND |0x80890256 |マニフェストで要求されたビットレートのメディア エントリが見つかりません。 <br/><br/>プレーヤーで、マニフェストに存在しなかった特定のビットレートのビデオ トラックが要求された可能性があります。|
|MPE_METADATA_NOT_FOUND |0x80890257 |マニフェストで特定のメタデータを見つけることができません。またはストレージからリベースを見つけることができません。 |
|MPE_STORAGE_RESOURCE_NOT_FOUND |0x808900ED |ストレージ操作エラー。リソースが見つかりません。 |

## <a name="409-conflict"></a>409 競合

`PUT` または `POST` 操作で指定されたリソースの ID が、既存のリソースで使用されています。 この問題を解決するには、リソースの別の ID を使用します。

|エラー コード|16 進値 |エラーの説明|
|---|---|---|
|MPE_STORAGE_CONFLICT  |0x808900EE  |ストレージ操作エラー。競合エラーです。  |

## <a name="410"></a>410

|エラー コード|16 進値 |エラーの説明|
|---|---|---|
|MPE_FILTER_FORCE_END_LEFT_EDGE_CROSSED_DVR_WINDOW|0x80890263|ライブ ストリーミングでは、forceEndTimestamp を持つフィルターが true に設定されている場合、開始または終了タイムスタンプは、現在の DVR ウィンドウの外部に設定されます。|

## <a name="412-precondition-failure"></a>412 Precondition Failure

操作で、サーバーで利用できるバージョンとは異なる eTag が指定されました。つまり、オプティミスティック同時実行制御エラーです。 リソースの最新バージョンを読み取り、要求の eTag を更新した後、要求を再試行してください。

|エラー コード|16 進値 |エラーの説明|
|---|---|---|
|MPE_FRAGMENT_NOT_READY |0x80890200 |要求されたフラグメントは準備ができていません。|
|MPE_STORAGE_PRECONDITION_FAILED| 0x808900EF|ストレージ操作エラー。前提条件エラーです。|

## <a name="415-unsupported-media-type"></a>415 Unsupported Media Type

クライアントによって送信されたペイロード形式が、サポートされていない形式になっています。

|エラー コード|16 進値 |エラーの説明|
|---|---|---|
|MPE_ENC_ALREADY_ENCRYPTED| 0x8088021F| 既に暗号化されているコンテンツに暗号化を適用すべきではありません。|
|MPE_ENC_INVALID_INPUT_ENCRYPTION_FORMAT|0x8088021D |入力形式の暗号化が無効です。|
|MPE_INVALID_ASSET_DELIVERY_POLICY_TYPE|0x8088021C| 配信ポリシーの種類が無効です。|
|MPE_ENC_MULTIPLE_SAME_DELIVERY_TYPE|0x8088024E |元の設定が複数の出力形式で共有されている可能性があります。|
|MPE_FORMAT_NOT_SUPPORTED|0x80890205|メディアの形式または種類がサポートされていません。 たとえば、Media Services では、64 を超える品質レベルはサポートされていません。 Media Services では、FLV ビデオ タグの複数の SPS と複数の PPS ビデオ フレームはサポートされていません。|
|MPE_INPUT_FORMAT_NOT_SUPPORTED|0x80890218| 要求された資産の入力形式はサポートされていません。 Media Services では、スムーズ (ライブ)、MP4 (VoD)、およびプログレッシブ ダウンロード形式がサポートされています。|
|MPE_OUTPUT_FORMAT_NOT_SUPPORTED|0x8089020D|要求された出力形式はサポートされていません。 Media Services では、スムーズ、DASH (CSF、CMAF)、HLS (v3、v4、CMAF)、およびプログレッシブ ダウンロード形式がサポートされています。|
|MPE_ENCRYPTION_NOT_SUPPORTED|0x80890208|サポートされていない暗号化の種類が検出されました。|
|MPE_MEDIA_TYPE_NOT_SUPPORTED|0x8089020E|要求されたメディアの種類は、出力形式でサポートされていません。 サポートされている種類は、ビデオ、オーディオ、または "SUBT" サブタイトルです。|
|MPE_MEDIA_ENCODING_NOT_SUPPORTED|0x8089020F|ソース資産メディアは、出力形式と互換性がないメディア形式でエンコードされていました。|
|MPE_VIDEO_ENCODING_NOT_SUPPORTED|0x80890210|ソース資産メディアは、出力形式と互換性がないビデオ形式でエンコードされていました。 H.264、AVC、H.265 (HEVC、hev1、または hvc1) がサポートされています。|
|MPE_AUDIO_ENCODING_NOT_SUPPORTED|0x80890211|ソース資産は、出力形式と互換性がないオーディオ形式でエンコードされていました。 サポートされているオーディオ形式は、AAC、E-AC3 (DD+)、Dolby DTS です。|
|MPE_SOURCE_PROTECTION_CONVERSION_NOT_SUPPORTED|0x80890212|ソースの保護された資産は、出力形式に変換できません。|
|MPE_OUTPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890213|保護形式が、出力形式でサポートされていません。|
|MPE_INPUT_PROTECTION_FORMAT_NOT_SUPPORTED|0x80890219|保護形式が、入力形式でサポートされていません。|
|MPE_INVALID_VIDEO_NAL_UNIT|0x80890231|無効なビデオ NAL ユニットです。たとえば、AUD になることができるのは、サンプルの最初の NAL だけです。|
|MPE_INVALID_NALU_SIZE|0x80890260|NAL ユニットのサイズが無効です。|
|MPE_INVALID_NALU_LENGTH_FIELD|0x80890261|NAL ユニットの長さ値が無効です。|
|MPE_FILTER_INVALID|0x80890236|動的マニフェスト フィルターが無効です。|
|MPE_FILTER_VERSION_INVALID|0x80890237|フィルターのバージョンが無効であるか、サポートされていません。|
|MPE_FILTER_TYPE_INVALID|0x80890238|フィルターの種類が無効です。|
|MPE_FILTER_RANGE_ATTRIBUTE_INVALID|0x80890239|無効な範囲がフィルターで指定されています。|
|MPE_FILTER_TRACK_ATTRIBUTE_INVALID|0x8089023A|無効な追跡属性がフィルターで指定されています。|
|MPE_FILTER_PRESENTATION_WINDOW_INVALID|0x8089023B|無効なプレゼンテーション ウィンドウの長さがフィルターで指定されています。|
|MPE_FILTER_LIVE_BACKOFF_INVALID|0x8089023C|無効なライブ バックオフがフィルターで指定されています。|
|MPE_FILTER_MULTIPLE_SAME_TYPE_FILTERS|0x8089023D|レガシ フィルターでは 1 つの absTimeInHNS 要素だけがサポートされています。|
|MPE_FILTER_REMOVED_ALL_STREAMS|0x8089023E|フィルターを適用した後にストリームはもうありません。|
|MPE_FILTER_LIVE_BACKOFF_OVER_DVRWINDOW|0x8089023F|ライブ バックオフが DVR ウィンドウを超えています。|
|MPE_FILTER_LIVE_BACKOFF_OVER_PRESENTATION_WINDOW|0x80890262|ライブ バックオフがプレゼンテーション ウィンドウを超えています。|
|MPE_FILTER_COMPOSITION_FILTER_COUNT_OVER_LIMIT|0x80890246|許可される最大 10 個の既定のフィルターを超えています。|
|MPE_FILTER_COMPOSITION_MULTIPLE_FIRST_QUALITY_OPERATOR_NOT_ALLOWED|0x80890248|複合要求フィルターでは、複数の最高動画品質演算子は許可されません。|
|MPE_FILTER_FIRST_QUALITY_ATTRIBUTE_INVALID|0x80890249|最高品質ビットレートの数は、1 でなければなりません。|
|MPE_HLS_SEGMENT_TOO_LARGE|0x80890243|HLS セグメント期間は、DVR ウィンドウおよび HLS バックオフの 3 分の 1 より小さくなければなりません。|
|MPE_KEY_FRAME_INTERVAL_TOO_LARGE|0x808901FE|フラグメント期間は約 20 秒以下である必要があります。そうでないと、入力品質レベルの時間が一致しません。|
|MPE_DTS_RESERVEDBOX_EXPECTED|0x80890105|DTS 固有エラー。DTS ボックスの解析中に DTSSpecficBox に存在する必要がある ReservedBox が見つかりません。|
|MPE_DTS_INVALID_CHANNEL_COUNT|0x80890106|DTS 固有エラー。DTS ボックスの解析中に、DTSSpecficBox にチャンネルが見つかりません。|
|MPE_DTS_SAMPLETYPE_MISMATCH|0x80890107|DTS 固有エラー。DTSSpecficBox でサンプルの種類が一致しません。|
|MPE_DTS_MULTIASSET_DTSH_MISMATCH|0x80890108|DTS 固有エラー。複数の資産が設定されていますが、DTSH サンプルの種類が一致しません。|
|MPE_DTS_INVALID_CORESTREAM_SIZE|0x80890109|DTS 固有エラー。コア ストリーム サイズが無効です。|
|MPE_DTS_INVALID_SAMPLE_RESOLUTION|0x8089010A|DTS 固有エラー。サンプル解像度が無効です。|
|MPE_DTS_INVALID_SUBSTREAM_INDEX|0x8089010B|DTS 固有エラー。サブストリーム拡張機能インデックスが無効です。|
|MPE_DTS_INVALID_BLOCK_NUM|0x8089010C|DTS 固有エラー。サブストリーム ブロック番号が無効です。|
|MPE_DTS_INVALID_SAMPLING_FREQUENCE|0x8089010D|DTS 固有エラー。サンプリング頻度が無効です。|
|MPE_DTS_INVALID_REFCLOCKCODE|0x8089010E|DTS 固有エラー。サブストリーム拡張機能の参照クロック コードが無効です。|
|MPE_DTS_INVALID_SPEAKERS_REMAP|0x8089010F|DTS 固有エラー。スピーカー再マッピング セットの番号が無効です。|

暗号化の記事と例については、以下をご覧ください。

- [概念: コンテンツ保護](content-protection-overview.md)
- [概念: コンテンツ キー ポリシー](content-key-policy-concept.md)
- [概念: ストリーミング ポリシー](streaming-policy-concept.md)
- [サンプル: AES 暗号化で保護する](protect-with-aes128.md)
- [サンプル: DRM で保護する](protect-with-drm.md)

フィルターのガイダンスについては、以下をご覧ください。

- [概念: 動的マニフェスト](filters-dynamic-manifest-overview.md)
- [概念: フィルター](filters-concept.md)
- [サンプル: REST API を使用してフィルターを作成する](filters-dynamic-manifest-rest-howto.md)
- [サンプル: .NET を使用してフィルターを作成する](filters-dynamic-manifest-dotnet-howto.md)
- [サンプル: CLI を使用してフィルターを作成する](filters-dynamic-manifest-cli-howto.md)

ライブの記事とサンプルについては、以下をご覧ください。

- [概念: ライブ ストリーミングの概要](live-streaming-overview.md)
- [概念: ライブ ストリーミングの概要](live-events-outputs-concept.md)
- [サンプル: ライブ ストリーミングのチュートリアル](stream-live-tutorial-with-api.md)

## <a name="416-range-not-satisfiable"></a>416 Range Not Satisfiable

|エラー コード|16 進値 |エラーの説明|
|---|---|---|
|MPE_STORAGE_INVALID_RANGE|0x808900F1|ストレージ操作エラー。HTTP 416 エラー (無効な範囲) が返されました。|

## <a name="500-internal-server-error"></a>500 内部サーバー エラー

要求の処理中に、Media Services では、処理の続行を妨げる何らかのエラーが発生します。  

|エラー コード|16 進値 |エラーの説明|
|---|---|---|
|MPE_STORAGE_SOCKET_TIMEOUT|0x808900F4|Winhttp エラー コード ERROR_WINHTTP_TIMEOUT (0x00002ee2) を受け取り、それから変換されました。|
|MPE_STORAGE_SOCKET_CONNECTION_ERROR|0x808900F5|Winhttp エラー コード ERROR_WINHTTP_CONNECTION_ERROR (0x00002efe) を受け取り、それから変換されました。|
|MPE_STORAGE_SOCKET_NAME_NOT_RESOLVED|0x808900F6|Winhttp エラー コード ERROR_WINHTTP_NAME_NOT_RESOLVED (0x00002ee7) を受け取り、それから変換されました。|
|MPE_STORAGE_INTERNAL_ERROR|0x808900E6|ストレージ操作エラー。HTTP 500 エラーのいずれかの一般的な InternalError です。|
|MPE_STORAGE_OPERATION_TIMED_OUT|0x808900E7|ストレージ操作エラー、HTTP 500 エラーのいずれかの一般的な OperationTimedOut です。|
|MPE_STORAGE_FAILURE|0x808900F2|ストレージ操作エラー。InternalError または OperationTimedOut 以外の HTTP 500 エラーです。|

## <a name="503-service-unavailable"></a>503 サービス利用不可

サーバは現在、要求を受信することができません。 このエラーは、サービスへの過度な要求によって引き起こされることがあります。 Media Services 調整メカニズムが、サービスに対して過剰な要求を作成するアプリケーションのリソース使用を制限する。

> [!NOTE]
> 503 エラーが発生した理由についての詳細な情報を取得するには、エラー メッセージとエラー コード文字列を確認してください。 このエラーは常に調整を意味するものではありません。
> 

|エラー コード|16 進値 |エラーの説明|
|---|---|---|
|MPE_STORAGE_SERVER_BUSY|0x808900E8|ストレージ操作エラー。HTTP サーバー ビジー エラー 503 を受け取りました。|

## <a name="ask-questions-give-feedback-get-updates"></a>質問する。フィードバックする。最新情報を入手する

「[Azure Media Services community (Azure Media Services コミュニティ)](media-services-community.md)」を参照して、さまざまな質問の方法、フィードバックする方法、Media Services に関する最新情報の入手方法を確認してください。

## <a name="see-also"></a>関連項目

- [エラー コードのエンコード](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
- [Azure Media Services の概念](concepts-overview.md)
- [クォータと制限](limits-quotas-constraints.md)

## <a name="next-steps"></a>次の手順

[例: .NET で ApiException から ErrorCode と Message にアクセスする](configure-connect-dotnet-howto.md#connect-to-the-net-client)
