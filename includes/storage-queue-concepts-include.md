## <a name="what-is-queue-storage"></a>キュー ストレージとは
Azure キュー ストレージは、HTTP または HTTPS を使用した認証された呼び出しを介して世界中のどこからでもアクセスできる大量のメッセージを格納するためのサービスです。 キューの 1 つのメッセージの最大サイズは 64 KB で、1 つのキューには、ストレージ アカウントの合計容量の上限に達するまで、数百万のメッセージを格納できます。

キュー ストレージの一般的な用途には、次のようなものがあります。

* 非同期に処理する作業のバックログを作成する
* Azure Web ロールから worker ロールにメッセージを渡す

## <a name="queue-service-concepts"></a>キュー サービスの概念
キュー サービスには、次のコンポーネントが含まれます。

![Queue1](./media/storage-queue-concepts-include/queue1.png)

* **URL 形式:** キューは、次の URL 形式を使用してアドレス指定できます。   
    http://`<storage account>`.queue.core.windows.net/`<queue>` 
  
    次の URL を使用すると、図のいずれかのキューをアドレス指定できます。  
  
    `http://myaccount.queue.core.windows.net/images-to-download`

* **ストレージ アカウント** : Azure Storage にアクセスする場合には必ず、ストレージ アカウントを使用します。 ストレージ アカウントの容量の詳細については、 [Azure のストレージの拡張性とパフォーマンスのターゲットに関するページ](../articles/storage/storage-scalability-targets.md) を参照してください。
* **キュー:** キューは、メッセージのセットを格納します。 すべてのメッセージはキューに 格納されている必要があります。 キュー名は小文字で入力する必要があります。 キューの名前付け規則については、「 [Naming Queues and Metadata (キューとメタデータの名前付け規則)](https://msdn.microsoft.com/library/azure/dd179349.aspx)」を参照してください。
* **メッセージ:** 形式を問わず、メッセージのサイズは最大で 64 KB です。 メッセージをキューで保持できる最長時間は 7 日間です。



<!--HONumber=Nov16_HO4-->


