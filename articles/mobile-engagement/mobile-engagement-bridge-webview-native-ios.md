---
title: "ネイティブ Mobile Engagement iOS SDK での iOS Web ビューのブリッジ"
description: "Javascript を実行している Web ビューとネイティブ Mobile Engagement iOS SDK の間にブリッジを作成する方法について説明します"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: e1d6ff6f-cd67-4131-96eb-c3d6318de752
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 35f7bdbeb480122513ae2a0b04a6d8cfd426802a


---
# <a name="bridge-ios-webview-with-native-mobile-engagement-ios-sdk"></a>ネイティブ Mobile Engagement iOS SDK での iOS Web ビューのブリッジ
> [!div class="op_single_selector"]
> * [Android ブリッジ](mobile-engagement-bridge-webview-native-android.md)
> * [iOS ブリッジ](mobile-engagement-bridge-webview-native-ios.md)
> 
> 

一部のモバイル アプリは、アプリ自体がネイティブの iOS Objective-C 開発を使用して開発されるハイブリッド アプリとして設計されますが、一部またはすべての画面が iOS Web ビュー内でレンダリングされます。 このようなアプリ内では、Mobile Engagement iOS SDK を継続して使用することができます。このチュートリアルではこれを行う方法について説明します。 

どちらも文書化されていませんが、これを実現するには 2 つのアプローチがあります。

* 1 つ目のアプローチは、この[リンク](http://stackoverflow.com/questions/9826792/how-to-invoke-objective-c-method-from-javascript-and-send-back-data-to-javascrip)で説明され、Web ビュー上の `UIWebViewDelegate` の登録に関連し、JavaScript で行われる場所の変更をキャッチしてすぐにキャンセルします。 
* 2 つ目のアプローチは、この [WWDC 2013 セッション](https://developer.apple.com/videos/play/wwdc2013/615)に基づいており、1 つ目のアプローチよりも簡潔で、このガイドではこの手法に従います。 このアプローチは iOS7 以降でのみ動作することに注意してください。 

iOS のブリッジ サンプルでは、次の手順に従います。

1. まず、「 [入門チュートリアル](mobile-engagement-ios-get-started.md) 」を実行して、ハイブリッド アプリに Mobile Engagement iOS SDK を統合していることを確認する必要があります。 必要に応じて、Web ビューからメソッドをトリガーしたときに、SDK メソッドを表示できるように、次のようにテストのログ記録を有効にすることもできます。 
   
        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
           ....
             [EngagementAgent setTestLogEnabled:YES];
           ....
        }
2. 自分のハイブリッド アプリに Web ビューを含む画面があることを確認します。 アプリの `Main.storyboard` に追加することができます。 
3. ビュー コントローラー シーンから `ViewController.h` 編集画面に Web ビューをクリックしてドラッグし、`@interface` 行の真下に配置して、Web ビューと **ViewController** を関連付けます。 
4. これを実行すると、名前を要求するポップアップ ダイアログ ボックスが表示されます。 **webView**として名前を指定します。 `ViewController.h` ファイルは次のようになります。
   
        #import <UIKit/UIKit.h>
        #import "EngagementViewController.h"
   
        @interface ViewController : EngagementViewController
        @property (strong, nonatomic) IBOutlet UIWebView *webView;
   
        @end
5. 後で `ViewController.m` ファイルを更新しますが、最初にいくつかの一般的に使用される Mobile Engagement iOS SDK メソッドにラッパーを作成する、ブリッジ ファイルを作成します。 ネイティブ iOS メソッドを公開するために、前述の[セッション](https://developer.apple.com/videos/play/wwdc2013/615)で説明された `JSExport` メカニズムを使用する、**EngagementJsExports.h** という名前の新しいヘッダー ファイルを作成します。 
   
        #import <Foundation/Foundation.h>
        #import <JavaScriptCore/JavascriptCore.h>
   
        @protocol EngagementJsExports <JSExport>
   
        + (void) sendEngagementEvent:(NSString*) name :(NSString*)extras;
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras;
        + (void) endEngagementJob:(NSString*) name;
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras;
        + (void) sendEngagementAppInfo:(NSString*) appInfo;
   
        @end
   
        @interface EngagementJs : NSObject <EngagementJsExports>
   
        @end
6. 次は、ブリッジ ファイルの 2 番目の部分を作成します。 Mobile Engagement iOS SDK メソッドを呼び出して、実際のラッパーを作成する実装を含む、 **EngagementJsExports.m** と呼ばれるファイルを作成します。 また、Web ビューの JavaScript から渡されている `extras` を解析し、これを Engagement SDK メソッドの呼び出しで渡される `NSMutableDictionary` オブジェクトに配置していることにも注意してください。  
   
        #import <UIKit/UIKit.h>
        #import "EngagementAgent.h"
        #import "EngagementJsExports.h"
   
        @implementation EngagementJs
   
        +(void) sendEngagementEvent:(NSString*)name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendEvent:name extras:extrasInput];
        }
   
        + (void) startEngagementJob:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] startJob:name extras:extrasInput];
        }
   
        + (void) endEngagementJob:(NSString*) name {
           [[EngagementAgent shared] endJob:name];
        }
   
        + (void) sendEngagementError:(NSString*) name :(NSString*)extras {
           NSMutableDictionary* extrasInput = [self ParseExtras:extras];
           [[EngagementAgent shared] sendError:name extras:extrasInput];
        }
   
        + (void) sendEngagementAppInfo:(NSString*) appInfo {
           NSMutableDictionary* appInfoInput = [self ParseExtras:appInfo];
           [[EngagementAgent shared] sendAppInfo:appInfoInput];
        }
   
        + (NSMutableDictionary*) ParseExtras:(NSString*) input {
           NSData *data = [input dataUsingEncoding:NSUTF8StringEncoding];
           NSError* error = nil;
           NSMutableDictionary* extras = [NSJSONSerialization JSONObjectWithData:data options:0 error:&error];
   
           return extras;
        }
   
        @end
7. ここで **ViewController.m** に戻り、次のコードで更新します。 
   
        #import <JavaScriptCore/JavaScriptCore.h>
        #import "ViewController.h"
        #import "EngagementJsExports.h"
   
        @interface ViewController ()
   
        @end
   
        @implementation ViewController
   
        - (void)viewDidLoad
        {
           self.webView.delegate = self;
           [super viewDidLoad];
           [self loadWebView];
        }
   
        - (void)loadWebView {
           NSBundle* mainBundle = [NSBundle mainBundle];
           NSURL* htmlPage = [mainBundle URLForResource:@"LocalPage" withExtension:@"html"];
   
           NSURLRequest* urlReq = [NSURLRequest requestWithURL:htmlPage];
           [self.webView loadRequest:urlReq];
        }
   
        - (void)webViewDidFinishLoad:(UIWebView*)wv
        {
           JSContext* context = [wv valueForKeyPath:@"documentView.webView.mainFrame.javaScriptContext"];
   
           context[@"EngagementJs"] = [EngagementJs class];
        }
   
        - (void)webView:(UIWebView*)wv didFailLoadWithError:(NSError*)error
        {
           NSLog(@"Error for WEBVIEW: %@", [error description]);
        }
   
        - (void)didReceiveMemoryWarning {
           [super didReceiveMemoryWarning];
           // Dispose of any resources that can be recreated.
        }
   
        @end
8. 次の **ViewController.m** ファイルに関して次の点に注意してください。
   
   * `loadWebView` メソッドでは、次を確認するコードを持つ **LocalPage.html** と呼ばれるローカル HTML ファイルを読み込みます。 
   * `webViewDidFinishLoad` メソッドでは、`JsContext` をつかみ、ラッパー クラスと関連付けています。 これにより、Web ビューからのハンドル **EngagementJs** を使用して、ラッパー SDK メソッドを呼び出すことができます。 
9. 次のコードを含む **LocalPage.html** という名前のファイルを作成します。
   
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
                   alert('window.onerror: ' + err);
               }
   
               function send(inputId)
               {
                   var input = document.getElementById(inputId);
                   if(input)
                   {
                       var value = input.value;
                       // Example of how extras info can be passed with the Engagement logs
                       var extras = '{"CustomerId":"MS290011"}';
                   }
   
                   if(value && value.length > 0)
                   {
                       switch(inputId)
                       {
                           case "event":
                           EngagementJs.sendEngagementEvent(value, extras);
                           break;
   
                           case "job":
                           EngagementJs.startEngagementJob(value, extras);
                           window.setTimeout(
                           function(){
                               EngagementJs.endEngagementJob(value);
                           }, 10000 );
                           break;
   
                           case "error":
                           EngagementJs.sendEngagementError(value, extras);
                           break;
   
                           case "appInfo":
                           var appInfo = '{"customer_name":"' + value + '"}';
                           EngagementJs.sendEngagementAppInfo(appInfo);
                           break;
                       }
                   }
               }
               </script>
   
           </head>
           <body>
               <h1>Bridge Tester</h1>
   
               <div id='engagement'>
   
                   <br/>
                   <h2>Event</h2>
                   <input type="text" id="event" size="35">
                   <button onclick="send('event')">Send</button>
   
                   <br/>
                   <h2>Job</h2>
                   <input type="text" id="job" size="35">
                   <button onclick="send('job')">Send</button>
   
                   <br/>
                   <h2>Error</h2>
                   <input type="text" id="error" size="35">
                   <button onclick="send('error')">Send</button
   
                   <br/>
                   <h2>AppInfo</h2>
                   <input type="text" id="appInfo" size="35">
                   <button onclick="send('appInfo')">Send</button>
   
               </div>
           </body>
        </html>
10. 上述の HTML ファイルに関して次の点に注意してください。
    
    * これには、イベント、ジョブ、エラー、AppInfo の名前として使用されるデータを指定できる入力ボックスのセットが含まれます。 この横にあるボタンをクリックすると、最終的にブリッジ ファイルからメソッドを呼び出して、Mobile Engagement iOS SDK にこの呼び出しを渡す、Javascript への呼び出しが行われます。 
    * これを実行できる方法を示すために、イベント、ジョブおよびエラーに静的な追加情報をタグ付けしています。 この追加情報は、 `EngagementJsExports.m` ファイルを検索する場合、解析され、イベント、ジョブ、エラーの送信で渡される JSON 文字列として設定されます。 
    * Mobile Engagement ジョブは入力ボックスに指定する名前でキック オフされ、10 秒間実行してシャットダウンされます。 
    * Mobile Engagement の appinfo またはタグは、静的なキーとしての 'customer_name' と、タグの値として入力された値で渡されます。 
11. このアプリを実行すると、次が表示されます。 ここで、次のようにテスト イベントにいくつかの名前を指定し、その横にある **[送信]** をクリックします。 
    
     ![][1]
12. アプリの **[モニター]** タブに移動し、**[イベント] - [詳細]** に表示された場合、このイベントは送信している静的なアプリ情報と共に表示されます。 
    
    ![][2]

<!-- Images. -->
[1]: ./media/mobile-engagement-bridge-webview-native-ios/sending-event.png
[2]: ./media/mobile-engagement-bridge-webview-native-ios/event-output.png



<!--HONumber=Dec16_HO2-->


