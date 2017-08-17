# Java Static Analysis

자바 정적분석 도구들.

1. [**PWD**](https://pmd.github.io/) : PMD is a source code analyzer. It finds common programming flaws like unused variables, empty catch blocks, unnecessary object creation, and so forth. It supports Java, JavaScript, Salesforce.com Apex, PLSQL, Apache Velocity, XML, XSL.

	- Include **CPD** the copy-paste-detector. CPD finds duplicated code

	- command :  ```mvn pmd:pmd pmd:cpd```

2. [Checkstyle](http://checkstyle.sourceforge.net/) : Checkstyle is a development tool to help programmers write Java code that adheres to a coding standard. It automates the process of checking Java code to spare humans of this boring (but important) task. This makes it ideal for projects that want to enforce a coding standard.  Checkstyle is highly configurable and can be made to support almost any coding standard. An example configuration files are supplied supporting the Sun Code Conventions, Google Java Style

	- 각 회사의 스타일엘 맞게 check_style.xml 을 정의할 수 있음
	- command ```mvn checkstyle:checkstyle```

3. [FindBugs](http://findbugs.sourceforge.net/) : Find Bugs in Java Programs

	- 굉장히 많은 경우에 대해서 버그가 정의되어 있음.
	- 참고 : http://findbugs.sourceforge.net/bugDescriptions.html
	- command ```mvn findbugs:findbugs```

4. [Cobertura](http://cobertura.github.io/cobertura/) : A code coverage utility for Java.  Cobertura is a free Java tool that calculates the percentage of code accessed by tests. It can be used to identify which parts of your Java program are lacking test coverage. It is based on jcoverage.

	- Packages, Files, Classes, Methods, Lines, Conditionals 의 기준으로 커버리지 측정
	- command ```mvn cobertura:cobertura```


## Jenkins에 연결하기

1. 플러그인 설치

	- Checkstyle Plug-in
	- Cobertura Plugin
	- FindBugs Plug-in
	- JUnit Plugin
	- PMD Plug-in

2. 각 플러그인에 맞게 설정! 설명은 젠킨스 플러그인 을 설치하면 잘 나와있습니다.

3. CI + Static Analysis는 필수!


## Reference
- [Review of Java Static Analysis Tools](https://blog.codacy.com/review-of-java-static-analysis-tools-5ad86cfc8ae2)
- [Jenkins 정적분석(PMD, FindBug, CheckStyle) 구축과 실행 결과](http://pseg.or.kr/pseg/infouse/4840)
- [PMD homepage](https://github.com/pmd/pmd)
- [checkstyle homepage](http://checkstyle.sourceforge.net/)
- [Find Bugs homepage](http://findbugs.sourceforge.net/)