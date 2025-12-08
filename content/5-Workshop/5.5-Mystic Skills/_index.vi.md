+++
title = "Mystic Skills"
weight = 5
chapter = false
pre = "<b>5.5. </b>"
+++


### **1. Lệnh Hugo**
1.1
```bash
hugo build
```

1.2
```bash
hugo server -D
```


---
### **2. Lệnh Git**
2.1 Kiểm tra trạng thái repository
```bash
git status
```

2.2 Liệt kê các nhánh local
```bash
git branch
```

2.3 Liệt kê tất cả các nhánh (local + remote)
```bash
git branch -a
```

2.4 Chuyển sang nhánh khác
```bash
git checkout <branch-name>
```
Example: git checkout developer

2.5 Tạo và chuyển sang nhánh mới
```bash
git checkout -b <branch-name>
```
Example: git checkout -b feature/login

2.6 Pull các cập nhật mới nhất từ remote
```bash
git pull
```

2.7 Fetch các cập nhật mà không merge
```bash
git fetch
```

2.8 Thêm file vào staging
```bash
git add <file>
```
Thêm tất cả thay đổi: git add .

2.9 Commit thay đổi
```bash
git commit -m "your message"
```

2.10 Push thay đổi lên remote
```bash
git push
```
Push nhánh mới lần đầu:
```bash
git push -u origin <branch-name>
```

2.11 Xem remote repositories
```bash
git remote -v
```

2.12 Merge một nhánh vào nhánh hiện tại
```bash
git merge <branch-name>
```
Example:
```bash
git checkout main
git merge developer
```

2.13 Xóa nhánh
Xóa nhánh local:
```bash
git branch -d <branch-name>
```
Xóa nhánh remote:
```bash
git push origin --delete <branch-name>
```

2.14 Hoàn tác thay đổi
Bỏ thay đổi trong một file:
```bash
git checkout -- <file>
```
Reset tất cả về commit cuối cùng:
```bash
git reset --hard
```


**3. Lệnh PowerShell**
3.1 Tự động cập nhật _index.md cho tất cả các tuần trong một thư mục cụ thể
```bash
# Đường dẫn tới thư mục của Dương Bình Minh
$targetDir = "content/1-Worklog/1.3-DuongBinhMinh"

# Kiểm tra xem thư mục có tồn tại không
if (-not (Test-Path $targetDir)) {
    Write-Host "Lỗi: Không tìm thấy thư mục $targetDir" -ForegroundColor Red
    break
}

# Lấy danh sách các folder con
$folders = Get-ChildItem -Path $targetDir -Directory

foreach ($folder in $folders) {
    if ($folder.Name -match "Week_(\d+)") {
        $weekNum = $matches[1]
        $filePath = Join-Path $folder.FullName "_index.md"

        if (Test-Path $filePath) {
            # Đọc nội dung file
            $content = Get-Content -Path $filePath -Raw -Encoding UTF8

            # Tạo nội dung pre mới
            $newPreLine = "pre = `" <b> 1.3.$weekNum. </b> `""

            # Thay thế dòng pre cũ
            $newContent = $content -replace '(?m)^pre\s*=\s*".*"', $newPreLine

            # Ghi đè lại file
            Set-Content -Path $filePath -Value $newContent -Encoding UTF8
            
            # ĐÃ SỬA DÒNG NÀY: Dùng $($weekNum) để tránh lỗi cú pháp với dấu hai chấm
            Write-Host "Đã sửa Week $($weekNum): $newPreLine" -ForegroundColor Green
        } else {
            Write-Host "Bỏ qua $folder.Name (Không tìm thấy _index.md)" -ForegroundColor Yellow
        }
    }
}

Write-Host "Hoàn tất cập nhật!" -ForegroundColor Cyan
```

3.2 Chuẩn hóa cấu trúc thư mục, chuyển Leaf Bundles → Branch Bundles, sửa frontmatter và link nội bộ

```bash
# --- CONFIGURATION ---
$worklogPath = "content/1-Worklog"

# --- HELPER FUNCTION: Fix index.md -> _index.md ---
Function Fix-IndexFileName ($dirPath) {
    $wrongFile = Join-Path $dirPath "index.md"
    $correctFile = Join-Path $dirPath "_index.md"
    if (Test-Path $wrongFile) {
        if (-not (Test-Path $correctFile)) {
            Rename-Item -Path $wrongFile -NewName "_index.md"
            Write-Host "  [File] Fixed index.md -> _index.md" -ForegroundColor Magenta
        }
    }
}

# --- START PROCESSING ---
Write-Host "=== STARTING HUGO DATA STANDARDIZATION ===" -ForegroundColor Cyan

# 1. Get all user directories (e.g., 1.1, 1.2, 1.3...)
$userFolders = Get-ChildItem -Path $worklogPath -Directory

foreach ($userFolder in $userFolders) {
    # Check if folder matches pattern "1.x-Name"
    if ($userFolder.Name -match "^(\d+\.\d+)-") {
        $userPrefix = $matches[1] 
        Write-Host "`n--- Processing User: $($userFolder.Name) (Prefix: $userPrefix) ---" -ForegroundColor Cyan

        # STEP 1: Fix the User's main _index.md
        Fix-IndexFileName $userFolder.FullName
        
        $parentIndexFile = Join-Path $userFolder.FullName "_index.md"
        if (Test-Path $parentIndexFile) {
            $pContent = Get-Content -Path $parentIndexFile -Raw -Encoding UTF8
            
            # Fix Hugo shortcode syntax warning (< > to % %)
            # Tách chuỗi '{' + '{' để Hugo không hiểu nhầm là shortcode
            $pContent = $pContent -replace '\{\{<\s*relref', ('{' + '{% relref')
            $pContent = $pContent -replace '>\}\}\)', ('%' + '}})')

            # Repair broken links to match the current User Prefix
            $pContent = [Regex]::Replace($pContent, 'relref\s*"[^"]*?Week_(\d+)"', {
                param($m)
                $w = $m.Groups[1].Value
                return 'relref "' + "$userPrefix.$w-Week_$w" + '"'
            })

            Set-Content -Path $parentIndexFile -Value $pContent -Encoding UTF8
            Write-Host "  [Link] Updated links in parent _index.md" -ForegroundColor Green
        }

        # STEP 2: Process Sub-folders (Weeks)
        $weekFolders = Get-ChildItem -Path $userFolder.FullName -Directory | Where-Object { $_.Name -match "Week_" }

        foreach ($weekFolder in $weekFolders) {
            if ($weekFolder.Name -match "Week_(\d+)") {
                $weekNum = $matches[1]
                
                # A. Standardize Folder Name (e.g., 1.3.1-Week_1)
                $correctFolderName = "$userPrefix.$weekNum-Week_$weekNum"
                $currentFolderPath = $weekFolder.FullName

                if ($weekFolder.Name -ne $correctFolderName) {
                    try {
                        Rename-Item -Path $currentFolderPath -NewName $correctFolderName -ErrorAction Stop
                        $currentFolderPath = Join-Path $userFolder.FullName $correctFolderName
                        Write-Host "  [Folder] Renamed to: $correctFolderName" -ForegroundColor Yellow
                    } catch {
                        Write-Host "  [Error] Could not rename folder $($weekFolder.Name)" -ForegroundColor Red
                        continue
                    }
                }

                # B. Fix Bundle Type inside the Week folder
                Fix-IndexFileName $currentFolderPath

                $childIndexFile = Join-Path $currentFolderPath "_index.md"
                if (Test-Path $childIndexFile) {
                    $cContent = Get-Content -Path $childIndexFile -Raw -Encoding UTF8
                    
                    # C. Update 'pre' in Frontmatter for Sidebar Navigation
                    $newPre = "pre = `" <b> $userPrefix.$weekNum. </b> `""
                    
                    if ($cContent -match '(?m)^pre\s*=') {
                        $cContent = $cContent -replace '(?m)^pre\s*=\s*".*"', $newPre
                    } else {
                        $cContent = $cContent -replace '(?m)^weight\s*=\s*(\d+)', "weight = `$1`n$newPre"
                    }

                    Set-Content -Path $childIndexFile -Value $cContent -Encoding UTF8
                    Write-Host "  [Pre] Updated frontmatter pre to: $userPrefix.$weekNum." -ForegroundColor Gray
                }
            }
        }
    }
}

Write-Host "`n=== COMPLETED! PLEASE RUN: hugo server -D ===" -ForegroundColor Green
```

3.3 Tạo nhiều thư mục cùng lúc
```bash
$basePath = "D:\IT\AWS-FCJ\AWS-Workshop\content\5-Workshop"

$folders = @(
    "5.1-Workshop_Overview",
    "5.2-Prerequisite",
    "5.3-Deploy_Flow",
    "5.4-Clean_Up"
)

foreach ($f in $folders) {
    $fullPath = Join-Path $basePath $f
    New-Item -ItemType Directory -Path $fullPath -Force | Out-Null
    New-Item -ItemType File -Path (Join-Path $fullPath "_index.md") -Force | Out-Null
}
```






3.4 Tạo một thư mục đơn lẻ
```bash
$basePath = "D:\IT\AWS-FCJ\AWS-Workshop\content\5-Workshop"
$folderName = "5.1-Workshop_Overview"   # <-- Change name here

$fullPath = Join-Path $basePath $folderName
New-Item -ItemType Directory -Path $fullPath -Force | Out-Null
New-Item -ItemType File -Path (Join-Path $fullPath "_index.md") -Force | Out-Null
```

3.5 Hiển thị toàn bộ cấu trúc project
```bash
tree /f /a
```

3.6 Hiển thị cấu trúc một thư mục cụ thể
```bash
tree content/1-Worklog/ /F
```

3.7 Tái tạo gh-pages worktree một cách an toàn

```bash
# 0. Remove current public folder
Remove-Item -Recurse -Force .\public

# 1. Remove old gh-pages worktree (even if folder no longer exists)

git worktree remove "D:/IT/AWS-FCJ/AWS-Workshop/public/public" --force

# 2. Clean orphaned worktree metadata

git worktree prune

# 3. Verify remaining worktrees

git worktree list

# 4. Recreate gh-pages worktree at /public

git worktree add -B gh-pages public origin/gh-pages

# 5. Verify status

cd public

git status
```