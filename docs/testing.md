# Testing

How tests in these repos are written. A test earns its place by failing when something users rely on breaks, and by doing that fast. Fewer, sharper tests beat many.

## What to test

1. Test the critical path and how it fails: the main thing users do, each failure they can see (an error, a refusal, a non-zero exit) and each boundary the code branches on. Spend tests where a break is likely or costly. Test behaviors, not methods: one behavior per test.
2. Before adding a test, name the regression it fails on. If a test already fails on it, extend that test. A fix gets one or two such tests where the bug lives, not one per layer.

## How many

3. Inputs that take the same path are one table-driven test, one row each (`t.Run` over a table, `pytest.mark.parametrize`, `test.each`, Swift Testing `arguments:`). A row needs its own partition or boundary. A test that fails on the same change as an existing one is a duplicate: don't add it.
4. Never add a test only to raise a coverage number. Coverage says a line ran, not that its result was checked. If a coverage gate forces a test, say so in the pull request.

## How a test checks

5. Call the code the way its users do and assert what they see: return value, output, exit code, file written, message sent. Not which internal calls were made. A test that restates the code (call order, a structural snapshot) fails on every refactor and catches nothing.
6. Use real code. Fake only what a test can't run: network, clock, model, external commands and services. A fake may return what the real one would, errors included; it must not supply the behavior the test asserts. Check a fake's calls only when the outcome can't be checked, or the count or order is the behavior (a cache that saves a request).
7. Match exact text only when the text is a contract: another program parses it, or the text is the product (a help page, a prompt, a message a person receives). Otherwise check the level, exit code or field that matters.

## Speed

8. Write small tests: one process, no network, no sleep. Use a medium test (child processes, localhost, temp files) only when the boundary is the behavior: a CLI's exit code and output, a file the program must write, a hook's input and output.
9. No sleep unless elapsed time is the behavior. Wait for the event (a file, a message, an exit) or inject the clock.
10. A test touches only what it declares: its own temp directory and `HOME`, and fakes for host tools it doesn't test. No fixed path, port or global shared between files, so test files run in parallel.
11. A test file finishes within 60 seconds. Raising a timeout to pass is not a fix; make the test smaller.

## Removing a test

A test that breaks these rules can go when another test still fails on the same regression; name that test in the pull request. When it's unclear whether a test catches anything, break the behavior it names and run it, instead of guessing. A failing test is a bug report, never a removal candidate.

## Sources

| Rule | Source |
| --- | --- |
| 1 | ISTQB [CTFL syllabus v4.0.1](https://istqb.org/wp-content/uploads/2024/11/ISTQB_CTFL_Syllabus_v4.0.1.pdf), §5.2: test scope and depth set by risk, likelihood and impact; Google Testing Blog, [Test Behaviors, Not Methods](https://testing.googleblog.com/2014/04/testing-on-toilet-test-behaviors-not.html) (2014); *Software Engineering at Google* (O'Reilly, 2020), [ch. 12](https://abseil.io/resources/swe-book/html/ch12.html): one behavior per test |
| 2 | OpenClaw [test-audit skill](https://github.com/openclaw/openclaw/blob/70bfdc2b58/.agents/skills/test-audit/SKILL.md): "One owner regression covers bug; never replay at every crossed layer"; Fowler, [The Practical Test Pyramid](https://martinfowler.com/articles/practical-test-pyramid.html): delete a higher-level test a lower one covers |
| 3 | Go wiki [TableDrivenTests](https://go.dev/wiki/TableDrivenTests); ISTQB CTFL v4.0.1, §4.2: one value per equivalence partition, plus boundaries; pytest [parametrize](https://docs.pytest.org/en/stable/how-to/parametrize.html); Vitest [`test.each`](https://vitest.dev/api/#test-each); Swift Testing [parameterized testing](https://developer.apple.com/documentation/testing/parameterizedtesting) |
| 4 | *Software Engineering at Google*, [ch. 11](https://abseil.io/resources/swe-book/html/ch11.html): coverage "only measures that a line was invoked, not what happened as a result" |
| 5 | *Software Engineering at Google*, ch. 12: "Test via Public APIs", "Test State, Not Interactions"; OpenClaw [writing-tests.md](https://github.com/openclaw/openclaw/blob/d70a0b7939/docs/help/testing/writing-tests.md); Google Testing Blog, [Change-Detector Tests Considered Harmful](https://testing.googleblog.com/2015/01/testing-on-toilet-change-detector-tests.html) (2015) |
| 6 | *Software Engineering at Google*, [ch. 13](https://abseil.io/resources/swe-book/html/ch13.html): real implementations first; stubs may simulate errors; interaction testing only when state can't be checked or call count or order matters |
| 7 | Go wiki [TestComments](https://go.dev/wiki/TestComments), "Test Error Semantics" |
| 8, 9 | Google Testing Blog, [Test Sizes](https://testing.googleblog.com/2010/12/test-sizes.html) (2010): small tests have no network, file system or sleep; medium tests may use child processes and localhost; *Software Engineering at Google*, ch. 11 |
| 10 | Bazel [Test encyclopedia](https://bazel.build/reference/test-encyclopedia): tests are hermetic and "must not assume that any constant path is available for their exclusive use"; Vitest [`fileParallelism`](https://vitest.dev/config/fileparallelism), on by default |
| 11 | Test Sizes: a small test's time limit is 60 seconds; Bazel Test encyclopedia: a small test target's timeout is 60 seconds |
| Removing | Thoughtworks Technology Radar, [Mutation testing](https://www.thoughtworks.com/radar/techniques/mutation-testing) |
