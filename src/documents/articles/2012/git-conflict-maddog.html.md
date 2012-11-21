--- yaml
layout: 'article'
title: 'Git: Conflict라는 미친개는 Topic 브랜치에서 물린다.'
author: 'Changwoo Park'
date: '2012-11-25'
tags: ['Git', 'Conflict', 'Topic', 'Branch', 'Long-Running']
---

Topic 브랜치를 만들고 이슈를 구현한다. 구현하고 Long-Running 브랜치에 Merge한다. Merge는 누가해야 할까? Conflict가 나면 어떻게 할까?

![git-maddog](/articles/2012/git-conflict-maddog/maddog.jpeg)

## 선수 소개

이 글은 Long-Running 브랜치와 Topic 브랜치의 담당자가 다르다고 가정한다. 혼자 Long-Runnig 브랜치와 Topic 브랜치를 관리할 때는 그냥 편한대로 하자.

Long-Runnig 브랜치 담당, @lnyarl 님:

![lnyarl](/articles/2012/git-conflict-maddog/lnyarl.jpeg)

Long-Running 브랜치 담당은 보통 전체적인 일을 담당한다.

Topic 브랜치 담당, @lethee 님:

![lethee](/articles/2012/git-conflict-maddog/lethee.jpeg)

## Merge

어떤 이슈를 구현하는 경우에 Topic 브랜치를 하나 만들고 작업하게 되는데 그림으로 보자면 아래와 같다:

![wrong-merge-before](/articles/2012/git-conflict-maddog/wrong-merge-before.png)

@lethee님은 Topic 브랜치를 하나 만들어 이슈를 해결했다. 이걸 Long-Running 브랜치에 merge한다:

![wrong-merge-after](/articles/2012/git-conflict-maddog/wrong-merge-after.png)

**이렇게 Merge하면 안된다.** 그 이유는 Long-Running 브랜치를 @lethee님이 관리하지 않기 때문이다. Long-Running 브랜치에 Merge하면 해당 이슈가 전체를 해치지 않는지 검증해야 하고 @lethee님은 Long-Running 브랜치 담당이 아니기 때문에 검증하기도 쉽지 않을 것이다. Merge는 Long-Running 브랜치 담당인 @lnyarl님이 해야 한다.

그러니까 다음과 같은 상황에서:

![wrong-merge-before](/articles/2012/git-conflict-maddog/merge-request.png)

"Topic 브랜치를 Merge해주세요"라고 말한다. 문자를 보내거나 메일을 보내거나 직접 만난다! GitHub이라면 Pull Request를 보낸다.

그러면 Long-Running 브랜치 담당인 @lnyarl 님은 다음과 같이 Long-Running 브랜치를 Checkout한 상태에서:

![merge-before](/articles/2012/git-conflict-maddog/merge-before.png)

Topic 브랜치를 Merge한다:

![merge-after](/articles/2012/git-conflict-maddog/merge-after.png)

## Conflict라는 미친개에 물린다.

보통은 Long-Running 브랜치 담당은 좀 더 바쁘다. Topic 브랜치를 Merge할 때마다 이슈가 기존 구현을 망치지 않는지 검증해야 한다. 위에서 얘기 한 것처럼 @lethee님이 요청한 브랜치를 @lnyarl님이 Merge하다가 Conflict나면 그냥 다시 돌려보낼게 분명하다.

우선 위에서 보여준 그림을 다시보자. @lethee님은 Topic 브랜치 작업을 완료해서 다음과 같은 상황이 됐다:

![wrong-merge-before](/articles/2012/git-conflict-maddog/wrong-merge-before.png)

그럼 여기서 Long-Running 브랜치를 Topic 브랜치에 Merge한다:

![lethee-merge-after](/articles/2012/git-conflict-maddog/topic-merge-after.png)

좀 특이하다. 우리가 하려는 것은 Topic 브랜치를 Long-Running 브랜치에 Merge하려는 것인데 Long-Running 브랜치를 Topic 브랜치에 Merge했다. 전자나 후자나 결국 같은 히스토리가 되지만 의미가 약간 다르다. Conflict를 해결하면서 Long-Running 브랜치는 건드리지 않았다.

Long-Running 브랜치를 Topic 브랜치에 Merge했으니 @lethee님은 그자리에서 Topic 브랜치를 다시 검증한다. Long-Running 브랜치가 Topic 브랜치를 망치지 않는지 검증하는 거다. 지금 막 구현한 이슈를 검증하는 것 뿐이니까 비교적 쉽다.

그리고 나서 Merge를 요청한다:

![lethee-merge-request](/articles/2012/git-conflict-maddog/topic-merge-request.png)

그러면 Long-Running 브랜치 관리자인 @lnyarl님은 Fast-Forward Merge를 해서 Merge를 완성 시킨다:

![lnyarl-merge](/articles/2012/git-conflict-maddog/merge-complete.png)

물론 @lethee님이 보내준 것을 그대로 사용하지 않고 @lnyarl님이 직접 Merge하거나 다시 Merge할 수도 있다. 하지만 그림에서 처럼 Merge Commit을 남길 거면 그냥 Fast-Forward Merge를 하면 된다.

그게 아니라 히스토리를 평평하게 펴고 싶다면 요청을 반려하고 Rebase해달라고 요청한다. Long-Running 브랜치 관리자는 직접 Rebase할 수 있지만 보통 직접 Rebase하기보다 Rebase해서 달라고 반려한다.

## Rebase

[Rebase는 언제 어떻게 할까?]()에서도 말했었지만 Merge를 요청할 때 Rebase해서 요청하는 것이 좋다.

다음과 같이 Rebase해서 Merge를 요청한다:

![lethee-rebase](/articles/2012/git-conflict-maddog/rebase.png)

그러면 Long-Running 브랜치 담당인 @lnyarl는 Merge한다:

![lnyarl-rebase-merge](/articles/2012/git-conflict-maddog/rebase-merge.png)

## 배려

Long-Running 브랜치 담당은 보통 좀 더 바쁘다. 더 많은 복잡도를 해결해야 한다. 그들이 conflict라는 미친개에 물리지 않도록 배려해야 한다.

Merge는 Topic 브랜치 담당과 Long-Running 브랜치 담당의 협력으로 이루어진다. Topic 브랜치 담당은 Conflict를 해결해야 하고 Long-Running 브랜치가 Topic 브랜치를 망치지 않는지 검증할 책임이 있다. 이 작업은 Long-Running 브랜치 담당이 하는 것보다 Topic 브랜치를 개발한 사람이 하는 것이 생산적이다.

반대로 Long-Running 브랜치 담당은 Topic 브랜치가 이미 구현된 다른 기능을 해치지 않는지 확인할 필요가 있다. Long-Running 브랜치에 Merge하는 것은 단순히 코드를 합치는 게 아니다. 검증까지 해야 한다.

