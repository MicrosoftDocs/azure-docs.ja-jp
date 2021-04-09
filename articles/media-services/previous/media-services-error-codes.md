---
title: Azure Media Services エラー コード | Microsoft Docs
description: Media Services でサポートされていないアクションに対する認証トークンの有効期限切れなどの問題によって、サービスから HTTP エラー コードを受け取ることがあります。 この記事では、Azure Media Services v2 API のエラー コードの概要を説明します。
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: d3a62a64-7608-4b17-8667-479b26ba0d6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 10ade5c8f34d806ca5706ccaaf4b7ff2e05ba657
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013416"
---
# <a name="azure-media-services-error-codes"></a>Azure Media Services エラー コード

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Microsoft Azure Media Services を使用する場合は、Media Services でサポートされていないアクションに対する認証トークンの有効期限切れなどの問題によって、サービスから HTTP エラー コードを受け取ることがあります。 Media Services やそれらが原因となって返されることがある **HTTP エラー コード** の一覧を次に示します。  

## <a name="400-bad-request"></a>400 Bad Request
要求には、無効な情報が含まれており、次のいずれかの理由で拒否されます。

* サポートされていない API バージョンが指定されています。 最新バージョンについては、「[Media Services REST API 開発用の設定](media-services-rest-how-to-use.md)」をご覧ください。
* Media Services の API バージョンが指定されていません。 API バージョンを指定する方法については、「[Media Services Operations REST API Reference (Media Services Operations REST API リファレンス)](/rest/api/media/operations/azure-media-services-rest-api-reference)」を参照してください。
  
  > [!NOTE]
  > .NET または Java SDK を使用して Media Services に接続する場合は、Media Services に対して何らかのアクションの実行を試行するたびに、API バージョンが指定されます。
  > 
  > 
* 未定義のプロパティが指定されています。 プロパティ名は、エラー メッセージ内にあります。 特定のエンティティのメンバーであるプロパティのみを指定することができます。 エンティティとそのプロパティの一覧については、「[Azure Media Services REST API リファレンス](/rest/api/media/operations/azure-media-services-rest-api-reference)」を参照してください。
* 無効なプロパティ値が指定されています。 プロパティ名は、エラー メッセージ内にあります。 有効なプロパティ タイプと値については、以前のリンクを参照してください。
* プロパティ値が欠落しているので、必要です。
* 指定された URL の一部には、無効な値が含まれています。
* WriteOnce プロパティの更新が試行されました。
* 指定されなかったか特定できなかったプライマリ AssetFile を使用して、入力資産のジョブの作成が試行されました。
* SAS ロケーターの更新が試行されました。 SAS ロケーターの作成または削除のみできます。 ストリーミング ロケーターを更新できます。 詳細については、「[ロケーター](/rest/api/media/operations/locator)」を参照してください。
* サポートされていない操作またはクエリが送信されました。

## <a name="401-unauthorized"></a>401 権限がありません
次のいずれかの理由で、(承認される前に) 要求を認証できませんでした。

* 認証ヘッダーがありません。
* 不正な認証ヘッダーの値。
  * トークンの有効期限が切れています。 
  * トークンには、無効な署名が含まれています。

## <a name="403-forbidden"></a>403 許可されていません
次のいずれかの理由で要求が受け入れられません。

* Media Services アカウントが見つからないか、削除されています。
* Media Services アカウントが無効になり、要求のタイプが HTTP GET ではありません。 サービス操作でも、403 応答が返されます。
* 認証トークンにユーザーの資格情報 AccountName または SubscriptionId、あるいはその両方が含まれていません。 この情報は、Azure 管理ポータルの Media Services アカウント向け Media Services UI 拡張機能で見つけることができます。
* リソースにアクセスすることはできません。
  
  * Media Services アカウントでは利用できない MediaProcessor の使用が試行されました。
  * Media Services で定義されている JobTemplate の更新が試行されました。
  * 他の Media Services アカウントのロケーターの上書きが試行されました。
  * 他の Media Services アカウントの ContentKey の上書きが試行されました。
* サービス クォータが Media Services アカウントに到達したため、リソースを作成できませんでした。 サービス クォータの詳細については、「[クォータと制限](media-services-quotas-and-limitations.md)」をご覧ください。

## <a name="404-not-found"></a>404 見つかりません
次のいずれかの理由で、リソースでは要求が受け入れられません。

* 存在しないエンティティの更新が試行されました。
* 存在しないエンティティの削除が試行されました。
* 存在しないエンティティにリンクするエンティティの作成が試行されました。
* 存在しないエンティティの GET が試行されました。
* Media Services アカウントに関連付けられていないストレージ アカウントの指定が試行されました。  

## <a name="409-conflict"></a>409 競合
次のいずれかの理由で要求が受け入れられません。

* 複数の AssetFile は、資産内で指定した名前を持ちます。
* 資産内で 2 つ目のプライマリ AssetFile の作成が試行されました。
* 既に使用されている指定された Id を使って ContentKey の作成が試行されました。
* 既に使用されている指定された Id を使ってロケーターの作成が試行されました。
* 複数の IngestManifestFile は、IngestManifest 内で指定した名前を持ちます。
* ストレージ暗号化資産に 2 つ目のストレージ暗号化 ContentKey のリンクが試行されました。
* 資産に、同じ ContentKey のリンクが試行されました。
* ストレージ コンテナーが存在しないか、もはや資産に関連付けられていない資産へのロケーターの作成が試行されました。
* 既に使用中の 5 つのロケーターを持つ資産へのロケーターの作成が試行されました。 (Azure Storage は、1 つのストレージ コンテナーに 5 つの共有アクセス ポリシーの制限を適用します。)
* 資産のストレージ アカウントを IngestManifestAsset にリンクするのと、親 IngestManifest で使用されるストレージ アカウントは同じではありません。  

## <a name="500-internal-server-error"></a>500 内部サーバー エラー
要求の処理中に、Media Services では、処理の続行を妨げる何らかのエラーが発生します。 この場合は、次のいずれかの理由が考えられます。

* Media Services アカウントのサービス クォータ情報が一時的に利用できないために、資産またはジョブの作成が失敗します。
* アカウントのストレージ アカウント情報が一時的に利用できないために、資産または IngestManifest blob ストレージ コンテナーの作成が失敗します。
* その他の予期しないエラー。

## <a name="503-service-unavailable"></a>503 サービス利用不可
サーバは現在、要求を受信することができません。 このエラーは、サービスへの過度な要求によって引き起こされることがあります。 Media Services 調整メカニズムが、サービスに対して過剰な要求を作成するアプリケーションのリソース使用を制限する。

> [!NOTE]
> 503 エラーが発生した理由についての詳細な情報を取得するには、エラー メッセージとエラー コード文字列を確認してください。 このエラーは常に調整を意味するものではありません。
> 
> 

可能な状態の説明は、次のとおりです。

* 「サーバーがビジー状態です。 このタイプの要求より前の実行には {0} 秒以上かかりました。」
* 「サーバーがビジー状態です。 1 秒あたり {0} 以上の要求を調整することができます。」
* 「サーバーがビジー状態です。 {1} 秒内に {0} を越える要求を調整することができます。」

このエラーを処理するには、指数関数的バックオフ再試行ロジックを使用することをお勧めします。 つまり、連続するエラー応答への再試行の間、徐々により長い待機時間を要するようになります。  詳細については、「[一時的なエラー処理アプリケーション ブロック](/previous-versions/msp-n-p/hh680905(v=pandp.50))」を参照してください。

> [!NOTE]
> [Azure Media Services SDK for .Net](https://github.com/Azure/azure-sdk-for-media-services/tree/master) を使用している場合は、503 エラーの再試行ロジックが SDK によって実装されています。  
> 
> 

## <a name="see-also"></a>参照
[Media Services 管理エラー コード](/rest/api/media/)

## <a name="next-steps"></a>次のステップ
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
