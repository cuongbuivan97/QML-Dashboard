# QML Dashboard

Ứng dụng Dashboard được xây dựng bằng **Qt 6 / QML**, hỗ trợ đa ứng dụng nhúng (calendar, email, radio, khoá học, tin tức, PLC) với hệ thống giao diện tùy biến theo chủ đề màu sắc.

> **A Qt 6 / QML multi-app dashboard** featuring calendar, email, web radio, courses, latest news, and PLC apps — with a dynamic green/orange theme system.

---

## Yêu cầu / Requirements

| Công cụ | Phiên bản |
|---------|-----------|
| Qt      | ≥ 6.5     |
| CMake   | ≥ 3.16    |
| Trình biên dịch C++ | hỗ trợ C++17 |

---

## Cách build / How to build

```bash
cmake -S . -B build
cmake --build build
./build/QDashboardApp
```

---

## Cấu trúc dự án / Project Structure

```
QML-Dashboard/
├── src/                      # C++ entry point & helpers
│   ├── main.cpp              # Điểm khởi động ứng dụng / App entry point
│   ├── app_environment.h     # Thiết lập biến môi trường Qt / Qt env setup
│   └── import_qml_plugins.h  # Import tĩnh các QML plugin / Static plugin imports
│
├── Main.qml                  # Cửa sổ gốc (ApplicationWindow) / Root window
├── CMakeLists.txt            # Cấu hình build chính / Main build config
├── QDashBoardApp.qmlproject  # File dự án Qt Creator / Qt Creator project file
├── qtquickcontrols2.conf     # Cấu hình style mặc định / Default style config
│
├── qmlmodules/               # Khai báo module QML (CMake) / QML module declarations
│
├── styles/                   # Theme giao diện tuỳ chỉnh / Custom UI theme
│   └── qdashboardstyle/      # Override các control Qt Quick Controls 2
│
├── imports/                  # Thành phần dùng chung / Shared components
│   ├── controls/             # BaseCard, Separator
│   ├── popups/               # NewReminderPopup, NewTaskPopup, NewOrReplyEmailPopup
│   ├── views/                # RemindersListView
│   ├── utils/                # Style singleton, Tracer (debug)
│   └── assets/               # Font, icon, ảnh, mesh 3D
│
├── mainui/                   # Khung giao diện chính / Main UI shell
│   ├── home/                 # HomePage, Header, Dashboard
│   └── mainmenu/             # MainMenu, MainMenuList, UserInfo
│
└── apps/                     # Các ứng dụng tính năng / Feature apps
    ├── calendar/             # Lịch & quản lý nhiệm vụ
    ├── webradio/             # Trình phát radio trực tuyến
    ├── inbox/                # Quản lý email
    ├── courses/              # Theo dõi tiến độ khoá học
    ├── latestnews/           # Tin tức mới nhất
    └── plc/                  # Ứng dụng PLC
```

---

## Giải thích chi tiết / Detailed Explanation

### 1. Điểm khởi động — `src/main.cpp`

```
QGuiApplication → QQmlApplicationEngine → load module "Main" → Main.qml
```

`main.cpp` khởi tạo Qt, tạo engine QML rồi nạp module `Main` (tức là `Main.qml`).

---

### 2. Cửa sổ gốc — `Main.qml`

```
ApplicationWindow (1920×1080)
└── Item
    ├── HomePage   ← toàn bộ nội dung dashboard
    └── Popups     ← các hộp thoại popup (reminder, task, email)
```

`Main.qml` là cửa sổ `ApplicationWindow` với kích thước 1920×1080, chứa `HomePage` và lớp `Popups` nổi phía trên.

---

### 3. Khung giao diện chính — `mainui/`

```
mainui/
├── home/
│   ├── HomePage.qml    ← layout tổng thể (Header + MainMenu + Dashboard)
│   ├── Header.qml      ← thanh tiêu đề: tên menu, nút Reorder, Settings, thoát
│   └── Dashboard.qml   ← vùng nội dung trung tâm (placeholder)
└── mainmenu/
    ├── MainMenu.qml    ← thanh menu dọc bên trái
    ├── MainMenuList.qml← danh sách các mục menu
    └── UserInfo.qml    ← ảnh đại diện, tên, địa chỉ, trạng thái người dùng
```

**HomePage** sắp xếp bố cục:
- `MainMenu` (285 px, cột trái, màu chủ đạo)
- `Header` (70 px, hàng trên, chiều rộng = màn hình − menu)
- `Dashboard` (phần còn lại, nội dung chính)

**Header** cung cấp:
- Nhãn tên menu hiện tại
- Switch *Reorder* để sắp xếp lại widget
- Nút *Settings* → dropdown chọn theme (Green / Orange)
- Nút thoát ứng dụng

---

### 4. Hệ thống Style — `imports/utils/Style.qml`

`Style` là một **singleton** dùng chung toàn dự án, cung cấp:

| Thuộc tính | Giá trị / Mô tả |
|-----------|----------------|
| `screenWidth/Height` | 1920 × 1080 |
| `theme` | `"green"` hoặc `"orange"` |
| `mainColor` | `#00D1A9` (green) / `#FEA601` (orange) |
| `bgColor` | `#D1DBE1` |
| `fontFamilyBold/Regular` | Quicksand Bold / Book |
| `fontSizeS/M/L` | 14 / 18 / 24 px |

**Hàm tiện ích:**
- `Style.icon("name")` → đường dẫn icon PNG
- `Style.gfx("name")` → đường dẫn ảnh PNG
- `Style.mesh("name")` → đường dẫn model 3D (.obj)
- `Style.setGreenTheme()` / `Style.setOrangeTheme()` → đổi theme

---

### 5. Thành phần dùng chung — `imports/`

| Module | Thành phần | Mô tả |
|--------|-----------|-------|
| `controls` | `BaseCard` | Card nền dùng chung |
| `controls` | `Separator` | Đường phân cách |
| `popups` | `NewReminderPopup` | Tạo nhắc nhở mới |
| `popups` | `NewTaskPopup` | Tạo nhiệm vụ mới |
| `popups` | `NewOrReplyEmailPopup` | Soạn / trả lời email |
| `popups` | `Popups` | Container chứa tất cả popup |
| `views` | `RemindersListView` | Danh sách nhắc nhở |
| `utils` | `Style` | Singleton style (xem mục 4) |
| `utils` | `Tracer` | Công cụ debug trực quan |
| `assets` | fonts, icons, images, mesh | Tài nguyên đa phương tiện |

---

### 6. Các ứng dụng tính năng — `apps/`

Mỗi app tuân theo cấu trúc:

```
apps/{tên-app}/
├── CMakeLists.txt   # Khai báo module QML
├── qmldir           # Định nghĩa module
├── Main.qml         # Component gốc của app
├── stores/          # Mô hình dữ liệu (RootStore, ListModel…)
├── views/           # Giao diện hiển thị danh sách / lưới
├── panels/          # Bảng nội dung chính
└── controls/        # Điều khiển riêng của app
```

| App | Chức năng | Thành phần nổi bật |
|-----|-----------|-------------------|
| `calendar` | Lịch & nhiệm vụ | CalendarPanel, TasksListView |
| `webradio` | Phát radio trực tuyến | RadioView, RadioControlsPanel, Object3DPanel (Qt Quick 3D) |
| `inbox` | Quản lý email | InboxView, EmailsListModel, InboxViewDelegate |
| `courses` | Tiến độ khoá học | CourseProgressPanel, WeeklyActivityPanel, CircularBar |
| `latestnews` | Tin tức | NewsItemsView, CategoriesPanel |
| `plc` | Ứng dụng PLC | Main.qml (stub) |

---

### 7. Style theme — `styles/qdashboardstyle/`

Ghi đè (override) các control mặc định của Qt Quick Controls 2:

`Button` · `ToolButton` · `Switch` · `Slider` · `Dial` · `RadioButton` · `Popup` · `Label` · `BusyIndicator`

Theme được chọn bằng `qtquickcontrols2.conf`:

```ini
[Controls]
Style=qdashboardstyle
```

---

### 8. Sơ đồ phụ thuộc module / Module Dependency Diagram

```
QDashboardApp (executable)
├── Main (Main.qml)
├── qdashboardstyle   ← styles/
├── utils             ← imports/utils/
├── controls          ← imports/controls/
├── popups            ← imports/popups/
├── views             ← imports/views/
├── mainui            ← mainui/
├── calendar          ← apps/calendar/
├── courses           ← apps/courses/
├── inbox             ← apps/inbox/
├── latestnews        ← apps/latestnews/
└── webradio          ← apps/webradio/
```

---

## Luồng dữ liệu / Data Flow

```
main.cpp
  └─► QQmlApplicationEngine loads "Main"
        └─► Main.qml (ApplicationWindow)
              ├─► HomePage
              │     ├─► Header (theme switch, reorder, quit)
              │     ├─► MainMenu (navigation)
              │     └─► Dashboard (content area)
              └─► Popups (overlays)
                    ├─► NewReminderPopup
                    ├─► NewTaskPopup
                    └─► NewOrReplyEmailPopup
```

---

## License

Xem file `LICENSE` (nếu có) trong thư mục gốc của dự án.
