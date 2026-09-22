# LAB-1-SEG-Group-4
# Focused Web Crawler for Technology News

## 1. Selected Topic and Domains
- **Topic:** Technology
- **Selected Domains:**
  - `theverge.com` (The Verge)
  - `wired.com` (WIRED)
  - `techcrunch.com` (TechCrunch)

## 2. Seed URLs
- `https://www.theverge.com/`
- `https://www.wired.com/`
- `https://techcrunch.com/`

## 3. Crawling Configuration
- **Max Depth:** 3
- **Max Pages:** 40 pages per domain
- **Request Timeout:** 10 seconds
- **Crawl Delay (Politeness):** 1.0 second
- **User-Agent:** Custom standard browser User-Agent header

## 4. Crawling Strategy
- **BFS Traversal:** Thuật toán Breadth-First Search được triển khai thông qua cấu trúc dữ liệu hàng đợi hai đầu (`collections.deque`). Cơ chế FIFO (First-In, First-Out) đảm bảo hệ thống thu thập toàn diện các liên kết cùng cấp độ sâu trước khi đào sâu vào các tầng tiếp theo.
- **URL Frontier:** Frontier duy trì hàng đợi `queue` lưu trữ cặp giá trị `(url, depth)` kết hợp cùng tập `visited` (`set`) nhằm quản lý trạng thái, ngăn ngừa các chu trình vòng lặp (crawling loops) và bỏ qua các đường dẫn vượt quá `MAX_DEPTH`.

## 5. URL Filtering & Content Rules
- **Protocol & Domain:** Chỉ chấp nhận giao thức `http` và `https`; tên miền phải thuộc danh sách `ALLOWED_DOMAINS`.
- **Static Assets:** Bỏ qua các tài nguyên file tĩnh (`.jpg`, `.png`, `.css`, `.js`, `.pdf`, `.mp4`,...).
- **Non-HTTP Schemes:** Loại bỏ các liên kết nội bộ hoặc scheme không tải về được (`mailto:`, `javascript:`, `tel:`, `#`).
- **Domain-Specific Path Filtering:** Loại bỏ các trang đăng nhập, tài khoản, giỏ hàng hoặc các chuyên mục phi công nghệ (`/entertainment`, `/lifestyle`, `/auth`, `/checkout`, `/privacy`,...).
- **Content-Based Focused Filtering:** Trang web sau khi bóc tách phải thỏa mãn chứa các từ khóa trọng tâm (`ai`, `software`, `hardware`, `robotics`, `cloud`, `startup`,...) để loại bỏ nội dung rác trước khi lưu trữ.
- **Deduplication:** Sử dụng hàm băm mật mã `SHA-256` trên toàn bộ plain text (sau khi khử nhiễu thẻ `nav`, `script`, `footer`, `style`) để phát hiện và bỏ qua các trang exact-duplicate.

## 6. Database Design (SQLite)
Hệ thống sử dụng cơ sở dữ liệu `crawler.db` gồm 2 bảng quan hệ:
- **`pages`**: Lưu trữ tài liệu crawl hợp lệ.
  - `id`: INTEGER PRIMARY KEY AUTOINCREMENT
  - `url`: TEXT UNIQUE
  - `domain`: TEXT
  - `title`: TEXT
  - `content`: TEXT (Văn bản thuần đã khử nhiễu)
  - `depth`: INTEGER
  - `status_code`: INTEGER
  - `crawled_at`: TEXT (ISO Timestamp)
- **`links`**: Lưu trữ đồ thị liên kết phục vụ Link Analysis / PageRank.
  - `id`: INTEGER PRIMARY KEY AUTOINCREMENT
  - `source_url`: TEXT
  - `target_url`: TEXT

## 7. Crawling Statistics (Tổng hợp từ quá trình chạy)

### The Verge (`crawler.db`)
- **Pages Crawled:** 40
- **Unique URLs Discovered:** 1,443
- **Skipped URLs:** 1,112
- **Failed Requests:** 0
- **Depth Breakdown:**
  - Depth 0: 1 page
  - Depth 1: 39 pages
- **HTTP Distribution:** HTTP 200: 40 pages stored
- **Total Links Saved:** 1,442 links

### WIRED (`crawler_wired.db`)
- **Pages Crawled:** 40
- **Unique URLs Discovered:** 1,893
- **Skipped URLs:** 1,157
- **Failed Requests:** 0
- **Depth Breakdown:**
  - Depth 0: 1 page
  - Depth 1: 39 pages
- **HTTP Distribution:** HTTP 200: 40 pages stored
- **Total Links Saved:** 1,892 links

### TechCrunch (`crawler_techcrunch.db`)
- **Pages Crawled:** 40
- **Unique URLs Discovered:** 4,021
- **Skipped URLs:** 3,226
- **Failed Requests:** 0
- **Depth Breakdown:**
  - Depth 0: 1 page
  - Depth 1: 39 pages
- **HTTP Distribution:** HTTP 200: 40 pages stored
- **Total Links Saved:** 9,406 links
