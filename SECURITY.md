# Security Policy / 보안 정책

[English](#english) · [한국어](#한국어)

## English

### Supported versions

FiTuna is pre-1.0. Only the latest release on [PyPI](https://pypi.org/project/fituna/) and `main` are supported. Fixes land on `main` and ship in the next release.

### Reporting a vulnerability

Please use GitHub's private reporting: [**Security → Report a vulnerability**](https://github.com/leeyunseokarchive/fituna/security/advisories/new). If that isn't available, email **dbstjr3576@gmail.com** with `[fituna security]` in the subject.

Include the FiTuna command you ran, your OS and llama.cpp build (`fituna doctor --json` shows both), and what an attacker could gain. This is a volunteer project, so an acknowledgement can take up to a week. Please don't open a public issue before a fix is released.

### Trust boundary

FiTuna does not run inference or quantization itself. It runs llama.cpp binaries as subprocesses with your user's permissions and parses their output.

- **Binaries.** `llama-quantize`, `llama-bench`, and `llama-perplexity` are taken from `--llama-bin-dir` or `PATH`. Pointing FiTuna at an untrusted binary is the same as running it yourself.
- **Conversion script.** To convert a Hugging Face model directory, FiTuna runs `convert_hf_to_gguf.py` with its own Python interpreter (`ensure_base_gguf()` in `fituna/model_info.py`). `_find_script()` in `fituna/binaries.py` looks for the script in `--llama-bin-dir` and also two levels above it, so both locations are part of the trust boundary.
- **Model files.** FiTuna reads the model file you point it at. The GGUF header parser treats it as untrusted input.
- **Corpus download.** `fituna fetch-corpus` only talks to `https://datasets-server.huggingface.co`. `--dataset`, `--config`, and `--split` choose which dataset to read from that host.
- **Not executed.** FiTuna never runs `llama-cli`, `llama-server`, or `ollama`. It only prints commands for you to run, and `--export-ollama` only writes a `Modelfile`.

Treat `--llama-bin-dir` (and the directories two levels above it), `PATH`, `--out`, and the chosen dataset as inputs you control. FiTuna does not sandbox or verify them.

**In scope:** a bug where FiTuna mishandles an untrusted path, model file, script, or downloaded corpus and grants more than the access described above.
**Out of scope:** the consequences of pointing FiTuna at binaries, scripts, or directories you don't trust.

---

## 한국어

### 지원 버전

FiTuna는 아직 1.0 이전 단계입니다. [PyPI](https://pypi.org/project/fituna/)의 최신 릴리스와 `main`만 지원합니다. 수정은 `main`에 반영한 뒤 다음 릴리스에 포함합니다.

### 취약점 신고

GitHub의 비공개 신고 기능을 이용해 주세요: [**Security → Report a vulnerability**](https://github.com/leeyunseokarchive/fituna/security/advisories/new). 이 기능을 쓸 수 없다면 제목에 `[fituna security]`를 넣어 **dbstjr3576@gmail.com**으로 이메일을 보내 주세요.

실행한 FiTuna 명령, OS와 llama.cpp 빌드(`fituna doctor --json`으로 둘 다 확인 가능), 공격자가 얻을 수 있는 권한이나 정보를 적어 주세요. 개인이 자원봉사로 관리하는 프로젝트라 접수 확인까지 최대 일주일이 걸릴 수 있습니다. 수정본이 나오기 전에는 공개 이슈로 올리지 말아 주세요.

### 신뢰 경계

FiTuna는 추론이나 양자화를 직접 하지 않습니다. llama.cpp 바이너리를 사용자 권한의 subprocess로 실행하고 그 출력을 해석합니다.

- **바이너리.** `llama-quantize`, `llama-bench`, `llama-perplexity`는 `--llama-bin-dir` 또는 `PATH`에서 찾습니다. 신뢰할 수 없는 바이너리를 지정하는 것은 그 프로그램을 직접 실행하는 것과 같습니다.
- **변환 스크립트.** Hugging Face 모델 디렉터리를 변환할 때 FiTuna는 자신과 같은 Python 인터프리터로 `convert_hf_to_gguf.py`를 실행합니다(`fituna/model_info.py`의 `ensure_base_gguf()`). `fituna/binaries.py`의 `_find_script()`는 이 스크립트를 `--llama-bin-dir`뿐 아니라 두 단계 위 디렉터리에서도 찾으므로, 두 위치 모두 신뢰 경계에 포함됩니다.
- **모델 파일.** 사용자가 지정한 모델 파일을 읽습니다. GGUF 헤더 파서는 이 파일을 신뢰할 수 없는 입력으로 다룹니다.
- **코퍼스 다운로드.** `fituna fetch-corpus`는 `https://datasets-server.huggingface.co`에만 접속합니다. `--dataset`, `--config`, `--split`은 이 호스트에서 어떤 데이터셋을 받을지만 정합니다.
- **실행하지 않는 것.** FiTuna는 `llama-cli`, `llama-server`, `ollama`를 실행하지 않습니다. 사용자가 실행할 명령을 출력할 뿐이며, `--export-ollama`도 `Modelfile`만 씁니다.

`--llama-bin-dir`(그리고 두 단계 위 디렉터리까지), `PATH`, `--out`, 선택한 데이터셋은 사용자가 통제하는 입력으로 취급해 주세요. FiTuna는 이들을 sandbox로 격리하거나 검증하지 않습니다.

**신고 대상:** FiTuna가 신뢰할 수 없는 경로, 모델 파일, 스크립트, 다운로드한 코퍼스를 잘못 다뤄 위에서 설명한 범위를 넘는 권한을 내주는 버그.
**신고 대상 아님:** 신뢰할 수 없는 바이너리, 스크립트, 디렉터리를 직접 지정해서 생긴 결과.
