# BÀI TẬP 3 - HỌC PHẦN PHÁT TRIỂN ỨNG DỤNG TRÊN THIẾT BỊ DI ĐỘNG

## Thông tin chung

* **Công cụ phát triển:** Android Studio
* **Tác giả:** Ngụy Đình Tuấn Hà - K225480106011
* **Mục tiêu:** Xây dựng ứng dụng Android đơn giản gồm 3 màn hình với tên **GREEN_FOOTPRINT**.

---

# PHẦN 1: LÝ THUYẾT VÀ MÔ TẢ BÀI TOÁN

## 1. Cơ sở lý thuyết

### Dấu chân Carbon (Carbon Footprint)

Là tổng lượng khí nhà kính (chủ yếu là CO₂) được giải phóng vào khí quyển từ các hoạt động sinh hoạt và sản xuất của con người.

### Bài toán điện năng tiêu thụ

Điện năng sử dụng tại Việt Nam phần lớn được sản xuất từ các nhà máy nhiệt điện (than, khí đốt). Do đó, mỗi kWh điện tiêu thụ đều gián tiếp tạo ra một lượng CO₂ nhất định.

### Công thức tính toán

**Lượng CO₂ phát thải (kg)** = **Điện năng tiêu thụ (kWh)** × **Hệ số phát thải (kg CO₂/kWh)**

> Hệ số phát thải mặc định: **0.72 kg CO₂/kWh**


> Hệ số phát thải mặc định sử dụng trong bài toán: **0.72 kg CO₂/kWh**

---

## 2. Mô tả chức năng ứng dụng

Ứng dụng **GREEN_FOOTPRINT** được cấu hình chạy trên nền tảng **SDK 17 (Android 4.2)** và gồm 3 màn hình (Activity):

### Activity 1 - Giới thiệu (About)

* Hiển thị thông tin sinh viên thực hiện.
* Hiển thị slogan bảo vệ môi trường.
* Cung cấp các nút điều hướng đến các chức năng còn lại.

### Activity 2 - Tính toán và gửi dữ liệu

* Nhập lượng điện tiêu thụ (kWh).
* Nhập hệ số phát thải.
* Tính toán lượng CO₂ phát sinh.
* Đóng gói dữ liệu JSON dạng lồng nhau:

  * `app_by`
  * `input`
  * `output`
* Gửi dữ liệu lên máy chủ thông qua HTTP POST.

### Activity 3 - Thống kê (WebView)

Tích hợp WebView để hiển thị trực tiếp trang thống kê của nhà trường:

```text
https://k58kmt.tdh.io.vn?masv=K225480106011
```

---

# PHẦN 2: QUY TRÌNH THỰC HIỆN

## 1. Các bước xây dựng ứng dụng

### Bước 1: Khởi tạo dự án và cấu hình hệ thống

* Tạo project dạng `Empty Views Activity`.
* Ngôn ngữ lập trình: **Java**.
* Thiết lập `minSdkVersion = 17`.
<img width="669" height="433" alt="Ảnh chụp màn hình 2026-06-11 122325" src="https://github.com/user-attachments/assets/cc340214-c94f-4590-b038-3e578a88b478" />
<img width="959" height="539" alt="Ảnh chụp màn hình 2026-06-11 122755" src="https://github.com/user-attachments/assets/e6d95acd-df7b-429d-884b-84318afd3668" />

Khai báo quyền Internet trong `AndroidManifest.xml`:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

Cho phép truyền dữ liệu không mã hóa trên các thiết bị Android cũ:

```xml
android:usesCleartextTraffic="true"
```
<img width="959" height="337" alt="Ảnh chụp màn hình 2026-06-11 155317" src="https://github.com/user-attachments/assets/4b06961f-eed9-4d31-ba6c-f5f16244e1ae" />

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">
    <uses-permission android:name="android.permission.INTERNET" />
    <application
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:usesCleartextTraffic="true"
        android:theme="@style/Theme.GREEN_FOOTPRINT">
        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <activity android:name=".CalcActivity" />
        <activity android:name=".WebActivity" />
    </application>

</manifest>
```
---

### Bước 2: Thêm thư viện kết nối mạng (Dependencies)

Bổ sung Retrofit và Gson vào file `build.gradle`:

```gradle
implementation 'com.squareup.retrofit2:retrofit:2.9.0'
implementation 'com.squareup.retrofit2:converter-gson:2.9.0'
implementation 'com.google.code.gson:gson:2.10.1'
```
<img width="959" height="350" alt="Ảnh chụp màn hình 2026-06-11 155351" src="https://github.com/user-attachments/assets/7520cd54-a498-47ec-93b6-8ceae69f8f07" />

---

### Bước 3: Thiết kế giao diện Eco-Style

* Đưa toàn bộ chuỗi ký tự vào `strings.xml`.
* Đưa toàn bộ màu sắc vào `colors.xml`.
* Sử dụng `CardView` để nhóm các thành phần giao diện.
* Áp dụng tông màu xanh lá pastel:

```text
#F4F9F4
#2E7D32
```
<img width="959" height="340" alt="Ảnh chụp màn hình 2026-06-11 155406" src="https://github.com/user-attachments/assets/546b5e60-cfe8-4cbb-b457-c4d2cfd0eea7" />

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="bg_eco_light">#F4F9F4</color>
    <color name="eco_dark">#1B5E20</color>
    <color name="eco_green">#4CAF50</color>
    <color name="eco_medium">#2E7D32</color>
    <color name="eco_teal">#00796B</color>
    <color name="eco_light_green">#66BB6A</color>
    <color name="bg_result_card">#E8F5E9</color>

    <color name="text_dark_gray">#424242</color>
    <color name="text_light_gray">#757575</color>
    <color name="text_black">#212121</color>
    <color name="white">#FFFFFF</color>
</resources>
```

<img width="959" height="464" alt="Ảnh chụp màn hình 2026-06-11 155418" src="https://github.com/user-attachments/assets/a8588df9-d819-41fa-994a-4e0d47d7f52a" />

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="app_name">GREEN_FOOTPRINT</string>

    <string name="app_title">GREEN FOOTPRINT</string>
    <string name="app_slogan">Chung tay bảo vệ môi trường</string>
    <string name="title_sv_info">THÔNG TIN SINH VIÊN</string>
    <string name="sv_name">Ngụy Đình Tuấn Hà</string>
    <string name="sv_id">MSSV: K225480106011</string>
    <string name="btn_nav_calc">Tính Toán Carbon</string>
    <string name="btn_nav_web">Xem Thống Kê Hệ Thống</string>

    <string name="title_calc">Tính Toán Lượng Carbon</string>
    <string name="label_kwh">Điện năng tiêu thụ (kWh)</string>
    <string name="hint_kwh">Ví dụ: 150</string>
    <string name="label_pt">Hệ số phát thải (kg CO2/kWh)</string>
    <string name="hint_pt">Hệ số mặc định</string>
    <string name="default_pt_val">0.72</string>
    <string name="btn_action_calc">TÍNH TOÁN &amp; GỬI BÁO CÁO</string>
    <string name="result_default">Kết quả: Chưa tính toán</string>
</resources>
```

---

### Bước 4: Xây dựng cấu trúc API bằng Retrofit

* Tạo `ApiResponse.java` để nhận dữ liệu phản hồi từ server.
* Tạo `ApiService.java`.
* Định nghĩa phương thức:

```java
@POST("api")
```

* Truyền dữ liệu động bằng `Map<String, Object>` kết hợp Gson.
<img width="959" height="299" alt="Ảnh chụp màn hình 2026-06-11 155430" src="https://github.com/user-attachments/assets/602cbd50-77a1-4325-8236-cd0d19e1b9c2" />

<img width="959" height="524" alt="Ảnh chụp màn hình 2026-06-11 155443" src="https://github.com/user-attachments/assets/efe70331-c32b-47d8-9b8d-c46ae34bc7fc" />

---

### Bước 5: Xử lý logic ứng dụng

* Sử dụng `new View.OnClickListener()` thay cho Lambda.
* Đảm bảo tương thích với SDK 17.
* Cấu hình WebView và bật JavaScript để hiển thị đầy đủ chức năng website.
<img width="959" height="513" alt="Ảnh chụp màn hình 2026-06-11 155547" src="https://github.com/user-attachments/assets/24dde7b8-f474-4f56-ab33-f580ed12c9bf" />

<img width="959" height="508" alt="Ảnh chụp màn hình 2026-06-11 155559" src="https://github.com/user-attachments/assets/d7c3dfc9-1e1c-4273-8a34-3a2095ec05aa" />

---

## 2. Kinh nghiệm thực tế

### Lỗi Lambda trên SDK thấp

Các cú pháp:

```java
v -> {}
```

có thể gây lỗi biên dịch trên môi trường SDK cũ.

Giải pháp:

```java
new View.OnClickListener() {
    @Override
    public void onClick(View v) {

    }
}
```

---

### Lỗi gửi API bằng Volley

Ban đầu sử dụng Volley và `JSONObject`, ứng dụng thường xuyên rơi vào `onErrorResponse()`.

Nguyên nhân:

* Header dữ liệu chưa chuẩn.
* Xử lý JSON phức tạp.
* Sai lệch kiểu dữ liệu số thực.

Giải pháp:

* Chuyển sang Retrofit.
* Kết hợp Gson.
* Đóng gói dữ liệu bằng `Map<String, Object>`.

---

### Lỗi thuộc tính EditText

Sai:

```xml
android:value="0.72"
```

Đúng:

```xml
android:text="0.72"
```

---

# PHẦN 3: SO SÁNH MIT APP INVENTOR VÀ ANDROID STUDIO

| Tiêu chí                | MIT App Inventor                                   | Android Studio                                          |
| ----------------------- | -------------------------------------------------- | ------------------------------------------------------- |
| **Hình thức lập trình** | Kéo thả giao diện, lập trình bằng Block trực quan. | Viết mã nguồn bằng Java/Kotlin kết hợp XML.             |
| **Tốc độ triển khai**   | Nhanh, phù hợp làm prototype.                      | Mất thời gian cấu hình và phát triển hơn.               |
| **Thiết kế UI/UX**      | Khả năng tùy biến hạn chế.                         | Tùy biến giao diện gần như không giới hạn.              |
| **Xử lý API & Dữ liệu** | Hỗ trợ API cơ bản, khó xử lý JSON phức tạp.        | Mạnh mẽ nhờ Retrofit, Gson và hệ sinh thái Android.     |
| **Khả năng mở rộng**    | Khó quản lý khi dự án lớn.                         | Quản lý mã nguồn và debug hiệu quả.                     |
| **Kiểm soát SDK**       | Tự động xử lý tương thích.                         | Chủ động cấu hình và tối ưu cho từng phiên bản Android. |

## Kết luận

**MIT App Inventor** phù hợp để học tư duy thuật toán, thiết kế nhanh các ứng dụng mẫu và xây dựng prototype.

Tuy nhiên, đối với các ứng dụng thực tế yêu cầu giao diện chuyên nghiệp, khả năng mở rộng cao, xử lý dữ liệu ổn định và dễ bảo trì, **Android Studio** là môi trường phát triển mạnh mẽ và phù hợp hơn cho lập trình Android chuyên nghiệp.

# Một số hình ảnh về app

<img width="320" height="600" alt="Screenshot_2026-06-11-15-20-34-682_com example green_footprint" src="https://github.com/user-attachments/assets/e67c977c-77af-42ff-b9ec-6dc2f2cef78a" />

<img width="320" height="600" alt="Screenshot_2026-06-11-15-21-23-429_com example green_footprint" src="https://github.com/user-attachments/assets/3150c457-6124-4412-865d-b83bec2ecee6" />

<img width="320" height="600" alt="Screenshot_2026-06-11-15-20-50-094_com example green_footprint" src="https://github.com/user-attachments/assets/20e71185-f5d0-4e83-bf9c-2bf53fc3fad5" />

<img width="480" height="1080" alt="Screenshot_2026-06-11-15-21-01-424_com example green_footprint" src="https://github.com/user-attachments/assets/f5ac43b1-1835-4d8e-b9a6-34ff21746b95" />

<img width="480" height="1080" alt="Screenshot_2026-06-11-15-21-09-521_com example green_footprint" src="https://github.com/user-attachments/assets/4fb906bb-7494-418e-bf03-47fd51d586e0" />


