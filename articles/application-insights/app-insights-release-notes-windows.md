---
title: Windows 適用的 Application Insights 版本資訊
description: Windows Store SDK 的最新更新。
services: application-insights
documentationcenter: ''
author: alancameronwills
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/12/2016
ms.author: joshweb

---
# 適用於 Windows Phone 和市集的 Application Insights 版本資訊
Application Insights SDK 傳送有關您作用中的應用程式遙測到 [Application Insights](https://azure.microsoft.com/services/application-insights/)，您可以在此分析它的使用情況與效能。

## 1\.1.1 版
### Windows SDK
* 修正在使用 Windows Phone 的 Silverlight SDK 時，於損毀期間發生停止回應。發生此變更之後，呼叫 WindowsAppInitialier.InitializeAsync(...) 2 秒之後所發生的任何損毀，皆會保存到磁碟，且會在下一次啟動應用程式時傳送。如果在呼叫後的前 2 秒內發生當機，則將會忽略。
* 將 NuGet 的相依性設為特定版本的核心與 Microsoft.ApplicationInsights.PersistenceChannel (v1.2.3)。

### 核心 SDK
* 核心管理於 github 中。您可於 [github](http://github.com/Microsoft/ApplicationInsights-dotnet/releases) 中找到核心 SDK 未來的版本資訊

## 1\.1 版
### 核心 SDK
* SDK 現已引進新的遙測類型 ```DependencyTelemetry```，其中包含從應用程式相依性呼叫的相關資訊
* 新方法 ```TelemetryClient.TrackDependency``` 允許從應用程式傳送相依性呼叫的相關資訊

## 1\.0.0 版
### Windows 應用程式 SDK
* Windows 應用程式的新初始化。`InitializeAsync()` 方法的新 `WindowsAppInitializer` 類別允許啟動 SDK 集合的初始化。這項變更透過先前的 ApplicationInsights.config 技術，允許更精確的控制和重要的應用程式初始化效能改進。
* 不再自動設定 DeveloperMode。若要變更 DeveloperMode 行為，您必須在程式碼中指定。
* NuGet 封裝不會再插入 ApplicationInsights.config。手動加入 NuGet 封裝時，建議使用新的 WindowsAppInitializer。
* ApplicationInsights.config 只會讀取 `<InstrumentationKey>`，略過所有其他設定，而採用 WindowsAppInitializer 設定。
* SDK 將會自動收集市集市場。
* 許多錯誤修正、穩定性改良和效能增強功能。

### 核心 SDK
* ApplicationInsights.config 檔案不再是必要。並且不會由 NuGet 封裝加入。可以在程式碼中指定完整的組態。
* NuGet 封裝將不再加入目標檔案到您的方案。這會移除在偵錯組建期間 DeveloperMode 的自動設定。DeveloperMode 應該在程式碼中手動設定。

## 0\.17 版
### Windows 應用程式 SDK
* Windows 應用程式 SDK 現在支援針對 Windows 10 技術預覽版且具備 VS 2015 RC 建立的通用 Windows 應用程式。

### 核心 SDK
* TelemetryClient 會預設為使用 InMemoryChannel 初始化。
* 已加入新的 API，`TelemetryClient.Flush()`。這個排清方法會觸發立即封鎖記錄到該用戶端的所有遙測上傳。這可讓您在程序關閉之前手動觸發上傳。
* NuGet 封裝加入了 .Net 4.5 目標。此目標沒有外部相依性 (移除了 BCL 和 EventSource 相依性)。

## 0\.16 版
2015-04-28 預覽

* SDK 現在支援 DNX 目標平台，以監視 [.NET 核心架構](http://www.dotnetfoundation.org/NETCore5) 應用程式。
* ```TelemetryClient``` 的執行個體不再快取檢測金鑰。現在如果未明確在 ```TelemetryClient``` 中設定檢測金鑰，則 ```InstrumentationKey``` 會傳回 Null。它可以在您於已收集一些遙測之後設定 ```TelemetryConfiguration.Active.InstrumentationKey``` 時修正問題，像是相依性收集器的遙測模組、Web 要求資料收集和效能計數器收集器會使用新的檢測金鑰。

## 0\.15 版
* 新屬性 ```Operation.SyntheticSource``` 現在可用於 ```TelemetryContext```。現在您可以將您的遙測項目標示為「非真實使用者流量」，並指定如何產生此流量。做為範例，藉由設定這個屬性您可以區分來自您的測試自動化的流量和來自負載測試的流量。
* 通道邏輯已移至稱為 Microsoft.ApplicationInsights.PersistenceChannel 的個別 NuGet。預設通道現在稱為 InMemoryChannel
* 新方法 ```TelemetryClient.Flush``` 讓使用者能夠以同步方式從緩衝區排清遙測項目

## 0\.13 版
沒有可用於較舊版本的版本資訊。

<!----HONumber=AcomDC_0907_2016-->