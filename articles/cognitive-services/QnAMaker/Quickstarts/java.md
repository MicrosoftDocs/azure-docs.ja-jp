---
title: REST API (V4) - Java - QnA Maker
titleSuffix: Azure Cognitive Services
description: Azure 上の Microsoft Cognitive Services で Microsoft Translator Text API の使用をすぐに開始するために役立つ Java REST ベースの情報とコード サンプルを提供します。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 08/20/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: aae59747422fd9dfb53266ee9693c3380c35f73b
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647126"
---
# <a name="how-to-use-the-qna-maker-rest-api-with-java"></a>Java で QnA Maker REST API を使用する方法 
<a name="HOLTop"></a>

この記事では、Java で [Microsoft QnA Maker API](../Overview/overview.md)  を使用し、次を行う方法について説明します。

- [新しいナレッジ ベースを作成します。](#Create)
- [既存のナレッジ ベースを更新します。](#Update)
- [ナレッジ ベースの作成または更新要求の状態を取得します。](#Status)
- [既存のナレッジ ベースを公開します。](#Publish)
- [既存のナレッジ ベースのコンテンツを置換します。](#Replace)
- [ナレッジ ベースのコンテンツをダウンロードします。](#GetQnA)
- [ナレッジ ベースを使用し、質問の回答を取得します。](#GetAnswers)
- [ナレッジ ベースに関する情報を取得します。](#GetKB)
- [指定のユーザーに属するすべてのナレッジ ベースに関する情報を取得します。](#GetKBsByUser)
- [サポート技術情報を削除します。](#Delete)
- [現在のエンドポイント キーを取得します。](#GetKeys)
- [現在のエンドポイント キーを再生成します。](#PutKeys)
- [大文字と小文字が区別される、現在の一連の単語変更を取得します。](#GetAlterations)
- [大文字と小文字が区別される、現在の一連の単語変更を置換します。](#PutAlterations)

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../../includes/cognitive-services-qnamaker-java-repo-note.md)]

## <a name="prerequisites"></a>前提条件

このコードをコンパイルして実行するには、[JDK 7 または 8](https://aka.ms/azure-jdks) が必要です。 好みの Java IDE がある場合はそれを使用してもかまいませんが、テキスト エディターで十分です。

[Cognitive Services API アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)と **Microsoft QnA Maker API** を用意している必要があります。 [Azure ダッシュボード](https://portal.azure.com/#create/Microsoft.CognitiveServices)の有料サブスクリプション キーが必要です。

<a name="Create"></a>

## <a name="create-knowledge-base"></a>サポート技術情報を作成する

次のコードでは、[Create](https://go.microsoft.com/fwlink/?linkid=2092179) メソッドを利用し、新しいサポート技術情報が作成されます。

1. 適切な IDE で新しい Java プロジェクトを作成します。
2. 次に示すコードを追加します。
3. `key` の値を、お使いのサブスクリプションで有効なアクセス キーに置き換えます。
4. プログラムを実行します。

```java
import java.io.*;
import java.lang.reflect.Type;
import java.net.*;
import java.util.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *    <dependency>
 *      <groupId>com.google.code.gson</groupId>
 *      <artifactId>gson</artifactId>
 *      <version>2.8.1</version>
 *    </dependency>
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonElement;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;
import com.google.gson.reflect.TypeToken;

/* NOTE: To compile and run this code:
1. Save this file as CreateKB.java.
2. Run:
    javac CreateKB.java -cp .;gson-2.8.1.jar -encoding UTF-8
3. Run:
    java -cp .;gson-2.8.1.jar CreateKB
*/

public class CreateKB {

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
    static String subscriptionKey = "ENTER KEY HERE";

    static String host = "https://westus.api.cognitive.microsoft.com";
    static String service = "/qnamaker/v4.0";
    static String method = "/knowledgebases/create";

// We'll serialize these classes into JSON for our request to the server.
// For the JSON request schema, see:
// https://go.microsoft.com/fwlink/?linkid=2092179
    public static class KB {
        String name;
        Question[] qnaList;
        String[] urls;
        File[] files;
    }

    public static class Question {
        Integer id;
        String answer;
        String source;
        String[] questions;
        Metadata[] metadata;
    }

    public static class Metadata {
        String name;
        String value;
    }

    public static class File {
        String fileName;
        String fileUri;
    }

// This class contains the headers and body of the HTTP response.
    public static class Response {
        Map<String, List<String>> Headers;
        String Response;

        public Response(Map<String, List<String>> headers, String response) {
            this.Headers = headers;
            this.Response = response;
        }
    }

    public static String PrettyPrint (String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

// Send an HTTP GET request.
    public static Response Get (URL url) throws Exception {
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("GET");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
        connection.setDoOutput(true);

        StringBuilder response = new StringBuilder ();
        BufferedReader in = new BufferedReader(new InputStreamReader(connection.getInputStream(), "UTF-8"));

        String line;
        while ((line = in.readLine()) != null) {
            response.append(line);
        }
        in.close();

        return new Response (connection.getHeaderFields(), response.toString());
    }

// Send an HTTP POST request.
    public static Response Post (URL url, String content) throws Exception {
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("POST");
        connection.setRequestProperty("Content-Type", "application/json");
        connection.setRequestProperty("Content-Length", content.length() + "");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
        connection.setDoOutput(true);

        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        byte[] encoded_content = content.getBytes("UTF-8");
        wr.write(encoded_content, 0, encoded_content.length);
        wr.flush();
        wr.close();

        StringBuilder response = new StringBuilder ();
        BufferedReader in = new BufferedReader(new InputStreamReader(connection.getInputStream(), "UTF-8"));
        String line;
        while ((line = in.readLine()) != null) {
            response.append(line);
        }
        in.close();

        return new Response (connection.getHeaderFields(), response.toString());
    }

// Sends the request to create the knowledge base.
    public static Response CreateKB (KB kb) throws Exception {
        URL url = new URL (host + service + method);
        System.out.println ("Calling " + url.toString() + ".");
        String content = new Gson().toJson(kb);
        return Post(url, content);
    }

// Checks the status of the request to create the knowledge base.
    public static Response GetStatus (String operation) throws Exception {
        URL url = new URL (host + service + operation);
        System.out.println ("Calling " + url.toString() + ".");
        return Get(url);
    }

// Returns a sample request to create a knowledge base.
    public static KB GetKB () {
        KB kb = new KB ();
        kb.name = "Example Knowledge Base";

        Question q = new Question();
        q.id = 0;
        q.answer = "You can use our REST APIs to manage your Knowledge Base. See here for details: https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update";
        q.source = "Custom Editorial";
        q.questions = new String[]{"How do I programmatically update my Knowledge Base?"};

        Metadata md = new Metadata();
        md.name = "category";
        md.value = "api";
        q.metadata = new Metadata[]{md};

        kb.qnaList = new Question[]{q};

        kb.urls = new String[]{"https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",     "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"};


        return kb;
    }

    public static void main(String[] args) {
        try {
// Send the request to create the knowledge base.
            Response response = CreateKB (GetKB ());
            String operation = response.Headers.get("Location").get(0);
            System.out.println (PrettyPrint (response.Response));
// Loop until the request is completed.
            Boolean done = false;
            while (true != done) {
// Check on the status of the request.
                response = GetStatus (operation);
                System.out.println (PrettyPrint (response.Response));
                Type type = new TypeToken<Map<String, String>>(){}.getType();
                Map<String, String> fields = new Gson().fromJson(response.Response, type);
                String state = fields.get ("operationState");
// If the request is still running, the server tells us how long to wait before checking the status again.
                if (state.equals("Running") || state.equals("NotStarted")) {
                    String wait = response.Headers.get ("Retry-After").get(0);
                    System.out.println ("Waiting " + wait + " seconds...");
                    Thread.sleep (Integer.parseInt(wait) * 1000);
                }
                else {
                    done = true;
                }
            }
        }
        catch (Exception e) {
            System.out.println (e.getCause().getMessage());
        }
    }
}
```

**ナレッジ ベースの応答を作成する**

成功した応答は、次の例に示すように JSON で返されます。 

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
...
{
  "operationState": "Running",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
...
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:46Z",
  "resourceLocation": "/knowledgebases/b0288f33-27b9-4258-a304-8b9f63427dad",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
```

[先頭に戻る](#HOLTop)

<a name="Update"></a>

## <a name="update-knowledge-base"></a>ナレッジ ベースを更新する

次のコードでは、[Update](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update) メソッドを利用し、既存のサポート技術情報が更新されます。

1. 適切な IDE で新しい Java プロジェクトを作成します。
2. 次に示すコードを追加します。
3. `key` の値を、お使いのサブスクリプションで有効なアクセス キーに置き換えます。
4. プログラムを実行します。

```java
import java.io.*;
import java.lang.reflect.Type;
import java.net.*;
import java.util.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *    <dependency>
 *      <groupId>com.google.code.gson</groupId>
 *      <artifactId>gson</artifactId>
 *      <version>2.8.1</version>
 *    </dependency>
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonElement;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;
import com.google.gson.reflect.TypeToken;

// Java does not natively support HTTP PATCH requests, so Apache HttpClient is required.
/*
 * HttpClient: https://hc.apache.org/downloads.cgi
 * Maven info:
 *    <dependency>
 *      <groupId>org.apache.httpcomponents</groupId>
 *      <artifactId>httpclient</artifactId>
 *      <version>4.5.5</version>
 *    </dependency>
 */
import org.apache.commons.logging.LogFactory;
import org.apache.http.client.methods.HttpPatch;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.entity.ByteArrayEntity;
import org.apache.http.Header;
import org.apache.http.HttpEntity;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;

/* NOTE: To compile and run this code:
1. Save this file as UpdateKB.java.
2. Run:
    javac UpdateKB.java -cp .;gson-2.8.1.jar;httpclient-4.5.5.jar;httpcore-4.4.9.jar;commons-logging-1.2.jar -encoding UTF-8
3. Run:
    java -cp .;gson-2.8.1.jar;httpclient-4.5.5.jar;httpcore-4.4.9.jar;commons-logging-1.2.jar UpdateKB
*/

public class UpdateKB {

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
    static String subscriptionKey = "ENTER KEY HERE";

// Replace this with a valid knowledge base ID.
    static String kb = "ENTER ID HERE";

    static String host = "https://westus.api.cognitive.microsoft.com";
    static String service = "/qnamaker/v4.0";
    static String method = "/knowledgebases/";

// We'll serialize these classes into JSON for our request to the server.
// For the JSON request schema, see:
// https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update
    public static class Request {
        Add add;
        Delete delete;
        Update update;
    }

    public static class Add {
        Question[] qnaList;
        String[] urls;
        File[] files;
    }

    public static class Delete {
        Integer[] ids;
    }

    public static class Update {
        String name;
        Question[] qnaList;
        String[] urls;
        File[] files;
    }

    public static class Question {
        Integer id;
        String answer;
        String source;
        String[] questions;
        Metadata[] metadata;
    }

    public static class Metadata {
        String name;
        String value;
    }

    public static class File {
        String fileName;
        String fileUri;
    }

// This class contains the headers and body of the HTTP response.
    public static class Response {
        Map<String, List<String>> Headers;
        String Response;

        public Response(Map<String, List<String>> headers, String response) {
            this.Headers = headers;
            this.Response = response;
        }
    }

    public static String PrettyPrint (String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

// Send an HTTP GET request.
    public static Response Get (URL url) throws Exception {
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("GET");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
        connection.setDoOutput(true);

        StringBuilder response = new StringBuilder ();
        BufferedReader in = new BufferedReader(new InputStreamReader(connection.getInputStream(), "UTF-8"));

        String line;
        while ((line = in.readLine()) != null) {
            response.append(line);
        }
        in.close();

        return new Response (connection.getHeaderFields(), response.toString());
    }

// Send an HTTP PATCH request. We use Apache HttpClient to do this, as HttpsURLConnection does not support PATCH.
    public static Response Patch (URL url, String content) throws Exception {
        HttpPatch patch = new HttpPatch(url.toString());
        // HttpPatch implements HttpMessage, which includes addHeader. See:
        // https://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/org/apache/http/client/methods/HttpPatch.html
        // https://hc.apache.org/httpcomponents-core-ga/httpcore/apidocs/org/apache/http/HttpMessage.html
        patch.addHeader("Content-Type", "application/json");
        // Note: Adding the Content-Length header causes the exception:
        // "Content-Length header already present."
        patch.addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
        // HttpPatch implements HttpEntityEnclosingRequest, which includes setEntity. See:
        // https://hc.apache.org/httpcomponents-core-ga/httpcore/apidocs/org/apache/http/HttpEntityEnclosingRequest.html
        HttpEntity entity = new ByteArrayEntity(content.getBytes("UTF-8"));
        patch.setEntity(entity);

        CloseableHttpClient httpClient = HttpClients.createDefault();
        CloseableHttpResponse response = httpClient.execute(patch);

        // CloseableHttpResponse implements HttpMessage, which includes getAllHeaders. See:
        // https://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/org/apache/http/client/methods/CloseableHttpResponse.html
        // Header implements NameValuePair. See:
        // https://hc.apache.org/httpcomponents-core-ga/httpcore/apidocs/org/apache/http/Header.html
        // https://hc.apache.org/httpcomponents-core-ga/httpcore/apidocs/org/apache/http/NameValuePair.html
        Map<String, List<String>> headers = new HashMap<String, List<String>>();
        for (Header header : response.getAllHeaders()) {
            List<String> list = new ArrayList<String>() {
                {
                    add(header.getValue());
                }
            };
            headers.put(header.getName(), list);
        }

        // CloseableHttpResponse implements HttpResponse, which includes getEntity. See:
        // https://hc.apache.org/httpcomponents-core-ga/httpcore/apidocs/org/apache/http/HttpResponse.html
        // HttpEntity implements getContent, which returns an InputStream. See:
        // https://hc.apache.org/httpcomponents-core-ga/httpcore/apidocs/org/apache/http/HttpEntity.html
        StringBuilder output = new StringBuilder ();
        BufferedReader reader = new BufferedReader(new InputStreamReader(response.getEntity().getContent(), "UTF-8"));
        String line;
        while ((line = reader.readLine()) != null) {
            output.append(line);
        }
        reader.close();

        return new Response (headers, output.toString());
    }

// Sends the request to update the knowledge base.
    public static Response UpdateKB (String kb, Request req) throws Exception {
        URL url = new URL(host + service + method + kb);
        System.out.println ("Calling " + url.toString() + ".");
        String content = new Gson().toJson(req);
        return Patch(url, content);
    }

// Checks on the status of the request to update the knowledge base.
    public static Response GetStatus (String operation) throws Exception {
        URL url = new URL (host + service + operation);
        System.out.println ("Calling " + url.toString() + ".");
        return Get(url);
    }

// Returns a sample request to update a knowledge base.
    public static Request GetRequest () {
        Request req = new Request ();

        Question q = new Question();
        q.id = 0;
        q.answer = "You can use our REST APIs to manage your Knowledge Base. See here for details: https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update";
        q.source = "Custom Editorial";
        q.questions = new String[]{"How do I programmatically update my Knowledge Base?"};

        Metadata md = new Metadata();
        md.name = "category";
        md.value = "api";
        q.metadata = new Metadata[]{md};

        req.add = new Add ();
        req.add.qnaList = new Question[]{q};
        req.add.urls = new String[]{"https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",     "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"};


        return req;
    }

    public static void main(String[] args) {
        try {
// Send the request to update the knowledge base.
            Response response = UpdateKB (kb, GetRequest ());
            String operation = response.Headers.get("Location").get(0);
            System.out.println (PrettyPrint (response.Response));
// Loop until the request is completed.
            Boolean done = false;
            while (true != done) {
// Check on the status of the request.
                response = GetStatus (operation);
                System.out.println (PrettyPrint (response.Response));
                Type type = new TypeToken<Map<String, String>>(){}.getType();
                Map<String, String> fields = new Gson().fromJson(response.Response, type);
                String state = fields.get ("operationState");
// If the request is still running, the server tells us how long to wait before checking the status again.
                if (state.equals("Running") || state.equals("NotStarted")) {
                    String wait = response.Headers.get ("Retry-After").get(0);
                    System.out.println ("Waiting " + wait + " seconds...");
                    Thread.sleep (Integer.parseInt(wait) * 1000);
                }
                else {
                    done = true;
                }
            }
        }
        catch (Exception e) {
            System.out.println (e.getCause().getMessage());
        }
    }
}
```

**ナレッジ ベースの応答を更新する**

成功した応答は、次の例に示すように JSON で返されます。 

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-04-13T01:49:48Z",
  "lastActionTimestamp": "2018-04-13T01:49:48Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "5156f64e-e31d-4638-ad7c-a2bdd7f41658"
}
...
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-04-13T01:49:48Z",
  "lastActionTimestamp": "2018-04-13T01:49:50Z",
  "resourceLocation": "/knowledgebases/140a46f3-b248-4f1b-9349-614bfd6e5563",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "5156f64e-e31d-4638-ad7c-a2bdd7f41658"
}
Press any key to continue.
```

[先頭に戻る](#HOLTop)

<a name="Status"></a>

## <a name="get-request-status"></a>要求の状態を取得する

[Operation](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails) メソッドを呼び出し、サポート技術情報の作成または更新要求の状態を確認できます。 このメソッドの使われ方を確認するには、[Create](#Create) または [Update](#Update) メソッドのサンプル コードを参照してください。

[先頭に戻る](#HOLTop)

<a name="Publish"></a>

## <a name="publish-knowledge-base"></a>ナレッジ ベースを公開する

次のコードでは、[Publish](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish) メソッドを利用し、既存のサポート技術情報が公開されます。

1. 適切な IDE で新しい Java プロジェクトを作成します。
2. 次に示すコードを追加します。
3. `key` の値を、お使いのサブスクリプションで有効なアクセス キーに置き換えます。
4. プログラムを実行します。

```java
import java.io.*;
import java.lang.reflect.Type;
import java.net.*;
import java.util.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *    <dependency>
 *      <groupId>com.google.code.gson</groupId>
 *      <artifactId>gson</artifactId>
 *      <version>2.8.1</version>
 *    </dependency>
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonElement;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;
import com.google.gson.reflect.TypeToken;

/* NOTE: To compile and run this code:
1. Save this file as PublishKB.java.
2. Run:
    javac PublishKB.java -cp .;gson-2.8.1.jar -encoding UTF-8
3. Run:
    java -cp .;gson-2.8.1.jar PublishKB
*/

public class PublishKB {

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
    static String subscriptionKey = "ENTER KEY HERE";

// Replace this with a valid knowledge base ID.
    static String kb = "ENTER ID HERE";

    static String host = "https://westus.api.cognitive.microsoft.com";
    static String service = "/qnamaker/v4.0";
    static String method = "/knowledgebases/";

    public static String PrettyPrint (String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

// Send an HTTP POST request.
    public static String Post (URL url, String content) throws Exception {
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("POST");
        connection.setRequestProperty("Content-Type", "application/json");
        connection.setRequestProperty("Content-Length", content.length() + "");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
        connection.setDoOutput(true);

        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        byte[] encoded_content = content.getBytes("UTF-8");
        wr.write(encoded_content, 0, encoded_content.length);
        wr.flush();
        wr.close();

        if (connection.getResponseCode() == 204)
        {
            return "{'result' : 'Success.'}";
        }
        else {
            StringBuilder response = new StringBuilder ();
            BufferedReader in = new BufferedReader(new InputStreamReader(connection.getErrorStream(), "UTF-8"));

            String line;
            while ((line = in.readLine()) != null) {
                response.append(line);
            }
            in.close();

            return response.toString();
        }
    }

// Sends the request to publish the knowledge base.
    public static String PublishKB (String kb) throws Exception {
        URL url = new URL(host + service + method + kb);
        System.out.println ("Calling " + url.toString() + ".");
        return Post(url, "");
    }

    public static void main(String[] args) {
        try {
            String response = PublishKB (kb);
            System.out.println (PrettyPrint (response));
        }
        catch (Exception e) {
            System.out.println (e.getCause().getMessage());
        }
    }
}
```

**ナレッジ ベースの応答を公開する**

成功した応答は、次の例に示すように JSON で返されます。 

```json
{
  "result": "Success."
}
```

[先頭に戻る](#HOLTop)

<a name="Replace"></a>

## <a name="replace-knowledge-base"></a>ナレッジ ベースを置換する

次のコードでは、[Replace](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace) メソッドを利用し、指定のサポート技術情報のコンテンツが置換されます。

1. 適切な IDE で新しい Java プロジェクトを作成します。
2. 次に示すコードを追加します。
3. `key` の値を、お使いのサブスクリプションで有効なアクセス キーに置き換えます。
4. プログラムを実行します。

```java
import java.io.*;
import java.lang.reflect.Type;
import java.net.*;
import java.util.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *    <dependency>
 *      <groupId>com.google.code.gson</groupId>
 *      <artifactId>gson</artifactId>
 *      <version>2.8.1</version>
 *    </dependency>
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonElement;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;
import com.google.gson.reflect.TypeToken;

/* NOTE: To compile and run this code:
1. Save this file as ReplaceKB.java.
2. Run:
    javac ReplaceKB.java -cp .;gson-2.8.1.jar -encoding UTF-8
3. Run:
    java -cp .;gson-2.8.1.jar ReplaceKB
*/

public class ReplaceKB {

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
    static String subscriptionKey = "ENTER KEY HERE";

// Replace this with a valid knowledge base ID.
    static String kb = "ENTER ID HERE";

    static String host = "https://westus.api.cognitive.microsoft.com";
    static String service = "/qnamaker/v4.0";
    static String method = "/knowledgebases/";

// We'll serialize these classes into JSON for our request to the server.
// For the JSON request schema, see:
// https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace
    public static class Request {
        Question[] qnaList;
    }

    public static class Question {
        Integer id;
        String answer;
        String source;
        String[] questions;
        Metadata[] metadata;
    }

    public static class Metadata {
        String name;
        String value;
    }

    public static String PrettyPrint (String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

// Send an HTTP PUT request.
    public static String Put (URL url, String content) throws Exception {
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("PUT");
        connection.setRequestProperty("Content-Type", "application/json");
        connection.setRequestProperty("Content-Length", content.length() + "");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
        connection.setDoOutput(true);

        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        byte[] encoded_content = content.getBytes("UTF-8");
        wr.write(encoded_content, 0, encoded_content.length);
        wr.flush();
        wr.close();

        if (connection.getResponseCode() == 204)
        {
            return "{'result' : 'Success.'}";
        }
        else {
            StringBuilder response = new StringBuilder ();
            BufferedReader in = new BufferedReader(new InputStreamReader(connection.getErrorStream(), "UTF-8"));

            String line;
            while ((line = in.readLine()) != null) {
                response.append(line);
            }
            in.close();

            return response.toString();
        }
    }

// Sends the request to replace the knowledge base.
    public static String ReplaceKB (String kb, Request req) throws Exception {
        URL url = new URL(host + service + method + kb);
        System.out.println ("Calling " + url.toString() + ".");
        String content = new Gson().toJson(req);
        return Put(url, content);
    }

// Returns a sample request to replace a knowledge base.
    public static Request GetRequest () {
        Request req = new Request ();

        Question q = new Question();
        q.id = 0;
        q.answer = "You can use our REST APIs to manage your Knowledge Base. See here for details: https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update";
        q.source = "Custom Editorial";
        q.questions = new String[]{"How do I programmatically update my Knowledge Base?"};

        Metadata md = new Metadata();
        md.name = "category";
        md.value = "api";
        q.metadata = new Metadata[]{md};

        req.qnaList = new Question[]{q};

        return req;
    }

    public static void main(String[] args) {
        try {
            String response = ReplaceKB (kb, GetRequest ());
            System.out.println (PrettyPrint (response));
        }
        catch (Exception e) {
            System.out.println (e.getCause().getMessage());
        }
    }
}
```

**ナレッジ ベースの応答を置換する**

成功した応答は、次の例に示すように JSON で返されます。 

```json
{
    "result": "Success."
}
```

[先頭に戻る](#HOLTop)

<a name="GetQnA"></a>

## <a name="download-the-contents-of-a-knowledge-base"></a>ナレッジ ベースのコンテンツをダウンロードする

次のコードでは、[Download knowledge base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download) メソッドを利用し、指定のサポート技術情報のコンテンツがダウンロードされます。

1. 適切な IDE で新しい Java プロジェクトを作成します。
2. 次に示すコードを追加します。
3. `key` の値を、お使いのサブスクリプションで有効なアクセス キーに置き換えます。
4. プログラムを実行します。

```java
import java.io.*;
import java.lang.reflect.Type;
import java.net.*;
import java.util.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *    <dependency>
 *      <groupId>com.google.code.gson</groupId>
 *      <artifactId>gson</artifactId>
 *      <version>2.8.1</version>
 *    </dependency>
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonElement;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;
import com.google.gson.reflect.TypeToken;

/* NOTE: To compile and run this code:
1. Save this file as GetQnA.java.
2. Run:
    javac GetQnA.java -cp .;gson-2.8.1.jar -encoding UTF-8
3. Run:
    java -cp .;gson-2.8.1.jar GetQnA
*/

public class GetQnA {

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
    static String subscriptionKey = "ENTER KEY HERE";

// Replace this with a valid knowledge base ID.
    static String kb = "ENTER ID HERE";

// Replace this with "test" or "prod".
    static String env = "test";

    static String host = "https://westus.api.cognitive.microsoft.com";
    static String service = "/qnamaker/v4.0";
    static String method = "/knowledgebases/%s/%s/qna/";

    public static String PrettyPrint (String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

// Send an HTTP GET request.
    public static String Get (URL url) throws Exception {
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("GET");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
        connection.setDoOutput(true);

        StringBuilder response = new StringBuilder ();
        BufferedReader in = new BufferedReader(new InputStreamReader(connection.getInputStream(), "UTF-8"));

        String line;
        while ((line = in.readLine()) != null) {
            response.append(line);
        }
        in.close();

        return response.toString();
    }

// Sends the request to download the knowledge base.
    public static String GetQnA (String kb) throws Exception {
        String method_with_id = String.format (method, kb, env);
        URL url = new URL (host + service + method_with_id);
        System.out.println ("Calling " + url.toString() + ".");
        return Get(url);
    }

    public static void main(String[] args) {
        try {
            String response = GetQnA (kb);
            System.out.println (PrettyPrint (response));
        }
        catch (Exception e) {
            System.out.println (e.getCause().getMessage());
        }
    }
}
```

**ナレッジ ベースの応答をダウンロードする**

成功した応答は、次の例に示すように JSON で返されます。 

```json
{
  "qnaDocuments": [
    {
      "id": 1,
      "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update",
      "source": "Custom Editorial",
      "questions": [
        "How do I programmatically update my Knowledge Base?"
      ],
      "metadata": [
        {
          "name": "category",
          "value": "api"
        }
      ]
    },
    {
      "id": 2,
      "answer": "QnA Maker provides an FAQ data source that you can query from your bot or application. Although developers will find this useful, content owners will especially benefit from this tool. QnA Maker is a completely no-code way of managing the content that powers your bot or application.",
      "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
      "questions": [
        "Who is the target audience for the QnA Maker tool?"
      ],
      "metadata": []
    },
...
  ]
}
```

[先頭に戻る](#HOLTop)

<a name="GetAnswers"></a>

## <a name="get-answers-to-a-question-by-using-a-knowledge-base"></a>ナレッジ ベースを使用し、質問の回答を取得する

次のコードでは、**Generate answers** メソッドを利用し、指定のサポート技術情報による質問の回答が生成されます。

1. 適切な IDE で新しい Java プロジェクトを作成します。
1. 次に示すコードを追加します。
1. `host` の値を QnA Maker サブスクリプションの Web サイト名で置換します。 詳細については、「[QnA Maker サービスを作成する](../How-To/set-up-qnamaker-service-azure.md)」を参照してください。
1. `endpoint_key` の値を、お使いのサブスクリプションで有効なエンドポイント キーに置き換えます。 これはサブスクリプション キーと同じではないことにご注意ください。 [Get endpoint keys](#GetKeys) メソッドでエンドポイント キーを取得できます。
1. `kb` 値を、回答を求めてクエリを実行しようとするナレッジ ベースの ID に置き換えます。 このナレッジ ベースは [Publish](#Publish) メソッドを利用して公開しておく必要があります。
1. プログラムを実行します。

```java
import java.io.*;
import java.lang.reflect.Type;
import java.net.*;
import java.util.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *    <dependency>
 *      <groupId>com.google.code.gson</groupId>
 *      <artifactId>gson</artifactId>
 *      <version>2.8.1</version>
 *    </dependency>
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonElement;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;
import com.google.gson.reflect.TypeToken;

/* NOTE: To compile and run this code:
1. Save this file as GetAnswers.java.
2. Run:
    javac GetAnswers.java -cp .;gson-2.8.1.jar -encoding UTF-8
3. Run:
    java -cp .;gson-2.8.1.jar GetAnswers
*/

public class GetAnswers {

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

    // NOTE: Replace this with a valid host name.
    static String host = "ENTER HOST HERE";

    // NOTE: Replace this with a valid endpoint key.
    // This is not your subscription key.
    // To get your endpoint keys, call the GET /endpointkeys method.
    static String endpoint_key = "ENTER KEY HERE";

    // NOTE: Replace this with a valid knowledge base ID.
    // Make sure you have published the knowledge base with the
    // POST /knowledgebases/{knowledge base ID} method.
    static String kb = "ENTER KB ID HERE";

    static String method = "/qnamaker/knowledgebases/" + kb + "/generateAnswer";

    static String question = "{ 'question' : 'Is the QnA Maker Service free?', 'top' : 3 }";

    public static String PrettyPrint (String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

// Send an HTTP POST request.
    public static String Post (URL url, String content) throws Exception {
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("POST");
        connection.setRequestProperty("Content-Type", "application/json");
        connection.setRequestProperty("Content-Length", content.length() + "");
        connection.setRequestProperty("Authorization", "EndpointKey " + endpoint_key);
        connection.setDoOutput(true);

        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        byte[] encoded_content = content.getBytes("UTF-8");
        wr.write(encoded_content, 0, encoded_content.length);
        wr.flush();
        wr.close();

        StringBuilder response = new StringBuilder ();
        BufferedReader in = new BufferedReader(new InputStreamReader(connection.getInputStream(), "UTF-8"));

        String line;
        while ((line = in.readLine()) != null) {
            response.append(line);
        }
        in.close();

        return response.toString();
    }

    public static String GetAnswers (String kb, String question) throws Exception {
        URL url = new URL(host + method);
        System.out.println ("Calling " + url.toString() + ".");
        return Post(url, question);
    }

    public static void main(String[] args) {
        try {
            String response = GetAnswers (kb, question);
            System.out.println (PrettyPrint(response));
        }
        catch (Exception e) {
            System.out.println (e);
        }
    }
}
```

**回答を取得する**

成功した応答は、次の例に示すように JSON で返されます。 

```json
{
  "answers": [
    {
      "questions": [
        "Can I use BitLocker with the Volume Shadow Copy Service?"
      ],
      "answer": "Yes. However, shadow copies made prior to enabling BitLocker will be automatically deleted when BitLocker is enabled on software-encrypted drives. If you are using a hardware encrypted drive, the shadow copies are retained.",
      "score": 17.3,
      "id": 62,
      "source": "https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-frequently-asked-questions",
      "metadata": []
    },
...
  ]
}
```

[先頭に戻る](#HOLTop)

<a name="GetKB"></a>

## <a name="get-information-about-a-knowledge-base"></a>ナレッジ ベースに関する情報を取得する

次のコードでは、[Get knowledge base details](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/getdetails) メソッドを利用し、指定のサポート技術情報に関する情報が取得されます。

1. 適切な IDE で新しい Java プロジェクトを作成します。
2. 次に示すコードを追加します。
3. `key` の値を、お使いのサブスクリプションで有効なアクセス キーに置き換えます。
4. プログラムを実行します。

```java
import java.io.*;
import java.lang.reflect.Type;
import java.net.*;
import java.util.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *    <dependency>
 *      <groupId>com.google.code.gson</groupId>
 *      <artifactId>gson</artifactId>
 *      <version>2.8.1</version>
 *    </dependency>
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonElement;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;
import com.google.gson.reflect.TypeToken;

/* NOTE: To compile and run this code:
1. Save this file as GetKB.java.
2. Run:
    javac GetKB.java -cp .;gson-2.8.1.jar -encoding UTF-8
3. Run:
    java -cp .;gson-2.8.1.jar GetKB
*/

public class GetKB {

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
    static String subscriptionKey = "ENTER KEY HERE";

// Replace this with a valid knowledge base ID.
    static String kb = "ENTER ID HERE";

    static String host = "https://westus.api.cognitive.microsoft.com";
    static String service = "/qnamaker/v4.0";
    static String method = "/knowledgebases/";

    public static String PrettyPrint (String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

// Send an HTTP GET request.
    public static String Get (URL url) throws Exception {
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("GET");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
        connection.setDoOutput(true);

        StringBuilder response = new StringBuilder ();
        BufferedReader in = new BufferedReader(new InputStreamReader(connection.getInputStream(), "UTF-8"));

        String line;
        while ((line = in.readLine()) != null) {
            response.append(line);
        }
        in.close();

        return response.toString();
    }

// Sends the request to get the knowledge base information.
    public static String GetKB (String kb) throws Exception {
        URL url = new URL (host + service + method + kb);
        System.out.println ("Calling " + url.toString() + ".");
        return Get(url);
    }

    public static void main(String[] args) {
        try {
            String response = GetKB (kb);
            System.out.println (PrettyPrint (response));
        }
        catch (Exception e) {
            System.out.println (e.getCause().getMessage());
        }
    }
}
```

**ナレッジ ベースの詳細応答を取得する**

成功した応答は、次の例に示すように JSON で返されます。 

```json
{
  "id": "140a46f3-b248-4f1b-9349-614bfd6e5563",
  "hostName": "https://qna-docs.azurewebsites.net",
  "lastAccessedTimestamp": "2018-04-12T22:58:01Z",
  "lastChangedTimestamp": "2018-04-12T22:58:01Z",
  "name": "QnA Maker FAQ",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "urls": [
    "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
    "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
  ],
  "sources": [
    "Custom Editorial"
  ]
}
```

[先頭に戻る](#HOLTop)

<a name="GetKBsByUser"></a>

## <a name="get-all-knowledge-bases-for-a-user"></a>ユーザーのすべてのナレッジ ベースを取得する

次のコードでは、[Get knowledge bases for user](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/listall) メソッドを利用し、指定のユーザーのすべてのサポート技術情報に関する情報が取得されます。

1. 適切な IDE で新しい Java プロジェクトを作成します。
2. 次に示すコードを追加します。
3. `key` の値を、お使いのサブスクリプションで有効なアクセス キーに置き換えます。
4. プログラムを実行します。

```java
import java.io.*;
import java.lang.reflect.Type;
import java.net.*;
import java.util.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *    <dependency>
 *      <groupId>com.google.code.gson</groupId>
 *      <artifactId>gson</artifactId>
 *      <version>2.8.1</version>
 *    </dependency>
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonElement;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;
import com.google.gson.reflect.TypeToken;

/* NOTE: To compile and run this code:
1. Save this file as GetKBsByUser.java.
2. Run:
    javac GetKBsByUser.java -cp .;gson-2.8.1.jar -encoding UTF-8
3. Run:
    java -cp .;gson-2.8.1.jar GetKBsByUser
*/

public class GetKBsByUser {

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
    static String subscriptionKey = "ENTER KEY HERE";

    static String host = "https://westus.api.cognitive.microsoft.com";
    static String service = "/qnamaker/v4.0";
    static String method = "/knowledgebases";

    public static String PrettyPrint (String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

// Send an HTTP GET request.
    public static String Get (URL url) throws Exception {
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("GET");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
        connection.setDoOutput(true);

        StringBuilder response = new StringBuilder ();
        BufferedReader in = new BufferedReader(new InputStreamReader(connection.getInputStream(), "UTF-8"));

        String line;
        while ((line = in.readLine()) != null) {
            response.append(line);
        }
        in.close();

        return response.toString();
    }

// Sends the request to get the knowledge bases for the specified user.
    public static String GetKBsByUser () throws Exception {
        URL url = new URL (host + service + method);
        System.out.println ("Calling " + url.toString() + ".");
        return Get(url);
    }

    public static void main(String[] args) {
        try {
            String response = GetKBsByUser ();
            System.out.println (PrettyPrint (response));
        }
        catch (Exception e) {
            System.out.println (e.getCause().getMessage());
        }
    }
}
```

**ユーザー応答のナレッジ ベースを取得する**

成功した応答は、次の例に示すように JSON で返されます。 

```json
{
  "knowledgebases": [
    {
      "id": "081c32a7-bd05-4982-9d74-07ac736df0ac",
      "hostName": "https://qna-docs.azurewebsites.net",
      "lastAccessedTimestamp": "2018-04-12T11:51:58Z",
      "lastChangedTimestamp": "2018-04-12T11:51:58Z",
      "name": "QnA Maker FAQ",
      "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
      "urls": [],
      "sources": []
    },
    {
      "id": "140a46f3-b248-4f1b-9349-614bfd6e5563",
      "hostName": "https://qna-docs.azurewebsites.net",
      "lastAccessedTimestamp": "2018-04-12T22:58:01Z",
      "lastChangedTimestamp": "2018-04-12T22:58:01Z",
      "name": "QnA Maker FAQ",
      "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
      "urls": [
        "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
        "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
      ],
      "sources": [
        "Custom Editorial"
      ]
    },
...
  ]
}
Press any key to continue.
```

[先頭に戻る](#HOLTop)

<a name="Delete"></a>

## <a name="delete-a-knowledge-base"></a>ナレッジ ベースを削除する

次のコードでは、[Delete knowledge base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/delete) メソッドを利用し、指定のサポート技術情報が削除されます。

1. 適切な IDE で新しい Java プロジェクトを作成します。
2. 次に示すコードを追加します。
3. `key` の値を、お使いのサブスクリプションで有効なアクセス キーに置き換えます。
4. プログラムを実行します。

```java
import java.io.*;
import java.lang.reflect.Type;
import java.net.*;
import java.util.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *    <dependency>
 *      <groupId>com.google.code.gson</groupId>
 *      <artifactId>gson</artifactId>
 *      <version>2.8.1</version>
 *    </dependency>
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonElement;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;
import com.google.gson.reflect.TypeToken;

/* NOTE: To compile and run this code:
1. Save this file as DeleteKB.java.
2. Run:
    javac DeleteKB.java -cp .;gson-2.8.1.jar -encoding UTF-8
3. Run:
    java -cp .;gson-2.8.1.jar DeleteKB
*/

public class DeleteKB {

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
    static String subscriptionKey = "ENTER KEY HERE";

// Replace this with a valid knowledge base ID.
    static String kb = "ENTER ID HERE";

    static String host = "https://westus.api.cognitive.microsoft.com";
    static String service = "/qnamaker/v4.0";
    static String method = "/knowledgebases/";

    public static String PrettyPrint (String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

// Send an HTTP DELETE request.
    public static String Delete (URL url) throws Exception {
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("DELETE");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
        connection.setDoOutput(true);

        if (connection.getResponseCode() == 204)
        {
            return "{'result' : 'Success.'}";
        }
        else {
            StringBuilder response = new StringBuilder ();
            BufferedReader in = new BufferedReader(new InputStreamReader(connection.getErrorStream(), "UTF-8"));

            String line;
            while ((line = in.readLine()) != null) {
                response.append(line);
            }
            in.close();

            return response.toString();
        }
    }

// Sends the request to delete the knowledge base.
    public static String DeleteKB (String kb) throws Exception {
        URL url = new URL (host + service + method + kb);
        System.out.println ("Calling " + url.toString() + ".");
        return Delete(url);
    }

    public static void main(String[] args) {
        try {
            String response = DeleteKB (kb);
            System.out.println (PrettyPrint (response));
        }
        catch (Exception e) {
            System.out.println (e.getCause().getMessage());
        }
    }
}
```

**ナレッジ ベースの応答を削除する**

成功した応答は、次の例に示すように JSON で返されます。 

```json
{
  "result": "Success."
}
```

[先頭に戻る](#HOLTop)

<a name="GetKeys"></a>

## <a name="get-endpoint-keys"></a>エンドポイント キーを取得する

次のコードでは、[Get endpoint keys](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/endpointkeys/getkeys) メソッドを利用し、現在のエンドポイント キーが取得されます。

1. 適切な IDE で新しい Java プロジェクトを作成します。
2. 次に示すコードを追加します。
3. `key` の値を、お使いのサブスクリプションで有効なアクセス キーに置き換えます。
4. プログラムを実行します。

```java
import java.io.*;
import java.lang.reflect.Type;
import java.net.*;
import java.util.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *    <dependency>
 *      <groupId>com.google.code.gson</groupId>
 *      <artifactId>gson</artifactId>
 *      <version>2.8.1</version>
 *    </dependency>
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonElement;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;
import com.google.gson.reflect.TypeToken;

/* NOTE: To compile and run this code:
1. Save this file as GetEndpointKeys.java.
2. Run:
    javac GetEndpointKeys.java -cp .;gson-2.8.1.jar -encoding UTF-8
3. Run:
    java -cp .;gson-2.8.1.jar GetEndpointKeys
*/

public class GetEndpointKeys {

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
    static String subscriptionKey = "ENTER KEY HERE";

    static String host = "https://westus.api.cognitive.microsoft.com";
    static String service = "/qnamaker/v4.0";
    static String method = "/endpointkeys";

    public static String PrettyPrint (String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

// Send an HTTP GET request.
    public static String Get (URL url) throws Exception {
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("GET");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
        connection.setDoOutput(true);

        StringBuilder response = new StringBuilder ();
        BufferedReader in = new BufferedReader(new InputStreamReader(connection.getInputStream(), "UTF-8"));

        String line;
        while ((line = in.readLine()) != null) {
            response.append(line);
        }
        in.close();

        return response.toString();
    }

// Sends the request to get the endpoint keys.
    public static String GetEndpointKeys () throws Exception {
        URL url = new URL (host + service + method);
        System.out.println ("Calling " + url.toString() + ".");
        return Get(url);
    }

    public static void main(String[] args) {
        try {
            String response = GetEndpointKeys ();
            System.out.println (PrettyPrint (response));
        }
        catch (Exception e) {
            System.out.println (e.getCause().getMessage());
        }
    }
}
```

**エンドポイント キーの応答を取得する**

成功した応答は、次の例に示すように JSON で返されます。 

```json
{
  "primaryEndpointKey": "ac110bdc-34b7-4b1c-b9cd-b05f9a6001f3",
  "secondaryEndpointKey": "1b4ed14e-614f-444a-9f3d-9347f45a9206"
}
```

[先頭に戻る](#HOLTop)

<a name="PutKeys"></a>

## <a name="refresh-endpoint-keys"></a>エンドポイント キーを更新する

次のコードでは、[Refresh endpoint keys](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/endpointkeys/refreshkeys) メソッドを利用し、現在のエンドポイント キーが再生成されます。

1. 適切な IDE で新しい Java プロジェクトを作成します。
2. 次に示すコードを追加します。
3. `key` の値を、お使いのサブスクリプションで有効なアクセス キーに置き換えます。
4. プログラムを実行します。

```java
import java.io.*;
import java.lang.reflect.Type;
import java.net.*;
import java.util.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *    <dependency>
 *      <groupId>com.google.code.gson</groupId>
 *      <artifactId>gson</artifactId>
 *      <version>2.8.1</version>
 *    </dependency>
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonElement;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;
import com.google.gson.reflect.TypeToken;

// Java does not natively support HTTP PATCH requests, so Apache HttpClient is required.
/*
 * HttpClient: https://hc.apache.org/downloads.cgi
 * Maven info:
 *    <dependency>
 *      <groupId>org.apache.httpcomponents</groupId>
 *      <artifactId>httpclient</artifactId>
 *      <version>4.5.5</version>
 *    </dependency>
 */
import org.apache.commons.logging.LogFactory;
import org.apache.http.client.methods.HttpPatch;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.entity.ByteArrayEntity;
import org.apache.http.Header;
import org.apache.http.HttpEntity;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;

/* NOTE: To compile and run this code:
1. Save this file as RefreshKeys.java.
2. Run:
    javac RefreshKeys.java -cp .;gson-2.8.1.jar;httpclient-4.5.5.jar;httpcore-4.4.9.jar;commons-logging-1.2.jar -encoding UTF-8
3. Run:
    java -cp .;gson-2.8.1.jar;httpclient-4.5.5.jar;httpcore-4.4.9.jar;commons-logging-1.2.jar RefreshKeys
*/

public class RefreshKeys {

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
    static String subscriptionKey = "ENTER KEY HERE";

// Replace this with "PrimaryKey" or "SecondaryKey."
    static String key_type = "PrimaryKey";

    static String host = "https://westus.api.cognitive.microsoft.com";
    static String service = "/qnamaker/v4.0";
    static String method = "/endpointkeys/";

    public static String PrettyPrint (String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

// Send an HTTP PATCH request. We use Apache HttpClient to do this, as HttpsURLConnection does not support PATCH.
    public static String Patch (URL url, String content) throws Exception {
        HttpPatch patch = new HttpPatch(url.toString());
        // HttpPatch implements HttpMessage, which includes addHeader. See:
        // https://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/org/apache/http/client/methods/HttpPatch.html
        // https://hc.apache.org/httpcomponents-core-ga/httpcore/apidocs/org/apache/http/HttpMessage.html
        patch.addHeader("Content-Type", "application/json");
        // Note: Adding the Content-Length header causes the exception:
        // "Content-Length header already present."
        patch.addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
        // HttpPatch implements HttpEntityEnclosingRequest, which includes setEntity. See:
        // https://hc.apache.org/httpcomponents-core-ga/httpcore/apidocs/org/apache/http/HttpEntityEnclosingRequest.html
        HttpEntity entity = new ByteArrayEntity(content.getBytes("UTF-8"));
        patch.setEntity(entity);

        CloseableHttpClient httpClient = HttpClients.createDefault();
        CloseableHttpResponse response = httpClient.execute(patch);

        // CloseableHttpResponse implements HttpMessage, which includes getAllHeaders. See:
        // https://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/org/apache/http/client/methods/CloseableHttpResponse.html
        // Header implements NameValuePair. See:
        // https://hc.apache.org/httpcomponents-core-ga/httpcore/apidocs/org/apache/http/Header.html
        // https://hc.apache.org/httpcomponents-core-ga/httpcore/apidocs/org/apache/http/NameValuePair.html
        Map<String, List<String>> headers = new HashMap<String, List<String>>();
        for (Header header : response.getAllHeaders()) {
            List<String> list = new ArrayList<String>() {
                {
                    add(header.getValue());
                }
            };
            headers.put(header.getName(), list);
        }

        // CloseableHttpResponse implements HttpResponse, which includes getEntity. See:
        // https://hc.apache.org/httpcomponents-core-ga/httpcore/apidocs/org/apache/http/HttpResponse.html
        // HttpEntity implements getContent, which returns an InputStream. See:
        // https://hc.apache.org/httpcomponents-core-ga/httpcore/apidocs/org/apache/http/HttpEntity.html
        StringBuilder output = new StringBuilder ();
        BufferedReader reader = new BufferedReader(new InputStreamReader(response.getEntity().getContent(), "UTF-8"));
        String line;
        while ((line = reader.readLine()) != null) {
            output.append(line);
        }
        reader.close();

        return output.toString();
    }

// Sends the request to refresh the endpoint keys.
    public static String RefreshKeys () throws Exception {
        URL url = new URL(host + service + method + key_type);
        System.out.println ("Calling " + url.toString() + ".");
        return Patch(url, "");
    }

    public static void main(String[] args) {
        try {
            String response = RefreshKeys ();
            System.out.println (PrettyPrint (response));
        }
        catch (Exception e) {
            System.out.println (e.getCause().getMessage());
        }
    }
}
```

**エンドポイント キーの応答を更新する**

成功した応答は、次の例に示すように JSON で返されます。 

```json
{
  "primaryEndpointKey": "ac110bdc-34b7-4b1c-b9cd-b05f9a6001f3",
  "secondaryEndpointKey": "1b4ed14e-614f-444a-9f3d-9347f45a9206"
}
```

[先頭に戻る](#HOLTop)

<a name="GetAlterations"></a>

## <a name="get-word-alterations"></a>単語変更を取得する

次のコードでは、[Download alterations](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/get) メソッドを利用し、現在の単語変更が取得されます。

1. 適切な IDE で新しい Java プロジェクトを作成します。
2. 次に示すコードを追加します。
3. `key` の値を、お使いのサブスクリプションで有効なアクセス キーに置き換えます。
4. プログラムを実行します。

```java
import java.io.*;
import java.lang.reflect.Type;
import java.net.*;
import java.util.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *    <dependency>
 *      <groupId>com.google.code.gson</groupId>
 *      <artifactId>gson</artifactId>
 *      <version>2.8.1</version>
 *    </dependency>
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonElement;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;
import com.google.gson.reflect.TypeToken;

/* NOTE: To compile and run this code:
1. Save this file as GetAlterations.java.
2. Run:
    javac GetAlterations.java -cp .;gson-2.8.1.jar -encoding UTF-8
3. Run:
    java -cp .;gson-2.8.1.jar GetAlterations
*/

public class GetAlterations {

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
    static String subscriptionKey = "ENTER KEY HERE";

    static String host = "https://westus.api.cognitive.microsoft.com";
    static String service = "/qnamaker/v4.0";
    static String method = "/alterations/";

    public static String PrettyPrint (String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

// Send an HTTP GET request.
    public static String Get (URL url) throws Exception {
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("GET");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
        connection.setDoOutput(true);

        StringBuilder response = new StringBuilder ();
        BufferedReader in = new BufferedReader(new InputStreamReader(connection.getInputStream(), "UTF-8"));

        String line;
        while ((line = in.readLine()) != null) {
            response.append(line);
        }
        in.close();

        return response.toString();
    }

// Sends the request to get the alterations.
    public static String GetAlterations () throws Exception {
        URL url = new URL (host + service + method);
        System.out.println ("Calling " + url.toString() + ".");
        return Get(url);
    }

    public static void main(String[] args) {
        try {
            String response = GetAlterations ();
            System.out.println (PrettyPrint (response));
        }
        catch (Exception e) {
            System.out.println (e.getCause().getMessage());
        }
    }
}
```

**単語変更の応答を取得する**

成功した応答は、次の例に示すように JSON で返されます。 

```json
{
  "wordAlterations": [
    {
      "alterations": [
        "botframework",
        "bot frame work"
      ]
    }
  ]
}
```

[先頭に戻る](#HOLTop)

<a name="PutAlterations"></a>

## <a name="replace-word-alterations"></a>単語変更を置換する

次のコードでは、[Replace alterations](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) メソッドを利用し、現在の単語変更が置換されます。

1. 適切な IDE で新しい Java プロジェクトを作成します。
2. 次に示すコードを追加します。
3. `key` の値を、お使いのサブスクリプションで有効なアクセス キーに置き換えます。
4. プログラムを実行します。

```java
import java.io.*;
import java.lang.reflect.Type;
import java.net.*;
import java.util.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *    <dependency>
 *      <groupId>com.google.code.gson</groupId>
 *      <artifactId>gson</artifactId>
 *      <version>2.8.1</version>
 *    </dependency>
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonElement;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;
import com.google.gson.reflect.TypeToken;

/* NOTE: To compile and run this code:
1. Save this file as PutAlterations.java.
2. Run:
    javac PutAlterations.java -cp .;gson-2.8.1.jar -encoding UTF-8
3. Run:
    java -cp .;gson-2.8.1.jar PutAlterations
*/

public class PutAlterations {

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace this with a valid subscription key.
    static String subscriptionKey = "ENTER KEY HERE";

    static String host = "https://westus.api.cognitive.microsoft.com";
    static String service = "/qnamaker/v4.0";
    static String method = "/alterations/";

// We'll serialize these classes into JSON for our request to the server.
// For the JSON request schema, see:
// https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace
    public static class Request {
        Alteration[] wordAlterations;
    }

    public static class Alteration {
        String[] alterations;
    }

    public static String PrettyPrint (String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

// Send an HTTP PUT request.
    public static String Put (URL url, String content) throws Exception {
        HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
        connection.setRequestMethod("PUT");
        connection.setRequestProperty("Content-Type", "application/json");
        connection.setRequestProperty("Content-Length", content.length() + "");
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
        connection.setDoOutput(true);

        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        byte[] encoded_content = content.getBytes("UTF-8");
        wr.write(encoded_content, 0, encoded_content.length);
        wr.flush();
        wr.close();

        if (connection.getResponseCode() == 204)
        {
            return "{'result' : 'Success.'}";
        }
        else {
            StringBuilder response = new StringBuilder ();
            BufferedReader in = new BufferedReader(new InputStreamReader(connection.getErrorStream(), "UTF-8"));

            String line;
            while ((line = in.readLine()) != null) {
                response.append(line);
            }
            in.close();

            return response.toString();
        }
    }

// Sends the request to replace the alterations.
    public static String PutAlterations (Request req) throws Exception {
        URL url = new URL(host + service + method);
        System.out.println ("Calling " + url.toString() + ".");
        String content = new Gson().toJson(req);

        return Put(url, content);
    }

// Returns a sample request to replace the alterations..
    public static Request GetRequest () {
        Request req = new Request ();

        Alteration alteration = new Alteration();
        alteration.alterations = new String[]{ "botframework", "bot frame work" };
        req.wordAlterations = new Alteration[]{ alteration };

        return req;
    }

    public static void main(String[] args) {
        try {
            String response = PutAlterations (GetRequest ());
            System.out.println (PrettyPrint (response));
        }
        catch (Exception e) {
            System.out.println (e.getCause().getMessage());
        }
    }
}
```

**単語変更の応答を置換する**

成功した応答は、次の例に示すように JSON で返されます。 

```json
{
  "result": "Success."
}
```

[先頭に戻る](#HOLTop)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API リファレンス](https://go.microsoft.com/fwlink/?linkid=2092179)

## <a name="see-also"></a>関連項目 

[QnA Maker の概要](../Overview/overview.md)
