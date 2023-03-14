---
draft: false

title: "Git과 Gitub 기초"
subtitle: ""
description: ""

date: 2023-03-10

categories: ["Git"]
tags: ["Git", "Computer Science"]
series: []

toc: 
    enable: true
    auto: false
---

<!--more-->

<br>

ㅤGit과 GitHub을 사용할 때, 알아두면 좋은 기초 개념들을 정리해보겠습니다. 

<br>

ㅤ이 글은 Reference 자료들을 참고하여 정리하였음을 먼저 밝힙니다. 혹시 내용 중 잘못된 점이나 보완할 점 있다면 댓글로 알려주시면 감사하겠습니다. 그럼 시작하겠습니다.

---

## 1 git 공간

ㅤGit에는 4가지 주요 공간이 존재합니다.

<br>

| 공간 | |
|:---:|:---|
| **Working directory** | ㅤworking directory는 우리가 작업하고 있는 프로젝트 폴더를 말합니다. 실제로 코드를 짜거나, 문서화 작업이 이루어지는 공간입니다.<br><br>ㅤworking directory는 'work tree'나 '작업 공간'이라고도 불립니다. |
| **Index** | ㅤindex는 working directory에서 local repository로 넘어가기 전에, 준비하는 공간입니다.<br><br>ㅤworking directory에서 작업한 파일이 remote repository에 저장되려면, `변경된 파일 → staging → commit`의 단계를 거치게 됩니다. 여기서, commit이 되지 않은 staging 된 파일들이 존재하는 공간이 index입니다. staging은 index에 파일 상태를 기록하는 것이라고 할 수 있습니다.<br><br>ㅤindex는 'staging area'라고도 불립니다. |
| **Local repository** | ㅤlocal repository는 index에 기록된 파일들이 저장되는 개인 전용 저장소를 말합니다. 이 개인 전용 저장소는 개인 컴퓨터에 존재합니다.<br><br>ㅤlocal repository는 '로컬 저장소'라고도 불립니다. |
| **Remote repository** | ㅤremote repository는 local repository에 기록된 파일 변경 이력이 저장되는 외부 공간을 말합니다. 전용 서버에서 관리되며, 여러 사람이 함께 공유할 수 있습니다.<br><br>ㅤremote repository는 '원격 저장소'라고도 불립니다. |

{{< image src="./Fig_01_space.png" caption="[그림 1] Git 공간" width=650px >}}

---

## 2 commit

ㅤ이번 [2 commit](#2-commit)에서 다루는 내용은 꼭 지켜야 하는 것이 아니라, '이러한 것도 있구나.' 정도로 받아들이시면 됩니다.

### 2.1 commit 전

ㅤcommit을 만들기 전에 생각해보면 좋은 질문들이 있습니다. 이 질문들에 대한 답을 바탕으로 commit 메시지를 작성하면 좋습니다.

- 변경 사항은 무엇인가요?
- 이렇게 변경한 이유는 무엇인가요?
- 변경이 필요한 이유는 무엇일까요?
- 변경 사항이 어떤 영향을 미쳤나요?

### 2.2 메시지 템플릿

ㅤconventional commits은 일관된 commit 메시지를 위한 commit 메시지 템플릿을 제안하고 있습니다.

```
<commit type>[(적용 범위 (option))]: <제목>

[본문 (option)]

[꼬리말 (option)]
```

<br>

**< commit type >**

| Type | |
|:---:|:---|
| `feat` | 기능 코드 변경 |
| `fix` | 버그 수정 |
| `docs` | 문서 작업 |
| `refactor` | 버그 수정이나 기능 변경이 아닌 코드 변경 |
| `perf` | 성능 향상과 관련된 코드 변경 |
| `test` | test 코드 변경 |
| `style` | 코드 의미에 영향이 없는 변경 (공백, 세미콜론, $\cdots$) |
| $\cdots$ | $\cdots$ |

<br>

**< 예시 >**

```text
fix: Correct minor typos in code (#23)

See the issue for details on the typos fixed.

Close issue #23
```

### 2.3 메시지 규칙

ㅤ사람들은 좋은 commit 메시지를 위한 규칙들을 제안하고 있습니다.

<br>

1. 제목은 영문 50자 이내로, 본문은 영문 72자 이내로 작성합니다.
1. 제목 첫 글자를 대문자로 시작합니다.
1. 제목 끝에 구두점(`.`)을 찍지 않습니다.
1. 제목은 명령형으로 작성합니다.
1. commit type을 적어줍니다.
1. filler word와 phrase를 사용하지 않습니다.
    - (e.g.) though, maybe, I think, kind of, ...
1. 본문이 필요하다면,
    - 제목과 본문은 한 줄 띄어 분리합니다.
    - 본문에는 무엇을, 왜 변경했는지 설명합니다.

<br>

**< 명령형 제목 >**

ㅤcommit 메시지 제목을 명령형으로 작성하는 것이 어색할 수 있습니다. 그럴 땐, `If applied, this commit will` 뒤에 제목을 배치해서, 문법이 잘 어울리는지 확인해보면 됩니다. 

ㅤ예를 들어,

- (If applied, this commit will) Refactor subsystem X for readability
- (If applied, this commit will) Update getting started documentation
- (If applied, this commit will) Release version 1.0.0

<br>

**< 명령형 제목 단어 >**

ㅤ명령형 제목의 시작으로 자주 사용되는 영어 단어들입니다.

- Add
- Fix
- Remove
- Implement
- Update
- Change
- Use
- Set
- Refactor
- Move
- Test
- Rename
- Improve

---

## 3 branch 전략

ㅤ이번 장에서는 branch(브랜치)를 어떻게 운용할 것인가에 대한 이야기를 해보겠습니다.

<br>

ㅤ유명한 브랜치 전략들은 존재하지만, 항상 정답인 전략은 없습니다. 브랜치 전략은 프로젝트와 팀 상황에 맞게 쓰기를 권장하고 있습니다. 만약 주어진 상황에 어울리는 전략이 없다면, 새로운 전략을 만드는 것도 고려해볼 수 있습니다.

<br>

ㅤ유명한 전략들을 간단히 비교하는 표입니다.

| | Git-flow | GitHub-flow | GitLab-flow |
|:---:|:---|:---|:---|
| 언제 사용? | - 정기적으로 배포하는 앱<br>- 버전 관리가 필요한 앱<br>- 장기 프로젝트 | - 상시 배포하는 앱<br>- 간단한 프로젝트 | *TBU.* |

### 3.1 Git-flow 전략

ㅤGit-flow는 5개의 브랜치를 사용합니다.

| 브랜치 | |
|:---:|:---|
| **main** | ㅤmain 브랜치는 공식 배포, 출시 이력을 관리하는 브랜치입니다. 그러므로 언제나 실행할 수 있는 상태(배포 가능한 상태)를 유지해야 합니다. |
| **develop** | ㅤdevelop 브랜치는 실행할 수 있는 상태를 만들어가는 브랜치입니다. 다음 배포할 버전을 개발하는 브랜치라고 할 수 있습니다. 평소에는 이 브랜치를 기반으로 개발을 진행합니다. |
| **feature** | ㅤfeature 브랜치는 기능 개발을 할 때 사용하는 브랜치입니다. 새로운 기능 개발이나 버그 수정이 필요할 때마다, develop 브랜치에서 분기하여 만듭니다. 개발이 완료되면, 다시 develop 브랜치에 병합합니다. 관련 개발이 모두 완료되면, 브랜치를 삭제합니다.<br><br>ㅤfeature 브랜치는 main 브랜치와 **직접** 상호 작용해서는 안 됩니다.<br><br>ㅤfeature 브랜치 이름은 `feature/<개발하는 기능 요약>` 형식을 추천합니다. (e.g., `feature/login`) |
| **release** | ㅤ이번 버전의 출시를 준비하는 브랜치입니다. 이 브랜치에서는 배포를 위한 최종적인 버그 수정, 문서 추가 등 출시와 직접적으로 관련된 작업을 진행합니다. 배포 준비가 완료되면, 이 브랜치를 **main 브랜치에 병합**하고, 버전 번호를 태그로 지정합니다. 이 브랜치가 시작된 이후, 기능 개발이 진행되었을 수 있으므로, 이 브랜치는 다시 **develop 브랜치에 병합**되어야 합니다. 관련 개발이 모두 완료되면, 브랜치를 삭제합니다.<br><br>ㅤ이 브랜치를 사용하면, 출시를 준비하는 팀과 더 먼 미래의 출시를 위한 기능 구현을 하는 팀 모두 각자의 작업을 진행할 수 있습니다.<br><br>ㅤrelease 브랜치 이름은 `release/<버전>` 형식을 추천합니다. (e.g., `release/1.3.0`) |
| **hotfix** | ㅤ출시된 버전의 신속한 버그 수정을 위한 브랜치입니다. main 브랜치에서 분기하는 유일한 브랜치입니다. main 브랜치에는 업데이트된 버전 번호를 태그로 지정합니다. 버그 수정이 완료되면, main과 develop 브랜치에 모두 병합되어야 합니다. 관련 개발이 모두 완료되면, 브랜치를 삭제합니다.<br><br>ㅤhotfix 브랜치 이름은 `hotfix/<버전>` 형식을 추천합니다. (e.g., `hotfix/1.3.1`) |

<br><br>

ㅤGit-flow의 workflow를 알아보겠습니다.

{{< image src="./Fig_02_git_flow.png" caption="[그림 2] Git-flow<br>출처: [A successful Git branching model](https://nvie.com/files/Git-branching-model.pdf)" width=650px >}}

#### 3.1.1 main 브랜치

- Github에서 repository를 만들 때, `Add a README file`에 체크하면, 자동으로 main 브랜치가 만들어지고, main 브랜치가 default 브랜치로 설정됩니다.
- `Add a README file`에 체크하지 않았다면, Github repository를 clone 받은 후, 아무 파일이나 push 해줍니다.
    ```bash
    touch README.md
    git add README.md
    git commit -m "First commit"
    git push origin main
    ```

#### 3.1.2 develop 브랜치

- main 브랜치에서 develop 브랜치를 만들어줍니다.
    ```bash
    git branch develop main 
    git push origin develop
    ```

#### 3.1.3 feature 브랜치

1. develop 브랜치에서 feature 브랜치를 만들어줍니다.
    - 브랜치 이름은 `feature/<개발하는 기능 요약>` 형식을 추천합니다.
    ```bash
    git checkout -b <feautre 브랜치 이름> develop
    git push origin <feautre 브랜치 이름>
    ```
1. 새로 만든 feature branch에서 기능 관련 개발을 합니다.
1. local repository와 remote repository의 feature 브랜치를 동기화합니다.
    ```bash
    git add <파일 이름>
    git commit -m <"commit message">
    git push origin <feautre 브랜치 이름>
    ```
1. Github에서 pull request를 만듭니다.
    - pull request 만들 때, feature 브랜치에서 develop 브랜치로 보내는 게 맞는지 다시 한번 확인합니다.
    - 마지막으로, 개발한 내용을 확인 후, feature 브랜치를 develop 브랜치에 병합시킵니다.
1. local repository와 remote repository의 develop 브랜치를 동기화합니다.
    - (4)의 결과로, remote repository의 develop 브랜치에는 feature 브랜치에서 개발한 내용이 반영됐습니다.
    - 하지만, local repository의 develop 브랜치에는 feature 브랜치 내용이 반영이 안 되어 있기 때문에, 이를 맞춰주는 작업이 필요합니다.
    ```bash
    git checkout develop
    git pull origin develop
    ```
1. feature 브랜치에서 작업할 내용이 모두 완료됐다면, local repository에서 feature 브랜치를 삭제합니다.
    ```bash
    git branch -d <feautre 브랜치 이름>
    ```

<br>

- 새로 개발하거나 수정할 기능이 있다면, [3.1.3](#313-feature-브랜치)의 내용을 반복합니다.

#### 3.1.4 release 브랜치

1. 배포할 준비가 완료됐다면, develop 브랜치에서 release 브랜치를 만들어줍니다.
    - 브랜치 이름은 `release/<버전>` 형식을 추천합니다.
    ```bash
    git checkout -b <release 브랜치 이름> develop
    git push origin <release 브랜치 이름>
    ```
1. release 브랜치에서 배포를 준비합니다.
    - 배포 준비는 버그 수정, 문서 추가 등의 작업이 이뤄집니다.
1. 배포 준비가 완료되면, main 브랜치에 배포합니다.
    ```bash
    git add <commit 할 파일 이름>
    git commit -m <"commit message">
    git tag -a <태그 이름> -m <"tag message">
    git push origin <release 브랜치 이름>
    git push origin <태그 이름>
    ```
1. Github에서 pull request를 만듭니다.
    1. release 브랜치 → main 브랜치
    2. release 브랜치 → develop 브랜치
1. local repository와 remote repository를 동기화합니다.
    ```bash
    git checkout main
    git pull origin main
    
    git checkout develop
    git pull origin develop
    ```
1. release 브랜치에서 작업할 내용이 모두 완료됐다면, local repository에서 relase 브랜치를 삭제합니다.
    ```bash
    git branch -d <release 브랜치 이름>
    ```

#### 3.1.5 hotfix 브랜치

1. 배포한 버전에 버그가 있다면, main 브랜치에서 hotfix 브랜치를 만들어줍니다.
    - 브랜치 이름은 `hotfix/<버전>` 형식을 추천합니다.
    ```bash
    git checkout -b <hotfix 브랜치 이름> main
    git push origin <hotfix 브랜치 이름>
    ```
1. hotfix 브랜치에서 버그를 수정합니다.
1. 버그 수정이 완료되면, hotfix 브랜치를 main 브랜치에 병합합니다.
    ```bash
    git add <commit 할 파일 이름>
    git commit -m <"commit message">
    git tag -a <태그 이름> -m <"tag message">
    git push origin <hotfix 브랜치 이름>
    git push origin <태그 이름>
    ```
1. Github에서 pull request를 만듭니다.
    1. hotfix 브랜치 → main 브랜치
    2. hotfix 브랜치 → develop 브랜치
1. local repository와 remote repository 동기화
    
    ```bash
    git checkout main
    git pull origin main
    
    git checkout develop
    git pull origin develop
    ```
1. hotfix 브랜치에서 작업할 내용이 모두 완료됐다면, local repository에서 hotfix 브랜치를 삭제합니다.
    ```bash
    git branch -d <hotfix 브랜치 이름>
    ```

### 3.2 GitHub-flow

ㅤGit-flow가 가지는 큰 문제는 너무 복잡하다는 것입니다. GitHub-flow는 Git-flow가 가진 복잡성을 전부 제거한 브랜치 전략입니다.

ㅤ[3.1 Git-flow 전략](#31-git-flow-전략)의 workflow에서도 Pull Request(PR)을 사용했지만, 사실 PR을 사용하지 않아도 됩니다. PR은 Git의 특징이 아니라, GitHub이 만든 것입니다. [3.1 Git-flow](#31-git-flow-전략)에서는 PR이 선택 사항이었지만, [3.2 GitHub-flow](#32-github-flow)에서는 PR을 꼭 사용하라고 얘기합니다.

<br>

ㅤGitHub-flow는 main 브랜치만 정의하고 있습니다.

| 브랜치 | |
|:---:|:---|
| **main** | ㅤmain 브랜치는 공식 배포, 출시 이력을 관리하는 브랜치입니다. 그러므로 언제나 실행할 수 있는 상태(배포 가능한 상태)를 유지해야 합니다. |
| **OOO** | ㅤmain 브랜치 이외의 브랜치는 정의하지 않고, 개발자의 재량에 맡깁니다. 이 브랜치에서 작업이 완료되었다면, main 브랜치에 **pull request**를 만들어서 병합합니다. |

<br><br>

{{< image src="./Fig_03_github_flow.png" caption="[그림 3] GitHub-flow" width=650px >}}

### 3.3 GitLab-flow

*TBU.*

---

## 4 Reference

- [Git foundation and introduction](https://programmer.group/enterprise-level-automation-code-release-git-foundation-and-introduction.html)

<br><br><br><br><br><br><br><br>
