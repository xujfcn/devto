---
title: 'Hướng dẫn cài đặt Claude Code trên Windows: Git, PATH, biến môi trường, PowerShell, WSL và xử lý lỗi toàn diện (2026)'
published: true
description: 'Hướng dẫn chi tiết cho người mới bắt đầu về cách cài đặt Claude Code trên Windows. Bao gồm PowerShell, winget, Git for Windows, Node.js, PATH, biến môi trường, Command Prompt vs PowerShell vs WSL, và các vấn đề sau cài đặt phổ biến.'
tags: 'ai, cli, tutorial, windows'
canonical_url: null
cover_image: 'https://lsky.zhongzhuan.chat/i/2025/12/29/69523b0cd9f0d.png'
id: 3511044
date: '2026-04-16T14:48:35Z'
---

Người dùng Windows thường gặp phải những vấn đề khác với người dùng macOS khi sử dụng Claude Code.

Không phải vì Claude Code không thể chạy trên Windows, mà vì Windows có nhiều cách kết hợp môi trường hơn:

- Command Prompt
- PowerShell
- Windows Terminal
- Git Bash
- WSL
- Node được cài từ MSI
- Node được cài từ winget
- Git được cài nhưng chưa thêm vào PATH
- biến môi trường được thiết lập cho một shell nhưng không phải shell khác

Đó là lý do hướng dẫn này diễn ra từng bước và giải thích chuỗi cài đặt từ đầu.

## Trước khi cài đặt bất cứ điều gì: Chọn đường dẫn Windows của bạn

Có hai đường dẫn thực tế.

### Tùy chọn A: Thiết lập Windows gốc

Sử dụng:
- Windows Terminal
- PowerShell
- Git for Windows
- Node.js for Windows
- npm global install

Cách này dễ hơn cho hầu hết người mới bắt đầu.

### Tùy chọn B: Thiết lập WSL

Sử dụng:
- WSL2
- Ubuntu hoặc Debian bên trong WSL
- Các bước cài đặt Linux bên trong WSL

Cách này thường sạch hơn cho nhà phát triển, nhưng nó thêm một lớp phức tạp. Nếu bạn hoàn toàn mới, hãy bắt đầu với Windows gốc trước khi không phải là bạn đã sử dụng WSL.

## Thiết lập được khuyến nghị cho người mới bắt đầu

Đối với hầu hết người dùng mới, tôi khuyến nghị:

- Windows 11 hoặc Windows 10 được cập nhật
- Windows Terminal
- PowerShell
- winget để cài đặt gói
- Git for Windows
- Node.js LTS
- Claude Code qua npm

## Bước 1: Mở terminal đúng

Cài đặt hoặc khởi chạy **Windows Terminal** nếu có thể.

Sau đó mở **PowerShell**.

Kiểm tra shell nào bạn đang sử dụng:

```powershell
$PSVersionTable.PSVersion
```

Nếu PowerShell mở và hoạt động, hãy ở đó trong suốt quá trình cài đặt. Không trộn PowerShell, cmd, Git Bash, và WSL khi cài đặt ban đầu trừ khi bạn biết lý do.

## Bước 2: Kiểm tra `winget` có sẵn hay không

`winget` là cách dễ nhất để cài đặt Git và Node trên Windows hiện đại.

```powershell
winget --version
```

Nếu nó hoạt động, tuyệt vời.

Nếu không:
- cập nhật App Installer từ Microsoft Store
- hoặc cài đặt gói thủ công từ trang web chính thức

## Bước 3: Cài đặt Git

Kiểm tra trước:

```powershell
git --version
```

Nếu Git bị thiếu, hãy cài đặt nó với winget:

```powershell
winget install --id Git.Git -e --source winget
```

Sau đó **đóng và mở lại PowerShell**.

Xác minh:

```powershell
git --version
where.exe git
```

### Tại sao Git quan trọng trên Windows

Lý do tương tự như trên macOS:
- quy trình làm việc nhận thức kho lưu trữ
- diffs
- chỉnh sửa an toàn hơn
- khôi phục phiên bản
- nhiều công cụ nhà phát triển giả định Git

Nếu bạn chưa có kho lưu trữ:

```powershell
mkdir $HOME\Projects\claude-code-test -Force
cd $HOME\Projects\claude-code-test
git init
```

Danh tính toàn cầu tùy chọn:

```powershell
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

## Bước 4: Cài đặt Node.js và npm

Kiểm tra phiên bản hiện tại:

```powershell
node --version
npm --version
```

Nếu bị thiếu, hãy cài đặt Node.js:

```powershell
winget install --id OpenJS.NodeJS.LTS -e --source winget
```

Đóng và mở lại PowerShell.

Xác minh:

```powershell
node --version
npm --version
where.exe node
where.exe npm
```

## Bước 5: Cài đặt Claude Code

Kiểm tra xem nó đã tồn tại hay chưa:

```powershell
where.exe claude
claude --version
```

Nếu không, hãy cài đặt nó toàn cầu:

```powershell
npm install -g @anthropic-ai/claude-code
```

Sau đó xác minh:

```powershell
where.exe claude
claude --version
```

## Bước 6: Sửa các vấn đề PATH trên Windows

Phàn nàn phổ biến nhất của Windows là:

- npm install cho biết thành công
- nhưng `claude` vẫn không được công nhận

Lỗi điển hình:

```powershell
claude : The term 'claude' is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### Trước tiên tìm tiền tố toàn cầu của npm

```powershell
npm config get prefix
```

Thường điều này chỉ đến một thư mục có `bin` hoặc vị trí có thể thực thi nên có thể được khám phá thông qua PATH.

Kiểm tra nơi npm cài đặt `claude`:

```powershell
npm list -g --depth=0
```

Cũng hãy thử:

```powershell
Get-Command claude -ErrorAction SilentlyContinue
```

### Sửa phổ biến: mở lại shell

Rất nhiều vấn đề PATH chỉ là các phiên bản cũ. Đóng PowerShell hoàn toàn, mở một cái mới, và thử lại.

### Nếu PATH vẫn sai

Kiểm tra PATH của người dùng:

```powershell
[Environment]::GetEnvironmentVariable("Path", "User")
```

Và PATH máy:

```powershell
[Environment]::GetEnvironmentVariable("Path", "Machine")
```

Nếu vị trí có thể thực thi toàn cầu của npm bị thiếu, bạn có thể thêm nó thông qua giao diện Biến môi trường Windows hoặc với PowerShell.

Hãy cẩn thận khi chỉnh sửa PATH theo chương trình. Sao lưu nó trước.

## Bước 7: Đặt Biến môi trường một cách chính xác

Người dùng Windows thường đặt các biến ở một nơi và giả định rằng mọi shell sẽ thấy chúng. Điều đó không phải lúc nào cũng đúng.

### Biến chỉ dành cho phiên làm việc trong PowerShell

```powershell
$env:ANTHROPIC_API_KEY = "your_key_here"
$env:OPENAI_API_KEY = "your_crazyrouter_key"
$env:OPENAI_BASE_URL = "https://crazyrouter.com/v1"
```

Những biến này chỉ tồn tại cho phiên làm việc hiện tại.

### Biến môi trường cấp người dùng lâu dài

```powershell
[Environment]::SetEnvironmentVariable("ANTHROPIC_API_KEY", "your_key_here", "User")
[Environment]::SetEnvironmentVariable("OPENAI_API_KEY", "your_crazyrouter_key", "User")
[Environment]::SetEnvironmentVariable("OPENAI_BASE_URL", "https://crazyrouter.com/v1", "User")
```

Sau đó đóng và mở lại PowerShell.

Xác minh:

```powershell
echo $env:ANTHROPIC_API_KEY
echo $env:OPENAI_API_KEY
echo $env:OPENAI_BASE_URL
```

## Bước 8: Hiểu sự khác biệt giữa PowerShell, cmd, Git Bash, và WSL

Điều này có quan trọng vì các biến và PATH có thể hoạt động khác nhau.

| Môi trường | Tốt cho người mới bắt đầu? | Ghi chú |
|------------|---------------------|-------|
| PowerShell | Có | Lựa chọn Windows gốc tốt nhất |
| Command Prompt | Ổn | Ít tiện lợi hơn PowerShell |
| Git Bash | Hỗn hợp | Hoạt động, nhưng thêm một lớp shell |
| WSL | Tốt cho nhà phát triển | Tốt nhất nếu bạn muốn hành vi giống Linux |

Nếu bạn cài đặt Claude Code trong PowerShell Windows gốc, không nên kiểm tra nó trước tiên trong WSL và giả định rằng cùng một môi trường được áp dụng.

WSL có hệ thống gói riêng, đường dẫn, tệp shell, và biến.

## Bước 9: Đường dẫn WSL tùy chọn

Nếu bạn muốn môi trường nhà phát triển sạch nhất lâu dài trên Windows, hãy cài đặt WSL.

Kiểm tra WSL:

```powershell
wsl --status
```

Cài đặt nếu cần:

```powershell
wsl --install
```

Sau đó khởi động lại nếu Windows yêu cầu.

Sau đó, mở Ubuntu và coi nó như một máy Linux:

- cài đặt Git bên trong WSL
- cài đặt Node bên trong WSL
- cài đặt Claude Code bên trong WSL
- đặt biến môi trường bên trong tệp shell WSL

Không giả định rằng cài đặt Node phía Windows của bạn tự động bao gồm WSL.

## Bước 10: Xác minh mọi thứ end-to-end

Chạy những kiểm tra này:

```powershell
git --version
node --version
npm --version
claude --version
where.exe git
where.exe node
where.exe npm
where.exe claude
```

Sau đó tạo một thư mục kiểm tra an toàn:

```powershell
mkdir $HOME\Projects\claude-code-test -Force
cd $HOME\Projects\claude-code-test
if (-not (Test-Path .git)) { git init }
"# test" | Out-File README.md -Encoding utf8
```

Và kiểm tra CLI với các lệnh không phá hủy trước tiên.

## Các vấn đề Windows phổ biến và cách sửa

## 1. `claude` không được công nhận

Nguyên nhân:
- thư mục có thể thực thi toàn cầu của npm không ở trong PATH
- shell chưa được khởi động lại
- cài đặt thất bại

Sửa:
- mở lại PowerShell
- kiểm tra `npm config get prefix`
- xác nhận gói tồn tại trong danh sách npm toàn cầu
- kiểm tra PATH

## 2. Git cài đặt nhưng PowerShell vẫn không thể tìm thấy nó

Nguyên nhân:
- phiên làm việc terminal được mở trước cài đặt
- PATH không được làm tươi

Sửa:
- hoàn toàn đóng và mở lại terminal
- xác minh với `where.exe git`

## 3. Node cài đặt, nhưng npm bị mất hoặc bị hỏng

Nguyên nhân:
- cài đặt không hoàn chỉnh
- phiên bản Node cũ xung đột

Sửa:
- gỡ cài đặt các phiên bản Node xung đột nếu cần thiết
- cài đặt lại LTS một cách sạch sẽ
- xác minh cả `node --version` và `npm --version`

## 4. Biến môi trường được đặt trong PowerShell nhưng không ở terminal khác

Nguyên nhân:
- biến chỉ dành cho phiên làm việc

Sửa:
- sử dụng biến môi trường cấp người dùng lâu dài
- mở lại terminal sau khi đặt chúng

## 5. WSL hoạt động nhưng PowerShell không, hoặc ngược lại

Nguyên nhân:
- bạn đã thiết lập hai môi trường khác nhau

Sửa:
- quyết định xem bạn muốn Windows gốc hay WSL là môi trường Claude Code chính của mình
- hoàn thành cài đặt bên trong môi trường đó hoàn toàn

## 6. Proxy công ty chặn npm install

Bạn có thể cần:

```powershell
npm config set proxy http://proxy.example.com:8080
npm config set https-proxy http://proxy.example.com:8080
```

Và có thể cả biến phiên làm việc nữa.

## 7. Phần mềm antivirus hoặc bảo mật can thiệp

Đôi khi các công cụ bảo mật can thiệp với các công cụ CLI hoặc tập lệnh mới cài đặt.

Nếu nhật ký cài đặt trông bình thường nhưng các tệp có thể thực thi không hoạt động bình thường, hãy kiểm tra trong terminal sạch, xác nhận tệp tồn tại, và kiểm tra lịch sử Bảo mật Windows hoặc bảo vệ điểm cuối.

## Thiết lập Windows mặc định an toàn

Nếu bạn muốn con đường đơn giản nhất dễ hỗ trợ nhất, hãy sử dụng chính xác ngăn xếp này:

- Windows Terminal
- PowerShell
- winget
- Git for Windows
- Node.js LTS
- Claude Code qua npm global install
- biến môi trường cấp người dùng lâu dài

Thiết lập đó nhàm chán, và đó chính xác là lý do tại sao nó lại tốt.

## Câu hỏi thường gặp

### Người mới bắt đầu nên sử dụng PowerShell hay WSL cho Claude Code?

Nếu bạn mới, hãy bắt đầu với PowerShell. Nếu bạn đã thích công cụ Linux hay đã sử dụng WSL hàng ngày, WSL có thể sạch hơn lâu dài.

### Tại sao Claude Code cài đặt thành công nhưng vẫn không chạy?

Thường xuyên nhất: PATH cũ, shell sai, hoặc npm cài đặt gói vào một vị trí mà terminal hiện tại của bạn không đọc được.

### Tôi có cần Git trước khi sử dụng Claude Code trên Windows không?

Để sử dụng nghiêm túc, có. Ngay cả khi CLI bắt đầu mà không cần Git, các quy trình làm việc mã hóa bình thường sạch hơn nhiều với Git được cài đặt và định cấu hình.

### Tôi nên lưu trữ các biến môi trường Claude Code ở đâu trên Windows?

Để duy trì, hãy đặt chúng ở mức môi trường người dùng, không chỉ phiên shell hiện tại.

### Git Bash có phải là một nơi tốt để chạy Claude Code không?

Nó có thể hoạt động, nhưng đối với người mới bắt đầu, nó thêm nhiều biến hơn. PowerShell đơn giản hơn để ghi chép và hỗ trợ.

## Kết luận cuối cùng

Câu chuyện cài đặt Windows không khó vì Claude Code chính nó khó. Nó khó vì Windows cho bạn nhiều môi trường trùng lặp.

Nếu bạn giữ thiết lập nhất quán — PowerShell, winget, Git, Node, npm, Claude Code, sau đó các biến môi trường — cài đặt sẽ dễ dàng hơn nhiều để gỡ lỗi và dạy dỗ.
