---
title: "トークン認証による Azure CDN 資産の保護 | Microsoft Docs"
description: "トークン認証を使用して、Azure CDN 資産へのアクセスをセキュリティで保護します。"
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: 
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/11/2016
ms.author: mezha
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 42b182c314795b1ebf69639ec7ac5583208dc7c1
ms.lasthandoff: 03/29/2017


---


# <a name="securing-azure-cdn-assets-with-token-authentication"></a>トークン認証による Azure CDN 資産の保護

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

##<a name="overview"></a>Overview

トークン認証メカニズムを使用すると、承認されていないクライアントに Azure CDN が資産を提供できないように指定できます。  これを行うには、通常、許可なく資産を使用する他の Web サイト (多くの場合、掲示板) でのコンテンツの "hotlinking" を防止します。  これはコンテンツの配信コストに影響を与えることがあります。 CDN でこの機能を有効にすると、コンテンツを配信する前に、CDN エッジ POP で要求が認証されます。 

## <a name="how-it-works"></a>動作のしくみ

トークン認証では、信頼済みサイトによって要求が生成されていることを確認するために、要求元に関するエンコードされた情報を含むトークン値がその要求に必要です。 コンテンツが要求元に提供されるのは、そのエンコードされた情報が要件を満たす場合だけです。それ以外の場合、要求は拒否されます。 要件を設定するには、以下に示す 1 つ以上のパラメーターを使用します。

- 国: 指定された国からの要求を許可または拒否します。  [有効な国コードの一覧。](https://msdn.microsoft.com/library/mt761717.aspx) 
- URL: 指定された資産またはパスによる要求のみを許可します。  
- ホスト: 要求ヘッダーで指定されたホストを使用する要求を許可または拒否します。
- 参照元: 指定された参照元による要求を許可または拒否します。
- IP アドレス: 特定の IP アドレスまたは IP サブネットからの要求のみを許可します。
- プロトコル: コンテンツの要求に使用されたプロトコルに基づいて要求を許可またはブロックします。
- 有効期限: 限られた期間だけリンクが有効になるように日付と期間を割り当てます。

各パラメーターの構成例の詳細を参照してください。

## <a name="reference-architecture"></a>参照アーキテクチャ

Web アプリを操作するための CDN におけるトークン認証の設定を示す、以下の参照アーキテクチャをご覧ください。

![[CDN プロファイル] ブレードの [管理] ボタン](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>CDN エンドポイントのトークン検証のロジック
    
この図は、CDN エンドポイントでトークン認証が構成されているときに、Azure CDN がクライアント要求を検証する方法について説明します。

![[CDN プロファイル] ブレードの [管理] ボタン](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>トークン認証の設定

1. [Azure Portal](https://portal.azure.com) で CDN プロファイルを参照し、**[管理]** ボタンをクリックして、補助ポータルを起動します。

    ![[CDN プロファイル] ブレードの [管理] ボタン](./media/cdn-rules-engine/cdn-manage-btn.png)

2. **[HTTP Large (HTTP ラージ)]** にポインターを置いて、フライアウトで **[Token Auth (トークン認証)]** をクリックします。 このタブで暗号化キーと暗号化パラメーターを設定します。

    1. **主キー**の一意の暗号化キーを入力します。  **バックアップ キー**の他のキーを入力します

        ![CDN トークン認証のセットアップ キー](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
    2. 暗号化ツールを使用して暗号化パラメーターを設定します (有効期限、国、参照元、プロトコル、クライアント IP に基づいて、要求を許可または拒否します。 任意の組み合わせを使用できます)。

        ![[CDN プロファイル] ブレードの [管理] ボタン](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

        - ec-expire: 指定した期間の後にトークンの有効期限を割り当てます。 有効期限の後に送信された要求は拒否されます。 このパラメーターでは、Unix タイムスタンプが使用されます (この時間は、1/1/1970 00:00:00 GMT の標準エポックからの秒数に基づいています。 標準時間と Unix 時間は、オンライン ツールを使用して変換できます)。たとえば、トークンが 12/31/2016 12:00:00 GMT に有効期限が切れるように設定する場合は、以下のように Unix 時間: 1483185600 を使用します。
    
        ![[CDN プロファイル] ブレードの [管理] ボタン](./media/cdn-token-auth/cdn-token-auth-expire2.png)
    
        - ec-url-allow: 特定の資産またはパスに合わせてトークンを調整できます。 特定の相対パスで始まる URL を持つ要求へのアクセスを制限します。 複数のパスを入力するには、各パスをコンマで区切ります。 URL は大文字と小文字が区別されます。 要件に応じてさまざまな値を設定して、さまざまなレベルのアクセスを提供できます。 シナリオをいくつか次に示します。
        
            URL が http://www.mydomain.com/pictures/city/strasbourg.png の場合:  入力値 "" とそのアクセス レベルを参照してください

            1. 入力値 "/": すべての要求が許可されます
            2. 入力値 "/pictures": 次のすべての要求が許可されます
            
                - http://www.mydomain.com/pictures.png
                - http://www.mydomain.com/pictures/city/strasbourg.png
                - http://www.mydomain.com/picturesnew/city/strasbourgh.png
            3. 入力値 "/pictures/": /pictures/ の要求のみが許可されます
            4. 入力値 "/pictures/city/strasbourg.png": この資産の要求のみが許可されます
    
        ![[CDN プロファイル] ブレードの [管理] ボタン](./media/cdn-token-auth/cdn-token-auth-url-allow4.png)
    
        - ec-country-allow: 指定した 1 つ以上の国からの要求のみが許可されます。 その他の国からの要求はすべて拒否されます。 国コードを使用してパラメーターを設定し、各国コードをコンマで区切ります。 たとえば、米国とフランスからのアクセスを許可する場合は、以下のように「US, FR」と列に入力します。  
        
        ![[CDN プロファイル] ブレードの [管理] ボタン](./media/cdn-token-auth/cdn-token-auth-country-allow.png)

        - ec-country-deny: 指定した 1 つ以上の国からの要求を拒否します。 その他の国からの要求はすべて許可されます。 国コードを使用してパラメーターを設定し、各国コードをコンマで区切ります。 たとえば、米国とフランスからのアクセスを拒否する場合は、「US, FR」と列に入力します。
    
        - ec-ref-allow: 指定された参照元からの要求のみを許可します。 参照元により、要求されているリソースにリンクされた Web ページの URL を特定します。 参照元パラメーターの値には、プロトコルを含めないでください。 入力できるのは、ホスト名やそのホスト名の特定のパスです。 また、1 つのパラメーター内の複数の参照元をコンマで区切って追加することもできます。 参照元の値を指定したにもかかわらず、参照元の情報が、一部のブラウザー構成のために要求で送信されない場合、既定では、こうした要求は拒否されます。 このような参照元の情報がない要求を許可するには、パラメーターで "Missing" つまり空の値を割り当てます。 "*.consoto.com" を使用して、consoto.com のすべてのサブドメインを許可することもできます。  たとえば、www.consoto.com からの要求、consoto2.com のすべてのサブドメインからの要求、および参照元情報がない要求のアクセスを許可するには、以下の値を入力します。
        
        ![[CDN プロファイル] ブレードの [管理] ボタン](./media/cdn-token-auth/cdn-token-auth-referrer-allow2.png)
    
        - ec-ref-deny: 指定された参照元からの要求を拒否します。 "ec-ref-allow" パラメーターの詳細と例を参照してください。
         
        - ec-proto-allow: 指定されたプロトコルからの要求のみを許可します。 たとえば、http、https を指定します。
        
        ![[CDN プロファイル] ブレードの [管理] ボタン](./media/cdn-token-auth/cdn-token-auth-url-allow4.png)
            
        - ec-proto-deny: 指定されたプロトコルからの要求を拒否します。 たとえば、http、https を指定します。
    
        - ec-clientip: 指定した要求者の IP アドレスへのアクセスを制限します。 IPV4 と IPV6 の両方がサポートされています。 1 つの要求 IP アドレスまたは IP サブネットを指定できます。
            
        ![[CDN プロファイル] ブレードの [管理] ボタン](./media/cdn-token-auth/cdn-token-auth-clientip.png)
        
    3. トークンをテストするには、暗号化解除ツールを使用ます。

    4. また、要求が拒否されたときに、ユーザーに返される応答の種類をカスタマイズすることもできます。 既定では、403 を使用します。

3. **[HTTP Large (HTTP ラージ)]** で **[ルール エンジン]** をクリックします。 このタブを使用して、機能を適用するパスを定義したり、トークン認証機能や、機能に関連する追加トークン認証を有効にしたりします。

    - "IF" 列を使用して、トークン認証を適用する資産またはパスを定義します。 
    - 機能のドロップダウンから "トークン認証" をクリックして追加し、トークン認証を有効にします。
        
    ![[CDN プロファイル] ブレードの [管理] ボタン](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. **[ルール エンジン]** には、有効にできる追加機能がいくつかあります。
    
    - トークン認証拒否コード: 要求が拒否されたときにユーザーに返される応答の種類を決定します。 [トークン認証] タブの拒否コード設定は、ここで設定されたルールで上書きされます。
    - トークン認証無視: トークンの検証に使用される URL で大文字と小文字が区別されるかどうかを決定します。
    - トークン認証パラメーター: 要求された URL のトークン認証クエリ文字列パラメーターの名前を変更します。 
        
    ![[CDN プロファイル] ブレードの [管理] ボタン](./media/cdn-token-auth/cdn-rules-engine2.png)

5. トークン ベースの認証機能のトークンを生成するアプリケーションであるトークンはカスタマイズできます。 ソース コードには、[GitHub](https://github.com/VerizonDigital/ectoken) からアクセスできます。
使用可能な言語は次のとおりです。
    
    - C
    - C#
    - PHP
    - Perl
    - Java
    - Python    


## <a name="azure-cdn-features-and-provider-pricing"></a>Azure CDN の機能とプロバイダーの価格

[CDN の概要](cdn-overview.md)に関するトピックをご覧ください。

