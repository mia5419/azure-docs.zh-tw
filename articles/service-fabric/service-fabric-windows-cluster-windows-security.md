---
title: "使用 Windows 安全性保護在 Windows 上執行的叢集 | Microsoft Docs"
description: "了解如何使用 Windows 安全性在 Windows 上執行的獨立叢集上設定節點對節點和用戶端對節點安全性。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: ce3bf686-ffc4-452f-b15a-3c812aa9e672
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/09/2016
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: 08024aedb0889c91909f12aa1e38c91b11d3090f
ms.openlocfilehash: 142b76f053adc273e2e071f169f8f647fbd1c241


---
# <a name="secure-a-standalone-cluster-on-windows-using-windows-security"></a>使用 Windows 安全性保護 Windows 上的獨立叢集
為了防止未經授權的 Service Fabric 叢集存取，您必須保護其安全性，尤其是當其中有執行中的生產工作負載時。 本文說明如何在 ClusterConfig.JSON  檔案中使用 Windows 安全性設定節點對節點和用戶端對節點安全性，並對應至 [建立在 Windows 上執行的獨立叢集](service-fabric-cluster-creation-for-windows-server.md)的設定安全性步驟。 如需有關 Service Fabric 如何使用 Windows 安全性的詳細資訊，請參閱 [叢集安全性案例](service-fabric-cluster-security.md)。

> [!NOTE]
> 您應該仔細考慮節點對節點安全性的安全性選取項目，因為沒有從一個安全性選擇到另一個安全性選擇的叢集升級。 變更安全性選取項目時需要完整的叢集重建。
> 
> 

## <a name="configure-windows-security"></a>設定 Windows 安全性
隨著 [Microsoft.Azure.ServiceFabric.WindowsServer.<version>.zip](http://go.microsoft.com/fwlink/?LinkId=730690) 獨立叢集封裝下載的範例 *ClusterConfig.Windows.JSON* 組態檔包含可供設定 Windows 安全性的範本。  Windows 安全性於 **Properties** 區段中設定︰

```
"security": {
            "ClusterCredentialType": "Windows",
            "ServerCredentialType": "Windows",
            "WindowsIdentities": {
                "ClusterIdentity" : "[domain\machinegroup]",
                "ClientIdentities": [{
                    "Identity": "[domain\username]",
                    "IsAdmin": true
                }]
            }
        }
```

| **組態設定** | **說明** |
| --- | --- |
| ClusterCredentialType |將 **ClusterCredentialType** 參數設定為 *Windows*可啟用 Windows 安全性。 |
| ServerCredentialType |將 **ServerCredentialType** 參數設定為 *Windows*可針對用戶端啟用 Windows 安全性。 這表示叢集的用戶端和叢集本身都在 Active Directory 網域內執行。 |
| WindowsIdentities |包含叢集和用戶端身分識別。 |
| ClusterIdentity |設定節點對節點安全性。 電腦群組名稱。 |
| ClientIdentities |設定用戶端對節點安全性。 用戶端使用者帳戶的陣列。 |
| 身分識別 |用戶端身分識別，即網域使用者。 |
| IsAdmin |True 表示網域使用者具有系統管理員用戶端存取，False 表示具有使用者用戶端存取。 |

[ClusterIdentity](service-fabric-cluster-security.md#node-to-node-security) 可設定 **ClusterIdentity**的設定安全性步驟。 為了建置節點之間的信任關係，它們必須注意彼此。 可以藉由建立其中包含叢集中所有節點的網域群組來達到此目的。 此群組名稱應指定於 **ClusterIdentity**。 如需詳細資訊，請參閱[在 Active Directory 中建立群組](https://msdn.microsoft.com/en-us/library/aa545347(v=cs.70).aspx)。

[ClientIdentities](service-fabric-cluster-security.md#client-to-node-security) 可設定 **ClientIdentities**的設定安全性步驟。 若要建立用戶端與叢集之間的信任，您必須設定叢集才能知道用戶端可以信任的身分識別。 有兩種不同的方式可達成此目的︰指定可以連線的網域群組使用者，或指定可以連線的網域節點使用者。 Service Fabric 針對連線到 Service Fabric 叢集的用戶端，支援兩種不同的存取控制類型：系統管理員和使用者。 存取控制可讓叢集系統管理員針對不同的使用者群組限制特定類型的叢集作業的存取權，讓叢集更加安全。  系統管理員可以完整存取管理功能 (包括讀取/寫入功能)。 使用者預設只具有管理功能的讀取存取權 (例如查詢功能)，以及解析應用程式和服務的能力。

下列範例 **security** 區段可設定 Windows 安全性，並指定電腦群組 *ServiceFabric\\ClusterNodes* 中的電腦隸屬於叢集，而 *CONTOSO\usera* 具有系統管理員用戶端存取權︰

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "ServiceFabric\\ClusterNodes",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
        "IsAdmin": true
        }]
    }
},
```

## <a name="next-steps"></a>後續步驟
在 *ClusterConfig.JSON* 檔案中設定 Windows 安全性之後，繼續進行 [建立在 Windows 上執行的獨立叢集](service-fabric-cluster-creation-for-windows-server.md)中的叢集建立程序。

如需有關節點對節點安全性、用戶端對節點安全性和角色型存取控制的詳細資訊，請參閱 [叢集安全性案例](service-fabric-cluster-security.md)。

如需使用 PowerShell 或 FabricClient 連接的範例，請參閱 [連線到安全的叢集](service-fabric-connect-to-secure-cluster.md) 。




<!--HONumber=Nov16_HO4-->


