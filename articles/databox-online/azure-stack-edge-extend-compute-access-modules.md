---
title: Azure Stack Edge Pro でコンピューティング ネットワークを管理してモジュールにアクセスする | Microsoft Docs
description: Azure Stack Edge Pro でコンピューティング ネットワークを拡張して外部 IP 経由でモジュールにアクセスする方法を説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 19c92deb58ac51aa882e7123b9a90aa3eae627d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90894120"
---
# <a name="enable-compute-network-on-your-azure-stack-edge-pro"></a>Azure Stack Edge Pro でコンピューティング ネットワークを有効にする

この記事では、Azure Stack Edge Pro で実行されているモジュールから、デバイスで有効になっているコンピューティング ネットワークにアクセスする方法について説明します。

ネットワークを構成するには、次の手順を実行します。

- コンピューティング用の Azure Stack Edge Pro デバイスでネットワーク インターフェイスを有効にします
- Azure Stack Edge Pro でコンピューティング ネットワークにアクセスするためのモジュールを追加します
- 有効になっているネットワーク インターフェイスにモジュールがアクセスできることを確認します

このチュートリアルでは、Web サーバー アプリのモジュールを使用したシナリオについて説明します。

## <a name="prerequisites"></a>前提条件

開始する前に、次の条件を満たしている必要があります。

- デバイスのセットアップが完了した Azure Stack Edge Pro デバイスがある。
- **コンピューティングの構成** が完了している。コンピューティングの構成は、お使いのデバイスで、[Azure Stack Edge Pro を使用してデータを変換することに関するチュートリアル](azure-stack-edge-deploy-configure-compute-advanced.md#configure-compute)の手順に従って行います。 お使いのデバイスには、関連付けられた IoT Hub リソース、IoT デバイス、および IoT Edge デバイスが必要です。

## <a name="enable-network-interface-for-compute"></a>コンピューティング用ネットワーク インターフェイスを有効にする

お使いのデバイスで実行されているモジュールに外部ネットワーク経由でアクセスするには、そのデバイスのネットワーク インターフェイスに IP アドレスを割り当てる必要があります。 ローカル Web UI から、これらのコンピューティング設定を管理できます。

コンピューティング設定を構成するには、ローカル Web UI で次の手順を実行します。

1. ローカル Web UI で、 **[構成] > [コンピューティング設定]** の順に移動します。  

2. デバイスで実行するコンピューティング モジュールへの接続に使用するネットワーク インターフェイスを **有効** にします。

    - 静的 IP アドレスを使用する場合は、ネットワーク インターフェイスの IP アドレスを入力します。
    - DHCP を使用している場合、IP アドレスは自動的に割り当てられます。 この例では、DHCP を使用します。

    ![コンピューティング設定 1 を有効にする](media/azure-stack-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. **[適用]** を選択して、設定を適用します。 DHCP を使用する場合は、ネットワーク インターフェイスに割り当てられている IP アドレスを書き留めておきます。

    ![コンピューティング設定を有効にする](media/azure-stack-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>Web サーバー アプリ モジュールを追加する

Azure Stack Edge Pro デバイスに Web サーバー アプリ モジュールを追加するには、次の手順を行います。

1. Azure Stack Edge Pro デバイスに関連付けられている IoT Hub リソースに移動し、 **[IoT Edge デバイス]** を選択します。
2. お使いの Azure Stack Edge Pro デバイスに関連付けられている IoT Edge デバイスを選択します。 **[デバイスの詳細]** で、 **[モジュールの設定]** を選択します。 **[モジュールの追加]** で、 **[+ 追加]** 、 **[IoT Edge モジュール]** の順に選択します。
3. **[IoT Edge のカスタム モジュール]** ブレードで、以下の手順を実行します。

    1. デプロイする Web サーバー アプリ モジュールの **名前** を指定します。
    2. お使いのモジュール イメージの **イメージ URI** を指定します。 指定した名前とタグに一致するモジュールが取得されます。 この場合は、`nginx:stable` によって、パブリック [Docker リポジトリ](https://hub.docker.com/_/nginx/)から、(stable としてタグ付けされた) 安定した nginx イメージがプルされます。
    3. **[コンテナーの作成オプション]** ボックスで、次のサンプル コードを貼り付けます。  

        ```
        {
            "HostConfig": {
                "PortBindings": {
                    "80/tcp": [
                        {
                            "HostPort": "8080"
                        }
                    ]
                }
            }
        }
        ```

        この構成を使用すると、TCP ポート 8080 で、コンピューティング ネットワーク IP over *http* を使用してモジュールにアクセスできます (既定の Web サーバー ポートは 80)。

        ![[IoT Edge カスタム モジュール] ブレードでポート情報を指定する](media/azure-stack-edge-extend-compute-access-modules/module-information.png)

    4. **[保存]** を選択します。

## <a name="verify-module-access"></a>モジュール アクセスを確認する

1. モジュールが正常にデプロイされ、実行されていることを確認します。 **[デバイスの詳細]** ページの **[モジュール]** タブで、モージュールの状態が **実行中** になっているはずです。  
2. Web サーバー アプリ モジュールに接続します。 ブラウザー ウィンドウを開いて、次を入力します。

    `http://<compute-network-IP-address>:8080`

    Web サーバー アプリが実行中であることがわかります。

    ![指定したポート経由でモジュールに接続されていることを確認する](media/azure-stack-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>次のステップ

- [Azure portal を使用してユーザーを管理する](azure-stack-edge-manage-users.md)方法について学習します。
