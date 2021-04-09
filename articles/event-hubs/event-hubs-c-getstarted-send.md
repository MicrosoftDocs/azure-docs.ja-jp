---
title: 'クイックスタート: C を使用してイベントを送信する - Azure Event Hubs'
description: 'クイックスタート: この記事では、Azure Event Hubs にイベントを送信する C アプリケーションを作成するためのチュートリアルを提供します。'
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: bfe1ca1a45f7b33d7431aed13446d8d72f79fb90
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "85315667"
---
# <a name="quickstart-send-events-to-azure-event-hubs-using-c"></a>クイックスタート: C を使用して Azure Event Hubs にイベントを送信する

## <a name="introduction"></a>はじめに
Azure Event Hubs はビッグ データ ストリーミング プラットフォームであり、毎秒数百万のイベントを受け取って処理できるイベント インジェスト サービスです。 Event Hubs では、分散されたソフトウェアやデバイスから生成されるイベント、データ、またはテレメトリを処理および格納できます。 イベント ハブに送信されたデータは、任意のリアルタイム分析プロバイダーやバッチ処理/ストレージ アダプターを使用して、変換および保存できます。 Event Hubs の詳しい概要については、[Event Hubs の概要](event-hubs-about.md)と [Event Hubs の機能](event-hubs-features.md)に関するページをご覧ください。

このチュートリアルでは、C のコンソール アプリケーションを使用して、イベント ハブへイベントを送信する方法について説明します。 

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、以下が必要です。

* C の開発環境。 このチュートリアルでは、Ubuntu 14.04 での Azure Linux VM 上の GCC スタックを想定しています。
* [Microsoft Visual Studio](https://www.visualstudio.com/)。
* **Event Hubs 名前空間とイベント ハブを作成する**。 [Azure portal](https://portal.azure.com) を使用して Event Hubs 型の名前空間を作成し、アプリケーションがイベント ハブと通信するために必要な管理資格情報を取得します。 名前空間とイベント ハブを作成するには、[こちらの記事](event-hubs-create.md)の手順に従います。 次の記事の手順に従って、イベント ハブ用のアクセス キーの値を取得します。[接続文字列を取得する](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)。 このチュートリアルの後半で記述するコードで、このアクセス キーを使用します。 既定のキー名は次のとおりです:**RootManageSharedAccessKey**。

## <a name="write-code-to-send-messages-to-event-hubs"></a>Event Hubs にメッセージを送信するコードの記述
このセクションでは、イベントをイベント ハブに送信する C アプリの作成方法を示します。 コードでは、[Apache Qpid プロジェクト](https://qpid.apache.org/)の Proton AMQP ライブラリを使用します。 これは、[この例](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504)に示すように、C の AMQP を Service Bus キューとトピックと共に使用するのに似ています。 詳しくは、[Qpid Proton のドキュメント](https://qpid.apache.org/proton/index.html)をご覧ください。

1. [Qpid AMQP Messenger ページ](https://qpid.apache.org/proton/messenger.html)で、環境に応じた Qpid Proton をインストールするための指示に従ってください。
2. Proton ライブラリをコンパイルするには、次のパッケージをインストールします。
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. [Qpid Proton ライブラリ](https://qpid.apache.org/proton/index.html)をダウンロードし、次のように抽出します。
   
    ```shell
    wget https://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. build ディレクトリを作成し、コンパイルとインストールを行います。
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. 作業ディレクトリに **sender.c** と呼ばれる新しいファイルを次のコードを使用して作成します。 SAS キー/名前、イベント ハブの名前、名前空間の値を忘れずに置換してください。 前に作成した **SendRule** のキーの URL でエンコードされたバージョンも代入する必要があります。 [ここ](https://www.w3schools.com/tags/ref_urlencode.asp)で URL でエンコードすることができます。
   
    ```c
    #include "proton/message.h"
    #include "proton/messenger.h"
   
    #include <getopt.h>
    #include <proton/util.h>
    #include <sys/time.h>
    #include <stddef.h>
    #include <stdio.h>
    #include <string.h>
    #include <unistd.h>
    #include <stdlib.h>
   
    #define check(messenger)                                                     \
      {                                                                          \
        if(pn_messenger_errno(messenger))                                        \
        {                                                                        \
          printf("check\n");                                                     \
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); \
        }                                                                        \
      }
   
    pn_timestamp_t time_now(void)
    {
      struct timeval now;
      if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
      return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
    }  
   
    void die(const char *file, int line, const char *message)
    {
      printf("Dead\n");
      fprintf(stderr, "%s:%i: %s\n", file, line, message);
      exit(1);
    }
   
    int sendMessage(pn_messenger_t * messenger) {
        char * address = (char *) "amqps://{SAS Key Name}:{SAS key}@{namespace name}.servicebus.windows.net/{event hub name}";
        char * msgtext = (char *) "Hello from C!";
   
        pn_message_t * message;
        pn_data_t * body;
        message = pn_message();
   
        pn_message_set_address(message, address);
        pn_message_set_content_type(message, (char*) "application/octect-stream");
        pn_message_set_inferred(message, true);
   
        body = pn_message_body(message);
        pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));
   
        pn_messenger_put(messenger, message);
        check(messenger);
        pn_messenger_send(messenger, 1);
        check(messenger);
   
        pn_message_free(message);
    }
   
    int main(int argc, char** argv) {
        printf("Press Ctrl-C to stop the sender process\n");
   
        pn_messenger_t *messenger = pn_messenger(NULL);
        pn_messenger_set_outgoing_window(messenger, 1);
        pn_messenger_start(messenger);
   
        while(true) {
            sendMessage(messenger);
            printf("Sent message\n");
            sleep(1);
        }
   
        // release messenger resources
        pn_messenger_stop(messenger);
        pn_messenger_free(messenger);
   
        return 0;
    }
    ```
6. **gcc** でファイルをコンパイルします。
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > このコードで、1 の送信ウィンドウを使用して、メッセージをできるだけ早く強制的に送信します。 アプリケーションではスループットが向上するようにメッセージをバッチ処理することをお勧めします。 この環境やその他の環境、バインドが提供されているプラットフォーム (現在は、Perl、PHP、Python、Ruby) から Qpid Proton ライブラリを使用する方法の詳細については、「[Qpid AMQP Messenger ページ](https://qpid.apache.org/proton/messenger.html)」を参照してください。

アプリケーションを実行してメッセージを Event Hubs に送信します。 

お疲れさまでした。 メッセージをイベント ハブに送信しました。

## <a name="next-steps"></a>次のステップ
次の記事を参照してください。

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Azure Event Hubs の機能と用語](event-hubs-features.md)。


<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png
