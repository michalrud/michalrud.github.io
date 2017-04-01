---
title:  Software Testing Levels
description: Short introduction into different software testing levels.
---

To make things more organized and to bring some reign over the chaos that glooms over the software development process testing has been divided into a few levels, each guarding differently sized scope. Thanks to that, you can have more detailed tests which execute quicker (as more things in tests are fakes with hardcoded values) on the lower levels, and some overall tests on the higher levels, which check for more at the same time, but require more time to run thanks to having practically a whole environment real.

# Unit Testing
This is the level which takes a look at the smallest detail. Usually checks only one class, so writing this kind of tests is easy and fast; everything outside of the currently tested unit should not be real (so, a fake or a mock should be used instead). Thanks to that we should have an environment, which is always the same, no matter when or where the test is ran. Tests should not require internet connectivity, certain time and should not rely on sleeps to simulate time, among other things. The idea is that those tests execute quickly, and can be executed with each compilation without much extra waiting time required from the developer, who should be rapidly notified of most mistakes that can be done while modifying the code - that's why Unit Tests are very valuable during refactoring, when neither logic nor interface should change.

Thanks to the approach where only a small portion of the whole system is tested at once, it is easy to find out the origin of the bug down to the one class or even function, which behaves wrongly.

Sometimes *Module Testing* can also be used, which is a little bit higher level than UT - it checks a few classes running together to achieve a certain goal. Therefore, we are able to check a whole scenario using relatively small amount of resources, as everything outside of the small set of classes that we are currently testing should be made of fakes and stubs.

Since all external world in here is a make-believe, it is important to stick to interfaces, which should be well-defined. Because how much are even the nicest tests worth, if they ensure that wrong interfaces are implemented?

Well, if your interfaces are not documented yet, this may be a good time to start doing that. Remember, that it doesn't have to mean creating a big, boring Word file - a header file in C++ or interface files in other languages will do just as good. You may use Doxygen or equivalent to clear up anything that is not clear.

# Integration Testing
Even if Unit Testing ensured us that at the smaller scale our logic is legit and works well, we still don't know if the pieces of our software actually fits together. Since this requires a more complicated environment than Unit Testing (for example, broker services may need to be started to pass messages between parts of the system), executing those tests may require more time and effort, therefore being unsuitable for frequent execution during the development process. As it should be fully automated (for example containers can be used to provide a pre-configured, disposable testing environment) it's still feasible to run those tests after each push to the repository using a CI.

In Integration Testing level different units are started together and their cooperation is tested. Here also tests can cover different areas, for example a basic test suite can only cover two units at the time, having simulators in place for all others.

# System Testing
This is the level where the biggest scope of the system is being tested. Since it requires the complete environment, including any third-party software that the system may integrate to. Things as speed or security is also checked at this level.

System Testing assumes that most smaller and logic errors have been already detected at lower testing levels, as finding the source of the problem is the hardest at this level, as all parts of the system are running and needs to be checked when a problem is uncovered.

# See Also
 * [SWEBOK Guide](https://www.computer.org/web/swebok), information from which has been used in this article.