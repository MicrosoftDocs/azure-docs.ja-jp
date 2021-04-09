---
title: エクスポート注文に関する Azure Data Box イベント、Azure Data Box Heavy イベントの追跡とログ記録 | Microsoft Docs
description: お使いの Azure Data Box と Azure Data Box Heavy のエクスポート注文のさまざまな段階で、イベントを追跡し、ログ記録を行う方法を説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 3a915ac8de83a5e183660ec4a3d05044eafff4a9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94337510"
---
# <a name="tracking-and-event-logging-for-your-azure-data-box-and-azure-data-box-heavy-export-orders"></a>お使いの Azure Data Box と Azure Data Box Heavy のエクスポート注文の追跡とイベント ログ記録

Data Box または Data Box Heavy のエクスポート注文は、注文、設定、データのコピー、返却、およびデータ消去の段階をたどります。 注文の各段階に対応して、注文へのアクセスの制御、イベントの監査、注文の追跡、生成されるさまざまなログの解釈のための、さまざまなアクションを実行できます。

この記事では、Data Box または Data Box Heavy のエクスポート注文の追跡と監査に使用できるさまざまなメカニズムやツールの詳細について説明します。 この記事の情報は、Data Box と Data Box Heavy の両方に適用されます。 後続のセクションにおいて、Data Box への言及はすべて、Data Box Heavy にも適用されます。

次の表で、Data Box のエクスポート注文の段階の概要と、各段階で注文の追跡と監査に使用できるツールを示します。

| Data Box のエクスポート注文の段階       | 追跡と監査のためのツール                                                                        |
|----------------------------|------------------------------------------------------------------------------------------------|
| 注文の作成               | [Azure RBAC を使用して注文へのアクセス制御を設定する](#set-up-access-control-on-the-order) <br> [注文の詳細ログを有効にする](#enable-verbose-log-in-the-order)                                                    |
| 処理された注文            | 以下を通じて[注文を追跡する](#track-the-order) <ul><li> Azure portal </li><li> 運送業者の Web サイト </li><li>メール通知</ul> |
| デバイスを設定する              | [アクティビティ ログ](#query-activity-logs-during-setup)に記録されたデバイスの資格情報へのアクセス              |
| デバイスからのデータのコピー        | [ログのコピーの確認](#copy-log) <br> データのコピー前の[詳細ログの確認](#verbose-log)            |
| デバイスからのデータの消去   | 監査ログと注文履歴を含む[生産物流管理ログを表示する](#get-chain-of-custody-logs-after-data-erasure)                |


## <a name="set-up-access-control-on-the-order"></a>注文へのアクセス制御を設定する

注文が初めて作成されたときに注文にアクセスできるユーザーを制御できます。 さまざまな範囲の Azure ロールを設定して、Data Box の注文へのアクセスを制御します。 Azure ロールは、アクセスの種類 (読み取り/書き込み、読み取り専用、操作のサブセットへの読み取り/書き込み) を決定します。

Azure Data Box サービスに対して定義できる 2 つのロールは次のとおりです。

- **Data Box 閲覧者** - スコープによって定義されている、注文に対する読み取り専用アクセス権を持ちます。 表示できるのは注文の詳細のみです。 ストレージ アカウントに関連するその他の詳細にアクセスしたり、住所などの注文の詳細を編集したりすることはできません。
- **Data Box 共同作成者** - *既にストレージ アカウントへの書き込みアクセス権を持っている場合は*、特定のストレージ アカウントにデータを転送するためにのみ注文を作成できます。 ストレージ アカウントへのアクセス権がない場合は、アカウントにデータをコピーするために Data Box の注文を作成することもできません。 このロールは、ストレージ アカウントに関連するアクセス許可を定義することも、ストレージ アカウントにアクセス権を付与することもありません。  

注文へのアクセスを制限するためには、次のようにすることができます。

- 注文レベルでロールを割り当てます。 その特定の Data Box の注文のみを操作し、その他は何も操作しないように、ユーザーはロールによって定義されているアクセス許可のみを持ちます。
- リソース グループ レベルのロールを割り当てます。ユーザーはリソース グループ内のすべての Data Box の注文へのアクセス権を持ちます。

推奨される Azure RBAC の使用方法の詳細については、「[Azure RBAC のベスト プラクティス](../role-based-access-control/best-practices.md)」を参照してください。

## <a name="enable-verbose-log-in-the-order"></a>注文の詳細ログを有効にする

Data Box のエクスポート注文を設定する場合、詳細ログの収集を有効にするかどうかのオプションがあります。 詳細ログを有効にする注文画面は次のとおりです。

![Select export option (エクスポート オプションの選択)](media/data-box-deploy-export-ordered/azure-data-box-export-order-export-option.png)

**[Include verbose log]\(詳細ログを含める\)** オプションを選択すると、お使いの Azure Storage アカウントからデータをコピーするときに詳細ログ ファイルが生成されます。 このログには、正常にエクスポートされたすべてのファイルが一覧表示されます。

エクスポート注文の詳細については、[Data Box のエクスポート注文の作成](data-box-deploy-export-ordered.md)に関するページを参照してください。

## <a name="track-the-order"></a>注文を追跡する

運送業者の Web サイトと Azure portal を通じて注文を追跡することができます。 Data Box の注文をいつでも追跡できるよう、次のメカニズムが用意されています。

- デバイスが Azure データセンターまたはオンプレミスにあるときに注文を追跡するには、Azure portal で **[Data Box order]\(Data Box の注文) > [概要]** の順に移動します。

    ![注文の状態と追跡番号の確認](media/data-box-logs/overview-view-status-1.png)

- デバイスの輸送中に注文を追跡するには、地域の運送業者の Web サイト (米国の UPS Web サイトなど) にアクセスします。 注文に関連付けられている追跡番号を入力します。
- また、Data Box は、注文が作成されたときに入力されたメールに基づいて、注文状態が変更されるたびにメール通知も送信します。 Data Box の注文のすべての状態の一覧については、「[注文の状態の確認](data-box-portal-admin.md#view-order-status)を参照してください。 注文に関連付けられている通知の設定を変更する場合は、「[通知の詳細の編集](data-box-portal-admin.md#edit-notification-details)」を参照してください。

## <a name="query-activity-logs-during-setup"></a>設定中のアクティビティ ログのクエリ

- Data Box は、お客様のオンプレミスにロックされた状態で届きます。 ご注文には Azure portal で使用可能なデバイスの資格情報を使用できます。  

    Data Box が設定されている場合は、デバイスの資格情報にアクセスしたすべてのユーザーがわかっている必要がある場合があります。 **[デバイスの資格情報]** ブレードにアクセスしたユーザーを調べるには、アクティビティ ログに対してクエリを実行することができます。  **[デバイスの詳細] > [資格情報]** ブレードへのアクセスに関連するアクションは、`ListCredentials` アクションとしてとしてアクティビティ ログに記録されます。

    ![アクティビティ ログのクエリ](media/data-box-logs/query-activity-log-1.png)

- Data Box へのサインインは、毎回リアルタイムでログに記録されます。 ただし、この情報は、注文が正常に完了した後に、[生産物流管理監査ログ](#chain-of-custody-audit-logs)からのみ参照できます。

## <a name="view-logs-during-data-copy"></a>データ コピー時のログの参照

お使いの Data Box からデータをコピーする前に、Data Box にコピーされたデータの *コピー ログ* と *詳細ログ* をダウンロードして確認できます。 これらのログは、Azure のお使いのストレージ アカウントからお使いの Data Box にデータがコピーされたときに生成されます。 

### <a name="copy-log"></a>コピー ログ

お使いの Data Box からデータをコピーする前に、 **[Connect and copy]** \(接続してコピー\) ページからコピー ログをダウンロードします。

次に示すのは、エラーが発生せず、Azure から Data Box デバイスへのデータ コピー中にすべてのファイルがコピーされた場合の *コピー ログ* のサンプル出力です。

```output
<CopyLog Summary="Summary">
  <Status>Succeeded</Status>
  <TotalFiles_Blobs>5521</TotalFiles_Blobs>
  <FilesErrored>0</FilesErrored>
</CopyLog>
``` 
    
次に示すのは、*コピー ログ* 内にエラーがあり、一部のファイルを Azure からコピーできなかった場合のサンプル出力です。

```output
<ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/wastorage.v140.3.0.2.nupkg">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444.hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444 (1).hcsml">
  <Category>UploadErrorCloudHttp</Category>
  <ErrorCode>400</ErrorCode>
  <ErrorMessage>UnsupportBlobType</ErrorMessage>
  <Type>File</Type>
</ErroredEntity><CopyLog Summary="Summary">
  <Status>Failed</Status>
  <TotalFiles_Blobs>4</TotalFiles_Blobs>
  <FilesErrored>3</FilesErrored>
</CopyLog>    
```

これらのファイルをエクスポートする場合には、次のオプションがあります。 

- ネットワーク経由でコピーできなかったファイルは転送できます。 
- ご自分のデータのサイズが使用可能なデバイスの容量よりも大きい場合は、一部がコピーされ、コピーされなかったすべてのファイルがこのログに一覧表示されます。 このログを入力 XML として使用して、新しい Data Box 注文を作成し、これらのファイルをコピーすることができます。

### <a name="verbose-log"></a>詳細ログ

*詳細ログ* には、Azure Storage アカウントから正常にエクスポートされたすべてのファイルが一覧表示されます。 このログには、ファイル サイズとチェックサムの計算も含まれています。

詳細ログの情報の書式は、次のとおりです。

`<file size = "file-size-in-bytes" crc64="cyclic-redundancy-check-string">\folder-path-on-data-box\name-of-file-copied.md</file>`

詳細ログの出力例を次に示します。 

```powershell
  <File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3.4" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/helloEndWithDot..txt" Size="11" crc64="7320094093915972193">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test..txt" Size="12" crc64="17906086011702236012">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test1" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/.......txt" Size="11" crc64="7320094093915972193">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/copylogb08fa3095564421bb550d775fff143ed====..txt" Size="53638" crc64="1147139997367113454">
  </File><File CloudFormat="BlockBlob" Path="validblobdata/testmaxChars-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-12345679" Size="1024" crc64="7573843669953104266">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file0" Size="0" crc64="0">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file1" Size="0" crc64="0">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000001" Size="4096" crc64="16969371397892565512">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000000" Size="4096" crc64="16969371397892565512">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed10.dat" Size="65536" crc64="10746682179555216785">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/LiveSiteReport_Oct.xlsx" Size="7028" crc64="6103506546789189963">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/NE_Oct_GeoReport.xlsx" Size="103197" crc64="13305485882546035852">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed1.dat" Size="65536" crc64="3140622834011462581">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-0" Size="1048576" crc64="16086591317856295272">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000001" Size="524288" crc64="8908547729214703832">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-0" Size="4194304" crc64="1339017920798612765">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000000" Size="524288" crc64="8908547729214703832">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-1" Size="8388608" crc64="3963298606737216548">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-1" Size="1048576" crc64="11061759121415905887">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/XLS-10MB.xls" Size="1199104" crc64="2218419493992437463">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-0" Size="8388608" crc64="1072783424245035917">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-1" Size="4194304" crc64="9991307204216370812">
  </File><File CloudFormat="BlockBlob" Path="export-ut-container/VL_Piracy_Negtive10_TPNameAndGCS.xlsx" Size="12398699" crc64="13526033021067702820">
  </File>
  ``````

詳細ログは、Azure ストレージ アカウントにもコピーされます。 既定では、ログは `copylog` という名前のコンテナーに書き込まれます。 ログは、次の命名規則を使用して保存されます。

`storage-account-name/databoxcopylog/ordername_device-serial-number_CopyLog_guid.xml`.

コピー ログ パスは、ポータルの **[概要]** ブレードにも表示されます。

<!-- add a screenshot-->

これらのログを使用すると、Azure からコピーされたファイルと、お使いのオンプレミスのサーバーにコピーされたデータとの一致を確認することができます。 

ご自分の詳細ログ ファイルは、次に使用できます。

- Data Box からコピーされたファイルの実際の名前と数の確認。
- ファイルの実際のサイズの確認。
- *crc64* が 0 以外の文字列であることの確認。 巡回冗長検査 (CRC) の計算は、Azure からのエクスポート時に行われます。 エクスポートにおける CRC と、Data Box からオンプレミス サーバーにデータがコピーされた後の CRC は、比較することができます。 CRC に不一致がある場合、該当するファイルが正しくコピーされていないことを意味します。


## <a name="get-chain-of-custody-logs-after-data-erasure"></a>データの消去後に生産物流管理ログを取得する

NIST SP 800-88 リビジョン 1 のガイドラインに従って Data Box ディスクからデータが消去された後、生産物流管理ログが使用可能になります。 これらのログには、生産物流管理監査ログと注文履歴が含まれます。 BOM またはマニフェスト ファイルの監査ログと一緒にコピーされます。

### <a name="chain-of-custody-audit-logs"></a>生産物流管理監査ログ

生産物流管理監査ログには、Azure データセンターの外部にある Data Box または Data Box Heavy の電源投入とそれの共有へのアクセスに関する情報が含まれます。 これらのログは、`storage-account/azuredatabox-chainofcustodylogs` にあります

Data Box からの監査ログのサンプルを次に示します。

```output
9/10/2018 8:23:01 PM : The operating system started at system time ‎2018‎-‎09‎-‎10T20:23:01.497758400Z.
9/10/2018 8:23:42 PM : An account was successfully logged on.
Subject:
    Security ID:        S-1-5-18
    Account Name:       WIN-DATABOXADMIN
    Account Domain: Workgroup
    Logon ID:       0x3E7
Logon Information:
    Logon Type:     3
    Restricted Admin Mode:  -
    Virtual Account:        No
    Elevated Token:     No
Impersonation Level:        Impersonation
New Logon:
    Security ID:        S-1-5-7
    Account Name:       ANONYMOUS LOGON
    Account Domain: NT AUTHORITY
    Logon ID:       0x775D5
    Linked Logon ID:    0x0
    Network Account Name:   -
    Network Account Domain: -
    Logon GUID:     {00000000-0000-0000-0000-000000000000}
Process Information:
    Process ID:     0x4
    Process Name:       
Network Information:
    Workstation Name:   -
    Source Network Address: -
    Source Port:        -
Detailed Authentication Information:
    Logon Process:      NfsSvr
    Authentication Package:MICROSOFT_AUTHENTICATION_PACKAGE_V1_0
    Transited Services: -
    Package Name (NTLM only):   -
    Key Length:     0
This event is generated when a logon session is created. It is generated on the computer that was accessed. 
The subject fields indicate the account on the local system which requested the logon. This is most commonly a service such as the Server service, or a local process such as Winlogon.exe or Services.exe. 
The logon type field indicates the kind of logon that occurred. The most common types are 2 (interactive) and 3 (network).
The New Logon fields indicate the account for whom the new logon was created, i.e. the account that was logged on.
The network fields indicate where a remote logon request originated. Workstation name is not always available and may be left blank in some cases.
The impersonation level field indicates the extent to which a process in the logon session can impersonate.
The authentication information fields provide detailed information about this specific logon request.
    - Logon GUID is a unique identifier that can be used to correlate this event with a KDC event.
    - Transited services indicate which intermediate services have participated in this logon request.
    - Package name indicates which sub-protocol was used among the NTLM protocols.
    - Key length indicates the length of the generated session key. This will be 0 if no session key was requested.
9/10/2018 8:25:58 PM : An account was successfully logged on.
```

## <a name="download-order-history"></a>注文履歴のダウンロード

注文履歴は Azure portal で利用できます。 注文が完了し、デバイスのクリーンアップ (ディスクからのデータ消去) が完了したら、デバイスの注文に移動し、次に **[注文の詳細]** に移動します。 **[注文履歴のダウンロード]** オプションが使用可能になっています。 詳細については、「[注文履歴のダウンロード](data-box-portal-admin.md#download-order-history)」を参照してください。

注文履歴をスクロールすると、以下の内容が表示されます。

- デバイスの運送業者の追跡情報。
- *SecureErase* アクティビティのイベント。 これらのイベントは、ディスク上のデータの消去に対応します。
- Data Box ログのリンク。 *監査ログ*、*コピー ログ*、および *BOM* ファイルのパスが表示されます。

次に、Azure portal からの注文履歴ログのサンプルを示します。

```
-------------------------------
Microsoft Data Box Order Report
-------------------------------
Name                                               : gus-poland                              
StartTime(UTC)                              : 9/19/2018 8:49:23 AM +00:00                       
DeviceType                                     : DataBox                                           
-------------------
Data Box Activities
-------------------
Time(UTC)                 | Activity                       | Status          | Description

9/19/2018 8:49:26 AM      | OrderCreated                   | Completed       |
10/2/2018 7:32:53 AM      | DevicePrepared                 | Completed       |
10/3/2018 1:36:43 PM      | ShippingToCustomer             | InProgress      | Shipment picked up. Local Time : 10/3/2018 1:36:43 PM at AMSTERDAM-NLD                                                                                
10/4/2018 8:23:30 PM      | ShippingToCustomer             | InProgress      | Processed at AMSTERDAM-NLD. Local Time : 10/4/2018 8:23:30 PM at AMSTERDAM-NLD                                                                        
10/4/2018 11:43:34 PM     | ShippingToCustomer             | InProgress      | Departed Facility in AMSTERDAM-NLD. Local Time : 10/4/2018 11:43:34 PM at AMSTERDAM-NLD
10/5/2018 8:13:49 AM      | ShippingToCustomer             | InProgress      | Arrived at Delivery Facility in BRIGHTON-GBR. Local Time : 10/5/2018 8:13:49 AM at LAMBETH-GBR                                                         
10/5/2018 9:13:24 AM      | ShippingToCustomer             | InProgress      | With delivery courier. Local Time : 10/5/2018 9:13:24 AM at BRIGHTON-GBR                                                                               
10/5/2018 12:03:04 PM     | ShippingToCustomer             | Completed       | Delivered - Signed for by. Local Time : 10/5/2018 12:03:04 PM at BRIGHTON-GBR                                                                          
1/25/2019 3:19:25 PM      | ShippingToDataCenter           | InProgress      | Shipment picked up. Local Time : 1/25/2019 3:19:25 PM at BRIGHTON-GBR                                                                                       
1/25/2019 8:03:55 PM      | ShippingToDataCenter           | InProgress      | Processed at BRIGHTON-GBR. Local Time : 1/25/2019 8:03:55 PM at LAMBETH-GBR                                                                            
1/25/2019 8:04:58 PM      | ShippingToDataCenter           | InProgress      | Departed Facility in BRIGHTON-GBR. Local Time : 1/25/2019 8:04:58 PM at BRIGHTON-GBR                                                                    
1/25/2019 9:06:09 PM      | ShippingToDataCenter           | InProgress      | Arrived at Sort Facility LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:06:09 PM at LONDON-HEATHROW-GBR                                                
1/25/2019 9:48:54 PM      | ShippingToDataCenter           | InProgress      | Processed at LONDON-HEATHROW-GBR. Local Time : 1/25/2019 9:48:54 PM at LONDON-HEATHROW-GBR                                                            
1/25/2019 10:30:20 PM     | ShippingToDataCenter           | InProgress      | Departed Facility in LONDON-HEATHROW-GBR. Local Time : 1/25/2019 10:30:20 PM at LONDON-HEATHROW-GBR
1/28/2019 7:11:35 AM      | ShippingToDataCenter           | InProgress      | Arrived at Delivery Facility in AMSTERDAM-NLD. Local Time : 1/28/2019 7:11:35 AM at AMSTERDAM-NLD                                                     
1/28/2019 9:07:57 AM      | ShippingToDataCenter           | InProgress      | With delivery courier. Local Time : 1/28/2019 9:07:57 AM at AMSTERDAM-NLD                                                                             
1/28/2019 1:35:56 PM      | ShippingToDataCenter           | InProgress      | Scheduled for delivery. Local Time : 1/28/2019 1:35:56 PM at AMSTERDAM-NLD                                                                            
1/28/2019 2:57:48 PM      | ShippingToDataCenter           | Completed       | Delivered - Signed for by. Local Time : 1/28/2019 2:57:48 PM at AMSTERDAM-NLD
1/29/2019 2:18:43 PM      | PhysicalVerification           | Completed       |
1/29/2019 3:49:50 PM      | DeviceBoot                     | Completed       | Appliance booted up successfully.
1/29/2019 3:49:51 PM      | AnomalyDetection               | Completed       | No anomaly detected.
2/12/2019 10:37:03 PM     | DataCopy                       | Resumed         |
2/13/2019 12:05:15 AM     | DataCopy                       | Resumed         |
2/15/2019 7:07:34 PM      | DataCopy                       | Completed       | Copy Completed.
2/15/2019 7:47:32 PM      | SecureErase                    | Started         |
2/15/2019 8:01:10 PM      | SecureErase                    | Completed       | Azure Data Box:<Device-serial-no> has been sanitized according to NIST 800-88 Rev 1.
------------------
Data Box Log Links
------------------
Account Name         : gusacct
Copy Logs Path       : databoxcopylog/gus-poland_<Device-serial-no>_CopyLog_<GUID>.xml
Audit Logs Path      : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
BOM Files Path       : azuredatabox-chainofcustodylogs\<GUID>\<Device-serial-no>
```

## <a name="next-steps"></a>次のステップ

- [Data Box および Data Box Heavy の問題のトラブルシューティング](data-box-troubleshoot.md)方法を確認します。
