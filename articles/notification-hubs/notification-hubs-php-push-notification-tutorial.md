---
title: "PHP で Notification Hubs を使用する方法"
description: "PHP バックエンドから Azure Notification Hubs を使用する方法について説明します。"
services: notification-hubs
documentationcenter: 
author: ysxu
manager: erikre
editor: 
ms.assetid: 0156f994-96d0-4878-b07b-49b7be4fd856
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: php
ms.devlang: php
ms.topic: article
ms.date: 06/07/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c81ab9004bf71007395d2545c4e44b07c865aa9d
ms.lasthandoff: 11/17/2016


---
# <a name="how-to-use-notification-hubs-from-php"></a>PHP から Notification Hubs を使用する方法
[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

MSDN のトピック「 [Notification Hubs の REST API](http://msdn.microsoft.com/library/dn223264.aspx)」の説明にあるように Notification Hub REST インターフェイスを使用して、Java/PHP/Ruby バックエンドから Notification Hubs のすべての機能にアクセスできます。

このトピックでは、次の方法について説明します。

* PHP で Notification Hubs 機能の REST クライアントを記述します。
* 選択したモバイル プラットフォームの「 [Notification Hubs の使用](notification-hubs-ios-apple-push-notification-apns-get-started.md) 」に従って、PHP のバックエンド部分を実装します。

## <a name="client-interface"></a>クライアント インターフェイス
メイン クライアント インターフェイスは、[.NET Notification Hubs の SDK](http://msdn.microsoft.com/library/jj933431.aspx) で利用可能なものと同じメソッドを提供します。これにより、このサイトで現在利用できる、およびインターネットのコミュニティによって随時追加されるすべてのチュートリアルとサンプルを直接変換できます。

「 [PHP REST wrapper sample (PHP REST ラッパー サンプル)]」で利用可能なすべてのコードを検索できます。

たとえば、クライアントを作成する場合:

    $hub = new NotificationHub("connection string", "hubname");    

iOS ネイティブ通知を送信する場合:

    $notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
    $hub->sendNotification($notification, null);

## <a name="implementation"></a>実装
まだ実施していない場合は、「 [Notification Hubs の使用] 」に従って最後のセクションまで進み、バックエンドを実装します。
また、必要があれば「[PHP REST wrapper sample (PHP REST ラッパー サンプル)]」のコードを使用し、直接[チュートリアルの完了](#complete-tutorial)セクションに進むこともできます。

REST ラッパーすべての実装の詳細については、 [MSDN の記事](http://msdn.microsoft.com/library/dn530746.aspx)を参照してください。 このセクションでは、Notification Hubs REST エンドポイントにアクセスするために必要な主要手順の PHP 実装について説明します。

1. 接続文字列を解析する
2. 認証トークンを生成する
3. HTTP 呼び出しを実行する

### <a name="parse-the-connection-string"></a>接続文字列を解析する
接続文字列を解析するコンストラクターを持つクライアントを実装するメイン クラスです。

    class NotificationHub {
        const API_VERSION = "?api-version=2013-10";

        private $endpoint;
        private $hubPath;
        private $sasKeyName;
        private $sasKeyValue;

        function __construct($connectionString, $hubPath) {
            $this->hubPath = $hubPath;

            $this->parseConnectionString($connectionString);
        }

        private function parseConnectionString($connectionString) {
            $parts = explode(";", $connectionString);
            if (sizeof($parts) != 3) {
                throw new Exception("Error parsing connection string: " . $connectionString);
            }

            foreach ($parts as $part) {
                if (strpos($part, "Endpoint") === 0) {
                    $this->endpoint = "https" . substr($part, 11);
                } else if (strpos($part, "SharedAccessKeyName") === 0) {
                    $this->sasKeyName = substr($part, 20);
                } else if (strpos($part, "SharedAccessKey") === 0) {
                    $this->sasKeyValue = substr($part, 16);
                }
            }
        }
    }


### <a name="create-security-token"></a>セキュリティ トークンを作成する
セキュリティ トークンの作成の詳細については、 [こちら](http://msdn.microsoft.com/library/dn495627.aspx)をご覧ください。
現在の要求の URI、および接続文字列から抽出した資格情報に基づきトークンを作成するため、 **NotificationHub** クラスに次のメソッドを追加する必要があります。

    private function generateSasToken($uri) {
        $targetUri = strtolower(rawurlencode(strtolower($uri)));

        $expires = time();
        $expiresInMins = 60;
        $expires = $expires + $expiresInMins * 60;
        $toSign = $targetUri . "\n" . $expires;

        $signature = rawurlencode(base64_encode(hash_hmac('sha256', $toSign, $this->sasKeyValue, TRUE)));

        $token = "SharedAccessSignature sr=" . $targetUri . "&sig="
                    . $signature . "&se=" . $expires . "&skn=" . $this->sasKeyName;

        return $token;
    }

### <a name="send-a-notification"></a>通知を送信する
最初に、通知を表すクラスを定義します。

    class Notification {
        public $format;
        public $payload;

        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        public $headers;

        function __construct($format, $payload) {
            if (!in_array($format, ["template", "apple", "windows", "gcm", "windowsphone"])) {
                throw new Exception('Invalid format: ' . $format);
            }

            $this->format = $format;
            $this->payload = $payload;
        }
    }

このクラスは、ネイティブ通知本体のコンテナー、またはテンプレート通知の場合にはプロパティのセットと、形式 (ネイティブ プラットフォームまたはテンプレート) およびプラットフォーム固有のプロパティを含むヘッダーのセットです (Apple 有効期限プロパティや WNS ヘッダーなど)。

利用可能なすべてのオプションについては、「 [REST API のメソッド](http://msdn.microsoft.com/library/dn495827.aspx) 」、および特定の通知プラットフォームの形式を参照してください。

このクラスを利用して、 **NotificationHub** クラス内に送信通知メソッドを作成できます。

    public function sendNotification($notification, $tagsOrTagExpression="") {
        if (is_array($tagsOrTagExpression)) {
            $tagExpression = implode(" || ", $tagsOrTagExpression);
        } else {
            $tagExpression = $tagsOrTagExpression;
        }

        # build uri
        $uri = $this->endpoint . $this->hubPath . "/messages" . NotificationHub::API_VERSION;
        $ch = curl_init($uri);

        if (in_array($notification->format, ["template", "apple", "gcm"])) {
            $contentType = "application/json";
        } else {
            $contentType = "application/xml";
        }

        $token = $this->generateSasToken($uri);

        $headers = [
            'Authorization: '.$token,
            'Content-Type: '.$contentType,
            'ServiceBusNotification-Format: '.$notification->format
        ];

        if ("" !== $tagExpression) {
            $headers[] = 'ServiceBusNotification-Tags: '.$tagExpression;
        }

        # add headers for other platforms
        if (is_array($notification->headers)) {
            $headers = array_merge($headers, $notification->headers);
        }

        curl_setopt_array($ch, array(
            CURLOPT_POST => TRUE,
            CURLOPT_RETURNTRANSFER => TRUE,
            CURLOPT_SSL_VERIFYPEER => FALSE,
            CURLOPT_HTTPHEADER => $headers,
            CURLOPT_POSTFIELDS => $notification->payload
        ));

        // Send the request
        $response = curl_exec($ch);

        // Check for errors
        if($response === FALSE){
            throw new Exception(curl_error($ch));
        }

        $info = curl_getinfo($ch);

        if ($info['http_code'] <> 201) {
            throw new Exception('Error sending notificaiton: '. $info['http_code'] . ' msg: ' . $response);
        }
    } 

上記のメソッドは、HTTP POST 要求、および通知を送信する正しい本体とヘッダーを通知ハブの /messages エンドポイントに送信します。

## <a name="a-namecomplete-tutorialacomplete-the-tutorial"></a><a name="complete-tutorial"></a>チュートリアルの完了
ここで、PHP バックエンドから通知を送信して、使用についてのチュートリアルを完了できます。

Notification Hubs クライアントを初期化します (「 [Notification Hubs の使用]」の説明に従って接続文字列とハブ名を置き換えます)。

    $hub = new NotificationHub("connection string", "hubname");    

次に、ターゲット モバイル プラットフォームに応じて送信コードを追加します。

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows ストアおよび Windows Phone 8.1 (非 Silverlight)
    $toast = '<toast><visual><binding template="ToastText01"><text id="1">Hello from PHP!</text></binding></visual></toast>';
    $notification = new Notification("windows", $toast);
    $notification->headers[] = 'X-WNS-Type: wns/toast';
    $hub->sendNotification($notification, null);

### <a name="ios"></a>iOS
    $alert = '{"aps":{"alert":"Hello from PHP!"}}';
    $notification = new Notification("apple", $alert);
    $hub->sendNotification($notification, null);

### <a name="android"></a>Android
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("gcm", $message);
    $hub->sendNotification($notification, null);

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 および 8.1 Silverlight
    $toast = '<?xml version="1.0" encoding="utf-8"?>' .
                '<wp:Notification xmlns:wp="WPNotification">' .
                   '<wp:Toast>' .
                        '<wp:Text1>Hello from PHP!</wp:Text1>' .
                   '</wp:Toast> ' .
                '</wp:Notification>';
    $notification = new Notification("windowsphone", $toast);
    $notification->headers[] = 'X-WindowsPhone-Target : toast';
    $notification->headers[] = 'X-NotificationClass : 2';
    $hub->sendNotification($notification, null);


### <a name="kindle-fire"></a>Kindle Fire
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("adm", $message);
    $hub->sendNotification($notification, null);

PHP コードを実行すると、ターゲット デバイスに表示される通知が生成されます。

## <a name="next-steps"></a>次のステップ
このトピックでは、Notification Hubs 用の単純な Java REST クライアントの作成方法を説明しました。 次は、以下を実行できます。

* [PHP REST wrapper sample (PHP REST ラッパー サンプル)]をすべてダウンロードします。サンプルには上記のコード、および登録管理のコードがすべて含まれています。
* 引き続き、「[ニュース速報チュートリアル]」で Notification Hubs のタグ付け機能について学習してください。
* 個別ユーザーへの通知のプッシュについては、「[ユーザーへの通知チュートリアル]」で学習してください。

詳細については、 [PHP デベロッパー センター](/develop/php/)も参照してください。

[PHP REST wrapper sample (PHP REST ラッパー サンプル)]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[Notification Hubs の使用]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/


