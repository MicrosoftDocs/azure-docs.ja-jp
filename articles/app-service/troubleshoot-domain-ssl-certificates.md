---
title: ドメインと TLS/SSL 証明書のトラブルシューティング
description: Azure App Service でドメインまたは TLS/SSL 証明書を構成するときに発生する可能性がある一般的な問題の解決策を見つけます。
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.topic: article
ms.date: 03/01/2019
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 691cbd79e82432c8e919dcbb51642a76000296dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97607611"
---
# <a name="troubleshoot-domain-and-tlsssl-certificate-problems-in-azure-app-service"></a>Azure App Serviceでのドメインと TLS/SSL 証明書に関する問題のトラブルシューティング

この記事では、Azure App Service でお使いの Web アプリ用のドメインまたは TLS/SSL 証明書を構成するときに発生する可能性がある、一般的な問題の一覧を示します。 これらの問題の考えられる原因と解決策についても説明します。

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN のフォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 [Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、 **[サポートの要求]** をクリックしてください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="certificate-problems"></a>証明書に関する問題

### <a name="you-cant-add-a-tlsssl-certificate-binding-to-an-app"></a>アプリに対する TLS/SSL 証明書のバインディングを作成できない 

#### <a name="symptom"></a>症状

TLS バインディングを追加するときに、次のエラー メッセージが表示されます。

"SSL バインディングを追加できませんでした。 既存の VIP の証明書は設定できません。別の VIP が既にその証明書を使用しています。"

#### <a name="cause"></a>原因

この問題は、複数のアプリにわたって、同じ IP アドレスへの IP ベースの SSL バインディングが複数ある場合に発生することがあります。 たとえば、アプリ A に、古い証明書を持つ IP ベースの SSL があり、 アプリ B に、同じ IP アドレスの新しい証明書を持つ IP ベースの SSL がある場合です。 新しい証明書でアプリの TLS バインディングを更新すると、別のアプリで同じ IP アドレスが使われているため、このエラーで更新が失敗します。 

#### <a name="solution"></a>解決策 

この問題を解決するには、次のいずれかの方法を使用します。

- アプリで、古い証明書を使用している IP ベースの SSL バインディングを削除します。 
- 新しい証明書を使用する、新しい IP ベースの SSL バインディングを作成します。

### <a name="you-cant-delete-a-certificate"></a>証明書を削除できない 

#### <a name="symptom"></a>症状

証明書を削除しようとすると、次のエラー メッセージが表示されます。

"証明書は、TLS/SSL バインディングで使用されているため、削除できません。 証明書を削除できるようにするには、先に TLS バインディングを削除する必要があります。"

#### <a name="cause"></a>原因

この問題は、別のアプリがその証明書を使用している場合に発生する可能性があります。

#### <a name="solution"></a>解決策

アプリからその証明書の TLS バインディングを削除します。 その後で証明書の削除を試みます。 それでも証明書を削除できない場合は、インターネット ブラウザーのキャッシュをクリアし、新しいブラウザー ウィンドウで再度 Azure Portal を開きます。 その後で証明書の削除を試みます。

### <a name="you-cant-purchase-an-app-service-certificate"></a>App Service 証明書を購入できない 

#### <a name="symptom"></a>症状
Azure Portal から [Azure App Service 証明書](./configure-ssl-certificate.md#import-an-app-service-certificate)を購入できません。

#### <a name="cause-and-solution"></a>原因と解決策
この問題は、次のいずれかの理由で発生することがあります。

- App Service プランが Free または Shared である。 これらの価格レベルでは TLS はサポートされていません。 

    **解決策**:アプリの App Service プランを Standard にアップグレードします。

- サブスクリプションに有効なクレジット カードが登録されていない。

    **解決策**:サブスクリプションに有効なクレジット カードを追加します。 

- サブスクリプション プランで、Microsoft Student などの App Service 証明書の購入がサポートされていない。  

    **解決策**:サブスクリプションをアップグレードします。 

- そのサブスクリプションは、サブスクリプションで許可されている購入の上限に達しました。

    **解決策**:サブスクリプションの種類が従量課金制および EA の場合、App Service 証明書の購入上限は 10 件です。 その他のサブスクリプションの種類の場合、上限は 3 件です。 この上限を増やすには、[Azure サポート](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)へお問い合わせください。
- App Service 証明書が不正であるとマークされて、 次のエラー メッセージが表示されました。"Your certificate has been flagged for possible fraud. The request is currently under review. If the certificate does not become usable within 24 hours, contact Azure Support."(証明書に詐欺の可能性のフラグが付けられました。要求を確認中です。24 時間以内に証明書が使用可能にならない場合、Azure サポートに問い合わせてください。)

    **解決策**:証明書が不正であるとマークされて 24 時間以内に解決されない場合は、以下の手順に従ってください。

    1. [Azure portal](https://portal.azure.com) にサインインします。
    2. **[App Service 証明書]** に移動して、証明書を選択します。
    3. **[証明書の構成]**  >  **[手順 2: 確認]**  >  **[ドメインの検証]** と選択します。 この手順により、問題を解決するため、Azure の証明書プロバイダーに電子メールの通知が送信されます。

## <a name="custom-domain-problems"></a>カスタム ドメインに関する問題

### <a name="a-custom-domain-returns-a-404-error"></a>カスタム ドメインから 404 エラーが返される 

#### <a name="symptom"></a>症状

カスタム ドメイン名を使用してサイトを参照すると、次のエラー メッセージが表示されます。

"Error 404-Web app not found."(エラー 404-Web アプリが見つかりません。)

#### <a name="cause-and-solution"></a>原因と解決策

**原因 1** 

構成したカスタム ドメインに CNAME または A レコードがありません。 

**原因 1 の解決策**

- A レコードを追加した場合は、必ず TXT レコードも追加するようにします。 詳細については、「[A レコードを作成する](./app-service-web-tutorial-custom-domain.md#create-the-a-record)」を参照してください。
- アプリのためにルート ドメインを使用する必要がない場合は、A レコードではなく CNAME レコードを使用することをお勧めします。
- 同じドメインのために CNAME レコードと A レコードの両方を使用しないでください。 この問題によって競合が発生し、ドメインの解決が妨げられる場合があります。 

**原因 2** 

インターネット ブラウザーが、ドメインの古い IP アドレスをまだキャッシュしている可能性があります。 

**原因 2 の解決策**

ブラウザーをクリアします。 Windows デバイスの場合は、`ipconfig /flushdns` コマンドを実行できます。 [WhatsmyDNS.net](https://www.whatsmydns.net/) を使用して、ドメインがアプリの IP アドレスを指し示していることを確認します。

### <a name="you-cant-add-a-subdomain"></a>サブドメインを追加できない 

#### <a name="symptom"></a>症状

アプリに新しいホスト名を追加し、サブドメインを割り当てることができません。

#### <a name="solution"></a>解決策

- サブスクリプションの管理者に問い合わせて、アプリにホスト名を追加する権限があることを確認します。
- さらに多くのサブドメインが必要な場合は、ドメイン ホスティングを Azure ドメイン ネーム サービス (DNS) に変更することをお勧めします。 Azure DNS を使用すると、アプリに 500 のホスト名を追加できます。 詳細については、[サブドメインの追加](/archive/blogs/waws/mapping-a-custom-subdomain-to-an-azure-website)に関するページを参照してください。

### <a name="dns-cant-be-resolved"></a>DNS を解決できない

#### <a name="symptom"></a>症状

次のエラー メッセージが表示されました。

"DNS レコードが見つかりませんでした。"

#### <a name="cause"></a>原因
この問題は、次のいずれかの理由で発生します。

- Time to Live (TTL) 期間が有効期限切れになりませんでした。 ドメインの DNS 構成を調べて TTL 値を確認し、その期間が有効期限切れになるまで待機します。
- DNS 構成が正しくありません。

#### <a name="solution"></a>解決策
- この問題が自動的に解決されるのを 48 時間待ちます。
- DNS 構成で TTL 設定を変更できる場合は、値を 5 分に変更し、この問題が解決されるかどうかを確認します。
- [WhatsmyDNS.net](https://www.whatsmydns.net/) を使用して、ドメインがアプリの IP アドレスを指し示していることを確認します。 そうでない場合は、A レコードを、アプリの正しい IP アドレスに向けて構成します。

### <a name="you-need-to-restore-a-deleted-domain"></a>削除したドメインを復元する必要がある 

#### <a name="symptom"></a>症状
ドメインが Azure Portal に表示されなくなっています。

#### <a name="cause"></a>原因 
サブスクリプションの所有者が意図せずドメインを削除した可能性があります。

#### <a name="solution"></a>解決策
ドメインが削除されたのが 7 日前までの場合、ドメインの削除プロセスはまだ開始されていません。 この場合、Azure Portal で、同じサブスクリプションを使用してもう一度同じドメインを購入できます。 (必ず、検索ボックスに正確なドメイン名を入力してください。)このドメインに対して再度の請求は行われません。 ドメインが 7 日前より前に削除された場合は、[Azure サポート](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)に連絡してドメイン復元の支援を受けてください。

## <a name="domain-problems"></a>ドメインに関する問題

### <a name="you-purchased-a-tlsssl-certificate-for-the-wrong-domain"></a>正しくないドメインの TLS/SSL 証明書を購入した

#### <a name="symptom"></a>症状

正しくないドメインの App Service 証明書を購入しました。 この証明書を、正しいドメインを使用するように更新することはできません。

#### <a name="solution"></a>解決策

その証明書を削除してから、新しい証明書を購入します。

誤ったドメインを使用している現在の証明書が “Issued” 状態にある場合は、課金の対象もその証明書になります。 App Service 証明書は払い戻しできませんが、[Azure サポート](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)に連絡し、他のオプションがあるかどうかを確認できます。 

### <a name="an-app-service-certificate-was-renewed-but-the-app-shows-the-old-certificate"></a>App Service 証明書は更新されたが、アプリに古い証明書が表示される 

#### <a name="symptom"></a>症状

App Service 証明書が更新されましたが、その App Service 証明書を使用するアプリが、まだ古い証明書を使用しています。 また、HTTPS プロトコルが必要であるという警告が表示されます。

#### <a name="cause"></a>原因 
証明書は 48 時間以内に App Service によって自動的に同期されます。 証明書の交換や更新を行うときには、アプリケーションが今までどおり古い証明書を取得していて、新しく更新された証明書を取得していないことがあります。 証明書リソースを同期するジョブがまだ実行されていないことが理由です。 [同期] をクリックします。同期操作によって、アプリにダウンタイムを発生させることなく、App Service 内の証明書に対するホスト名のバインドが自動的に更新されます。

#### <a name="solution"></a>解決策

証明書の同期を強制することができます。

1. [Azure portal](https://portal.azure.com) にサインインします。 **[App Service 証明書]** を選択し、次に目的の証明書を選択します。
2. **[キー更新と同期]** を選択してから、 **[同期]** を選択します。同期が完了するまでしばらく時間がかかります。 
3. 同期が完了すると、次の通知が表示されます。"最新の証明書ですべてのリソースが正常に更新されました。"

### <a name="domain-verification-is-not-working"></a>ドメインの確認が機能していない 

#### <a name="symptom"></a>症状 
App Service 証明書では、証明書を使う準備ができる前に、ドメインの確認を行う必要があります。 **[確認]** を選択するとプロセスが失敗します。

#### <a name="solution"></a>解決策
TXT レコードを追加して、手動でドメインを確認します。

1. 使用中のドメイン名をホストしているドメイン ネーム サービス (DNS) プロバイダーに移動します。
1. Azure Portal に表示されるドメイン トークンの値を使用しているドメインの TXT レコードを追加します。 

DNS 伝達が実行されるのを数分待ってから、 **[最新の情報に更新]** ボタンを選択して確認をトリガーします。 

別の方法として、HTML Web ページによる方法を使用して、手動でドメインを確認することができます。 この方法を使用すると、証明機関は、証明書が発行されるドメインの所有権を確認できます。

1. {domain verification token}.html という名前の HTML ファイルを作成します。 このファイルの内容には、ドメイン確認トークンの値を指定します。
1. ドメインをホストしている Web サーバーのルートに、このファイルをアップロードします。
1. **[最新の情報に更新]** を選択して証明書の状態を確認します。 確認が完了するまで数分かかる場合があります。

たとえば、1234abcd というドメイン確認トークンを使用して azure.com の標準証明書を購入しようとしている場合、 https://azure.com/1234abcd.html に対して Web 要求を行うと、1234abcd が返されます。 

> [!IMPORTANT]
> 証明書の注文では、ドメイン確認操作を完了するまでの日数は 15 日のみです。 15 日を過ぎると証明機関は証明書を拒否し、その証明書の課金は行われません。 この状況では、この証明書を削除し、もう一度やり直します。
>
> 

### <a name="you-cant-purchase-a-domain"></a>ドメインを購入できない

#### <a name="symptom"></a>症状
Microsoft Azure portal 内の App Service からドメインを購入できません。

#### <a name="cause-and-solution"></a>原因と解決策

この問題は、次のいずれかの理由で発生します。

- Azure サブスクリプションに登録されたクレジット カードがない、またはクレジット カードが無効。

    **解決策**:サブスクリプションに有効なクレジット カードを追加します。

- サブスクリプションの所有者でないため、ドメインを購入する権限がありません。

    **解決策**:アカウントに [所有者ロールを割り当て](../role-based-access-control/role-assignments-portal.md)ます。 または、サブスクリプションの管理者に連絡し、ドメインを購入する権限を取得します。
- サブスクリプションで、ドメイン購入の上限に達しました。 現在の上限は 20 です。

    **解決策**:上限の増加を要求するには、[Azure サポート](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)にお問い合わせください。
- ご利用の Azure サブスクリプションの種類は、App Service ドメインの購入をサポートしていません。

    **解決策**:Azure サブスクリプションを、従量課金制サブスクリプションなどの別のサブスクリプションの種類にアップグレードします。

### <a name="you-cant-add-a-host-name-to-an-app"></a>アプリにホスト名を追加できない 

#### <a name="symptom"></a>症状

ホスト名を追加するときに、ドメインの確認と検証のプロセスが失敗します。

#### <a name="cause"></a>原因 

この問題は、次のいずれかの理由で発生します。

- ホスト名を追加するための権限がありません。

    **解決策**:サブスクリプションの管理者に、ホスト名を追加する権限を与えるよう依頼します。
- ドメインの所有権を確認できませんでした。

    **解決策**:CNAME レコードまたは A レコードが正しく構成されていることを確認します。 アプリにカスタム ドメインをマップするには、CNAME レコードまたは A レコードのいずれかを作成します。 ルート ドメインを使用する場合は、A レコードおよび TXT レコードを使用する必要があります。

    |レコード タイプ|Host|参照先|
    |------|------|-----|
    |A|@|アプリの IP アドレス|
    |TXT|@|`<app-name>.azurewebsites.net`|
    |CNAME|www|`<app-name>.azurewebsites.net`|

## <a name="faq"></a>よく寄せられる質問

**カスタム ドメインを購入した後、それを Web サイト用に構成する必要がありますか**

Azure Portal からドメインを購入した場合、App Service アプリケーションは、そのカスタム ドメインを使用するように自動的に構成されます。 追加の手順を実行する必要はありません。 詳細については、Channel9 の [Azure App Service のセルフ ヘルプ: カスタム ドメイン名の追加](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name)に関するページを参照してください。

**Azure Portal で購入されたドメインを使用して、代わりに Azure VM を指すことはできますか**

はい。そのドメインで VM を指すことができます。 詳細については、「[Azure DNS を使用して Azure サービス用のカスタム ドメイン設定を提供する](../dns/dns-custom-domain.md)」をご覧ください。

**このドメインは GoDaddy または Azure DNS によってホストされますか**

App Service ドメインはドメイン登録のために GoDaddy を使用し、Azure DNS を使用してドメインをホストします。 

**自動更新を有効にしているにもかかわらず、依然として電子メール経由でドメインの更新通知を受信しました。どうすればよいですか。**

自動更新を有効にしている場合は、何も対処する必要はありません。 その通知電子メールは、ドメインが期限切れに近づいていることを通知し、自動更新が有効になっていない場合は手動で更新するために提供されます。

**ドメインをホストしている Azure DNS に対して課金されますか**

ドメイン購入の初期コストは、ドメイン登録にのみ適用されます。 登録コストに加えて、使用状況に基づいた Azure DNS に対する料金が発生します。 詳細については、「[Azure DNS の価格](https://azure.microsoft.com/pricing/details/dns/)」を参照してください。

**以前に Azure Portal からドメインを購入しており、GoDaddy ホスティングから Azure DNS ホスティング移行したいと考えています。どうすればよいですか。**

Azure DNS ホスティングへの移行は必須ではありません。 Azure DNS に移行したい場合は、Azure Portal でのドメイン管理エクスペリエンスによって、Azure DNS に移行するために必要な手順に関する情報が提供されます。 そのドメインが App Service 経由で購入された場合、GoDaddy ホスティングから Azure DNS への移行は比較的シームレスな手順になります。

**App Service ドメインからドメインを購入したいと考えていますが、Azure DNS の代わりに GoDaddy でドメインをホストできますか**

2017 年 7 月 24 日から、ポータルで購入された App Service ドメインは Azure DNS でホストされます。 別のホスティング プロバイダーを使用したい場合は、そのプロバイダーの Web サイトにアクセスしてドメイン ホスティング ソリューションを入手する必要があります。

**ドメインのプライバシー保護に対して支払う必要がありますか**

Azure Portal 経由でドメインを購入した場合は、追加コストなしでプライバシーを追加することを選択できます。 これは、Azure App Service 経由でドメインを購入する利点の 1 つです。

**ドメインが必要なくなったと判断した場合、返金を受けることはできますか**

ドメインを購入した場合、5 日間は課金されません。その期間内に、そのドメインが必要ないことを判断できます。 その 5 日の期間内にドメインが必要ないと判断した場合は、課金されません。 (.uk ドメインはこれの例外です。 .uk ドメインを購入した場合は、直ちに課金され、返金を受けることはできません。)

**サブスクリプションの別の Azure App Service アプリでドメインを使用できますか**

はい。 Azure Portal でカスタム ドメインや TLS ブレードにアクセスすると、購入したドメインが表示されます。 これらのドメインのいずれかを使用するようにアプリを構成できます。

**ドメインをあるサブスクリプションから別のサブスクリプションに転送できますか**

[Move-AzResource](/powershell/module/az.Resources/Move-azResource) PowerShell コマンドレットを使用して、ドメインを別のサブスクリプションやリソース グループに移動できます。

**現在 Azure App Service アプリがない場合、カスタム ドメインを管理するにはどうすればよいですか**

App Service Web Apps がない場合でも、ドメインを管理できます。 ドメインは、仮想マシン、ストレージなどの Azure サービスのために使用できます。ドメインを App Service Web Apps のために使用する場合は、そのドメインを Web アプリにバインドするために、Free App Service プランにない Web アプリを含める必要があります。

**カスタム ドメインを含む Web アプリを別のサブスクリプションに、または App Service 環境 v1 から V2 に移動できますか**

はい。Web アプリはサブスクリプション間で移動できます。 [Azure でリソースを移動する方法](../azure-resource-manager/management/move-resource-group-and-subscription.md)に関するページにあるガイダンスに従ってください。 Web アプリを移動する場合は、いくつかの制限があります。 詳細については、[App Service リソースを移動するための制限](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)に関するページを参照してください。

Web アプリを移動した後、カスタム ドメイン設定内のドメインのホスト名バインディングは同じままになります。 ホスト名バインディングを構成するための追加の手順は必要ありません。