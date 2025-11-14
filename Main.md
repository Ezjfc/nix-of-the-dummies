# Nix 「腦殘」（寫的）筆記
Nix of the Dummies, by the Dummies, for the Dummies.
<a href="https://github.com/Ezjfc/nix-of-the-dummies">Nix of the Dummies</a> © 2025 by <a href="https://github.com/Ezjfc">Endermanbugzjfc</a> is licensed under <a href="https://creativecommons.org/licenses/by/4.0/">CC BY 4.0</a><img src="https://mirrors.creativecommons.org/presskit/icons/cc.svg" alt="" style="max-width: 1em;max-height:1em;margin-left: .2em;"><img src="https://mirrors.creativecommons.org/presskit/icons/by.svg" alt="" style="max-width: 1em;max-height:1em;margin-left: .2em;">
<a href="https://github.com/Ezjfc/nix-of-the-dummies">Nix 「腦殘」（寫的）筆記</a> © 2025 <a href="https://github.com/Ezjfc">Endermanbugzjfc</a> 作， 使用 <a href="https://creativecommons.org/licenses/by/4.0/deed.zh-hant">CC BY 4.0</a><img src="https://mirrors.creativecommons.org/presskit/icons/cc.svg" alt="" style="max-width: 1em;max-height:1em;margin-left: .2em;"><img src="https://mirrors.creativecommons.org/presskit/icons/by.svg" alt="" style="max-width: 1em;max-height:1em;margin-left: .2em;"> 共享創意授權

2025-11-14

此筆記的第一語言為繁體中文（香港）， 部分用詞差異已在註腳標記，若仍有困惑，敬請見諒；
從筆記若有錯誤之處，請不吝賜正。

閱讀順序建議：
- **非 NixOS 用戶但希望瞭解 Nix 生態的中階程序員（或其他人士）**
	1. [[Main#序章： Nix 包裹管理器]]
	2. [[Main#第壹章： Nix Flakes 、 `nix` 指令及 Nixpkgs]]
	3. [[Main#第貳章： Nix 開發環境殼層]]
	4. [[Main#第叄章： Nix REPL 及 Nix 語言]]
	5. [[Main#第肆章： NixOS 模組系統及 Nixpkgs 覆蓋層]]
	6. [[Main#附錄壹： 英文學習資料]]
	7. [[Main#附錄貳： 筆者常用參考頁面]]
- **非 NixOS 用戶且多語學習者：**
	1. [[Main#^89f05b|《百秒 Nix 介紹》— Fireship, 2022]]
	2. [[Main#序章： Nix 包裹管理器]]
	3. [[Main#第壹章： Nix Flakes 、 `nix` 指令及 Nixpkgs]]
	4. [[Main#第貳章： Nix 開發環境殼層]]
	5. [[Main#第叄章： Nix REPL 及 Nix 語言]]
	6. [[Main#^7091e7|《 Nix 基本原理： Flakes 版》— Tony Finn, 2024]]
	7. [[Main#第肆章： NixOS 模組系統及 Nixpkgs 覆蓋層]]
	8. [[Main#附錄貳： 筆者常用參考頁面]]
	9. [[Main#^f7473a|《 Nix 生態官方文檔 》— Nix documentation team]]
- **NixOS 新用戶：**
	1. [[Main#第叄章： Nix REPL 及 Nix 語言]]
	2. [[Main#第肆章： NixOS 模組系統及 Nixpkgs 覆蓋層]]
	3. [[Main#第壹章： Nix Flakes 、 `nix` 指令及 Nixpkgs]]
	4. [[Main#附錄貳： 筆者常用參考頁面]]
	5. [[Main#第貳章： Nix 開發環境殼層]]
	6. [[Main#附錄壹： 英文學習資料]]
- **NixOS 新用戶且多語學習者：**
	1. [[Main#第叄章： Nix REPL 及 Nix 語言]]
	2. [[Main#第肆章： NixOS 模組系統及 Nixpkgs 覆蓋層]]
	3. [[Main#^f7473a|《 Nix 生態官方文檔 》— Nix documentation team]]
	4. [[Main#第壹章： Nix Flakes 、 `nix` 指令及 Nixpkgs]]
	5. [[Main#附錄貳： 筆者常用參考頁面]]
	6. [[Main#^7091e7|《 Nix 基本原理： Flakes 版》— Tony Finn, 2024]]

## 序章： Nix 包裹管理器


Nix 是一個（軟件[^4]）包裹管理器， 旗下包含一個同名的編程語言。 雖說 Nix 語言起初為 Nix 包裹管理器專用， 但如今亦有服務其他衍生的包裹管理器， 如 Lix 。

無論是編程語言或操作系統， 市面上都有主流的包裹管理系統， 例如 Python&mdash;pip； macOS&mdash;Homebrew 。 Nix 主要作為操作系統的包裹管理器為 NixOS 服務， 但實際上它的不少功能也適用於管理不同語言的編程專案[^1]。 而相較於那些主流的包裹管理器， Nix 有以下特色：
- **宣告式設計[^2]**
	主流的包裹管理器基本上都十分依靠命令行， 而顧名思義， 命令行採用了命令式設計[^3]； Nix 把邏輯中心放置在便於記錄及追蹤的檔案上， 大幅減少因使用命令行導致程式開發過程中細節的流失。
	註： Nix 語言屬於（純）函數式程式設計[^6]， 為宣告式設計旗下的設計範式之一， 但 Nix 語言有少量影響純度的函數存在。 ^5711ea
- **原子操作[^5]**
	主流的包裹管理器雖有類似的措施， 但因命令式設計的限制[[Main#^5711ea|（見上文）]]， 並不能有效地令到在執行一系列步驟時， 任何失敗都將反映在最終結果； Nix 杜絕了這種模凌两可結果的存在， 極大提高程式開發環境的可複製性。
	原子操作於 NixOS 的優勢更明顯， 單單修改或嘗試建構有問題[^13]的 NixOS 設置並不會使其生效， 而已經生效的 NixOS 設置亦可被撤銷。 ^37eb6f

學習或使用 Nix 包裹管理器的注意事項： 
- **佔用儲存空間**
  註： 儘管如此， Nix 包裹管理器不會過分複製內容， 其存取的大部分內容將儲存於唯讀檔案系統 `/nix/store` 並透過符號連結[^12]作用於其他檔案系統。
- **文檔散碎**

Nix 包裹管理器目前支援多數 Linux 發行版，對於 macOS 也有部分的社群支援。 要安裝 Nix 包裹管理器，請參考官方的安裝手冊。 該手冊推薦安裝多用戶版本，此安裝方法有便於未來設置特定功能，如遠程建構。


## 第壹章： Nix Flakes 、 `nix` 指令及 Nixpkgs
Flakes 是於 2021 年 11 月所發佈的 Nix 2.4[^9] 首次亮相的實驗功能， 在平常工作流中可作為編程專案[^1]的入口。

它簡化了依附的添加、鎖定及驗證。 以往在 Nix 中下載依附一般是使用該語言內建的獲取器[^7]函數， 但這些函數影響了函數式程式設計的純度， 令程式開發環境的複製性有所下降。 Flakes 在添加依附後會及時鎖定依附的特徵（如哈希校驗碼）， 為將來出現特徵差異時提供亡羊補牢的機會。 

除此之外， 不同的 Flakes 還可以互相依附，讓整個 Nix 生態得到整合[^20]： ^00d353
> Flakes 就像《寵物小精靈》[^21] 卡片一樣讓你拿來跟朋友交換。[^22]

`nix` 指令是與 Flakes 同期加入的實驗功能， 目的是取代傳統的 `nix-` 指令， 處理遺留的技術債[^14]。

由於它們是後加入的實驗功能， 不但 Nix 官方對它的承認有限[^10]， 私人使用前亦須開啟對應的功能標誌。 下列開啟功能標誌的方法可根據不同使用需求而選擇：
- **環境變數**
	```bash
	export NIX_CONFIG = "experimental-features = nix-command flakes"
	```

	此方法適用於非常短期的測試。
- **Nix 包裹管理器設置**
	```conf
	experimental-features = nix-command flakes
	```

	此方法適用於非 NixOS 上的長期使用。 全局設置的位置為 `/etc/nix/nix.conf` ， 用戶設置的位置為 `~/.config/nix/nix.conf`， 有機會需要手動創建設置檔案及路徑中出現的目錄。
- **NixOS 設置**
	```nix
	{
	  nix.settings.experimental-features = [ "nix-command" "flakes" ];
	}
	```

	此方法適用於 NixOS 上的長期使用。 非 Flakes 管理的 NixOS 設置位置為 `/etc/nixos/configuration.nix` ， Flakes 管理的 NixOS 設置則沒有固定位置， 修改 NixOS 設置後必須重新建構系統[[Main#^37eb6f|（見上文）]]令其生效。

Flakes 的固定檔案名稱為 `flake.nix` （非 `flakes.nix` ）， 其包含程度相當的樣板代碼[^8]來完成普遍的基本任務：
1. **添加 Nixpkgs 作為輸入[^15]**
	Nixpkgs 是最龐大的官方 Nix 包裹（及 NixOS 模組）集合[^17]， 同時亦超越其他主流的操作系統包裹集合。 以 Nixpkgs 的 2025 年 5 月穩定版為例， 其收錄五萬餘持續更新中的包裹[^18]。
	Nixpkgs 時常提供任務中必要的軟件以及程式碼依附。
	註： Nixpkgs 本身亦是整合 Nix 生態[[Main#^00d353|（見上文）]]的重要 Flake(s) 個體。
2. **為指定的平同及架構建構輸出[^19]**
	部分輸出要求明確列出支援的平臺及架構[^16]， 例如包裹及開發環境殼層[[Main#第貳章： Nix 開發環境殼層|（見下文）]]，因此 Flakes 經常出現四項預設的平臺及架構。
	某些

目前 Flakes 內建的輸出類型：
- **檢查（ `checks` ）**
- **包裹（ `packages` ）**
- **應用程式（ `apps` ）**
- **排版器（ `formatter` ）**
- **舊式包裹（ `legacyPackages` ）**
- **覆蓋層（ `overlays` ）**
- **NixOS 模組（ `nixosModules` ）**
- **NixOS 設置（ `nixosConfigurations` ）**
- **開發環境殼層（ `devShells` ）**
- **Hydra 工序（ `hydraJobs` ）**
- **範本（ `templates` ）**

其他輸出類型則可透過輸入增加。

<!--
基本 `flake.nix` 例子：
> {
>   inputs.nixpkgs.url = "github:nixos/nixpkgs/nixpkgs-unstable";
>   outputs = { self, nixpkgs }:
> }
-->

## 第貳章： Nix 開發環境殼層
## 第叄章： Nix REPL 及 Nix 語言
## 第肆章： NixOS 模組系統及 Nixpkgs 覆蓋層

## 附錄壹： 英文學習資料

無年份標記即代表內容持續更新。

- 《 Nix 基本原理： Flakes 版》&mdash; Tony Finn, 2022[^11] ^7091e7
- 《百秒 Nix 介紹》&mdash; Fireship, 2024[^22] ^89f05b
- 《 Nix 生態官方文檔 》&mdash; Nix documentation team[^23] ^f7473a
## 附錄貳： 筆者常用參考頁面

無年份標記即代表內容持續更新。

- 《 Nixpkgs 目錄》&mdash; Nix contributors[^24]
- 《 Nixpkgs 說明書》&mdash; Nix contributors[^25]
- 《「用戶主目錄管理器」設置目錄》&mdash; Pim Snel[^26]

[^1]: 即「項目」（ project ）。

[^2]: （ Declarative ）。

[^3]: （ Imperative ）。

[^4]: 即「軟體」（ software ）。

[^5]: （ Atomic ）。

[^6]: （ Purely Functional Programming ）。

[^7]: （ fetchers ）。

[^8]: （ boilerplate code ）。

[^9]: https://nix.dev/manual/nix/2.32/release-notes/rl-2.4.html 。

[^10]: 來源遺失，內容有待核實。

[^11]: https://tonyfinn.com/blog/nix-from-first-principles-flake-edition/ 。

[^12]: （ Symbolic link ）。

[^13]: NixOS 模組系統能力範圍內， 見 https://nixos.wiki/wiki/NixOS_modules 。

[^14]: （ Technical debt ）。

[^15]: 「輸入」為 Flakes 內「依附」的稱呼。

[^16]: （ architecture ）。

[^17]: https://nixos.wiki/wiki/Nixpkgs 。

[^18]: https://repology.org/repositories/statistics/total 。

[^19]: 「輸出」為 Flakes 內「建構結果」的稱呼。

[^20]: 即「集成」（ integrated ）。

[^21]: 即《寶可夢》或《神奇寶貝》（ Pokémon ）。

[^22]: https://youtu.be/FJVFXsNzYZQ?si=k1gIoqUMO1yhhwkw&t=98 。

[^23]: https://nix.dev/ 。

^4f56f9

[^24]: https://search.nixos.org/ 。

[^25]: https://nixos.org/manual/nixpkgs/stable/ 。

[^26]: https://home-manager-options.extranix.com/ 。
