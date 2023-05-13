- [Hackintosh\_MyPC](#hackintosh_mypc)
  - [Giới thiệu](#giới-thiệu)
  - [Kiểm tra cấu hình](#kiểm-tra-cấu-hình)
  - [Tạo USB cài macOS](#tạo-usb-cài-macos)
    - [Chuẩn bị File cần thiết](#chuẩn-bị-file-cần-thiết)
      - [1. Installer](#1-installer)
      - [2. Bootloaders](#2-bootloaders)
      - [3. Firmware Drivers](#3-firmware-drivers)
        - [Universal](#universal)
      - [4. Kexts](#4-kexts)
        - [Bắt buộc phải có Kext sau: `VirtualSMC`, `Lilu`](#bắt-buộc-phải-có-kext-sau-virtualsmc-lilu)
        - [VirtualSMC Plugins](#virtualsmc-plugins)
        - [Graphic](#graphic)
        - [Audio](#audio)
        - [Ethernet](#ethernet)
        - [USB](#usb)
    - [Tạo USB](#tạo-usb)
      - [1. Ghi bộ cài vào USB](#1-ghi-bộ-cài-vào-usb)
      - [2. Tạo thư mục EFI](#2-tạo-thư-mục-efi)
      - [3. Chép EFI vào ESP](#3-chép-efi-vào-esp)
  - [Cài đặt](#cài-đặt)
    - [1. Phân vùng ổ cứng](#1-phân-vùng-ổ-cứng)
    - [2. Setup BIOS](#2-setup-bios)
    - [3. Cài đặt macOS vào ổ cứng](#3-cài-đặt-macos-vào-ổ-cứng)
  - [Sau cài đặt](#sau-cài-đặt)

# Hackintosh_MyPC
## Giới thiệu
> :grey_exclamation: Notes: Hướng dẫn dưới đây **không** áp dụng được cho tất cả các phần cứng, để biết cụ thể cách thực hiện cho máy tính của bản thân, mọi người nên tham khảo các trang sau: 
> Trang tiếng Anh: [dortania](https://dortania.github.io/OpenCore-Install-Guide/)
> Trang tiếng Việt: [VNOHackintosh](https://vnohackintosh.com/)

:recycle: ***Quy trình cơ bản***
Để mọi người không mất phương hướng trong quá trình tìm hiểu thực hiện hackintosh, cần hiểu cơ bản quy trình hackintosh như sau:
1. Chuẩn bị
- USB boot (Mình cài bản **Ventura** nên dùng **32Gb**)
- Internet
- Backup dữ liệu Window (Có khả năng làm *mất hệ điều hành Win*)

2. Tạo bộ cài macOS
- Ghi file bộ cài (file `.dmg`) ra USB (dùng ***etcher***)
- Tạo EFI phù hợp (Bước này có 2 cách: tự tạo EFI nhờ hướng dẫn hoặc tìm kiếm file EFI phù hợp trên Internet)
- Copy thư mục EFI vào ESP
- Chỉnh sửa EFI nếu cần

3. Cài đặt
- Setup BIOS
- Boot USB bộ cài rồi cài macOS lên ổ cứng

4. Kiểm tra và sửa lỗi
- Kiểm tra cpu, GPU, audio, wifi... có hoạt động không
- Cài đặt phần mềm
- Thiết lập một số cài đặt

## Kiểm tra cấu hình
Tải [AIDA64](https://www.aida64.com/downloads) để kiểm tra các thành phần như `CPUID CPU Name`, `Motherboard Name`, `PCI/AGP Video`, `Audio`, `Network`, `Windows Storage`.  
*Dựa vào cấu hình này để làm file EFI hoặc tìm kiếm file EFI phù hợp.*
> :warning: Khuyến khích nên lựa chọn phần cứng phù hợp để cài đặt hackintosh (hỏi cộng đồng VNOhackintosh hoặc tra phần trên trang [vnohackintosh][1])
> :warning: Nếu phần cứng đang có sẵn thì kiểm tra phần cứng có phù hợp không cũng tại trang [vnohackintosh][1]

[1]: <https://vnohackintosh.com/docs/basic-knowledge/limits>

## Tạo USB cài macOS
### Chuẩn bị File cần thiết
#### 1. Installer
Hiện nay có nhiều bản macOS từ Catalina đến Ventura. Bạn cần đưa phần cứng máy của bạn cho những người có kinh nghiệm đánh giá khả năng máy có thể cài được bản nào. Đây là 2 link google drive bản [Monterey (12.6)](https://drive.google.com/file/d/1qDOmuhEXFJW0qih6xm08dQ0_ktfMrYpF/view?usp=sharing) và [Ventura (13.3.1)](https://drive.google.com/file/d/1qEiiUgYiC8fdkpptxQKOO81qc-ZB56tC/view?usp=sharing).
Mọi người có thể tìm các bộ cài khác trên Internet.

#### 2. Bootloaders
Hiện nay có 2 Bootloader chính là [OpenCore](https://github.com/acidanthera/OpenCorePkg/releases) và [Clover](https://github.com/CloverHackyColor/CloverBootloader/releases).
Trong bài hướng dẫn này sử dụng **OpenCore**

#### 3. Firmware Drivers
- Firmware này sử dụng cho bootloader (không phải cho OS)
- Những driver chính đã có sẵn trong file bootloader ở trên (thư mục Drivers).

##### Universal
Mọi hệ thống cần phải có một số `.efi` drivers như: `HfsPlus.efi`, `ApfsDriverLoader.efi`, `OpenRuntime.efi`
> Tham khảo [vnohackintosh][2].

#### 4. Kexts
Kext:  **K**ernel **ext**ension
##### Bắt buộc phải có Kext sau: `VirtualSMC`, `Lilu`

##### VirtualSMC Plugins
Những plugins này không cần thiết để boot, nhưng nó có số tác trong trường hợp cụ thể (nên biết):

`SMCProcessor.kext` Dùng để đọc thông tin nhiệt độ CPU, không hoạt động với CPU AMD  
`SMCBatteryManager.kext` Dùng để đọc thông pin phần trăm pin trên laptop, pc thì bỏ qua  

##### Graphic
`WhateverGreen` Dùng cho graphics patching, DRM fix, board ID check, framebuffer fix, ... tất cả GPU cần kext này

##### Audio
`AppleALC` Dùng để patch AppleHDA, giúp audio hoạt động trong macOS

##### Ethernet
Tuỳ vào cấu hình máy bạn, chọn kext phù hợp với Ethernet card trong máy

`RealtekRTL8111` Dành cho Realtek Gigabit Ethernet. macOS 10.13 dùng bản 2.2.2 tới v2.3.0, macOS 10.14 trở đi dùng bản 2.4.0 hoặc cao hơn. Nếu bạn dùng bản kext cao mà lỗi thì thử bản cũ hơn
`LucyRTL8125Ethernet` Cho Realtek 2.5Gb Ethernet. Đối với Intel I225-V NICs, cần phải patch device properties, sẽ đề cập sau. Một số NICs native trong macOS, thường rất ít khi gặp nên tôi không đề cập

##### USB
`USBInjectAll` Kext dùng cho Intel USB hoạt động mà không cần patch acpi. Không hoạt động với AMD CPU

> Tham khảo [vnohackintosh][2].

[2]: <https://vnohackintosh.com/docs/usb-creation/download-files>

### Tạo USB 
#### 1. Ghi bộ cài vào USB
- Tải phần mềm [etcher](https://github.com/balena-io/etcher/releases/tag/v1.18.4)
- Khởi động, chọn file macOS (.dmg), chọn USB cài macOS và tiến hành Flash USB  
![](https://vnohackintosh.com/assets/images/etcher-63fce3d9d0fc29c5b79bf274e52225c0.png)

#### 2. Tạo thư mục EFI
Có 2 cách để có thư mục EFI:
- Cách 1: Đọc hướng dẫn về [OpenCore Config](https://vnohackintosh.com/docs/opencore-config/intro) và thực hiện theo. Để có thể thực hiện được cần đọc kỹ hướng dẫn từ đầu đến cuối. Cụ thể là copy file sample.plist vào OC đổi tên thành Config.plist và chỉnh sửa file này bằng phần mềm [OCAuxiliaryTools (OCAT)](https://github.com/ic005k/OCAuxiliaryTools) trên Window.
- Cách 2: Tìm kiếm EFI trên trang web [dortania](https://www.olarila.com/topic/5676-hackintosh-efi-folder-with-clover-and-opencore/) dựa trên cấu hình máy tính.

#### 3. Chép EFI vào ESP
- Khi tạo thành công USB boot MACOS, mặc định sẽ có 1 phân vùng EFI bị ẩn đi, cần dùng phần mềm [minitool Partition Wizaard](https://www.partitionwizard.com/free-partition-manager.html) để **mount ESP**
- Truy cập vào phân vùng này và chỉnh sửa dùng [Explorer++](https://explorerplusplus.com/)
- Chép file EFI tự tạo hoặc tìm được trên mạng vào phân vùng này.

## Cài đặt
### 1. Phân vùng ổ cứng
> :warning: Khuyến khích: dùng ổ cứng riêng cho hệ điều hành macOS để việc cài đặt được thuận lợi.

- Dùng Minitool Partion Wizzard để tạo phân vùng EFI >= 200MB.

### 2. Setup BIOS
Có thể tham khảo các trang trên Internet và [vnohackintosh](https://vnohackintosh.com/docs/installation/setup-bios)
### 3. Cài đặt macOS vào ổ cứng
Xem hướng dẫn tại [vnohackintosh](https://vnohackintosh.com/docs/installation/install-macos)

## Sau cài đặt
Đọc những phần tương ứng với những phần còn thiếu sau khi cài đặt macOS.
https://vnohackintosh.com/docs/post-install/must-do-after-install