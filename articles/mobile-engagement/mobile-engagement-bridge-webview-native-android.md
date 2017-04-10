---
title: "ネイティブ Mobile Engagement Android SDK での Android Web ビューのブリッジ"
description: "Javascript を実行している Web ビューとネイティブ Mobile Engagement Android SDK の間にブリッジを作成する方法について説明します"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: cf272f3f-2b09-41b1-b190-944cdca8bba2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f4fc7b3c81747ec80974a99084eeb1acc311f11f


---
# <a name="bridge-android-webview-with-native-mobile-engagement-android-sdk"></a>ネイティブ Mobile Engagement Android SDK での Android Web ビューのブリッジ
> [!div class="op_single_selector"]
> * [Android ブリッジ](mobile-engagement-bridge-webview-native-android.md)
> * [iOS ブリッジ](mobile-engagement-bridge-webview-native-ios.md)
> 
> 

一部のモバイル アプリは、アプリ自体がネイティブの Android 開発を使用して開発されるハイブリッド アプリとして設計されますが、一部またはすべての画面が Android Web ビュー内でレンダリングされます。 このようなアプリ内では、Mobile Engagement Android SDK を継続して使用することができます。このチュートリアルではこれを行う方法について説明します。 次のサンプル コードは、[こちら](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript)の Android ドキュメントに基づいています。 このドキュメントでは、この文書化されたアプローチを使用して、Mobile Engagement Android SDK で一般的に使用されるメソッドに対して同様に実装する方法について説明しています。たとえば、ハイブリッド アプリからの Web ビューは、Android SDK を使用してパイプ処理を行う間に、イベント、ジョブ、エラー、アプリ情報を追跡する要求も開始することができます。 

1. まず、「 [入門チュートリアル](mobile-engagement-android-get-started.md) 」を実行して、ハイブリッド アプリに Mobile Engagement Android SDK を統合していることを確認する必要があります。 これを行うと、 `OnCreate` メソッドは、次のようになります。  
   
        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
   
            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("<Mobile Engagement Conn String>");
            EngagementAgent.getInstance(this).init(engagementConfiguration);
        }
2. 自分のハイブリッド アプリに Web ビューを含む画面があることを確認します。 このためのコードは、次のようになります。画面の `onCreate` メソッド内の Web ビューにローカル HTML ファイルの **Sample.html** を読み込んでいます。 
   
        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
        }
   
        protected void onCreate(Bundle savedInstanceState) {
            ...
            ...
            SetWebView();
        }
3. [Android ドキュメント](https://developer.android.com/guide/webapps/webview.html#BindingJavaScript)で説明される `@JavascriptInterface` アプローチを使用して、一部の一般的に使用される Mobile Engagement Android SDK メソッドにラッパーを作成する **WebAppInterface** と呼ばれるブリッジ ファイルを作成します。
   
        import android.content.Context;
        import android.os.Bundle;
        import android.util.Log;
        import android.webkit.JavascriptInterface;
   
        import com.microsoft.azure.engagement.EngagementAgent;
   
        import org.json.JSONArray;
        import org.json.JSONObject;
   
        public class WebAppInterface {
            Context mContext;
   
            /** Instantiate the interface and set the context */
            WebAppInterface(Context c) {
                mContext = c;
            }
   
            @JavascriptInterface
            public void sendEngagementEvent(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendEvent(name, ParseExtras(extras));
            }
   
            @JavascriptInterface
            public void startEngagementJob(String name, String extras ){
                EngagementAgent.getInstance(mContext).startJob(name, ParseExtras(extras));
            }
   
            @JavascriptInterface
            public void endEngagementJob(String name){
                EngagementAgent.getInstance(mContext).endJob(name);
            }
   
            @JavascriptInterface
            public void sendEngagementError(String name, String extras ){
                EngagementAgent.getInstance(mContext).sendError(name, ParseExtras(extras));
            }
   
            @JavascriptInterface
            public void sendEngagementAppInfo(String appInfo){
                EngagementAgent.getInstance(mContext).sendAppInfo(ParseExtras(appInfo));
            }
   
            public Bundle ParseExtras(String input) {
                Bundle extras = new Bundle();
   
                try {
                    JSONObject jObject = new JSONObject(input);
                    extras.putString(jObject.names().getString(0),
                            jObject.get(jObject.names().getString(0)).toString());
                } catch (Exception e) {
                    e.printStackTrace();
                }
                return extras;
            }
        }  
4. 上記のブリッジ ファイルを作成したら、そのファイルが Web ビューに関連付けられていることを確認する必要があります。 これを実現させるには、 `SetWebview` メソッドを次のように編集する必要があります。
   
        private void SetWebView() {
            WebView myWebView = (WebView) findViewById(R.id.webview);
            myWebView.loadUrl("file:///android_asset/Sample.html");
            WebSettings webSettings = myWebView.getSettings();
            webSettings.setJavaScriptEnabled(true);
            myWebView.addJavascriptInterface(new WebAppInterface(this), "EngagementJs");
        }
5. 上記のスニペットでは、ブリッジ クラスと Web ビューを関連付けるために `addJavascriptInterface` を呼び出し、ブリッジ ファイルからメソッドを呼び出すために **EngagementJs** と呼ばれるハンドルを作成しました。 
6. **assets** と呼ばれるフォルダー内の自分のプロジェクトに、**Sample.html** と呼ばれる次のファイルを作成します。これは、Web ビューに読み込まれ、ブリッジ ファイルからメソッドを呼び出します。
   
        <!doctype html>
        <html>
            <head>
                <style type='text/css'>
                    html { font-family:Helvetica; color:#222; }
                    h1 { color:steelblue; font-size:22px; margin-top:16px; }
                    h2 { color:grey; font-size:14px; margin-top:18px; margin-bottom:0px; }
                </style>
   
                <script type="text/javascript">
   
                    window.onerror = function(err)
                    {
                      log('window.onerror: ' + err);
                    }
   
                    send = function(inputId)
                    {
                        var input = document.getElementById(inputId);
                        if(input)
                        {
                            var value = input.value;
                            // Example of how extras info can be passed with the Engagement logs
                            var extras = '{"CustomerId":"MS290011"}';
   
                            if(value && value.length > 0)
                            {
                                switch(inputId)
                                {
                                    case "event":
                                    EngagementJs.sendEngagementEvent(value, extras);
                                    break;
   
                                    case "job":
                                    EngagementJs.startEngagementJob(value, extras);
                                    window.setTimeout( function()
                                    {
                                      EngagementJs.endEngagementJob(value);
                                    }, 10000 );
                                    break;
   
                                    case "error":
                                    EngagementJs.sendEngagementError(value, extras);
                                    break;
   
                                    case "appInfo":
                                    EngagementJs.sendEngagementAppInfo({"customer_name":value});
                                    break;
                                }
                            }
                        }
                    }
                </script>
            </head>
            <body>
                <h1>Bridge Tester</h1>
                <div id='engagement'>
                    <h2>Event</h2>
                    <input type="text" id="event" size="35">
                    <button onclick="send('event')">Send</button>
   
                    <h2>Job</h2>
                    <input type="text" id="job" size="35">
                    <button onclick="send('job')">Send</button>
   
                    <h2>Error</h2>
                    <input type="text" id="error" size="35">
                    <button onclick="send('error')">Send</button>
   
                    <h2>AppInfo</h2>
                    <input type="text" id="appInfo" size="35">
                    <button onclick="send('appInfo')">Send</button>
                </div>
            </body>
        </html>
7. 上述の HTML ファイルに関して次の点に注意してください。
   
   * これには、イベント、ジョブ、エラー、AppInfo の名前として使用されるデータを指定できる入力ボックスのセットが含まれます。 この横にあるボタンをクリックすると、最終的にブリッジ ファイルからメソッドを呼び出して、Mobile Engagement Android SDK にこの呼び出しを渡す、Javascript への呼び出しが行われます。 
   * これを実行できる方法を示すために、イベント、ジョブおよびエラーに静的な追加情報をタグ付けしています。 この追加情報は、`WebAppInterface` ファイルを検索する場合、解析され、Android `Bundle` に配置し、イベント、ジョブ、エラーの送信で渡される JSON 文字列として設定されます。 
   * Mobile Engagement ジョブは入力ボックスに指定する名前でキック オフされ、10 秒間実行してシャットダウンされます。 
   * Mobile Engagement の appinfo またはタグは、静的なキーとしての 'customer_name' と、タグの値として入力された値で渡されます。 
8. このアプリを実行すると、次が表示されます。 ここで、次のようにテスト イベントにいくつかの名前を指定し、その下にある **[送信]** をクリックします。 
   
    ![][1]
9. アプリの **[モニター]** タブに移動し、**[イベント] - [詳細]** に表示された場合、このイベントは送信している静的なアプリ情報と共に表示されます。 
   
   ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-android/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-android/event-output.png



<!--HONumber=Nov16_HO3-->


