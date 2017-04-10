# [概要](media-services-overview.md)
## [概念](media-services-concepts.md)

# 作業開始
## [アカウントの作成と管理](media-services-portal-create-account.md)
## [開発環境のセットアップ](media-services-set-up-computer.md)
###[.NET](media-services-dotnet-how-to-use.md)
###[REST ()](media-services-rest-how-to-use.md)  
## プログラムによる接続
### [.NET](media-services-dotnet-connect-programmatically.md)
### [REST ()](media-services-rest-connect-programmatically.md)

## ビデオ オン デマンドの配信
### [ポータル](media-services-portal-vod-get-started.md)
### [.NET SDK](media-services-dotnet-get-started.md)
### [Java](media-services-java-how-to-use.md)
### [REST ()](media-services-rest-get-started.md)
## ライブ ストリーミングの実行
### [ポータル](media-services-portal-live-passthrough-get-started.md)
### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)

# 方法
## Manage
### アカウント
#### [PowerShell](media-services-manage-with-powershell.md)
#### [REST ()](/rest/api/media/mediaservice)
### エンティティ
#### [.NET](media-services-dotnet-manage-entities.md)
#### [REST ()](media-services-rest-manage-entities.md)
### [ストリーミング エンドポイント](media-services-streaming-endpoints-overview.md)
#### [ポータル](media-services-portal-manage-streaming-endpoints.md)
#### [.NET](media-services-dotnet-manage-streaming-endpoints.md)
### Storage
#### [ストレージ アクセス キーをローリングした後に Media Services を更新する](media-services-roll-storage-access-keys.md)
#### [複数のストレージ アカウントで資産を管理する](meda-services-managing-multiple-storage-accounts.md)
### [クォータと制限](media-services-quotas-and-limitations.md)

## コンテンツをアップロードする
### ファイルをアカウントにアップロードする
#### [ポータル](media-services-portal-upload-files.md)
#### [.NET](media-services-dotnet-upload-files.md)
#### [REST ()](media-services-rest-upload-files.md)
### [Aspera を使用した大きなファイルのアップロード](media-services-upload-files-with-aspera.md)
### [StorSimple を使用したファイルのアップロード](media-services-upload-files-from-storsimple.md)
### [既存の BLOB のコピー](media-services-copying-existing-blob.md)

## [コンテンツのエンコード](media-services-encode-asset.md)
### [エンコーダーの比較](media-services-compare-encoders.md)
### [エンコードの速度と同時実行の管理](media-services-manage-encoding-speed.md)
### Media Encoder Standard (MES)
#### [Media Encoder Standard の形式とコーデック](media-services-media-encoder-standard-formats.md)
#### [MES を使用したビットレート ラダーの自動生成](media-services-autogen-bitrate-ladder-with-mes.md)
#### Media Encoder Standard を使用したエンコード
##### [ポータル](media-services-portal-encode.md)
##### [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
##### [REST ()](media-services-rest-encode-asset.md)
#### [MES による高度なエンコード](media-services-advanced-encoding-with-mes.md)
##### [Media Encoder Standard のプリセットのカスタマイズ](media-services-custom-mes-presets-with-dotnet.md)
##### [.NET で Media Encoder Standard を使用してサムネイルを生成する方法](media-services-dotnet-generate-thumbnail-with-mes.md)
##### [Media Encoder Standard を使用してビデオをトリミングする](media-services-crop-video.md)
#### MES スキーマ
##### [Media Encoder Standard スキーマ](media-services-mes-schema.md)
##### [入力メタデータ](media-services-input-metadata-schema.md)
##### [出力メタデータ](media-services-output-metadata-schema.md)
#### [MES のプリセット](media-services-mes-presets-overview.md) 
##### [H264 Multiple Bitrate 1080p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md)
##### [H264 Multiple Bitrate 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md)
##### [H264 Multiple Bitrate 16x9 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md)
##### [H264 Multiple Bitrate 16x9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md)
##### [H264 Multiple Bitrate 16x9 for iOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md)
##### [H264 Multiple Bitrate 4K Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md)
##### [H264 Multiple Bitrate 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md)
##### [H264 Multiple Bitrate 4x3 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md)
##### [H264 Multiple Bitrate 4x3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md)
##### [H264 Multiple Bitrate 4x3 for iOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md)
##### [H264 Multiple Bitrate 720p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md)
##### [H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md)
##### [H264 Single Bitrate 1080p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md)
##### [H264 Single Bitrate 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md)
##### [H264 Single Bitrate 16x9 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md)
##### [H264 Single Bitrate 16x9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md)
##### [H264 Single Bitrate 4K Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md)
##### [H264 Single Bitrate 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md)
##### [H264 Single Bitrate 4x3 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md)
##### [H264 Single Bitrate 4x3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md)
##### [H264 Single Bitrate 720p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md)
##### [H264 Single Bitrate 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md)
##### [H264 Single Bitrate 720p for Android](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md)
##### [H264 Single Bitrate High Quality SD for Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md)
##### [H264 Single Bitrate Low Quality SD for Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md)
### メディア エンコーダー Premium ワークフロー
#### [メディア エンコーダー プレミアム ワークフローの形式とコーデック](media-services-premium-workflow-encoder-formats.md)
#### メディア エンコーダー プレミアム ワークフローを使用したエンコード
##### [メディア エンコーダー Premium ワークフロー](media-services-encode-with-premium-workflow.md)
##### [メディア エンコーダー Premium ワークフローのチュートリアル](media-services-media-encoder-premium-workflow-tutorials.md)
##### [ワークフロー デザイナーを使用して高度なエンコード ワークフローを作成する](media-services-workflow-designer.md)
##### [複数入力の場合の Premium ワークフロー](media-services-media-encoder-premium-workflow-multiplefilesinput.md)
### [fMP4 チャンクを生成するタスクを作成する](media-services-generate-fmp4-chunks.md)
### メディア プロセッサ
#### [.NET](media-services-get-media-processor.md)
#### [REST ()](media-services-rest-get-media-processor.md)
### [エラー コード](media-services-encoding-error-codes.md)
### 非推奨
#### [静的パッケージと暗号化](media-services-static-packaging.md)

## [ライブ ストリーミング](media-services-manage-channels-overview.md)
### [オンプレミス エンコーダー](media-services-live-streaming-with-onprem-encoders.md)
#### [ポータル](media-services-portal-live-passthrough-get-started.md)
#### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)
#### [REST ()](https://docs.microsoft.com/rest/api/media/operations/channel)
### [クラウド エンコーダーを使用したライブ ストリーミング](media-services-manage-live-encoder-enabled-channels.md)
#### [ポータル](media-services-portal-creating-live-encoder-enabled-channel.md)
#### [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
### [オンプレミス エンコーダーをクラウド エンコーダーと使用するための構成](media-services-live-encoders-overview.md)
#### [Elemental Live エンコーダー](media-services-configure-elemental-live-encoder.md)
#### [FMLE エンコーダー](media-services-configure-fmle-live-encoder.md)
#### [NewTek TriCaster エンコーダー](media-services-configure-tricaster-live-encoder.md)
#### [Wirecast エンコーダー](media-services-configure-wirecast-live-encoder.md)
### [長時間にわたって実行される操作の処理](media-services-dotnet-long-operations.md)
### [フラグメント化 MP4 ライブ取り込みの仕様](media-services-fmp4-live-ingest-overview.md)

## [保護](media-services-content-protection-overview.md)
### [ポータルでコンテンツ保護を構成する](media-services-portal-protect-content.md)
### [ストリームの AES-128 クリア キーを構成する](media-services-protect-with-aes128.md)
### [REST を使用してストレージ暗号化でコンテンツを暗号化する](media-services-rest-storage-encryption.md)
### [Media Services PlayReady ライセンス テンプレートの概要](media-services-playready-license-template-overview.md)
### [Widevine ライセンス テンプレートの概要](media-services-widevine-license-template-overview.md)
### [DRM ライセンス配信](media-services-deliver-keys-and-licenses.md)
### [パートナーを使用して Media Services に Widevine ライセンスを配信する](media-services-licenses-partner-integration.md)
### [PlayReady または Widevine の動的共通暗号化を使用する](media-services-protect-with-drm.md)
### [Apple FairPlay で保護された HLS コンテンツをストリーミングする](media-services-protect-hls-with-fairplay.md)
### [CENC とマルチ DRM およびアクセス制御](media-services-cenc-with-multidrm-access-control.md)
### [Axinom を使用して Media Services に Widevine ライセンスを配信する](media-services-axinom-integration.md)
### [castLabs を使用して Media Services に Widevine ライセンスを配信する](media-services-castlabs-integration.md)

### 資産の配信
#### 資産配信ポリシーを構成する
##### [.NET](media-services-dotnet-configure-asset-delivery-policy.md)
##### [REST ()](media-services-rest-configure-asset-delivery-policy.md)
### ContentKeys を作成する
#### [.NET](media-services-dotnet-create-contentkey.md)
#### [REST ()](media-services-rest-create-contentkey.md)
### コンテンツ キー承認ポリシーを構成する
#### [ポータル](media-services-portal-configure-content-key-auth-policy.md)
#### [.NET](media-services-dotnet-configure-content-key-auth-policy.md)
#### [REST ()](media-services-rest-configure-content-key-auth-policy.md)

## [分析](media-services-analytics-overview.md)
### [ポータルを使用したメディアの分析](media-services-portal-analyze.md)
### [Indexer 2 による処理](media-services-process-content-with-indexer2.md)
### [Indexer による処理](media-services-index-content.md)
### [Hyperlapse による処理](media-services-hyperlapse-content.md)
### [Face Detector による処理](media-services-face-and-emotion-detection.md)
### [Motion Detector による処理](media-services-motion-detection.md)
### [Face Redactor による処理](media-services-face-redaction.md)
#### [Face Redactor のチュートリアル](media-services-redactor-walkthrough.md)
### [Video Thumbnails による処理](media-services-video-summarization.md)
### [OCR による処理](media-services-video-optical-character-recognition.md)

## [テレメトリの構成](media-services-telemetry-overview.md)
###[.NET](media-services-dotnet-telemetry.md)
###[REST ()](media-services-rest-telemetry.md)

## スケール
### [メディア処理](media-services-scale-media-processing-overview.md)
#### [ポータル](media-services-portal-scale-media-processing.md)
#### [.NET](media-services-dotnet-encoding-units.md)
#### [REST ()](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
### ストリーミング エンドポイント
#### [ポータル](media-services-portal-scale-streaming-endpoints.md)

## [コンテンツの配信](media-services-deliver-content-overview.md)
### [ダイナミック パッケージ](media-services-dynamic-packaging-overview.md)
### [フィルターと動的マニフェストの概要](media-services-dynamic-manifest-overview.md)
#### [.NET を使用したフィルターの作成](media-services-dotnet-dynamic-manifest.md)
#### [REST を使用したフィルターの作成](media-services-rest-dynamic-manifest.md)
### [Media Services Extension での CDN キャッシュ ポリシー](../cdn/cdn-caching-policy.md?toc=%2fazure%2fmedia-services%2ftoc.json)
### コンテンツを発行する
#### [ポータル](media-services-portal-publish.md)
#### [.NET](media-services-deliver-streaming-content.md)
#### [REST ()](media-services-rest-deliver-streaming-content.md)
### [ダウンロードで配信する](media-services-deliver-asset-download.md)
### [フェールオーバー ストリーミング シナリオ](media-services-implement-failover.md)

## 使用
### [既存のプレーヤーを使用したメディアの再生](media-services-playback-content-with-existing-players.md)
### [Media Player を使用したメディアの再生](media-services-develop-video-players.md)
### その他の再生オプション
#### [Smooth Streaming Windows ストア アプリケーション](media-services-build-smooth-streaming-apps.md)
#### [DASH.js を使用した HTML5 アプリケーション](media-services-embed-mpeg-dash-in-html5.md)
#### [Adobe Open Source Media Framework プレーヤー](media-services-use-osmf-smooth-streaming-client-plugin.md)
### [クライアント側での広告の挿入](media-services-inserting-ads-on-client-side.md)
### [Microsoft Smooth Streaming Client Porting Kit のライセンス](media-services-sspk.md)

## 統合
### [Media Services を使用する Azure 関数の使用](media-services-dotnet-how-to-use-azure-functions.md)
### [Media Services を使用する Azure 関数の例](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)

## 監視
### ジョブの進行状況をチェックする
#### [REST ()](media-services-rest-check-job-progress.md)
#### [ポータル](media-services-portal-check-job-progress.md)
#### [.NET](media-services-check-job-progress.md)
### [Queue Storage を使用したジョブ通知の監視](media-services-dotnet-check-job-progress-with-queues.md)
### [webhook を使用したジョブ通知の監視](media-services-dotnet-check-job-progress-with-webhooks.md)

## トラブルシューティング
### [よく寄せられる質問](media-services-frequently-asked-questions.md)
### [ライブ ストリーミングのトラブルシューティング ガイド](media-services-troubleshooting-live-streaming.md)
### [エラー コード](media-services-error-codes.md)
### [再試行ロジック](media-services-retry-logic-in-dotnet-sdk.md)

# リファレンス
## [PowerShell (Resource Manager)](/powershell/resourcemanager/azurerm.media/v0.3.1/azurerm.media)
## [PowerShell (サービス管理)](/powershell/servicemanagement/azure.compute/v3.1.0/azure.compute)
## [.NET](/dotnet/api/microsoft.windowsazure.mediaservices.client)
## [REST ()](/rest/api/media)

# リソース
## [リリース ノート](media-services-release-notes.md)
## [料金](https://azure.microsoft.com/pricing/details/media-services/)
## [Azure Media Services コミュニティ](media-services-community.md)
## [ビデオ](https://azure.microsoft.com/resources/videos/index/?services=media-services)
