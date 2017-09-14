---
ms.assetid: 
title: "Azure Key Vault のスロットル ガイダンス | Microsoft Docs"
ms.service: key-vault
author: BrucePerlerMS
ms.author: bruceper
manager: mbaldwin
ms.date: 06/21/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adaf7026d455210db4d7ce6e7111d13c2b75374
ms.openlocfilehash: fe700e22c5323c2a0bdc315e349cd119798bcf40
ms.contentlocale: ja-jp
ms.lasthandoff: 06/22/2017

---
# <a name="azure-key-vault-throttling-guidance"></a>Azure Key Vault のスロットル ガイダンス

スロットルは、Azure サービスに対する同時呼び出し数を制限し、リソースの過剰使用を防止することを目的に開始されるプロセスです。 Azure Key Vault (AKV) は、大量の要求を処理できるように設計されていますが、 処理しきれない数の要求が発生した場合、クライアントの要求をスロットルすることで、AKV サービスのパフォーマンスと信頼性を最適な状態に保つことができます。

スロットルの制限は、シナリオによって異なります。 たとえば大量の書き込みを実行している場合、読み取りだけを実行している場合と比べて、スロットルの余地は大きくなります。

## <a name="how-does-key-vault-handle-its-limits"></a>Key Vault における制限の扱い

リソースの乱用を防ぎ、Key Vault の全クライアントのサービス品質を確保するために、Key Vault にはサービス制限があります。 サービスのしきい値を超えると、Key Vault はそれ以降、一定時間そのクライアントからの要求を制限します。 この状態になると、Key Vault からは HTTP 状態コード 429 (要求が多すぎます) が返され、要求は失敗します。 また、Key Vault によって追跡されているスロットル制限には、429 が返されて失敗した要求も加算されます。 

業務上の正当な理由でスロットル制限の引き上げを希望される場合は、Microsoft にお問い合わせください。


## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>サービス制限に対応してアプリをスロットルする方法

アプリのスロットルに関する**ベスト プラクティス**を次に示します。
- 要求あたりの操作の数を減らす。
- 要求の頻度を減らす。
- すぐに再試行しない。 
    - すべての要求が使用制限のカウント対象になります。

アプリのエラー処理を実装するときに、HTTP エラー コード 429 を使って、クライアント側でのスロットルの必要性を検出してください。 HTTP 429 エラー コードで再び要求が失敗すれば、Azure のサービス制限が引き続き適用されます。 それ以降は、推奨されているクライアント側のスロットル手法を使用して、成功するまで要求を再試行してください。

### <a name="recommended-client-side-throttling-method"></a>推奨されるクライアント側のスロットル手法

HTTP エラー コード 429 が発生したら、次のように指数関数的バックオフ手法を使ってクライアントのスロットルを開始します。

1. 1 秒待って要求を再試行
2. 引き続きスロットルされる場合は 2 秒待って要求を再試行
3. 引き続きスロットルされる場合は 4 秒待って要求を再試行
4. 引き続きスロットルされる場合は 8 秒待って要求を再試行
5. 引き続きスロットルされる場合は 16 秒待って要求を再試行

通常は、この時点で HTTP 429 応答コードは発生しなくなります。

## <a name="see-also"></a>関連項目

Microsoft Cloud のスロットルに関するさらに詳しい情報については、「[Throttling Pattern (スロットル パターン)](https://docs.microsoft.com/azure/architecture/patterns/throttling)」を参照してください。


