# Báo Cáo Bài 4: Mô Phỏng Quy Trình Hotfix & Gitflow Thực Tế

- **Thực hiện:** Học viên  
- **Thư mục bài tập:** `homework/session_05/ex4/`  
- **Mục tiêu:** Áp dụng quy trình chuẩn Gitflow để xử lý bản vá lỗi khẩn cấp (Hotfix) trên môi trường Production (`main`), phát hành thẻ phiên bản (Release Tag `v1.0.1`), đồng thời đồng bộ ngược về nhánh phát triển (`develop`) để tránh trôi lỗi.

---

## 1. Bối cảnh & Yêu cầu bài toán

- **Trạng thái hệ thống:**
  - Nhánh `main`: Đang chạy phiên bản ổn định `v1.0.0`. Sự cố lộ dữ liệu người dùng phát sinh cần xử lý gấp.
  - Nhánh `develop`: Đang chứa mã nguồn phát triển dở dang cho các tính năng mới, chưa thể deploy lên production.
- **Giải pháp theo Gitflow:**
  - Tách nhánh `hotfix/v1.0.1` từ `main` để sửa lỗi độc lập.
  - Sau khi sửa xong, hợp nhất (`merge`) vào `main` và đánh tag `v1.0.1`.
  - Hợp nhất ngược `hotfix/v1.0.1` vào `develop` để bảo đảm tính đồng nhất dữ liệu.

---

## 2. Các bước thực hiện chi tiết

### Bước 1: Khởi tạo mô hình nhánh `main` và `develop`

```bash
# 1. Tạo commit ban đầu trên main (v1.0.0)
echo "Console.WriteLine(\"App v1.0.0 - Production\");" > app.cs
git add app.cs
git commit -m "chore: release initial v1.0.0"
git tag -a v1.0.0 -m "Release version 1.0.0"

# 2. Tạo nhánh develop và giả lập tính năng đang phát triển dở
git checkout -b develop
echo "// Feature in progress" >> app.cs
git add app.cs
git commit -m "feat: ongoing work for v1.1.0"

```

### Bước 2: Tạo nhánh Hotfix từ `main` & Khắc phục sự cố

```bash
# Chuyển về main và tách nhánh hotfix
git checkout main
git checkout -b hotfix/v1.0.1

# Thực hiện sửa lỗi bảo mật khẩn cấp
echo "// Security fix: patch data leak vulnerability" >> app.cs
git add app.cs
git commit -m "fix: patch security vulnerability in user data"

```

### Bước 3: Hợp nhất Hotfix vào `main` & Đánh dấu phiên bản (`Tag`)

```bash
# Quay về main và thực hiện no-ff merge
git checkout main
git merge --no-ff hotfix/v1.0.1 -m "Merge branch 'hotfix/v1.0.1' into main"

# Gắn tag phiên bản v1.0.1
git tag -a v1.0.1 -m "Release Hotfix 1.0.1"

```

### Bước 4: Hợp nhất ngược Hotfix vào `develop` & Dọn dẹp

```bash
# Chuyển sang nhánh develop
git checkout develop

# Merge hotfix vào develop để đồng bộ mã nguồn
git merge --no-ff hotfix/v1.0.1 -m "Merge branch 'hotfix/v1.0.1' into develop"

# Xóa nhánh hotfix sau khi đã đồng bộ hoàn toàn
git branch -d hotfix/v1.0.1

```

---

## 3. Sơ đồ nhánh Gitflow (Branch Graph)

### Mô phỏng dạng ASCII Graph:

```text
*   d4e5f6a (HEAD -> develop) Merge branch 'hotfix/v1.0.1' into develop
|\  
| *   c3d4e5f (tag: v1.0.1, main) Merge branch 'hotfix/v1.0.1' into main
| |\  
| | * b2c3d4e fix: patch security vulnerability in user data
| |/  
* |   a1b2c3d feat: ongoing work for v1.1.0
|/    
* (tag: v1.0.0) chore: release initial v1.0.0

```

---

## 4. Kiểm tra kết quả

### 1. Kiểm tra danh sách nhánh và tag hiện có:

```bash
git branch -a
git tag

```

**Kết quả:**

* Nhánh `hotfix/v1.0.1` đã được xóa sạch sẽ.
* Danh sách tag hiển thị cả `v1.0.0` và `v1.0.1`.

### 2. Kiểm tra sơ đồ lịch sử commit (`git log --graph --oneline --all`):

```bash
git log --graph --oneline --all

```

**Kết quả:** Sơ đồ phân nhánh hiển thị rõ ràng luồng tách từ `main`, sửa lỗi độc lập, hợp nhất song song vào cả `main` và `develop`.

