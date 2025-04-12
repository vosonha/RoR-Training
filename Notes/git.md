## Khái niệm của Git

- Git là một hệ thống quản lý phiên bản phân tán (distributed version control system) => mỗi người có 1 bản sao của repository trên máy tính của mình.
- Git cho phép nhiều người làm việc trên cùng một dự án mà không cần phải kết nối với nhau liên tục.
- Git cho phép theo dõi lịch sử thay đổi của mã nguồn, giúp dễ dàng quay lại các phiên bản trước đó nếu cần thiết.

## Git Workflow

- **Working directory**: Nơi chứa mã nguồn của dự án, nơi bạn làm việc trực tiếp. Đây là nơi bạn thực hiện các thay đổi trên mã nguồn.
- **Staging area**: Là khu vực trung gian nơi chứa các thay đổi đã được chuẩn bị để commit. Bạn có thể thêm hoặc xóa các thay đổi trong staging area trước khi thực hiện commit. Điều này giúp kiểm soát chính xác những gì sẽ được lưu vào lịch sử phiên bản.
- **Repository**: Là nơi lưu trữ toàn bộ lịch sử thay đổi của mã nguồn, bao gồm tất cả các commit đã được thực hiện. Repository giúp theo dõi và quản lý các phiên bản khác nhau của dự án.
- **Remote repository**: Là repository được lưu trữ trên server, cho phép chia sẻ mã nguồn với nhiều người khác. Mỗi người có thể clone (sao chép) remote repository về máy tính cá nhân để làm việc và đồng bộ hóa các thay đổi với remote repository.

![Git Space](https://github.com/vosonha/RoR-Training/blob/main/lib/images/git_space.png)

### Git Workflow Steps

1. **Modify files**: Thực hiện thay đổi trong working directory.
2. **Stage changes**: Sử dụng `git add <file>` hoặc `git add .` để thêm các thay đổi vào staging area.
3. **Commit changes**: Sử dụng `git commit -m "<message>"` để lưu các thay đổi vào repository.
4. **Push changes**: Sử dụng `git push` để đồng bộ hóa các thay đổi với remote repository.
5. **Pull changes**: Sử dụng `git pull` để lấy các thay đổi mới nhất từ remote repository về local repository.

### Notes

- Luôn kiểm tra trạng thái của repository bằng `git status` trước khi thực hiện commit hoặc push.
- Sử dụng `git log` để xem lịch sử commit và kiểm tra các thay đổi đã được lưu.
- Khi làm việc với nhiều người, hãy thường xuyên pull code để tránh conflict.
- Nếu xảy ra conflict, hãy giải quyết trước khi tiếp tục làm việc.

### Resolve conflict (conflict xảy ra khi merge/rebase mà có code trong 1 file khác version):

- Lựa chọn:

```bash
  - Bỏ code mình (1)
  - Bỏ code người ta (2)
  - Giữ cả 2 (có thể modify) (3)
```

=> Đọc code tự tin thì chọn (1) or (2) or (3). Ko tự tin thì kiếm người tạo ra commit conflict với mình để discuss và giải quyết.

- Giải quyết:

```bash
  - Sửa file(s) bị conflict
  - git add <file(s)>
  - git commit -m <message>
  - git push
```

## Commands

### Clone repo

- Generate ssh key: https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

- Add public key: https://github.com/settings/keys

- Config file `~/.ssh/config`:

```
Host github_havs => alias, đặt tùy thích, đặt gì thì khi clone code phải sử dụng như vậy
  HostName github.com => domain thật sự của git server sử dụng
  User git => mặc định, ko chỉnh sửa
  IdentityFile ~/.ssh/havs/id_rsa_havs => file private key pair với public key đã đăng ký
```

- Clone repo: `git clone <git-url>`

```bash
git clone git@github_havs:vosonha/RoR-Training.git
```

### Configure multiple sss keys

- Generate keys => should create sub folder for each key

- Add public keys to github accounts

- Config file `~/.ssh/config`

### Working on branch

- View local branches and current branch: `git branch`

- Create new branch: `git checkout -b <branch name>`

- Push code:

  - `git add <files>` or `git add .`

  - `git commit -m '<your message>'`

  - `git push <remote name> <local branch>:<remote branch>`

    - `git push`: push to upstream

    - `git push origin`: push to remote branch on remote origin

    - `git push origin feature2`: push local branch feature2 to remote branch feature2 on remote origin

    - `git push origin feature1:feature2`: push local branch feature1 to remote branch feature2 on remote origin => ko khuyến khích

    - `git push origin :feature1` => delete remote branch feature1 on remote origin

- Pull code:

  - `git pull`

  - `git fetch` -> `git merge <branch name>` or `git rebase <branch name>`

    - git rebase: sử dụng trên nhánh private (làm 1 mình). Không sử dụng trên nhánh làm chung or nhánh làm một mình bà rebase báo conflict 2 lần.

- Differences Between Git Merge and Rebase: https://blog.git-init.com/differences-between-git-merge-and-rebase-and-why-you-should-care/

### Common commands

- `git stash`: tạm thời bỏ những thay đổi lên stash để chuyển nhánh.

  - `git stash list`: view stashed list
  - `git stash apply`: apply thay đổi, vẫn giữ lại code changes trên stash
  - `git stash pop`: apply and pop code change trên stash
    https://git-scm.com/docs/git-stash#_description

- `git pull` = `git fetch` + `git merge`

- `git checkout`:

  - Chuyển nhánh: `git checkout <branch name>`

  - Chuyển version file: `git checkout <commit id>|<branch name> <file name>`

  - Tạo nhánh mới: `git checkout -b <new branch name>`

- `git status`: provide info: current branch, file untracked, file modify, file ready to commit, conflict.

- `git reset`:

  - `--mix` (default): bỏ lịch sử, giữ code, ko add changes vào index

  - `--soft`: bỏ lịch sử, giữ code

  - `--hard`: bỏ code, bỏ lịch sử

- `git config`

  - `git config --local user.name <name>`

  - `git config --global user.email <email>`

- `git remote`
  - `git remote -v`: view remote list
  - `git remote remove <remote name>`: remove remote
  - `git remote add <remote name> git@alias_name:git_repo_path`: add remote

## GUI tool

- Git cola:

  - view status of branch + support full commands (commit, push, pull...)

  - review change trước khi commit

  - commit 1 phần file hay revert change dễ dàng

- gitk: view history

- Git cheat sheet: https://education.github.com/git-cheat-sheet-education.pdf
