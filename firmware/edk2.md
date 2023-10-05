# EDK2

## 簡介

UEFI全名為Unified Extensible Firmware Interface，目前由UEFI協會管理與制定規格。UEFI推出一套稱為EDK（EFI Development Kit，而穩定發佈版稱之為UDK）開放原始碼的開發套件，裡面除了已經定義好的標準外（如C的header file），還有許多被已被開發出的標準程式模組。

### UDK

全名為UEFI Development Kit ，是在做UEFI開發所需要的軟體套件，然後UDK是在EDK2當中比較穩定的release，因為UDK的releases 會透過Intel架構平台測試。

## Package的概念

在EDK寫UEFI driver的時候沒有模組(module)的概念。開發的時候是以 driver(UEFI的可執行檔)為單位，並不會考慮一些模組化的問題。&#x20;

在EDK2裡加上了 Package這個概念(模組化)。可以把它看成是一個檔案目錄，而這個目錄名稱會是"xxxPkg"，而目錄裡面就是一堆相關的UEFI driver。

所謂的UEFI Driver是指若目錄名稱是"NetworkPkg"，那麼這個Package裡面的東西就跟Network有關，所以在這個目錄內可以有其他子目錄去存放著 PEI/DXE/SMM/APP 的 Driver或是 Library。

&#x20;組成一個 Package的元素(一個PKG目錄內)必定包含一個 \*.dec 和 \*.dsc。Package的好處是「獨立」。在EDK時，若要單獨產生一個特定的CODE時，你至少需要整套CODE要先產生一次。而EDK2使用這種"獨立"的概念就不需要整套CODE build完才產生你要的CODE。但也是因為獨立，所以有很多地方都是為了解決獨立出來問題而去改了一堆Build tool動作跟描述.dsc還有.FDF的動作。

## EDK與EDK2主要的變動

### Build process的改變&#xD;

* EDK : 由ProcessDsc.exe執行將 \*.inf 轉換成 makefile 的動作
* EDK2: 由Build.py  去處理，同時還會產生  AutoGen.c & AutoGen.h (Parsing \*.dec )

### DSC file / FDF file&#xD;

在EDKI裡，決定一個 driver要放在那一個 firmware volume裡是在"1個.dsc"就可以決定的，通常都會是叫做PlatformDsc.dsc或是 Build.dsc這個檔案。&#x20;

在檔案內會透過FV=FVREVOVERY的方式把底下的UEFI Driver放進去FVREVOVERY.FV，接著透過Flashmap.fdf去規劃ROM的區域，然後決定 FVMAIN.FV放進去哪個區域。&#x20;

但是 EDK2為了實現 Package這個概念，所以.dsc不再用來描述UEFI driver要放到哪個FV，而只是拿來決定要不要BUILD這目錄下的UEFI driver而已。描述那些UEFI DRIVER放到ROM的哪個位置的動作就變成EDK2新的FDF SPEC所描述的那些動作，所以必須要新的FDF 格式中描述你那些UEFI driver是要放進去ROM裡面的。

### PCD/DEC&#xD;

因為EDK2希望做到不用改到Source code就能夠改變CODE的行為，所以延伸出了PCD這種控制方式。



### &#xD;

&#x20;
