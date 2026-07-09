---
title: "Hardening STB libraries using AI agents"
date: 2026-07-09T12:30:59+01:00
draft: false
---

**stb** is a collection of single-header C libraries used for a variety of tasks, such as decoding images, fonts, audio, and many other things. However, these libraries have a history of security issues, and many of them are not addressed quickly, making them unsuitable for environments where untrusted input is expected.

Meanwhile, in the last few months, there has been a lot of discussion around the use of LLMs for security research, especially after the introduction of [Claude Mythos](https://www.anthropic.com/claude/mythos) by Anthropic, followed by the short-lived [Claude Mythos 5](https://www.anthropic.com/news/claude-fable-5-mythos-5).

And while I don't have access to Mythos, I still believe currently available LLMs can be useful for security research and hardening, so here I'm gonna use them to harden **stb** libraries.

# The agent flow

After some experiments, I was able to come up with an agentic setup that allows us to discover and fix security vulnerabilities, you can check out the [AGENTS.md](https://github.com/mrunix00/stb_hardened/blob/master/AGENTS.md) used for this.

The main advantage of this flow is that it ensure that the bugs being found are real security issues, and not just false positives, by validating the bugs before patching.

This flow goes through 3 different phases:

- Discovery
- Validation
- Patching

## Phase 1: Discovery

In this phase, the agent searches for potential security issues and documents them in _BUGS.md_.

For the discovery process, there are three different techniques our agent can use:

- **Web search**: The agent can look up known bugs and vulnerabilities online from different sources, such as GitHub issues and CVEs.
- **Fuzzing**: The agent can use or implement tools and scripts for fuzzing to test the target against different sets of malicious inputs.
- **Static analysis**: The LLM can read the source code and flag suspicious sections that may contain bugs or security issues.

## Phase 2: Validation

Since LLMs are well known for hallucinations and for reporting security vulnerabilities that do not exist, it's generally a good idea to validate the bugs that have been flagged earlier, since we do not want to patch bugs that do not exist.

For that, we let the agent implement some automated tests (unit tests or E2E tests) that would let us validate both the vulnerability and the patch for it.

## Phase 3: Patching

After we validate the bugs we discovered earlier, it's time to fix them.

Here, the agent can simply patch these bugs with the minimal amount of changes, then re-run the previously implemented tests to validate the fix.

# The results

I have tested multiple AI agents, models and subscriptions, including Opencode, Codex, GitHub Copilot and Hermes.

Suprisingly, all of them gave impressive results, including the free models from Opencode.

At the time of writing, this is the number of bugs that have been found and fixed in stb:

| Library             | Total bugs found | web-search | fuzzing | static-analysis | Valid bugs |
| ------------------- | ---------------- | ---------- | ------- | --------------- | ---------- |
| stb_c_lexer.h       | 12               | 7          | 2       | 3               | 12         |
| stb_easy_font.h     | 5                | 0          | 1       | 4               | 5          |
| stb_hexwave.h       | 12               | 5          | 4       | 3               | 12         |
| stb_image.h         | 30               | 21         | 5       | 4               | 21         |
| stb_image_resize2.h | 17               | 1          | 3       | 13              | 13         |
| stb_image_write.h   | 9                | 4          | 2       | 3               | 7          |
| stb_truetype.h      | 36               | 13         | 15      | 8               | 34         |
| stb_vorbis.c        | 13               | 8          | 2       | 3               | 11         |
| **Total**           | **134**          | **59**     | **34**  | **41**          | **115**    |

# Performance Impact

So I did some benchmarks to measure the performance impact of the patches that have been done, the performance regressions for these libraries was negligible, except for **stb_truetype.h**.

| Workload                                       | Original mean | Hardened mean | Change |
| ---------------------------------------------- | ------------: | ------------: | -----: |
| _**stb_image.h**_                              |               |               |        |
| PNG decode, 512x512 RGB                        |      1.930 ms |      1.894 ms |  -1.9% |
| JPEG decode, 512x512 RGB                       |      1.999 ms |      1.988 ms |  -0.6% |
| PNG decode, 1024x1024 RGB                      |      7.041 ms |      6.930 ms |  -1.6% |
| PNG decode, 512x512 RGBA conversion            |      1.643 ms |      1.606 ms |  -2.3% |
| 16-bit PNG decode, 512x512 RGB                 |      1.503 ms |      1.431 ms |  -4.8% |
| HDR decode, 512x512 RGB                        |      2.259 ms |      2.438 ms |  +7.9% |
| _**stb_truetype.h**_                           |               |               |        |
| Kerning advances, 95 glyph pairs               |      0.459 ms |      0.472 ms |  +2.8% |
| Glyph shapes, 95 glyphs                        |      0.010 ms |      0.011 ms | +10.0% |
| Bitmap rendering, 16px, 95 glyphs              |      0.093 ms |      0.103 ms | +10.8% |
| Bitmap rendering, 64px, 95 glyphs              |      0.378 ms |      0.500 ms | +32.3% |
| Bitmap rendering, 128px, 95 glyphs             |      0.880 ms |      1.162 ms | +32.0% |
| SDF rendering, 16px, 94 glyphs                 |      6.348 ms |      6.219 ms |  -2.0% |
| SDF rendering, 64px, 94 glyphs                 |     36.872 ms |     36.096 ms |  -2.1% |
| Atlas packing, 16px, 94 characters             |      0.110 ms |      0.124 ms | +12.7% |
| Atlas packing, 48px, 94 characters             |      0.343 ms |      0.429 ms | +25.1% |
| _**stb_image_resize2.h**_                      |               |               |        |
| uint8 sRGB upscale, 256x256 to 512x512         |      0.837 ms |      0.835 ms |  -0.2% |
| uint8 sRGB downscale, 1024x1024 to 512x512     |      2.732 ms |      2.671 ms |  -2.2% |
| float linear downscale, 512x512 to 256x256     |      0.407 ms |      0.402 ms |  -1.2% |
| BOX filter, 512x512 to 256x256                 |      0.192 ms |      0.190 ms |  -1.0% |
| MITCHELL filter, 512x512 to 256x256            |      0.422 ms |      0.418 ms |  -0.9% |
| CLAMP edge mode, 256x256 to 512x512            |      0.332 ms |      0.329 ms |  -0.9% |
| 4x upscale, uint8 sRGB                         |      0.794 ms |      0.791 ms |  -0.4% |
| 4x downscale, uint8 sRGB                       |      0.494 ms |      0.486 ms |  -1.6% |
| _**stb_image_write.h**_                        |               |               |        |
| PNG encode, 512x512 RGB (format suite)         |      6.052 ms |      5.946 ms |  -1.8% |
| JPEG quality 80 encode, 512x512 RGB            |      3.293 ms |      3.296 ms |  +0.1% |
| HDR encode, 512x512 RGB                        |      1.718 ms |      1.785 ms |  +3.9% |
| PNG encode, 1024x1024 RGB                      |     20.641 ms |     20.613 ms |  -0.1% |
| JPEG quality 80 encode, 1024x1024 RGB          |     13.197 ms |     13.628 ms |  +3.3% |
| HDR encode, 1024x1024 RGB                      |      6.672 ms |      7.216 ms |  +8.2% |
| PNG compression level 8, 512x512 RGB           |      6.130 ms |      6.237 ms |  +1.7% |
| JPEG quality 90, 512x512 RGB                   |      3.971 ms |      4.039 ms |  +1.7% |
| PNG encode, 512x512 RGB (channel-count suite)  |      5.887 ms |      6.015 ms |  +2.2% |
| PNG encode, 512x512 RGBA (channel-count suite) |      7.501 ms |      7.476 ms |  -0.3% |
