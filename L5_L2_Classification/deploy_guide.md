# Deploy Guide — PAX_CODE_INTERPRETER
**Stack:** Python 3.11, RestrictedPython, subprocess (sandboxed), AIOSS_FORMAT | Air-gap capable

## Prerequisites
Anticloud core stack installed. PAX 27B weights (pax-27b-q4.gguf). AIOSS_FORMAT.

## Install
```bash
pip install anticloud-pax-code-interpreter
```

## AIOSS Integration
```bash
aioss init --module PAX_CODE_INTERPRETER --output ./pax_code_interpreter.aioss
```

## Air-Gap
```bash
pip download -r requirements.txt -d ./wheels/
pip install --no-index --find-links ./wheels/ -r requirements.txt
```

## PAX Harness Wiring
```python
from anticloud_pax import PAXHarness
harness = PAXHarness(model_path="./pax-27b-q4.gguf", module="PAX_CODE_INTERPRETER",
                     aioss_chain="./pax_code_interpreter.aioss",
                     classification="L5_NARROW_L2_GENERAL")
```

## Verification
```bash
aioss verify --chain ./pax_code_interpreter.aioss --verbose
python -m pax_code_interpreter.tests.smoke
```
