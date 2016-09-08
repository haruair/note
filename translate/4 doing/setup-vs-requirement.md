---
title: setup.py vs requirements.txt
date: 2013-07-22
post_url: https://caremad.io/2013/07/setup-vs-requirement/
repo_url: https://github.com/dstufft/caremad.io/blob/master/content/post/setup-vs-requirement.md
---

There's a lot of misunderstanding between ``setup.py`` and ``requirements.txt``
and their roles. A lot of people have felt they are duplicated information and
have even created [tools][1] to handle this "duplication".

``setup.py``와 ``requirements.txt``의 역할에 대한 오해가 많다. 사람들 대다수가 이 둘을
중복된 정보를 제공하고 있다고 생각하고 있고 이 "중복"을 다루기 위한 [도구][1]를 만들기까지 했다.

## Python Libraries

## 파이썬 라이브러리

A Python library in this context is something that has been developed and
released for others to use. You can find a number of them on [PyPI][2] that others
have made available. A library has a number of pieces of metadata that need to
be provided in order to successfully distribute it. These are things such as
the Name, Version, Dependencies, etc. The ``setup.py`` file gives you the
ability to specify this metadata like:

이 글에서 이야기하는 파이썬 라이브러리는 타인이 사용할 수 있도록 개발하고 릴리스하는 것을
의미한다. 다른 사람들이 만든 수많은 라이브러리는 [PyPI][2]에서 찾을 수 있을 것이다. 각각의 라이브러리가
제공될 때 문제 없이 배포될 수 있도록 메타데이터를 포함하고 있다. 이 메타데이터는 명칭, 버전, 의존성 등을
포함한다. 라이브러리에 이와 같은 메타데이터를 정의할 수 있도록 ``setup.py`` 파일에서 다음과 같은 기능을
제공한다.

```python
from setuptools import setup

setup(
    name="MyLibrary",
    version="1.0",
    install_requires=[
        "requests",
        "bcrypt",
    ],
    # ...
)
```

This is simple enough, you have the required pieces of metadata declared.
However something you don't see is a specification as to where you'll be
getting those dependencies from. There's no url or filesystem where you can
fetch these dependencies from, it's just "requests" and "bcrypt". This is
important and for lack of a better term I call these "abstract dependencies".
They are dependencies which exist only as a name and an optional version
specifier. Think of it like duck typing your dependencies, you don't care what
specific "requests" you get as long as it looks like "requests".

이 방식은 매우 단순해서 필요한 메타 데이터를 정의하기에 부족하지 않다. 하지만 이 명세에서는
이 의존성을 어디에서 가져와 해결해야 하는지에 대해서는 적혀있지 않다. 단순히 "requests", "bcrypt"라고만
적혀있고 URL도, 파일 경로도 존재하지 않아서 어디서 의존 라이브러리를 가져와야 하는지 불분명하다.
이 특징은 중요하며 이 부분에 대한 특별한 용어가 있는 것은 아니지만 "추상 의존성(abstract dependencies)"라고
이야기할 수 있다. 이 의존성에는 의존 라이브러리의 명칭만 존재하며 선택적으로 버전 지정도 할 수 있다.
의존성 라이브러리를 덕 타이핑(duck typing)한다고 생각해보자. 이런 맥락에서 생각하면 특정
라이브러리인 "requests"가 필요한 것이 아니라 "requests"처럼 보이는 라이브러리만 있으면 된다는 뜻이다.


## Python Applications

## 파이썬 어플리케이션

Here when I speak of a Python application I'm going to typically be speaking
about something that you specifically deploy. It may or may not exist on PyPI
but it's something that likely does not have much in the way of reusability. An
application that does exist on PyPI typically requires a deploy specific
configuration file and this section deals with the "deploy specific" side of a
Python application.

여기서 이야기하는 파이썬 어플리케이션은 일반적으로 서버에 배포하게 되는 부분을 뜻한다.
이 코드는 PyPI에 존재할 수도 있고 존재하지 않을 수도 있다. 하지만 이 코드에서 재사용을
위해 작성한 부분은 그리 많지 않을 것이다. PyPI에 존재하는 어플리케이션은 일반적으로
배포를 위한 특정 설정 파일이 필요하다. 여기서는 "배포에 초점을 둔" 파이썬 어플리케이션을
중심으로 다룬다.


An application typically has a set of dependencies, often times even a very
complex set of dependencies, that it has been tested against. Being a specific
instance that has been deployed, it typically does not have a name, nor any of
the other packaging related metadata. This is reflected in the abilities of a
[pip][3] requirements file. A typical requirements file might look something
like:

어플리케이션은 일반적으로 의존성 라이브러리를 갖고 있는 데다 대부분 복잡하게 많은
의존성을 갖는 경우가 많고 이런 의존성을 검사할 필요도 없었다. 이렇게 배포(deploy)되는
특정 인스턴스를 위한 코드는 이름을 갖고 있지 않거나 다른 패키지와의 관계를 메타데이터로
갖지 않는 경우가 일반적이다. 이런 경우에는 [pip][3]의 requirements 파일 기능으로 환경을
저장할 수 있다. requirements 파일 대부분은 다음과 같은 모습을 하고 있다.

```ini
# This is an implicit value, here for clarity
# 이 플래그는 기본 값으로 설정된 부분이지만 관계를 명확하게 보여주기 위해 추가함
--index-url https://pypi.python.org/simple/

MyPackage==1.0
requests==1.2.0
bcrypt==1.0.2
```

Here you have each dependency shown along with an exact version specifier.
While a library tends to want to have wide open ended version specifiers an
application wants very specific dependencies. It may not have mattered up
front what version of requests was installed but you want the same version
to install in production as you developed and tested with locally.

이 파일에서는 각 의존성 라이브러리를 정확한 버전 지정과 함께 확인할 수 있다.
라이브러리에서는 넓은 범위의 버전 지정을 사용하는 편이지만 어플리케이션은 매우
특정한 버전의 의존성을 갖는다. requests가 정확하게 어떤 버전이 설치되었는지는 큰
문제가 되지 않지만 이렇게 명확한 버전을 기입하면 로컬에서 테스트하거나 개발하는 환경에서도
프로덕션에 설치한 의존성과 정확히 동일한 버전을 사용할 수 있게 된다.

At the top of this file you'll also notice a
``--index-url https://pypi.python.org/simple/``. Your typical requirements.txt
won't have this listed explicitly like this unless they are not using PyPI, it
is however an important part of a ``requirements.txt``. This single line is
what turns the abstract dependency of ``requests==1.2.0`` into a "concrete"
dependency of "requests 1.2.0 from https://pypi.python.org/simple/". This is
not like duck typing, this is the packaging equivalent of an ``isinstance()``
check.

파일 첫 부분에 ``--index-url https://pypi.python.org/simple/`` 부분을 이미 눈치챘을
것이다. requirements.txt에는 PyPI를 사용하지 않는 경우를 제외하고는 일반적으로 인덱스
주소를 명시하지 않긴 하지만 ``requirements.txt``에서 중요한 부분에 해당한다. 이 내용 한 줄이
추상 의존성이었던 ``requests==1.2.0``을 "구체적인" 의존성인 "https://pypi.python.org/simple/에
있는 requests 1.2.0"으로 처리하게 만든다. 즉, 더이상 덕 타이핑이 아니라 ``isinstance()`` 확인과
동일한 패키징 방식이라고 할 수 있다.


## So Why Does Abstract and Concrete Matter?

## 추상 의존성 또는 구체적인 의존성에는 어떤 문제가 있을까?

You've read this far and maybe you've said, ok I know that ``setup.py`` is
designed for redistributable things and that ``requirements.txt`` is designed
for non-redistributable things but I already have something that reads a
``requirements.txt`` and fills out my ``install_requires=[...]`` so why should I
care?

여기까지 읽었다면 이렇게 생각할 수도 있다. ``setup.py``는 재배포를 위한 파일이고
``requirements.txt``는 배포할 수 없는 것을 위한 파일이라고 했지만 이미 ``requirements.txt``에
담긴 항목이 ``install_requires=[...]``에 정의된 의존성과 동일한데 왜 이런 부분을
신경써야 하는가 말이다.

This split between abstract and concrete is an important one. It was what
allows the PyPI mirroring infrastructure to work. It is what allows a company
to host their own private package index. It is even what enables you to fork a
library to fix a bug or add a feature and use your own fork. Because an
abstract dependency is a name and an optional version specifier you can install
it from PyPI or from Crate.io, or from your own filesystem. You can fork a
library, change the code, and as long as it has the right name and version
specifier that library will happily go on using it.

추상 의존과 구체적 의존을 분리하는 것은 중요하다. 이런 접근 방식이 있기 때문에 PyPI를
미러링해서 사용하는 것이 가능하다. 또한 같은 이유로 회사 스스로 사설(private) 패키지 색인을
구축해서 사용할 수 있는 것이다. 동일한 라이브러리를 가져와서 버그를 고치거나 새로운 기능을
더한 다음에 그 라이브러리를 의존성으로 사용하는 것도 가능하게 된다. 추상 의존성은 명칭,
버전 지정만 있고 이 의존성을 설치할 때 해당 패키지를 PyPI에서 받을지, Create.io에서 받을지,
아니면 자신의 파일 시스템에서 지정할 수 있기 때문이다. 라이브러리를 포크하고 코드를 변경했다 하더라도
라이브러리에 명칭과 버전 지정을 올바르게 했다면 이 라이브러리를 사용하는데 전혀 문제가 없을 것이다.

A more extreme version of what can happen when you use a concrete requirement
where an abstract requirement should be used can be found in the
[Go language][4]. In the go language the default package manager (``go get``)
allows you to specify your imports via an url inside the code which the package
manager collects and downloads. This would look something like:

구체적인 요구 사항을 추상적 요구사항이 필요한 곳에서 사용했을 때 발생하는 문제가 있다. 그 문제에
대한 극단적인 예시는 [Go 언어][4]에서 찾아볼 수 있다. go에서 사용하는 기본 패키지 관리자(``go get``)는
사용할 패키지를 다음 예제처럼 URL로 지정해서 받아오는 것이 가능하다.

```go
import (
    "github.com/foo/bar"
)
```

Here you can see that an exact url to a dependency has been specified. Now if I
used a library that specified its dependencies this way and I wanted to change
the "bar" library because of a bug that was affecting me or a feature I needed,
I would not only need to fork the bar library, but I would also need to fork
the library that depended on the bar library to update it. Even worse, if the
bar library was say, 5 levels deep, then that's a potential of 5 different
packages that I would need to fork and modify only to point it at a slightly
different "bar".

이 코드에서 의존성이 정확한 주소로 지정된 것을 확인할 수 있다. 이제 이 라이브러리를
사용하면서 "bar" 라이브러리에 존재하는 버그가 내 작업에 영향을 줘서 "bar" 라이브러리를
교체하려고 한다고 생각해보자. "bar" 라이브러리를 포크해서 문제를 수정했다면 이제
"bar" 라이브러리의 의존성이 명시된 코드를 변경해야 한다. 물론 지금 바로 수정할 수 있는
패키지라면 상관 없겠지만 5단계 깊숙히 존재하는 라이브러리의 의존성이라면 일이 커지게 된다.
단지 조금 다른 "bar"를 쓰기 위한 작업인데 다른 패키지를 최소 5개를 포크하고 내용을 수정해서
라이브러리를 갱신해야 하는 상황이 되고 말았다.


### A Setuptools Misfeature

### Setuptools의 잘못된 기능

Setuptools has a feature similar to the Go example. It's called
[dependency links][5] and it looks like this:

Setuptools는 Go 예제와 비슷한 기능이 존재한다. [의존성 링크(dependency links)][5]
라는 기능이며 다음 코드처럼 작성한다.

Setup

```python
from setuptools import setup

setup(
    # ...
    dependency_links = [
        "http://packages.example.com/snapshots/",
        "http://example2.com/p/bar-1.0.tar.gz",
    ],
)
```

This "feature" of setuptools removes the abstractness of its dependencies and
hardcodes an exact url from which you can fetch the dependency from. Now very
similarly to Go if we want to modify packages, or simply fetch them from a
different server we'll need to go in and edit each package in the dependency
chain in order to update the ``dependency_links``.


## Developing Reusable Things or How Not to Repeat Yourself

The "Library" and "Application" distinction is all well and good, but whenever
you're developing a Library, in a way *it* becomes your application. You want a
specific set of dependencies that you want to fetch from a specific location
and you know that you should have abstract dependencies in your ``setup.py``
and concrete dependencies in your ``requirements.txt`` but you don't want to
need to maintain two separate lists which will inevitably go out of sync. As it
turns out pip requirements file have a construct to handle just such a case.
Given a directory with a ``setup.py`` inside of it you can write a requirements
file that looks like:

```ini
--index-url https://pypi.python.org/simple/

-e .
```

Now your ``pip install -r requirements.txt`` will work just as before. It will
first install the library located at the file path ``.`` and then move on to
its abstract dependencies, combining them with its ``--index-url`` option and
turning them into concrete dependencies and installing them.

This method grants another powerful ability. Let's say you have two or more
libraries that you develop as a unit but release separately, or maybe you've
just split out part of a library into its own piece and haven't officially
released it yet. If your top level library still depends on just the name then
you can install the development version when using the ``requirements.txt`` and
the release version when not, using a file like:

```ini
--index-url https://pypi.python.org/simple/

-e https://github.com/foo/bar.git#egg=bar
-e .
```

This will first install the bar library from https://github.com/foo/bar.git,
making it equal to the name "bar", and then will install the local package,
again combining its dependencies with the ``--index`` option and installing
but this time since the "bar" dependency has already been satisfied it will
skip it and continue to use the in development version.


_**Recognition:** This post was inspired by [Yehuda Katz's blog post][6] on a
similar issue in Ruby with ``Gemfile`` and ``gemspec``._

[1]: https://pypi.python.org/pypi/pbr/#requirements
[2]: https://pypi.python.org/pypi
[3]: http://pip-installer.org/
[4]: http://golang.org/
[5]: http://pythonhosted.org/setuptools/setuptools.html#dependencies-that-aren-t-in-pypi
[6]: http://yehudakatz.com/2010/12/16/clarifying-the-roles-of-the-gemspec-and-gemfile/
