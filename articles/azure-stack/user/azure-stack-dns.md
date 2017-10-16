---
title: "Azure Stack の DNS | Microsoft Docs"
description: "Azure Stack の DNS"
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/25/2017
ms.author: victorh
ms.openlocfilehash: ac93b5eb4228cef373428b7b69932d5993d54fa0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="dns-in-azure-stack"></a>Azure Stack の DNS

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Azure Stack には次の DNS 機能が含まれています。
* DNS ホスト名解決のサポート
* API を使った DNS ゾーンとレコードの作成と管理

## <a name="support-for-dns-hostname-resolution"></a>DNS ホスト名解決のサポート
パブリック IP リソースに DNS ドメイン名ラベルを指定して、Azure Stack で管理される DNS サーバーのパブリック IP アドレスに対する *domainnamelabel.location*.cloudapp.azurestack.external のマッピングを作成できます。  

たとえば、ドメイン名ラベルとして **contoso** を使用したパブリック IP リソースをローカルの Azure Stack の場所に作成した場合、完全修飾ドメイン名 (FQDN) である **contoso.local.cloudapp.azurestack.external** は、このリソースのパブリック IP アドレスに解決されます。 この FQDN を使用して、Azure Stack 内のパブリック IP アドレスをポイントするカスタム ドメイン CNAME レコードを作成できます。

> [!IMPORTANT]
> 作成された各ドメイン名ラベルは、Azure Stack の場所内で一意である必要があります。

ポータルを使用してパブリック IP アドレスを作成する場合は、次のような画面になります。

![パブリック IP アドレスの作成](media/azure-stack-whats-new-dns/image01.png)

この構成は、パブリック IP アドレスを負荷分散リソースと関連付ける場合に便利です。 たとえば、Web アプリケーションからの要求を処理するロード バランサーがあり、 その背後には、1 つ以上の仮想マシン上に置かれた Web サイトがあるとします。 この場合、IP アドレスではなく、DNS 名で負荷分散 Web サイトにアクセスすることができます。

## <a name="create-and-manage-dns-zones-and-records-using-api"></a>API を使った DNS ゾーンとレコードの作成と管理
Azure Stack 内に DNS ゾーンとレコードを作成して管理することができます。  

Azure Stack は、Azure の DNS API と同じ API を使用して、Azure と同じような DNS サービスを提供します。  Azure Stack DNS でドメインをホストすることで、他の Azure サービスと同じ資格情報、API、ツール、課金情報、サポートを使用して DNS レコードを管理できます。 

言うまでもなく、Azure Stack DNS のインフラストラクチャは Azure のインフラストラクチャよりもコンパクトです。 したがって、スコープ、スケール、およびパフォーマンスは、Azure Stack のデプロイの規模と、そのデプロイ先の環境に依存します。  そのため、パフォーマンス、可用性、グローバル配布、高可用性 (HA) などは、デプロイに応じて変わってきます。

## <a name="comparison-with-azure-dns"></a>Azure DNS との比較
Azure Stack の DNS は Azure の DNS と似ていますが、次の 2 つの点が大きく異なります。
* **AAAA レコードのサポートがない**

    Azure Stack では IPv6 アドレスがサポートされないので、AAAA レコードもサポートされません。  これは、Azure と Azure Stack の DNS の大きな違いのひとつです。
* **マルチテナントではない**

    Azure とは異なり、Azure Stack の DNS サービスはマルチテナントではありません。 そのため、各テナントで同じ DNS ゾーンを作成することはできません。 ゾーンの作成を試みた最初のサブスクリプションだけが成功し、その後の要求は失敗します。  これは既知の問題で、Azure と Azure Stack の DNS の大きな違いのひとつです。 この問題は今後のリリースで修正される予定です。

さらに、Azure Stack DNS でのタグ、メタデータ、Etag、および制限の実装方法にも多少の違いがあります。

以下に示す情報は Azure Stack DNS だけに適用されるものであり、Azure DNS と比べると若干の違いがあります。 Azure DNS の詳細については、Microsoft Azure ドキュメント サイトの [DNS ゾーンとレコード](../../dns/dns-zones-records.md)に関するページを参照してください。

### <a name="tags-metadata-and-etags"></a>タグ、メタデータ、Etag

**タグ**

Azure Stack DNS では、DNS ゾーン リソースに対して Azure Resource Manager のタグを使用できます。 DNS レコード セットのタグはサポートされませんが、その代わりとして、DNS レコード セットでは、次に説明する "メタデータ" がサポートされます。

**Metadata**

レコード セットのタグの代わりに、Azure Stack DNS では "メタデータ" を使用してレコード セットに注釈を付けることができます。 メタデータを使用すると、タグと同じように、各レコード セットに名前と値のペアを関連付けることができます。 これは、各レコード セットの用途を記録しておきたい場合などに便利です。 タグと異なる点として、メタデータは、Azure の課金内容に関するフィルター ビューを提供するためには使用できず、Azure Resource Manager のポリシーで指定することもできません。

**Etag**

たとえば、2 人のユーザーまたは 2 つのプロセスが同時に DNS レコードを変更しようとするとします。 どちらの変更が優先されるでしょうか。 また、優先された側は、他のユーザーまたはプロセスによって行われた変更を上書きしたことに気付くのでしょうか。

Azure Stack DNS は、Etag を使用して同じリソースへの同時変更を安全に処理します。 Etag と Azure Resource Manager の "タグ" は独立しています。 各 DNS リソース (ゾーンまたはレコード セット) には、関連付けられている Etag があります。 リソースが取得されるときは、常に Etag も取得されます。 リソースを更新する場合は、Azure Stack DNS がサーバー上の Etag の一致を確認できるように Etag を返すこともできます。 リソースを更新するたびに Etag が再生成されるため、Etag の不一致は同時変更が発生していることを示します。 Etag は、既存のリソースがないことを確認するために、新しいリソースの作成時にも使用されます。

Azure Stack DNS PowerShell は、既定で、ゾーンおよびレコード セットへの同時変更のブロックに Etag を使用します。 オプションの *-Overwrite* スイッチを使用すると、Etag チェックを実行しないように設定できます。この場合、発生したすべての同時変更が上書きされます。

Azure Stack DNS REST API のレベルでは、HTTP ヘッダーを使用して Etag を指定します。 次の表に各ヘッダーの動作を示します。

| ヘッダー | 動作|
|--------|---------|
| なし   | PUT は常に成功します (Etag チェックなし)。|
| If-match| PUT は、リソースが存在し、Etag が一致する場合にのみ成功します|
| If-match *| PUT はリソースが存在する場合にのみ成功します|
| If-none-match *| PUT はリソースが存在しない場合にのみ成功します|

### <a name="limits"></a>制限

Azure Stack DNS を使用する際は、次の制限が既定で適用されます。

| リソース| 既定の制限|
|---------|--------------|
| サブスクリプションあたりのゾーン数| 100|
| ゾーンあたりのレコード セット数| 5000|
| レコード セットあたりのレコード数| 20|

## <a name="next-steps"></a>次のステップ
[Azure Stack の iDNS の概要](azure-stack-understanding-dns.md)
