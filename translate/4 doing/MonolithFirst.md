# MonolithFirst 모노리스 우선 전략
3 June 2015

마이크로서비스 아키택처를 사용하는 팀의 이야기를 듣다보니 다음과 같은 일반적인 패턴을 발견할 수 있었다.

1. 성공적인 마이크로서비스 이야기 대다수는 모노리스에서 시작해 너무 커지거나 문제가 생겨 시작한 경우였음.
2. 시작부터 마이크로서비스로 시작한 시스템의 경우 대다수는 끝내 중대한 문제가 됨.

이 패턴은 동료들 사이에서 새 프로젝트를 마이크로서비스로 시작하지 말아야 하는지에 대한 논쟁을 만들었다. 개발하게 될 어플리케이션이 큰 규모가 될 예정이라 충분히 이득이 될 상황이라는 걸 알고 있는 경우에도 말이다.

마이크로서비스는 유용한 아키택처다. 마이크로서비스 옹호자가 막대한 [마이크로서비스 프리미엄]()을 얻을 수 있다고 얘기하지만 마이크로서비스는 복잡한 시스템에서만 유용하다. 이 프리미엄, 특히 여러 벌의 서비스를 관리하는 비용은 팀을 느리게 만드는데 간단한 어플리케이션이라면 모노리스가 바람직하다. 이런 문제로 모노리스 우선 전략은 새로운 어플리케이션을 개발을 시작할 때 나중에 마이크로서비스 아키택처로 이득을 볼 수 있는 경우에도 모노리스로 시작해야 하는지에 대한 논쟁으로 이끈다.

첫번째 이유는 [Yagni]() 때문이다. 새로은 어플리케이션을 시작할 때 이 어플리케이션이 사용자에게 얼마나 유용한지 어떻게 확신하는가? 성공한 소프트웨어 시스템이지만 지저분하게 디자인된 경우라면 확장하기 힘들다. 하지만 여전히 반대의 경우보다는 낫다. 여기서 알아챌 수 있는 것처럼 종종 소프트웨어 아이디어가 유용한지 알아내기 위한 최고의 방법은 간단한 버전으로 만들어서 잘 동작하는지 확인하는 것이다. 이런 첫단계에서는 속도에 중점을 둬야 할 필요가 있고 (그 덕분에 피드백 순환 주기가 빨라지고) 그래서 마이크로서비스 프리미엄 없이 개발해야 한다.

마이크로서비스로 시작해서 발생하는 두번째 문제는 서비스 사이에 안정적이고 잘 만들어진 경계가 있을 때만 제대로 동작한다는 점이다. 즉 서비스 사이 맥락에 맞게 [올바른 경계]()를 그리는 업무가 필수적이다. 서비스 사이에 있는 기능을 새로 작성하는 과정은 모노리스보다 훨씬 어렵다. 아무리 유사한 도메인에서 일한 경험이 있는 숙련된 아키택처라도 처음부터 올바른 경계를 만드는 것은 대단히 어려운 일에 해당한다. 모노리스로 먼저 만드는 과정은 마이크로서비스를 디자인하며 각각의 계층으로 만들기 전에 올바른 경계를 설정하는데 도움이 된다. 마이크로서비스를 개발하기 앞서 [부드럽게 정제된 서비스]()를 만들 수 있도록 사전에 준비하는 역할도 한다.

모노리스 우선 전략을 실천하는 다른 방법도 들은 적이 있다. 모노리스를 신중하게 설계하는 것으로 API 경계, 데이터를 어떻게 저장하는 등에 주의해서 소프트웨어 내에 모듈 방식을 유지하는 것이다. 이 작업을 잘하면 마이크로서비스로 전환하는 일은 간단한 문제가 된다. 이 방식이 제대로 동작했다는 얘기를 많이 들었다면 이런 접근법을 사용하는데 더 편하게 느꼈을텐데 그렇지 않았다. [^1]

A more common approach is to start with a monolith and gradually peel off microservices at the edges. Such an approach can leave a substantial monolith at the heart of the microservices architecture, but with most new development occurring in the microservices while the monolith is relatively quiescent.

Another common approach is to just replace the monolith entirely. Few people look at this as an approach to be proud of, yet there are advantages to building a monolith as a SacrificialArchitecture. Don't be afraid of building a monolith that you will discard, particularly if a monolith can get you to market quickly.

Another route I've run into is to start with just a couple of coarse-grained services, larger than those you expect to end up with. Use these coarse-grained services to get used to working with multiple services, while enjoying the fact that such coarse granularity reduces the amount of inter-service refactoring you have to do. Then as boundaries stabilize, break down into finer-grained services. [2]

While the bulk of my contacts lean toward the monolith-first approach, it is by no means unanimous. The counter argument says that starting with microservices allows you to get used to the rhythm of developing in a microservice environment. It takes a lot, perhaps too much, discipline to build a monolith in a sufficiently modular way that it can be broken down into microservices easily. By starting with microservices you get everyone used to developing in separate small teams from the beginning, and having teams separated by service boundaries makes it much easier to scale up the development effort when you need to. This is especially viable for system replacements where you have a better chance of coming up with stable-enough boundaries early. Although the evidence is sparse, I feel that you shouldn't start with microservices unless you have reasonable experience of building a microservices system in the team.

I don't feel I have enough anecdotes yet to get a firm handle on how to decide whether to use a monolith-first strategy. These are early days in microservices, and there are relatively few anecdotes to learn from. So anybody's advice on these topics must be seen as tentative, however confidently they argue.

Further Reading

Sam Newman describes a case study of a team considering using microservices on a greenfield project.

Notes

1: You cannot assume that you can take an arbitrary system and break it into microservices. Most systems acquire too many dependencies between their modules, and thus can't be sensibly broken apart. I've heard of plenty of cases where an attempt to decompose a monolith has quickly ended up in a mess. I've also heard of a few cases where a gradual route to microservices has been successful - but these cases required a relatively good modular design to start with.

2: I suppose that strictly you should call this a "duolith", but I think the approach follows the essence of monolith-first strategy: start with coarse-granularity to gain knowledge and split later.

Acknowledgements

I stole much of this thinking from my coleagues: James Lewis, Sam Newman, Thiyagu Palanisamy, and Evan Bottcher. Stefan Tilkov's comments on an earlier draft played a pivotal role in clarifying my thoughts. Chad Currie created the lovely glyphy dragons. Steven Lowe, Patrick Kua, Jean Robert D'amore, Chelsea Komlo, Ashok Subramanian, Dan Siwiec, Prasanna Pendse, Kief Morris, Chris Ford, and Florian Sellmayr discussed drafts on our internal mailing list.