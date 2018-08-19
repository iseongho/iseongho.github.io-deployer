---
title: Git Subtree - 하나의 저장소에서 여러 저장소를 관리하기
intro: 상위 저장소에서 하위 저장소들을 관리하는 방법
date: 2018-08-19 13:35:13
tags: [git, subtree]
category: git
---
Git에서 저장소를 관리하다 보면, 상위 저장소에서 다른 저장소를 Clone하게 되는 경우가 있습니다. 그 경우 상위 저장소에서 하위 저장소를 Push하면 다른 저장소들은 빈 폴더로 Push가 됩니다. 해당 문제를 해결하기 적합한 것이 Subtree라는 것입니다. 한번 공부해봅시다.

# Subtree의 기능

상위 저장소에서 하위 저장소를 가질 수 있으며, 마치 하나의 저장소처럼 관리하도록 도와주는 기능입니다.

# 장점

- 하나의 저장소에서 여러 저장소를 통합 관리가 가능합니다.
- 통합된 저장소와 원격 저장소는 별개로 변경 사항을 경우에 알맞게 Push, Pull이 가능합니다.

# 단점

- Subtree Push를 하려면 `--prefix` 옵션을 사용해야 하므로 하위 저장소의 경로를 기억하고 있어야 합니다.

# Subtree 생성하기

상위 프로젝트를 Parent, 하위 프로젝트를 Child라고 가정하여 설명하겠습니다.

## 1. 상위 저장소로 이동

모든 작업은 상위 저장소의 Root 경로에서 이루어집니다. Console에서 상위 저장소로 가줍시다.

```console
git clone Parent.git
cd Parent
```

## 2. 원격 참조 추가

Subtree에서 Add, Push, Pull, ...을 할 때 어느 저장소에서 이루어지는지 참조를 추가해야 합니다.

```console
# git remote add { Remote name } { Child repo }
git remote add remote-child Child.git
```

Remote name은 원하시는 대로 정하시면 됩니다. 참조 시에 사용될 이름입니다. 해당 글에선 remote-child라는 이름을 사용하겠습니다.

잘 추가됐는지 확인하고 싶다면 아래의 명령어를 입력해보시면 됩니다.

```console
git remote -v
```

## 3. Subtree 추가

드디어 Subtree 명령어를 사용합니다.

```console
# git subtree add --prefix { Child path } { Remote name } { Child branch }
git subtree add --prefix childs/project remote-child master
```

- Child path는 remote에 참조된 저장소가 어느 폴더에 저장될 지를 정해주는 겁니다. (어느 곳에 Clone 시킬지 라고 생각하면 편합니다.) 해당 글에선 childs 폴더 안에 project라는 폴더를 사용하겠습니다.
  - 폴더는 미리 만들어져 있으면 안 됩니다. 그 경우 `rm -rf { Child path }`로 지워주시고 다시 `git subtree add ...`를 하시길 바랍니다.

- Remote name은 remote add 할 때 지정하신 이름으로 해주시면 됩니다. 해당 글에선 위에서 정한 대로 remote-child라는 이름을 사용합니다.

- Child branch는 어느 브랜치에서 add할 지 정해주는 겁니다. 해당 글에선 무난한 master로 정하겠습니다.

# Subtree 관리(Push, Pull)

Subtree의 Child 관리에는 두 가지가 있습니다.

- Parent 저장소에서 Child를 관리한다. (Parent 입장에서 Subtree는 평범한 폴더일 뿐입니다.)
- Child의 저장소를 직접 관리한다. (이 경우 `git subtree push`, `git subtree pull`을 사용합니다.)

## Parent 저장소에서 Child를 관리하기

평소에 Push하던 과정을 진행하시면 됩니다.

```console
git add { Child path }
git commit -m { Commit message }
git push origin { branch }
```

이렇게 `git push`만 하는 경우에는 오로지 Parent 저장소에만 Child의 변경 사항이 반영됩니다.

## Child의 저장소를 직접 관리하기

Parent에서 Child 변경 사항을 Child 저장소에도 반영하기 위해선 이 기능을 사용해야 합니다. Parent 저장소에서 Child 폴더까지 Push를 완료했다는 전제하에 가능합니다.

```console
# push
git subtree push --prefix { Child path } { Remote name } { Child branch }

# pull
git subtree pull --prefix { Child path } { Remote name } { Child branch }
```