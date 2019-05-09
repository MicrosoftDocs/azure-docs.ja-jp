---
title: Azure Key Vault のスロットル ガイダンス
description: Key Vault の調整では同時呼び出しの数を制限して、リソースの乱用を防ぎます。
services: key-vault
documentationcenter: ''
author: msmbaldwin
manager: barbkess
tags: ''
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: mbaldwin
ms.openlocfilehash: 0f8aafce4c4feeed742504db84664e4dfd472ca6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59787551"
---
# <a name="azure-key-vault-throttling-guidance"></a>Azure Key Vault のスロットル ガイダンス

スロットルは、Azure サービスに対する同時呼び出し数を制限し、リソースの過剰使用を防止することを目的に開始されるプロセスです。 Azure Key Vault (AKV) は、大量の要求を処理できるように設計されていますが、 処理しきれない数の要求が発生した場合、クライアントの要求をスロットルすることで、AKV サービスのパフォーマンスと信頼性を最適な状態に保つことができます。

スロットルの制限は、シナリオによって異なります。 たとえば大量の書き込みを実行している場合、読み取りだけを実行している場合と比べて、スロットルの余地は大きくなります。

## <a name="how-does-key-vault-handle-its-limits"></a>Key Vault における制限の扱い

リソースの乱用を防ぎ、Key Vault の全クライアントのサービス品質を確保するために、Key Vault にはサービス制限があります。 サービスのしきい値を超えると、Key Vault はそれ以降、一定時間そのクライアントからの要求を制限します。 この状態になると、Key Vault からは HTTP 状態コード 429 (要求が多すぎます) が返され、要求は失敗します。 また、Key Vault によって追跡されているスロットル制限には、429 が返されて失敗した要求も加算されます。 

業務上の正当な理由でスロットル制限の引き上げを希望される場合は、Microsoft にお問い合わせください。


## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>サービス制限に対応してアプリをスロットルする方法

サービスがスロットルされているときに実践すべき**ベスト プラクティス**を次に示します。
- 要求あたりの操作の数を減らす。
- 要求の頻度を減らす。
- すぐに再試行しない。 
    - すべての要求が使用制限のカウント対象になります。

アプリのエラー処理を実装するときに、HTTP エラー コード 429 を使って、クライアント側でのスロットルの必要性を検出してください。 HTTP 429 エラー コードで再び要求が失敗すれば、Azure のサービス制限が引き続き適用されます。 それ以降は、推奨されているクライアント側のスロットル手法を使用して、成功するまで要求を再試行してください。

エクスポネンシャル バックオフを実装するコードを次に示します。 
```
    public sealed class RetryWithExponentialBackoff
    {
        private readonly int maxRetries, delayMilliseconds, maxDelayMilliseconds;

        public RetryWithExponentialBackoff(int maxRetries = 50,
            int delayMilliseconds = 200,
            int maxDelayMilliseconds = 2000)
        {
            this.maxRetries = maxRetries;
            this.delayMilliseconds = delayMilliseconds;
            this.maxDelayMilliseconds = maxDelayMilliseconds;
        }

        public async Task RunAsync(Func<Task> func)
        {
            ExponentialBackoff backoff = new ExponentialBackoff(this.maxRetries,
                this.delayMilliseconds,
                this.maxDelayMilliseconds);
            retry:
            try
            {
                await func();
            }
            catch (Exception ex) when (ex is TimeoutException ||
                ex is System.Net.Http.HttpRequestException)
            {
                Debug.WriteLine("Exception raised is: " +
                    ex.GetType().ToString() +
                    " –Message: " + ex.Message +
                    " -- Inner Message: " +
                    ex.InnerException.Message);
                await backoff.Delay();
                goto retry;
            }
        }
    }

    public struct ExponentialBackoff
    {
        private readonly int m_maxRetries, m_delayMilliseconds, m_maxDelayMilliseconds;
        private int m_retries, m_pow;

        public ExponentialBackoff(int maxRetries, int delayMilliseconds,
            int maxDelayMilliseconds)
        {
            m_maxRetries = maxRetries;
            m_delayMilliseconds = delayMilliseconds;
            m_maxDelayMilliseconds = maxDelayMilliseconds;
            m_retries = 0;
            m_pow = 1;
        }

        public Task Delay()
        {
            if (m_retries == m_maxRetries)
            {
                throw new TimeoutException("Max retry attempts exceeded.");
            }
            ++m_retries;
            if (m_retries < 31)
            {
                m_pow = m_pow << 1; // m_pow = Pow(2, m_retries - 1)
            }
            int delay = Math.Min(m_delayMilliseconds * (m_pow - 1) / 2,
                m_maxDelayMilliseconds);
            return Task.Delay(delay);
        }
    }
```


このコードは、クライアントの C\# アプリケーションで使用するのが簡単です。 次の例では HttpClient クラスを使用して方法を示します。

```csharp
public async Task<Cart> GetCartItems(int page)
{
    _apiClient = new HttpClient();
    //
    // Using HttpClient with Retry and Exponential Backoff
    //
    var retry = new RetryWithExponentialBackoff();
    await retry.RunAsync(async () =>
    {
        // work with HttpClient call
        dataString = await _apiClient.GetStringAsync(catalogUrl);
    });
    return JsonConvert.DeserializeObject<Cart>(dataString);
}
```

このコードは概念実証にしか適していないことに注意してください。 

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

