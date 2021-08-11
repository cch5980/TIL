# [GitHub] .DS_Store 파일 개념 및 삭제 방법



## 1) .DS_Store 파일

- Desktop Services Store의 약자로, 애플에서 정의한 파일 포맷이다.
- 애플의 맥 OS X 시스템이 finder로 폴더에 접근할 때 자동으로 생기는 파일이다.
- 해당 폴더에 대한 메타데이터를 저장하는 파일이다.
- 해당 디렉토리의 크기, 아이콘의 위치, 폴더의 배경에 대한 정보 등을 얻을 수 있다.
- DS_Store 파일은 프로젝트와 관련없는 파일이기 때문에, github에 올릴 때 같이 올리지 않게 gitignore 해주자!



## 2) .DS_Store 삭제 및 gitignore 방법

- 저장소 디렉토리 기준 .DS_Store 파일 제거

  - ```bash
    $ find . -name .DS_Store -print0 | xargs -0 git rm -f --ignore-unmatch
    ```

- 저장소 디렉토리에 .gitignore 파일 생성 및 .DS_Store 파일 추가

  - ```bash
    echo .DS_Store >> .gitignore
    ```

- 변경 사항을 원격 저장소에 push

  - ```bash
    $ git add .
    $ git commit -m ".DS_Store removed!"
    $ git push -u origin main
    ```