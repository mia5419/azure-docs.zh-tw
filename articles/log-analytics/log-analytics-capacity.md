---
title: "Log Analytics 中的容量管理解決方案 | Microsoft Docs"
description: "您可以使用 Log Analytics 中的容量規劃方案，協助您瞭解 System Center Virtual Machine Manager 所管理的 Hyper-V 伺服器的容量"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 51617a6f-ffdd-4ed2-8b74-1257149ce3d4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 57e7fbdaa393e078b62a6d6a0b181b67d532523d
ms.openlocfilehash: c34cda0da164c711c8effc78d2af38ad8df581aa


---
# <a name="capacity-management-solution-in-log-analytics"></a>Log Analytics 中的容量管理方案
您可以使用 Log Analytics 中的容量管理解決方案，協助您瞭解 Hyper-V 伺服器的容量。 此方案需要 System Center Operations Manager 和 System Center Virtual Machine Manager。 如果您直接使用連接的代理程式，則容量規劃解決方案未作用。 此方案會讀取受監視伺服器上的效能計數器，並將使用量資料傳送至雲端中的 OMS 服務進行處理。 會將邏輯套用至使用量資料，且雲端服務會記錄資料。 經過一段時間，會根據目前的耗用量識別使用模式和規劃容量。

例如，當個別伺服器會需要額外的處理器核心或額外的記憶體時，可能會識別預測。 在此範例中，預測可能表示在 30 天內，伺服器將需要額外的記憶體。 這個預測可協助您規劃在伺服器的下一段維護期間升級記憶體。

> [!NOTE]
> 容量管理方案無法加入至工作區。 已安裝容量管理方案的客戶可以繼續使用該解決方案。  
>
>

取代容量和效能解決方案目前為私人預覽版本。 這個取代解決方案是要解決原始容量管理解決方案的下列客戶回報的挑戰：

* 使用 Virtual Machine Manager 和 Operations Manager 的需求
* 無法根據群組來自訂/篩選
* 每小時的資料彙總不夠頻繁
* 沒有 VM 層級深入資訊
* 資料可靠性

新容量解決方案的優點︰

* 支援更精細的資料集合及更高的可靠性和正確性
* 支援 Hyper-V 而不需要 VMM
* VM 層級使用率的深入資訊

新的解決方案目前需要 Hyper-V Server 2012 或更新版本。 這個解決方案深入了解 Hyper-V 環境，並查看在這些 Hyper-V 伺服器上執行的主機和 VM 的整體使用率 (CPU、記憶體和磁碟)。 會跨您的所有主機和在其上執行的 VM 來收集 CPU、記憶體和磁碟的度量。

這個頁面上的其餘文件指的是舊的容量管理解決方案。 新解決方案目前為公開預覽版本時，將會更新這份文件。

## <a name="installing-and-configuring-the-solution"></a>安裝和設定方案
請使用下列資訊來安裝和設定方案。

* 容量管理方案需要 Operations Manager。
* 容量管理方案需要 Virtual Machine Manager。
* Operations Manager 與 Virtual Machine Manager (VMM) 之間的連線是必要的。 如需連接系統的詳細資訊，請參閱[如何連接 VMM 與 Operations Manager](http://technet.microsoft.com/library/hh882396.aspx)。
* Operations Manager 必須連接到 Log Analytics。
* 使用 [從方案庫加入 Log Analytics 方案](log-analytics-add-solutions.md)所述的程序，將容量管理方案加入您的 OMS 工作區。  不需要進一步的組態。

## <a name="capacity-management-data-collection-details"></a>「容量管理」資料收集詳細資訊
容量管理會使用您已啟用的代理程式，來收集效能資料、中繼資料及狀態資料。

下表顯示容量管理的資料收集方法及如何收集資料的其他詳細資料。

| 平台 | 直接代理程式 | Operations Manager 代理程式 | Azure 儲存體 | 是否需要 Operations Manager？ | 透過管理群組傳送的 Operations Manager 代理程式資料 | 收集頻率 |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |![否](./media/log-analytics-capacity/oms-bullet-red.png) |![是](./media/log-analytics-capacity/oms-bullet-green.png) |![否](./media/log-analytics-capacity/oms-bullet-red.png) |![是](./media/log-analytics-capacity/oms-bullet-green.png) |![是](./media/log-analytics-capacity/oms-bullet-green.png) |每小時 |

下表顯示由容量管理所收集的資料類型範例︰

| **資料類型** | **欄位** |
| --- | --- |
| 中繼資料 |BaseManagedEntityId、ObjectStatus、OrganizationalUnit、ActiveDirectoryObjectSid、PhysicalProcessors、NetworkName、IPAddress、ForestDNSName、NetbiosComputerName、VirtualMachineName、LastInventoryDate、HostServerNameIsVirtualMachine、IP 位址、NetbiosDomainName、LogicalProcessors、DNSName、DisplayName、DomainDnsName、ActiveDirectorySite、PrincipalName、OffsetInMinuteFromGreenwichTime |
| 效能 |ObjectName、CounterName、PerfmonInstanceName、PerformanceDataId、PerformanceSourceInternalID、SampleValue、TimeSampled、TimeAdded |
| 狀況 |StateChangeEventId、StateId、NewHealthState、OldHealthState、Context、TimeGenerated、TimeAdded、StateId2、BaseManagedEntityId、MonitorId、HealthState、LastModified、LastGreenAlertGenerated、DatabaseTimeModified |

## <a name="capacity-management-page"></a>容量管理頁面
安裝容量規劃方案後，您可以在 OMS 中使用 [概觀] 頁面上的 [容量規劃] 圖格，檢視受監視伺服器的容量。

![image of Capacity Planning tile](./media/log-analytics-capacity/oms-capacity01.png)

該磚會開啟 [ **容量管理** ] 儀表板，您可以在其中檢視伺服器容量的摘要。 頁面會顯示以下可點擊的磚：

* *虛擬機器計數*：顯示虛擬機器容量的剩餘天數
* *計算*：顯示處理器核心和可用記憶體
* *儲存體*：顯示使用的磁碟空間和平均磁碟延遲
* *搜尋*：可用來在 OMS 系統中搜尋任何資料的資料總管

![image of Capacity Management dashboard](./media/log-analytics-capacity/oms-capacity02.png)

### <a name="to-view-a-capacity-page"></a>檢視產能頁面
* 在 [概觀] 頁面上按一下 [容量管理]，然後按一下 [計算] 或 [儲存體]。

## <a name="compute-page"></a>計算頁面
您可以使用 Microsoft Azure OMS 中的 [計算] 儀表板，檢視有關使用量、預測容量天數及基礎結構相關效率等容量資訊。 您可以使用 [使用率] 區域來檢視虛擬機器主機中的 CPU 核心和記憶體使用率。 您可以使用預測工具來預估指定日期範圍內的可用產能。 您可以使用 [效率] 區域來查看虛擬機器主機的效率。 按一下連結項目可以檢視它們的相關詳細資料。

您可以產生以下類別的 Excel 活頁簿：

* 核心使用率最高的主機
* 記憶體使用率最高的主機
* 虛擬機器效率最差的主機
* 使用率最高的主機
* 使用率最低的主機

![image of Capacity Management Compute page](./media/log-analytics-capacity/oms-capacity03.png)

以下區域會顯示在 [ **計算** ] 儀表板中：

**使用率**：檢視虛擬機器主機的 CPU 核心和記憶體使用率。

* *使用的核心*：所有主機的總和 (使用的 CPU % 乘以主機上的實體核心數目)。
* *可用的核心*：實體核心總數減去使用核心數目。
* *可用核心百分比*：可用實體核心數目除以實體核心總數。
* *每個 VM 的虛擬核心*：系統中的虛擬核心總數除以系統中的虛擬機器總數。
* *虛擬核心和實體核心的比率*：實體核心總數與系統中虛擬機器使用之實體核心數目的比率。
* *可用虛擬核心的數目*：虛擬核心與實體核心的比率乘以可用實體核心數目。
* *使用的記憶體*：所有主機使用的記憶體總和。
* *可用的記憶體*：實體記憶體總數減去使用記憶體數目。
* *可用記憶體的百分比*：可用實體記憶體除以實體記憶體總數。
* *每個 VM 的虛擬記憶體*：系統中的虛擬記憶體總數除以系統中的虛擬機器總數。
* *虛擬記憶體和實體記憶體的比率*：系統中的虛擬記憶體總數除以系統中的實體記憶體總數。
* *可用的虛擬記憶體*：虛擬記憶體與實體記憶體的比率乘以可用實體記憶體數目。

**預測工具**

透過預測工具，您可以檢視資源使用率的歷史趨勢。 這包括虛擬機器、記憶體、核心和存放裝置的使用率趨勢。 預測功能使用預測演算法來協助您了解每項資源耗盡的時間。 這有助於計算出適當的產能規劃，讓您知道何時該添購更多產能 (如記憶體、核心或存放裝置)。

**效率**

* *閒置 VM*：在指定期間內，CPU 使用率低於 10% 且記憶體使用率低於 10%。
* *使用量過高的 VM*：在指定期間內，CPU 使用率高於 90% 且記憶體使用率高於 90%。
* *閒置主機*：在指定期間內，CPU 使用率低於 10% 且記憶體使用率低於 10%。
* *使用量過高的主機*：在指定期間內，CPU 使用率高於 90% 且記憶體使用率高於 90%。

### <a name="to-work-with-items-on-the-compute-page"></a>操作計算頁面中的項目
1. 在 [使用率] 區域的 [計算] 儀表板中，您可以檢視有關 CPU 核心和使用中記憶體的容量資訊。
2. 按一下項目可在 [ **搜尋** ] 頁面中予以開啟，以及檢視相關的詳細資訊。
3. 在 [ **預測** ] 工具中，移動日期滑桿可顯示基礎結構在選定日期將使用的容量預測。
4. 在 [ **效率** ] 區域中，您可以檢視虛擬機器和虛擬機器主機的容量效率資訊。

## <a name="direct-attached-storage-page"></a>直接連結存放裝置頁面
在 OMS 中，您可以使用 [直接連結存放裝置] 儀表板，檢視有關儲存體使用量、磁碟效能及磁碟容量的預測天數等容量資訊。 您可以使用 [使用率] 區域來檢視虛擬機器主機中的磁碟空間使用率。 您可以使用 [磁碟效能] 區域來檢視虛擬機器主機中的磁碟輸送量和延遲。 您也可以使用預測工具來預估指定日期範圍內的可用產能。 按一下連結項目可以檢視它們的相關詳細資料。

您可以根據上述產能資訊產生以下類別的 Excel 活頁簿：

* 磁碟空間使用率最高的主機
* 平均延遲最高的主機

以下區域會顯示在 [ **儲存體** ] 頁面中：

* *使用率*：檢視虛擬機器主機中的磁碟空間使用率。
* *總磁碟空間*：所有主機的總和 (邏輯磁碟空間)
* *使用的磁碟空間*：所有主機的總和 (使用的邏輯磁碟空間)
* *可用的磁碟空間*：磁碟空間總數減去使用磁碟空間數目
* *使用的磁碟百分比*：使用磁碟空間數目除以磁碟空間總數
* *可用的磁碟百分比*：可用磁碟空間數目除以磁碟空間總數

![image of Capacity Management Direct Attached Storage page](./media/log-analytics-capacity/oms-capacity04.png)

**磁碟效能**

透過 OMS，您可以檢視磁碟空間使用率的歷史趨勢。 預測功能會使用演算法來預測未來使用率。 針對空間使用率，預測功能可讓您預測磁碟空間何時會耗盡。 這項預測有助於您規劃適當的儲存體，以及得知何時該添購更多儲存體。

**預測工具**

透過預測工具，您可以檢視磁碟空間使用率的歷史趨勢。 預測功能也能讓您預測磁碟空間何時耗盡。 這項預測有助於您規劃適當的容量，以及得知何時該添購更多儲存體容量。

### <a name="to-work-with-items-on-the-direct-attached-storage-page"></a>操作直接連結存放裝置頁面中的項目
1. 在 [使用率] 區域的 [直接連結存放裝置] 儀表板中，您可以檢視磁碟使用率資訊。
2. 按一下連結項目可在 [ **搜尋** ] 頁面中予以開啟，以及檢視相關的詳細資訊。
3. 在 [ **磁碟效能** ] 區域中，您可以檢視磁碟輸送量和延遲資訊。
4. 在 [ **預測工具**] 中，移動日期滑桿可顯示基礎結構在選定日期將使用的容量預測。

## <a name="next-steps"></a>後續步驟
* 使用 [Log Analytics 中的記錄檔搜尋](log-analytics-log-searches.md) ，檢視詳細的容量管理資料。



<!--HONumber=Nov16_HO3-->


