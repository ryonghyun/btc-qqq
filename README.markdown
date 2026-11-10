# rules-for-iavb

데이터를 인터랙티브하게 분석하고 시각화하는 웹 프로젝트들이 함께 쓰는 Claude Code 규칙입니다. 각 프로젝트의 `.claude/rules/` 폴더에 git subtree로 넣어 씁니다.

이 파일은 확장자가 `.md`가 아니어서 Claude Code가 규칙으로 읽지 않습니다(2026-11 기준). 사람이 읽는 사용법만 적습니다.

## 새 프로젝트에 넣기

시작하기 전에 확인합니다.

- 프로젝트에 커밋이 하나 이상 있어야 합니다. `subtree add`는 지금 커밋 위에 merge 커밋을 만들기 때문에, 커밋이 없는 새 저장소에서는 실패합니다.
- 이 저장소는 비공개라서 GitHub 인증이 필요합니다. HTTPS로 받을 때 비밀번호를 물으면 개인 액세스 토큰(personal access token)을 넣습니다.

```sh
# 커밋이 없는 새 저장소에서만 실행합니다.
git commit --allow-empty -m "chore: initial commit"

git remote add rules https://github.com/ryonghyun/rules-for-iavb.git
git subtree add --prefix=.claude/rules rules main --squash -m "chore: add rules-for-iavb as subtree in .claude/rules"
```

`-m`은 merge 커밋의 메시지를 Conventional Commits 형식으로 정합니다. 함께 생기는 squash 커밋의 메시지(`Squashed '.claude/rules/' content from commit ...`)는 자동으로 붙으며 바꾸지 않습니다. `subtree pull`이 이 커밋에 기록된 `git-subtree-split` 줄로 마지막으로 받은 지점을 찾기 때문입니다.

잘 들어왔는지 확인합니다.

```sh
git log --oneline --graph -3
```

아래처럼 merge 커밋 하나와 squash 커밋 하나가 보이면 성공입니다.

```text
*   1a2b3c4 chore: add rules-for-iavb as subtree in .claude/rules
|\
| * 5d6e7f8 Squashed '.claude/rules/' content from commit 7359bb0
* 9a8b7c6 chore: initial commit
```

이 저장소의 커밋이 하나씩 그대로 보이면 `--squash`가 빠진 것입니다. 푸시하기 전이라면 `git reset --hard HEAD~1`로 subtree를 넣기 전으로 되돌린 뒤 다시 실행합니다. 이 명령은 커밋하지 않은 변경도 지우므로, `git status`로 변경이 없는지 먼저 확인합니다.

마지막으로 프로젝트의 `.claude/settings.json`에서 auto memory를 끕니다. 여러 컴퓨터를 옮겨 다니며 작업하므로 규칙은 모두 프로젝트 안에서 관리하는데, auto memory는 컴퓨터마다 `~/.claude/` 아래에 따로 저장되어 다른 컴퓨터로 따라오지 않기 때문입니다.

```json
{
  "$schema": "https://json.schemastore.org/claude-code-settings.json",
  "autoMemoryEnabled": false
}
```

```sh
git add .claude/settings.json
git commit -m "chore(claude): disable auto memory for this project"
```

다음 세션에서 `/memory`를 열어 auto memory가 꺼져 있으면 성공입니다(2026-11 기준).

## 바뀐 규칙 받기

```sh
git subtree pull --prefix=.claude/rules rules main --squash -m "chore: update rules-for-iavb subtree"
```

`--squash`는 이 저장소의 히스토리를 커밋 하나로 합쳐 받아 프로젝트의 로그를 짧게 둡니다. 처음 넣을 때 붙였으므로 받을 때도 매번 붙입니다.

## 고친 규칙 올리기

규칙 변경만 따로 커밋한 뒤 올립니다. 커밋 메시지가 그대로 이 저장소로 옮겨지기 때문입니다.

```sh
git subtree push --prefix=.claude/rules rules main
```

## 참고

- 원격 저장소 설정(`rules`)은 clone할 때 따라오지 않습니다. 새로 clone한 프로젝트에서는 `git remote add`를 한 번 실행합니다.
- 프로젝트에 `.claude/rules/` 폴더가 이미 있으면 `subtree add`가 실행되지 않습니다. 기존 규칙을 이 저장소에 옮긴 뒤 폴더를 지우는 커밋을 먼저 만듭니다.
- `subtree add`와 `subtree pull`은 추적 중인 파일에 커밋하지 않은 변경이 있으면 실행되지 않습니다. 먼저 커밋하거나 `git stash`로 치워 둡니다.
