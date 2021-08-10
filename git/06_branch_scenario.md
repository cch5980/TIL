## 준비

> 반드시 Root commit이 있는 상태에서 브랜치를 조작해야 합니다.

* 폴더를 만들고, README.md 파일을 만든 후 커밋을 해주세요.

### 상황 1. fast-foward

> fast-foward는 feature 브랜치 생성된 이후 master 브랜치에 변경 사항이 없는 상황

1. feature/index branch 생성 및 이동

   ```bash
   $ git branch feature/index
   $ git checkout feature/index
   (feature/index) $
   ```

2. 작업 완료 후 commit

   ```bash
   $ touch index.html # index.html
   $ git add . 
   $ git commit -m 'Complete index page'
   [feature/index 552a702] Complete index page
    1 file changed, 0 insertions(+), 0 deletions(-)
    create mode 100644 index.html
   $ git log --oneline
   552a702 (HEAD -> feature/index) Complete index page
   a7f5099 (master) Add README
   ```


3. master 이동

   ```bash
   (feature/index) $ git checkout master
   Switched to branch 'master'
   ```


4. master에 병합

   ```bash
   $ git log --oneline
   a7f5099 (HEAD -> master) Add README
   $ git merge feature/index
   Updating a7f5099..552a702
   # fast-forward
   Fast-forward
    index.html | 0
    1 file changed, 0 insertions(+), 0 deletions(-)
    create mode 100644 index.html
   ```


5. 결과 -> fast-foward (단순히 HEAD를 이동)

   ```bash
   $ git log --oneline
   552a702 (HEAD -> master, feature/index) Complete index page
   a7f5099 Add README
   ```

6. branch 삭제

   ```bash
   $ git branch -d feature/index
   Deleted branch feature/index (was 552a702)
   ```

---

### 상황 2. merge commit

> 서로 다른 이력(commit)을 병합(merge)하는 과정에서 다른 파일이 수정되어 있는 상황
>
> git이 auto merging을 진행하고, commit이 발생된다.

1. feature/style branch 생성 및 이동

   ```bash
   $ git checkout -b feature/style
   Switched to a new branch 'feature/style'
   (feature/style) $
   ```

2. 작업 완료 후 commit

   ```bash
   $ touch style.css
   $ git add .
   $ git commit -m 'Complete style'
   $ git log --oneline
   420c551 (HEAD -> feature/style) Complete style
   552a702 (master) Complete index page
   a7f5099 Add README
   ```

3. master 이동

   ```bash
   $ git checkout master
   Switched to branch 'master'
   ```

4. *master에 추가 commit 발생시키기!!*

   * **다른 파일을 수정 혹은 생성하세요!**

   ```bash
   $ touch hotfix.html
   $ git add .
   $ git commit -m 'Hotfix'
   $ git log --oneline
   565e160 (HEAD -> master) Hotfix!
   552a702 Complete index page
   a7f5099 Add README
   ```

5. master에 병합

   ```bash
   $ git merge feature/style
   ```

6. 결과 -> 자동으로 *merge commit 발생*

   * vim 편집기 화면이 나타납니다.

      ![image-20210604144000214](md-images/image-20210604144000214.png)

   * 자동으로 작성된 커밋 메시지를 확인하고, `esc`를 누른 후 `:wq`를 입력하고 엔터를 눌러서 저장 및 종료를 합니다.
      * `w` : write
      * `q` : quit
      
   * 커밋  확인 해봅시다.

   * 혹은 visual studio code 라면, 창을 종료하시고

      ![image-20210604143933603](md-images/image-20210604143933603.png)

7. 그래프 확인하기

   ```bash
   $ git log --graph --oneline
   *   33689bb (HEAD -> master) Merge branch 'feature/style'
   |\
   | * 420c551 (feature/style) Complete style
   * | 565e160 Hotfix!
   |/
   * 552a702 Complete index page
   * a7f5099 Add README
   ```

8. branch 삭제

   ```bash
   $ git branch -d feature/style
   Deleted branch feature/style (was 420c551).
   ```
   
   

---

### 상황 3. merge commit 충돌

> 서로 다른 이력(commit)을 병합(merge)하는 과정에서 동일 파일이 수정되어 있는 상황
>
> git이 auto merging을 하지 못하고, 해당 파일의 위치에 라벨링을 해준다.
>
> 원하는 형태의 코드로 직접 수정을 하고 merge commit을 발생 시켜야 한다.

1. feature/about branch 생성 및 이동

   ```bash
   $ git checkout -b feature/about
   Switched to a new branch 'feature/about'
   ```

2. 작업 완료 후 commit *추가적으로 README.md에 내용을 추가해주세요*

   ```bash
   $ touch about.html
   # README 열어서 수정하고 저장!
   $ git status
   # README 고치고 추가 파일도 있고!!!!!
   On branch feature/about
   Changes not staged for commit:
     (use "git add <file>..." to update what will be committed)
     (use "git restore <file>..." to discard changes in working directory)
           modified:   README.md
   
   Untracked files:
     (use "git add <file>..." to include in what will be committed)
           about.html
   
   no changes added to commit (use "git add" and/or "git commit -a")
   
   ```
   
   ```bash
   $ git add .
   $ git commit -m 'Update about and README'
   $ git log --oneline
   3215402 (HEAD -> feature/about) Update about and README
   33689bb (master) Merge branch 'feature/style'
   565e160 Hotfix!
   420c551 Complete style
   552a702 Complete index page
   a7f5099 Add README
   
   ```


3. master 이동

   ```bash
   $ git checkout master
   ```
   
   


4. *master에 추가 commit 발생시키기!!*

   * **동일 파일을 수정 혹은 생성하세요! (README에 내용을 아까와 다르게 작성해주세요) **

   ```bash
   $ git status
   On branch master
   Changes not staged for commit:
     (use "git add <file>..." to update what will be committed)
     (use "git restore <file>..." to discard changes in working directory)
           modified:   README.md
   
   no changes added to commit (use "git add" and/or "git commit -a")
   
   ```

   ```bash
   $ git add .
   $ git commit -m 'Update README'
   $ git log --oneline
   792a392 (HEAD -> master) Update README
   33689bb Merge branch 'feature/style'
   565e160 Hotfix!
   420c551 Complete style
   552a702 Complete index page
   a7f5099 Add README
   
   ```

   

5. master에 병합

   ```bash
   $ git merge feature/about
   Auto-merging README.md
   # Merge conflict => README.md
   CONFLICT (content): Merge conflict in README.md
   # conflict를 고치고 커밋을 해라..!
   Automatic merge failed; fix conflicts and then commit the result.
   (master | MERGING) $
   ```
   
   


6. 결과 -> *merge conflict발생*

   ```bash
   $ git status
   On branch master
   You have unmerged paths.
     (fix conflicts and run "git commit")
     (use "git merge --abort" to abort the merge)
   
   Changes to be committed:
           new file:   about.html
   
   Unmerged paths:
     (use "git add <file>..." to mark resolution)
           both modified:   README.md
   
   ```


7. 충돌 확인 및 해결

   ```bash
   >>>>>>>>>>>> HEAD
   r12121
   ===============
   asdsf
   <<<<<<<<<<< feature/about
   ```
   
   ```bash
   $ git status
   On branch master
   All conflicts fixed but you are still merging.
     (use "git commit" to conclude merge)
   
   Changes to be committed:
           modified:   README.md
           new file:   about.html
   
   ```


8. merge commit 진행

    ```bash
    $ git commit
    ```

   * vim 편집기 화면이 나타납니다.
   
   * 자동으로 작성된 커밋 메시지를 확인하고, `esc`를 누른 후 `:wq`를 입력하여 저장 및 종료를 합니다.
      * `w` : write
      * `q` : quit
      
   * 커밋이  확인 해봅시다.
   
9. 그래프 확인하기

    ```bash
    $ git log --oneline --graph
    *   83b7fbd (HEAD -> master) Merge branch 'feature/about'
    |\
    | * 3215402 (feature/about) Update about and README
    * | 792a392 Update README
    |/
    *   33689bb Merge branch 'feature/style'
    |\
    | * 420c551 Complete style
    * | 565e160 Hotfix!
    |/
    * 552a702 Complete index page
    * a7f5099 Add README
    
    ```


10. branch 삭제

    ```bash
    $ git branch -d feature/about
    ```
    
    
