# Github Wiki란?

`Github.com`에 저장된 모든 레포지토리는 `Wiki` 라 불리는 `Document`를 갖습니다. 위키에는 `Readme` 에 작성하기에 긴 형식의 문서를 작성할 수 있습니다. 아키텍처, 프로젝트 실행 방법, 기술 스택 등의 정보를 입력합니다. `Readme` 에서는 간단하게 내용을 요약한다면, `wiki` 에서는 추가적인 문서를 제공하도록 하며 `README` 와 마찬가지로 `markdown` 형식으로 작성할 수 있습니다.

# 왜 Github Wiki를 쓰지 ?

모두가 알고 있듯이 프로젝트에서 소스코드만큼 중요한 것이 Document입니다. Github Wiki를 통해 방대한량의 문서를 해당 리포지토리 내에 작성할 수 있습니다. 소스코드를 확인하는 원격 레포지토리에 포함되어 편하게 관리할 수 있으므로 사용하기에 유용합니다.

# Github Wiki의 특징

이러한 위키는 `Github` 에 종속적입니다. 즉, 해당 프로젝트(레포지토리)의 `git` 상태관리에 포함되지 않습니다. 로컬 레포지토리 내에서 직접 수정할 수 없다는 뜻입니다.

사실 위키는 별도의 git 레포지토리로 관리됩니다. 위키를 생성하게되면 `repository.wiki.git` 과 같이 새로운 레포지토리가 만들어지며 이를 클론하여 위키를 로컬환경에서 관리할 수 있습니다.

# Wiki 사용

위키는 기본적으로 page 단위로 관리됩니다. 모든 Wiki의 글은 위키의 사이드바 상단의 page에서 확인할 수 있습니다. 

![image](https://user-images.githubusercontent.com/49678555/147809312-3914529c-f75f-46ea-8db3-63ff54b004e4.png)

- 위키 사이드바

pages 아래에 위치한 사이드바는 직접 편집할 수 있으며 이 사이드바 역시 마크다운 형식으로 관리됩니다. 따라서 사이드바을 탭처럼 관리하기 위해서 마크다운 문법으로 링크를 걸거나, 토글 메뉴를 사용할 수 있습니다.

![image](https://user-images.githubusercontent.com/49678555/147809319-53230d0b-c910-4d02-8a19-ff1a9726b725.png)

- 작성된 하나의 위키 페이지

위키 역시 레포지토리처럼 관리되므로 위 이미지의 revision 을 클릭하여 변경 이력을 확인할 수 있습니다. 그리고 각 위키 작성 및 수정 시 eddtional message를 관리하여 커밋 메시지처럼 사용할 수 있습니다.

> reference: 
[https://docs.github.com/en/communities/documenting-your-project-with-wikis/about-wikis](https://docs.github.com/en/communities/documenting-your-project-with-wikis/about-wikis)
[https://hbase.tistory.com/223](https://hbase.tistory.com/223)
>
