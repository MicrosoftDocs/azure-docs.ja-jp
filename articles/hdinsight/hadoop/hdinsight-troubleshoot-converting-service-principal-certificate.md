---
title: サービス プリンシパル証明書の内容を base-64 に変換する - Azure HDInsight
description: Azure HDInsight でサービス プリンシパル証明書の内容を base-64 でエンコードされた文字列形式に変換する
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 0f9158164ec598682841b217b9954145f8540c6c
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087426"
---
# <a name="converting-service-principal-certificate-contents-to-base-64-encoded-string-format-in-hdinsight"></a>HDInsight でサービス プリンシパル証明書の内容を base-64 でエンコードされた文字列形式に変換する

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

base-64 以外の文字、3 個以上のパディング文字、またはパディング文字の間の空白以外の文字が含まれているため、入力が有効な base-64 文字列ではないことを示すエラー メッセージが表示されます。

## <a name="cause"></a>原因

PowerShell または Azure テンプレートのデプロイを使用して、プライマリまたは追加のストレージとして Data Lake を使用するクラスターを作成する場合、Data Lake ストレージ アカウントにアクセスするために提供されるサービス プリンシパルの証明書の内容は、base-64 形式になっています。 pfx 証明書の内容を base-64 でエンコードされた文字列に不適切に変換すると、このエラーが発生する可能性があります。

## <a name="resolution"></a>解決策

pfx 形式のサービス プリンシパル証明書を用意したら (サンプルのサービス プリンシパル作成手順については[こちら](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage)を参照してください)、次の PowerShell コマンドまたは C# スニペットを使用して、証明書の内容を base-64 形式に変換します。

```powershell
$servicePrincipalCertificateBase64 = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(path-to-servicePrincipalCertificatePfxFile))
```

```csharp
using System;
using System.IO;

namespace ConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            var certContents = File.ReadAllBytes(@"<path to pfx file>");
            string certificateData = Convert.ToBase64String(certContents);
            System.Diagnostics.Debug.WriteLine(certificateData);
        }
    }
}
```

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上させる Microsoft Azure の公式アカウント) に問い合わせる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)」をご覧ください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
