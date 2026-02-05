# Copilot / AI Agent Instructions ✅

**Project snapshot:** Small utility that extracts the plaintext password length from an Instagram/Facebook encrypted password ciphertext. Primary files: `script.py` (implementation) and `README.md` (algorithm explanation and examples).

## What this repository does 💡
- Accepts either a full Instagram ciphertext (`#PWD_INSTAGRAM_BROWSER:version:timestamp:base64...`) or the raw base64 portion and prints the password length.
- Uses a deterministic formula derived from observed ciphertext lengths and base64 padding to compute password length.

## Key behaviors to preserve 🔧
- Support both input formats (full colon-separated ciphertext or raw base64 string).
- Keep the simple interactive CLI behavior: a loop that prompts for encrypted input and prints `Password len: <n>` or `Wrong input` on invalid inputs.
- Preserve the exact computation formula currently implemented in `script.py`:
  - Extract the base64 segment: `c = enc.split(':')[3] if ':' in enc else enc`
  - Compute: `pad = int((len(c) / 4) - 36)`
  - Check base64 padding chars: `pad1 = 1 if c[-1] == '=' else 0` and `pad2 = 1 if c[-2] == '=' else 0`
  - Final length: `pl = len(c) - 136 - pad - pad1 - pad2`

## Examples (use these verbatim in unit tests) 🧪
- Input (password length 8):
  `#PWD_INSTAGRAM_BROWSER:10:1633796644:AY5QAOHhnlwGkvikhrThjD0/XSZAVlJ+dFBGNAtG4JhnP5c42slFXO0H0xpE3W2JSlcdjDEDI1O/CioKL5zXhXCfkRpL+ItOqUB0jhpl/D3EcTEI9iTq0XSpmGDvxb7fwaCvNFv2xFj4lvsv`
  → Expected output: `Password len: 8`
- Input (password length 12):
  `#PWD_INSTAGRAM_BROWSER:10:1633796717:AY5QAElzjWV0j+OJ+qAnNXpQjZ6TN7A980Y2RMlrl63z80AkALvvb1IHYpzDXeX5w/Mf1jxTbF2PVJRh/Q99+J7FXkgmnE9qOhatEbKkdyoatN952Dee/PC8CiWLJTcoFDiCFovU9uwijaIDycIQ7w==`
  → Expected output: `Password len: 12`

## What an AI agent should do first 🛠️
1. Run the script locally: `python script.py` (or `python3 script.py` on non-Windows systems) and try the example ciphertexts above to confirm current behavior.
2. Add unit tests that assert behavior on the examples and edge cases (short inputs, one- or two-char base64 payloads, inputs that are just base64 with paddings).
3. Refactor minimal internals for testability (without changing external CLI behavior):
   - Extract `parse_base64_segment(enc: str) -> str` and `compute_password_length(c: str) -> int`.
   - Add `if __name__ == '__main__':` CLI wrapper that calls these helpers.

## Edge cases and validation to encode into tests ⚠️
- Inputs shorter than 3 chars → currently prints `Wrong input`. Confirm this in tests.
- Base64 strings with length < 2 (indexing `c[-2]`) may raise exceptions — add tests to document desired behavior (either catch and print `Wrong input` or validate length first).
- Inputs without `:` must be treated as base64 directly.

## Tests & CI suggestions (minimal, actionable) ✅
- Add `tests/test_script.py` using `pytest` with the two example ciphertexts and the following cases:
  - Raw base64 input (both padded and unpadded) → expected lengths
  - Short/invalid inputs → expect printed `Wrong input` or handled exceptions
- (Optional) Add a simple GitHub Actions workflow that runs `pytest` on pushes and PRs.

## Notes for contributors / code reviewers 📝
- Keep changes small and behavior-preserving; this repository is intentionally minimal and focused on the specific ciphertext-length calculation.
- Any change to the main formula must be explicitly documented and accompanied by updated tests using the two canonical examples in `README.md`.

---
If anything is missing or unclear, please point to the exact part of `script.py` or an example from `README.md` and I'll update this instruction file. 🙋‍♂️
