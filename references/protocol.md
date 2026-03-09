# Channel Protocol (`==...==`)

This file defines the authoritative parsing and execution protocol for all turns.

## Purpose

- Keep cross-agent behavior deterministic.
- Separate third-party model payload from assistant-local instructions.
- Avoid accidental forwarding of local instructions.

## Delimiter Rule

- Delimiter tokens: `==` opening and `==` closing.
- Each complete pair `==...==` marks one OpenRouter segment.
- Text outside any complete pair is assistant-local segment.

## Parsing Algorithm

1. Read raw user message as a string.
2. Scan left to right and extract all complete `==...==` pairs.
3. Third-party segment:
   - Concatenate all inside texts in encounter order.
   - Join multiple segments with two newlines.
   - Trim final result.
4. Assistant-local segment:
   - Remove all extracted `==...==` blocks from original text.
   - Keep remaining text order.
   - Trim final result.
5. Unmatched `==` without a closing pair is treated as plain assistant-local text.

## Execution Rules

1. If third-party segment is non-empty:
   - Send it to OpenRouter using resolved alias/model.
2. If third-party segment is empty:
   - Do not call OpenRouter for that turn.
3. If assistant-local segment is non-empty:
   - Treat it as instructions for the assistant only.
   - Never include it in OpenRouter prompt.

## Examples

- Input: `请你总结一下这份规范`
  - Third-party: empty
  - Assistant-local: `请你总结一下这份规范`
  - Action: no OpenRouter call

- Input: `==请用三点总结这份规范==`
  - Third-party: `请用三点总结这份规范`
  - Assistant-local: empty
  - Action: call OpenRouter

- Input: `先别解释太多，==比较 gpt 和 claude 的风格==然后给我一个结论`
  - Third-party: `比较 gpt 和 claude 的风格`
  - Assistant-local: `先别解释太多，然后给我一个结论`
  - Action: call OpenRouter with inside text only

- Input: `A==Q1==B==Q2==C`
  - Third-party: `Q1\n\nQ2`
  - Assistant-local: `ABC`
  - Action: call OpenRouter once with merged third-party content

- Input: `这是未闭合 == 内容`
  - Third-party: empty
  - Assistant-local: `这是未闭合 == 内容`
  - Action: no OpenRouter call
