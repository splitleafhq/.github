# Testing

How tests in these repos are written. A test earns its place by failing when something users rely on breaks.

## Rules

1. Before adding a test, name the regression it fails on. If a test already fails on it, extend that test. A fix gets one or two such tests where the bug lives, not one per layer.
2. Call the code the way its users do and assert what they see: return value, output, exit code, file written, message sent. Not which internal calls were made.
3. Use real code. Fake only what a test can't run: network, clock, model, external commands and services. A fake may return what the real one would, errors included; it must not supply the behavior the test asserts. Check a fake's calls only when the outcome can't be checked, or the count or order is the behavior (a cache that saves a request).
4. Match exact text only when the text is a contract: another program parses it, or the text is the product (a help page, a prompt, a message a person receives). Otherwise check the level, exit code or field that matters.

## Removing a test

A test that breaks these rules can go when another test still fails on the same regression; name that test in the pull request. A failing test is a bug report, never a removal candidate.

## Sources

| Rule | Source |
| --- | --- |
| 1 | OpenClaw [test-audit skill](https://github.com/openclaw/openclaw/blob/70bfdc2b58/.agents/skills/test-audit/SKILL.md): "One owner regression covers bug; never replay at every crossed layer" |
| 2 | *Software Engineering at Google* (O'Reilly, 2020), ch. 12: "Test via Public APIs", "Test State, Not Interactions"; OpenClaw [writing-tests.md](https://github.com/openclaw/openclaw/blob/d70a0b7939/docs/help/testing/writing-tests.md) |
| 3 | *Software Engineering at Google*, ch. 13: real implementations first; stubs may simulate errors; interaction testing only when state can't be checked or call count or order matters |
| 4 | Go wiki [TestComments](https://go.dev/wiki/TestComments), "Test Error Semantics" |
