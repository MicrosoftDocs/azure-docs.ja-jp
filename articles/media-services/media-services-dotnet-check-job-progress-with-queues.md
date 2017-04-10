---
title: "Azure Queue Storage を使用して .NET で Media Services ジョブ通知を監視する | Microsoft Docs"
description: "Azure キュー ストレージを使用して Media Services ジョブ通知を監視する方法について説明します。 コード サンプルは C# で記述され、Media Services SDK for .NET を利用しています。"
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: f535d0b5-f86c-465f-81c6-177f4f490987
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 0ddac6ef30439e6bea04d63c41662bc49309de2c
ms.lasthandoff: 03/22/2017


---
# <a name="use-azure-queue-storage-to-monitor-media-services-job-notifications-with-net"></a>Azure キュー ストレージを使用して .NET で Media Services ジョブ通知を監視する
ジョブを実行する際には、多くの場合、ジョブの進行状況を追跡する手段が必要になります。 進行状況は Azure Queue Storage を使用して Azure Media Services ジョブ通知を監視する (この記事で説明) ことにより確認できます。 また、「[.NET を使用したジョブの進行状況の監視](media-services-check-job-progress.md)」の説明に従って、**StateChanged** イベント ハンドラーを定義することもできます。  

## <a name="use-queue-storage-to-monitor-media-services-job-notifications"></a>Queue Storage を使用して Media Services ジョブ通知を監視する
メディア ジョブの処理時に、Media Services は [Queue Storage](../storage/storage-dotnet-how-to-use-queues.md) に通知を配信することができます。 このトピックでは、キュー ストレージからこれらの通知メッセージを取得する方法について説明します。

キュー ストレージに配信されたメッセージは、世界中のどこからでもアクセスできます。 Queue Storage メッセージング アーキテクチャは、信頼性と拡張性に優れています。 Queue Storage のメッセージのポーリングには、他のメソッドを使用することをお勧めします。

Media Services 通知をリッスンする 1 つの一般的なシナリオは、エンコード ジョブの完了後にいくつかの追加タスクを実行する必要があるコンテンツ管理システムを開発している場合です (ワークフローの次の手順のトリガーや、コンテンツの発行など)。

### <a name="considerations"></a>考慮事項
Queue Storage を使用する Media Services アプリケーションを開発する場合は、次の点を考慮してください。

* Queue Storage では、先入先出法 (FIFO) の順次配送を保証しません。 詳細については、「 [Azure キューと Service Bus キューの比較](https://msdn.microsoft.com/library/azure/hh767287.aspx)」をご覧ください。
* Queue Storage はプッシュ サービスではありません。 キューをポーリングする必要があります。
* キューの数に制限はありません。 詳細については、「 [Queue サービスの REST API](https://docs.microsoft.com/rest/api/storageservices/fileservices/Queue-Service-REST-API)」をご覧ください。
* Queue Storage には、注意すべきいくつかの制限事項と特性があります。 これらについては、[Azure キューと Azure Service Bus キューの比較](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted)に関する記事で説明しています。

### <a name="code-example"></a>コード例
このセクションのコード例では、次の処理を行います。

1. 通知メッセージの形式にマップする **EncodingJobMessage** クラスを定義します。 このコードは、キューから受信したメッセージを **EncodingJobMessage** 型のオブジェクトに逆シリアル化します。
2. Media Services とストレージのアカウント情報を app.config ファイルから読み込みます。 コード例ではこの情報を使用して、**CloudMediaContext** オブジェクトと **CloudQueue** オブジェクトを作成します。
3. エンコード ジョブに関する通知メッセージを受信するキューを作成します。
4. キューにマップされる通知エンドポイントを作成します。
5. ジョブに通知エンドポイントを添付し、エンコード ジョブを送信します。 複数の通知エンドポイントをジョブに添付できます。
6. **NotificationJobState.FinalStatesOnly** を **AddNew** メソッドに渡します (この例では、ジョブ処理の最終状態にのみ関心があります)。

        job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly, _notificationEndPoint);
7. **NotificationJobState.All** を渡すと、次のすべての状態変更通知 (キューに登録、スケジュール済み、処理中、完了) を受信します。 ただし、前述のように、Queue Storage では順次配送を保証しません。 メッセージの順序を指定するには、**Timestamp** プロパティ (次の例の **EncodingJobMessage** 型で定義) を使用します。 メッセージが重複している可能性があります。 重複を確認するには、**ETag プロパティ** (**EncodingJobMessage** 型で定義) を使用します。 一部の状態変更通知がスキップされる可能性もあります。
8. 10 秒ごとにキューをチェックし、ジョブが完了状態になるまで待機します。 処理が終了したら、メッセージを削除します。
9. キューと通知エンドポイントを削除します。

> [!NOTE]
> ジョブの状態を監視するお勧めの方法は、次の例に示すように、通知メッセージをリッスンすることです。
>
> または、 **IJob.State** プロパティを使用してジョブの状態を確認することもできます。  **IJob** の状態が **[完了]** に設定される前に、ジョブの完了に関する通知メッセージが到着する場合があります。 **IJob.State** プロパティは、多少遅延しますが正確な状態を反映します。
>
>

    using System;
    using System.Linq;
    using System.Configuration;
    using System.IO;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Web;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;
    using System.Runtime.Serialization.Json;

    namespace JobNotification
    {
        public class EncodingJobMessage
        {
            // MessageVersion is used for version control.
            public String MessageVersion { get; set; }

            // Type of the event. Valid values are
            // JobStateChange and NotificationEndpointRegistration.
            public String EventType { get; set; }

            // ETag is used to help the customer detect if
            // the message is a duplicate of another message previously sent.
            public String ETag { get; set; }

            // Time of occurrence of the event.
            public String TimeStamp { get; set; }

            // Collection of values specific to the event.

            // For the JobStateChange event the values are:
            //     JobId - Id of the Job that triggered the notification.
            //     NewState- The new state of the Job. Valid values are:
            //          Scheduled, Processing, Canceling, Cancelled, Error, Finished
            //     OldState- The old state of the Job. Valid values are:
            //          Scheduled, Processing, Canceling, Cancelled, Error, Finished

            // For the NotificationEndpointRegistration event the values are:
            //     NotificationEndpointId- Id of the NotificationEndpoint
            //          that triggered the notification.
            //     State- The state of the Endpoint.
            //          Valid values are: Registered and Unregistered.

            public IDictionary<string, object> Properties { get; set; }
        }

        class Program
        {
            private static CloudMediaContext _context = null;
            private static CloudQueue _queue = null;
            private static INotificationEndPoint _notificationEndPoint = null;

            private static readonly string _singleInputMp4Path =
                Path.GetFullPath(@"C:\supportFiles\multifile\BigBuckBunny.mp4");

            static void Main(string[] args)
            {
                // Get the values from app.config file.
                string mediaServicesAccountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
                string mediaServicesAccountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];
                string storageConnectionString = ConfigurationManager.AppSettings["StorageConnectionString"];


                string endPointAddress = Guid.NewGuid().ToString();

                // Create the context.
                _context = new CloudMediaContext(mediaServicesAccountName, mediaServicesAccountKey);

                // Create the queue that will be receiving the notification messages.
                _queue = CreateQueue(storageConnectionString, endPointAddress);

                // Create the notification point that is mapped to the queue.
                _notificationEndPoint =
                        _context.NotificationEndPoints.Create(
                        Guid.NewGuid().ToString(), NotificationEndPointType.AzureQueue, endPointAddress);


                if (_notificationEndPoint != null)
                {
                    IJob job = SubmitEncodingJobWithNotificationEndPoint(_singleInputMp4Path);
                    WaitForJobToReachedFinishedState(job.Id);
                }

                // Clean up.
                _queue.Delete();      
                _notificationEndPoint.Delete();
           }


            static public CloudQueue CreateQueue(string storageAccountConnectionString, string endPointAddress)
            {
                CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageAccountConnectionString);

                // Create the queue client
                CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

                // Retrieve a reference to a queue
                CloudQueue queue = queueClient.GetQueueReference(endPointAddress);

                // Create the queue if it doesn't already exist
                queue.CreateIfNotExists();

                return queue;
            }


            public static IJob SubmitEncodingJobWithNotificationEndPoint(string inputMediaFilePath)
            {
                // Declare a new job.
                IJob job = _context.Jobs.Create("My MP4 to Smooth Streaming encoding job");

                //Create an encrypted asset and upload the mp4.
                IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted,
                    inputMediaFilePath);

                // Get a media processor reference, and pass to it the name of the
                // processor to use for the specific task.
                IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

                // Create a task with the conversion details, using a configuration file.
                ITask task = job.Tasks.AddNew("My encoding Task",
                    processor,
                    "Adaptive Streaming",
                    Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.None);

                // Specify the input asset to be encoded.
                task.InputAssets.Add(asset);

                // Add an output asset to contain the results of the job.
                task.OutputAssets.AddNew("Output asset",
                    AssetCreationOptions.None);

                // Add a notification point to the job. You can add multiple notification points.  
                job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly,
                    _notificationEndPoint);

                job.Submit();

                return job;
            }

            public static void WaitForJobToReachedFinishedState(string jobId)
            {
                int expectedState = (int)JobState.Finished;
                int timeOutInSeconds = 600;

                bool jobReachedExpectedState = false;
                DateTime startTime = DateTime.Now;
                int jobState = -1;

                while (!jobReachedExpectedState)
                {
                    // Specify how often you want to get messages from the queue.
                    Thread.Sleep(TimeSpan.FromSeconds(10));

                    foreach (var message in _queue.GetMessages(10))
                    {
                        using (Stream stream = new MemoryStream(message.AsBytes))
                        {
                            DataContractJsonSerializerSettings settings = new DataContractJsonSerializerSettings();
                            settings.UseSimpleDictionaryFormat = true;
                            DataContractJsonSerializer ser = new DataContractJsonSerializer(typeof(EncodingJobMessage), settings);
                            EncodingJobMessage encodingJobMsg = (EncodingJobMessage)ser.ReadObject(stream);

                            Console.WriteLine();

                            // Display the message information.
                            Console.WriteLine("EventType: {0}", encodingJobMsg.EventType);
                            Console.WriteLine("MessageVersion: {0}", encodingJobMsg.MessageVersion);
                            Console.WriteLine("ETag: {0}", encodingJobMsg.ETag);
                            Console.WriteLine("TimeStamp: {0}", encodingJobMsg.TimeStamp);
                            foreach (var property in encodingJobMsg.Properties)
                            {
                                Console.WriteLine("    {0}: {1}", property.Key, property.Value);
                            }

                            // We are only interested in messages
                            // where EventType is "JobStateChange".
                            if (encodingJobMsg.EventType == "JobStateChange")
                            {
                                string JobId = (String)encodingJobMsg.Properties.Where(j => j.Key == "JobId").FirstOrDefault().Value;
                                if (JobId == jobId)
                                {
                                    string oldJobStateStr = (String)encodingJobMsg.Properties.
                                                                Where(j => j.Key == "OldState").FirstOrDefault().Value;
                                    string newJobStateStr = (String)encodingJobMsg.Properties.
                                                                Where(j => j.Key == "NewState").FirstOrDefault().Value;

                                    JobState oldJobState = (JobState)Enum.Parse(typeof(JobState), oldJobStateStr);
                                    JobState newJobState = (JobState)Enum.Parse(typeof(JobState), newJobStateStr);

                                    if (newJobState == (JobState)expectedState)
                                    {
                                        Console.WriteLine("job with Id: {0} reached expected state: {1}",
                                            jobId, newJobState);
                                        jobReachedExpectedState = true;
                                        break;
                                    }
                                }
                            }
                        }
                        // Delete the message after we've read it.
                        _queue.DeleteMessage(message);
                    }

                    // Wait until timeout
                    TimeSpan timeDiff = DateTime.Now - startTime;
                    bool timedOut = (timeDiff.TotalSeconds > timeOutInSeconds);
                    if (timedOut)
                    {
                        Console.WriteLine(@"Timeout for checking job notification messages,
                                            latest found state ='{0}', wait time = {1} secs",
                            jobState,
                            timeDiff.TotalSeconds);

                        throw new TimeoutException();
                    }
                }
            }

            static private IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
            {
                var asset = _context.Assets.Create("UploadSingleFile_" + DateTime.UtcNow.ToString(),
                    assetCreationOptions);

                var fileName = Path.GetFileName(singleFilePath);

                var assetFile = asset.AssetFiles.Create(fileName);

                Console.WriteLine("Created assetFile {0}", assetFile.Name);
                Console.WriteLine("Upload {0}", assetFile.Name);

                assetFile.Upload(singleFilePath);
                Console.WriteLine("Done uploading of {0}", assetFile.Name);

                return asset;
            }

            static private IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
            {
                var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

                if (processor == null)
                    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

                return processor;
            }
        }
    }

上記の例では、次の出力が生成されました。 値は異なる場合があります。

    Created assetFile BigBuckBunny.mp4
    Upload BigBuckBunny.mp4
    Done uploading of BigBuckBunny.mp4

    EventType: NotificationEndPointRegistration
    MessageVersion: 1.0
    ETag: e0238957a9b25bdf3351a88e57978d6a81a84527fad03bc23861dbe28ab293f6
    TimeStamp: 2013-05-14T20:22:37
        NotificationEndPointId: nb:nepid:UUID:d6af9412-2488-45b2-ba1f-6e0ade6dbc27
        State: Registered
        Name: dde957b2-006e-41f2-9869-a978870ac620
        Created: 2013-05-14T20:22:35

    EventType: JobStateChange
    MessageVersion: 1.0
    ETag: 4e381f37c2d844bde06ace650310284d6928b1e50101d82d1b56220cfcb6076c
    TimeStamp: 2013-05-14T20:24:40
        JobId: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54
        JobName: My MP4 to Smooth Streaming encoding job
        NewState: Finished
        OldState: Processing
        AccountName: westeuropewamsaccount
    job with Id: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54 reached expected
    State: Finished


## <a name="next-step"></a>次のステップ
Media Services のラーニング パスを確認します。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

