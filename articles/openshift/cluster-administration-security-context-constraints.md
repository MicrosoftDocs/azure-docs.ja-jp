---
title: Azure Red Hat OpenShift でセキュリティ コンテンツ制約を管理する | Microsoft Docs
description: Azure Red Hat OpenShift クラスター管理者に対するセキュリティ コンテンツ制約
services: container-service
author: troy0820
ms.author: b-trconn
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 977504c1faec9bd8134646a8cbe31f9eea665edd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100636205"
---
# <a name="manage-security-context-constraints-in-azure-red-hat-openshift"></a>Azure Red Hat OpenShift でセキュリティ コンテンツ制約を管理する

> [!IMPORTANT]
> Azure Red Hat OpenShift 3.11 は、2022 年 6 月 30 日に廃止されます。 新しい Azure Red Hat OpenShift 3.11 クラスターの作成のサポートは、2020 年 11 月 30 日まで継続されます。 廃止された後、残っている Azure Red Hat OpenShift 3.11 クラスターは、セキュリティの脆弱性を防ぐためにシャットダウンされます。
> 
> このガイドに従って、[Azure Red Hat OpenShift 4 クラスターを作成](tutorial-create-cluster.md)します。
> ご質問がある場合は、[お問い合わせください](mailto:arofeedback@microsoft.com)。

セキュリティ コンテキスト制約 (SCC) を利用することで、クラスター管理者はポッドのアクセス許可を制御できます。 この API タイプの詳細については、[SCC のアーキテクチャ ドキュメント](https://docs.openshift.com/container-platform/3.11/architecture/additional_concepts/authorization.html)を参照してください。 インスタンス内の SCC は、CLI を使用して通常の API オブジェクトとして管理できます。

## <a name="list-security-context-constraints"></a>セキュリティ コンテキスト制約の一覧表示

SCC の現在の一覧を取得するには、次のコマンドを使用します。 

```bash
$ oc get scc

NAME               PRIV      CAPS      SELINUX     RUNASUSER          FSGROUP     SUPGROUP    PRIORITY   READONLYROOTFS   VOLUMES
anyuid             false     []        MustRunAs   RunAsAny           RunAsAny    RunAsAny    10         false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
hostaccess         false     []        MustRunAs   MustRunAsRange     MustRunAs   RunAsAny    <none>     false            [configMap downwardAPI emptyDir hostPath persistentVolumeClaim secret]
hostmount-anyuid   false     []        MustRunAs   RunAsAny           RunAsAny    RunAsAny    <none>     false            [configMap downwardAPI emptyDir hostPath nfs persistentVolumeClaim secret]
hostnetwork        false     []        MustRunAs   MustRunAsRange     MustRunAs   MustRunAs   <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
nonroot            false     []        MustRunAs   MustRunAsNonRoot   RunAsAny    RunAsAny    <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
privileged         true      [*]       RunAsAny    RunAsAny           RunAsAny    RunAsAny    <none>     false            [*]
restricted         false     []        MustRunAs   MustRunAsRange     MustRunAs   RunAsAny    <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
```

## <a name="examine-an-object-for-security-context-constraints"></a>セキュリティ コンテキスト制約のオブジェクトを調べる

特定の SCC を調べるには、`oc get`、`oc describe`、または `oc edit` を使用します。  たとえば、**制約付き** SCC を調べるには、次のコマンドを使用します。
```bash
$ oc describe scc restricted
Name:                    restricted
Priority:                <none>
Access:
  Users:                <none>
  Groups:                system:authenticated
Settings:
  Allow Privileged:            false
  Default Add Capabilities:        <none>
  Required Drop Capabilities:        KILL,MKNOD,SYS_CHROOT,SETUID,SETGID
  Allowed Capabilities:            <none>
  Allowed Seccomp Profiles:        <none>
  Allowed Volume Types:            configMap,downwardAPI,emptyDir,persistentVolumeClaim,projected,secret
  Allow Host Network:            false
  Allow Host Ports:            false
  Allow Host PID:            false
  Allow Host IPC:            false
  Read Only Root Filesystem:        false
  Run As User Strategy: MustRunAsRange
    UID:                <none>
    UID Range Min:            <none>
    UID Range Max:            <none>
  SELinux Context Strategy: MustRunAs
    User:                <none>
    Role:                <none>
    Type:                <none>
    Level:                <none>
  FSGroup Strategy: MustRunAs
    Ranges:                <none>
  Supplemental Groups Strategy: RunAsAny
    Ranges:                <none>
```
## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift クラスターを作成する](tutorial-create-cluster.md) 
