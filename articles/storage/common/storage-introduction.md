---
title: "Azure Storage の概要 | Microsoft Docs"
description: "クラウドにおける Microsoft のデータ ストレージ Azure Storage について概要を紹介します。"
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: a4a1bc58-ea14-4bf5-b040-f85114edc1f1
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/09/2017
ms.author: tamram
ms.openlocfilehash: e7b32aa2de5d6501e8d7894a936e9ab8b2f4f42f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-microsoft-azure-storage"></a>Microsoft Azure Storage の概要

Microsoft Azure Storage は、高い可用性とセキュリティ、耐久性、スケーラビリティ、冗長性を備えたストレージを提供する、Microsoft が管理するクラウド サービスです。 メンテナンスや重大な問題には、Microsoft がお客様に代わって対処します。 

Azure Storage は、Blob Storage、File Storage、Queue Storage の 3 つのデータ サービスから成ります。 Blob Storage は、Standard Storage と Premium Storage の両方をサポートします。Premium Storage では、パフォーマンスを最大限に引き出すために SSD のみが使われます。 さらに別の機能として、利用頻度の低い大量のデータを低コストで保管できるクール ストレージがあります。

この記事で取り上げる内容は次のとおりです。
* Azure Storage サービス
* ストレージ アカウントの種類
* BLOB、キュー、ファイルへのアクセス
* 暗号化
* レプリケーション 
* ストレージへのデータの転送とストレージからのデータの転送
* 公開されている各種ストレージ クライアント ライブラリ 

Azure Storage を今すぐ立ち上げ、使用を開始するには、次のクイック スタートを参照してください。
* [PowerShell を使用したストレージ アカウントの作成](storage-quickstart-create-storage-account-powershell.md)
* [CLl を使用したストレージ アカウントの作成](storage-quickstart-create-storage-account-cli.md)

## <a name="introducing-the-azure-storage-services"></a>Azure Storage サービスの概要

Azure Storage で提供されているいずれかのサービス (Blob Storage、File Storage、Queue Storage) を使うにはまず、ストレージ アカウントを作成します。その作成したストレージ アカウント内の特定のサービスに対してデータを転送したり、そこからデータを転送したりすることができます。 

## <a name="blob-storage"></a>BLOB ストレージ

BLOB は基本的には、お使いのコンピューター (またはタブレット、モバイル デバイスなど) に保管されているものと同じようなファイルです。 画像や Microsoft Excel ファイル、HTML ファイル、仮想ハード ディスク (VHD) から、ログやデータベースのバックアップなど大きなデータまで、ほとんど何でも格納することができます。 BLOB は、フォルダーに似たコンテナーに格納されます。 

Blob Storage に保存したファイルには、URL や REST インターフェイスのほか、いずれかの Azure SDK ストレージ クライアント ライブラリを使って世界中のどこからでもアクセスすることができます。 ストレージ クライアント ライブラリは、Node.js、Java、PHP、Ruby、Python、.NET などさまざまな言語で利用できます。 

BLOB には、ブロック BLOB、ページ BLOB (VHD ファイル用)、追加 BLOB の 3 種類があります。

* ブロック BLOB は、約 4.7 TB までの標準的なファイルの保存に使用されます。 
* ページ BLOB は最大 8 TB のランダム アクセス ファイルの保存に使用されます。 VM に使用される VHD ファイルには、これらが使用されます。
* 追加 BLOB は、ブロック BLOB のようなブロックから成りますが、追加操作に最適化されています。 同じ BLOB に対して複数の VM から情報を記録するような用途に使用されます。

ネットワーク上の制限があるために、有線接続経由で Blob Storage にデータをアップロードまたはダウンロードできない場合は、一連のハード ドライブを Microsoft に送付し、データ センターから直接データをインポートまたはエクスポートできます。 「 [Microsoft Azure Import/Export サービスを使用した Blob Storage へのデータの転送](../storage-import-export-service.md)」を参照してください。

## <a name="azure-files"></a>Azure Files
[Azure Files](../files/storage-files-introduction.md) では、標準的なサーバー メッセージ ブロック (SMB) プロトコルを使用してアクセスできる可用性の高いネットワーク ファイル共有を設定することができます。 つまり、複数の VM が、読み取りアクセス権と書き込みアクセス権の両方を使用して同じファイルを共有できます。 また、REST インターフェイスまたはストレージ クライアント ライブラリを使用してファイルを読み取ることもできます。 

Azure Files と企業のファイル共有上のファイルとの違いの 1 つは、世界中のどこからでも、ファイルを指し、共有アクセス署名 (SAS) トークンを含む URL を使用して、ファイルにアクセスできることです。 SAS トークンは生成することができます。SAS トークンを使用することで、特定の期間にわたってプライベートな資産への特定のアクセスを許可できます。 

ファイル共有は、多くの一般的なシナリオで使用できます。 

* 多くのオンプレミスのアプリケーションでファイル共有が使用されます。 この機能により、データを共有するそれらのアプリケーションを Azure に簡単に移行できます。 オンプレミスのアプリケーションで使用されているのと同じドライブ文字にファイル共有をマウントすることにより、ファイル共有にアクセスするアプリケーションの部分は、(変更があっても) 最小限の変更で動作します。

* 構成ファイルをファイル共有に格納して、複数の VM からアクセスできます。 グループ内の複数の開発者によって使用されるツールやユーティリティをファイル共有に格納できるため、だれでもそれらを見つけ、同じバージョンを使用することができます。

* ファイル共有に書き込んだ後で処理または分析できるデータの例として、診断ログ、メトリック、およびクラッシュ ダンプの 3 つが挙げられます。

Active Directory ベースの認証およびアクセス制御リスト (ACL) は現時点でサポートされていませんが、将来的にサポートされる予定です。 ファイル共有へのアクセスの認証を提供するために、ストレージ アカウントの資格情報が使用されます。 これは、共有をマウントしている全員に、共有に対する完全な読み取り/書き込みアクセス権が与えられることを意味します。

## <a name="queue-storage"></a>Queue Storage

Azure Queue サービスは、メッセージの保管と取得に使用します。 キューに許容されるメッセージ サイズは最大 64 KB で、1 つのキューに数百万件のメッセージを格納することができます。 一般に、非同期的に処理するメッセージのリストを格納するのがキューの用途となります。 

たとえば、画像をアップロードしてそれぞれにサムネイルを作成する機能をユーザーに提供するとしましょう。 もちろん画像のアップロードの際、サムネイルを作成するまでユーザーに待ってもらうことはできます。 しかし、その代わりの方法として、キューを使うこともできます。 ユーザーからのアップロードが完了したら、メッセージをキューに書き込みます。 その後、Azure Functions でキューからメッセージを取得して、サムネイルを作成するのです。 この処理では、各段階を個別にスケーリングすることができるので、用途に応じたチューニングの自由度が高まります。

## <a name="table-storage"></a>テーブル ストレージ

Cosmos DB に、新たに Standard Azure Table Storage が加わりました。 ドキュメントについては、[Azure Table Storage の概要](../../cosmos-db/table-storage-overview.md)を参照してください。 Azure Table Storage の Premium テーブルにも対応しているので、スループットに最適化されたテーブル、グローバルな分散、自動セカンダリ インデックスを利用することができます。 詳細を確認し、新しい Premium 版を使ってみるには、[Azure Cosmos DB の Table API](https://aka.ms/premiumtables) に関する記事を参照してください。

## <a name="disk-storage"></a>ディスク ストレージ

Azure Storage には、仮想マシンで使用される管理ディスクと非管理対象ディスクの機能も含まれています。 これらの機能の詳細については、[コンピューティング サービスのドキュメント](https://docs.microsoft.com/azure/#pivot=services&panel=Compute)を参照してください。

## <a name="types-of-storage-accounts"></a>ストレージ アカウントの種類 

次の表は各種のストレージ アカウントと、それぞれのストレージ アカウントで使用できるオブジェクトとを示したものです。

|**ストレージ アカウントの種類**|**汎用 (Standard)**|**汎用 (Premium)**|**Blob Storage (ホット アクセス レベルとクール アクセス レベル)**|
|-----|-----|-----|-----|
|**サポートされるサービス**| Blob service、File サービス、Queue サービス | Blob service | Blob service|
|**サポートされる BLOB の種類**|ブロック BLOB、ページ BLOB、追加 BLOB | ページ blob | ブロック BLOB と追加 BLOB|

### <a name="general-purpose-storage-accounts"></a>汎用ストレージ アカウント

汎用ストレージ アカウントは 2 種類存在します。 

#### <a name="standard-storage"></a>Standard Storage 

最も一般的に使用されているストレージ アカウントが Standard ストレージ アカウントであり、あらゆるタイプのデータに使用できます。 Standard ストレージ アカウントでは、磁気媒体を使ってデータが保存されます。

#### <a name="premium-storage"></a>Premium Storage

Premium Storage はページ BLOB 向けのハイパフォーマンスのストレージであり、主に VHD ファイルに使用されます。 Premium ストレージ アカウントでは、SSD を使ってデータが保存されます。 Microsoft では、あらゆる VM に Premium Storage の使用を推奨しています。

### <a name="blob-storage-accounts"></a>BLOB ストレージ アカウント

BLOB ストレージ アカウントは、ブロック BLOB と追加 BLOB の格納に特化したストレージ アカウントです。 これらのアカウントにページ BLOB を格納することはできません。したがって VHD ファイルを格納することはできません。 これらのアカウントでは、アクセス レベルを "ホット" または "クール" に設定することができ、レベルはいつでも変更できます。 

ホット アクセス レベルは、アクセス頻度の高いファイルに使用します。対価としてのストレージ コストは高くなりますが、BLOB へのアクセス コストは大幅に削減されます。 クール アクセス レベルに BLOB を保存した場合、BLOB へのアクセス コストは大きくなりますが、ストレージ コストは大幅に削減されます。

## <a name="accessing-your-blobs-files-and-queues"></a>BLOB、ファイル、キューへのアクセス

各ストレージ アカウントには認証キーが 2 つあり、どちらを使っても、すべての操作を実行することができます。 キーが 2 つあるため、キーを時折ロール オーバーしてセキュリティを強化することができます。 アカウント名と共にキーを手に入れると、ストレージ アカウントのあらゆるデータに無制限でアクセスできるため、キーを安全に保つことがきわめて重要となります。 

このセクションでは、ストレージ アカウントとそのデータのセキュリティを確保する 2 つの方法について説明します。 ストレージ アカウントとデータのセキュリティについて詳しくは、「[Azure Storage security guide (Azure Storage セキュリティ ガイド)](storage-security-guide.md)」を参照してください。

### <a name="securing-access-to-storage-accounts-using-azure-ad"></a>Azure AD を使ってストレージ アカウントへのアクセスのセキュリティを確保する

ストレージ データへのアクセスのセキュリティを確保する方法の 1 つとして、ストレージ アカウント キーへのアクセスを制御する方法が考えられます。 Resource Manager のロールベースのアクセス制御 (RBAC) を使って、ユーザー、グループ、アプリケーションのいずれかにロールを割り当てることができます。 これらのロールは、ひとまとめにして許可または禁止される一連の操作に関連付けられます。 RBAC を使ってストレージ アカウントへのアクセスを許可する場合、対象となるのは、そのストレージ アカウントの管理操作だけです (アクセス レベルを変更するなど)。 RBAC を使って、データ オブジェクト (特定のコンテナー、特定のファイル共有など) へのアクセスを許可することはできません。 ただし RBAC を使ってストレージ アカウント キーへのアクセスを許可したうえで、そのキーをデータ オブジェクトの読み取りに使用することはできます。 

### <a name="securing-access-using-shared-access-signatures"></a>Shared Access Signature を使ってアクセスのセキュリティを確保する 

データ オブジェクトのセキュリティは、Shared Access Signature と保存されているアクセス ポリシーを使って確保できます。 Shared Access Signature (SAS) は、セキュリティ トークンを含む文字列です。資産の URI にセキュリティ トークンをアタッチすることで、特定のストレージ オブジェクトへのアクセスを委任し、アクセス許可やアクセスの日時の範囲などの制約を指定することができます。 この機能には、さまざまな可能性があります。 詳細については、「[Using Shared Access Signatures (SAS) (Shared Access Signature (SAS) の使用)](storage-dotnet-shared-access-signature-part-1.md)」を参照してください。

### <a name="public-access-to-blobs"></a>BLOB へのパブリック アクセス

Blob service を通じて、コンテナーとその BLOB (または特定の BLOB) へのパブリック アクセスを提供することができます。 コンテナーまたは BLOB をパブリックとして指定すると、認証が不要になり、すべてのユーザーが匿名でリソースを読み取ることができます。 たとえば、Blob Storage に格納されている画像やビデオ、ドキュメントを使った Web サイトがある場合などにこの方法を利用できます。 詳細については、「[コンテナーと BLOB への匿名読み取りアクセスを管理する](../blobs/storage-manage-access-to-resources.md)」を参照してください。 

## <a name="encryption"></a>暗号化

ストレージ サービスには、いくつか基本的な種類の暗号化を使用できます。 

### <a name="encryption-at-rest"></a>保存時の暗号化 

Storage サービスの暗号化 (SSE) は、Azure Storage アカウントの Files サービス (プレビュー) と Blob service のどちらでも有効にすることができます。 有効にした場合、特定のサービスに書き込まれるすべてのデータは、書き込みの前に暗号化されます。 データを読み取るときは、暗号化が解除されてからデータが返されます。 

### <a name="client-side-encryption"></a>クライアント側暗号化

ストレージ クライアント ライブラリのメソッドを呼び出すことで、プログラムによってデータを暗号化したうえで、クライアントからネットワーク経由で Azure に送信することができます。 データは暗号化された状態で保存されます。つまり、保存状態でも暗号化されます。 データを再度読み取るときは、その情報を受信した後で暗号化を解除することになります。 

### <a name="encryption-in-transit-with-azure-file-shares"></a>Azure ファイル共有を使った転送中の暗号化

Shared Access Signature の詳細については、「 [Using Shared Access Signatures (SAS) (Shared Access Signature (SAS) の使用)](../storage-dotnet-shared-access-signature-part-1.md) 」を参照してください。 ストレージ アカウントへのセキュリティで保護されたアクセスの詳細については、「[コンテナーと BLOB への匿名読み取りアクセスを管理する](../blobs/storage-manage-access-to-resources.md)」および「[Azure Storage サービスの認証](https://msdn.microsoft.com/library/azure/dd179428.aspx)」を参照してください。

ストレージ アカウントのセキュリティ保護と暗号化の詳細については、「[Azure Storage security guide (Azure Storage セキュリティ ガイド)](storage-security-guide.md)」を参照してください。

## <a name="replication"></a>レプリケーション

Azure Storage は、データの耐久性を確保するために、データのコピーを複数維持し、管理できるようになっています。 これを "レプリケーション" といいます ("冗長性" と呼ばれることもあります)。 レプリケーションの種類は、ストレージ アカウントをセットアップするときに選択します。 この設定は、ほとんどの場合、ストレージ アカウントのセットアップ後に変更することができます。 

すべてのストレージ アカウントには、**ローカル冗長ストレージ (LRS)** が存在します。 この場合、ストレージ アカウントのセットアップ時に指定したデータ センターで、データのコピーが 3 つ Azure Storage によって管理されます。 ある 1 つのコピーに対して変更がコミットされたとき、成功の応答が返されるのは、他の 2 つのコピーが更新された後となります。 つまり、3 つのレプリカは常に同期された状態になります。また、3 つのコピーは、それぞれ別個の障害ドメインおよびアップグレード ドメインに置かれるため、データを格納しているストレージ ノードに障害が発生したり更新のためにオフラインにされたりしても、データの可用性は維持されます。 

**ローカル冗長ストレージ (LRS)**

前述のように、LRS では、1 つのデータセンターにデータのコピーが 3 つ作成されます。 この方法では、ストレージ ノードに障害が発生したり更新のためにオフラインにされたりした場合にデータが利用できなくなる問題には対処できますが、データセンター全体が利用できなくなった場合は対処できません。

**ゾーン冗長ストレージ (ZRS)**

ゾーン冗長ストレージ (ZRS) では、データのローカル コピーが 3 つ保持されることに加え、さらに別のコピーが 3 つ保持されます。 2 つ目のセット (3 つのコピー) は、1 つまたは 2 つのリージョン内の複数のデータセンターに対して非同期的にレプリケートされます。 ZRS が利用できるのは、汎用ストレージ アカウントのブロック BLOB に限られるのでご注意ください。 また、ストレージ アカウントを作成し、ZRS を選択すると、他の種類のレプリケーションを使用するように変換することはできません (その逆の変換もできません)。

ZRS アカウントは、LRS よりも耐久性は高いですが、ZRS アカウントにはメトリックやログ機能がありません。 

**geo 冗長ストレージ (GRS)**

geo 冗長ストレージ (GRS) では、プライマリ リージョンにデータのローカル コピーが 3 つ保持されることに加え、プライマリ リージョンから数百マイル離れたセカンダリ リージョンに、さらに別のコピーが 3 つ保持されます。 プライマリ リージョンで障害が発生すると、Azure Storage はセカンダリ リージョンにフェールオーバーします。 

**読み取りアクセス geo 冗長ストレージ (RA-GRS)** 

読み取りアクセス geo 冗長ストレージは、セカンダリ ロケーションにあるデータへのアクセスが読み取りアクセスになる点を除き、GRS とまったく同じです。 プライマリ データ センターが一時的に利用できない状態になった場合、セカンダリ ロケーションからデータを読み取ることができます。 これは非常に有益な機能です。 たとえばご利用の Web アプリケーションで読み取り専用モードに切り替えてセカンダリ コピーを参照すれば、更新はできなくても、何らかのアクセスが可能となります。 

> [!IMPORTANT]
> アカウントを作成したときに ZRS を指定した場合を除き、ストレージ アカウントを作成した後、データのレプリケート方法を変更することはできます。 ただし、LRS から GRS、または RA-GRS に切り替えると、一時的に追加のデータ転送コストが生じる可能性があります。
>

レプリケーションの詳細については、「[Azure Storage replication (Azure Storage レプリケーション)](storage-redundancy.md)」を参照してください。

ディザスター リカバリーについては、「[Azure Storage の停止が発生した場合の対処方法](storage-disaster-recovery-guidance.md)」を参照してください。

RA-GRS ストレージを利用して高可用性を確保する方法の例については、「[RA-GRS を使用した高可用性アプリケーションの設計](storage-designing-ha-apps-with-ragrs.md)」を参照してください。

## <a name="transferring-data-to-and-from-azure-storage"></a>Azure Storage との間のデータ転送

AzCopy コマンド ライン ユーティリティを使用して、BLOB やファイルのデータを同じストレージ アカウント内で、または別のストレージ アカウントとの間でコピーすることができます。 詳しくは、次のいずれかの記事を参照してください。

* [AzCopy for Windows を使ったデータの転送](storage-use-azcopy.md)
* [AzCopy for Linux を使ったデータの転送](storage-use-azcopy-linux.md)

AzCopy は [Azure Data Movement Library](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/) を基盤として構築されています。このライブラリは現時点ではプレビューとして公開されています。

Azure Import/Export サービスを利用すると、ストレージ アカウントとの間で大量の BLOB データをインポートまたはエクスポートできます。 複数のハード ドライブを用意して Azure データ センターに発送すると、そのデータ センターで、ハード ドライブにデータを転送 (またはハード ドライブからデータを転送) して返送してくれます。 Import/Export サービスの詳細については、「 [Microsoft Azure Import/Export サービスを使用した Blob Storage へのデータの転送](../storage-import-export-service.md)」を参照してください。

## <a name="pricing"></a>価格

Azure Storage の価格の詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/storage/blobs/)を参照してください。

## <a name="storage-apis-libraries-and-tools"></a>ストレージ API、ライブラリ、ツール
Azure Storage のリソースは、HTTP/HTTPS 要求が可能な任意の言語からアクセスできます。 さらに、主要な複数の言語のプログラミング ライブラリも用意されています。 これらのライブラリを使用すると、同期呼び出しと非同期呼び出し、操作のバッチ処理、例外管理、自動再試行、実行動作などの詳細が処理され、Azure Storage の使用に関するさまざまな側面が簡略化されます。 ライブラリは、次の言語およびプラットフォーム用が現在提供されており、その他についても準備中です。

### <a name="azure-storage-data-services"></a>Azure Storage データ サービス
* [Storage サービス REST API](/rest/api/storageservices/)
* [.NET 用ストレージ クライアント ライブラリ](https://docs.microsoft.com/dotnet/api/?view=azurestorage-8.1.1)
* [C++ 用ストレージ クライアント ライブラリ](https://github.com/Azure/azure-storage-cpp)
* [Java/Android 用ストレージ クライアント ライブラリ](https://azure.microsoft.com/develop/java/)
* [Node.js 用ストレージ クライアント ライブラリ](http://dl.windowsazure.com/nodestoragedocs/index.html)
* [PHP 用ストレージ クライアント ライブラリ](https://azure.microsoft.com/develop/php/)
* [Python 用ストレージ クライアント ライブラリ](https://azure.microsoft.com/develop/python/)
* [Ruby 用ストレージ クライアント ライブラリ](https://azure.microsoft.com/develop/ruby/)
* [PowerShell 用ストレージ コマンドレット](/powershell/module/azure.storage/?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)
* [CLI 2.0 用ストレージ コマンド](/cli/azure/storage)

## <a name="next-steps"></a>次のステップ

* [Blob Storage の詳細](../blobs/storage-blobs-introduction.md)
* [File Storage の詳細](../storage-files-introduction.md)
* [Queue Storage の詳細](../queues/storage-queues-introduction.md)

Azure Storage を今すぐ立ち上げ、使用を開始するには、次のクイック スタートを参照してください。
* [PowerShell を使用したストレージ アカウントの作成](storage-quickstart-create-storage-account-powershell.md)
* [CLl を使用したストレージ アカウントの作成](storage-quickstart-create-storage-account-cli.md)

<!-- FIGURE OUT WHAT TO DO WITH ALL THESE LINKS.

Azure Storage resources can be accessed by any language that can make HTTP/HTTPS requests. Additionally, Azure Storage offers programming libraries for several popular languages. These libraries simplify many aspects of working with Azure Storage by handling details such as synchronous and asynchronous invocation, batching of operations, exception management, automatic retries, operational behavior and so forth. Libraries are currently available for the following languages and platforms, with others in the pipeline:

### Azure Storage data services
* [Storage Services REST API](https://docs.microsoft.com/rest/api/storageservices/)
* [Storage Client Library for .NET](https://docs.microsoft.com/dotnet/api/?view=azurestorage-8.1.1)
* [Storage Client Library for C++](https://github.com/Azure/azure-storage-cpp)
* [Storage Client Library for Java/Android](https://azure.microsoft.com/develop/java/)
* [Storage Client Library for Node.js](http://dl.windowsazure.com/nodestoragedocs/index.html)
* [Storage Client Library for PHP](https://azure.microsoft.com/develop/php/)
* [Storage Client Library for Python](https://azure.microsoft.com/develop/python/)
* [Storage Client Library for Ruby](https://azure.microsoft.com/develop/ruby/)
* [Storage Cmdlets for PowerShell](/powershell/module/azure.storage/?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)

### Azure Storage management services
* [Storage Resource Provider REST API Reference](/rest/api/storagerp/)
* [Storage Resource Provider Client Library for .NET](/dotnet/api/microsoft.azure.management.storage)
* [Storage Resource Provider Cmdlets for PowerShell 1.0](/powershell/module/azure.storage)
* [Storage Service Management REST API (Classic)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### Azure Storage data movement services
* [Storage Import/Export Service REST API](../storage-import-export-service.md)
* [Storage Data Movement Client Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/)

### Tools and utilities
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) is a free, standalone app from Microsoft that enables you to work visually with Azure Storage data on Windows, macOS, and Linux.
* [Azure Storage Client Tools](../storage-explorers.md)
* [Azure SDKs and Tools](https://azure.microsoft.com/tools/)
* [Azure Storage Emulator](http://www.microsoft.com/download/details.aspx?id=43709)
* [Azure PowerShell](/powershell/azure/overview)
* [AzCopy Command-Line Utility](http://aka.ms/downloadazcopy)

## Next steps
To learn more about Azure Storage, explore these resources:

### Documentation
* [Azure Storage Documentation](https://azure.microsoft.com/documentation/services/storage/)
* [Create a storage account](../storage-create-storage-account.md)

-->

### <a name="for-administrators"></a>管理者向け
* [Azure Storage での Azure PowerShell の使用](storage-powershell-guide-full.md)
* [Azure Storage での Azure CLI の使用](../storage-azure-cli.md)

### <a name="for-net-developers"></a>.NET 開発者向け
* [.NET を使用して Azure Blob Storage を使用する](../blobs/storage-dotnet-how-to-use-blobs.md)
* [.NET での Azure Files 用の開発](../files/storage-dotnet-how-to-use-files.md)
* [.NET を使用して Azure Table Storage を使用する](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [.NET を使用して Azure Queue Storage を使用する](../storage-dotnet-how-to-use-queues.md)

### <a name="for-javaandroid-developers"></a>Java/Android 開発者向け
* [Java から BLOB ストレージを使用する方法](../blobs/storage-java-how-to-use-blob-storage.md)
* [Java を使用して Azure Files 用に開発する](../files/storage-java-how-to-use-file-storage.md)
* [Java からテーブル ストレージを使用する方法](../../cosmos-db/table-storage-how-to-use-java.md)
* [Java から Queue ストレージを使用する方法](../storage-java-how-to-use-queue-storage.md)

### <a name="for-nodejs-developers"></a>Node.js 開発者向け
* [Node.js から BLOB ストレージを使用する方法](../blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Node.js から Table ストレージを使用する方法](../../cosmos-db/table-storage-how-to-use-nodejs.md)
* [Node.js から Queue ストレージを使用する方法](../storage-nodejs-how-to-use-queues.md)

### <a name="for-php-developers"></a>PHP 開発者向け
* [PHP から BLOB ストレージを使用する方法](../blobs/storage-php-how-to-use-blobs.md)
* [PHP から Table ストレージを使用する方法](../../cosmos-db/table-storage-how-to-use-php.md)
* [PHP から Queue ストレージを使用する方法](../storage-php-how-to-use-queues.md)

### <a name="for-ruby-developers"></a>Ruby 開発者向け
* [Ruby から BLOB ストレージを使用する方法](../blobs/storage-ruby-how-to-use-blob-storage.md)
* [Ruby から Table ストレージを使用する方法](../../cosmos-db/table-storage-how-to-use-ruby.md)
* [Ruby から Queue ストレージを使用する方法](../storage-ruby-how-to-use-queue-storage.md)

### <a name="for-python-developers"></a>Python 開発者向け
* [Python から BLOB ストレージを使用する方法](../blobs/storage-python-how-to-use-blob-storage.md)
* [Python を使用して Azure Files 用に開発する](../files/storage-python-how-to-use-file-storage.md)
* [Python から Table ストレージを使用する方法](../../cosmos-db/table-storage-how-to-use-python.md)
* [Python から Queue ストレージを使用する方法](../storage-python-how-to-use-queue-storage.md)