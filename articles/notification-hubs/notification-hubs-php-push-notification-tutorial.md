---
title: PHP から Azure Notification Hubs を使用する方法
description: PHP バックエンドから Azure Notification Hubs を使用する方法について説明します。
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: php
ms.devlang: php
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: daebf7c6a5fc9056e16b77a40ee9f90db598749f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87076636"
---
# <a name="how-to-use-notification-hubs-from-php"></a>PHP から Notification Hubs を使用する方法

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

MSDN のトピック「[Notification Hubs REST API](/previous-versions/azure/reference/dn223264(v=azure.100))」の説明にあるように Notification Hub REST インターフェイスを使用し、Java/PHP/Ruby バックエンドから Notification Hubs のすべての機能にアクセスできます。

このトピックでは、次の方法について説明します。

* PHP で Notification Hubs 機能の REST クライアントを記述します。
* 選択したモバイル プラットフォーム用の [Azure Notification Hubs を使用した iOS アプリへのプッシュ通知の送信](ios-sdk-get-started.md)に関するページに従って、バックエンド部分を PHP で実装します。

## <a name="client-interface"></a>クライアント インターフェイス

メインのクライアント インターフェイスは、[.NET Notification Hubs SDK](https://msdn.microsoft.com/library/jj933431.aspx) で使用可能なものと同じメソッドを提供できます。これを使用すると、現在このサイトで使用可能であり、インターネット上のコミュニティによって提供されたすべてのチュートリアルとサンプルを直接変換できます。

「 [PHP REST wrapper sample (PHP REST ラッパー サンプル)]」で利用可能なすべてのコードを検索できます。

たとえば、クライアントを作成する場合:

```php
$hub = new NotificationHub("connection string", "hubname");
```

iOS ネイティブ通知を送信する場合:

```php
$notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
$hub->sendNotification($notification, null);
```

## <a name="implementation"></a>実装

まだ実行していない場合は、[入門用チュートリアル] の、最後のセクションのバックエンドを実装する必要がある箇所まで手順に従ってください。
また、必要があれば「[PHP REST wrapper sample (PHP REST ラッパー サンプル)]」のコードを使用し、直接[チュートリアルの完了](#complete-tutorial)セクションに進むこともできます。

REST ラッパーすべての実装の詳細については、 [MSDN の記事](/previous-versions/azure/reference/dn530746(v=azure.100))を参照してください。 このセクションでは、Notification Hubs REST エンドポイントにアクセスするために必要な主要な手順の PHP 実装について説明します。

1. 接続文字列を解析する
2. 認証トークンを生成する
3. HTTP 呼び出しを実行する

### <a name="parse-the-connection-string"></a>接続文字列を解析する

接続文字列を解析するコンストラクターを持つクライアントを実装するメイン クラスです。

```php
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
```

### <a name="create-a-security-token"></a>セキュリティ トークンを作成する

[SAS セキュリティ トークンを作成する](/previous-versions/azure/reference/dn495627(v=azure.100)#create-sas-security-token)方法については、Azure ドキュメントを参照してください。

現在の要求の URI と接続文字列から抽出された資格情報に基づいてトークンを作成するには、`NotificationHub` クラスに `generateSasToken` メソッドを追加します。

```php
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
```

### <a name="send-a-notification"></a>通知を送信する

最初に、通知を表すクラスを定義します。

```php
class Notification {
    public $format;
    public $payload;

    # array with keynames for headers
    # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
    # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
    public $headers;

    function __construct($format, $payload) {
        if (!in_array($format, ["template", "apple", "windows", "fcm", "windowsphone"])) {
            throw new Exception('Invalid format: ' . $format);
        }

        $this->format = $format;
        $this->payload = $payload;
    }
}
```

このクラスは、ネイティブ通知の本文、またはテンプレート通知の場合は一連のプロパティ、および形式 (ネイティブなプラットフォームまたはテンプレート) とプラットフォーム固有のプロパティ (Apple の有効期限プロパティや WNS ヘッダーなど) を含む一連のヘッダーのコンテナーです。

使用可能なすべてのオプションについては、[Notification Hubs REST API のドキュメント](/previous-versions/azure/reference/dn495827(v=azure.100))および特定の通知プラットフォームの形式を参照してください。

このクラスを利用して、`NotificationHub` クラス内に送信通知メソッドを作成できます。

```php
public function sendNotification($notification, $tagsOrTagExpression="") {
    if (is_array($tagsOrTagExpression)) {
        $tagExpression = implode(" || ", $tagsOrTagExpression);
    } else {
        $tagExpression = $tagsOrTagExpression;
    }

    # build uri
    $uri = $this->endpoint . $this->hubPath . "/messages" . NotificationHub::API_VERSION;
    $ch = curl_init($uri);

    if (in_array($notification->format, ["template", "apple", "fcm"])) {
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
        throw new Exception('Error sending notification: '. $info['http_code'] . ' msg: ' . $response);
    }
} 
```

上記のメソッドは、HTTP POST 要求とともに、通知を送信するための正しい本文とヘッダーを、通知ハブの `/messages` エンドポイントに送信します。

## <a name="complete-the-tutorial"></a><a name="complete-tutorial"></a>チュートリアルの完了

ここで、PHP バックエンドから通知を送信して、使用についてのチュートリアルを完了できます。

Notification Hubs クライアントを初期化します ([入門用チュートリアル] の説明に従って接続文字列とハブ名を置き換えます)。

```php
$hub = new NotificationHub("connection string", "hubname");
```

次に、ターゲット モバイル プラットフォームに応じて送信コードを追加します。

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows ストアおよび Windows Phone 8.1 (非 Silverlight)

```php
$toast = '<toast><visual><binding template="ToastText01"><text id="1">Hello from PHP!</text></binding></visual></toast>';
$notification = new Notification("windows", $toast);
$notification->headers[] = 'X-WNS-Type: wns/toast';
$hub->sendNotification($notification, null);
```

### <a name="ios"></a>iOS

```php
$alert = '{"aps":{"alert":"Hello from PHP!"}}';
$notification = new Notification("apple", $alert);
$hub->sendNotification($notification, null);
```

### <a name="android"></a>Android

```php
$message = '{"data":{"msg":"Hello from PHP!"}}';
$notification = new Notification("fcm", $message);
$hub->sendNotification($notification, null);
```

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 および 8.1 Silverlight

```php
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
```

### <a name="kindle-fire"></a>Kindle Fire

```php
$message = '{"data":{"msg":"Hello from PHP!"}}';
$notification = new Notification("adm", $message);
$hub->sendNotification($notification, null);
```

PHP コードを実行すると、ターゲット デバイスに表示される通知が生成されます。

## <a name="next-steps"></a>次の手順

このトピックでは、Notification Hubs 用の単純な Java REST クライアントを作成する方法を示しました。 ここでは、次の操作を実行できます。

* [PHP REST wrapper sample (PHP REST ラッパー サンプル)]をすべてダウンロードします。サンプルには上記のコード、および登録管理のコードがすべて含まれています。
* 引き続き、「[ニュース速報チュートリアル]」で Notification Hubs のタグ付け機能について学習してください。
* 個別ユーザーへの通知のプッシュについては、「[ユーザーへの通知チュートリアル]」で学習してください。

詳細については、 [PHP デベロッパー センター](https://azure.microsoft.com/develop/php/)も参照してください。

[PHP REST wrapper sample (PHP REST ラッパー サンプル)]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[Azure Notification Hubs を使用して iOS アプリにプッシュ通知を送信する](ios-sdk-get-started.md))
