


# 백준에서 푼 문제를 노션에서 몰아보기!!  


💡 노션 무료 라이센스의 경우 데이터베이스 100개로 제한됩니다

---  
- 전체 문제 목록
![전체 문제 목록](https://i.ibb.co/Lp5Hp1C/Untitled.png)  

- 유형별로 몰아보기
![유형별로 몰아보기](https://i.ibb.co/C2r5rH2/Untitled-1.png)

- 문제상세  
![Untitled](https://i.ibb.co/hRZXbhp/Untitled-2.png)

> 저는 아래와 같은 목적으로 해당 기능을 사용합니다!!
> 
> - [ ]  개념별로 예제 정리 ( 예시:[ 응용4. 백트랙킹](https://hye12.notion.site/hye12/4-f8c94a5cba1943548323d9c874543d79)  )
> - [ ]  알고리즘 개념 정리
> - [ ]  내가 풀어본 문제 복습하기

아래 글을 참조하였습니다. 

[백준 사용하기](https://www.notion.so/bbacb8600c0940f99205bf0f8bdcb5ff) 

# Notion 설정

## 노션 API key 생성하기

[Notion – The all-in-one workspace for your notes, tasks, wikis, and databases.](https://www.notion.so/my-integrations)

위의 링크로 들어가서 통합 API 키 생성하기

![Untitled](https://i.ibb.co/vjJdjJ9/Untitled-3.png)

## Notion DB 생성

[BaekJoon 문제목록 ](https://www.notion.so/e4f1d20a108841c9b9f95a46198f33d1)

위의 데이터 베이스 복제하고 Database key 기억하기

- **Database key 찾는 법**
    
    복제한 데이터 베이스 페이지의 URL을 복제하면 
    
    > https://www.notion.so/hye12/**e4f1d20a108841c9b9f95a46198f33d1**?v=f5bcdb88956d48c5b8d17848b09a013e&pvs=4
    > 
    
    이런 URL을 얻을 수 있는데, url은 이러한 형식으로 {userid}/{id}?v={viewid} 빨간색 표시된 부분이 database id!!
    

## 데이터베이스에 노션API 연결하기

![Untitled](https://i.ibb.co/mNnTVN2/Untitled-4.png)

데이터 베이스 페이지 > 설정 > 연결 > 연결추가 > 위에서 생성한 API 연결

- 연결 방법 참조
    
    [Notion API, database Integration 연결](https://velog.io/@rmaomina/Notion-API-database-Integration)
    

# 백준허브 설정

1. 크롬 익스텐션 설치

[백준허브(BaekjoonHub)](https://chrome.google.com/webstore/detail/백준허브baekjoonhub/ccammcjdkpgjmcpijpahlehmapgmphmk?hl=ko)

1. 아래 래포지토리 fork
    1. https://github.com/mihye126/BaekjoonHub
        
        *fork가 반드시 필요한 것은 아닙니다만, 기타 스크립트를 추가할 필요가 적어집니다.*
        
2. 백준허브와 위에서 포크한 레포지토리 연결

![Untitled](https://i.ibb.co/LvpVcvN/Untitled-5.png)

# Github Action

## Github Action Secret 설정

노션 설정에서 미리 만들어둔 api key와 database id를 백준허브에 연결한 레포지토리의 변수로 만드는 작업을 해야한다.

<aside>
💡 **레포지토리 > Settings > Secrets and variables > Action > Secrets**

</aside>

위의 경로로 들어가서 아래와 같이 두개의 Secrets를 설정한다.

![Untitled](https://i.ibb.co/3Bs6QBx/Untitled-6.png)

- Github Secrets 추가 자료
    
    [Github) Github actions에서 Secrets로 환경변수 관리하기](https://velog.io/@2ast/Github-Github-actions에서-Secrets로-환경변수-관리하기)
    

## Python script

1. ReadME.md 자동업데이트 스크립트
    1. 아래의 스크립트를 `update.py` 라는 파일을 레포지토리 최상단 디렉토리에 생성하기
    
    ```python
    #!/usr/bin/env python
    
    import os
    from urllib import parse
    
    HEADER="""# 
    # 백준 & 프로그래머스 & SWEA 문제 풀이 목록
    """
    
    def main():
        content = ""
        content += HEADER
        
        directories = [];
        solveds = [];
    
        for root, dirs, files in os.walk("."):
            dirs.sort()
            if root == '.':
                for dir in ('.git', '.github'):
                    try:
                        dirs.remove(dir)
                    except ValueError:
                        pass
                continue
    
            category = os.path.basename(root)
            
            if category == 'images':
                continue
            
            directory = os.path.basename(os.path.dirname(root))
            
            if directory == '.':
                continue
                
            if directory not in directories:
                if directory in ["백준", "프로그래머스"]:
                    content += "## 📚 {}\n".format(directory)
                else:
                    content += "### 🚀 {}\n".format(directory)
                    content += "| 문제번호 | 링크 |\n"
                    content += "| ----- | ----- |\n"
                directories.append(directory)
    
            for file in files:
                if category not in solveds:
                    content += "|{}|[링크]({})|\n".format(category, parse.quote(os.path.join(root, file)))
                    solveds.append(category)
                    print("category : " + category)
    
        with open("README.md", "w") as fd:
            fd.write(content)
            
    if __name__ == "__main__":
        main()
    ```
    
    출처:[e) readme.md 파일 자동화](https://www.notion.so/e-readme-md-c01cb19f27b947c39916042ec1322846) 
    
2. 백준 문제 정보를 notion으로 Post 보내는 스크립트 생성
    
    만약, 위에서 레포지토리를 fork하였다면, 이미 필요한 스크립트는 레포지토리 안에 있으므로 설정이 필요없습니다. 아니라면, 아래의 폴더를 다운받아 레포지토리에 넣어주세요!!
    
    [notion.zip](https://github.com/mihye126/BaekjoonHub/tree/main/notion)
    

## .yml 파일 설정

Action > Publish Python Package

![Untitled](https://i.ibb.co/sFXfSYT/Untitled-7.png)

![Untitled](https://i.ibb.co/xmpf4xT/Untitled-8.png)

Editor 부분을 아래의 코드로 바꿔주세요, 제목변경은 선택사항입니다.

<aside>
💡 하단의 깃 username과 email을 반드시 자신의 것으로 바꿔 주세요!!

</aside>

```yaml
name: Update Problems # GitHub Actions 탭에서 확인할 수 있는 액션 이름

on: # jobs가 실행되어야 하는 상황 정의
  push:
    branches: [ "main" ] # main 브랜치에 push가 발생했을 때
  pull_request:

jobs: # 실제 실행될 내용
  build:
    runs-on: ubuntu-latest # 빌드 환경
    steps:
    - uses: actions/checkout@v3 # checkout
    - name: Set up Python 3.10 
      uses: actions/setup-python@v3 # setup-python
      with:
        python-version: "3.10" # 3.10버전 파이썬 사용
    - name: Install dependencies # 1) 스크립트에 필요한 dependency 설치
      run: |
        python -m pip install --upgrade pip
        pip install python-leetcode 
        pip install -r ./notion/requirements.txt
    - name: Run script
      run: python ./notion/to_notion.py
      env:
        NOTION_API_KEY: ${{ secrets.NOTION_API_KEY }}
        NOTION_DATABASE_ID: ${{ secrets.NOTION_DATABASE_ID }}
    - name: Run update.py # 2) update.py 실행
      run: |
        python update.py
    - name: Commit changes # 3) 추가된 파일 commit
      run: |
        git config --global user.name '{user name}' # 유저명
        git config --global user.email '{user email}' # 유저 이메일
        git add -A
        git commit -am "auto update README.md & Notion Database" # 커밋 메시지
    - name: Push changes # 4) 메인에 푸시
      run: |
        git push
```

## Action 권한 설정

레포지토리 Setting > Action > General > ****Workflow permissions

![Untitled](https://i.ibb.co/n80dXt3/Untitled-9.png)

**Read and write permissions 로 변경**한 뒤 저장 버튼

![Untitled](https://i.ibb.co/hWktqR7/Untitled-10.png)

# 테스트 해보기!

마지막으로 백준에서 간단한 문제를 풀어서 테스트 해보세요

# Q&A

- 백준 문제 몇개까지 넣을 수 있나요?
    
    노션 데이터 베이스 제한에 따라 다릅니다. 무료 라이센스라면 100개가 최대이므로 추가 작업이 필요합니다.
    
- 일정 티어 이상의 문제들만 커밋하고 싶어요
    
    notion 디렉토리의 to_notion.py 스크립트를 수정하시면 가능합니다.
    
    ```python
    # to_notion.py 스크립트중 디렉토리 정보 조회 부분
    for (root, directories, files) in os.walk("백준"):
            for d in directories:
                d_path = os.path.join(root, d)
    ```
    
    위의 부분에서 `os.walk("백준")` 부분을 원하는 티어에 대한 디렉토리로 변경하신다면 특정 티어의 문제들만 커밋 가능합니다. 
    
    ```yaml
    # 변경예시: 골드 티어의 문제들만 커밋하기
    for (root, directories, files) in os.walk("백준/Gold"):
            for d in directories:
                d_path = os.path.join(root, d)
    ```
    
- 프로그래머스나 SWEA 사이트는 지원 안하나요?
    
    … 백준도 한땀한땀 json을 새겨서 만든거라,, ㅜㅜ 시간나면 나머지 2개도 해볼게요…ㅎㅎ
