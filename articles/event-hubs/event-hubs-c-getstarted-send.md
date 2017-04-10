---
title: "C を使用して Azure Event Hubs にイベントを送信する | Microsoft Docs"
description: "C を使用して Azure Event Hubs にイベントを送信する"
services: event-hubs
documentationcenter: 
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: article
ms.date: 01/30/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: f92909e0098a543f99baf3df3197a799bc9f1edc
ms.openlocfilehash: f62c0ca57bfd15a9ad1f767fa8fd59cc73b71c43
ms.lasthandoff: 03/01/2017

---

# <a name="send-events-to-azure-event-hubs-using-c"></a>C を使用して Azure Event Hubs にイベントを送信する

## <a name="introduction"></a>はじめに
Event Hubs は、拡張性の高いインジェスト システムで、1 秒あたり何百万ものイベントを取り込むことができます。そのためアプリケーションは、接続されているデバイスやアプリケーションによって生成された大量のデータを処理し、分析できます。 Event Hubs に収集されたデータは、任意のリアルタイム分析プロバイダーやストレージ クラスターを使用して転送と格納できます。

詳細については、「[Event Hubs の概要][Event Hubs overview]」を参照してください。

このチュートリアルでは、C のコンソール アプリケーションを使用して Event Hub にイベントを送信する方法について説明します。イベントを受け取るには、左側の目次で適切な受信言語をクリックします。

このチュートリアルを最後まで行うには、以下のものが必要です。

* C の開発環境。 このチュートリアルでは、Ubuntu 14.04 での Azure Linux VM 上の GCC スタックを想定しています。
* Microsoft Visual Studio、または Visual Studio Community エディション
* アクティブな Azure アカウント。 アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。

## <a name="send-messages-to-event-hubs"></a>Event Hub へのメッセージ送信
このセクションでは、イベントを Event Hub に送信する C アプリを作成します。 [Apache Qpid プロジェクト](http://qpid.apache.org/)の Proton AMQP ライブラリを使用します。 これは、 [ここ](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504)に示すように、C の AMQP を Service Bus キューと Topics と使用するのに似ています。 詳細については、「 [Qpid Proton のドキュメント](http://qpid.apache.org/proton/index.html)」を参照してください。

1. [Qpid AMQP Messenger ページ](http://qpid.apache.org/components/messenger/index.html)の **[Installing Qpid Proton]** リンクをクリックして、環境に応じた指示に従ってください。
2. Proton ライブラリをコンパイルするには、次のパッケージをインストールします。
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. [Qpid Proton ライブラリ](http://qpid.apache.org/proton/index.html)をダウンロードし、次のように抽出します。
   
    ```shell
    wget http://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
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
5. 作業ディレクトリに **sender.c** と呼ばれる新しいファイルを次の内容で作成します。 Event Hub の名前と名前空間の名前の値を置き換えます (通常、後者は `{event hub name}-ns`です)。 前に作成した **SendRule** のキーの URL でエンコードされたバージョンも代入する必要があります。 [ここ](http://www.w3schools.com/tags/ref_urlencode.asp)で URL でエンコードすることができます。
   
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
   
    #define check(messenger)                                                     
      {                                                                          
        if(pn_messenger_errno(messenger))                                        
        {                                                                        
          printf("check\n");                                                     
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); 
        }                                                                        
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
        char * address = (char *) "amqps://SendRule:{Send Rule key}@{namespace name}.servicebus.windows.net/{event hub name}";
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
6. **gcc**でファイルをコンパイルします。
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > コンパイルしたコードで、1 の送信ウィンドウを使用して、メッセージをできるだけ早く強制的に送信します。 一般に、アプリケーションではスループットが向上するようにメッセージをバッチ処理する必要があります。 この環境やその他の環境、バインドが提供されているプラットフォーム (現在は、Perl、PHP、Python、Ruby) から Qpid Proton ライブラリを使用する方法の詳細については、「 [Qpid AMQP Messenger ページ](http://qpid.apache.org/components/messenger/index.html) 」を参照してください。


## <a name="next-steps"></a>次のステップ
Event Hubs の詳細については、次のリンク先を参照してください:

* [Event Hubs の概要](event-hubs-what-is-event-hubs.md)
* [Event Hub を作成する](event-hubs-create.md)
* [Event Hubs の FAQ](event-hubs-faq.md)

<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Event Processor Host]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Event Hubs overview]: event-hubs-overview.md
[sample application that uses Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3

