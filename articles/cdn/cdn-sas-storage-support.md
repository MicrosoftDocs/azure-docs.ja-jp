---
title: SAS を利用した Azure CDN の使用 | Microsoft Docs
description: Azure CDN では、Shared Access Signature (SAS) を使用した、プライベート ストレージ コンテナーへの制限付きアクセス権限の付与がサポートされています。
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/21/2018
ms.author: allensu
ms.openlocfilehash: ccf55e0e3986de8afe23cb646d4df743b576900c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101725324"
---
# <a name="using-azure-cdn-with-sas"></a>SAS を利用した Azure CDN の使用

コンテンツをキャッシュする目的で Azure Content Delivery Network (CDN) ストレージ アカウントを設定する場合、既定では、ユーザーがファイルをアップロードしたストレージ コンテナーの URL を入手したすべてのユーザーがそのファイルにアクセスできます。 ストレージ アカウント内のファイルを保護するには、ストレージ コンテナーのアクセス設定をパブリックからプライベートに変更できます。 ただし、この設定では、どのユーザーもファイルにアクセスできなくなります。 

プライベート ストレージ コンテナーへの制限付きアクセス権限を付与するには、Azure ストレージ アカウントの Shared Access Signature (SAS) 機能を使用できます。 SAS は、アカウント キーを公開することなく Azure Storage リソースへの制限付きアクセス権を付与する URI です。 ストレージ アカウント キーで信頼していないが特定のストレージ アカウント リソースへのアクセスを委任するクライアントに SAS を提供できます。 これらのクライアントに共有アクセス署名 URI を配布して、指定した期間にわたってリソースへのアクセスを許可します。
 
SAS を使用すると、開始時刻や有効期限、アクセス許可 (読み取り/書き込み)、IP 範囲など、BLOB へのアクセスに関するさまざまなパラメーターを定義することができます。 この記事では、Azure CDN と共に SAS を使用する方法について説明します。 作成方法やパラメーター オプションなど、SAS について詳しくは、「[Shared Access Signatures (SAS) の使用](../storage/common/storage-sas-overview.md)」をご覧ください。

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>ストレージ SAS と共に使用するための Azure CDN の設定
SAS を Azure CDN と共に使用する場合は、次の 3 つのオプションをお勧めします。 すべてのオプションで、動作する SAS が既に作成されていることを前提としています (前提条件をご覧ください)。 
 
### <a name="prerequisites"></a>前提条件
まず、ストレージ アカウントを作成し、資産の SAS を生成します。 保存されている 2 種類のアクセス署名 (サービス SAS またはアカウント SAS) を生成できます。 詳しくは、「[共有アクセス署名の種類](../storage/common/storage-sas-overview.md#types-of-shared-access-signatures)」をご覧ください。

SAS トークンが生成されたら、URL の末尾に `?sv=<SAS token>` を追加することにより、BLOB ストレージ ファイルにアクセスできます。 この URL は、以下のフォーマットになります。 

`https://<account name>.blob.core.windows.net/<container>/<file>?sv=<SAS token>`
 
次に例を示します。
 ```
https://democdnstorage1.blob.core.windows.net/container1/demo.jpg?sv=2017-07-29&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

パラメーターの設定について詳しくは、「[SAS parameter considerations](#sas-parameter-considerations)」(SAS パラメーターの考慮事項) と「[Shared Access Signature パラメーター](../storage/common/storage-sas-overview.md#how-a-shared-access-signature-works)」をご覧ください。

![CDN SAS 設定](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-azure-cdn"></a>オプション 1: Azure CDN から BLOB ストレージまで、SAS のパススルー機能を使用する

このオプションは最も簡単で、CDN から配信元サーバーに渡される SAS トークン 1 つのみを使用します。
 
1. エンドポイントを選択し、 **[キャッシュ規則]** をクリックしてから、 **[クエリ文字列のキャッシュ]** の一覧で **[一意の URL をすべてキャッシュ]** を選択します。

    ![CDN キャッシュ規則](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. ストレージ アカウントに SAS を設定したら、CDN エンドポイントと配信元サーバーの URL と共に SAS トークンを使用してファイルにアクセスする必要があります。 
   
   結果として生成される CDN エンドポイント URL の形式は次のとおりです: `https://<endpoint hostname>.azureedge.net/<container>/<file>?sv=<SAS token>`

   次に例を示します。   
   ```
   https://demoendpoint.azureedge.net/container1/demo.jpg/?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. キャッシュ規則を使用するか、配信元サーバーに `Cache-Control` ヘッダーを追加して、キャッシュ期間を微調整します。 Azure CDN では SAS トークンはプレーンなクエリ文字列として処理されるため、ベスト プラクティスとして、SAS の有効期限か、それより前に期限が切れるようにキャッシュ期間を設定する必要があります。 そうしないと、SAS がアクティブになっている期間よりも長くファイルがキャッシュされた場合、SAS の有効期限が過ぎた後も Azure CDN 配信元サーバーからファイルにアクセスできます。 この状況が発生した場合、キャッシュされたファイルにアクセスできないようにするには、ファイルに対し消去操作を実行して、キャッシュから削除する必要があります。 Azure CDN のキャッシュ期間の設定の詳細については、「[キャッシュ規則で Azure CDN キャッシュの動作を制御する](cdn-caching-rules.md)」を参照してください。

### <a name="option-2-hidden-cdn-sas-token-using-a-rewrite-rule"></a>オプション 2:書き換えルールを使用して CDN SAS トークンを非表示にする
 
このオプションは、**Azure CDN Premium from Verizon** プロファイルでのみ使用できます。 このオプションを使用して、配信元サーバーの BLOB ストレージをセキュリティで保護できます。 ファイルに対する特定のアクセス制限が必要なくても、Azure CDN オフロード時間を短縮するためにユーザーがストレージ配信元に直接アクセスできないようにする場合は、このオプションを使用できます。 ユーザーには知られていない SAS トークンは、配信元サーバーの指定されたコンテナー内のファイルにアクセスする人に必要です。 ただし、URL 書き換えルールにより、SAS トークンは CDN エンドポイントでは不要です。
 
1. [ルール エンジン](./cdn-verizon-premium-rules-engine.md)を使用して URL 書き換えルールを作成します。 新しいルールが反映されるまで最大で 4 時間かかります。

   ![CDN の [管理] ボタン](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![CDN の [ルール エンジン] ボタン](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   次のサンプル URL 書き換えルールでは、キャプチャ グループを使用する正規表現パターンと、*sasstoragedemo* という名前のエンドポイントを使用します。
   
   ソース:   
   `(container1/.*)`


   変換先:   
   ```
   $1?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![CDN URL 書き換えルール - 左](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   ![CDN URL 書き換えルール - 右](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

2. 新しいルールがアクティブになった後は、URL で SAS トークンを使用しているかどうかに関係なく、CDN エンドポイント上の指定されたコンテナー内のファイルにだれでもアクセスできます。 形式は次のとおりです: `https://<endpoint hostname>.azureedge.net/<container>/<file>`
 
   次に例を示します。   
   `https://sasstoragedemo.azureedge.net/container1/demo.jpg`
       

3. キャッシュ規則を使用するか、配信元サーバーに `Cache-Control` ヘッダーを追加して、キャッシュ期間を微調整します。 Azure CDN では SAS トークンはプレーンなクエリ文字列として処理されるため、ベスト プラクティスとして、SAS の有効期限か、それより前に期限が切れるようにキャッシュ期間を設定する必要があります。 そうしないと、SAS がアクティブになっている期間よりも長くファイルがキャッシュされた場合、SAS の有効期限が過ぎた後も Azure CDN エンドポイントからファイルにアクセスできます。 この状況が発生した場合、キャッシュされたファイルにアクセスできないようにするには、ファイルに対し消去操作を実行して、キャッシュから削除する必要があります。 Azure CDN のキャッシュ期間の設定の詳細については、「[キャッシュ規則で Azure CDN キャッシュの動作を制御する](cdn-caching-rules.md)」を参照してください。

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>オプション 3:書き換えルールと共に CDN セキュリティ トークン認証を使用する

Azure CDN セキュリティ トークン認証を使用するには、**Azure CDN Premium from Verizon** プロファイルが必要です。 このオプションは、最も安全かつカスタマイズ可能です。 クライアント アクセスは、セキュリティ トークンに設定されているセキュリティ パラメーターを使用して実行されます。 セキュリティ トークンを作成し、設定したら、すべての CDN エンドポイント URL で必要になります。 ただし、URL 書き換えルールにより、SAS トークンは CDN エンドポイントでは不要です。 SAS トークンが後で無効になった場合、Azure CDN は配信元サーバーからのコンテンツを再検証できなくなります。

1. [Azure CDN セキュリティ トークンを作成](./cdn-token-auth.md#setting-up-token-authentication)し、CDN エンドポイントのルール エンジンと、ユーザーがファイルにアクセスできる場所のパスを使用してアクティブにします。

   セキュリティ トークン エンドポイント URL は、次のような形式です。   
   `https://<endpoint hostname>.azureedge.net/<container>/<file>?<security_token>`
 
   次に例を示します。   
   ```
   https://sasstoragedemo.azureedge.net/container1/demo.jpg?a4fbc3710fd3449a7c99986bkquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   セキュリティ トークン認証のためのパラメーター オプションは、SAS トークンのパラメーター オプションとは異なります。 セキュリティ トークンを作成する際に有効期限を使用する場合は、SAS トークンの有効期限と同じ値に設定する必要があります。 これにより、有効期限が予測可能になります。 
 
2. コンテナー内のすべての BLOB にアクセスできる SAS トークンを有効にするには、[ルール エンジン](./cdn-verizon-premium-rules-engine.md)を使用して URL 書き換えルールを作成します。 新しいルールが反映されるまで最大で 4 時間かかります。

   次のサンプル URL 書き換えルールでは、キャプチャ グループを使用する正規表現パターンと、*sasstoragedemo* という名前のエンドポイントを使用します。
   
   ソース:   
   `(container1/.*)`
   
   変換先:   
   ```
   $1&sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![CDN URL 書き換えルール - 左](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   ![CDN URL 書き換えルール - 右](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

3. SAS を更新する場合は、新しい SAS トークンを使用するよう URL 書き換えルールを更新してください。 

## <a name="sas-parameter-considerations"></a>SAS パラメーターに関する考慮事項

SAS パラメーターは Azure CDN からは認識できないため、Azure CDN は、SAS パラメーターに基づいた配信動作を変更できません。 定義されたパラメーター制約は、Azure CDN から配信元サーバーに対する要求のみに適用され、クライアントから Azure CDN への要求には適用されません。 この差は、SAS パラメーターを設定する際に重要になります。 これらの高度の機能が必要で、[オプション 3](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule) を使用している場合は、Azure CDN セキュリティ トークンに適切な制限を設定します。

| SAS パラメーター名 | 説明 |
| --- | --- |
| [開始] | Azure CDN が BLOB ファイルにアクセスを開始できる時刻。 クロックのずれ (クロック シグナルが各コンポーネントで受信された時刻が異なる) のため、アセットをすぐ使用する必要がある場合は、15 分早い時刻を選択してください。 |
| End | Azure CDN が BLOB ファイルにアクセスできなくなる時刻。 これより以前に Azure CDN にキャッシュされたファイルは引き続きアクセスできます。 ファイルの有効期限を制御するためには、Azure CDN のセキュリティ トークンに適切な有効期限を設定するか、アセットを削除します。 |
| 許可された IP アドレス | 省略可能。 **Azure CDN from Verizon** を使用する場合は、このパラメーターを「[Azure CDN from Verizon Edge Server IP Ranges](./cdn-pop-list-api.md)」(Azure CDN from Verizon Edge Server の IP 範囲) で定義されている範囲に設定できます。 **Azure CDN from Akamai** を使用する場合、IP アドレスは静的でないため、IP 範囲パラメーターを設定できません。|
| 許可されるプロトコル | アカウント SAS を使用して行われた要求に対して許可されるプロトコル。 HTTPS 設定をお勧めします。|

## <a name="next-steps"></a>次のステップ

SAS の詳細については、次の記事を参照してください。
- [Shared Access Signatures (SAS) の使用](../storage/common/storage-sas-overview.md)
- [Shared Access Signature、パート 2: BLOB ストレージでの SAS の作成と使用](../storage/common/storage-sas-overview.md)

トークン認証設定の詳細については、「[トークン認証による Azure Content Delivery Network 資産の保護](./cdn-token-auth.md)」をご覧ください。