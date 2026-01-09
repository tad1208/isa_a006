# Project Context Guide (A006)

> [!NOTE]
> File này được sử dụng để AI và Developer nắm bắt nhanh ngữ cảnh dự án khi bắt đầu phiên làm việc.

## 1. Project Overview (Tổng quan dự án)
*   **Tên dự án:** A006
*   **Mục tiêu:** Phát triển hệ thống tự động hóa gồm Robot và App.
*   **Thành phần chính:**
    *   **2 UiPath Robots:** Cần tuân thủ Chile Framework.
    *   **1 Power App:** Ứng dụng tương tác người dùng.

## 2. Operational Rules (Quy tắc hoạt động)
*   **Ngôn ngữ giao tiếp:** Tiếng Việt (ưu tiên).
*   **Ngôn ngữ Coding:** Tiếng Anh (Tên biến, comment, commit message...).
*   **Vai trò của AI:**
    *   Hỗ trợ code, debug, viết tài liệu.
    *   **BẮT BUỘC** tuân thủ cấu trúc thư mục và coding convention đã định.
    *   Luôn kiểm tra `chile_framework/` trước khi đề xuất giải pháp kiến trúc cho Robot.

## 3. Architecture & Standards (Kiến trúc & Tiêu chuẩn)
*   **Framework:** Chile Framework.
    *   Tài liệu & Source: Xem trong thư mục `06_KNOWLEDGE_BASE/frameworks/chile_framework/`.
    *   **Lưu ý:** Có 3 file .md mô tả chi tiết và 1 file coding convention trong đó.
*   **Design:** Xem thư mục `02_SDD/`.

## 4. Project Structure (Cấu trúc dự án)

Dự án được tổ chức theo workflow tự nhiên từ lập kế hoạch → thiết kế → phát triển → triển khai → kiểm thử:

### 📋 01_PDD (Product Design Document)
Tài liệu định nghĩa sản phẩm và phân tích yêu cầu.
*   **`raw_materials/`**: Tài liệu gốc từ stakeholder, requirements, sample files. (AI không cần đọc folder này trừ khi có yêu cầu đặc biệt)
*   **`analysis/`**: Phân tích yêu cầu, đánh giá tiêu chí, gap analysis.
*   **`decisions/`**: Các quyết định thiết kế quan trọng (ADR - Architecture Decision Records).

### 🏗️ 02_SDD (System Design Document)
Tài liệu thiết kế hệ thống chi tiết.
*   **`architecture/`**: Sơ đồ kiến trúc tổng thể, component design.
*   **`a006_app/`**: Thiết kế riêng cho Power App.
*   **`diagrams/`**: Các loại diagram (flowchart, sequence, ERD...).

### 💻 03_DEV (Development Resources)
Tài nguyên hỗ trợ phát triển.
*   **`guide/`**: Hướng dẫn setup, deploy, export/import.
*   **`a006_app/`**: Dev docs cho Power App (SharePoint lists schema, workflows...).
*   **`a006_robot1/`**: Dev docs cho UiPath Robot 1.
*   **`test/`**: Test scripts, sample data cho dev.

### 🚀 04_IMPLEMENTATION
Source code thực thi (chưa sử dụng - dành cho tương lai).
*   **`robot_1/`**: UiPath Robot 1 code.
*   **`robot_2/`**: UiPath Robot 2 code.
*   **`power_app/`**: Power App source (exported YAML format).

### 🧪 05_TESTING
Tài liệu và dữ liệu kiểm thử (chưa sử dụng - dành cho tương lai).
*   **`test_plans/`**: Kế hoạch test tổng thể.
*   **`test_cases/`**: Chi tiết test cases.
*   **`test_data/`**: Sample data cho testing.
*   **`test_results/`**: Kết quả test và bug reports.

### 📚 06_KNOWLEDGE_BASE
Kiến thức tham khảo và framework.
*   **`frameworks/chile_framework/`**: Chile Framework documentation cho UiPath.
*   **`references/`**: Tài liệu tham khảo kỹ thuật.
*   **`faqs/`**: Các vấn đề thường gặp và giải pháp.

### 📦 07_CODES (External Reference)
Nơi chứa Source Code tham khảo được copy từ máy local vào.
*   **Mục đích**: Để AI đọc hiểu project structure và logic cũ.
*   **QUY TẮC BẤT DI BẤT DỊCH**: Folder này là **READ-ONLY**.
    *   AI **TUYỆT ĐỐI KHÔNG** được chỉnh sửa, ghi đè bất kỳ file nào trong folder này.
    *   Chỉ được phép đọc nội dung để trả lời câu hỏi hoặc suggestion.


